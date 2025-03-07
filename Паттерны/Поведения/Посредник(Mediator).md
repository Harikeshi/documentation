Паттерн **"Mediator"** (Посредник) в C++ используется для снижения прямых зависимостей между объектами, что облегчает их взаимодействие и поддерживаемость. Посредник управляет коммуникацией между объектами, минимизируя необходимость каждого объекта знать о других объектах.

### Основные элементы паттерна Mediator

1. Mediator: Интерфейс, определяющий метод для обмена сообщениями между участниками.
2. ConcreteMediator: Конкретная реализация посредника, управляющего обменом сообщениями между объектами.
3. Colleague: Интерфейс или абстрактный класс, представляющий участников, которые общаются через посредника.
4. ConcreteColleague: Конкретные реализации коллег, которые взаимодействуют друг с другом через посредника.
```cpp
#include <iostream>
#include <string>
#include <memory>
#include <vector>

// Интерфейс Mediator, определяющий метод для отправки сообщений
class Mediator {
public:
    virtual ~Mediator() = default;
    virtual void send(const std::string& message, class Colleague* colleague) const = 0;
};
```
Интерфейс `Colleague`, представляющий участника
```cpp
class Colleague {
public:
    Colleague(std::shared_ptr<Mediator> mediator) : mediator_(mediator) {}
    virtual ~Colleague() = default;
    virtual void receive(const std::string& message) const = 0;
    void send(const std::string& message) const {
        mediator_->send(message, this);
    }

protected:
    std::shared_ptr<Mediator> mediator_;
};
```
Конкретный `Colleague1`
```cpp
class ConcreteColleague1 : public Colleague {
public:
    ConcreteColleague1(std::shared_ptr<Mediator> mediator) : Colleague(mediator) {}

    void receive(const std::string& message) const override {
        std::cout << "ConcreteColleague1 received: " << message << std::endl;
    }
};
```
Конкретный `Colleague2`
```cpp
class ConcreteColleague2 : public Colleague {
public:
    ConcreteColleague2(std::shared_ptr<Mediator> mediator) : Colleague(mediator) {}

    void receive(const std::string& message) const override {
        std::cout << "ConcreteColleague2 received: " << message << std::endl;
    }
};
```
Конкретный `Mediator`, управляющий коммуникацией между `Colleague`
```cpp
class ConcreteMediator : public Mediator {
public:
    void addColleague(std::shared_ptr<Colleague> colleague) {
        colleagues_.push_back(colleague);
    }

    void send(const std::string& message, Colleague* sender) const override {
        for (const auto& colleague : colleagues_) {
            if (colleague.get() != sender) {
                colleague->receive(message);
            }
        }
    }

private:
    std::vector<std::shared_ptr<Colleague>> colleagues_;
};
```
main():
```cpp
int main() {
    std::shared_ptr<ConcreteMediator> mediator = std::make_shared<ConcreteMediator>();

    std::shared_ptr<ConcreteColleague1> colleague1 = std::make_shared<ConcreteColleague1>(mediator);
    std::shared_ptr<ConcreteColleague2> colleague2 = std::make_shared<ConcreteColleague2>(mediator);

    mediator->addColleague(colleague1);
    mediator->addColleague(colleague2);

    colleague1->send("Hello from Colleague1!");
    colleague2->send("Hi from Colleague2!");

    return 0;
}
```
### Описание:
1. `Mediator`: Абстрактный класс, объявляющий метод `send` для отправки сообщений.
2. `ConcreteMediator`: Конкретная реализация посредника, который управляет коммуникацией между коллегами.
3. `Colleague`: Абстрактный класс, представляющий участника. Он содержит ссылку на посредника и методы для отправки и получения сообщений.
4. `ConcreteColleague1` и `ConcreteColleague2`: Конкретные реализации участников, которые взаимодействуют через посредника.
5. `main()`: Клиентский код, создающий экземпляры посредника и участников, и демонстрирующий их взаимодействие.

### Преимущества:
- Снижает зависимость между объектами, делая их взаимодействие более гибким и легко расширяемым.
- Упрощает обслуживание кода за счет централизованного управления коммуникацией.

### Недостатки:
- Может усложнить код из-за добавления дополнительных классов и методов.
- Посредник может стать слишком сложным, если управляет большим количеством объектов.

Паттерн **Mediator** особенно полезен, когда требуется централизованное управление взаимодействием множества объектов, чтобы снизить их взаимозависимость.