Паттерн **"Prototype"** (Прототип) в C++ позволяет создавать новые объекты путем копирования существующих экземпляров, вместо создания объектов с нуля. Этот паттерн полезен, когда создание объектов является сложным или затратным процессом. Вот пример реализации паттерна **Prototype** на C++:

## Основные элементы паттерна Prototype

1. `Prototype`: Интерфейс, который определяет метод клонирования.
2. `ConcretePrototype`: Конкретная реализация прототипа, которая поддерживает клонирование.
3. `Client`: Использует прототипы для создания новых объектов.
```cpp
#include <iostream>
#include <memory>
#include <unordered_map>

// Интерфейс Prototype, определяющий метод clone()
class Prototype
{
public:
    virtual ~Prototype() = default;
    virtual std::unique_ptr<Prototype> clone() const = 0;
    virtual void show() const  = 0;
};
```
Конкретный прототип `1`
```cpp
class ConcretePrototype1 : public Prototype
{
public:
    ConcretePrototype1(const std::string& name)
        : name_(name)
    {
    }

    std::unique_ptr<Prototype> clone() const override
    {
        return std::make_unique<ConcretePrototype1>(*this);
    }

    void show() const override
    {
        std::cout << "ConcretePrototype1 with name: " << name_ << std::endl;
    }

private:
    std::string name_;
};
```
Конкретный прототип `2`
```cpp
class ConcretePrototype2 : public Prototype
{
public:
    ConcretePrototype2(int id)
        : id_(id)
    {
    }

    std::unique_ptr<Prototype> clone() const override
    {
        return std::make_unique<ConcretePrototype2>(*this);
    }

    void show() const override
    {
        std::cout << "ConcretePrototype2 with id: " << id_ << std::endl;
    }

private:
    int id_;
};
```
Клиентский код
```cpp
int main()
{
    // Создаем прототипы
    std::unique_ptr<Prototype> prototype1 = std::make_unique<ConcretePrototype1>("Prototype1");
    std::unique_ptr<Prototype> prototype2 = std::make_unique<ConcretePrototype2>(42);

    // Клонируем прототипы
    std::unique_ptr<Prototype> clone1 = prototype1->clone();
    std::unique_ptr<Prototype> clone2 = prototype2->clone();

    // Показываем клонированные объекты
    clone1->show(); // Output: ConcretePrototype1 with name: Prototype1
    clone2->show(); // Output: ConcretePrototype2 with id: 42

    return 0;
}
```
### Описание:
1. `Prototype`: Абстрактный класс, который определяет метод `clone()`.
2. `ConcretePrototype1` и `ConcretePrototype2`: Конкретные реализации прототипов, которые поддерживают клонирование.
3. `Client`: Клиентский код, который использует прототипы для создания новых объектов.

Преимущества:
- Снижение затрат на создание новых объектов.
- Упрощение процесса создания объектов с помощью клонирования.
- Возможность легкого добавления и изменения типов создаваемых объектов.

### Недостатки:
- Возможные сложности с клонированием объектов, содержащих сложные структуры данных.
- Увеличение использования памяти из-за хранения дополнительных прототипов.

Этот паттерн полезен, когда вы хотите избежать затрат на создание новых объектов с нуля и использовать уже существующие экземпляры в качестве шаблонов для клонирования.