title:: lauch.json for VS code
- ```json
  {
    "version": "0.2.0",
    "configurations": [
      {
        // name: A descriptive name for the configuration that will show up in the dropdown list in the Debug view.
        "name": "Debug",
        // type: Specifies the debugger type. This could be node for Node.js, cppdbg for C++, python for Python, etc.
        "type": "cppdbg",
        // request: Indicates the request type. The most common values are
        // launch (to start and debug a new instance of your target) and 
        // attach (to connect the debugger to an already running instance).
        "request": "launch",
        // program: Specifies the path to the program you want to debug. 
        // Depending on the type, this might be a JavaScript file, a compiled binary, a Python script, etc.
        "program": "${workspaceFolder}/myProgram.out",
        // args: An array of command-line arguments passed to the program when it's launched.
        "args": [],
        "stopAtEntry": false,
        // cwd: Current working directory for the debugging process.
        "cwd": "${workspaceFolder}",
        // environment: An array of environment variables for the debugging process.
        "environment": [],
        // externalConsole: For some debuggers, this decides if a new console window should be opened for the debugged process.
        "externalConsole": false,
        // MIMode refers to the "Machine Interface Mode." 
        // It specifies which debugger backend Visual Studio Code should use when debugging a particular type of code. 
        // This is relevant when you're debugging languages like C or C++ where different debugging backends might be preferable based on the platform or the specifics of what you're trying to achieve.
        "MIMode": "gdb",
        "setupCommands": [
          {
            "description": "Enable pretty-printing for gdb",
            "text": "-enable-pretty-printing",
            "ignoreFailures": true
          }
        ],
        // preLaunchTask: Name of a task from tasks.json to run before starting the debug session.
        "preLaunchTask": "build-my-program", // This assumes you have a task named 'build-my-program' in your tasks.json
        "miDebuggerPath": "/usr/bin/gdb",
        "linux": {
          "miDebuggerPath": "/usr/bin/gdb"
        },
        "windows": {
          "miDebuggerPath": "C:\\path\\to\\gdb.exe"
        },
        "osx": {
          "miDebuggerPath": "/usr/local/bin/gdb"
        }
      }
    ]
  }
  
  ```