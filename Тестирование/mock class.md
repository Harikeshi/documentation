### Пример использования с классом

1. **Класс, который будем мокировать**:
```cpp
// my_class.h
#ifndef MY_CLASS_H
#define MY_CLASS_H

class MyClass {
public:
    MyClass() = default;
    virtual ~MyClass() = default;

    virtual void DoSomething(int x);
    virtual int Calculate(int a, int b);
};

#endif // MY_CLASS_H
```

2. **Создание mock-объекта**:
```cpp
// mock_my_class.h
#ifndef MOCK_MY_CLASS_H
#define MOCK_MY_CLASS_H

#include <gmock/gmock.h>
#include "my_class.h"

class MockMyClass : public MyClass {
public:
    MOCK_METHOD(void, DoSomething, (int x), (override));
    MOCK_METHOD(int, Calculate, (int a, int b), (override));
};

#endif // MOCK_MY_CLASS_H
```

3. **Класс, который использует `MyClass`**:
```cpp
// user_class.h
#ifndef USER_CLASS_H
#define USER_CLASS_H

#include "my_class.h"

class UserClass {
public:
    UserClass(MyClass* my_class) : my_class_(my_class) {}

    void Process(int value) {
        my_class_->DoSomething(value);
    }

    int Sum(int a, int b) {
        return my_class_->Calculate(a, b);
    }

private:
    MyClass* my_class_;
};

#endif // USER_CLASS_H
```

4. **Тесты с использованием mock-объекта**:
```cpp
// my_tests.cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>
#include "mock_my_class.h"
#include "user_class.h"

using ::testing::_;
using ::testing::Eq;
using ::testing::Return;

TEST(UserClassTest, ProcessTest) {
    MockMyClass mock;
    UserClass user_class(&mock);

    // Установка ожидания вызова DoSomething с аргументом 42
    EXPECT_CALL(mock, DoSomething(Eq(42)))
        .Times(1);

    // Вызов метода Process
    user_class.Process(42);
}

TEST(UserClassTest, SumTest) {
    MockMyClass mock;
    UserClass user_class(&mock);

    // Установка ожидания вызова Calculate с аргументами 3 и 4, возвратом 7
    EXPECT_CALL(mock, Calculate(3, 4))
        .WillOnce(Return(7));

    // Вызов метода Sum и проверка результата
    int result = user_class.Sum(3, 4);
    EXPECT_EQ(result, 7);
}

int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);
    ::testing::InitGoogleMock(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### Объяснение:

1. **Класс `MyClass`**: Класс с виртуальными методами `DoSomething` и `Calculate`, которые будем мокировать.
2. **Mock-объект**: `MockMyClass` используется для создания mock-методов.
3. **Класс `UserClass`**: Класс, который использует `MyClass` для выполнения операций.
4. **Тесты**:
   - `ProcessTest` проверяет, что метод `DoSomething` вызывается с аргументом `42`.
   - `SumTest` проверяет, что метод `Calculate` вызывается с аргументами `3` и `4`, и возвращает `7`.