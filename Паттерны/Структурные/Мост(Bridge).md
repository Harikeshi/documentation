Паттерн Мост **"Bridge"** позволяет разделить классы на иерархии абстракций и реализации, что позволяет гибко вызывать конкретные классы из различных групп.

### Пример использования паттерна Мост (Bridge)

Рассмотрим пример с группами Форм и Цветов.

1. Определите интерфейс абстракции
```cpp
#include <iostream>
#include <memory>
#include <string>

class Color
{
public:
    virtual std::string getColor() = 0;
    virtual ~Color() = default;
};
```
2. Создайте конкретные реализации интерфейса абстракции
```cpp
class Red : public Color
{
public:
    std::string getColor() override
    {
        return "Red";
    }
};

class Blue : public Color
{
public:
    std::string getColor() override
    {
        return "Blue";
    }
};
```
3. Определите интерфейс абстракции для формы
```cpp
class Shape
{
protected:
    std::shared_ptr<Color> color;

public:
    Shape(std::shared_ptr<Color> c)
        : color(c)
    {
    }
    virtual void draw() = 0;
    virtual ~Shape() = default;
};
```
4. Создайте конкретные реализации абстракции формы
```cpp
class Circle : public Shape
{
public:
    Circle(std::shared_ptr<Color> c)
        : Shape(c)
    {
    }
    void draw() override
    {
        std::cout << "Drawing Circle in " << color->getColor() << " color.\n";
    }
};

class Square : public Shape
{
public:
    Square(std::shared_ptr<Color> c)
        : Shape(c)
    {
    }
    void draw() override
    {
        std::cout << "Drawing Square in " << color->getColor() << " color.\n";
    }
};
```
5. Использование паттерна Мост
```
int main()
{
    std::shared_ptr<Color> red = std::make_shared<Red>();
    std::shared_ptr<Color> blue = std::make_shared<Blue>();

    std::shared_ptr<Shape> redCircle = std::make_shared<Circle>(red);
    std::shared_ptr<Shape> blueSquare = std::make_shared<Square>(blue);

    redCircle->draw();
    blueSquare->draw();

    return 0;
}
```
### Объяснение кода:
- Абстракция Цвета: Color представляет интерфейс для различных цветов.
- Конкретные цвета: Red и Blue реализуют интерфейс Color.
- Абстракция Форма: Shape представляет абстрактный класс для всех форм, используя Color для определения цвета.
- Конкретные формы: Circle и Square реализуют абстрактный класс Shape.
