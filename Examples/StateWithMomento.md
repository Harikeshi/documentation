В паттерне **State** (Состояние) задача сохранения и восстановления последнего состояния может быть реализована с помощью **Memento** (Хранитель) — это дополнительный паттерн, который позволяет сохранять и восстанавливать состояние объекта без нарушения инкапсуляции.

Вот пример на C++, где используется паттерн **State** вместе с **Memento** для сохранения и восстановления состояния:

---

### Пример реализации с паттерном State и Memento

```cpp
#include <iostream>
#include <string>
#include <memory>

// Предварительное объявление классов
class State;
class ConcreteStateA;
class ConcreteStateB;

// Memento: хранит состояние объекта
class Memento {
private:
    std::string state_; // Состояние, которое нужно сохранить

public:
    Memento(const std::string& state) : state_(state) {}

    std::string getState() const {
        return state_;
    }
};

// Context: класс, который использует состояние
class Context {
private:
    std::shared_ptr<State> state_; // Текущее состояние
    std::string contextData_; // Данные контекста

public:
    Context(std::shared_ptr<State> state) : state_(state) {}

    void setState(std::shared_ptr<State> state) {
        state_ = state;
    }

    void setData(const std::string& data) {
        contextData_ = data;
    }

    std::string getData() const {
        return contextData_;
    }

    void request() {
        state_->handle(*this);
    }

    // Сохраняет текущее состояние в Memento
    std::shared_ptr<Memento> saveState() const {
        return std::make_shared<Memento>(contextData_);
    }

    // Восстанавливает состояние из Memento
    void restoreState(const std::shared_ptr<Memento>& memento) {
        contextData_ = memento->getState();
        std::cout << "Состояние восстановлено: " << contextData_ << std::endl;
    }
};

// State: интерфейс состояния
class State {
public:
    virtual void handle(Context& context) = 0;
    virtual ~State() = default;
};

// ConcreteStateA: конкретное состояние A
class ConcreteStateA : public State {
public:
    void handle(Context& context) override {
        std::cout << "Обработка в состоянии A" << std::endl;
        context.setData("Данные состояния A");
        context.setState(std::make_shared<ConcreteStateB>()); // Переход в состояние B
    }
};

// ConcreteStateB: конкретное состояние B
class ConcreteStateB : public State {
public:
    void handle(Context& context) override {
        std::cout << "Обработка в состоянии B" << std::endl;
        context.setData("Данные состояния B");
        context.setState(std::make_shared<ConcreteStateA>()); // Переход в состояние A
    }
};

int main() {
    // Создаем контекст с начальным состоянием A
    auto context = std::make_shared<Context>(std::make_shared<ConcreteStateA>());

    // Выполняем запрос и сохраняем состояние
    context->request();
    auto memento = context->saveState();

    // Выполняем еще один запрос (состояние изменится)
    context->request();

    // Восстанавливаем предыдущее состояние
    context->restoreState(memento);

    return 0;
}
```

---

### Объяснение работы:
1. **Context**:
   - Содержит текущее состояние (`State`) и данные (`contextData_`).
   - Методы `saveState` и `restoreState` позволяют сохранять и восстанавливать состояние с помощью **Memento**.

2. **State**:
   - Интерфейс для всех состояний. Каждое состояние реализует метод `handle`, который изменяет состояние контекста.

3. **ConcreteStateA и ConcreteStateB**:
   - Конкретные реализации состояний. В методе `handle` они изменяют данные контекста и переключают состояние.

4. **Memento**:
   - Хранит состояние контекста (в данном случае строку `contextData_`).

5. **Сохранение и восстановление**:
   - Перед изменением состояния вызывается `saveState`, который сохраняет текущие данные в объект **Memento**.
   - После изменения состояния можно восстановить предыдущее состояние с помощью `restoreState`.

---

### Вывод программы:
```
Обработка в состоянии A
Обработка в состоянии B
Состояние восстановлено: Данные состояния A
```

---

### Преимущества:
- **Инкапсуляция**: Состояние сохраняется и восстанавливается без раскрытия внутренней структуры объекта.
- **Гибкость**: Можно сохранять и восстанавливать состояние в любой момент.
- **Расширяемость**: Легко добавлять новые состояния и изменять логику.