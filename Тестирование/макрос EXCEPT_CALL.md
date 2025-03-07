Макрос `EXPECT_CALL` в Google Mock используется для создания ожиданий вызовов методов в mock-объектах. Этот макрос позволяет вам задать, какие методы должны быть вызваны, с какими аргументами и сколько раз эти вызовы должны произойти. Это полезно для тестирования взаимодействий между объектами и проверки корректности поведения вашего кода в различных сценариях.

Например:

```cpp
EXPECT_CALL(mockObject, MethodName(ExpectedArg)).Times(1);
```

Здесь `mockObject` — это мок-объект, `MethodName` — метод, который ожидается, и `ExpectedArg` — ожидаемый аргумент. `Times(1)` указывает, что этот метод должен быть вызван ровно один раз.


### Основные элементы `EXPECT_CALL`:

- **Mock Object**: Объект, на котором устанавливаются ожидания вызова методов.
- **Method**: Метод mock-объекта, вызов которого ожидается.
- **Matcher**: Условия, которые должны быть выполнены для аргументов метода.
- **Action**: Действие, которое должно быть выполнено при вызове метода.
- **Times**: Количество раз, которое метод должен быть вызван.

### Пример использования `EXPECT_CALL`:

```cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>

class MyClass {
public:
    virtual ~MyClass() = default;
    virtual void MyMethod(int x) = 0;
};

class MockMyClass : public MyClass {
public:
    MOCK_METHOD(void, MyMethod, (int x), (override));
};

TEST(MyTestSuite, ExampleTest) {
    MockMyClass mock;

    // Устанавливаем ожидание вызова метода MyMethod с аргументом 42, который должен быть вызван один раз.
    EXPECT_CALL(mock, MyMethod(::testing::Eq(42)))
        .Times(1)
        .WillOnce(::testing::Invoke([](int x) {
            std::cout << "MyMethod called with: " << x << std::endl;
        }));

    // Вызов метода с ожидаемым аргументом
    mock.MyMethod(42);
}

int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### Объяснение:

1. **Создание Mock-объекта**: `MockMyClass` наследует от `MyClass` и использует макрос `MOCK_METHOD` для создания mock-метода `MyMethod`.
2. **Установка ожидания с `EXPECT_CALL`**:
   - Устанавливаем, что метод `MyMethod` должен быть вызван с аргументом `42`.
   - Метод должен быть вызван один раз (`Times(1)`).
   - Используем `WillOnce` для указания действия при вызове метода: в данном случае, вызов лямбда-функции, которая выводит аргумент на экран.
3. **Вызов метода**: Метод `MyMethod` вызывается с аргументом `42`, что соответствует ожиданиям.

### Дополнительные возможности:

- **Matcher**: Вы можете использовать различные matchers для аргументов метода, например, `::testing::Lt`, `::testing::Gt`, `::testing::Ne` и другие для указания условий на аргументы.
- **Actions**: В `WillOnce` и `WillRepeatedly` можно использовать различные действия, такие как `::testing::Return`, `::testing::Throw`, `::testing::Invoke`, и другие для указания поведения при вызове метода.