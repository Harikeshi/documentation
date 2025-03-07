c:\msys64\usr\include

Gtest должен быть установлен в msys2:
- Настройка для **Visual Studio 2022**:
Установка в CMakeSettings.json - Аргументы команд cmake:
C:\Users\ShchegolikhinSN\AppData\Local\Microsoft\VisualStudio\17.0_f6d0c812\OpenFolder\CMakeSettings_schema.json
```cmake
-DGTEST_LIBRARY=GTest::gtest -DGTEST_LIBRARIES=GTest::gtest
```
- Настройка для **QtCreator**(так как в новых версиях gtest вместо GTEST_LIBRARY GTEST_LIBRARIES):
Установка в Build/Run -> Current Configuration:
```cmake
GTEST_LIBRARY=Gtest::gtest
```
- Настройка для **VS code**:
Установка в settings.json:
```json
 "cmake.configureSettings": {
    "DCMAKE_BUILD_TYPE": "Debug",
    "GTEST_LIBRARY":"GTest::gtest"
  },
```

Visual Studio 2022, в корне проекта создается файл CMakeSettings.json и в нем прописывается следующая конфигурация.

```json
 {
      "name": "Mingw64-Debug",
      "generator": "Ninja",
      "configurationType": "Debug",
      "buildRoot": "${projectDir}\\out\\build\\${name}",
      "installRoot": "${projectDir}\\out\\install\\${name}",
      "cmakeCommandArgs": "-DGTEST_LIBRARY=GTest::gtest -DGTEST_LIBRARIES=GTest::gtest",
      "buildCommandArgs": "-v",
      "ctestCommandArgs": "",
      "inheritEnvironments": [ "mingw_64" ],
      "environments": [
        {
          "MINGW64_ROOT": "C:/msys64/mingw64",
          "BIN_ROOT": "${env.MINGW64_ROOT}/bin",
          "FLAVOR": "x86_64-w64-mingw32",
          "TOOLSET_VERSION": "9.1.0",
          "PATH": "${env.MINGW64_ROOT}/bin;${env.MINGW64_ROOT}/../usr/local/bin;${env.MINGW64_ROOT}/../usr/bin;${env.MINGW64_ROOT}/../bin;${env.PATH}",
          "INCLUDE": "${env.INCLUDE};${env.MINGW64_ROOT}/include/c++/${env.TOOLSET_VERSION};${env.MINGW64_ROOT}/include/c++/${env.TOOLSET_VERSION}/tr1;${env.MINGW64_ROOT}/include/c++/${env.TOOLSET_VERSION}/${env.FLAVOR}",
          "environment": "mingw_64"
        }
      ],
      "variables": [
        {
          "name": "CMAKE_C_COMPILER",
          "value": "${env.BIN_ROOT}/gcc.exe",
          "type": "STRING"
        },
        {
          "name": "CMAKE_CXX_COMPILER",
          "value": "${env.BIN_ROOT}/g++.exe",
          "type": "STRING"
        }
      ],
      "intelliSenseMode": "linux-gcc-x64"
    }
```