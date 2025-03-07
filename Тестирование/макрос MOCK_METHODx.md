Макросы `MOCK_METHODx`, где `x` - это количество аргументов метода. Давайте рассмотрим пример, как это сделать для шаблонного метода в не-шаблонном классе.

### Пример не-шаблонного класса с шаблонным методом

1. **Создание интерфейса с шаблонным методом**:
```cpp
// my_interface.h
#ifndef MY_INTERFACE_H
#define MY_INTERFACE_H

class MyInterface {
public:
    virtual ~MyInterface() = default;

    template<typename T>
    T DoSomething(const T& value);
};

#endif // MY_INTERFACE_H
```

2. **Создание mock-объекта с использованием Google Mock**:
```cpp
// mock_my_interface.h
#ifndef MOCK_MY_INTERFACE_H
#define MOCK_MY_INTERFACE_H

#include <gmock/gmock.h>
#include "my_interface.h"

class MockMyInterface : public MyInterface {
public:
    template<typename T>
    class MockMethod {
    public:
        MOCK_METHOD1(Call, T(const T&));
    };

    template<typename T>
    T DoSomething(const T& value) override {
        return mock_method<T>.Call(value);
    }

    template<typename T>
    MockMethod<T> mock_method;
};

#endif // MOCK_MY_INTERFACE_H
```

3. **Написание теста с использованием mock-объекта**:
```cpp
// my_tests.cpp
#include <gtest/gtest.h>
#include "mock_my_interface.h"

TEST(MyTestSuite, TemplateMethodTest) {
    // Создание mock-объекта
    MockMyInterface mock_object;

    // Ожидание вызова шаблонного метода DoSomething с аргументом 42 и возвратом 84
    EXPECT_CALL(mock_object.mock_method<int>, Call(::testing::Eq(42)))
        .WillOnce(::testing::Return(84));

    // Вызов метода и проверка результата
    int result = mock_object.DoSomething(42);
    EXPECT_EQ(result, 84);
}

int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### Объяснение:

1. **Интерфейс**: Класс `MyInterface` с виртуальным шаблонным методом `DoSomething`.
2. **Mock-объект**: Класс `MockMyInterface`, который наследует `MyInterface` и использует макрос `MOCK_METHOD1` для создания mock-метода `Call` внутри шаблонного класса `MockMethod`. Метод `DoSomething` переопределяется и вызывает mock-метод `Call`.
3. **Тест**: Тестовый пример, в котором создается mock-объект и устанавливаются ожидания вызова метода `Call` с аргументом `42` и возвратом `84`. Затем вызывается метод `DoSomething`, и проверяется результат.