### Пример `c_cpp_properties.json`
```json
{
  "configurations": [
    {
      "name": "Win32",
      "includePath": [
        "${workspaceFolder}/**",
        "C:/path/to/includes"
      ],
      "defines": [
        "MY_DEFINE",
        "_DEBUG"
      ],
      "compilerPath": "C:/path/to/compiler/gcc.exe",
      "cStandard": "c11",
      "cppStandard": "c++11",
      "intelliSenseMode": "gcc-x64"
    },
    {
      "name": "Linux",
      "includePath": [
        "${workspaceFolder}/**",
        "/usr/include"
      ],
      "defines": [
        "MY_DEFINE",
        "NDEBUG"
      ],
      "compilerPath": "/usr/bin/gcc",
      "cStandard": "c11",
      "cppStandard": "c++11",
      "intelliSenseMode": "gcc-x64"
    }
  ],
  "version": 4
}
```

### Описание параметров:
- **configurations**: Список конфигураций для разных платформ (например, Windows, Linux).
- **name**: Имя конфигурации (например, `Win32`, `Linux`).
- **includePath**: Пути к директориям с заголовочными файлами, которые будут использоваться для автодополнения и IntelliSense.
- **defines**: Предопределенные макросы для компиляции.
- **compilerPath**: Путь к компилятору, который будет использоваться для сборки проекта.
- **cStandard**: Стандарт языка C (например, `c11`).
- **cppStandard**: Стандарт языка C++ (например, `c++11`).
- **intelliSenseMode**: Режим IntelliSense для конкретного компилятора (например, `gcc-x64`).

### Как использовать:
1. Создайте файл `c_cpp_properties.json` в папке `.vscode` вашего проекта.
2. Настройте параметры в соответствии с вашими требованиями и окружением.
3. Visual Studio Code автоматически применит эти настройки для автодополнения, IntelliSense и других функций.
