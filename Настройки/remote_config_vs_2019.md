```json
{
  "configurations": [
    {
      "name": "Astra-Debug",
      "generator": "Ninja",
      "configurationType": "Debug",
      "buildRoot": "${workspaceRoot}/build/${name}",
      "installRoot": "${workspaceRoot}/install/${name}",
      "remoteMachineName": "${defaultRemoteMachineName}",
      "remoteCMakeListsRoot": "/home/harikeshi/work/projects/support",
      "remoteBuildRoot": "/home/harikeshi/work/projects/support/build",
      "remoteInstallRoot": "/home/harikeshi/work/projects/support/install",
      "cmakeExecutable": "/usr/bin/cmake",
      "remoteCopySources": true,
      "remoteCopyBuildOutput": true,
      "buildCommandArgs": "-j40",
      "ctestCommandArgs": "",```json
      "variables": [
        {
          "name": "CMAKE_C_COMPILER",
          "value": "/usr/bin/gcc",
          "type": "STRING"
        },
        {
          "name": "CMAKE_CXX_COMPILER",
          "value": "/usr/bin/g++",
          "type": "STRING"
        },
        {
          "name": "CMAKE_CXX_STANDARD",
          "value": "17",
          "type": "STRING"
        }
      ],
      "inheritEnvironments": [ "linux_x64" ],
      "intelliSenseMode": "linux-gcc-x64",
      "remoteCopySourcesMethod": "rsync",
      "addressSanitizerEnabled": true
    },
    {
      "name": "Astra-Release",
      "generator": "Ninja",
      "configurationType": "Release",
      "buildRoot": "${workspaceRoot}/build/${name}",
      "installRoot": "${workspaceRoot}/install/${name}",
      "remoteMachineName": "${defaultRemoteMachineName}",
      "remoteCMakeListsRoot": "/home/harikeshi/work/projects/support",
      "remoteBuildRoot": "/home/harikeshi/work/projects/support/build",
      "remoteInstallRoot": "/home/harikeshi/work/projects/support/install",
      "cmakeExecutable": "/usr/bin/cmake",
      "remoteCopySources": true,
      "remoteCopyBuildOutput": true,
      "buildCommandArgs": "-j40",
      "ctestCommandArgs": "",
      "variables": [
        {
          "name": "CMAKE_C_COMPILER",
          "value": "/usr/bin/gcc",
          "type": "STRING"
        },
        {
          "name": "CMAKE_CXX_COMPILER",
          "value": "/usr/bin/g++",
          "type": "STRING"
        }
      ],
      "inheritEnvironments": []
    }
  ]
}
```