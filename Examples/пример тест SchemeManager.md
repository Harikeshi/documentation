**Тесты для SchemeManager**

```cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>
#include "input.h"  // Input, InputZigzag
#include "scheme.h"  // Scheme, Zigzag
#include "scheme_manager.h"  // SchemeManager, SearchScheme, SearchType

class SchemeManagerTest : public ::testing::Test {
protected:
    SchemeManager manager;
};

TEST_F(SchemeManagerTest, CreateInput) {
    auto input = manager.createInput(SearchScheme::Zigzag);
    EXPECT_NE(input, nullptr);
    EXPECT_TRUE(dynamic_cast<InputZigzag*>(input.get()) != nullptr);
}

TEST_F(SchemeManagerTest, CreateScheme) {
    InputZigzag input;
    auto scheme = manager.createScheme(SearchScheme::Zigzag, input);
    EXPECT_NE(scheme, nullptr);
    EXPECT_TRUE(dynamic_cast<Algorithms::Search::Zigzag*>(scheme.get()) != nullptr);
}

TEST_F(SchemeManagerTest, GetSchemes) {
    auto schemes = manager.getSchemes(SearchType::InRegion);
    EXPECT_FALSE(schemes.empty());
    EXPECT_EQ(schemes[0], SearchScheme::Zigzag);
    EXPECT_EQ(schemes[1], SearchScheme::Shift);
}

TEST_F(SchemeManagerTest, UnknownSchemeInput) {
    EXPECT_THROW(manager.createInput(static_cast<SearchScheme>(999)), std::invalid_argument);
}

TEST_F(SchemeManagerTest, UnknownScheme) {
    InputZigzag input;
    EXPECT_THROW(manager.createScheme(static_cast<SearchScheme>(999), input), std::invalid_argument);
}

TEST_F(SchemeManagerTest, GetSchemesForUnknownType) {
    EXPECT_THROW(manager.getSchemes(static_cast<SearchType>(999)), std::out_of_range);
}
```

- Создание входных данных для известных схем.
- Создание схем для известных входных данных.
- Получение списка схем для заданного типа поиска.
- Обработка неизвестных схем и типов поиска с выбросом соответствующих исключений.