---
layout: default
title: "Supplement Determining Root Cause"
permalink: /solo-purple-teaming/supplement-determining-root-cause/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Supplement Determining Root Cause</h1>
</section>
<section class="spt-content">

# Supplement: Determining Root Cause

Owner: Josh

Understanding how to determine root cause is a foundational skill across numerous cybersecurity disciplines, including—but not limited to—Incident Response, Reverse Engineering, Malware Analysis, Threat Hunting, and Red Teaming. Root cause analysis is the structured process of uncovering the underlying reason or chain of events that led to a specific security outcome or anomaly.

This process relies heavily on the practitioner’s ability to interpret evidence within the context of prior knowledge, including known behaviors, threat patterns, and baseline activity. Baselining—the practice of defining and understanding what constitutes normal behavior in a given environment—plays a critical role in this process. When a deviation from the baseline is observed, it serves as a signal that prompts further investigation.

By comparing current activity to established norms and leveraging accumulated knowledge, cybersecurity professionals can more effectively formulate hypotheses about the cause of an incident. They then follow a methodical approach to test and validate these hypotheses. This investigative mindset not only sharpens technical analysis but also enhances the accuracy, speed, and relevance of both defensive and offensive operations in complex environments.

# Identifying the Deviation from the Baseline

When we ran our reverse shell PowerShell payload with realtime protection enabled, we noticed a deviation - that is a different outcome, from the baseline we established in the previous lecture.

![image.png](Supplement%20Determining%20Root%20Cause/image.png)

Determining the root cause of this error is relatively straightforward. Applying a common system administration technique—examining recent changes—we can hypothesize that Windows Defender Real-Time Protection is blocking our payload. This hypothesis is supported by the `FullyQualifiedErrorId`, which is set to `ScriptContainedMaliciousContent`, a strong indicator that the script has been flagged as malicious.

# Proving Our Hypothesis

One effective way to validate our hypothesis is through debugging. Debugging is the process of analyzing a program's behavior by stepping through its execution in a controlled manner. This allows us to observe the program’s internal state, logic flow, and interactions with system components in real time.

In this case, we will attach a debugger to a PowerShell process to gain deeper insight into how the `ScriptContainedMaliciousContent` error is triggered. By tracing the execution path and monitoring relevant function calls, we can reverse engineer the underlying detection logic used by Windows Defender. This approach helps us not only confirm whether the error is being generated due to a specific code pattern or behavior, but also enhances our overall understanding of how defensive mechanisms are implemented at the system level.

Open a PowerShell instance

![image.png](Supplement%20Determining%20Root%20Cause/image%201.png)

Open x64dbg

![image.png](Supplement%20Determining%20Root%20Cause/image%202.png)

Attach to the PowerShell process

![image.png](Supplement%20Determining%20Root%20Cause/image%203.png)

When we attach a debugger to a PowerShell process, Windows Defender blocks the activity.

![image.png](Supplement%20Determining%20Root%20Cause/image%204.png)

# Use PSRunspaces to Work Around Windows Defender

A PowerShell runspace is the execution environment in which PowerShell commands, scripts, and functions are parsed, compiled, and executed. It encapsulates the runtime context, including variables, session state, execution policies, and loaded modules. Runspaces are the foundation of PowerShell’s execution model and can be created and managed programmatically, allowing for advanced scenarios such as multithreading, custom hosting, and automation within .NET applications. Understanding runspaces is essential for tasks such as debugging, custom script hosting, and performing more complex operations that require isolation or concurrency within a PowerShell-based workflow.

### Key Components:

**Engine State**

- Holds session-specific commands, providers, variables, and functions.
- Represents the current "state" of the PowerShell environment.

**Runtime**

- Manages execution context.
- Controls the flow of data and command execution.

**Pipeline**

- Enables object-based communication between commands.
- Core to PowerShell’s functionality.

### Open Visual Studio Code and Create a Console App(.NET Framework)

![image.png](Supplement%20Determining%20Root%20Cause/image%205.png)

Browse to and add a reference to C:\Program Files (x86)\Reference Assemblies\Microsoft\WindowsPowerShel\3.0\System.Management.Automation.dll

![image.png](Supplement%20Determining%20Root%20Cause/image%206.png)

Write a program to create a PSRunspace and run the command “whoami”

