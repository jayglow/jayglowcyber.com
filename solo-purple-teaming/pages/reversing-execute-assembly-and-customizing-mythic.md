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

Owner: Josh

## Current IOC Diagram

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image.png)

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%201.png)

We have determined that when we run our malicious PowerShell command via the run dialog, Explorer.exe spawns a PowerShell process that reflectively loads apollo.exe. When we use execute_assembly, a sacrificial process is spawned determined by the setting of spawns_x64 and a CLR loader is injected that reflectively loads the .NET assembly passed as an argument. In this walkthrough,  we set out to understand how the global unique pipe name is created and why is the system process involved. In CobaltStrike, when execute assembly is used, the sacrificial process creates the process and the host process connects to the pipe.

## Mythic Directory Structure

Reference:

[https://docs.mythic-c2.net/customizing/payload-type-development](https://docs.mythic-c2.net/customizing/payload-type-development)

### All C2 Profiles and Payload Types are installed in <mythic install directory>/InstallServices.

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%202.png)

### Looking inside apollo, we see the Dockerfile and the [main.py](http://main.py) file along with another directory named apollo

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%203.png)

The `Mythic/InstalledServices/[agent name]` folder is mapped to `/Mythic` in the docker container. Editing the files on disk results in the edits appearing in the docker container and visa versa.`Mythic/InstalledServices/[agent name]/main.py` file is what will get executed by Python 3.11.

### Inside the `apollo` directory, we find the `agent_code` folder, which we discussed in earlier lectures, along with another folder named `mythic`. The `mythic` directory contains all the code required for the Mythic framework.

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%204.png)

### Inside the mythic folder is the agent_functions and browser_scripts directories

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%205.png)

### The agent_functions directory holds all of the python scripts for the various commands that Apollo implements including execute_assembly.py

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%206.png)

Inside execute_assembly.py, we can add additional command  parameters which are defined in the **init** function:

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%207.png)

Add another CommandParameter for PipeName. The name we set for this CommandParemter is very important since it is referenced later in the code. 

```python
CommandParameter(
    name="pipe_name",
    cli_name="PipeName",
    display_name="PipeName",
    type=ParameterType.String,
    description="PipeName used for IPC",
    parameter_group_info=[
        ParameterGroupInfo(
            required=False, group_name="Default", ui_position=3
        ),
        ParameterGroupInfo(
            required=False, group_name="New Assembly", ui_position=3
        ),
    ],
),
```

In the create_go_tasking function, copy this lline of code in the source and then comment this line of code out. 

```python
taskData.args.add_arg("pipe_name", str(uuid4()))
```

The above line of code sets the pipe name used for IPC to a globally unique identifier.  We want to be able to set a custom value for pipe name or use the global unique identifier as the default.

Move to the top of the create_go_tasking under this line of code

```python
originalGroupNameIsDefault = taskData.args.get_parameter_group_name() == "Default"
```

Insert the following custom code to check for the pipe_name and use if set otherwise generate a globally unique identifie

```python
pipe_name = taskData.args.get_arg('pipe_name')
if pipe_name == "" or pipe_name is None:
	taskData.args.set_arg("pipe_name", str(uuid4()))
```

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%208.png)

## Coding Challenge

Write the code to update display in mythic to show the pipe name parameter if set.

![image.png](Reversing%20Execute%20Assembly%20and%20Customizing%20Mythic/image%209.png)

## Full Code for Customized execute_assembly.py

