Рассмотрим пример использования паттерна **Состояние** для управления отрисовкой пути в приложении. Путь может находиться в разных состояниях: **активная отрисовка**, **пауза** и **перезапуск отрисовки с начала**. В каждом состоянии поведение отрисовки будет разным.

### Реализация на C++

```cpp
#include <iostream>
#include <memory>
#include <vector>

// Абстрактное состояние отрисовки
class DrawingState {
public:
    virtual void draw(std::vector<std::pair<int, int>>& path) = 0;
    virtual ~DrawingState() = default;
};

// Конкретное состояние: Активная отрисовка
class ActiveDrawingState : public DrawingState {
public:
    void draw(std::vector<std::pair<int, int>>& path) override {
        std::cout << "Отрисовка пути: ";
        for (const auto& point : path) {
            std::cout << "(" << point.first << ", " << point.second << ") ";
        }
        std::cout << std::endl;
    }
};

// Конкретное состояние: Пауза
class PausedDrawingState : public DrawingState {
public:
    void draw(std::vector<std::pair<int, int>>& path) override {
        std::cout << "Отрисовка на паузе. Текущий путь: ";
        for (const auto& point : path) {
            std::cout << "(" << point.first << ", " << point.second << ") ";
        }
        std::cout << std::endl;
    }
};

// Конкретное состояние: Перезапуск отрисовки
class RestartDrawingState : public DrawingState {
public:
    void draw(std::vector<std::pair<int, int>>& path) override {
        path.clear(); // Очищаем путь
        std::cout << "Отрисовка начинается с начала. Путь очищен." << std::endl;
    }
};

// Контекст: Отрисовка пути
class PathDrawer {
private:
    std::unique_ptr<DrawingState> state;
    std::vector<std::pair<int, int>> path;

public:
    PathDrawer(std::unique_ptr<DrawingState> initialState) : state(std::move(initialState)) {}

    void setState(std::unique_ptr<DrawingState> newState) {
        state = std::move(newState);
    }

    void draw() {
        state->draw(path);
    }

    void addPoint(int x, int y) {
        path.emplace_back(x, y);
    }
};

int main() {
    // Создаем объект отрисовки с начальным состоянием "активная отрисовка"
    PathDrawer drawer(std::make_unique<ActiveDrawingState>());

    // Добавляем точки пути
    drawer.addPoint(1, 1);
    drawer.addPoint(2, 2);
    drawer.addPoint(3, 3);

    // Имитируем управление отрисовкой
    while (true) {
        std::cout << "Выберите действие (1 - Активная отрисовка, 2 - Пауза, 3 - Перезапуск, 0 - Выход): ";
        int choice;
        std::cin >> choice;

        if (choice == 0) break;

        switch (choice) {
            case 1:
                drawer.setState(std::make_unique<ActiveDrawingState>());
                break;
            case 2:
                drawer.setState(std::make_unique<PausedDrawingState>());
                break;
            case 3:
                drawer.setState(std::make_unique<RestartDrawingState>());
                break;
            default:
                std::cout << "Неверный выбор. Попробуйте снова." << std::endl;
                break;
        }

        // Отрисовываем путь в текущем состоянии
        drawer.draw();
    }

    return 0;
}
```

### Как это работает:

1. **DrawingState** — это абстрактный класс, который определяет интерфейс для всех состояний отрисовки. Каждое состояние реализует метод `draw()`, который отвечает за отрисовку пути.

2. **ActiveDrawingState**, **PausedDrawingState**, **RestartDrawingState** — это конкретные классы состояний, которые реализуют поведение отрисовки в каждом из состояний:
   - **ActiveDrawingState**: Отрисовывает текущий путь.
   - **PausedDrawingState**: Показывает текущий путь, но не изменяет его.
   - **RestartDrawingState**: Очищает путь и начинает отрисовку с начала.

3. **PathDrawer** — это контекст, который содержит текущее состояние и путь. Метод `draw()` вызывает соответствующий метод текущего состояния.

4. В `main()` создается объект `PathDrawer` с начальным состоянием "активная отрисовка". Пользователь может выбирать новое состояние, и отрисовка будет меняться в зависимости от выбранного состояния.

### Пример вывода программы:

```
Выберите действие (1 - Активная отрисовка, 2 - Пауза, 3 - Перезапуск, 0 - Выход): 1
Отрисовка пути: (1, 1) (2, 2) (3, 3) 
Выберите действие (1 - Активная отрисовка, 2 - Пауза, 3 - Перезапуск, 0 - Выход): 2
Отрисовка на паузе. Текущий путь: (1, 1) (2, 2) (3, 3) 
Выберите действие (1 - Активная отрисовка, 2 - Пауза, 3 - Перезапуск, 0 - Выход): 3
Отрисовка начинается с начала. Путь очищен.
Выберите действие (1 - Активная отрисовка, 2 - Пауза, 3 - Перезапуск, 0 - Выход): 1
Отрисовка пути: 
Выберите действие (1 - Активная отрисовка, 2 - Пауза, 3 - Перезапуск, 0 - Выход): 0
```

### Преимущества паттерна:

1. **Упрощение кода**: Логика каждого состояния изолирована в отдельном классе, что делает код более читаемым и поддерживаемым.
2. **Гибкость**: Добавление новых состояний не требует изменения существующего кода контекста.
3. **Управление сложными переходами**: Паттерн позволяет легко управлять переходами между состояниями, особенно если они зависят от множества условий.