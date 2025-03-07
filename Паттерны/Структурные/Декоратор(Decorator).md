Паттерн **"Decorator"** (Декоратор) в C++ позволяет динамически добавлять объектам новые поведения, не изменяя их код. Это достигается путем создания оберток (декораторов) вокруг объектов, которые могут добавлять свои собственные методы и свойства, а также делегировать вызовы основному объекту.

### Основные элементы паттерна Decorator

1. `Component`: Интерфейс или абстрактный класс, объявляющий методы, которые будут реализованы как объектами, так и их декораторами.
2. `ConcreteComponent`: Конкретная реализация `Component`, к которой будут добавляться новые поведения.
3. `Decorator`: Абстрактный класс, реализующий интерфейс `Component` и имеющий ссылку на объект `Component`. Декоратор делегирует вызовы основному объекту и может добавлять свои собственные поведения.
4. `ConcreteDecorator`: Конкретные реализации `Decorator`, добавляющие новые поведения.
```cpp
#include <iostream>
#include <memory>

// Интерфейс Component, объявляющий метод operation()
class Component
{
public:
    virtual ~Component() = default;
    virtual void operation() const = 0;
};
```
Конкретный компонент, к которому будут добавляться новые поведения
```cpp
class ConcreteComponent : public Component
{
public:
    void operation() const override
    {
        std::cout << "ConcreteComponent operation" << std::endl;
    }
};
```
Абстрактный декоратор, реализующий интерфейс `Component` и имеющий ссылку на объект `Component`
```cpp
class Decorator : public Component
{
public:
    Decorator(std::shared_ptr<Component> component)
        : component_(component)
    {
    }

    void operation() const override
    {
        component_->operation();
    }

protected:
    std::shared_ptr<Component> component_;
};
```
Конкретный декоратор, добавляющий новое поведение
```cpp
class ConcreteDecoratorA : public Decorator
{
public:
    ConcreteDecoratorA(std::shared_ptr<Component> component)
        : Decorator(component)
    {
    }

    void operation() const override
    {
        Decorator::operation(); // Вызов метода базового класса
        additionalBehavior();   // Добавление нового поведения
    }

    void additionalBehavior() const
    {
        std::cout << "ConcreteDecoratorA additional behavior" << std::endl;
    }
};
```
Еще один конкретный декоратор, добавляющий другое новое поведение
```cpp
class ConcreteDecoratorB : public Decorator
{
public:
    ConcreteDecoratorB(std::shared_ptr<Component> component)
        : Decorator(component)
    {
    }

    void operation() const override
    {
        Decorator::operation(); // Вызов метода базового класса
        additionalBehavior();   // Добавление нового поведения
    }

    void additionalBehavior() const
    {
        std::cout << "ConcreteDecoratorB additional behavior" << std::endl;
    }
};
```
main():
```cpp
int main()
{
    std::shared_ptr<Component> component  = std::make_shared<ConcreteComponent>();
    std::shared_ptr<Component> decoratorA = std::make_shared<ConcreteDecoratorA>(component);
    std::shared_ptr<Component> decoratorB = std::make_shared<ConcreteDecoratorB>(decoratorA);

    decoratorB->operation();

    return 0;
}
```
### Описание:
1. `Component`: Абстрактный класс, объявляющий метод operation, который будет реализован компонентами и декораторами.
2. `ConcreteComponent`: Конкретная реализация компонента, к которой будут добавляться новые поведения.
3. `Decorator`: Абстрактный декоратор, реализующий интерфейс `Component` и имеющий ссылку на объект Component. Декоратор делегирует вызовы основному объекту и может добавлять свои собственные поведения.
4. `ConcreteDecoratorA` и `ConcreteDecoratorB`: Конкретные декораторы, добавляющие новые поведения.

### Преимущества:
- Позволяет динамически добавлять новые поведения объектам.
- Снижается необходимость создания множества подклассов для добавления новых функций.
- Повышается гибкость и расширяемость кода.

### Недостатки:
- Увеличивает количество объектов и может усложнить код.
- Может привести к трудностям в отладке из-за множества оберток вокруг исходного объекта.

Паттерн **Decorator** особенно полезен, когда требуется добавлять новые поведения объектам без изменения их кода, предоставляя гибкость и расширяемость.