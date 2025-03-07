Паттерн **"Command"** (Команда) в C++ используется для инкапсуляции запроса в виде объекта, что позволяет параметризировать объекты с различными запросами, организовывать очереди запросов, логировать запросы и поддерживать операции отмены. Паттерн Command отделяет объект, который инициирует операцию, от объекта, который выполняет эту операцию.

### Основные элементы паттерна Command

1. `Command`: Интерфейс или абстрактный класс, объявляющий метод для выполнения команды.
2. `ConcreteCommand`: Конкретная реализация интерфейса `Command`, выполняющая конкретную операцию.
3. `Receiver`: Класс, который фактически выполняет операцию, когда команда вызывается.
4. `Invoker`: Класс, который хранит команду и вызывает метод выполнения команды.
5. `Client`: Создает объекты команд и передает их `Invoker`.
```cpp
#include <iostream>
#include <memory>
#include <vector>

// Интерфейс Command, объявляющий метод execute()
class Command
{
public:
    virtual ~Command() = default;
    virtual void execute() const = 0;
};

// Класс Receiver, который выполняет конкретные операции
class Receiver
{
public:
    void action() const
    {
        std::cout << "Receiver: Performing action\n";
    }
};
```
```
Конкретная команда, выполняющая операцию на `Receiver`
```cpp
class ConcreteCommand : public Command
{
public:
    ConcreteCommand(std::shared_ptr<Receiver> receiver)
        : receiver_(receiver)
    {
    }

    void execute() const override
    {
        receiver_->action();
    }

private:
    std::shared_ptr<Receiver> receiver_;
};
```
Класс Invoker, который хранит команду и вызывает её выполнение
```cpp
class Invoker
{
public:
    void setCommand(std::shared_ptr<Command> command)
    {
        command_ = command;
    }

    void invoke() const
    {
        if (command_)
        {
            command_->execute();
        }
    }

private:
    std::shared_ptr<Command> command_;
};
```
main():
```cpp
int main()
{
    // Создаем Receiver, Command и Invoker
    std::shared_ptr<Receiver> receiver = std::make_shared<Receiver>();
    std::shared_ptr<Command>  command  = std::make_shared<ConcreteCommand>(receiver);
    Invoker invoker;

    // Устанавливаем команду для Invoker и вызываем её выполнение
    invoker.setCommand(command);
    invoker.invoke();

    return 0;
}
```

### Описание:
1. `Command`: Абстрактный класс, объявляющий метод `execute`, который реализуют конкретные команды.
2. `ConcreteCommand`: Конкретная команда, которая вызывает метод action объекта `Receiver`.
3. `Receiver`: Класс, который выполняет конкретную операцию при вызове команды.
4. `Invoker`: Класс, который хранит команду и вызывает её метод `execute`.
5. `main()`: Клиентский код, который создает объекты `Receiver`, `ConcreteCommand` и `Invoker`, задает команду для Invoker и вызывает её выполнение.

### Преимущества:
- Упрощает добавление новых команд без изменения существующего кода.
- Позволяет легко реализовать отмену и повтор операций.
- Отделяет объекты, инициирующие операции, от объектов, выполняющих эти операции.

### Недостатки:
- Увеличивает сложность кода из-за множества дополнительных классов.
- Может привести к увеличению накладных расходов при частом создании и выполнении команд.

Паттерн Command особенно полезен для реализации задач отмены операций, управления транзакциями и организации сложных последовательностей операций.