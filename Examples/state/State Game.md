Рассмотрим пример использования паттерна **Состояние** для управления поведением персонажа в игре. Персонаж может находиться в разных состояниях, таких как **стояние**, **бег**, **прыжок** и **атака**. В каждом состоянии поведение персонажа будет разным.

### Реализация на C++

```cpp
#include <iostream>
#include <memory>

// Абстрактное состояние персонажа
class CharacterState {
public:
    virtual void handleInput() = 0;
    virtual void update() = 0;
    virtual ~CharacterState() = default;
};

// Конкретное состояние: Стояние
class StandingState : public CharacterState {
public:
    void handleInput() override {
        std::cout << "Персонаж стоит. Нажмите клавишу для перехода в другое состояние." << std::endl;
    }

    void update() override {
        std::cout << "Персонаж продолжает стоять." << std::endl;
    }
};

// Конкретное состояние: Бег
class RunningState : public CharacterState {
public:
    void handleInput() override {
        std::cout << "Персонаж бежит. Нажмите клавишу для перехода в другое состояние." << std::endl;
    }

    void update() override {
        std::cout << "Персонаж продолжает бежать." << std::endl;
    }
};

// Конкретное состояние: Прыжок
class JumpingState : public CharacterState {
public:
    void handleInput() override {
        std::cout << "Персонаж прыгает. Нажмите клавишу для перехода в другое состояние." << std::endl;
    }

    void update() override {
        std::cout << "Персонаж продолжает прыжок." << std::endl;
    }
};

// Конкретное состояние: Атака
class AttackingState : public CharacterState {
public:
    void handleInput() override {
        std::cout << "Персонаж атакует. Нажмите клавишу для перехода в другое состояние." << std::endl;
    }

    void update() override {
        std::cout << "Персонаж продолжает атаку." << std::endl;
    }
};

// Контекст: Персонаж
class Character {
private:
    std::unique_ptr<CharacterState> state;

public:
    Character(std::unique_ptr<CharacterState> initialState) : state(std::move(initialState)) {}

    void setState(std::unique_ptr<CharacterState> newState) {
        state = std::move(newState);
    }

    void handleInput() {
        state->handleInput();
    }

    void update() {
        state->update();
    }
};

int main() {
    // Создаем персонажа с начальным состоянием "стояние"
    Character character(std::make_unique<StandingState>());

    // Имитируем игровой цикл
    while (true) {
        character.handleInput();
        character.update();

        // Пример переключения состояний
        std::cout << "Выберите новое состояние (1 - Бег, 2 - Прыжок, 3 - Атака, 0 - Выход): ";
        int choice;
        std::cin >> choice;

        if (choice == 0) break;

        switch (choice) {
            case 1:
                character.setState(std::make_unique<RunningState>());
                break;
            case 2:
                character.setState(std::make_unique<JumpingState>());
                break;
            case 3:
                character.setState(std::make_unique<AttackingState>());
                break;
            default:
                std::cout << "Неверный выбор. Попробуйте снова." << std::endl;
                break;
        }
    }

    return 0;
}
```

### Как это работает:

1. **CharacterState** — это абстрактный класс, который определяет интерфейс для всех состояний. Каждое состояние реализует методы `handleInput()` и `update()`.

2. **StandingState**, **RunningState**, **JumpingState**, **AttackingState** — это конкретные классы состояний, которые реализуют поведение персонажа в каждом из состояний.

3. **Character** — это контекст, который содержит текущее состояние и делегирует ему выполнение операций. Методы `handleInput()` и `update()` вызывают соответствующие методы текущего состояния.

4. В `main()` создается объект персонажа с начальным состоянием "стояние". В игровом цикле пользователь может выбирать новое состояние, и персонаж будет менять свое поведение в зависимости от выбранного состояния.

### Пример вывода программы:

```
Персонаж стоит. Нажмите клавишу для перехода в другое состояние.
Персонаж продолжает стоять.
Выберите новое состояние (1 - Бег, 2 - Прыжок, 3 - Атака, 0 - Выход): 1
Персонаж бежит. Нажмите клавишу для перехода в другое состояние.
Персонаж продолжает бежать.
Выберите новое состояние (1 - Бег, 2 - Прыжок, 3 - Атака, 0 - Выход): 2
Персонаж прыгает. Нажмите клавишу для перехода в другое состояние.
Персонаж продолжает прыжок.
Выберите новое состояние (1 - Бег, 2 - Прыжок, 3 - Атака, 0 - Выход): 3
Персонаж атакует. Нажмите клавишу для перехода в другое состояние.
Персонаж продолжает атаку.
Выберите новое состояние (1 - Бег, 2 - Прыжок, 3 - Атака, 0 - Выход): 0
```

### Преимущества паттерна:

1. **Упрощение кода**: Логика каждого состояния изолирована в отдельном классе, что делает код более читаемым и поддерживаемым.
2. **Гибкость**: Добавление новых состояний не требует изменения существующего кода контекста.
3. **Управление сложными переходами**: Паттерн позволяет легко управлять переходами между состояниями, особенно если они зависят от множества условий.