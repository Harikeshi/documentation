### Пример mock-объекта с шаблонным методом

1. **Создание интерфейса с шаблонным методом**:
```cpp
// my_interface.h
#ifndef MY_INTERFACE_H
#define MY_INTERFACE_H

template<typename T>
class MyInterface {
public:
    virtual ~MyInterface() = default;
    virtual T DoSomething(const T& value) = 0;
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

template<typename T>
class MockMyInterface : public MyInterface<T> {
public:
    MOCK_METHOD(T, DoSomething, (const T& value), (override));
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
    MockMyInterface<int> mock_object;

    // Ожидание вызова метода DoSomething с аргументом 42 и возвратом 84
    EXPECT_CALL(mock_object, DoSomething(::testing::Eq(42)))
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

1. **Интерфейс**: Интерфейс `MyInterface` с шаблонным методом `DoSomething`, который принимает и возвращает значение типа `T`.
2. **Mock-объект**: Класс `MockMyInterface`, который наследует `MyInterface` и использует макрос `MOCK_METHOD` для создания mock-метода `DoSomething`.
3. **Тест**: Тестовый пример, в котором создается mock-объект и устанавливаются ожидания вызова метода `DoSomething` с аргументом `42` и возвратом `84`. Затем вызывается метод и проверяется результат.