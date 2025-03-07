Паттерн **"Chain of Responsibility"** (Цепочка обязанностей) в C++ используется для передачи запроса вдоль цепочки обработчиков. Каждый обработчик решает, обработать запрос или передать его следующему обработчику в цепочке. Этот паттерн полезен, когда нужно избежать жесткой привязки отправителя запроса к его получателю, позволяя нескольким объектам обрабатывать запрос последовательно.

### Основные элементы паттерна Chain of Responsibility

1. `Handler`: Абстрактный класс или интерфейс, объявляющий метод для обработки запроса и ссылку на следующий обработчик в цепочке.
2. `ConcreteHandler`: Конкретные обработчики, которые обрабатывают запрос или передают его следующему обработчику.
3. `Client`: Отправляет запрос первому обработчику в цепочке.
```cpp
#include <iostream>
#include <memory>
#include <string>

// Абстрактный класс Handler, объявляющий метод для обработки запроса
class Handler {
public:
    virtual ~Handler() = default;

    void setNext(std::shared_ptr<Handler> nextHandler) {
        next_ = nextHandler;
    }

    virtual void handle(const std::string& request) {
        if (next_) {
            next_->handle(request);
        }
    }

protected:
    std::shared_ptr<Handler> next_;
};
```
Конкретный обработчик `1`
```cpp
class ConcreteHandler1 : public Handler {
public:
    void handle(const std::string& request) override {
        if (request == "Request1") {
            std::cout << "ConcreteHandler1 handled the request: " << request << std::endl;
        } else {
            std::cout << "ConcreteHandler1 passed the request: " << request << std::endl;
            Handler::handle(request);
        }
    }
};
```
Конкретный обработчик `2`
```cpp
class ConcreteHandler2 : public Handler {
public:
    void handle(const std::string& request) override {
        if (request == "Request2") {
            std::cout << "ConcreteHandler2 handled the request: " << request << std::endl;
        } else {
            std::cout << "ConcreteHandler2 passed the request: " << request << std::endl;
            Handler::handle(request);
        }
    }
};
```
Клиентский код, который создает цепочку обработчиков и отправляет запросы
```cpp
int main() {
    std::shared_ptr<ConcreteHandler1> handler1 = std::make_shared<ConcreteHandler1>();
    std::shared_ptr<ConcreteHandler2> handler2 = std::make_shared<ConcreteHandler2>();

    handler1->setNext(handler2);

    // Отправляем запросы первому обработчику в цепочке
    handler1->handle("Request1");
    handler1->handle("Request2");
    handler1->handle("Request3");

    return 0;
}
```
### Описание:
1. `Handler`: Абстрактный класс, объявляющий метод `handle` для обработки запроса и метод `setNext` для установки следующего обработчика в цепочке.
2. `ConcreteHandler1` и `ConcreteHandler2`: Конкретные обработчики, которые обрабатывают запрос или передают его следующему обработчику в цепочке.
3. `main()`: Клиентский код, который создает цепочку обработчиков и отправляет запросы первому обработчику.

### Преимущества:
- Ослабляет привязку между отправителем и получателем запроса.
- Упрощает добавление новых обработчиков в цепочку.
- Улучшает гибкость и управляемость обработкой запросов.

### Недостатки:
- Возможная сложность отладки из-за динамического принятия решений о передаче запроса.
- Может привести к тому, что запрос останется необработанным, если ни один из обработчиков не примет его.

Паттерн **Chain of Responsibility** особенно полезен, когда необходимо обрабатывать запросы гибким и расширяемым способом.