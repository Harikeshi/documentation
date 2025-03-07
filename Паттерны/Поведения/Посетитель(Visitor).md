Паттерн **"Visitor"** (Посетитель) в C++ позволяет добавлять новые операции к объектам без изменения их классов. Этот паттерн разделяет алгоритмы и структуру данных, предоставляя способ добавления новых операций, не изменяя сами классы, к которым эти операции применяются.

### Основные элементы паттерна Visitor

1. `Visitor`: Интерфейс или абстрактный класс, объявляющий методы для посещения каждого типа элемента.
2. `ConcreteVisitor`: Конкретные реализации интерфейса `Visitor`, реализующие конкретные операции для каждого типа элемента.
3. `Element`: Интерфейс или абстрактный класс, объявляющий метод `accept()`, который принимает объект `Visitor`.
4. `ConcreteElement`: Конкретные реализации интерфейса `Element`, которые реализуют метод accept.
5. `Client`: Клиентский код, который создает объекты `Visitor` и `Element`, и вызывает метод `accept()`.
```cpp
#include <iostream>
#include <vector>
#include <memory>

// Интерфейс Visitor
class Visitor {
public:
    virtual ~Visitor() = default;
    virtual void visit(class ConcreteElementA* element) = 0;
    virtual void visit(class ConcreteElementB* element) = 0;
};

// Интерфейс Element
class Element {
public:
    virtual ~Element() = default;
    virtual void accept(Visitor* visitor) = 0;
};
```
Конкретный элемент `A`
```cpp
class ConcreteElementA : public Element {
public:
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }

    void operationA() const {
        std::cout << "ConcreteElementA operation\n";
    }
};
```
Конкретный элемент `B`
```cpp
class ConcreteElementB : public Element {
public:
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }

    void operationB() const {
        std::cout << "ConcreteElementB operation\n";
    }
};
```
Конкретный `Visitor`, реализующий операции для каждого элемента
```cpp
class ConcreteVisitor : public Visitor {
public:
    void visit(ConcreteElementA* element) override {
        std::cout << "ConcreteVisitor visiting ConcreteElementA\n";
        element->operationA();
    }

    void visit(ConcreteElementB* element) override {
        std::cout << "ConcreteVisitor visiting ConcreteElementB\n";
        element->operationB();
    }
};
```
main():
```cpp
int main() {
    std::vector<std::shared_ptr<Element>> elements = {
        std::make_shared<ConcreteElementA>(),
        std::make_shared<ConcreteElementB>()
    };

    ConcreteVisitor visitor;

    for (const auto& element : elements) {
        element->accept(&visitor);
    }

    return 0;
}
```
### Описание:
1. `Visitor`: Интерфейс, объявляющий методы `visit` для каждого типа элемента (например, `ConcreteElementA` и `ConcreteElementB`).
2. `ConcreteVisitor`: Конкретная реализация Visitor, которая реализует операции для каждого типа элемента.
3. `Element`: Интерфейс, объявляющий метод `accept`, который принимает объект `Visitor`.
4. `ConcreteElementA` и `ConcreteElementB`: Конкретные реализации Element, которые реализуют метод accept и вызывают соответствующий метод `visit` объекта `Visitor`.
5. `main()`: Клиентский код, который создает объекты элементов и посетителей, и вызывает метод accept на каждом элементе, передавая объект `Visitor`.

### Преимущества:
- Позволяет добавлять новые операции без изменения классов, к которым эти операции применяются.
- Упрощает добавление новых операций.
- Разделяет алгоритмы и структуру данных.

### Недостатки:
- Увеличивает сложность кода из-за добавления дополнительных классов и методов.
- Может нарушить принцип инкапсуляции, так как классы элементов должны раскрывать свои внутренние данные методам посетителя.

Паттерн **Visitor** особенно полезен, когда вам нужно выполнять различные операции над объектами сложной структуры данных, не изменяя их классы.