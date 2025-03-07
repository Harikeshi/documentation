```json
CMakeSettings.json:{
  "configurations": [
    {
      "name": "x64-Debug",
      "generator": "Ninja",
      "configurationType": "Debug",
      "inheritEnvironments": [ "gcc_arm" ],
      "remoteMachineName": "your-remote-machine",
      "remoteCMakeRoot": "/home/user/cmake-remote",
      "remoteCMakeBuildRoot": "/home/user/cmake-remote-build",
      "cmakeExecutable": "/usr/bin/cmake",
      "buildRoot": "${projectDir}\\out\\build\\${name}",
      "installRoot": "${projectDir}\\out\\install\\${name}",
      "cmakeCommandArgs": "",
      "buildCommandArgs": "-v",
      "ctestCommandArgs": "",
      "cmakeToolchain": "",
      "variables": [
        {
          "name": "CMAKE_BUILD_TYPE",
          "value": "Debug"
        },
        {
          "name": "CMAKE_C_COMPILER",
          "value": "/usr/bin/gcc"
        },
        {
          "name": "CMAKE_CXX_COMPILER",
          "value": "/usr/bin/g++"
        }
      ],
      "cmakeEnvironment": {
        "NATVIS": "${projectDir}/path/to/your_file.natvis"
      },
      "buildEnvironment": {
        "NATVIS": "${projectDir}/path/to/your_file.natvis"
      }
    }
  ]
}
```