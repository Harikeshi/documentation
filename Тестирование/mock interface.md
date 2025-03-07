### Пример использования с интерфейсом

### Шаги:

1. **Создадим интерфейс**: Определим интерфейс с методами, которые будем мокировать.
2. **Создадим mock-объект**: Сгенерируем mock-объект на основе интерфейса.
3. **Напишем тесты**: Создадим тесты, которые используют mock-объект для проверки взаимодействий.

### Пример:

#### 1. Интерфейс

```cpp
// my_interface.h
#ifndef MY_INTERFACE_H
#define MY_INTERFACE_H

class MyInterface {
public:
    virtual ~MyInterface() = default;
    virtual void DoSomething(int x) = 0;
    virtual int Calculate(int a, int b) = 0;
};

#endif // MY_INTERFACE_H
```

#### 2. Mock-объект

```cpp
// mock_my_interface.h
#ifndef MOCK_MY_INTERFACE_H
#define MOCK_MY_INTERFACE_H

#include <gmock/gmock.h>
#include "my_interface.h"

class MockMyInterface : public MyInterface {
public:
    MOCK_METHOD(void, DoSomething, (int x), (override));
    MOCK_METHOD(int, Calculate, (int a, int b), (override));
};

#endif // MOCK_MY_INTERFACE_H
```

#### 3. Тестируемый класс

```cpp
// my_class.h
#ifndef MY_CLASS_H
#define MY_CLASS_H

#include "my_interface.h"

class MyClass {
public:
    MyClass(MyInterface* interface) : interface_(interface) {}
    
    void Process(int value) {
        interface_->DoSomething(value);
    }

    int Sum(int a, int b) {
        return interface_->Calculate(a, b);
    }

private:
    MyInterface* interface_;
};

#endif // MY_CLASS_H
```

#### 4. Тесты

```cpp
// my_tests.cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>
#include "mock_my_interface.h"
#include "my_class.h"

using ::testing::_;
using ::testing::Eq;
using ::testing::Return;

TEST(MyClassTest, ProcessTest) {
    MockMyInterface mock_interface;
    MyClass my_class(&mock_interface);

    // Установка ожидания вызова DoSomething с аргументом 42
    EXPECT_CALL(mock_interface, DoSomething(Eq(42)))
        .Times(1);

    // Вызов метода Process
    my_class.Process(42);
}

TEST(MyClassTest, SumTest) {
    MockMyInterface mock_interface;
    MyClass my_class(&mock_interface);

    // Установка ожидания вызова Calculate с аргументами 3 и 4, возвратом 7
    EXPECT_CALL(mock_interface, Calculate(3, 4))
        .WillOnce(Return(7));

    // Вызов метода Sum и проверка результата
    int result = my_class.Sum(3, 4);
    EXPECT_EQ(result, 7);
}

int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);
    ::testing::InitGoogleMock(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### Объяснение:

1. **Интерфейс**: Определяем интерфейс `MyInterface` с методами `DoSomething` и `Calculate`.
2. **Mock-объект**: Создаем `MockMyInterface` с использованием `MOCK_METHOD` для генерации mock-методов.
3. **Тестируемый класс**: Класс `MyClass` использует интерфейс для выполнения операций.
4. **Тесты**:
   - Тест `ProcessTest` проверяет, что метод `DoSomething` вызывается с аргументом `42`.
   - Тест `SumTest` проверяет, что метод `Calculate` вызывается с аргументами `3` и `4`, и возвращает `7`.