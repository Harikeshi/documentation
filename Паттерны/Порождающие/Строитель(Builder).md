Паттерн **"Builder"** (Строитель) в C++ используется для пошагового создания сложных объектов. Он разделяет процесс создания объекта на несколько этапов, позволяя изменять процесс создания без изменения представления объекта. Это особенно полезно, если объект имеет много параметров или сложную структуру. Вот пример реализации паттерна Builder на C++:
```cpp
#include <iostream>
#include <memory>
#include <string>

// Продукт, который будет строиться
class Product
{
public:
    void setPartA(const std::string &partA)
    {
        partA_ = partA;
    }
    void setPartB(const std::string &partB)
    {
        partB_ = partB;
    }
    void setPartC(const std::string &partC)
    {
        partC_ = partC;
    }

    void show() const
    {
        std::cout << "Product parts: " << partA_ << ", " << partB_ << ", " << partC_ << std::endl;
    }

private:
    std::string partA_;
    std::string partB_;
    std::string partC_;
};
```
Интерфейс строителя
```cpp
class Builder
{
public:
    virtual ~Builder() = default;
    virtual void buildPartA() = 0;
    virtual void buildPartB() = 0;
    virtual void buildPartC() = 0;
    virtual std::shared_ptr<Product> getResult() = 0;
};
```
Конкретный строитель
```cpp
class ConcreteBuilder : public Builder
{
public:
    ConcreteBuilder()
    {
        reset();
    }

    void buildPartA() override
    {
        product_->setPartA("Part A");
    }
    void buildPartB() override
    {
        product_->setPartB("Part B");
    }
    void buildPartC() override
    {
        product_->setPartC("Part C");
    }

    std::shared_ptr<Product> getResult() override
    {
        std::shared_ptr<Product> result = product_;
        reset();
        return result;
    }

private:
    void reset()
    {
        product_ = std::make_shared<Product>();
    }
    std::shared_ptr<Product> product_;
};
```
Директор, который управляет процессом строительства
```cpp
class Director
{
public:
    void setBuilder(std::shared_ptr<Builder> builder)
    {
        builder_ = builder;
    }

    void construct()
    {
        builder_->buildPartA();
        builder_->buildPartB();
        builder_->buildPartC();
    }

private:
    std::shared_ptr<Builder> builder_;
};
```
main():
```cpp
int main()
{
    Director director;

    std::shared_ptr<ConcreteBuilder> builder = std::make_shared<ConcreteBuilder>();
    director.setBuilder(builder);

    director.construct();
    std::shared_ptr<Product> product = builder->getResult();
    product->show();

    return 0;
}
```
### Описание:
1. `Product`: Класс, представляющий сложный объект, который будет создаваться.
2. `Builder`: Абстрактный интерфейс, определяющий этапы создания продукта.
3. `ConcreteBuilder`: Реализация интерфейса `Builder`, которая определяет конкретные шаги создания продукта.
4. `Director`: Класс, который управляет процессом строительства, определяя порядок вызова методов строителя.

### Преимущества:
- Разделение процесса создания объекта на отдельные шаги.
- Легкость изменения алгоритма создания объекта.
- Возможность повторного использования кода строителя для создания разных вариаций продукта.