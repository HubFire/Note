# vscode 调试ros
在cmake文件中启用gdb，然后，在launch文件加内容
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) planning",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/devel/lib/planning/planning_node",
            "args": ["__name:=planning_node","GLOG_logtostderr=1","GLOG_colorlogtostderr=1","--log_level=0"],
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
            ]
        },
        {
            "name": "(gdb) fake_msgs",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/devel/lib/path_recorder/fake_msgs_node",
            "args": ["__name:=fake_msgs_node","GLOG_logtostderr=1","GLOG_colorlogtostderr=1","--log_level=0","--start=0,0,0,0,0,0","--local_generator=ctrl_simulator","--loop_rate=100","--path_generator=b_spline"],
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
            ]
        }
    ]
}
```
