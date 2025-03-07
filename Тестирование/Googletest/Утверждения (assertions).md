Утверждения (assertions) в GoogleTest — это инструменты для проверки условий в тестах. Они позволяют вам сравнивать ожидаемые и фактические результаты и сообщать об ошибках, если условия не выполняются. Утверждения делятся на две основные категории: `EXPECT_*` и `ASSERT_*`.

### Основные виды утверждений:

1. **Утверждения с EXPECT_***:
   - Проверяют условия и продолжают выполнение теста, если условие не выполняется.
   - Примеры:
     ```cpp
     EXPECT_EQ(value1, value2);    // Проверяет равенство значений
     EXPECT_NE(value1, value2);    // Проверяет неравенство значений
     EXPECT_LT(value1, value2);    // Проверяет, что value1 меньше value2
     EXPECT_LE(value1, value2);    // Проверяет, что value1 меньше или равно value2
     EXPECT_GT(value1, value2);    // Проверяет, что value1 больше value2
     EXPECT_GE(value1, value2);    // Проверяет, что value1 больше или равно value2
     EXPECT_TRUE(condition);       // Проверяет, что условие истинно
     EXPECT_FALSE(condition);      // Проверяет, что условие ложно
     EXPECT_STREQ(str1, str2);     // Проверяет равенство строк
     EXPECT_STRNE(str1, str2);     // Проверяет неравенство строк
     EXPECT_STRCASEEQ(str1, str2); // Проверяет равенство строк без учета регистра
     EXPECT_STRCASENE(str1, str2); // Проверяет неравенство строк без учета регистра
     ```

2. **Утверждения с ASSERT_***:
   - Проверяют условия и останавливают выполнение теста, если условие не выполняется.
   - Примеры:
     ```cpp
     ASSERT_EQ(value1, value2);    // Проверяет равенство значений
     ASSERT_NE(value1, value2);    // Проверяет неравенство значений
     ASSERT_LT(value1, value2);    // Проверяет, что value1 меньше value2
     ASSERT_LE(value1, value2);    // Проверяет, что value1 меньше или равно value2
     ASSERT_GT(value1, value2);    // Проверяет, что value1 больше value2
     ASSERT_GE(value1, value2);    // Проверяет, что value1 больше или равно value2
     ASSERT_TRUE(condition);       // Проверяет, что условие истинно
     ASSERT_FALSE(condition);      // Проверяет, что условие ложно
     ASSERT_STREQ(str1, str2);     // Проверяет равенство строк
     ASSERT_STRNE(str1, str2);     // Проверяет неравенство строк
     ASSERT_STRCASEEQ(str1, str2); // Проверяет равенство строк без учета регистра
     ASSERT_STRCASENE(str1, str2); // Проверяет неравенство строк без учета регистра
     ```

### Объяснение:

- **`EXPECT_*`**:
  - Используются, когда вы хотите, чтобы тест продолжал выполняться, даже если утверждение не выполняется.
  - Предоставляют больше информации о проблемах, поскольку продолжают проверять другие утверждения.
  - Пример:
    ```cpp
    EXPECT_EQ(Add(1, 1), 2);
    EXPECT_NE(Add(1, 1), 3);
    ```

- **`ASSERT_*`**:
  - Используются, когда выполнение теста должно быть немедленно прекращено при невыполнении условия.
  - Полезны для критических условий, когда последующие проверки не имеют смысла, если условие не выполнено.
  - Пример:
    ```cpp
    ASSERT_EQ(Add(1, 1), 2);
    ASSERT_TRUE(Add(1, 1) == 2);
    ```

### Примеры использования:

#### Пример 1: Проверка равенства и неравенства

```cpp
TEST(AdditionTest, HandlesPositiveNumbers) {
    int result = Add(2, 3);
    
    // Проверяет равенство значений
    EXPECT_EQ(result, 5);
    
    // Проверяет неравенство значений
    EXPECT_NE(result, 6);
}
```

#### Пример 2: Проверка условий

```cpp
TEST(ConditionTest, HandlesConditions) {
    int value = 5;
    
    // Проверяет, что условие истинно
    EXPECT_TRUE(value > 0);
    
    // Проверяет, что условие ложно
    EXPECT_FALSE(value < 0);
}
```

#### Пример 3: Проверка строк

```cpp
TEST(StringTest, HandlesStrings) {
    const char* str1 = "hello";
    const char* str2 = "HELLO";
    const char* str3 = "world";
    
    // Проверяет равенство строк
    EXPECT_STREQ(str1, "hello");
    
    // Проверяет неравенство строк
    EXPECT_STRNE(str1, str3);
    
    // Проверяет равенство строк без учета регистра
    EXPECT_STRCASEEQ(str1, str2);
}
```

### Дополнительные утверждения:

- **Проверка исключений**:
  ```cpp
  EXPECT_THROW(SomeFunction(), std::runtime_error);
  ASSERT_THROW(SomeFunction(), std::runtime_error);
  EXPECT_ANY_THROW(SomeFunction());
  ASSERT_ANY_THROW(SomeFunction());
  ```

- **Проверка отсутствия исключений**:
  ```cpp
  EXPECT_NO_THROW(SomeFunction());
  ASSERT_NO_THROW(SomeFunction());
  ```

### Заключение:

Утверждения в GoogleTest — это мощные инструменты для проверки условий в тестах. Они помогают выявлять ошибки и обеспечивать правильное функционирование вашего кода. Разница между `EXPECT_*` и `ASSERT_*` заключается в том, как они обрабатывают невыполнение условий: `EXPECT_*` продолжают выполнение теста, а `ASSERT_*` немедленно прекращают выполнение.