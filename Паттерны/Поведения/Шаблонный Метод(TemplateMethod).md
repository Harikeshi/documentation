Паттерн **"Template Method"** (Шаблонный метод) в C++ используется для определения скелета алгоритма в методе базового класса, делегируя некоторые шаги подклассам. Этот паттерн позволяет подклассам изменять определённые шаги алгоритма, не изменяя структуру самого алгоритма.

### Основные элементы паттерна Template Method

1. `AbstractClass`: Абстрактный класс, содержащий шаблонный метод, который реализует скелет алгоритма. Некоторые шаги алгоритма делегируются абстрактным методам, которые должны быть реализованы в подклассах.
2. `ConcreteClass`: Конкретный класс, реализующий абстрактные методы, определённые в `AbstractClass`.

```cpp
#include <iostream>

// Абстрактный класс, содержащий шаблонный метод
class AbstractClass {
public:
    virtual ~AbstractClass() = default;

    // Шаблонный метод, который определяет скелет алгоритма
    void templateMethod() const {
        baseOperation1();
        requiredOperation1();
        baseOperation2();
        requiredOperation2();
        hook();
    }

protected:
    void baseOperation1() const {
        std::cout << "AbstractClass: Base operation 1\n";
    }

    void baseOperation2() const {
        std::cout << "AbstractClass: Base operation 2\n";
    }

    // Абстрактные методы, которые должны быть реализованы в подклассах
    virtual void requiredOperation1() const = 0;
    virtual void requiredOperation2() const = 0;

    // Метод-хук, который может быть переопределён в подклассах
    virtual void hook() const {}
};
```
Конкретный класс, реализующий абстрактные методы
```cpp
class ConcreteClass1 : public AbstractClass {
protected:
    void requiredOperation1() const override {
        std::cout << "ConcreteClass1: Implemented Operation 1\n";
    }

    void requiredOperation2() const override {
        std::cout << "ConcreteClass1: Implemented Operation 2\n";
    }
};
```
Другой конкретный класс, реализующий абстрактные методы
```cpp
class ConcreteClass2 : public AbstractClass {
protected:
    void requiredOperation1() const override {
        std::cout << "ConcreteClass2: Implemented Operation 1\n";
    }

    void requiredOperation2() const override {
        std::cout << "ConcreteClass2: Implemented Operation 2\n";
    }

    void hook() const override {
        std::cout << "ConcreteClass2: Overridden Hook\n";
    }
};
```
main():
```cpp
int main() {
    ConcreteClass1 concreteClass1;
    ConcreteClass2 concreteClass2;

    std::cout << "ConcreteClass1:\n";
    concreteClass1.templateMethod();

    std::cout << "\nConcreteClass2:\n";
    concreteClass2.templateMethod();

    return 0;
}
```
### Описание:
1. `AbstractClass`: Определяет шаблонный метод `templateMethod`, который реализует скелет алгоритма, и включает абстрактные методы `requiredOperation1` и `requiredOperation2`, которые должны быть реализованы в подклассах. Также включает базовые методы `baseOperation1` и `baseOperation2` и метод-хук `hook`, который может быть переопределён в подклассах.
2. `ConcreteClass1` и `ConcreteClass2`: Конкретные классы, реализующие абстрактные методы, определённые в `AbstractClass`, и, при необходимости, переопределяющие метод-хук.
3. `main()`: Клиентский код, который создаёт объекты конкретных классов и вызывает их шаблонные методы.

### Преимущества:
- Позволяет легко изменять части алгоритма, не изменяя его структуру.
- Содействует повторному использованию кода за счёт наследования и переопределения методов.
- Улучшает управление изменениями, предоставляя единый скелет алгоритма.

### Недостатки:
- Увеличивает сложность кода за счёт добавления абстрактных классов и методов.
- Жёсткие связи между базовым классом и подклассами могут привести к проблемам с поддерживаемостью и расширяемостью.

Паттерн **Template Method** особенно полезен, когда требуется создать семейство алгоритмов с общей структурой, где некоторые шаги могут различаться.