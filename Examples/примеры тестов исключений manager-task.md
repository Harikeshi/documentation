***Тестирование исключений для SchemeManager***

**Mock классы**

```cpp
#include <gmock/gmock.h>
#include "input.h"
#include "scheme.h"

class MockInput : public Abstractions::Input {
public:
    MOCK_METHOD(void, initializeProperties, (const nlohmann::json& json), (override));
};

class MockScheme : public Algorithms::Search::Scheme {
public:
    MOCK_METHOD(Outputs::Route, calculate, (), (override));
    MOCK_METHOD(Outputs::SchemeEfficiency, probability, (), (override));
};
```

**Тесты для SchemeManager с Mock**

```cpp
class SchemeManagerMockTest : public ::testing::Test {
protected:
    SchemeManager manager;
    std::unique_ptr<MockInput> mockInput;
    std::unique_ptr<MockScheme> mockScheme;

    void SetUp() override {
        mockInput = std::make_unique<MockInput>();
        mockScheme = std::make_unique<MockScheme>();
    }
};

TEST_F(SchemeManagerMockTest, CreateInput) {
    EXPECT_CALL(*mockInput, initializeProperties(testing::_)).Times(0); // Нет вызова функции, так как мы просто создаем входные данные

    auto input = manager.createInput(SearchScheme::Zigzag);
    EXPECT_NE(input, nullptr);
    EXPECT_TRUE(dynamic_cast<InputZigzag*>(input.get()) != nullptr);
}

TEST_F(SchemeManagerMockTest, CreateScheme) {
    EXPECT_CALL(*mockScheme, calculate()).Times(0); // Нет вызова функции, так как мы просто создаем схему

    InputZigzag input;
    auto scheme = manager.createScheme(SearchScheme::Zigzag, input);
    EXPECT_NE(scheme, nullptr);
    EXPECT_TRUE(dynamic_cast<Algorithms::Search::Zigzag*>(scheme.get()) != nullptr);
}

TEST_F(SchemeManagerMockTest, GetSchemes) {
    auto schemes = manager.getSchemes(SearchType::InRegion);
    EXPECT_FALSE(schemes.empty());
    EXPECT_EQ(schemes[0], SearchScheme::Zigzag);
    EXPECT_EQ(schemes[1], SearchScheme::Shift);
}

TEST_F(SchemeManagerMockTest, UnknownSchemeInput) {
    EXPECT_THROW(manager.createInput(static_cast<SearchScheme>(999)), std::invalid_argument);
}

TEST_F(SchemeManagerMockTest, UnknownScheme) {
    InputZigzag input;
    EXPECT_THROW(manager.createScheme(static_cast<SearchScheme>(999), input), std::invalid_argument);
}

TEST_F(SchemeManagerMockTest, GetSchemesForUnknownType) {
    EXPECT_THROW(manager.getSchemes(static_cast<SearchType>(999)), std::out_of_range);
}
```

***Тестирование исключений для SearchTask***

**Mock классы**

```cpp
class MockSchemeManager : public SchemeManager {
public:
    MOCK_METHOD(std::unique_ptr<Abstractions::Input>, createInput, (const SearchScheme scheme), (override));
    MOCK_METHOD(std::unique_ptr<Algorithms::Search::Scheme>, createScheme, (const SearchScheme scheme, const Abstractions::Input& input), (override));
};
```

**Тесты для SearchTask с Mock**

```cpp
class SearchTaskMockTest : public ::testing::Test {
protected:
    nlohmann::json jsonConfig;
    MockSchemeManager mockManager;
    std::unique_ptr<MockInput> mockInput;
    std::unique_ptr<MockScheme> mockScheme;

    void SetUp() override {
        jsonConfig = {
            {"ships_parameters", {{"search_velocity", 10.0}, {"max_velocity", 20.0}, {"detection_range", 5000.0}}},
            {"time", 3600.0},
            {"search_region", {{"entrance", {{"x", 0}, {"y", 0}}}, {"exit", {{"x", 10}, {"y", 10}}}}}
        };

        mockInput = std::make_unique<MockInput>();
        mockScheme = std::make_unique<MockScheme>();
    }
};

TEST_F(SearchTaskMockTest, SearchRouteValidInput) {
    SearchTask task(jsonConfig);

    EXPECT_CALL(mockManager, createInput(SearchScheme::Zigzag))
        .WillOnce(Return(ByMove(std::move(mockInput))));
    EXPECT_CALL(mockManager, createScheme(SearchScheme::Zigzag, testing::_))
        .WillOnce(Return(ByMove(std::move(mockScheme))));

    auto result = task.searchRoute(SearchScheme::Zigzag);
    EXPECT_FALSE(result["routes"].empty());
    EXPECT_EQ(result["messages"][0]["type"], "Info");
}

TEST_F(SearchTaskMockTest, SearchRouteInvalidInput) {
    jsonConfig.erase("time");
    SearchTask task(jsonConfig);

    EXPECT_CALL(mockManager, createInput(SearchScheme::Zigzag))
        .WillOnce(Return(ByMove(std::move(mockInput))));

    auto result = task.searchRoute(SearchScheme::Zigzag);
    EXPECT_TRUE(result["routes"][0]["points"].empty());
    EXPECT_EQ(result["messages"][0]["type"], "Error");
}

TEST_F(SearchTaskMockTest, CheckScheme) {
    SearchTask task(jsonConfig);

    EXPECT_CALL(mockManager, createInput(SearchScheme::Zigzag))
        .WillOnce(Return(ByMove(std::move(mockInput))));
    EXPECT_CALL(mockManager, createScheme(SearchScheme::Zigzag, testing::_))
        .WillOnce(Return(ByMove(std::move(mockScheme))));

    auto result = task.checkScheme(SearchType::InRegion);
    EXPECT_FALSE(result["schemes_efficiencies"].empty());
    EXPECT_EQ(result["messages"][0]["type"], "Info");
}

TEST_F(SearchTaskMockTest, CreateInputException) {
    SearchTask task(jsonConfig);

    EXPECT_CALL(mockManager, createInput(SearchScheme::Zigzag))
        .WillOnce(Throw(std::invalid_argument("Unknown scheme.")));

    EXPECT_THROW(task.searchRoute(SearchScheme::Zigzag), std::invalid_argument);
}

TEST_F(SearchTaskMockTest, CreateSchemeException) {
    SearchTask task(jsonConfig);

    EXPECT_CALL(mockManager, createInput(SearchScheme::Zigzag))
        .WillOnce(Return(ByMove(std::make_unique<MockInput>())));
    EXPECT_CALL(mockManager, createScheme(SearchScheme::Zigzag, testing::_))
        .WillOnce(Throw(std::runtime_error("Ошибка из Алгоритма")));

    auto result = task.searchRoute(SearchScheme::Zigzag);
    EXPECT_TRUE(result["routes"][0]["points"].empty());
    EXPECT_EQ(result["messages"][0]["type"], "Error");
    EXPECT_EQ(result["messages"][0]["text"], "Ошибка из Алгоритма");
}
```