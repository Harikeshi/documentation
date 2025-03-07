### Пример файла `launch.json` для C++ с различными конфигурациями:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch with Debugger",
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
            }
        },
        {
            "name": "Launch without Debugger",
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
            "debugger": null
        },
        {
            "name": "Attach to Process",
            "type": "cppdbg",
            "request": "attach",
            "program": "${workspaceFolder}/a.out",
            "processId": "${command:pickProcess}",
            "MIMode": "gdb",
            "miDebuggerServerAddress": "localhost:1234",
            "miDebuggerPath": "/usr/bin/gdb",
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

### Описание конфигураций:

1. **Launch with Debugger**: Запуск программы с отладчиком.
   - **MIMode: "gdb"** указывает на использование GDB для отладки.
   - **preLaunchTask** запускает задачу сборки перед отладкой.
   - **externalConsole: true** использует внешнюю консоль для вывода.

2. **Launch without Debugger**: Запуск программы без отладчика.
   - **debugger: null** указывает на отсутствие подключения отладчика.

3. **Attach to Process**: Подключение к уже запущенному процессу.
   - **processId: "${command:pickProcess}"** позволяет выбрать процесс для подключения.
   - **miDebuggerServerAddress** указывает на адрес и порт сервера отладчика.

### Дополнительные условия:

- **setupCommands**: Команды для настройки отладчика, такие как включение pretty-printing.
- **sourceFileMap**: Карта путей к исходным файлам для отображения исходников (например, сопоставление путей в контейнере с локальными путями).
- **stopAtEntry**: Останавливать ли выполнение при входе в программу.
- **cwd**: Рабочий каталог (например, папка проекта).
- **environment**: Переменные окружения для настройки среды выполнения.
- **externalConsole**: Использовать ли внешнюю консоль для вывода.

Эти настройки позволяют гибко управлять процессом запуска и отладки приложения на C++ в Visual Studio Code. Если у вас есть дополнительные вопросы или требования, дайте знать!