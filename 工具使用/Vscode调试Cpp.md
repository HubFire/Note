## 注意的点：
- launch文件中修改program为可执行文件路径
- 需要先make编译出可执行文件
- make时候要打开-g选项 cmakeList中：add_definitions("-Wall -g")
- tasks.json里定义任务，在launch中的preLaunchTask进行配置

## launch.json配置如下
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++-5 build and debug active file",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/bspline",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "g++-5 build active file",
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```
