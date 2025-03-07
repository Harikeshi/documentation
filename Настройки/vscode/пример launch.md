Для создания конфигураций отладки в Visual Studio Code для проектов на C++ файл `launch.json` также используется для настройки параметров отладки. Вот пример файла `launch.json`, специально настроенного для C++:

### Пример файла `launch.json` для C++:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "GDB Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/a.out",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
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
            "preLaunchTask": "build",
            "miDebuggerPath": "/usr/bin/gdb",
            "logging": {
                "engineLogging": false
            },
            "launchCompleteCommand": "exec-run",
            "sourceFileMap": {
                "/usr/src/myapp": "${workspaceFolder}"
            },
            "pipeTransport": {
                "pipeCwd": "",
                "pipeProgram": "",
                "pipeArgs": [],
                "debuggerPath": ""
            }
        }
    ]
}
```

### Описание элементов:

1. **version**: Версия формата файла конфигурации отладки. Текущая версия - "0.2.0".
2. **configurations**: Массив конфигураций отладки, каждая из которых представлена объектом с настройками.

#### Внутри каждой конфигурации:

- **name**: Имя конфигурации, отображаемое в списке конфигураций отладки.
- **type**: Тип среды выполнения (для C++ используется "cppdbg").
- **request**: Тип запроса. В данном случае используется "launch" для запуска программы.
- **program**: Путь к исполняемому файлу, который нужно запустить.
- **args**: Аргументы, передаваемые программе (массив).
- **stopAtEntry**: Останавливать ли выполнение при входе в программу.
- **cwd**: Рабочий каталог (например, папка проекта).
- **environment**: Переменные окружения для настройки среды выполнения.
- **externalConsole**: Использовать ли внешнюю консоль для вывода.
- **MIMode**: Режим взаимодействия с отладчиком (для GDB используется "gdb").
- **setupCommands**: Команды, выполняемые перед запуском отладки (например, включение pretty-printing).
- **preLaunchTask**: Задача, выполняемая перед запуском (например, сборка проекта).
- **miDebuggerPath**: Путь к исполняемому файлу отладчика GDB.
- **logging**: Настройки логирования (например, `engineLogging`).
- **launchCompleteCommand**: Команда, выполняемая после запуска программы.
- **sourceFileMap**: Карта путей к исходным файлам для отображения исходников (например, сопоставление путей в контейнере с локальными путями).
- **pipeTransport**: Параметры для настройки транспортного канала (если необходимо).

Этот файл `launch.json` позволяет настроить и автоматизировать процесс отладки для проектов на C++ в Visual Studio Code.