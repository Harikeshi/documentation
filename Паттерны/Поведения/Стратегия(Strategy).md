Паттерн **"Strategy"** (Стратегия) в C++ используется для определения набора алгоритмов, инкапсуляции каждого из них и обеспечения их взаимозаменяемости. Этот паттерн позволяет выбирать алгоритм поведения на этапе выполнения, не изменяя классы, которые используют этот алгоритм.

### Основные элементы паттерна Strategy

1. `Strategy`: Интерфейс или абстрактный класс, объявляющий метод, который должен реализовать конкретная стратегия.
2. `ConcreteStrategy`: Конкретные реализации интерфейса `Strategy`, представляющие различные алгоритмы.
3. `Context`: Класс, который использует объекты `Strategy` для выполнения конкретного алгоритма.
```cpp
#include <iostream>
#include <memory>

// Интерфейс Strategy, объявляющий метод алгоритма
class Strategy {
public:
    virtual ~Strategy() = default;
    virtual void execute() const = 0;
};
```
Конкретные стратегии, реализующие различные алгоритмы
```cpp
class ConcreteStrategyA : public Strategy {
public:
    void execute() const override {
        std::cout << "Executing Strategy A\n";
    }
};

class ConcreteStrategyB : public Strategy {
public:
    void execute() const override {
        std::cout << "Executing Strategy B\n";
    }
};
```
Класс `Context`, который использует объекты `Strategy`
```cpp
class Context {
public:
    void setStrategy(std::shared_ptr<Strategy> strategy) {
        strategy_ = strategy;
    }

    void executeStrategy() const {
        if (strategy_) {
            strategy_->execute();
        } else {
            std::cerr << "No strategy set\n";
        }
    }

private:
    std::shared_ptr<Strategy> strategy_;
};
```
main():
```cpp
int main() {
    Context context;

    std::shared_ptr<Strategy> strategyA = std::make_shared<ConcreteStrategyA>();
    std::shared_ptr<Strategy> strategyB = std::make_shared<ConcreteStrategyB>();

    // Используем стратегию A
    context.setStrategy(strategyA);
    context.executeStrategy();

    // Используем стратегию B
    context.setStrategy(strategyB);
    context.executeStrategy();

    return 0;
}
```
### Описание:
1. `Strategy`: Абстрактный класс, объявляющий метод execute(), который реализуют конкретные стратегии.
2. `ConcreteStrategyA` и `ConcreteStrategyB`: Конкретные стратегии, реализующие различные алгоритмы.
3. `Context`: Класс, который использует объекты `Strategy` для выполнения алгоритмов. Он содержит метод `setStrategy()` для установки текущей стратегии и метод `executeStrategy()` для выполнения установленной стратегии.
4. `main()`: Клиентский код, который демонстрирует установку и выполнение различных стратегий.

### Преимущества:
- Позволяет легко менять алгоритмы на этапе выполнения.
- Упрощает добавление новых алгоритмов без изменения существующего кода.
- Снижает зависимость между классами и алгоритмами.

### Недостатки:
- Может усложнить код за счет увеличения количества классов.
- Частое переключение стратегий может привести к снижению производительности.

Паттерн **Strategy** особенно полезен, когда требуется выбрать один из нескольких алгоритмов на этапе выполнения и обеспечить возможность легкого добавления новых алгоритмов.