Хорошо, давайте рассмотрим более детально параметризованные фикстуры в GoogleTest с конкретным примером. Мы создадим класс `Calculator`, который выполняет базовые арифметические операции, и напишем параметризованные тесты для проверки этих операций с различными входными данными.

### Шаги:

1. **Создадим класс `Calculator`**: Класс с методами для выполнения операций.
2. **Создадим параметризованную фикстуру**: Фикстура, наследующаяся от `::testing::TestWithParam`, для использования параметров в тестах.
3. **Напишем тесты с использованием фикстуры**: Тесты, которые используют параметры и проверяют методы класса.

#### 1. Класс `Calculator`

```cpp
// calculator.h
#ifndef CALCULATOR_H
#define CALCULATOR_H

class Calculator {
public:
    int Add(int a, int b) const {
        return a + b;
    }
    int Subtract(int a, int b) const {
        return a - b;
    }
};

#endif // CALCULATOR_H
```

#### 2. Параметризованная фикстура

```cpp
// calculator_test.h
#ifndef CALCULATOR_TEST_H
#define CALCULATOR_TEST_H

#include <gtest/gtest.h>
#include "calculator.h"

class CalculatorTest : public ::testing::TestWithParam<std::tuple<int, int, int>> {
protected:
    Calculator calculator;
};

#endif // CALCULATOR_TEST_H
```

#### 3. Тесты с использованием параметризованной фикстуры

```cpp
#include "calculator_test.h"

// Тест для метода Add
TEST_P(CalculatorTest, HandlesAddition) {
    // Получаем параметры
    int a = std::get<0>(GetParam());
    int b = std::get<1>(GetParam());
    int expected = std::get<2>(GetParam());

    // Проверяем метод Add
    EXPECT_EQ(calculator.Add(a, b), expected);
}

// Тест для метода Subtract
TEST_P(CalculatorTest, HandlesSubtraction) {
    // Получаем параметры
    int a = std::get<0>(GetParam());
    int b = std::get<1>(GetParam());
    int expected = std::get<2>(GetParam());

    // Проверяем метод Subtract
    EXPECT_EQ(calculator.Subtract(a, b), expected);
}

// Задаем набор параметров для тестов Add и Subtract
INSTANTIATE_TEST_SUITE_P(
    AdditionTests,
    CalculatorTest,
    ::testing::Values(
        std::make_tuple(1, 1, 2),
        std::make_tuple(2, 3, 5),
        std::make_tuple(-1, -1, -2),
        std::make_tuple(-1, 1, 0)
    )
);

INSTANTIATE_TEST_SUITE_P(
    SubtractionTests,
    CalculatorTest,
    ::testing::Values(
        std::make_tuple(1, 1, 0),
        std::make_tuple(5, 3, 2),
        std::make_tuple(-1, -1, 0),
        std::make_tuple(-1, 1, -2)
    )
);

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### Объяснение:

1. **Класс `Calculator`**:
   - Класс `Calculator` имеет методы `Add` и `Subtract`, которые выполняют сложение и вычитание соответственно.

2. **Параметризованная фикстура `CalculatorTest`**:
   - Наследуется от `::testing::TestWithParam<std::tuple<int, int, int>>`, где `std::tuple<int, int, int>` представляет параметры теста: два входных числа и ожидаемый результат.
   - Содержит объект `Calculator`, который будет использоваться в тестах.

3. **Тесты `HandlesAddition` и `HandlesSubtraction`**:
   - Используют `GetParam()` для получения текущего набора параметров.
   - `std::get<N>(GetParam())` извлекает параметры из `std::tuple`.
   - `EXPECT_EQ(calculator.Add(a, b), expected)` и `EXPECT_EQ(calculator.Subtract(a, b), expected)` проверяют результаты методов `Add` и `Subtract`.

4. **Макрос `INSTANTIATE_TEST_SUITE_P`**:
   - Задает набор параметров для тестов `AdditionTests` и `SubtractionTests`, используя `::testing::Values`.
   - Параметры задаются как `std::tuple` с тремя элементами: два входных числа и ожидаемый результат.

### Преимущества параметризованных фикстур:

- **Избегание дублирования кода**: Один и тот же тест запускается с различными параметрами.
- **Гибкость**: Легко добавлять или изменять набор параметров без изменения самого теста.
- **Организация**: Тесты с параметрами группируются в одной фикстуре, что делает код тестов более чистым и организованным.

Параметризованные фикстуры в GoogleTest позволяют эффективно тестировать методы класса с различными входными данными, обеспечивая корректную работу кода в различных сценариях. Если у вас есть дополнительные вопросы или нужна помощь с другими аспектами тестирования, дайте знать!