```csharp
using System;
using System.Collections.ObjectModel;
using System.Diagnostics;
using System.Management.Automation;
using System.Runtime.InteropServices;
using PowerShell = System.Management.Automation.PowerShell;

namespace AttachDebuggerTest
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.ReadLine();
            PowerShell ps = PowerShell.Create();
            string script = "whoami";
            ps.AddScript(script);
            Collection<PSObject> results = ps.Invoke();

            foreach (PSObject obj in results)
            {
                Console.WriteLine(obj.ToString());
            }
            Console.ReadLine();
            
        }
    }
}
```

Compile for x64 and run:

![image.png](Supplement%20Determining%20Root%20Cause/image%207.png)

Attach to the process with x64dbg

![image.png](Supplement%20Determining%20Root%20Cause/image%208.png)

![image.png](Supplement%20Determining%20Root%20Cause/image%209.png)

## Reverse Engineering PowerShell

Download the PowerShell Project from GitHub on your Reverse Engineering VM:
https://github.com/PowerShell/PowerShell

### 1. Open in the solution file with VisualStudio and search the project for `ScriptContainedMaliciousContent`

![image.png](Supplement%20Determining%20Root%20Cause/image%2010.png)

You will see 4 results:

![image.png](Supplement%20Determining%20Root%20Cause/image%2011.png)

### 3. Click on the first instance in CompiledScriptBlock.cs and analyze the code to determine what is going on

```csharp
// Call the AMSI API to determine if the script block has malicious content
var amsiResult = AmsiUtils.ScanContent(scriptExtent.Text, scriptFile);

if (amsiResult == AmsiUtils.AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_DETECTED)
{
    var parseError = new ParseError(
        scriptExtent,
        "ScriptContainedMaliciousContent",
        ParserStrings.ScriptContainedMaliciousContent);
    throw new ParseException(new[] { parseError });
}
```

This code checks whether a PowerShell script block contains malicious content using the **Antimalware Scan Interface (AMSI)**. If malicious content is detected, it generates a **parse error** and halts further processing of the script.

This line tells us where to go next

```csharp
// Call the AMSI API to determine if the script block has malicious content
var amsiResult = AmsiUtils.ScanContent(scriptExtent.Text, scriptFile);
```

amsiResult which is used as a flag to determine if the content was malicious or not, is a result of calling AmsiUtils.ScanContent. 

### 4. Jump to the definition of ScanContent and analyze the code to determine the function of ScanContent

```csharp
/// <summary>
/// Scans a string buffer for malware using the Antimalware Scan Interface (AMSI).
/// Caller is responsible for calling AmsiCloseSession when a "session" (script)
/// is complete, and for calling AmsiUninitialize when the runspace is being torn down.
/// </summary>
/// <param name="content">The string to be scanned.</param>
/// <param name="sourceMetadata">Information about the source (filename, etc.).</param>
/// <returns>AMSI_RESULT_DETECTED if malware was detected in the sample.</returns>
internal static AmsiNativeMethods.AMSI_RESULT ScanContent(string content, string sourceMetadata)
{
        #if UNIX
                return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_NOT_DETECTED;
        #else
                return WinScanContent(content, sourceMetadata, warmUp: false);
        #endif
}
```

### 5. Jump to the definition of WinScanContent

