---
title: vscode配置-cpp
comments: true
date: 2018-04-27 10:59:14
categories:
- 工作环境配置
---

# My snippet
```json
{
	// Place your snippets for cpp here. Each snippet is defined under a snippet name and has a prefix, body and 
	// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the 
	// same ids are connected.
	// Example:
	// "Print to console": {
	// 	"prefix": "log",
	// 	"body": [
	// 		"console.log('$1');",
	// 		"$2"
	// 	],
	// 	"description": "Log output to console"
	// }
	
    "print to console":{
		"prefix": "inc",
		"body": [
			"//ybmj",
			"#include<bits/stdc++.h>",
			"using namespace std;",
			"#define lson (rt << 1)",
			"#define rson (rt << 1 | 1)",
			"#define lson_len (len - (len >> 1))",
			"#define rson_len (len >> 1)",
			"#define pb(x) push_back(x)",
			"#define clr(a, x) memset(a, x, sizeof(a))",
			"#define mp(x, y) make_pair(x, y)",
			"typedef long long ll;",
			"typedef pair<int,int> pii;",
			"typedef pair<ll,ll> pll;",
			"const int INF = 0x3f3f3f3f;",
			"const int NINF = 0xc0c0c0c0;",
			"",
			"int main(){",
			"\t/*",
			"\t#ifndef ONLINE_JUDGE",
			"\tfreopen(\"1.in\",\"r\",stdin);",
			"\tfreopen(\"1.out\",\"w\",stdout);",
			"\t#endif",
			"\t*/",
			"\tstd::ios::sync_with_stdio(false);",
			"",
			"}"
		]
	}
}


```
# launch.json
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/a.out",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "build"
        }
    ]
}
```

# tasks.json
```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "g++",
            "args": [
                // Ask msbuild to generate full paths for file names.
                "-g",
                "-std=c++11",
                "${file}"
            ],
            "group": {
                "kind":  "build",
                "isDefault": true
            },
            // Use the standard MS compiler pattern to detect errors, warnings and infos
            "problemMatcher": {
                "owner": "cpp",
                "fileLocation":"absolute",
                "pattern":{
                    "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
                    "file": 1,
                    "line": 2,
                    "column": 3,
                    "severity": 4,
                    "message": 5
                },
            }
        }
    ]
}
```

# 字体

**Windows : **

字体： Source Code Pro

渲染器： MacType 