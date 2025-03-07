- Унификация исключений: Обрабатываем исключения с использованием одного метода для формирования сообщений об ошибках.
- Упрощение структуры кода: Использование унифицированного метода валидации чисел и строк.
- Улучшение читаемости: Улучшаем читабельность, используя новые строки и комментирование кода.

```cpp
#include <nlohmann/json.hpp>
#include <stdexcept>
#include <string>
#include <vector>
#include <functional>
#include <iostream>

namespace Exceptions {
    enum class ValidationEnumFailure {
        MissingField,
        NotDigit,
        InvalidValue,
        NotString,
        EmptyField,
        NotArray,
        EmptyArray,
        BadLength
    };

    class ValidationException : public std::runtime_error {
    public:
        ValidationEnumFailure failureType;
        std::string fieldName;

        ValidationException(ValidationEnumFailure type, const std::string& field, const std::string& message)
            : std::runtime_error(message), failureType(type), fieldName(field) {}
    };
}
```
```cpp
namespace Abstractions {

inline void validateContains(const nlohmann::json& j, const std::string& key)
{
    if (!j.contains(key)) {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::MissingField, key, "Поле " + key + " отсутствует");
    }
}

template<typename T>
inline void validateDigit(const nlohmann::json& j, const std::string& key, T min, T max)
{
    validateContains(j, key);
    if (!j.at(key).is_number()) {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::NotDigit, key, "Поле " + key + " не является числом.");
    }
    T value = j.at(key).get<T>();
    if (value < min || value > max) {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::InvalidValue, key, "Поле " + key + " должно иметь значение от " + std::to_string(min) + " до " + std::to_string(max) + ".");
    }
}

inline void validateStringField(const nlohmann::json& j, const std::string& key)
{
    validateContains(j, key);
    if (!j.at(key).is_string()) {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::NotString, key, "Поле " + key + " не является строкой.");
    }
    if (j.at(key).get<std::string>().empty() || j.at(key).get<std::string>() == "null") {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::EmptyField, key, "Поле " + key + " не должно быть пустым.");
    }
}

inline void validateArrayField(const nlohmann::json& j, const std::string& key)
{
    validateContains(j, key);
    if (!j.at(key).is_array()) {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::NotArray, key, "Поле " + key + " не является массивом.");
    }
    if (j.at(key).empty()) {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::EmptyArray, key, "Поле " + key + " не должно быть пустым.");
    }
}

inline void validatePoint2D(const nlohmann::json& j, const std::string& key, const double min, const double max)
{
    validateContains(j, key);
    if (!j.at(key).is_array() || j.at(key).size() != 2) {
        throw Exceptions::ValidationException(Exceptions::ValidationEnumFailure::BadLength, key, "Поле " + key + " не является массивом длины 2.");
    }
    validateDigit(j.at(key)[0], key, min, max);
    validateDigit(j.at(key)[1], key, min, max);
}

class Input
{
public:
    virtual ~Input() = default;

    void fromJson(const nlohmann::json& json)
    {
        validate(json);
        initializeProperties(json);
    }

protected:
    virtual void initializeProperties(const nlohmann::json& json) = 0;

    void validate(const nlohmann::json& object) const
    {
        for (const auto& validator : validators)
            validator(object);
    }

    void addValidator(const std::string& key, const std::function<void(const nlohmann::json&)>& validator)
    {
        validators.emplace_back([key, validator](const nlohmann::json& j) {
            validator(j);
        });
    }

    std::vector<std::function<void(const nlohmann::json&)>> validators;
};
}
```
Тесты для класса Input

```cpp

#include <gtest/gtest.h>
#include <gmock/gmock.h>
#include "input.h"  // Наш класс Input и функции валидации
#include <nlohmann/json.hpp>
#include <stdexcept>

// Тестовая реализация класса Input для тестирования
class TestInput : public Abstractions::Input {
public:
    std::string name;
    int age;
    std::vector<double> points;

protected:
    void initializeProperties(const nlohmann::json& json) override {
        name = json.at("name").get<std::string>();
        age = json.at("age").get<int>();
        points = json.at("points").get<std::vector<double>>();
    }
};

class InputTest : public ::testing::Test {
protected:
    TestInput input;
    nlohmann::json jsonObject;

    void SetUp() override {
        jsonObject = {
            {"name", "Test Name"},
            {"age", 30},
            {"points", {1.0, 2.0}}
        };

        input.addValidator("name", Abstractions::validateStringField);
        input.addValidator("age", [](const nlohmann::json& j) {
            Abstractions::validateValueField<int>(j, "age", 0, 120);
        });
        input.addValidator("points", Abstractions::validateArrayField);
    }
};
```
```cpp
TEST_F(InputTest, ValidInput) {
    EXPECT_NO_THROW(input.fromJson(jsonObject));
    EXPECT_EQ(input.name, "Test Name");
    EXPECT_EQ(input.age, 30);
    EXPECT_EQ(input.points.size(), 2);
}

TEST_F(InputTest, MissingField) {
    jsonObject.erase("name");
    EXPECT_THROW(input.fromJson(jsonObject), Exceptions::ValidationException);
}

TEST_F(InputTest, NotDigitField) {
    jsonObject["age"] = "thirty";
    EXPECT_THROW(input.fromJson(jsonObject), Exceptions::ValidationException);
}

TEST_F(InputTest, InvalidValueField) {
    jsonObject["age"] = 150;
    EXPECT_THROW(input.fromJson(jsonObject), Exceptions::ValidationException);
}

TEST_F(InputTest, NotStringField) {
    jsonObject["name"] = 12345;
    EXPECT_THROW(input.fromJson(jsonObject), Exceptions::ValidationException);
}

TEST_F(InputTest, EmptyStringField) {
    jsonObject["name"] = "";
    EXPECT_THROW(input.fromJson(jsonObject), Exceptions::ValidationException);
}

TEST_F(InputTest, NotArrayField) {
    jsonObject["points"] = "not an array";
    EXPECT_THROW(input.fromJson(jsonObject), Exceptions::ValidationException);
}

TEST_F(InputTest, EmptyArrayField) {
    jsonObject["points"] = nlohmann::json::array();
    EXPECT_THROW(input.fromJson(jsonObject), Exceptions::ValidationException);
}
```
Объяснение тестов:
- `ValidInput`: Проверяет корректное выполнение метода `fromJson` при корректных входных данных.
- `MissingField`: Проверяет выброс исключения `ValidationException`, если отсутствует обязательное поле.
- `NotDigitField`: Проверяет выброс исключения `ValidationException`, если поле не является числом.
- `InvalidValueField`: Проверяет выброс исключения `ValidationException`, если значение числового поля выходит за пределы допустимого диапазона.
- `NotStringField`: Проверяет выброс исключения `ValidationException`, если поле не является строкой.
- `EmptyStringField`: Проверяет выброс исключения `ValidationException`, если строковое поле пустое.
- `NotArrayField`: Проверяет выброс исключения `ValidationException`, если поле не является массивом.
- `EmptyArrayField`: Проверяет выброс исключения `ValidationException`, если массив пуст.