```csharp
internal static AmsiNativeMethods.AMSI_RESULT WinScanContent(
    string content,
    string sourceMetadata,
    bool warmUp)
{
    if (string.IsNullOrEmpty(sourceMetadata))
    {
        sourceMetadata = string.Empty;
    }

    const string EICAR_STRING = "X5O!P%@AP[4\\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*";
    if (InternalTestHooks.UseDebugAmsiImplementation)
    {
        if (content.Contains(EICAR_STRING, StringComparison.Ordinal))
        {
            return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_DETECTED;
        }
    }

    // If we had a previous initialization failure, just return the neutral result.
    if (s_amsiInitFailed)
    {
        PSEtwLog.LogAmsiUtilStateEvent("ScanContent-InitFail", $"{s_amsiContext}-{s_amsiSession}");
        return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_NOT_DETECTED;
    }

    lock (s_amsiLockObject)
    {
        if (s_amsiInitFailed)
        {
            PSEtwLog.LogAmsiUtilStateEvent("ScanContent-InitFail", $"{s_amsiContext}-{s_amsiSession}");
            return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_NOT_DETECTED;
        }

        try
        {
            if (!CheckAmsiInit())
            {
                return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_NOT_DETECTED;
            }

            if (warmUp)
            {
                // We are warming up the AMSI component in console startup, and that means we initialize AMSI
                // and create a AMSI session, but don't really scan anything.
                return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_NOT_DETECTED;
            }

            AmsiNativeMethods.AMSI_RESULT result = AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_CLEAN;

            // Run AMSI content scan
            int hr;
            unsafe
            {
                fixed (char* buffer = content)
                {
                    var buffPtr = new IntPtr(buffer);
                    hr = AmsiNativeMethods.AmsiScanBuffer(
                        s_amsiContext,
                        buffPtr,
                        (uint)(content.Length * sizeof(char)),
                        sourceMetadata,
                        s_amsiSession,
                        ref result);
                }
            }

            if (!Utils.Succeeded(hr))
            {
                // If we got a failure, just return the neutral result ("AMSI_RESULT_NOT_DETECTED")
                PSEtwLog.LogAmsiUtilStateEvent($"AmsiScanBuffer-{hr}", $"{s_amsiContext}-{s_amsiSession}");
                return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_NOT_DETECTED;
            }

            return result;
        }
        catch (DllNotFoundException)
        {
            PSEtwLog.LogAmsiUtilStateEvent("DllNotFoundException", $"{s_amsiContext}-{s_amsiSession}");
            return AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_NOT_DETECTED;
        }
    }
}
```

This method prepares the script content, ensures AMSI is properly initialized, and then calls the native AMSI API to scan the buffer. The scan result is either:

- `AMSI_RESULT_DETECTED` for malicious content, or
- `AMSI_RESULT_NOT_DETECTED` / `AMSI_RESULT_CLEAN` if the content is safe.

### 5. Follow the definition of AmsiScanBuffer

```csharp
/// Return Type: HRESULT->LONG->int
///amsiContext: HAMSICONTEXT->HAMSICONTEXT__*
///buffer: PVOID->void*
///length: ULONG->unsigned int
///contentName: LPCWSTR->WCHAR*
///amsiSession: HAMSISESSION->HAMSISESSION__*
///result: AMSI_RESULT*
[DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
[DllImport("amsi.dll", EntryPoint = "AmsiScanBuffer", CallingConvention = CallingConvention.StdCall)]
internal static extern int AmsiScanBuffer(
System.IntPtr amsiContext,
System.IntPtr buffer,
uint length,
[In][MarshalAs(UnmanagedType.LPWStr)] string contentName,
System.IntPtr amsiSession,
ref AMSI_RESULT result);
```

This native function takes the memory buffer for the script, its length, a metadata string (e.g., file name), and outputs an AMSI scan result. It's the bridge between PowerShell and the Windows Defender antimalware engine.

### 6. Relaunch AttachDebuggerTest in x64dbg

![image.png](Supplement%20Determining%20Root%20Cause/image%2012.png)

### 7. Make sure x64dbg is in a running state and then press enter in the terminal to run the PowerShell script in the PSRunspace

![image.png](Supplement%20Determining%20Root%20Cause/image%2013.png)

### 8. Run the application to run the “whoami” via the PSRunspace within AttachDebuggerTest

![image.png](Supplement%20Determining%20Root%20Cause/image%2014.png)

### 9. Via the Symbols tab, find AmsiScanBuffer and set a breakpoint

![image.png](Supplement%20Determining%20Root%20Cause/image%2015.png)

### 10. Modify AttachDebuggerTest to run “Invoke-Mimikatz”  and rebuild solution

![image.png](Supplement%20Determining%20Root%20Cause/image%2016.png)

### 11. Relaunch AttachDebuggerTest in x64dbg

![image.png](Supplement%20Determining%20Root%20Cause/image%2012.png)

### 11. Step through the program until you hit the breakpoint for AmsiScanContent and verify “Invoke-Mimikatz” is being passed as a parameter

![image.png](Supplement%20Determining%20Root%20Cause/image%2017.png)

### 12. Step through the program until it terminates and verify the parse error for malicious content blocked occurs

![image.png](Supplement%20Determining%20Root%20Cause/image%2018.png)

</section>
</div>
