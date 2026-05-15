---
layout: default
title: "Building Detections for IWR and RunMRU"
permalink: /solo-purple-teaming/building-detections-for-iwr-and-runmru/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Detections for IWR and RunMRU</h1>
</section>
<section class="spt-content">

Owner: Josh

# IOC Diagram

We have determined that using the Run dialog to execute our malicious PowerShell command invoke Explorer.exe which creates registry keys to store a history of commands and the order of those commands. Explorer.exe also spawns PowerShell.exe which executes our malicious command to reflective load apollo.exe. When using execute_assembly, a sacrificial process is spawned set by the spawn_x64 property, in our case smart screen.exe, and injects a CLR loader that reflective loads the assembly identified by the passed argument and executes. Named pipes are used for IPC and since Apollo uses pipe security, the transport is brokered through SMB involving the System process.

![image.png](Building%20Detections%20for%20IWR%20and%20RunMRU/image.png)

## Improving Invoke-WebRequest Detection

This detection uses keyword matching, which is inherently brittle and easy for a skilled threat actor to evade. In a Solo Purple Teaming workflow, however, even these simple detections have strategic value. They’re quick wins that help you learn and practice both the blue and red sides of Detection Engineering—understanding how defenders build rules and how attackers work to bypass them. By introducing more variables into the Detection Engineering stack, you force the adversary to adapt, increasing their workload and the chances they’ll make an OPSEC slip. Each added hurdle—no matter how small—becomes an opportunity to observe, learn, and refine your defenses, creating a feedback loop where detection drives adversary exposure, and adversary actions drive detection improvements.

```xml
<rule id="100102" level="15">
        <if_sid>91802</if_sid>
        <field name="win.eventdata.scriptBlockText" type="pcre2">(?i)iwr|Invoke-WebRequest</field>
        <description>Detected Malicious PowerShell - Invoke-WebRequest</description>
</rule>
```

## Creating RunMRU Detection for PowerShell

This is another keyword-based detection, but it poses a slightly greater challenge for threat actors to evade because the **PowerShell** keyword is required when launching PowerShell from the Run dialog. While adversaries can bypass it by switching to alternative execution mechanisms, that’s exactly the goal of Detection Engineering—forcing them to adapt and potentially make mistakes.

The primary drawback is that if system administrators regularly use the Run dialog to launch PowerShell, this detection can produce a high volume of false positives. Fortunately, this is often solvable through minor behavior changes, such as encouraging admins to use alternative launch methods. In my experience, very few administrators execute PowerShell commands directly from the Run dialog, so the impact is typically minimal.

```xml
<rule id="100108" level="15">
		<if_sid>61615</if_sid> 
		<field name="win.eventdata.targetObject" type="pcre2">(?i)runmru</field>
		<field name="win.eventdata.details" type="pcre2">(?i)powershell</field>
		<description>Malicious RunMRU Key Details</description>
</rule>
```

## BONUS: Detection for Suspicious Process Access

This rule on its own will be noisy, but when correlated with other detections, it can become a powerful tool for identifying malicious activity. Consider pairing it with **pipe creation events** from the target process and **network connection events**from the source process. By linking these signals, you transform high-volume, low-fidelity alerts into high-confidence indicators of suspicious behavior.

```xml
<rule id="100109" level="12”>
		<if_sid>61612</if_sid> 
		<field name="win.eventdata.grantedAccess" type="pcre2">(?i)0x1fffff</field>
		<description>Suspicious Process Access</description>
</rule>
```

# Full Custom Solo Purple Teaming Configuration File With Out Bonus Detection

```xml
<!-- Modify it at your will. -->
<group name="solo_purple_teaming_c2,windows,sysmon">
	<rule id="100100" level="12">
		<if_sid>61609</if_sid> 
		<field name="win.eventdata.ImageLoaded" type="pcre2">(?i)[c-z]:\\\\Users\\\\.+|[c-z]:\\\\Windows\\\\Temp\\\\.+</field>
		<field name="win.eventdata.Signed">false</field>
		<description>Unsigned image loaded from user or temp directory</description>
	</rule>
	<rule id="100101" level="12">
		<if_sid>61605</if_sid> 
		<field name="win.eventdata.DestinationPort">80|443</field>
        <field name="win.eventdata.Image" type="pcre2">(?i)[c-z]:\\\\Users\\\\.+|[c-z]:\\\\Windows\\\\Temp\\\\.+</field>
		<description>Network Connect to Common C2 Ports From Downloads or Users</description>
	</rule>
	<rule id="100108" level="15">
		<if_sid>61615</if_sid> 
		<field name="win.eventdata.targetObject" type="pcre2">(?i)runmru</field>
        <field name="win.eventdata.details" type="pcre2">(?i)powershell</field>
		<description>Possible Malicious RunMRU Value Set</description>
	</rule>
</group>
<group name="solo_purple_teaming_c2_correlation">
    <rule id="100200" level="13" frequency="2" timeframe="2">
        <if_matched_group>solo_purple_teaming_c2</if_matched_group>
    <description>Correlation: 100100 and 100101 occurred within 2 seconds. Possible C2 Activity</description>
  </rule>
</group>
<group name="solo_purple_teaming, windows, powershell">
    <rule id="100103" level="15">
        <if_sid>91802</if_sid>
        <match>Reflection</match>
        <description>Detected Malicious PowerShell Reflection</description>
    </rule>
    <rule id="100102" level="15">
        <if_sid>91802</if_sid>
        <field name="win.eventdata.scriptBlockText" type="pcre2">(?i)Invoke-Webrequest|iwr</field>
        <description>Detected Malicious PowerShell - Invoke-WebRequest</description>
    </rule>
</group>
<group name="solo_purple_teaming,windows,sysmon,suspicious_commands">
  <rule id="100104" level="14">
    <if_sid>92032</if_sid>
    <field name="win.eventdata.commandLine" type="pcre2">.*sc.*(qc|sdshow).*</field>
    <options>no_full_log</options>
    <description>Service Enumeration activity spawned via cmd shell execution</description>
  </rule>
  <rule id="100107" level="14">
    <if_sid>92032</if_sid>
    <field name="win.eventdata.commandLine" type="pcre2">.*wmic.*service.*get.*</field>
    <options>no_full_log</options>
    <description>Service Enumeration activity spawned via cmd shell execution</description>
  </rule>
  <rule id="100105" level="13">
    <if_sid>92032</if_sid>
    <field name="win.eventdata.commandLine" type="pcre2">.*net.*localgroup.*administrators.*</field>
    <options>no_full_log</options>
    <description>Local group enumeration for administrators activity spawned via cmd shell execution</description>
  </rule>
  <rule id="100106" level="13">
    <if_sid>92032</if_sid>
    <field name="win.eventdata.commandLine" type="pcre2">.*whoami.*/priv.*</field>
    <options>no_full_log</options>
    <description>User privilege enumeration using whoami spawned via cmd shell execution</description>
  </rule>
</group>
```

</section>
</div>
