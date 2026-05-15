---
layout: default
title: "Reversing Execute Assembly and Customizing Mythic"
permalink: /solo-purple-teaming/reversing-execute-assembly-and-customizing-mythic/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Reversing Execute Assembly and Customizing Mythic</h1>
</section>
<section class="spt-content">
<h2 id="current-ioc-diagram">Current IOC Diagram</h2>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image.png"
alt="image.png" /></p>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%201.png"
alt="image.png" /></p>
<p>We have determined that when we run our malicious PowerShell command
via the run dialog, Explorer.exe spawns a PowerShell process that
reflectively loads apollo.exe. When we use execute_assembly, a
sacrificial process is spawned determined by the setting of spawns_x64
and a CLR loader is injected that reflectively loads the .NET assembly
passed as an argument. In this walkthrough, we set out to understand how
the global unique pipe name is created and why is the system process
involved. In CobaltStrike, when execute assembly is used, the
sacrificial process creates the process and the host process connects to
the pipe.</p>
<h2 id="mythic-directory-structure">Mythic Directory Structure</h2>
<p>Reference:</p>
<p>[<a
href="https://docs.mythic-c2.net/customizing/payload-type-development](https://docs.mythic-c2.net/customizing/payload-type-development)">https://docs.mythic-c2.net/customizing/payload-type-development](https://docs.mythic-c2.net/customizing/payload-type-development)</a></p>
<h3
id="all-c2-profiles-and-payload-types-are-installed-in-installservices">All
C2 Profiles and Payload Types are installed in
<mythic install directory>/InstallServices.</h3>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%202.png"
alt="image.png" /></p>
<h3
id="looking-inside-apollo-we-see-the-dockerfile-and-the-mainpy-file-along-with-another-directory-named-apollo">Looking
inside apollo, we see the Dockerfile and the <a
href="http://main.py">main.py</a> file along with another directory
named apollo</h3>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%203.png"
alt="image.png" /></p>
<p>The <code>Mythic/InstalledServices/[agent name]</code> folder is
mapped to <code>/Mythic</code> in the docker container. Editing the
files on disk results in the edits appearing in the docker container and
visa versa.<code>Mythic/InstalledServices/[agent name]/main.py</code>
file is what will get executed by Python 3.11.</p>
<h3
id="inside-the-apollo-directory-we-find-the-agent_code-folder-which-we-discussed-in-earlier-lectures-along-with-another-folder-named-mythic-the-mythic-directory-contains-all-the-code-required-for-the-mythic-framework">Inside
the <code>apollo</code> directory, we find
the <code>agent_code</code> folder, which we discussed in earlier
lectures, along with another folder named <code>mythic</code>.
The <code>mythic</code> directory contains all the code required for the
Mythic framework.</h3>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%204.png"
alt="image.png" /></p>
<h3
id="inside-the-mythic-folder-is-the-agent_functions-and-browser_scripts-directories">Inside
the mythic folder is the agent_functions and browser_scripts
directories</h3>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%205.png"
alt="image.png" /></p>
<h3
id="the-agent_functions-directory-holds-all-of-the-python-scripts-for-the-various-commands-that-apollo-implements-including-execute_assemblypy">The
agent_functions directory holds all of the python scripts for the
various commands that Apollo implements including
execute_assembly.py</h3>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%206.png"
alt="image.png" /></p>
<p>Inside execute_assembly.py, we can add additional command parameters
which are defined in the <strong>init</strong> function:</p>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%207.png"
alt="image.png" /></p>
<p>Add another CommandParameter for PipeName. The name we set for this
CommandParemter is very important since it is referenced later in the
code.</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a>CommandParameter(</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>    name<span class="op">=</span><span class="st">"pipe_name"</span>,</span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>    cli_name<span class="op">=</span><span class="st">"PipeName"</span>,</span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>    display_name<span class="op">=</span><span class="st">"PipeName"</span>,</span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a>    <span class="bu">type</span><span class="op">=</span>ParameterType.String,</span>
<span id="cb1-6"><a href="#cb1-6" aria-hidden="true" tabindex="-1"></a>    description<span class="op">=</span><span class="st">"PipeName used for IPC"</span>,</span>
<span id="cb1-7"><a href="#cb1-7" aria-hidden="true" tabindex="-1"></a>    parameter_group_info<span class="op">=</span>[</span>
<span id="cb1-8"><a href="#cb1-8" aria-hidden="true" tabindex="-1"></a>        ParameterGroupInfo(</span>
<span id="cb1-9"><a href="#cb1-9" aria-hidden="true" tabindex="-1"></a>            required<span class="op">=</span><span class="va">False</span>, group_name<span class="op">=</span><span class="st">"Default"</span>, ui_position<span class="op">=</span><span class="dv">3</span></span>
<span id="cb1-10"><a href="#cb1-10" aria-hidden="true" tabindex="-1"></a>        ),</span>
<span id="cb1-11"><a href="#cb1-11" aria-hidden="true" tabindex="-1"></a>        ParameterGroupInfo(</span>
<span id="cb1-12"><a href="#cb1-12" aria-hidden="true" tabindex="-1"></a>            required<span class="op">=</span><span class="va">False</span>, group_name<span class="op">=</span><span class="st">"New Assembly"</span>, ui_position<span class="op">=</span><span class="dv">3</span></span>
<span id="cb1-13"><a href="#cb1-13" aria-hidden="true" tabindex="-1"></a>        ),</span>
<span id="cb1-14"><a href="#cb1-14" aria-hidden="true" tabindex="-1"></a>    ],</span>
<span id="cb1-15"><a href="#cb1-15" aria-hidden="true" tabindex="-1"></a>),</span></code></pre></div>
<p>In the create_go_tasking function, copy this lline of code in the
source and then comment this line of code out.</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>taskData.args.add_arg(<span class="st">"pipe_name"</span>, <span class="bu">str</span>(uuid4()))</span></code></pre></div>
<p>The above line of code sets the pipe name used for IPC to a globally
unique identifier. We want to be able to set a custom value for pipe
name or use the global unique identifier as the default.</p>
<p>Move to the top of the create_go_tasking under this line of code</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>originalGroupNameIsDefault <span class="op">=</span> taskData.args.get_parameter_group_name() <span class="op">==</span> <span class="st">"Default"</span></span></code></pre></div>
<p>Insert the following custom code to check for the pipe_name and use
if set otherwise generate a globally unique identifie</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a>pipe_name <span class="op">=</span> taskData.args.get_arg(<span class="st">'pipe_name'</span>)</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="cf">if</span> pipe_name <span class="op">==</span> <span class="st">""</span> <span class="kw">or</span> pipe_name <span class="kw">is</span> <span class="va">None</span>:</span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a>    taskData.args.set_arg(<span class="st">"pipe_name"</span>, <span class="bu">str</span>(uuid4()))</span></code></pre></div>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%208.png"
alt="image.png" /></p>
<h2 id="coding-challenge">Coding Challenge</h2>
<p>Write the code to update display in mythic to show the pipe name
parameter if set.</p>
<p><img
src="/_assets/reversing-execute-assembly-and-customizing-mythic/image%209.png"
alt="image.png" /></p>
<h2 id="full-code-for-customized-execute_assemblypy">Full Code for
Customized execute_assembly.py</h2>
<div class="sourceCode" id="cb5"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="im">from</span> distutils.dir_util <span class="im">import</span> copy_tree</span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a><span class="im">import</span> shutil</span>
<span id="cb5-3"><a href="#cb5-3" aria-hidden="true" tabindex="-1"></a><span class="im">import</span> tempfile</span>
<span id="cb5-4"><a href="#cb5-4" aria-hidden="true" tabindex="-1"></a><span class="im">from</span> mythic_container.MythicCommandBase <span class="im">import</span> <span class="op">*</span></span>
<span id="cb5-5"><a href="#cb5-5" aria-hidden="true" tabindex="-1"></a><span class="im">from</span> uuid <span class="im">import</span> uuid4</span>
<span id="cb5-6"><a href="#cb5-6" aria-hidden="true" tabindex="-1"></a><span class="im">from</span> mythic_container.MythicRPC <span class="im">import</span> <span class="op">*</span></span>
<span id="cb5-7"><a href="#cb5-7" aria-hidden="true" tabindex="-1"></a><span class="im">from</span> os <span class="im">import</span> path</span>
<span id="cb5-8"><a href="#cb5-8" aria-hidden="true" tabindex="-1"></a><span class="im">import</span> asyncio</span>
<span id="cb5-9"><a href="#cb5-9" aria-hidden="true" tabindex="-1"></a><span class="im">import</span> donut</span>
<span id="cb5-10"><a href="#cb5-10" aria-hidden="true" tabindex="-1"></a><span class="im">import</span> platform</span>
<span id="cb5-11"><a href="#cb5-11" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-12"><a href="#cb5-12" aria-hidden="true" tabindex="-1"></a><span class="cf">if</span> platform.system() <span class="op">==</span> <span class="st">"Windows"</span>:</span>
<span id="cb5-13"><a href="#cb5-13" aria-hidden="true" tabindex="-1"></a>    EXEECUTE_ASSEMBLY_PATH <span class="op">=</span> <span class="st">"C:</span><span class="ch">\\</span><span class="st">Mythic</span><span class="ch">\\</span><span class="st">Apollo</span><span class="ch">\\</span><span class="st">srv</span><span class="ch">\\</span><span class="st">ExecuteAssembly.exe"</span></span>
<span id="cb5-14"><a href="#cb5-14" aria-hidden="true" tabindex="-1"></a><span class="cf">else</span>:</span>
<span id="cb5-15"><a href="#cb5-15" aria-hidden="true" tabindex="-1"></a>    EXEECUTE_ASSEMBLY_PATH <span class="op">=</span> <span class="st">"/srv/ExecuteAssembly.exe"</span></span>
<span id="cb5-16"><a href="#cb5-16" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-17"><a href="#cb5-17" aria-hidden="true" tabindex="-1"></a><span class="kw">class</span> ExecuteAssemblyArguments(TaskArguments):</span>
<span id="cb5-18"><a href="#cb5-18" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-19"><a href="#cb5-19" aria-hidden="true" tabindex="-1"></a>    <span class="kw">def</span> <span class="fu">__init__</span>(<span class="va">self</span>, command_line, <span class="op">**</span>kwargs):</span>
<span id="cb5-20"><a href="#cb5-20" aria-hidden="true" tabindex="-1"></a>        <span class="bu">super</span>().<span class="fu">__init__</span>(command_line, <span class="op">**</span>kwargs)</span>
<span id="cb5-21"><a href="#cb5-21" aria-hidden="true" tabindex="-1"></a>        <span class="va">self</span>.args <span class="op">=</span> [</span>
<span id="cb5-22"><a href="#cb5-22" aria-hidden="true" tabindex="-1"></a>            CommandParameter(</span>
<span id="cb5-23"><a href="#cb5-23" aria-hidden="true" tabindex="-1"></a>                name<span class="op">=</span><span class="st">"assembly_name"</span>,</span>
<span id="cb5-24"><a href="#cb5-24" aria-hidden="true" tabindex="-1"></a>                cli_name<span class="op">=</span><span class="st">"Assembly"</span>,</span>
<span id="cb5-25"><a href="#cb5-25" aria-hidden="true" tabindex="-1"></a>                display_name<span class="op">=</span><span class="st">"Assembly"</span>,</span>
<span id="cb5-26"><a href="#cb5-26" aria-hidden="true" tabindex="-1"></a>                <span class="bu">type</span><span class="op">=</span>ParameterType.ChooseOne,</span>
<span id="cb5-27"><a href="#cb5-27" aria-hidden="true" tabindex="-1"></a>                dynamic_query_function<span class="op">=</span><span class="va">self</span>.get_files,</span>
<span id="cb5-28"><a href="#cb5-28" aria-hidden="true" tabindex="-1"></a>                description<span class="op">=</span><span class="st">"Assembly to execute (e.g., Seatbelt.exe)."</span>,</span>
<span id="cb5-29"><a href="#cb5-29" aria-hidden="true" tabindex="-1"></a>                parameter_group_info<span class="op">=</span>[</span>
<span id="cb5-30"><a href="#cb5-30" aria-hidden="true" tabindex="-1"></a>                    ParameterGroupInfo(</span>
<span id="cb5-31"><a href="#cb5-31" aria-hidden="true" tabindex="-1"></a>                        required<span class="op">=</span><span class="va">True</span>, group_name<span class="op">=</span><span class="st">"Default"</span>, ui_position<span class="op">=</span><span class="dv">1</span></span>
<span id="cb5-32"><a href="#cb5-32" aria-hidden="true" tabindex="-1"></a>                    )</span>
<span id="cb5-33"><a href="#cb5-33" aria-hidden="true" tabindex="-1"></a>                ],</span>
<span id="cb5-34"><a href="#cb5-34" aria-hidden="true" tabindex="-1"></a>            ),</span>
<span id="cb5-35"><a href="#cb5-35" aria-hidden="true" tabindex="-1"></a>            CommandParameter(</span>
<span id="cb5-36"><a href="#cb5-36" aria-hidden="true" tabindex="-1"></a>                name<span class="op">=</span><span class="st">"assembly_file"</span>,</span>
<span id="cb5-37"><a href="#cb5-37" aria-hidden="true" tabindex="-1"></a>                display_name<span class="op">=</span><span class="st">"New Assembly"</span>,</span>
<span id="cb5-38"><a href="#cb5-38" aria-hidden="true" tabindex="-1"></a>                <span class="bu">type</span><span class="op">=</span>ParameterType.File,</span>
<span id="cb5-39"><a href="#cb5-39" aria-hidden="true" tabindex="-1"></a>                description<span class="op">=</span><span class="st">"A new assembly to execute. After uploading once, you can just supply the assembly_name parameter"</span>,</span>
<span id="cb5-40"><a href="#cb5-40" aria-hidden="true" tabindex="-1"></a>                parameter_group_info<span class="op">=</span>[</span>
<span id="cb5-41"><a href="#cb5-41" aria-hidden="true" tabindex="-1"></a>                    ParameterGroupInfo(</span>
<span id="cb5-42"><a href="#cb5-42" aria-hidden="true" tabindex="-1"></a>                        required<span class="op">=</span><span class="va">True</span>, group_name<span class="op">=</span><span class="st">"New Assembly"</span>, ui_position<span class="op">=</span><span class="dv">1</span>,</span>
<span id="cb5-43"><a href="#cb5-43" aria-hidden="true" tabindex="-1"></a>                    )</span>
<span id="cb5-44"><a href="#cb5-44" aria-hidden="true" tabindex="-1"></a>                ]</span>
<span id="cb5-45"><a href="#cb5-45" aria-hidden="true" tabindex="-1"></a>            ),</span>
<span id="cb5-46"><a href="#cb5-46" aria-hidden="true" tabindex="-1"></a>            CommandParameter(</span>
<span id="cb5-47"><a href="#cb5-47" aria-hidden="true" tabindex="-1"></a>                name<span class="op">=</span><span class="st">"assembly_arguments"</span>,</span>
<span id="cb5-48"><a href="#cb5-48" aria-hidden="true" tabindex="-1"></a>                cli_name<span class="op">=</span><span class="st">"Arguments"</span>,</span>
<span id="cb5-49"><a href="#cb5-49" aria-hidden="true" tabindex="-1"></a>                display_name<span class="op">=</span><span class="st">"Arguments"</span>,</span>
<span id="cb5-50"><a href="#cb5-50" aria-hidden="true" tabindex="-1"></a>                <span class="bu">type</span><span class="op">=</span>ParameterType.String,</span>
<span id="cb5-51"><a href="#cb5-51" aria-hidden="true" tabindex="-1"></a>                description<span class="op">=</span><span class="st">"Arguments to pass to the assembly."</span>,</span>
<span id="cb5-52"><a href="#cb5-52" aria-hidden="true" tabindex="-1"></a>                parameter_group_info<span class="op">=</span>[</span>
<span id="cb5-53"><a href="#cb5-53" aria-hidden="true" tabindex="-1"></a>                    ParameterGroupInfo(</span>
<span id="cb5-54"><a href="#cb5-54" aria-hidden="true" tabindex="-1"></a>                        required<span class="op">=</span><span class="va">False</span>, group_name<span class="op">=</span><span class="st">"Default"</span>, ui_position<span class="op">=</span><span class="dv">2</span></span>
<span id="cb5-55"><a href="#cb5-55" aria-hidden="true" tabindex="-1"></a>                    ),</span>
<span id="cb5-56"><a href="#cb5-56" aria-hidden="true" tabindex="-1"></a>                    ParameterGroupInfo(</span>
<span id="cb5-57"><a href="#cb5-57" aria-hidden="true" tabindex="-1"></a>                        required<span class="op">=</span><span class="va">False</span>, group_name<span class="op">=</span><span class="st">"New Assembly"</span>, ui_position<span class="op">=</span><span class="dv">2</span></span>
<span id="cb5-58"><a href="#cb5-58" aria-hidden="true" tabindex="-1"></a>                    ),</span>
<span id="cb5-59"><a href="#cb5-59" aria-hidden="true" tabindex="-1"></a>                ],</span>
<span id="cb5-60"><a href="#cb5-60" aria-hidden="true" tabindex="-1"></a>            ),</span>
<span id="cb5-61"><a href="#cb5-61" aria-hidden="true" tabindex="-1"></a>            CommandParameter(</span>
<span id="cb5-62"><a href="#cb5-62" aria-hidden="true" tabindex="-1"></a>                name<span class="op">=</span><span class="st">"pipe_name"</span>,</span>
<span id="cb5-63"><a href="#cb5-63" aria-hidden="true" tabindex="-1"></a>                cli_name<span class="op">=</span><span class="st">"PipeName"</span>,</span>
<span id="cb5-64"><a href="#cb5-64" aria-hidden="true" tabindex="-1"></a>                display_name<span class="op">=</span><span class="st">"PipeName"</span>,</span>
<span id="cb5-65"><a href="#cb5-65" aria-hidden="true" tabindex="-1"></a>                <span class="bu">type</span><span class="op">=</span>ParameterType.String,</span>
<span id="cb5-66"><a href="#cb5-66" aria-hidden="true" tabindex="-1"></a>                description<span class="op">=</span><span class="st">"PipeName use for IPC."</span>,</span>
<span id="cb5-67"><a href="#cb5-67" aria-hidden="true" tabindex="-1"></a>                parameter_group_info<span class="op">=</span>[</span>
<span id="cb5-68"><a href="#cb5-68" aria-hidden="true" tabindex="-1"></a>                    ParameterGroupInfo(</span>
<span id="cb5-69"><a href="#cb5-69" aria-hidden="true" tabindex="-1"></a>                        required<span class="op">=</span><span class="va">False</span>, group_name<span class="op">=</span><span class="st">"Default"</span>, ui_position<span class="op">=</span><span class="dv">3</span></span>
<span id="cb5-70"><a href="#cb5-70" aria-hidden="true" tabindex="-1"></a>                    ),</span>
<span id="cb5-71"><a href="#cb5-71" aria-hidden="true" tabindex="-1"></a>                    ParameterGroupInfo(</span>
<span id="cb5-72"><a href="#cb5-72" aria-hidden="true" tabindex="-1"></a>                        required<span class="op">=</span><span class="va">False</span>, group_name<span class="op">=</span><span class="st">"New Assembly"</span>, ui_position<span class="op">=</span><span class="dv">3</span></span>
<span id="cb5-73"><a href="#cb5-73" aria-hidden="true" tabindex="-1"></a>                    ),</span>
<span id="cb5-74"><a href="#cb5-74" aria-hidden="true" tabindex="-1"></a>                ],</span>
<span id="cb5-75"><a href="#cb5-75" aria-hidden="true" tabindex="-1"></a>            ),</span>
<span id="cb5-76"><a href="#cb5-76" aria-hidden="true" tabindex="-1"></a>        ]</span>
<span id="cb5-77"><a href="#cb5-77" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-78"><a href="#cb5-78" aria-hidden="true" tabindex="-1"></a>    <span class="cf">async</span> <span class="kw">def</span> get_files(</span>
<span id="cb5-79"><a href="#cb5-79" aria-hidden="true" tabindex="-1"></a>        <span class="va">self</span>, inputMsg: PTRPCDynamicQueryFunctionMessage</span>
<span id="cb5-80"><a href="#cb5-80" aria-hidden="true" tabindex="-1"></a>    ) <span class="op">-></span> PTRPCDynamicQueryFunctionMessageResponse:</span>
<span id="cb5-81"><a href="#cb5-81" aria-hidden="true" tabindex="-1"></a>        fileResponse <span class="op">=</span> PTRPCDynamicQueryFunctionMessageResponse(Success<span class="op">=</span><span class="va">False</span>)</span>
<span id="cb5-82"><a href="#cb5-82" aria-hidden="true" tabindex="-1"></a>        file_resp <span class="op">=</span> <span class="cf">await</span> SendMythicRPCFileSearch(</span>
<span id="cb5-83"><a href="#cb5-83" aria-hidden="true" tabindex="-1"></a>            MythicRPCFileSearchMessage(</span>
<span id="cb5-84"><a href="#cb5-84" aria-hidden="true" tabindex="-1"></a>                CallbackID<span class="op">=</span>inputMsg.Callback,</span>
<span id="cb5-85"><a href="#cb5-85" aria-hidden="true" tabindex="-1"></a>                LimitByCallback<span class="op">=</span><span class="va">True</span>,</span>
<span id="cb5-86"><a href="#cb5-86" aria-hidden="true" tabindex="-1"></a>                Filename<span class="op">=</span><span class="st">""</span>,</span>
<span id="cb5-87"><a href="#cb5-87" aria-hidden="true" tabindex="-1"></a>            )</span>
<span id="cb5-88"><a href="#cb5-88" aria-hidden="true" tabindex="-1"></a>        )</span>
<span id="cb5-89"><a href="#cb5-89" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> file_resp.Success:</span>
<span id="cb5-90"><a href="#cb5-90" aria-hidden="true" tabindex="-1"></a>            file_names <span class="op">=</span> []</span>
<span id="cb5-91"><a href="#cb5-91" aria-hidden="true" tabindex="-1"></a>            <span class="cf">for</span> f <span class="kw">in</span> file_resp.Files:</span>
<span id="cb5-92"><a href="#cb5-92" aria-hidden="true" tabindex="-1"></a>                <span class="cf">if</span> f.Filename <span class="kw">not</span> <span class="kw">in</span> file_names <span class="kw">and</span> f.Filename.endswith(<span class="st">".exe"</span>):</span>
<span id="cb5-93"><a href="#cb5-93" aria-hidden="true" tabindex="-1"></a>                    file_names.append(f.Filename)</span>
<span id="cb5-94"><a href="#cb5-94" aria-hidden="true" tabindex="-1"></a>            fileResponse.Success <span class="op">=</span> <span class="va">True</span></span>
<span id="cb5-95"><a href="#cb5-95" aria-hidden="true" tabindex="-1"></a>            fileResponse.Choices <span class="op">=</span> file_names</span>
<span id="cb5-96"><a href="#cb5-96" aria-hidden="true" tabindex="-1"></a>            <span class="cf">return</span> fileResponse</span>
<span id="cb5-97"><a href="#cb5-97" aria-hidden="true" tabindex="-1"></a>        <span class="cf">else</span>:</span>
<span id="cb5-98"><a href="#cb5-98" aria-hidden="true" tabindex="-1"></a>            fileResponse.Error <span class="op">=</span> file_resp.Error</span>
<span id="cb5-99"><a href="#cb5-99" aria-hidden="true" tabindex="-1"></a>            <span class="cf">return</span> fileResponse</span>
<span id="cb5-100"><a href="#cb5-100" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-101"><a href="#cb5-101" aria-hidden="true" tabindex="-1"></a>    <span class="cf">async</span> <span class="kw">def</span> parse_arguments(<span class="va">self</span>):</span>
<span id="cb5-102"><a href="#cb5-102" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> <span class="bu">len</span>(<span class="va">self</span>.command_line) <span class="op">==</span> <span class="dv">0</span>:</span>
<span id="cb5-103"><a href="#cb5-103" aria-hidden="true" tabindex="-1"></a>            <span class="cf">raise</span> <span class="pp">Exception</span>(</span>
<span id="cb5-104"><a href="#cb5-104" aria-hidden="true" tabindex="-1"></a>                <span class="st">"Require an assembly to execute.</span><span class="ch">\n\t</span><span class="st">Usage: </span><span class="sc">{}</span><span class="st">"</span>.<span class="bu">format</span>(</span>
<span id="cb5-105"><a href="#cb5-105" aria-hidden="true" tabindex="-1"></a>                    ExecuteAssemblyCommand.help_cmd</span>
<span id="cb5-106"><a href="#cb5-106" aria-hidden="true" tabindex="-1"></a>                )</span>
<span id="cb5-107"><a href="#cb5-107" aria-hidden="true" tabindex="-1"></a>            )</span>
<span id="cb5-108"><a href="#cb5-108" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> <span class="va">self</span>.command_line[<span class="dv">0</span>] <span class="op">==</span> <span class="st">"{"</span>:</span>
<span id="cb5-109"><a href="#cb5-109" aria-hidden="true" tabindex="-1"></a>            <span class="va">self</span>.load_args_from_json_string(<span class="va">self</span>.command_line)</span>
<span id="cb5-110"><a href="#cb5-110" aria-hidden="true" tabindex="-1"></a>        <span class="cf">else</span>:</span>
<span id="cb5-111"><a href="#cb5-111" aria-hidden="true" tabindex="-1"></a>            parts <span class="op">=</span> <span class="va">self</span>.command_line.split(<span class="st">" "</span>, maxsplit<span class="op">=</span><span class="dv">1</span>)</span>
<span id="cb5-112"><a href="#cb5-112" aria-hidden="true" tabindex="-1"></a>            <span class="va">self</span>.add_arg(<span class="st">"assembly_name"</span>, parts[<span class="dv">0</span>])</span>
<span id="cb5-113"><a href="#cb5-113" aria-hidden="true" tabindex="-1"></a>            <span class="va">self</span>.add_arg(<span class="st">"assembly_arguments"</span>, <span class="st">""</span>)</span>
<span id="cb5-114"><a href="#cb5-114" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> <span class="bu">len</span>(parts) <span class="op">==</span> <span class="dv">2</span>:</span>
<span id="cb5-115"><a href="#cb5-115" aria-hidden="true" tabindex="-1"></a>                <span class="va">self</span>.add_arg(<span class="st">"assembly_arguments"</span>, parts[<span class="dv">1</span>])</span>
<span id="cb5-116"><a href="#cb5-116" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-117"><a href="#cb5-117" aria-hidden="true" tabindex="-1"></a><span class="kw">class</span> ExecuteAssemblyCommand(CommandBase):</span>
<span id="cb5-118"><a href="#cb5-118" aria-hidden="true" tabindex="-1"></a>    cmd <span class="op">=</span> <span class="st">"execute_assembly"</span></span>
<span id="cb5-119"><a href="#cb5-119" aria-hidden="true" tabindex="-1"></a>    needs_admin <span class="op">=</span> <span class="va">False</span></span>
<span id="cb5-120"><a href="#cb5-120" aria-hidden="true" tabindex="-1"></a>    help_cmd <span class="op">=</span> <span class="st">"execute_assembly [Assembly.exe] [args]"</span></span>
<span id="cb5-121"><a href="#cb5-121" aria-hidden="true" tabindex="-1"></a>    description <span class="op">=</span> <span class="st">"Executes a .NET assembly with the specified arguments. This assembly must first be known by the agent using the `register_assembly` command or by supplying an assembly with the task."</span></span>
<span id="cb5-122"><a href="#cb5-122" aria-hidden="true" tabindex="-1"></a>    version <span class="op">=</span> <span class="dv">3</span></span>
<span id="cb5-123"><a href="#cb5-123" aria-hidden="true" tabindex="-1"></a>    author <span class="op">=</span> <span class="st">"@djhohnstein"</span></span>
<span id="cb5-124"><a href="#cb5-124" aria-hidden="true" tabindex="-1"></a>    argument_class <span class="op">=</span> ExecuteAssemblyArguments</span>
<span id="cb5-125"><a href="#cb5-125" aria-hidden="true" tabindex="-1"></a>    attackmapping <span class="op">=</span> [<span class="st">"T1547"</span>]</span>
<span id="cb5-126"><a href="#cb5-126" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-127"><a href="#cb5-127" aria-hidden="true" tabindex="-1"></a>    <span class="cf">async</span> <span class="kw">def</span> build_exeasm(<span class="va">self</span>):</span>
<span id="cb5-128"><a href="#cb5-128" aria-hidden="true" tabindex="-1"></a>        <span class="cf">try</span>:</span>
<span id="cb5-129"><a href="#cb5-129" aria-hidden="true" tabindex="-1"></a>            <span class="kw">global</span> EXEECUTE_ASSEMBLY_PATH</span>
<span id="cb5-130"><a href="#cb5-130" aria-hidden="true" tabindex="-1"></a>            agent_build_path <span class="op">=</span> tempfile.TemporaryDirectory()</span>
<span id="cb5-131"><a href="#cb5-131" aria-hidden="true" tabindex="-1"></a>            outputPath <span class="op">=</span> <span class="st">"</span><span class="sc">{}</span><span class="st">/ExecuteAssembly/bin/Release/ExecuteAssembly.exe"</span>.<span class="bu">format</span>(</span>
<span id="cb5-132"><a href="#cb5-132" aria-hidden="true" tabindex="-1"></a>                agent_build_path.name</span>
<span id="cb5-133"><a href="#cb5-133" aria-hidden="true" tabindex="-1"></a>            )</span>
<span id="cb5-134"><a href="#cb5-134" aria-hidden="true" tabindex="-1"></a>            <span class="co"># shutil to copy payload files over</span></span>
<span id="cb5-135"><a href="#cb5-135" aria-hidden="true" tabindex="-1"></a>            copy_tree(<span class="bu">str</span>(<span class="va">self</span>.agent_code_path), agent_build_path.name)</span>
<span id="cb5-136"><a href="#cb5-136" aria-hidden="true" tabindex="-1"></a>            shell_cmd <span class="op">=</span> <span class="st">"dotnet build -c release -p:DebugType=None -p:DebugSymbols=false -p:Platform=x64 </span><span class="sc">{}</span><span class="st">/ExecuteAssembly/ExecuteAssembly.csproj -o </span><span class="sc">{}</span><span class="st">/ExecuteAssembly/bin/Release/"</span>.<span class="bu">format</span>(</span>
<span id="cb5-137"><a href="#cb5-137" aria-hidden="true" tabindex="-1"></a>                agent_build_path.name, agent_build_path.name</span>
<span id="cb5-138"><a href="#cb5-138" aria-hidden="true" tabindex="-1"></a>            )</span>
<span id="cb5-139"><a href="#cb5-139" aria-hidden="true" tabindex="-1"></a>            proc <span class="op">=</span> <span class="cf">await</span> asyncio.create_subprocess_shell(</span>
<span id="cb5-140"><a href="#cb5-140" aria-hidden="true" tabindex="-1"></a>                shell_cmd,</span>
<span id="cb5-141"><a href="#cb5-141" aria-hidden="true" tabindex="-1"></a>                stdout<span class="op">=</span>asyncio.subprocess.PIPE,</span>
<span id="cb5-142"><a href="#cb5-142" aria-hidden="true" tabindex="-1"></a>                stderr<span class="op">=</span>asyncio.subprocess.PIPE,</span>
<span id="cb5-143"><a href="#cb5-143" aria-hidden="true" tabindex="-1"></a>                cwd<span class="op">=</span>agent_build_path.name,</span>
<span id="cb5-144"><a href="#cb5-144" aria-hidden="true" tabindex="-1"></a>            )</span>
<span id="cb5-145"><a href="#cb5-145" aria-hidden="true" tabindex="-1"></a>            stdout, stderr <span class="op">=</span> <span class="cf">await</span> proc.communicate()</span>
<span id="cb5-146"><a href="#cb5-146" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> <span class="kw">not</span> path.exists(outputPath):</span>
<span id="cb5-147"><a href="#cb5-147" aria-hidden="true" tabindex="-1"></a>                <span class="cf">raise</span> <span class="pp">Exception</span>(</span>
<span id="cb5-148"><a href="#cb5-148" aria-hidden="true" tabindex="-1"></a>                    <span class="st">"Failed to build ExecuteAssembly.exe:</span><span class="ch">\n</span><span class="sc">{}</span><span class="st">"</span>.<span class="bu">format</span>(</span>
<span id="cb5-149"><a href="#cb5-149" aria-hidden="true" tabindex="-1"></a>                        stderr.decode() <span class="op">+</span> <span class="st">"</span><span class="ch">\n</span><span class="st">"</span> <span class="op">+</span> stdout.decode()</span>
<span id="cb5-150"><a href="#cb5-150" aria-hidden="true" tabindex="-1"></a>                    )</span>
<span id="cb5-151"><a href="#cb5-151" aria-hidden="true" tabindex="-1"></a>                )</span>
<span id="cb5-152"><a href="#cb5-152" aria-hidden="true" tabindex="-1"></a>            shutil.copy(outputPath, EXEECUTE_ASSEMBLY_PATH)</span>
<span id="cb5-153"><a href="#cb5-153" aria-hidden="true" tabindex="-1"></a>        <span class="cf">except</span> <span class="pp">Exception</span> <span class="im">as</span> ex:</span>
<span id="cb5-154"><a href="#cb5-154" aria-hidden="true" tabindex="-1"></a>            <span class="cf">raise</span> <span class="pp">Exception</span>(ex)</span>
<span id="cb5-155"><a href="#cb5-155" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-156"><a href="#cb5-156" aria-hidden="true" tabindex="-1"></a>    <span class="cf">async</span> <span class="kw">def</span> create_go_tasking(</span>
<span id="cb5-157"><a href="#cb5-157" aria-hidden="true" tabindex="-1"></a>        <span class="va">self</span>, taskData: PTTaskMessageAllData</span>
<span id="cb5-158"><a href="#cb5-158" aria-hidden="true" tabindex="-1"></a>    ) <span class="op">-></span> PTTaskCreateTaskingMessageResponse:</span>
<span id="cb5-159"><a href="#cb5-159" aria-hidden="true" tabindex="-1"></a>        response <span class="op">=</span> PTTaskCreateTaskingMessageResponse(</span>
<span id="cb5-160"><a href="#cb5-160" aria-hidden="true" tabindex="-1"></a>            TaskID<span class="op">=</span>taskData.Task.ID,</span>
<span id="cb5-161"><a href="#cb5-161" aria-hidden="true" tabindex="-1"></a>            Success<span class="op">=</span><span class="va">True</span>,</span>
<span id="cb5-162"><a href="#cb5-162" aria-hidden="true" tabindex="-1"></a>        )</span>
<span id="cb5-163"><a href="#cb5-163" aria-hidden="true" tabindex="-1"></a>        <span class="kw">global</span> EXEECUTE_ASSEMBLY_PATH</span>
<span id="cb5-164"><a href="#cb5-164" aria-hidden="true" tabindex="-1"></a>        originalGroupNameIsDefault <span class="op">=</span> taskData.args.get_parameter_group_name() <span class="op">==</span> <span class="st">"Default"</span></span>
<span id="cb5-165"><a href="#cb5-165" aria-hidden="true" tabindex="-1"></a>        </span>
<span id="cb5-166"><a href="#cb5-166" aria-hidden="true" tabindex="-1"></a>        pipe_name <span class="op">=</span> taskData.args.get_arg(<span class="st">'pipe_name'</span>)</span>
<span id="cb5-167"><a href="#cb5-167" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> pipe_name <span class="op">==</span> <span class="st">""</span> <span class="kw">or</span> pipe_name <span class="kw">is</span> <span class="va">None</span>:</span>
<span id="cb5-168"><a href="#cb5-168" aria-hidden="true" tabindex="-1"></a>            taskData.args.set_arg(<span class="st">"pipe_name"</span>, <span class="bu">str</span>(uuid4()))</span>
<span id="cb5-169"><a href="#cb5-169" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-170"><a href="#cb5-170" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> taskData.args.get_parameter_group_name() <span class="op">==</span> <span class="st">"New Assembly"</span>:</span>
<span id="cb5-171"><a href="#cb5-171" aria-hidden="true" tabindex="-1"></a>            fileSearchResp <span class="op">=</span> <span class="cf">await</span> SendMythicRPCFileSearch(MythicRPCFileSearchMessage(</span>
<span id="cb5-172"><a href="#cb5-172" aria-hidden="true" tabindex="-1"></a>                TaskID<span class="op">=</span>taskData.Task.ID,</span>
<span id="cb5-173"><a href="#cb5-173" aria-hidden="true" tabindex="-1"></a>                AgentFileID<span class="op">=</span>taskData.args.get_arg(<span class="st">"assembly_file"</span>)</span>
<span id="cb5-174"><a href="#cb5-174" aria-hidden="true" tabindex="-1"></a>            ))</span>
<span id="cb5-175"><a href="#cb5-175" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> <span class="kw">not</span> fileSearchResp.Success:</span>
<span id="cb5-176"><a href="#cb5-176" aria-hidden="true" tabindex="-1"></a>                <span class="cf">raise</span> <span class="pp">Exception</span>(<span class="ss">f"Failed to find uploaded file: </span><span class="sc">{</span>fileSearchResp<span class="sc">.</span>Error<span class="sc">}</span><span class="ss">"</span>)</span>
<span id="cb5-177"><a href="#cb5-177" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> <span class="bu">len</span>(fileSearchResp.Files) <span class="op">==</span> <span class="dv">0</span>:</span>
<span id="cb5-178"><a href="#cb5-178" aria-hidden="true" tabindex="-1"></a>                <span class="cf">raise</span> <span class="pp">Exception</span>(<span class="ss">f"Failed to find matching file, was it deleted?"</span>)</span>
<span id="cb5-179"><a href="#cb5-179" aria-hidden="true" tabindex="-1"></a>            searchedTaskResp <span class="op">=</span> <span class="cf">await</span> SendMythicRPCTaskSearch(MythicRPCTaskSearchMessage(</span>
<span id="cb5-180"><a href="#cb5-180" aria-hidden="true" tabindex="-1"></a>                TaskID<span class="op">=</span>taskData.Task.ID,</span>
<span id="cb5-181"><a href="#cb5-181" aria-hidden="true" tabindex="-1"></a>                SearchCallbackID<span class="op">=</span>taskData.Callback.ID,</span>
<span id="cb5-182"><a href="#cb5-182" aria-hidden="true" tabindex="-1"></a>                SearchCommandNames<span class="op">=</span>[<span class="st">"register_file"</span>],</span>
<span id="cb5-183"><a href="#cb5-183" aria-hidden="true" tabindex="-1"></a>                SearchParams<span class="op">=</span>taskData.args.get_arg(<span class="st">"assembly_file"</span>)</span>
<span id="cb5-184"><a href="#cb5-184" aria-hidden="true" tabindex="-1"></a>            ))</span>
<span id="cb5-185"><a href="#cb5-185" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> <span class="kw">not</span> searchedTaskResp.Success:</span>
<span id="cb5-186"><a href="#cb5-186" aria-hidden="true" tabindex="-1"></a>                <span class="cf">raise</span> <span class="pp">Exception</span>(<span class="ss">f"Failed to search for matching tasks: </span><span class="sc">{</span>searchedTaskResp<span class="sc">.</span>Error<span class="sc">}</span><span class="ss">"</span>)</span>
<span id="cb5-187"><a href="#cb5-187" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> <span class="bu">len</span>(searchedTaskResp.Tasks) <span class="op">==</span> <span class="dv">0</span>:</span>
<span id="cb5-188"><a href="#cb5-188" aria-hidden="true" tabindex="-1"></a>                <span class="co"># we need to register this file with apollo first</span></span>
<span id="cb5-189"><a href="#cb5-189" aria-hidden="true" tabindex="-1"></a>                subtaskCreationResp <span class="op">=</span> <span class="cf">await</span> SendMythicRPCTaskCreateSubtask(MythicRPCTaskCreateSubtaskMessage(</span>
<span id="cb5-190"><a href="#cb5-190" aria-hidden="true" tabindex="-1"></a>                    TaskID<span class="op">=</span>taskData.Task.ID,</span>
<span id="cb5-191"><a href="#cb5-191" aria-hidden="true" tabindex="-1"></a>                    CommandName<span class="op">=</span><span class="st">"register_file"</span>,</span>
<span id="cb5-192"><a href="#cb5-192" aria-hidden="true" tabindex="-1"></a>                    Params<span class="op">=</span>json.dumps({<span class="st">"file"</span>: taskData.args.get_arg(<span class="st">"assembly_file"</span>)})</span>
<span id="cb5-193"><a href="#cb5-193" aria-hidden="true" tabindex="-1"></a>                ))</span>
<span id="cb5-194"><a href="#cb5-194" aria-hidden="true" tabindex="-1"></a>                <span class="cf">if</span> <span class="kw">not</span> subtaskCreationResp.Success:</span>
<span id="cb5-195"><a href="#cb5-195" aria-hidden="true" tabindex="-1"></a>                    <span class="cf">raise</span> <span class="pp">Exception</span>(<span class="ss">f"Failed to create register_file subtask: </span><span class="sc">{</span>subtaskCreationResp<span class="sc">.</span>Error<span class="sc">}</span><span class="ss">"</span>)</span>
<span id="cb5-196"><a href="#cb5-196" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-197"><a href="#cb5-197" aria-hidden="true" tabindex="-1"></a>            taskData.args.add_arg(<span class="st">"assembly_name"</span>, fileSearchResp.Files[<span class="dv">0</span>].Filename)</span>
<span id="cb5-198"><a href="#cb5-198" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> fileSearchResp.Files[<span class="dv">0</span>].AgentFileId <span class="kw">in</span> taskData.Task.OriginalParams:</span>
<span id="cb5-199"><a href="#cb5-199" aria-hidden="true" tabindex="-1"></a>                response.DisplayParams <span class="op">=</span> <span class="ss">f"-Assembly </span><span class="sc">{</span>fileSearchResp<span class="sc">.</span>Files[<span class="dv">0</span>]<span class="sc">.</span>Filename<span class="sc">}</span><span class="ss"> -Arguments </span><span class="sc">{</span>taskData<span class="sc">.</span>args<span class="sc">.</span>get_arg(<span class="st">'assembly_arguments'</span>)<span class="sc">}</span><span class="ss"> -PipeName </span><span class="sc">{</span>taskData<span class="sc">.</span>args<span class="sc">.</span>get_arg(<span class="st">'pipe_name'</span>)<span class="sc">}</span><span class="ss">"</span></span>
<span id="cb5-200"><a href="#cb5-200" aria-hidden="true" tabindex="-1"></a>            taskData.args.remove_arg(<span class="st">"assembly_file"</span>)</span>
<span id="cb5-201"><a href="#cb5-201" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-202"><a href="#cb5-202" aria-hidden="true" tabindex="-1"></a>        taskargs <span class="op">=</span> taskData.args.get_arg(<span class="st">"assembly_arguments"</span>)</span>
<span id="cb5-203"><a href="#cb5-203" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> originalGroupNameIsDefault:</span>
<span id="cb5-204"><a href="#cb5-204" aria-hidden="true" tabindex="-1"></a>            <span class="cf">if</span> taskargs <span class="op">==</span> <span class="st">""</span> <span class="kw">or</span> taskargs <span class="kw">is</span> <span class="va">None</span>:</span>
<span id="cb5-205"><a href="#cb5-205" aria-hidden="true" tabindex="-1"></a>                response.DisplayParams <span class="op">=</span> <span class="st">"-Assembly </span><span class="sc">{}</span><span class="st"> -PipeName </span><span class="sc">{}</span><span class="st">"</span>.<span class="bu">format</span>(</span>
<span id="cb5-206"><a href="#cb5-206" aria-hidden="true" tabindex="-1"></a>                    taskData.args.get_arg(<span class="st">"assembly_name"</span>), taskData.args.get_arg(<span class="st">'pipe_name'</span>)</span>
<span id="cb5-207"><a href="#cb5-207" aria-hidden="true" tabindex="-1"></a>                )</span>
<span id="cb5-208"><a href="#cb5-208" aria-hidden="true" tabindex="-1"></a>            <span class="cf">else</span>:</span>
<span id="cb5-209"><a href="#cb5-209" aria-hidden="true" tabindex="-1"></a>                response.DisplayParams <span class="op">=</span> <span class="st">"-Assembly </span><span class="sc">{}</span><span class="st"> -Arguments </span><span class="sc">{}</span><span class="st">"</span>.<span class="bu">format</span>(</span>
<span id="cb5-210"><a href="#cb5-210" aria-hidden="true" tabindex="-1"></a>                    taskData.args.get_arg(<span class="st">"assembly_name"</span>), taskargs</span>
<span id="cb5-211"><a href="#cb5-211" aria-hidden="true" tabindex="-1"></a>                )</span>
<span id="cb5-212"><a href="#cb5-212" aria-hidden="true" tabindex="-1"></a>        <span class="co">#taskData.args.add_arg("pipe_name", str(uuid4()))</span></span>
<span id="cb5-213"><a href="#cb5-213" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> <span class="kw">not</span> path.exists(EXEECUTE_ASSEMBLY_PATH):</span>
<span id="cb5-214"><a href="#cb5-214" aria-hidden="true" tabindex="-1"></a>            <span class="co"># create</span></span>
<span id="cb5-215"><a href="#cb5-215" aria-hidden="true" tabindex="-1"></a>            <span class="cf">await</span> SendMythicRPCTaskUpdate(MythicRPCTaskUpdateMessage(</span>
<span id="cb5-216"><a href="#cb5-216" aria-hidden="true" tabindex="-1"></a>                TaskID<span class="op">=</span>taskData.Task.ID,</span>
<span id="cb5-217"><a href="#cb5-217" aria-hidden="true" tabindex="-1"></a>                UpdateStatus<span class="op">=</span><span class="ss">f"building injection stub"</span></span>
<span id="cb5-218"><a href="#cb5-218" aria-hidden="true" tabindex="-1"></a>            ))</span>
<span id="cb5-219"><a href="#cb5-219" aria-hidden="true" tabindex="-1"></a>            <span class="cf">await</span> <span class="va">self</span>.build_exeasm()</span>
<span id="cb5-220"><a href="#cb5-220" aria-hidden="true" tabindex="-1"></a>        <span class="cf">await</span> SendMythicRPCTaskUpdate(MythicRPCTaskUpdateMessage(</span>
<span id="cb5-221"><a href="#cb5-221" aria-hidden="true" tabindex="-1"></a>            TaskID<span class="op">=</span>taskData.Task.ID,</span>
<span id="cb5-222"><a href="#cb5-222" aria-hidden="true" tabindex="-1"></a>            UpdateStatus<span class="op">=</span><span class="ss">f"generating stub shellcode"</span></span>
<span id="cb5-223"><a href="#cb5-223" aria-hidden="true" tabindex="-1"></a>        ))</span>
<span id="cb5-224"><a href="#cb5-224" aria-hidden="true" tabindex="-1"></a>        donutPic <span class="op">=</span> donut.create(</span>
<span id="cb5-225"><a href="#cb5-225" aria-hidden="true" tabindex="-1"></a>            <span class="bu">file</span><span class="op">=</span>EXEECUTE_ASSEMBLY_PATH, params<span class="op">=</span>taskData.args.get_arg(<span class="st">"pipe_name"</span>)</span>
<span id="cb5-226"><a href="#cb5-226" aria-hidden="true" tabindex="-1"></a>        )</span>
<span id="cb5-227"><a href="#cb5-227" aria-hidden="true" tabindex="-1"></a>        file_resp <span class="op">=</span> <span class="cf">await</span> SendMythicRPCFileCreate(</span>
<span id="cb5-228"><a href="#cb5-228" aria-hidden="true" tabindex="-1"></a>            MythicRPCFileCreateMessage(</span>
<span id="cb5-229"><a href="#cb5-229" aria-hidden="true" tabindex="-1"></a>                TaskID<span class="op">=</span>taskData.Task.ID, FileContents<span class="op">=</span>donutPic, DeleteAfterFetch<span class="op">=</span><span class="va">True</span></span>
<span id="cb5-230"><a href="#cb5-230" aria-hidden="true" tabindex="-1"></a>            )</span>
<span id="cb5-231"><a href="#cb5-231" aria-hidden="true" tabindex="-1"></a>        )</span>
<span id="cb5-232"><a href="#cb5-232" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span> file_resp.Success:</span>
<span id="cb5-233"><a href="#cb5-233" aria-hidden="true" tabindex="-1"></a>            taskData.args.add_arg(<span class="st">"loader_stub_id"</span>, file_resp.AgentFileId)</span>
<span id="cb5-234"><a href="#cb5-234" aria-hidden="true" tabindex="-1"></a>        <span class="cf">else</span>:</span>
<span id="cb5-235"><a href="#cb5-235" aria-hidden="true" tabindex="-1"></a>            <span class="cf">raise</span> <span class="pp">Exception</span>(</span>
<span id="cb5-236"><a href="#cb5-236" aria-hidden="true" tabindex="-1"></a>                <span class="st">"Failed to register execute_assembly binary: "</span> <span class="op">+</span> file_resp.Error</span>
<span id="cb5-237"><a href="#cb5-237" aria-hidden="true" tabindex="-1"></a>            )</span>
<span id="cb5-238"><a href="#cb5-238" aria-hidden="true" tabindex="-1"></a>        <span class="cf">return</span> response</span>
<span id="cb5-239"><a href="#cb5-239" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-240"><a href="#cb5-240" aria-hidden="true" tabindex="-1"></a>    <span class="cf">async</span> <span class="kw">def</span> process_response(</span>
<span id="cb5-241"><a href="#cb5-241" aria-hidden="true" tabindex="-1"></a>        <span class="va">self</span>, task: PTTaskMessageAllData, response: <span class="bu">any</span></span>
<span id="cb5-242"><a href="#cb5-242" aria-hidden="true" tabindex="-1"></a>    ) <span class="op">-></span> PTTaskProcessResponseMessageResponse:</span>
<span id="cb5-243"><a href="#cb5-243" aria-hidden="true" tabindex="-1"></a>        resp <span class="op">=</span> PTTaskProcessResponseMessageResponse(TaskID<span class="op">=</span>task.Task.ID, Success<span class="op">=</span><span class="va">True</span>)</span>
<span id="cb5-244"><a href="#cb5-244" aria-hidden="true" tabindex="-1"></a>        <span class="cf">return</span> resp</span></code></pre></div>
</section>
</div>
