### Пример файла `tasks.json` для C++:

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Build",
            "type": "shell",
            "command": "g++",
            "args": [
                "-g",
                "${workspaceFolder}/src/main.cpp",
                "-o",
                "${workspaceFolder}/bin/app"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "problemMatcher": ["$gcc"],
            "detail": "Task to build the project using g++"
        },
        {
            "label": "Run",
            "type": "shell",
            "command": "${workspaceFolder}/bin/app",
            "group": {
                "kind": "test",
                "isDefault": true
            },
            "problemMatcher": [],
            "detail": "Task to run the application"
        },
        {
            "label": "Clean",
            "type": "shell",
            "command": "rm",
            "args": [
                "-f",
                "${workspaceFolder}/bin/app"
            ],
            "group": "none",
            "problemMatcher": [],
            "detail": "Task to clean the project"
        },
        {
            "label": "Lint",
            "type": "shell",
            "command": "cppcheck",
            "args": [
                "--enable=all",
                "${workspaceFolder}/src"
            ],
            "group": "none",
            "problemMatcher": [
                {
                    "owner": "cppcheck",
                    "fileLocation": ["relative", "${workspaceFolder}"],
                    "pattern": {
                        "regexp": "^(.*):(\\d+):(\\d+):\\s+(error|warning|style):\\s+(.*)$",
                        "file": 1,
                        "line": 2,
                        "column": 3,
                        "severity": 4,
                        "message": 5
                    }
                }
            ],
            "detail": "Task to lint C++ files using cppcheck"
        },
        {
            "label": "Watch",
            "type": "shell",
            "command": "inotifywait",
            "args": [
                "-m",
                "-r",
                "-e",
                "modify",
                "${workspaceFolder}/src"
            ],
            "isBackground": true,
            "problemMatcher": [],
            "detail": "Task to watch for file changes"
        },
        {
            "label": "Debug Build",
            "type": "shell",
            "command": "g++",
            "args": [
                "-g",
                "-DDEBUG",
                "${workspaceFolder}/src/main.cpp",
                "-o",
                "${workspaceFolder}/bin/app-debug"
            ],
            "group": "build",
            "problemMatcher": ["$gcc"],
            "detail": "Task to build the project in debug mode"
        },
        {
            "label": "Release Build",
            "type": "shell",
            "command": "g++",
            "args": [
                "-O2",
                "-DNDEBUG",
                "${workspaceFolder}/src/main.cpp",
                "-o",
                "${workspaceFolder}/bin/app-release"
            ],
            "group": "build",
            "problemMatcher": ["$gcc"],
            "detail": "Task to build the project in release mode"
        }
    ]
}
```

### Описание задач:

1. **Build**: Сборка проекта с использованием команды `g++`.
2. **Run**: Запуск приложения.
3. **Clean**: Очистка проекта (удаление скомпилированного исполняемого файла).
4. **Lint**: Анализ кода (линтинг) с использованием инструмента `cppcheck`.
5. **Watch**: Наблюдение за изменениями в исходных файлах с использованием `inotifywait`.
6. **Debug Build**: Сборка проекта в режиме отладки с использованием `g++` и флага `-DDEBUG`.
7. **Release Build**: Сборка проекта в релизном режиме с оптимизацией с использованием `g++` и флага `-O2`.

### Дополнительные параметры:

- **group**: Группа задач (например, "build" или "test").
- **problemMatcher**: Сопоставители проблем для анализа вывода и отображения ошибок.
- **isBackground**: Выполнение задачи в фоновом режиме.
- **detail**: Дополнительная информация о задаче.