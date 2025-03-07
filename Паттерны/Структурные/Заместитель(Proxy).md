Паттерн **"Proxy"** (Заместитель) в C++ предоставляет замену другому объекту для контроля доступа к нему. Прокси обычно используется для выполнения дополнительных действий при доступе к объекту, таких как ленивое создание, кэширование, контроль доступа и логирование.

### Основные элементы паттерна Proxy

1. `Subject`: Интерфейс или абстрактный класс, объявляющий общие для реального объекта и его заместителя методы.
2. `RealSubject`: Реальный объект, который прокси заменяет.
3. `Proxy`: Класс заместителя, который управляет доступом к реальному объекту.
```cpp
#include <iostream>
#include <memory>

// Интерфейс Subject, объявляющий метод request()
class Subject
{
public:
    virtual ~Subject() = default;
    virtual void request() const = 0;
};
```
Реальный объект `RealSubject`, который выполняет основную работу
```cpp
class RealSubject : public Subject
{
public:
    void request() const override
    {
        std::cout << "RealSubject: Handling request.\n";
    }
};
```
Класс `Proxy`, который управляет доступом к `RealSubject`
```cpp
class Proxy : public Subject
{
public:
    Proxy(std::shared_ptr<RealSubject> realSubject)
        : realSubject_(realSubject)
    {
    }

    void request() const override
    {
        // Дополнительная логика до вызова реального объекта
        if (checkAccess())
        {
            realSubject_->request();
            logAccess();
        }
    }

private:
    std::shared_ptr<RealSubject> realSubject_;

    bool checkAccess() const
    {
        // Проверка доступа (например, проверка прав пользователя)
        std::cout << "Proxy: Checking access prior to firing a real request.\n";
        return true;
    }

    void logAccess() const
    {
        // Логирование доступа (например, запись времени вызова)
        std::cout << "Proxy: Logging the time of request.\n";
    }
};
```
main():
```cpp
int main()
{
    std::shared_ptr<RealSubject> realSubject = std::make_shared<RealSubject>();
    Proxy proxy(realSubject);

    // Клиентский код использует Proxy для доступа к RealSubject
    proxy.request();

    return 0;
}
```
### Описание:
1. `Subject`: Абстрактный класс, объявляющий метод `request()`, который реализуют как `RealSubject`, так и `Proxy`.
2. `RealSubject`: Класс реального объекта, который выполняет основную работу.
3. `Proxy`: Класс, который управляет доступом к `RealSubject`. Он может выполнять дополнительные действия до и после вызова реального объекта, такие как проверка доступа и логирование.
4. `main()`: Клиентский код, использующий прокси для доступа к реальному объекту.

 Преимущества:
- Контроль доступа к объекту.
- Ленивая инициализация (создание объекта только при необходимости).
- Кэширование результатов для повышения производительности.
- Логирование запросов и другие дополнительные действия.

### Недостатки:
- Увеличение сложности кода из-за дополнительного уровня абстракции.
- Может привести к проблемам с производительностью при ненадлежащем использовании.

Паттерн **Proxy** особенно полезен, когда необходимо контролировать доступ к объектам и выполнять дополнительные действия перед или после взаимодействия с ними.