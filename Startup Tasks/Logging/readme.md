This is a logging wrapper for existing startup tasks. The start and end of each task is logged to `%TEMP%\StartupLog.txt`. In the **ServiceDefinition.cmd** file, add the task with

```xml
<Startup>
    <Task commandLine="logwrap.cmd other-task.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

In the called startup tasks (like **other-task.cmd** in the example above), each `ECHO` statement will also be logged to the same file. For example:

```cmd
ECHO [%date% %time%] Some log information about this task
```

Example of the **StartupLog.txt** output.

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================
```