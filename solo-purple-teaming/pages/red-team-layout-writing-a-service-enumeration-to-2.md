---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-2/
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
id="red-team-layout---writing-a-service-enumeration-tool---part-4">Red
Team Layout - Writing A Service Enumeration Tool - Part 4</h1>
<h3 id="1-introduction"><strong>1. Introduction</strong></h3>
<p>In this final stage of building our <strong>Service Scan
Utility</strong>, we’re going to:</p>
<ul>
<li>Review some additional <strong>.NET classes</strong> that will help
us complete the functionality.</li>
<li>Write the code to:
<ul>
<li>Retrieve the binary path of a Windows service.</li>
<li>Check whether the current user can <strong>modify the service
binary</strong>.</li>
</ul></li>
<li>Test the utility on a vulnerable target.</li>
</ul>
<hr />
<h3 id="2-key-net-classes-for-our-service-scan"><strong>2. Key .NET
Classes for Our Service Scan</strong></h3>
<h3 id="windowsidentity-systemsecurityprincipal"><strong>WindowsIdentity
(System.Security.Principal)</strong></h3>
<ul>
<li><p>Represents the identity of a Windows user.</p></li>
<li><p>Can be instantiated using:</p>
<ul>
<li>The current process token</li>
<li>A UPN (User Principal Name)</li>
<li>An explicit token handle</li>
</ul></li>
<li><p><strong>Key Properties:</strong></p>
<ul>
<li><code>Name</code> – Username</li>
<li><code>IsAuthenticated</code> – Boolean flag for authentication
status</li>
<li><code>User</code> – Security Identifier (SID) of the current
user</li>
<li><code>Groups</code> – List of group SIDs the user belongs to</li>
</ul></li>
<li><p><strong>Why we use it:</strong></p>
<p>We’ll retrieve the current user’s SID and group SIDs to compare
against the Access Control Entries (ACEs) in the Discretionary Access
Control List (DACL) of the service binary.</p></li>
</ul>
<hr />
<h3
id="managementobjectsearcher-systemmanagement"><strong>ManagementObjectSearcher
(System.Management)</strong></h3>
<ul>
<li><p>Executes <strong>WMI queries</strong> using WQL (WMI Query
Language).</p></li>
<li><p>Returns a <strong>ManagementObjectCollection</strong> to iterate
over.</p></li>
<li><p><strong>Why we use it:</strong></p>
<p>To query the <code>Win32_Service</code> class and extract the
<code>PathName</code> property, which gives the full path to the service
binary.</p></li>
</ul>
<hr />
<h3 id="fileinfo-systemio"><strong>FileInfo (System.IO)</strong></h3>
<ul>
<li><p>Provides an object-oriented way to work with files.</p></li>
<li><p>Properties: full path, size, timestamps, etc.</p></li>
<li><p>Can also perform file operations (copy, delete, open
stream).</p></li>
<li><p><strong>Why we use it:</strong></p>
<p>To target the service binary file and retrieve its
<code>FileSecurity</code> object for DACL inspection.</p></li>
</ul>
<hr />
<h3 id="filesecurity-systemsecurityaccesscontrol"><strong>FileSecurity
(System.Security.AccessControl)</strong></h3>
<ul>
<li><p>Represents a file’s <strong>security descriptor</strong>,
including its DACL.</p></li>
<li><p><code>GetAccessRules</code> returns a collection of
ACEs.</p></li>
<li><p>ACEs specify:</p>
<ul>
<li>A SID</li>
<li>Granted/denied access rights (read, write, full control)</li>
</ul></li>
<li><p><strong>Why we use it:</strong></p>
<p>To compare current user/group SIDs against ACEs to see if write
permissions are allowed.</p></li>
</ul>
<hr />
<h3 id="3-coding-the-service-binary-path-retrieval"><strong>3. Coding
the Service Binary Path Retrieval</strong></h3>
<p><strong>Function:</strong>
<code>GetServiceBinaryPath(string serviceName)</code></p>
<p><strong>Steps:</strong></p>
<ol type="1">
<li><p>Initialize a <code>path</code> string to
<code>null</code>.</p></li>
<li><p>Build a <strong>WQL query</strong>:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a>SELECT PathName FROM Win32_Service WHERE Name <span class="op">=</span> 'ServiceName'<span class="op">;</span></span></code></pre></div></li>
<li><p>Create a <code>ManagementObjectSearcher</code> with this
query.</p></li>
<li><p>Retrieve results with <code>searcher.Get()</code>.</p></li>
<li><p>Iterate over results and extract the <code>PathName</code>:</p>
<ul>
<li>Use a <strong>null-conditional check</strong> with <code>?</code> to
avoid errors.</li>
<li>Convert to string if not null.</li>
</ul></li>
<li><p>Return the <code>path</code>.</p></li>
</ol>
<hr />
<h3 id="4-checking-if-the-user-can-modify-the-service-binary"><strong>4.
Checking if the User Can Modify the Service Binary</strong></h3>
<p><strong>Function:</strong>
<code>CanModifyServiceBinary(string pathName)</code></p>
<p><strong>Steps:</strong></p>
<ol type="1">
<li><p>Wrap the logic in <strong>error handling</strong> — if anything
fails, return <code>false</code>.</p></li>
<li><p>Create a <code>FileInfo</code> object for the
<code>pathName</code>.</p></li>
<li><p>If the file exists:</p>
<ul>
<li><p>Retrieve its DACL with <code>GetAccessControl()</code>.</p></li>
<li><p>Get all ACEs using:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>dacl<span class="op">.</span><span class="fu">GetAccessRules</span><span class="op">(</span><span class="kw">true</span><span class="op">,</span> <span class="kw">true</span><span class="op">,</span> <span class="kw">typeof</span><span class="op">(</span>SecurityIdentifier<span class="op">))</span></span></code></pre></div></li>
</ul></li>
<li><p>Get the current user SID via:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>WindowsIdentity currentUser <span class="op">=</span> WindowsIdentity<span class="op">.</span><span class="fu">GetCurrent</span><span class="op">();</span></span></code></pre></div></li>
<li><p><strong>Compare permissions:</strong></p>
<ul>
<li>Iterate over ACEs:
<ul>
<li>Cast <code>IdentityReference</code> to
<code>SecurityIdentifier</code>.</li>
<li>If SID matches <strong>current user</strong> or any <strong>current
user’s groups</strong>:
<ul>
<li>Perform a <strong>bitwise AND</strong> on
<code>ace.FileSystemRights</code> with
<code>FileSystemRights.WriteData</code>.</li>
<li>Ensure <code>AccessControlType</code> is <code>Allow</code>.</li>
<li>If both conditions are true → return <code>true</code>.</li>
</ul></li>
</ul></li>
</ul></li>
<li><p>If no match found → return <code>false</code>.</p></li>
</ol>
<hr />
<h3 id="5-integrating-into-the-main-function"><strong>5. Integrating
Into the Main Function</strong></h3>
<ul>
<li>After retrieving the service path with
<code>GetServiceBinaryPath</code>:
<ul>
<li>Call <code>CanModifyServiceBinary(path)</code>.</li>
<li>If <strong>both</strong>:
<ul>
<li>The service can be <strong>started</strong>.</li>
<li>The binary can be <strong>modified</strong>.</li>
<li>→ Display the service as vulnerable.</li>
</ul></li>
</ul></li>
</ul>
<hr />
<h3 id="6-running-and-testing"><strong>6. Running and
Testing</strong></h3>
<ol type="1">
<li><strong>Build the project</strong> and fix any syntax errors (often
missing braces or misplaced parentheses).</li>
<li>Run the utility:
<ul>
<li>It will iterate through <strong>all services</strong>.</li>
<li>For each:
<ul>
<li>Check if the current user can <strong>start</strong> it.</li>
<li>Retrieve the binary path.</li>
<li>Check if the user can <strong>modify</strong> the binary.</li>
</ul></li>
<li>This process can be <strong>slow</strong> because it’s
thorough.</li>
</ul></li>
<li>On the <strong>Zoom breach host</strong>:
<ul>
<li><p>Upload the binary via SCP to the staging server, then to the
Downloads folder.</p></li>
<li><p>Run the service scan utility.</p></li>
<li><p>Example result:</p>
<p>Found <code>ecoin sync</code> service → can both start and modify
binary → <strong>vulnerable service</strong>.</p></li>
</ul></li>
</ol>
<hr />
<h3 id="7-student-task"><strong>7. Student Task</strong></h3>
<ul>
<li>Run your own service scan utility.</li>
<li>Confirm it:
<ul>
<li>Iterates through all services.</li>
<li>Checks start permission.</li>
<li>Checks modify permission on service binaries.</li>
</ul></li>
<li>Identify at least <strong>one vulnerable service</strong>.</li>
</ul>
<hr />
<h3 id="8-next-steps"><strong>8. Next Steps</strong></h3>
<p>In the next lecture, we’ll cover <strong>Apollo’s
<code>execute-assembly</code></strong> command so we can:</p>
<ul>
<li>Register our service scan utility.</li>
<li>Run it <strong>in-memory</strong> for stealth.</li>
</ul>
<p>Final Code:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">;</span></span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">ServiceProcess</span><span class="op">;</span></span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Runtime</span><span class="op">.</span><span class="fu">InteropServices</span><span class="op">;</span></span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Text</span><span class="op">;</span></span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Security</span><span class="op">.</span><span class="fu">Principal</span><span class="op">;</span></span>
<span id="cb4-6"><a href="#cb4-6" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Security</span><span class="op">.</span><span class="fu">AccessControl</span><span class="op">;</span></span>
<span id="cb4-7"><a href="#cb4-7" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Management</span><span class="op">;</span></span>
<span id="cb4-8"><a href="#cb4-8" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Diagnostics</span><span class="op">.</span><span class="fu">Eventing</span><span class="op">.</span><span class="fu">Reader</span><span class="op">;</span></span>
<span id="cb4-9"><a href="#cb4-9" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">IO</span></span>
<span id="cb4-10"><a href="#cb4-10" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Security</span><span class="op">;</span></span>
<span id="cb4-11"><a href="#cb4-11" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-12"><a href="#cb4-12" aria-hidden="true" tabindex="-1"></a><span class="kw">namespace</span> ServiceScanDemo</span>
<span id="cb4-13"><a href="#cb4-13" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb4-14"><a href="#cb4-14" aria-hidden="true" tabindex="-1"></a>    <span class="kw">internal</span> <span class="kw">class</span> Program</span>
<span id="cb4-15"><a href="#cb4-15" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb4-16"><a href="#cb4-16" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span>Flags<span class="op">]</span></span>
<span id="cb4-17"><a href="#cb4-17" aria-hidden="true" tabindex="-1"></a>        <span class="kw">private</span> <span class="kw">enum</span> AccessRights <span class="op">:</span> <span class="dt">uint</span></span>
<span id="cb4-18"><a href="#cb4-18" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb4-19"><a href="#cb4-19" aria-hidden="true" tabindex="-1"></a>            SC_MANAGER_CONNECT <span class="op">=</span> <span class="bn">0x00000001</span><span class="op">,</span></span>
<span id="cb4-20"><a href="#cb4-20" aria-hidden="true" tabindex="-1"></a>            SC_MANAGER_ENUMERATE_SERVICE <span class="op">=</span> <span class="bn">0x00000004</span><span class="op">,</span></span>
<span id="cb4-21"><a href="#cb4-21" aria-hidden="true" tabindex="-1"></a>            READ_CONTROL <span class="op">=</span> <span class="bn">0x0020000</span></span>
<span id="cb4-22"><a href="#cb4-22" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb4-23"><a href="#cb4-23" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-24"><a href="#cb4-24" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span>Flags<span class="op">]</span></span>
<span id="cb4-25"><a href="#cb4-25" aria-hidden="true" tabindex="-1"></a>        <span class="kw">enum</span> SECURITY_INFORMATION <span class="op">:</span> <span class="dt">uint</span></span>
<span id="cb4-26"><a href="#cb4-26" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb4-27"><a href="#cb4-27" aria-hidden="true" tabindex="-1"></a>            OWNER_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x00000001</span><span class="op">,</span></span>
<span id="cb4-28"><a href="#cb4-28" aria-hidden="true" tabindex="-1"></a>            GROUP_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x00000002</span><span class="op">,</span></span>
<span id="cb4-29"><a href="#cb4-29" aria-hidden="true" tabindex="-1"></a>            DACL_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x00000004</span><span class="op">,</span></span>
<span id="cb4-30"><a href="#cb4-30" aria-hidden="true" tabindex="-1"></a>            SACL_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x00000008</span><span class="op">,</span></span>
<span id="cb4-31"><a href="#cb4-31" aria-hidden="true" tabindex="-1"></a>            UNPROTECTED_SACL_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x10000000</span><span class="op">,</span></span>
<span id="cb4-32"><a href="#cb4-32" aria-hidden="true" tabindex="-1"></a>            UNPROTECTED_DACL_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x20000000</span><span class="op">,</span></span>
<span id="cb4-33"><a href="#cb4-33" aria-hidden="true" tabindex="-1"></a>            PROTECTED_SACL_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x40000000</span><span class="op">,</span></span>
<span id="cb4-34"><a href="#cb4-34" aria-hidden="true" tabindex="-1"></a>            PROTECTED_DACL_SECURITY_INFORMATION <span class="op">=</span> <span class="bn">0x80000000</span></span>
<span id="cb4-35"><a href="#cb4-35" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb4-36"><a href="#cb4-36" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-37"><a href="#cb4-37" aria-hidden="true" tabindex="-1"></a>        <span class="co">// CloseServiceHandle</span></span>
<span id="cb4-38"><a href="#cb4-38" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"advapi32.dll"</span><span class="op">,</span> SetLastError <span class="op">=</span> <span class="kw">true</span><span class="op">)]</span></span>
<span id="cb4-39"><a href="#cb4-39" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="kw">return</span><span class="op">:</span> <span class="fu">MarshalAs</span><span class="op">(</span>UnmanagedType<span class="op">.</span><span class="fu">Bool</span><span class="op">)]</span></span>
<span id="cb4-40"><a href="#cb4-40" aria-hidden="true" tabindex="-1"></a>        <span class="kw">private</span> <span class="kw">static</span> <span class="kw">extern</span> <span class="dt">bool</span> <span class="fu">CloseServiceHandle</span><span class="op">(</span>IntPtr hSCObject<span class="op">);</span></span>
<span id="cb4-41"><a href="#cb4-41" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-42"><a href="#cb4-42" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"advapi32.dll"</span><span class="op">,</span> SetLastError <span class="op">=</span> <span class="kw">true</span><span class="op">,</span> CharSet <span class="op">=</span> CharSet<span class="op">.</span><span class="fu">Auto</span><span class="op">)]</span></span>
<span id="cb4-43"><a href="#cb4-43" aria-hidden="true" tabindex="-1"></a>        <span class="kw">private</span> <span class="kw">static</span> <span class="kw">extern</span> IntPtr <span class="fu">OpenSCManager</span><span class="op">(</span></span>
<span id="cb4-44"><a href="#cb4-44" aria-hidden="true" tabindex="-1"></a>          <span class="dt">string</span> lpMachineName<span class="op">,</span></span>
<span id="cb4-45"><a href="#cb4-45" aria-hidden="true" tabindex="-1"></a>          <span class="dt">string</span> lpDatabaseName<span class="op">,</span></span>
<span id="cb4-46"><a href="#cb4-46" aria-hidden="true" tabindex="-1"></a>          <span class="dt">uint</span> dwDesiredAccess</span>
<span id="cb4-47"><a href="#cb4-47" aria-hidden="true" tabindex="-1"></a>        <span class="op">);</span></span>
<span id="cb4-48"><a href="#cb4-48" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-49"><a href="#cb4-49" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"advapi32.dll"</span><span class="op">,</span> SetLastError <span class="op">=</span> <span class="kw">true</span><span class="op">,</span> CharSet <span class="op">=</span> CharSet<span class="op">.</span><span class="fu">Auto</span><span class="op">)]</span></span>
<span id="cb4-50"><a href="#cb4-50" aria-hidden="true" tabindex="-1"></a>        <span class="kw">private</span> <span class="kw">static</span> <span class="kw">extern</span> IntPtr <span class="fu">OpenService</span><span class="op">(</span></span>
<span id="cb4-51"><a href="#cb4-51" aria-hidden="true" tabindex="-1"></a>          IntPtr hSCManager<span class="op">,</span></span>
<span id="cb4-52"><a href="#cb4-52" aria-hidden="true" tabindex="-1"></a>          <span class="dt">string</span> lpServiceName<span class="op">,</span></span>
<span id="cb4-53"><a href="#cb4-53" aria-hidden="true" tabindex="-1"></a>          <span class="dt">uint</span> dwDesiredAccess</span>
<span id="cb4-54"><a href="#cb4-54" aria-hidden="true" tabindex="-1"></a>        <span class="op">);</span></span>
<span id="cb4-55"><a href="#cb4-55" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-56"><a href="#cb4-56" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"advapi32.dll"</span><span class="op">,</span> SetLastError <span class="op">=</span> <span class="kw">true</span><span class="op">)]</span></span>
<span id="cb4-57"><a href="#cb4-57" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="kw">return</span><span class="op">:</span> <span class="fu">MarshalAs</span><span class="op">(</span>UnmanagedType<span class="op">.</span><span class="fu">Bool</span><span class="op">)]</span></span>
<span id="cb4-58"><a href="#cb4-58" aria-hidden="true" tabindex="-1"></a>        <span class="kw">private</span> <span class="kw">static</span> <span class="kw">extern</span> <span class="dt">bool</span> <span class="fu">QueryServiceObjectSecurity</span><span class="op">(</span></span>
<span id="cb4-59"><a href="#cb4-59" aria-hidden="true" tabindex="-1"></a>            IntPtr hService<span class="op">,</span></span>
<span id="cb4-60"><a href="#cb4-60" aria-hidden="true" tabindex="-1"></a>            <span class="dt">uint</span> dwSecurityInformation<span class="op">,</span></span>
<span id="cb4-61"><a href="#cb4-61" aria-hidden="true" tabindex="-1"></a>            IntPtr lpSecurityDescriptor<span class="op">,</span></span>
<span id="cb4-62"><a href="#cb4-62" aria-hidden="true" tabindex="-1"></a>            <span class="dt">uint</span> cbBufSize<span class="op">,</span></span>
<span id="cb4-63"><a href="#cb4-63" aria-hidden="true" tabindex="-1"></a>            <span class="kw">out</span> <span class="dt">uint</span> pcbBytesNeeded</span>
<span id="cb4-64"><a href="#cb4-64" aria-hidden="true" tabindex="-1"></a>        <span class="op">);</span></span>
<span id="cb4-65"><a href="#cb4-65" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-66"><a href="#cb4-66" aria-hidden="true" tabindex="-1"></a>        <span class="kw">private</span> <span class="dt">const</span> <span class="dt">uint</span> SDDL_REVISION_1 <span class="op">=</span> <span class="dv">1</span><span class="op">;</span></span>
<span id="cb4-67"><a href="#cb4-67" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"advapi32.dll"</span><span class="op">,</span> SetLastError <span class="op">=</span> <span class="kw">true</span><span class="op">,</span> CharSet <span class="op">=</span> CharSet<span class="op">.</span><span class="fu">Auto</span><span class="op">)]</span></span>
<span id="cb4-68"><a href="#cb4-68" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="kw">return</span><span class="op">:</span> <span class="fu">MarshalAs</span><span class="op">(</span>UnmanagedType<span class="op">.</span><span class="fu">Bool</span><span class="op">)]</span></span>
<span id="cb4-69"><a href="#cb4-69" aria-hidden="true" tabindex="-1"></a>        <span class="kw">private</span> <span class="kw">static</span> <span class="kw">extern</span> <span class="dt">bool</span> <span class="fu">ConvertSecurityDescriptorToStringSecurityDescriptor</span><span class="op">(</span></span>
<span id="cb4-70"><a href="#cb4-70" aria-hidden="true" tabindex="-1"></a>            IntPtr SecurityDescriptor<span class="op">,</span></span>
<span id="cb4-71"><a href="#cb4-71" aria-hidden="true" tabindex="-1"></a>            <span class="dt">uint</span> RequestedStringSDRevision<span class="op">,</span></span>
<span id="cb4-72"><a href="#cb4-72" aria-hidden="true" tabindex="-1"></a>            <span class="dt">uint</span> SecurityInformation<span class="op">,</span></span>
<span id="cb4-73"><a href="#cb4-73" aria-hidden="true" tabindex="-1"></a>            <span class="kw">out</span> StringBuilder StringSecurityDescriptor<span class="op">,</span></span>
<span id="cb4-74"><a href="#cb4-74" aria-hidden="true" tabindex="-1"></a>            <span class="kw">out</span> <span class="dt">uint</span> StringSecurityDescriptorLen</span>
<span id="cb4-75"><a href="#cb4-75" aria-hidden="true" tabindex="-1"></a>        <span class="op">);</span></span>
<span id="cb4-76"><a href="#cb4-76" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-77"><a href="#cb4-77" aria-hidden="true" tabindex="-1"></a>        <span class="kw">public</span> <span class="kw">static</span> <span class="dt">string</span> <span class="fu">GetServiceSDDL</span><span class="op">(</span><span class="dt">string</span> serviceName<span class="op">)</span></span>
<span id="cb4-78"><a href="#cb4-78" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb4-79"><a href="#cb4-79" aria-hidden="true" tabindex="-1"></a>            <span class="co">// open connection to the SCM using OpenSCManager Win API call that lives advapi32.dll</span></span>
<span id="cb4-80"><a href="#cb4-80" aria-hidden="true" tabindex="-1"></a>            IntPtr scmHandle <span class="op">=</span> <span class="fu">OpenSCManager</span><span class="op">(</span><span class="kw">null</span><span class="op">,</span> <span class="kw">null</span><span class="op">,</span> <span class="op">(</span><span class="dt">uint</span><span class="op">)(</span>AccessRights<span class="op">.</span><span class="fu">SC_MANAGER_ENUMERATE_SERVICE</span> <span class="op">|</span> AccessRights<span class="op">.</span><span class="fu">SC_MANAGER_CONNECT</span><span class="op">));</span></span>
<span id="cb4-81"><a href="#cb4-81" aria-hidden="true" tabindex="-1"></a>            <span class="kw">if</span> <span class="op">(</span>scmHandle <span class="op">==</span> IntPtr<span class="op">.</span><span class="fu">Zero</span><span class="op">)</span></span>
<span id="cb4-82"><a href="#cb4-82" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-83"><a href="#cb4-83" aria-hidden="true" tabindex="-1"></a>                <span class="kw">throw</span> <span class="kw">new</span> <span class="fu">Exception</span><span class="op">(</span>$<span class="st">"Failed to connect to SCM. ERROR: {Marshal.GetLastWin32Error()}"</span><span class="op">);</span></span>
<span id="cb4-84"><a href="#cb4-84" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-85"><a href="#cb4-85" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-86"><a href="#cb4-86" aria-hidden="true" tabindex="-1"></a>            <span class="co">// Get handle to service using OpenService Win API call that lives in advapi32.dll</span></span>
<span id="cb4-87"><a href="#cb4-87" aria-hidden="true" tabindex="-1"></a>            IntPtr serviceHandle <span class="op">=</span> IntPtr<span class="op">.</span><span class="fu">Zero</span><span class="op">;</span></span>
<span id="cb4-88"><a href="#cb4-88" aria-hidden="true" tabindex="-1"></a>            IntPtr sdPtr <span class="op">=</span> IntPtr<span class="op">.</span><span class="fu">Zero</span><span class="op">;</span></span>
<span id="cb4-89"><a href="#cb4-89" aria-hidden="true" tabindex="-1"></a>            StringBuilder sddlStringBuilder <span class="op">=</span> <span class="kw">null</span><span class="op">;</span></span>
<span id="cb4-90"><a href="#cb4-90" aria-hidden="true" tabindex="-1"></a>            <span class="kw">try</span></span>
<span id="cb4-91"><a href="#cb4-91" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-92"><a href="#cb4-92" aria-hidden="true" tabindex="-1"></a>                serviceHandle <span class="op">=</span> <span class="fu">OpenService</span><span class="op">(</span>scmHandle<span class="op">,</span> serviceName<span class="op">,</span> <span class="op">(</span><span class="dt">uint</span><span class="op">)</span>AccessRights<span class="op">.</span><span class="fu">READ_CONTROL</span><span class="op">);</span></span>
<span id="cb4-93"><a href="#cb4-93" aria-hidden="true" tabindex="-1"></a>                <span class="co">// Query the service object for the security descriptor and copy into memory</span></span>
<span id="cb4-94"><a href="#cb4-94" aria-hidden="true" tabindex="-1"></a>                <span class="co">// Query serviceHandle object for security descriptor using QueryServiceObjectSecurity</span></span>
<span id="cb4-95"><a href="#cb4-95" aria-hidden="true" tabindex="-1"></a>                <span class="dt">uint</span> bytesNeeded <span class="op">=</span> <span class="dv">0</span><span class="op">;</span></span>
<span id="cb4-96"><a href="#cb4-96" aria-hidden="true" tabindex="-1"></a>                <span class="fu">QueryServiceObjectSecurity</span><span class="op">(</span>serviceHandle<span class="op">,</span> <span class="op">(</span><span class="dt">uint</span><span class="op">)(</span>SECURITY_INFORMATION<span class="op">.</span><span class="fu">DACL_SECURITY_INFORMATION</span> <span class="op">|</span> SECURITY_INFORMATION<span class="op">.</span><span class="fu">OWNER_SECURITY_INFORMATION</span> <span class="op">|</span> SECURITY_INFORMATION<span class="op">.</span><span class="fu">GROUP_SECURITY_INFORMATION</span><span class="op">),</span> IntPtr<span class="op">.</span><span class="fu">Zero</span><span class="op">,</span> <span class="dv">0</span><span class="op">,</span> <span class="kw">out</span> bytesNeeded<span class="op">);</span></span>
<span id="cb4-97"><a href="#cb4-97" aria-hidden="true" tabindex="-1"></a>                sdPtr <span class="op">=</span> Marshal<span class="op">.</span><span class="fu">AllocHGlobal</span><span class="op">((</span><span class="dt">int</span><span class="op">)</span>bytesNeeded<span class="op">);</span></span>
<span id="cb4-98"><a href="#cb4-98" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-99"><a href="#cb4-99" aria-hidden="true" tabindex="-1"></a>                <span class="fu">QueryServiceObjectSecurity</span><span class="op">(</span>serviceHandle<span class="op">,</span> <span class="op">(</span><span class="dt">uint</span><span class="op">)(</span>SECURITY_INFORMATION<span class="op">.</span><span class="fu">DACL_SECURITY_INFORMATION</span> <span class="op">|</span> SECURITY_INFORMATION<span class="op">.</span><span class="fu">OWNER_SECURITY_INFORMATION</span> <span class="op">|</span> SECURITY_INFORMATION<span class="op">.</span><span class="fu">GROUP_SECURITY_INFORMATION</span><span class="op">),</span> sdPtr<span class="op">,</span> bytesNeeded<span class="op">,</span> <span class="kw">out</span> bytesNeeded<span class="op">);</span></span>
<span id="cb4-100"><a href="#cb4-100" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-101"><a href="#cb4-101" aria-hidden="true" tabindex="-1"></a>                <span class="co">// Convert the security descriptor to string and return</span></span>
<span id="cb4-102"><a href="#cb4-102" aria-hidden="true" tabindex="-1"></a>                <span class="co">// Get Security Descriptor</span></span>
<span id="cb4-103"><a href="#cb4-103" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-104"><a href="#cb4-104" aria-hidden="true" tabindex="-1"></a>                <span class="dt">uint</span> sddlStringLen <span class="op">=</span> <span class="dv">0</span><span class="op">;</span></span>
<span id="cb4-105"><a href="#cb4-105" aria-hidden="true" tabindex="-1"></a>                <span class="fu">ConvertSecurityDescriptorToStringSecurityDescriptor</span><span class="op">(</span></span>
<span id="cb4-106"><a href="#cb4-106" aria-hidden="true" tabindex="-1"></a>                    sdPtr<span class="op">,</span></span>
<span id="cb4-107"><a href="#cb4-107" aria-hidden="true" tabindex="-1"></a>                    SDDL_REVISION_1<span class="op">,</span></span>
<span id="cb4-108"><a href="#cb4-108" aria-hidden="true" tabindex="-1"></a>                    <span class="op">(</span><span class="dt">uint</span><span class="op">)(</span>SECURITY_INFORMATION<span class="op">.</span><span class="fu">DACL_SECURITY_INFORMATION</span> <span class="op">|</span> SECURITY_INFORMATION<span class="op">.</span><span class="fu">OWNER_SECURITY_INFORMATION</span> <span class="op">|</span> SECURITY_INFORMATION<span class="op">.</span><span class="fu">GROUP_SECURITY_INFORMATION</span><span class="op">),</span></span>
<span id="cb4-109"><a href="#cb4-109" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">out</span> sddlStringBuilder<span class="op">,</span></span>
<span id="cb4-110"><a href="#cb4-110" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">out</span> sddlStringLen</span>
<span id="cb4-111"><a href="#cb4-111" aria-hidden="true" tabindex="-1"></a>                    <span class="op">);</span></span>
<span id="cb4-112"><a href="#cb4-112" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-113"><a href="#cb4-113" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-114"><a href="#cb4-114" aria-hidden="true" tabindex="-1"></a>            <span class="kw">catch</span></span>
<span id="cb4-115"><a href="#cb4-115" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-116"><a href="#cb4-116" aria-hidden="true" tabindex="-1"></a>                <span class="dt">int</span> error <span class="op">=</span> Marshal<span class="op">.</span><span class="fu">GetLastWin32Error</span><span class="op">();</span></span>
<span id="cb4-117"><a href="#cb4-117" aria-hidden="true" tabindex="-1"></a>                <span class="kw">if</span> <span class="op">(</span>error <span class="op">!=</span> <span class="dv">5</span><span class="op">)</span></span>
<span id="cb4-118"><a href="#cb4-118" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-119"><a href="#cb4-119" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">throw</span> <span class="kw">new</span> <span class="fu">Exception</span><span class="op">(</span>$<span class="st">"Could not get handle to service. ERROR: {error}"</span><span class="op">);</span></span>
<span id="cb4-120"><a href="#cb4-120" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-121"><a href="#cb4-121" aria-hidden="true" tabindex="-1"></a>                <span class="kw">else</span></span>
<span id="cb4-122"><a href="#cb4-122" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-123"><a href="#cb4-123" aria-hidden="true" tabindex="-1"></a>                    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">"Access Denied to service {serviceName}"</span><span class="op">);</span></span>
<span id="cb4-124"><a href="#cb4-124" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">return</span> <span class="kw">null</span><span class="op">;</span></span>
<span id="cb4-125"><a href="#cb4-125" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-126"><a href="#cb4-126" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-127"><a href="#cb4-127" aria-hidden="true" tabindex="-1"></a>            <span class="kw">finally</span></span>
<span id="cb4-128"><a href="#cb4-128" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-129"><a href="#cb4-129" aria-hidden="true" tabindex="-1"></a>                <span class="co">// Clean UP</span></span>
<span id="cb4-130"><a href="#cb4-130" aria-hidden="true" tabindex="-1"></a>                <span class="kw">if</span> <span class="op">(</span>sdPtr <span class="op">!=</span> IntPtr<span class="op">.</span><span class="fu">Zero</span><span class="op">)</span></span>
<span id="cb4-131"><a href="#cb4-131" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-132"><a href="#cb4-132" aria-hidden="true" tabindex="-1"></a>                    Marshal<span class="op">.</span><span class="fu">FreeHGlobal</span><span class="op">(</span>sdPtr<span class="op">);</span></span>
<span id="cb4-133"><a href="#cb4-133" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-134"><a href="#cb4-134" aria-hidden="true" tabindex="-1"></a>                <span class="kw">if</span> <span class="op">(</span>serviceHandle <span class="op">!=</span> IntPtr<span class="op">.</span><span class="fu">Zero</span><span class="op">)</span></span>
<span id="cb4-135"><a href="#cb4-135" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-136"><a href="#cb4-136" aria-hidden="true" tabindex="-1"></a>                    <span class="fu">CloseServiceHandle</span><span class="op">(</span>serviceHandle<span class="op">);</span></span>
<span id="cb4-137"><a href="#cb4-137" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-138"><a href="#cb4-138" aria-hidden="true" tabindex="-1"></a>                <span class="kw">if</span> <span class="op">(</span>scmHandle <span class="op">!=</span> IntPtr<span class="op">.</span><span class="fu">Zero</span><span class="op">)</span></span>
<span id="cb4-139"><a href="#cb4-139" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-140"><a href="#cb4-140" aria-hidden="true" tabindex="-1"></a>                    <span class="fu">CloseServiceHandle</span><span class="op">(</span>scmHandle<span class="op">);</span></span>
<span id="cb4-141"><a href="#cb4-141" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-142"><a href="#cb4-142" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-143"><a href="#cb4-143" aria-hidden="true" tabindex="-1"></a>            <span class="kw">if</span> <span class="op">(</span>sddlStringBuilder <span class="op">!=</span> <span class="kw">null</span><span class="op">)</span></span>
<span id="cb4-144"><a href="#cb4-144" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-145"><a href="#cb4-145" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> sddlStringBuilder<span class="op">.</span><span class="fu">ToString</span><span class="op">();</span></span>
<span id="cb4-146"><a href="#cb4-146" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-147"><a href="#cb4-147" aria-hidden="true" tabindex="-1"></a>            <span class="kw">else</span></span>
<span id="cb4-148"><a href="#cb4-148" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-149"><a href="#cb4-149" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> <span class="kw">null</span><span class="op">;</span></span>
<span id="cb4-150"><a href="#cb4-150" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-151"><a href="#cb4-151" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-152"><a href="#cb4-152" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb4-153"><a href="#cb4-153" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-154"><a href="#cb4-154" aria-hidden="true" tabindex="-1"></a>        <span class="kw">public</span> <span class="kw">static</span> <span class="dt">bool</span> <span class="fu">CanStartService</span><span class="op">(</span><span class="dt">string</span> sddl<span class="op">)</span></span>
<span id="cb4-155"><a href="#cb4-155" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb4-156"><a href="#cb4-156" aria-hidden="true" tabindex="-1"></a>            <span class="kw">if</span> <span class="op">(</span>sddl <span class="op">!=</span> <span class="kw">null</span><span class="op">)</span></span>
<span id="cb4-157"><a href="#cb4-157" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-158"><a href="#cb4-158" aria-hidden="true" tabindex="-1"></a>                WindowsIdentity currentUser <span class="op">=</span> WindowsIdentity<span class="op">.</span><span class="fu">GetCurrent</span><span class="op">();</span></span>
<span id="cb4-159"><a href="#cb4-159" aria-hidden="true" tabindex="-1"></a>                RawSecurityDescriptor rsd <span class="op">=</span> <span class="kw">new</span> <span class="fu">RawSecurityDescriptor</span><span class="op">(</span>sddl<span class="op">);</span></span>
<span id="cb4-160"><a href="#cb4-160" aria-hidden="true" tabindex="-1"></a>                <span class="kw">foreach</span> <span class="op">(</span>CommonAce ace <span class="kw">in</span> rsd<span class="op">.</span><span class="fu">DiscretionaryAcl</span><span class="op">)</span></span>
<span id="cb4-161"><a href="#cb4-161" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-162"><a href="#cb4-162" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">if</span> <span class="op">(</span>ace<span class="op">.</span><span class="fu">AceType</span> <span class="op">==</span> AceType<span class="op">.</span><span class="fu">AccessAllowed</span><span class="op">)</span></span>
<span id="cb4-163"><a href="#cb4-163" aria-hidden="true" tabindex="-1"></a>                    <span class="op">{</span></span>
<span id="cb4-164"><a href="#cb4-164" aria-hidden="true" tabindex="-1"></a>                        <span class="kw">if</span> <span class="op">(</span>currentUser<span class="op">.</span><span class="fu">User</span> <span class="op">==</span> ace<span class="op">.</span><span class="fu">SecurityIdentifier</span> <span class="op">||</span> currentUser<span class="op">.</span><span class="fu">Groups</span><span class="op">.</span><span class="fu">Contains</span><span class="op">(</span>ace<span class="op">.</span><span class="fu">SecurityIdentifier</span><span class="op">))</span></span>
<span id="cb4-165"><a href="#cb4-165" aria-hidden="true" tabindex="-1"></a>                        <span class="op">{</span></span>
<span id="cb4-166"><a href="#cb4-166" aria-hidden="true" tabindex="-1"></a>                            <span class="kw">return</span> <span class="kw">true</span><span class="op">;</span></span>
<span id="cb4-167"><a href="#cb4-167" aria-hidden="true" tabindex="-1"></a>                        <span class="op">}</span></span>
<span id="cb4-168"><a href="#cb4-168" aria-hidden="true" tabindex="-1"></a>                    <span class="op">}</span></span>
<span id="cb4-169"><a href="#cb4-169" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-170"><a href="#cb4-170" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-171"><a href="#cb4-171" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-172"><a href="#cb4-172" aria-hidden="true" tabindex="-1"></a>            <span class="kw">return</span> <span class="kw">false</span><span class="op">;</span></span>
<span id="cb4-173"><a href="#cb4-173" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb4-174"><a href="#cb4-174" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-175"><a href="#cb4-175" aria-hidden="true" tabindex="-1"></a>        <span class="kw">public</span> <span class="kw">static</span> <span class="dt">string</span> <span class="fu">GetServiceBinaryPath</span><span class="op">(</span><span class="dt">string</span> serviceName<span class="op">)</span></span>
<span id="cb4-176"><a href="#cb4-176" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb4-177"><a href="#cb4-177" aria-hidden="true" tabindex="-1"></a>            <span class="dt">string</span> path <span class="op">=</span> <span class="kw">null</span><span class="op">;</span></span>
<span id="cb4-178"><a href="#cb4-178" aria-hidden="true" tabindex="-1"></a>            <span class="dt">string</span> query <span class="op">=</span> $<span class="st">"SELECT PathName FROM Win32_Service WHERE Name = '{serviceName}'"</span><span class="op">;</span></span>
<span id="cb4-179"><a href="#cb4-179" aria-hidden="true" tabindex="-1"></a>            ManagementObjectSearcher searcher <span class="op">=</span> <span class="kw">new</span> <span class="fu">ManagementObjectSearcher</span><span class="op">(</span>query<span class="op">);</span></span>
<span id="cb4-180"><a href="#cb4-180" aria-hidden="true" tabindex="-1"></a>            ManagementObjectCollection results <span class="op">=</span> searcher<span class="op">.</span><span class="fu">Get</span><span class="op">();</span></span>
<span id="cb4-181"><a href="#cb4-181" aria-hidden="true" tabindex="-1"></a>            <span class="kw">foreach</span> <span class="op">(</span>ManagementObject result <span class="kw">in</span> results<span class="op">)</span></span>
<span id="cb4-182"><a href="#cb4-182" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-183"><a href="#cb4-183" aria-hidden="true" tabindex="-1"></a>                path <span class="op">=</span> result<span class="op">[</span><span class="st">"PathName"</span><span class="op">]?.</span><span class="fu">ToString</span><span class="op">();</span></span>
<span id="cb4-184"><a href="#cb4-184" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-185"><a href="#cb4-185" aria-hidden="true" tabindex="-1"></a>            <span class="kw">return</span> path<span class="op">;</span></span>
<span id="cb4-186"><a href="#cb4-186" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb4-187"><a href="#cb4-187" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-188"><a href="#cb4-188" aria-hidden="true" tabindex="-1"></a>        <span class="kw">public</span> <span class="kw">static</span> <span class="dt">bool</span> <span class="fu">CanModifyServiceBinary</span><span class="op">(</span><span class="dt">string</span> pathName<span class="op">)</span></span>
<span id="cb4-189"><a href="#cb4-189" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb4-190"><a href="#cb4-190" aria-hidden="true" tabindex="-1"></a>            <span class="kw">try</span></span>
<span id="cb4-191"><a href="#cb4-191" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-192"><a href="#cb4-192" aria-hidden="true" tabindex="-1"></a>                FileInfo fileInfo <span class="op">=</span> <span class="kw">new</span> <span class="fu">FileInfo</span><span class="op">(</span>pathName<span class="op">);</span></span>
<span id="cb4-193"><a href="#cb4-193" aria-hidden="true" tabindex="-1"></a>                <span class="kw">if</span> <span class="op">(</span>fileInfo<span class="op">.</span><span class="fu">Exists</span><span class="op">)</span></span>
<span id="cb4-194"><a href="#cb4-194" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-195"><a href="#cb4-195" aria-hidden="true" tabindex="-1"></a>                    FileSecurity dacl <span class="op">=</span> fileInfo<span class="op">.</span><span class="fu">GetAccessControl</span><span class="op">();</span></span>
<span id="cb4-196"><a href="#cb4-196" aria-hidden="true" tabindex="-1"></a>                    WindowsIdentity currentUser <span class="op">=</span> WindowsIdentity<span class="op">.</span><span class="fu">GetCurrent</span><span class="op">();</span></span>
<span id="cb4-197"><a href="#cb4-197" aria-hidden="true" tabindex="-1"></a>                    AuthorizationRuleCollection aces <span class="op">=</span> dacl<span class="op">.</span><span class="fu">GetAccessRules</span><span class="op">(</span><span class="kw">true</span><span class="op">,</span> <span class="kw">true</span><span class="op">,</span> <span class="kw">typeof</span><span class="op">(</span>SecurityIdentifier<span class="op">));</span></span>
<span id="cb4-198"><a href="#cb4-198" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">foreach</span><span class="op">(</span>FileSystemAccessRule ace <span class="kw">in</span> aces<span class="op">)</span></span>
<span id="cb4-199"><a href="#cb4-199" aria-hidden="true" tabindex="-1"></a>                    <span class="op">{</span></span>
<span id="cb4-200"><a href="#cb4-200" aria-hidden="true" tabindex="-1"></a>                        <span class="dt">var</span> sid <span class="op">=</span> <span class="op">(</span>SecurityIdentifier<span class="op">)</span>ace<span class="op">.</span><span class="fu">IdentityReference</span><span class="op">.</span><span class="fu">Translate</span><span class="op">(</span><span class="kw">typeof</span><span class="op">(</span>SecurityIdentifier<span class="op">));</span></span>
<span id="cb4-201"><a href="#cb4-201" aria-hidden="true" tabindex="-1"></a>                        <span class="kw">if</span><span class="op">(</span>currentUser<span class="op">.</span><span class="fu">User</span> <span class="op">!=</span> <span class="kw">null</span> <span class="op">&amp;&amp;</span> sid <span class="op">==</span> currentUser<span class="op">.</span><span class="fu">User</span><span class="op">)</span></span>
<span id="cb4-202"><a href="#cb4-202" aria-hidden="true" tabindex="-1"></a>                        <span class="op">{</span></span>
<span id="cb4-203"><a href="#cb4-203" aria-hidden="true" tabindex="-1"></a>                            <span class="kw">if</span><span class="op">((</span>ace<span class="op">.</span><span class="fu">FileSystemRights</span> <span class="op">&amp;</span> FileSystemRights<span class="op">.</span><span class="fu">WriteData</span><span class="op">)</span> <span class="op">==</span> FileSystemRights<span class="op">.</span><span class="fu">WriteData</span> </span>
<span id="cb4-204"><a href="#cb4-204" aria-hidden="true" tabindex="-1"></a>                                   <span class="op">&amp;&amp;</span> ace<span class="op">.</span><span class="fu">AccessControlType</span> <span class="op">==</span> AccessControlType<span class="op">.</span><span class="fu">Allow</span><span class="op">)</span> <span class="op">{</span></span>
<span id="cb4-205"><a href="#cb4-205" aria-hidden="true" tabindex="-1"></a>                                <span class="kw">return</span> <span class="kw">true</span><span class="op">;</span></span>
<span id="cb4-206"><a href="#cb4-206" aria-hidden="true" tabindex="-1"></a>                            <span class="op">}</span></span>
<span id="cb4-207"><a href="#cb4-207" aria-hidden="true" tabindex="-1"></a>                            <span class="kw">if</span> <span class="op">(</span>currentUser<span class="op">.</span><span class="fu">Groups</span><span class="op">.</span><span class="fu">Contains</span><span class="op">(</span>sid<span class="op">))</span></span>
<span id="cb4-208"><a href="#cb4-208" aria-hidden="true" tabindex="-1"></a>                            <span class="op">{</span></span>
<span id="cb4-209"><a href="#cb4-209" aria-hidden="true" tabindex="-1"></a>                                <span class="kw">if</span> <span class="op">((</span>ace<span class="op">.</span><span class="fu">FileSystemRights</span> <span class="op">&amp;</span> FileSystemRights<span class="op">.</span><span class="fu">WriteData</span><span class="op">)</span> <span class="op">==</span> FileSystemRights<span class="op">.</span><span class="fu">WriteData</span></span>
<span id="cb4-210"><a href="#cb4-210" aria-hidden="true" tabindex="-1"></a>                                   <span class="op">&amp;&amp;</span> ace<span class="op">.</span><span class="fu">AccessControlType</span> <span class="op">==</span> AccessControlType<span class="op">.</span><span class="fu">Allow</span><span class="op">)</span></span>
<span id="cb4-211"><a href="#cb4-211" aria-hidden="true" tabindex="-1"></a>                                <span class="op">{</span></span>
<span id="cb4-212"><a href="#cb4-212" aria-hidden="true" tabindex="-1"></a>                                    <span class="kw">return</span> <span class="kw">true</span><span class="op">;</span></span>
<span id="cb4-213"><a href="#cb4-213" aria-hidden="true" tabindex="-1"></a>                                <span class="op">}</span></span>
<span id="cb4-214"><a href="#cb4-214" aria-hidden="true" tabindex="-1"></a>                            <span class="op">}</span></span>
<span id="cb4-215"><a href="#cb4-215" aria-hidden="true" tabindex="-1"></a>                        <span class="op">}</span></span>
<span id="cb4-216"><a href="#cb4-216" aria-hidden="true" tabindex="-1"></a>                    <span class="op">}</span></span>
<span id="cb4-217"><a href="#cb4-217" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-218"><a href="#cb4-218" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-219"><a href="#cb4-219" aria-hidden="true" tabindex="-1"></a>            <span class="kw">catch</span> <span class="op">(</span>Exception ex<span class="op">)</span></span>
<span id="cb4-220"><a href="#cb4-220" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-221"><a href="#cb4-221" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> <span class="kw">false</span><span class="op">;</span></span>
<span id="cb4-222"><a href="#cb4-222" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-223"><a href="#cb4-223" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-224"><a href="#cb4-224" aria-hidden="true" tabindex="-1"></a>            <span class="kw">return</span> <span class="kw">false</span><span class="op">;</span></span>
<span id="cb4-225"><a href="#cb4-225" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb4-226"><a href="#cb4-226" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-227"><a href="#cb4-227" aria-hidden="true" tabindex="-1"></a>        <span class="kw">static</span> <span class="dt">void</span> <span class="fu">Main</span><span class="op">(</span><span class="dt">string</span><span class="op">[]</span> args<span class="op">)</span></span>
<span id="cb4-228"><a href="#cb4-228" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb4-229"><a href="#cb4-229" aria-hidden="true" tabindex="-1"></a>            ServiceController<span class="op">[]</span> services <span class="op">=</span> ServiceController<span class="op">.</span><span class="fu">GetServices</span><span class="op">();</span></span>
<span id="cb4-230"><a href="#cb4-230" aria-hidden="true" tabindex="-1"></a>            <span class="kw">foreach</span> <span class="op">(</span>ServiceController service <span class="kw">in</span> services<span class="op">)</span></span>
<span id="cb4-231"><a href="#cb4-231" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb4-232"><a href="#cb4-232" aria-hidden="true" tabindex="-1"></a>                <span class="dt">string</span> sddl <span class="op">=</span> <span class="fu">GetServiceSDDL</span><span class="op">(</span>service<span class="op">.</span><span class="fu">ServiceName</span><span class="op">);</span></span>
<span id="cb4-233"><a href="#cb4-233" aria-hidden="true" tabindex="-1"></a>                <span class="kw">if</span> <span class="op">(</span><span class="fu">CanStartService</span><span class="op">(</span>sddl<span class="op">))</span></span>
<span id="cb4-234"><a href="#cb4-234" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb4-235"><a href="#cb4-235" aria-hidden="true" tabindex="-1"></a>                    </span>
<span id="cb4-236"><a href="#cb4-236" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">if</span> <span class="op">(</span><span class="fu">CanModifyServiceBinary</span><span class="op">(</span><span class="fu">GetServiceBinaryPath</span><span class="op">(</span>service<span class="op">.</span><span class="fu">ServiceName</span><span class="op">))){</span></span>
<span id="cb4-237"><a href="#cb4-237" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">"Display Name: {service.DisplayName}"</span><span class="op">);</span></span>
<span id="cb4-238"><a href="#cb4-238" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">"Start Type: {service.StartType}"</span><span class="op">);</span></span>
<span id="cb4-239"><a href="#cb4-239" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">"Status: {service.Status}"</span><span class="op">);</span></span>
<span id="cb4-240"><a href="#cb4-240" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">"Service Name: {service.ServiceName}"</span><span class="op">);</span></span>
<span id="cb4-241"><a href="#cb4-241" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>sddl<span class="op">);</span></span>
<span id="cb4-242"><a href="#cb4-242" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span><span class="st">"Can Start Service!"</span><span class="op">);</span></span>
<span id="cb4-243"><a href="#cb4-243" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span><span class="st">"Can Modify Service Binary"</span><span class="op">);</span></span>
<span id="cb4-244"><a href="#cb4-244" aria-hidden="true" tabindex="-1"></a>                        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">();</span></span>
<span id="cb4-245"><a href="#cb4-245" aria-hidden="true" tabindex="-1"></a>                    <span class="op">}</span></span>
<span id="cb4-246"><a href="#cb4-246" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb4-247"><a href="#cb4-247" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb4-248"><a href="#cb4-248" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb4-249"><a href="#cb4-249" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span>
<span id="cb4-250"><a href="#cb4-250" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
</section>
</div>