```python
from distutils.dir_util import copy_tree
import shutil
import tempfile
from mythic_container.MythicCommandBase import *
from uuid import uuid4
from mythic_container.MythicRPC import *
from os import path
import asyncio
import donut
import platform

if platform.system() == "Windows":
    EXEECUTE_ASSEMBLY_PATH = "C:\\Mythic\\Apollo\\srv\\ExecuteAssembly.exe"
else:
    EXEECUTE_ASSEMBLY_PATH = "/srv/ExecuteAssembly.exe"

class ExecuteAssemblyArguments(TaskArguments):

    def __init__(self, command_line, **kwargs):
        super().__init__(command_line, **kwargs)
        self.args = [
            CommandParameter(
                name="assembly_name",
                cli_name="Assembly",
                display_name="Assembly",
                type=ParameterType.ChooseOne,
                dynamic_query_function=self.get_files,
                description="Assembly to execute (e.g., Seatbelt.exe).",
                parameter_group_info=[
                    ParameterGroupInfo(
                        required=True, group_name="Default", ui_position=1
                    )
                ],
            ),
            CommandParameter(
                name="assembly_file",
                display_name="New Assembly",
                type=ParameterType.File,
                description="A new assembly to execute. After uploading once, you can just supply the assembly_name parameter",
                parameter_group_info=[
                    ParameterGroupInfo(
                        required=True, group_name="New Assembly", ui_position=1,
                    )
                ]
            ),
            CommandParameter(
                name="assembly_arguments",
                cli_name="Arguments",
                display_name="Arguments",
                type=ParameterType.String,
                description="Arguments to pass to the assembly.",
                parameter_group_info=[
                    ParameterGroupInfo(
                        required=False, group_name="Default", ui_position=2
                    ),
                    ParameterGroupInfo(
                        required=False, group_name="New Assembly", ui_position=2
                    ),
                ],
            ),
            CommandParameter(
                name="pipe_name",
                cli_name="PipeName",
                display_name="PipeName",
                type=ParameterType.String,
                description="PipeName use for IPC.",
                parameter_group_info=[
                    ParameterGroupInfo(
                        required=False, group_name="Default", ui_position=3
                    ),
                    ParameterGroupInfo(
                        required=False, group_name="New Assembly", ui_position=3
                    ),
                ],
            ),
        ]

    async def get_files(
        self, inputMsg: PTRPCDynamicQueryFunctionMessage
    ) -> PTRPCDynamicQueryFunctionMessageResponse:
        fileResponse = PTRPCDynamicQueryFunctionMessageResponse(Success=False)
        file_resp = await SendMythicRPCFileSearch(
            MythicRPCFileSearchMessage(
                CallbackID=inputMsg.Callback,
                LimitByCallback=True,
                Filename="",
            )
        )
        if file_resp.Success:
            file_names = []
            for f in file_resp.Files:
                if f.Filename not in file_names and f.Filename.endswith(".exe"):
                    file_names.append(f.Filename)
            fileResponse.Success = True
            fileResponse.Choices = file_names
            return fileResponse
        else:
            fileResponse.Error = file_resp.Error
            return fileResponse

    async def parse_arguments(self):
        if len(self.command_line) == 0:
            raise Exception(
                "Require an assembly to execute.\n\tUsage: {}".format(
                    ExecuteAssemblyCommand.help_cmd
                )
            )
        if self.command_line[0] == "{":
            self.load_args_from_json_string(self.command_line)
        else:
            parts = self.command_line.split(" ", maxsplit=1)
            self.add_arg("assembly_name", parts[0])
            self.add_arg("assembly_arguments", "")
            if len(parts) == 2:
                self.add_arg("assembly_arguments", parts[1])

class ExecuteAssemblyCommand(CommandBase):
    cmd = "execute_assembly"
    needs_admin = False
    help_cmd = "execute_assembly [Assembly.exe] [args]"
    description = "Executes a .NET assembly with the specified arguments. This assembly must first be known by the agent using the `register_assembly` command or by supplying an assembly with the task."
    version = 3
    author = "@djhohnstein"
    argument_class = ExecuteAssemblyArguments
    attackmapping = ["T1547"]

    async def build_exeasm(self):
        try:
            global EXEECUTE_ASSEMBLY_PATH
            agent_build_path = tempfile.TemporaryDirectory()
            outputPath = "{}/ExecuteAssembly/bin/Release/ExecuteAssembly.exe".format(
                agent_build_path.name
            )
            # shutil to copy payload files over
            copy_tree(str(self.agent_code_path), agent_build_path.name)
            shell_cmd = "dotnet build -c release -p:DebugType=None -p:DebugSymbols=false -p:Platform=x64 {}/ExecuteAssembly/ExecuteAssembly.csproj -o {}/ExecuteAssembly/bin/Release/".format(
                agent_build_path.name, agent_build_path.name
            )
            proc = await asyncio.create_subprocess_shell(
                shell_cmd,
                stdout=asyncio.subprocess.PIPE,
                stderr=asyncio.subprocess.PIPE,
                cwd=agent_build_path.name,
            )
            stdout, stderr = await proc.communicate()
            if not path.exists(outputPath):
                raise Exception(
                    "Failed to build ExecuteAssembly.exe:\n{}".format(
                        stderr.decode() + "\n" + stdout.decode()
                    )
                )
            shutil.copy(outputPath, EXEECUTE_ASSEMBLY_PATH)
        except Exception as ex:
            raise Exception(ex)

    async def create_go_tasking(
        self, taskData: PTTaskMessageAllData
    ) -> PTTaskCreateTaskingMessageResponse:
        response = PTTaskCreateTaskingMessageResponse(
            TaskID=taskData.Task.ID,
            Success=True,
        )
        global EXEECUTE_ASSEMBLY_PATH
        originalGroupNameIsDefault = taskData.args.get_parameter_group_name() == "Default"
        
        pipe_name = taskData.args.get_arg('pipe_name')
        if pipe_name == "" or pipe_name is None:
            taskData.args.set_arg("pipe_name", str(uuid4()))

        if taskData.args.get_parameter_group_name() == "New Assembly":
            fileSearchResp = await SendMythicRPCFileSearch(MythicRPCFileSearchMessage(
                TaskID=taskData.Task.ID,
                AgentFileID=taskData.args.get_arg("assembly_file")
            ))
            if not fileSearchResp.Success:
                raise Exception(f"Failed to find uploaded file: {fileSearchResp.Error}")
            if len(fileSearchResp.Files) == 0:
                raise Exception(f"Failed to find matching file, was it deleted?")
            searchedTaskResp = await SendMythicRPCTaskSearch(MythicRPCTaskSearchMessage(
                TaskID=taskData.Task.ID,
                SearchCallbackID=taskData.Callback.ID,
                SearchCommandNames=["register_file"],
                SearchParams=taskData.args.get_arg("assembly_file")
            ))
            if not searchedTaskResp.Success:
                raise Exception(f"Failed to search for matching tasks: {searchedTaskResp.Error}")
            if len(searchedTaskResp.Tasks) == 0:
                # we need to register this file with apollo first
                subtaskCreationResp = await SendMythicRPCTaskCreateSubtask(MythicRPCTaskCreateSubtaskMessage(
                    TaskID=taskData.Task.ID,
                    CommandName="register_file",
                    Params=json.dumps({"file": taskData.args.get_arg("assembly_file")})
                ))
                if not subtaskCreationResp.Success:
                    raise Exception(f"Failed to create register_file subtask: {subtaskCreationResp.Error}")

            taskData.args.add_arg("assembly_name", fileSearchResp.Files[0].Filename)
            if fileSearchResp.Files[0].AgentFileId in taskData.Task.OriginalParams:
                response.DisplayParams = f"-Assembly {fileSearchResp.Files[0].Filename} -Arguments {taskData.args.get_arg('assembly_arguments')} -PipeName {taskData.args.get_arg('pipe_name')}"
            taskData.args.remove_arg("assembly_file")

        taskargs = taskData.args.get_arg("assembly_arguments")
        if originalGroupNameIsDefault:
            if taskargs == "" or taskargs is None:
                response.DisplayParams = "-Assembly {} -PipeName {}".format(
                    taskData.args.get_arg("assembly_name"), taskData.args.get_arg('pipe_name')
                )
            else:
                response.DisplayParams = "-Assembly {} -Arguments {}".format(
                    taskData.args.get_arg("assembly_name"), taskargs
                )
        #taskData.args.add_arg("pipe_name", str(uuid4()))
        if not path.exists(EXEECUTE_ASSEMBLY_PATH):
            # create
            await SendMythicRPCTaskUpdate(MythicRPCTaskUpdateMessage(
                TaskID=taskData.Task.ID,
                UpdateStatus=f"building injection stub"
            ))
            await self.build_exeasm()
        await SendMythicRPCTaskUpdate(MythicRPCTaskUpdateMessage(
            TaskID=taskData.Task.ID,
            UpdateStatus=f"generating stub shellcode"
        ))
        donutPic = donut.create(
            file=EXEECUTE_ASSEMBLY_PATH, params=taskData.args.get_arg("pipe_name")
        )
        file_resp = await SendMythicRPCFileCreate(
            MythicRPCFileCreateMessage(
                TaskID=taskData.Task.ID, FileContents=donutPic, DeleteAfterFetch=True
            )
        )
        if file_resp.Success:
            taskData.args.add_arg("loader_stub_id", file_resp.AgentFileId)
        else:
            raise Exception(
                "Failed to register execute_assembly binary: " + file_resp.Error
            )
        return response

    async def process_response(
        self, task: PTTaskMessageAllData, response: any
    ) -> PTTaskProcessResponseMessageResponse:
        resp = PTTaskProcessResponseMessageResponse(TaskID=task.Task.ID, Success=True)
        return resp
```

</section>
</div>
