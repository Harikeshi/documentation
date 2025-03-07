### Пример файла `launch.json` для LLDB:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch with LLDB",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/a.out",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "lldb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for lldb",
                    "text": "settings set target.inline-breakpoint-strategy always",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "build",
            "miDebuggerPath": "/usr/bin/lldb",
            "logging": {
                "engineLogging": false
            },
            "sourceFileMap": {
                "/usr/src/myapp": "${workspaceFolder}"
            },
            "launchCompleteCommand": "exec-run"
        },
        {
            "name": "Attach to Process with LLDB",
            "type": "cppdbg",
            "request": "attach",
            "program": "${workspaceFolder}/a.out",
            "processId": "${command:pickProcess}",
            "MIMode": "lldb",
            "miDebuggerPath": "/usr/bin/lldb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for lldb",
                    "text": "settings set target.inline-breakpoint-strategy always",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```

### Описание конфигураций:

1. **Launch with LLDB**: Запуск программы с использованием LLDB.
   - **MIMode: "lldb"** указывает на использование LLDB для отладки.
   - **preLaunchTask** запускает задачу сборки перед отладкой.
   - **externalConsole: true** использует внешнюю консоль для вывода.

2. **Attach to Process with LLDB**: Подключение к уже запущенному процессу с использованием LLDB.
   - **processId: "${command:pickProcess}"** позволяет выбрать процесс для подключения.

### Дополнительные условия:

- **setupCommands**: Команды для настройки отладчика, такие как включение pretty-printing и установка стратегии точек останова.
- **sourceFileMap**: Карта путей к исходным файлам для отображения исходников (например, сопоставление путей в контейнере с локальными путями).
- **stopAtEntry**: Останавливать ли выполнение при входе в программу.
- **cwd**: Рабочий каталог (например, папка проекта).
- **environment**: Переменные окружения для настройки среды выполнения.
- **externalConsole**: Использовать ли внешнюю консоль для вывода.