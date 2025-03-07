Паттерн **"Interpreter"** (Интерпретатор) в C++ используется для определения грамматики языка и создания интерпретатора, который интерпретирует предложения этого языка. Он полезен для обработки специализированных языков, математических выражений и парсеров.

### Основные элементы паттерна Interpreter

1. `AbstractExpression`: Интерфейс или абстрактный класс, объявляющий метод интерпретации.
2. `TerminalExpression`: Конкретная реализация `AbstractExpression` для терминальных символов грамматики.
3. `NonTerminalExpression`: Конкретная реализация `AbstractExpression` для нетерминальных символов грамматики.
4. `Context`: Класс, который хранит глобальное состояние интерпретации.
5. `Client`: Клиентский код, который создает и запускает интерпретатор.
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <memory>

// Интерфейс AbstractExpression, объявляющий метод interpret()
class AbstractExpression {
public:
    virtual ~AbstractExpression() = default;
    virtual int interpret() const = 0;
};
```
Класс `TerminalExpression`, представляющий терминальные символы
```cpp
class TerminalExpression : public AbstractExpression {
public:
    TerminalExpression(int value) : value_(value) {}

    int interpret() const override {
        return value_;
    }

private:
    int value_;
};
```
Класс `NonTerminalExpression`, представляющий нетерминальные символы (например, операция сложения)
```cpp
class AddExpression : public AbstractExpression {
public:
    AddExpression(std::shared_ptr<AbstractExpression> left, std::shared_ptr<AbstractExpression> right)
        : left_(left), right_(right) {}

    int interpret() const override {
        return left_->interpret() + right_->interpret();
    }

private:
    std::shared_ptr<AbstractExpression> left_;
    std::shared_ptr<AbstractExpression> right_;
};
```
Клиентский код, который создает и запускает интерпретатор
```cpp
int main() {
    std::shared_ptr<AbstractExpression> expr1 = std::make_shared<TerminalExpression>(5);
    std::shared_ptr<AbstractExpression> expr2 = std::make_shared<TerminalExpression>(10);
    std::shared_ptr<AbstractExpression> addExpr = std::make_shared<AddExpression>(expr1, expr2);

    std::cout << "Result: " << addExpr->interpret() << std::endl; // Output: 15

    return 0;
}
```
### Описание:
1. `AbstractExpression`: Абстрактный класс, объявляющий метод `interpret()`, который будет реализован в конкретных выражениях.
2. `TerminalExpression`: Конкретное выражение для терминальных символов грамматики. В примере это просто число.
3. `AddExpression`: Конкретное выражение для нетерминальных символов грамматики, представляющее операцию сложения. Оно интерпретирует два подвыражения и возвращает их сумму.
4. `main()`: Клиентский код, который создает терминальные выражения, затем создаёт нетерминальное выражение, которое их объединяет, и интерпретирует результат.

### Преимущества:
- Упрощает добавление новых грамматических правил и операций.
- Позволяет легко изменять и расширять язык.

### Недостатки:
- Может привести к сложности и уменьшению производительности при использовании для сложных языков и больших выражений.
- Может потребовать большого количества классов для представления всех правил грамматики.

Паттерн **Interpreter** особенно полезен для создания небольших языков, выражений и парсеров, где требуется простота добавления и изменения грамматических правил.