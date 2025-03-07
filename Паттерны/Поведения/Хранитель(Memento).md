Паттерн **"Memento"** (Хранитель) в C++ используется для сохранения и восстановления состояния объекта без нарушения его инкапсуляции. Он позволяет откатить объект к предыдущему состоянию, что полезно для реализации функций отмены и повторения операций.

### Основные элементы паттерна Memento

1. `Memento`: Класс, представляющий сохраненное состояние объекта.
2. `Originator`: Класс, создающий и использующий объекты `Memento` для сохранения и восстановления своего состояния.
3. `Caretaker`: Класс, управляющий объектами `Memento` и не имеющий доступа к их внутреннему содержимому.
```cpp
#include <iostream>
#include <string>
#include <memory>
#include <stack>

// Класс Memento, представляющий сохраненное состояние
class Memento {
public:
    Memento(const std::string& state) : state_(state) {}

    std::string getState() const {
        return state_;
    }

private:
    std::string state_;
};
```
Класс `Originator`, создающий и использующий объекты Memento
```cpp
class Originator {
public:
    void setState(const std::string& state) {
        std::cout << "Setting state to: " << state << std::endl;
        state_ = state;
    }

    std::string getState() const {
        return state_;
    }

    std::shared_ptr<Memento> saveStateToMemento() const {
        return std::make_shared<Memento>(state_);
    }

    void restoreStateFromMemento(const std::shared_ptr<Memento>& memento) {
        state_ = memento->getState();
        std::cout << "Restoring state to: " << state_ << std::endl;
    }

private:
    std::string state_;
};
```
Класс `Caretaker`, управляющий объектами `Memento`
```cpp
class Caretaker {
public:
    void saveMemento(const std::shared_ptr<Memento>& memento) {
        mementos_.push(memento);
    }

    std::shared_ptr<Memento> getMemento() {
        if (!mementos_.empty()) {
            auto memento = mementos_.top();
            mementos_.pop();
            return memento;
        }
        return nullptr;
    }

private:
    std::stack<std::shared_ptr<Memento>> mementos_;
};
```
main():
```cpp
int main() {
    Originator originator;
    Caretaker caretaker;

    originator.setState("State1");
    caretaker.saveMemento(originator.saveStateToMemento());

    originator.setState("State2");
    caretaker.saveMemento(originator.saveStateToMemento());

    originator.setState("State3");

    // Восстанавливаем предыдущее состояние
    originator.restoreStateFromMemento(caretaker.getMemento());
    // Восстанавливаем еще одно предыдущее состояние
    originator.restoreStateFromMemento(caretaker.getMemento());

    return 0;
}
```
### Описание:
1. `Memento`: Класс, представляющий сохраненное состояние объекта. Он предоставляет метод для получения состояния, но не раскрывает его внутренние детали.
2. `Originator`: Класс, который создает объект `Memento` для сохранения своего состояния и использует его для восстановления состояния.
3. `Caretaker`: Класс, который управляет объектами `Memento`, сохраняя и восстанавливая их, но не имея доступа к их внутреннему содержимому.
4. `main()`: Клиентский код, который демонстрирует создание, сохранение и восстановление состояния объекта с использованием паттерна `Memento`.

### Преимущества:
- Сохраняет инкапсуляцию объекта, не раскрывая его внутреннее состояние.
- Упрощает реализацию функций отмены и повторения операций.
- Повышает гибкость и расширяемость кода.

### Недостатки:
- Могут возникнуть проблемы с производительностью и потреблением памяти при частом сохранении и восстановлении состояния.
- Сложность реализации для объектов с большим количеством состояний.

Паттерн `Memento` особенно полезен для сохранения истории изменений объекта и реализации функций отмены действий.