Композит **"Composite"**. Этот паттерн позволяет вам создавать структуры объектов в виде деревьев, где каждый узел может быть как сложным объектом (состоящим из других объектов), так и простым объектом (листом). Композит особенно полезен, когда вам нужно работать с иерархическими структурами данных.

### Представим, что у вас есть иерархия геометрических фигур.

1. Определите интерфейс компонента
```cpp
#include <iostream>

class Shape
{
public:
    virtual void draw() = 0;
    virtual ~Shape()    = default;
};
```
2. Создайте конкретные компоненты (листья)
```
class Circle : public Shape
{
public:
    void draw() override
    {
        std::cout << "Drawing a circle.\n";
    }
};

class Rectangle : public Shape
{
public:
    void draw() override
    {
        std::cout << "Drawing a rectangle.\n";
    }
};
```
3. Создайте композитный класс
```cpp
#include <memory>
#include <vector>

class CompositeShape : public Shape
{
private:
    std::vector<std::shared_ptr<Shape>> children;

public:
    void add(const std::shared_ptr<Shape>& shape)
    {
        children.push_back(shape);
    }

    void draw() override
    {
        for (const auto& child : children)
        {
            child->draw();
        }
    }
};
```
4. Использование паттерна Композит
```cpp
int main()
{
    auto circle1    = std::make_shared<Circle>();
    auto rectangle1 = std::make_shared<Rectangle>();

    auto composite1 = std::make_shared<CompositeShape>();
    composite1->add(circle1);
    composite1->add(rectangle1);

    auto composite2 = std::make_shared<CompositeShape>();
    composite2->add(composite1);
    composite2->add(std::make_shared<Circle>());

    composite2->draw();

    return 0;
}

### Объяснение кода:
- Интерфейс компонента: `Shape` определяет интерфейс для всех компонентов в дереве.
- Листовые компоненты: `Circle` и `Rectangle` реализуют интерфейс Shape и представляют собой листовые узлы.
- Композитный компонент: CompositeShape также реализует интерфейс Shape и может содержать другие компоненты, как листовые, так и композитные.

### Другая реализация 
Паттерн **"Composite"** (Компоновщик) в C++ используется для представления древовидных структур данных, таких как файловые системы, иерархии объектов или графы сцены в играх. Паттерн позволяет обрабатывать отдельные объекты и их группы одинаково, используя единый интерфейс.

### Основные элементы паттерна Composite

1. `Component`: Объявляет интерфейс для объектов в составе компоновщика.
2. `Leaf`: Представляет конечные объекты в составе компоновщика. Лист не имеет потомков.
3. `Composite`: Определяет поведение компонентов, имеющих потомков, и хранит их.
```cpp
#include <iostream>
#include <memory>
#include <vector>

// Интерфейс Component, объявляющий интерфейс для объектов в составе компоновщика
class Component
{
public:
    virtual ~Component() = default;
    virtual void operation() const = 0;
    virtual void add(std::shared_ptr<Component> component)
    {
    }
    virtual void remove(std::shared_ptr<Component> component)
    {
    }
    virtual std::shared_ptr<Component> getChild(int index)
    {
        return nullptr;
    }
};
```
Класс `Leaf`, представляющий конечные объекты в составе компоновщика
```cpp
class Leaf : public Component
{
public:
    void operation() const override
    {
        std::cout << "Leaf operation." << std::endl;
    }
};
```
Класс `Composite`, представляющий компоненты, которые имеют потомков
```cpp
class Composite : public Component
{
public:
    void operation() const override
    {
        std::cout << "Composite operation." << std::endl;
        for (const auto& child : children_)
        {
            child->operation();
        }
    }

    void add(std::shared_ptr<Component> component) override
    {
        children_.push_back(component);
    }

    void remove(std::shared_ptr<Component> component) override {
        children_.erase(std::remove(children_.begin(), children_.end(), component), children_.end());
    }

    std::shared_ptr<Component> getChild(int index) override
    {
        if (index < 0 || index >= children_.size())
        {
            return nullptr;
        }
        return children_[index];
    }

private:
    std::vector<std::shared_ptr<Component>> children_;
};
```
main():
```cpp
int main()
{
    // Создаем объект Composite и добавляем в него несколько Leaf
    std::shared_ptr<Component> composite = std::make_shared<Composite>();
    std::shared_ptr<Component> leaf1 = std::make_shared<Leaf>();
    std::shared_ptr<Component> leaf2 = std::make_shared<Leaf>();

    composite->add(leaf1);
    composite->add(leaf2);

    // Выполняем операцию на композитном объекте
    composite->operation();

    return 0;
}
```
### Описание:
1. `Component`: Абстрактный класс, объявляющий интерфейс для всех компонентов в составе компоновщика.
2. `Leaf`: Класс, представляющий конечные объекты, не имеющие потомков.
3. `Composite`: Класс, представляющий объекты, которые могут содержать другие компоненты (включая другие `Composite` и `Leaf`). Реализует методы для добавления, удаления и получения потомков.
4. `main()`: Создает композитный объект, добавляет в него несколько листьев и выполняет операцию на композитном объекте, что вызывает операцию на всех его потомках.

### Преимущества:
- Упрощает работу с древовидными структурами.
- Позволяет обрабатывать отдельные объекты и их группы одинаково.
- Упрощает добавление новых типов компонентов.

### Недостатки:
- Может усложнить код из-за добавления дополнительных классов.
- Могут возникнуть проблемы с управлением памятью и производительностью при использовании сложных структур.

Паттерн **Composite** особенно полезен, когда вам нужно создать сложные иерархические структуры объектов и обрабатывать их единообразно.