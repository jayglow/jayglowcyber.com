---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-4/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Writing A Service Enumeration To</h1>
</section>
<section class="spt-content">
<h1
id="red-team-layout---writing-a-service-enumeration-tool---part-2">Red
Team Layout - Writing A Service Enumeration Tool - Part 2</h1>
<p>Owner: Mike Sterrett</p>
<h3 id="1-introduction"><strong>1. Introduction</strong></h3>
<p>In this phase, we’re expanding our custom <strong>Service
Scan</strong> utility to retrieve the <strong>Security Descriptor
Definition Language (SDDL)</strong> string for Windows services.</p>
<p>To do this, we’ll use <strong>P/Invoke</strong> in C# to call
unmanaged Windows API functions from our managed .NET code.</p>
<hr />
<h3 id="2-understanding-pinvoke"><strong>2. Understanding
P/Invoke</strong></h3>
<ul>
<li><strong>What is P/Invoke?</strong>
<ul>
<li>Allows managed .NET code to call unmanaged Windows API
functions.</li>
<li>Lets you work directly with <strong>native Windows DLLs</strong>
like <code>advapi32.dll</code>.</li>
</ul></li>
<li><strong>Why is it advanced?</strong>
<ul>
<li>Requires knowledge of both managed (.NET) and unmanaged (Win32 API)
environments.</li>
<li>Involves <strong>manual memory management</strong> (allocating,
copying, freeing).</li>
<li><strong>Data marshaling</strong> is critical — incorrect marshaling
can cause:
<ul>
<li>Memory corruption</li>
<li>Runtime crashes</li>
<li>Architecture-specific bugs</li>
</ul></li>
<li>Struct layout <strong>must match</strong> native memory layout
exactly.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-why-we-need-pinvoke-for-services"><strong>3. Why We Need
P/Invoke for Services</strong></h3>
<ul>
<li>.NET does <strong>not</strong> provide a built-in way to query a
service’s <strong>security descriptor</strong>.</li>
<li>Security descriptors tell us:
<ul>
<li>Which users/groups can control a service.</li>
<li>Potential privilege escalation vectors.</li>
</ul></li>
<li>P/Invoke allows us to:
<ol type="1">
<li>Connect to the Service Control Manager (SCM) with
<code>OpenSCManager</code></li>
<li>Open a handle to a service with <code>OpenService</code></li>
<li>Query the service’s security descriptor with
<code>QueryServiceObjectSecurity</code></li>
</ol></li>
</ul>
<hr />
<h3 id="4-setting-up-the-c-project"><strong>4. Setting Up the C#
Project</strong></h3>
<ol type="1">
<li><strong>Code cleanup</strong>
<ul>
<li>Remove unused <code>using</code> statements.</li>
<li>Replace string concatenation with <strong>string
interpolation</strong> (<code>$"..."</code>).</li>
</ul></li>
<li><strong>Capture service names</strong>
<ul>
<li>Ensure we store the <strong>service name</strong> for later
use.</li>
</ul></li>
</ol>
<hr />
<h3 id="5-creating-getservicesddl-function"><strong>5. Creating
<code>GetServiceSDDL</code> Function</strong></h3>
<p>We’ll create a <strong>public static</strong> function that:</p>
<ul>
<li>Accepts a <code>serviceName</code> string.</li>
<li>Returns the SDDL string.</li>
<li>Steps:
<ol type="1">
<li>Open SCM connection (<code>OpenSCManager</code>).</li>
<li>Get a handle to the target service (<code>OpenService</code>).</li>
<li>Query the service’s security descriptor
(<code>QueryServiceObjectSecurity</code>).</li>
<li>Convert it to a string
(<code>ConvertSecurityDescriptorToStringSecurityDescriptor</code>).</li>
<li>Return the string.</li>
</ol></li>
</ul>
<hr />
<h3 id="6-declaring-external-entry-points"><strong>6. Declaring External
Entry Points</strong></h3>
<ul>
<li>Add <code>using System.Runtime.InteropServices;</code></li>
<li>Use
<code>[DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Auto)]</code>
for P/Invoke.</li>
<li>Declare:
<ul>
<li><code>OpenSCManager</code></li>
<li><code>OpenService</code></li>
<li><code>QueryServiceObjectSecurity</code></li>
<li><code>ConvertSecurityDescriptorToStringSecurityDescriptor</code></li>
<li><code>CloseServiceHandle</code></li>
</ul></li>
</ul>
<hr />
<h3 id="7-creating-enums-for-access-rights"><strong>7. Creating Enums
for Access Rights</strong></h3>
<ul>
<li>Define <strong>Access Rights</strong> as <code>[Flags] enum</code>
so they can be combined with bitwise OR (<code>|</code>):
<ul>
<li><code>SC_MANAGER_CONNECT</code> (0x0001)</li>
<li><code>SC_MANAGER_ENUMERATE_SERVICE</code> (0x0004)</li>
<li><code>READ_CONTROL</code> (0x00020000) — needed for
<code>OpenService</code></li>
</ul></li>
</ul>
<hr />
<h3 id="8-using-openscmanager"><strong>8. Using
<code>OpenSCManager</code></strong></h3>
<ul>
<li>Parameters:
<ul>
<li><code>lpMachineName</code> → <code>null</code> (local machine)</li>
<li><code>lpDatabaseName</code> → <code>null</code> (default active
services DB)</li>
<li>Desired access → combine <code>SC_MANAGER_CONNECT</code> and
<code>SC_MANAGER_ENUMERATE_SERVICE</code></li>
</ul></li>
<li>Check return value:
<ul>
<li>If <code>IntPtr.Zero</code>, call
<code>Marshal.GetLastWin32Error()</code> to get error code.</li>
</ul></li>
</ul>
<hr />
<h3 id="9-using-openservice"><strong>9. Using
<code>OpenService</code></strong></h3>
<ul>
<li>Parameters:
<ul>
<li>SCM handle</li>
<li>Service name</li>
<li>Desired access → <code>READ_CONTROL</code></li>
</ul></li>
<li>Handle error <strong>code 5 (Access Denied)</strong> gracefully by
skipping the service.</li>
</ul>
<hr />
<h3 id="10-querying-the-security-descriptor"><strong>10. Querying the
Security Descriptor</strong></h3>
<ul>
<li>Call <code>QueryServiceObjectSecurity</code> <strong>twice</strong>:
<ol type="1">
<li><p>First with:</p>
<ul>
<li><p>Null pointer for security descriptor</p></li>
<li><p>Buffer size = 0</p></li>
<li><p>Out <code>bytesNeeded</code></p>
<p>→ This tells us how much memory to allocate.</p></li>
</ul></li>
<li><p>Allocate memory with
<code>Marshal.AllocHGlobal(bytesNeeded)</code>.</p></li>
<li><p>Call again, passing the allocated buffer.</p></li>
</ol></li>
<li>Flags used:
<ul>
<li><code>OWNER_SECURITY_INFORMATION</code></li>
<li><code>GROUP_SECURITY_INFORMATION</code></li>
<li><code>DACL_SECURITY_INFORMATION</code></li>
</ul></li>
</ul>
<hr />
<h3 id="11-converting-to-sddl-string"><strong>11. Converting to SDDL
String</strong></h3>
<ul>
<li>Call
<code>ConvertSecurityDescriptorToStringSecurityDescriptor</code>:
<ul>
<li>Pass pointer to security descriptor</li>
<li>Pass same security information flags</li>
<li>Provide a <code>StringBuilder</code> to hold the result</li>
</ul></li>
<li>Return <code>stringBuilder.ToString()</code> as the SDDL.</li>
</ul>
<hr />
<h3 id="12-memory-and-handle-cleanup"><strong>12. Memory and Handle
Cleanup</strong></h3>
<ul>
<li>Always close:
<ul>
<li>Service handle</li>
<li>SCM handle</li>
</ul></li>
<li>Free allocated memory with <code>Marshal.FreeHGlobal()</code>.</li>
</ul>
<hr />
<h3 id="13-error-handling"><strong>13. Error Handling</strong></h3>
<ul>
<li>Wrap operations in <code>try/catch/finally</code>
<ul>
<li><strong>Catch</strong> → handle specific errors like
<code>Access Denied</code>.</li>
<li><strong>Finally</strong> → ensure memory and handles are freed.</li>
</ul></li>
</ul>
<hr />
<h3 id="14-testing-the-utility"><strong>14. Testing the
Utility</strong></h3>
<ul>
<li>Compile and run the service scan tool.</li>
<li>Confirm it:
<ul>
<li>Iterates through all services.</li>
<li>Prints the SDDL for those accessible.</li>
<li>Matches output from <code>sc sdshow &lt;service&gt;</code>.</li>
</ul></li>
</ul>
<hr />
<h3 id="15-next-steps"><strong>15. Next Steps</strong></h3>
<p>In the next lesson:</p>
<ul>
<li>We’ll <strong>parse the SDDL</strong> to determine what privileges
the current user has.</li>
<li>We’ll also integrate the tool into Apollo’s
<code>execute-assembly</code> for in-memory execution.</li>
</ul>
<hr />
<p>✅ <strong>End of Walkthrough</strong> — At this point, students
should:</p>
<ul>
<li>Understand why P/Invoke is necessary for this task.</li>
<li>Know how to open SCM, open services, and retrieve security
descriptors.</li>
<li>See the importance of memory management in C# interop with Win32
APIs.</li>
</ul>
</section>
</div>
