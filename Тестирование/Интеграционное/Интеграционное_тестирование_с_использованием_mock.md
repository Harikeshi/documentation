### Интеграционное тестирование с использованием mock-объектов

Интеграционное тестирование с использованием mock-объектов позволяет имитировать поведение реальных объектов, что помогает изолировать компоненты системы и тестировать их взаимодействие без зависимости от реальных реализаций. Mock-объекты полезны для проверки взаимодействия между компонентами, особенно если некоторые компоненты еще не готовы или сложно использовать их реальные реализации.

В C++ можно использовать Google Mock (часть Google Test), чтобы создавать mock-объекты. Давайте рассмотрим пример интеграционного тестирования с mock-объектами.

### Пример реализации интеграционного тестирования с использованием Google Mock

Предположим, у нас есть два модуля: `ModuleA` и `ModuleB`. Мы будем тестировать `ModuleA`, используя mock-объект для `ModuleB`.

**ModuleB.h**:
```cpp
#ifndef MODULE_B_H
#define MODULE_B_H

#include <string>

class ModuleB {
public:
    virtual ~ModuleB() = default;
    virtual std::string getDataB() const = 0;
};

#endif // MODULE_B_H
```

**ModuleA.h**:
```cpp
#ifndef MODULE_A_H
#define MODULE_A_H

#include "ModuleB.h"
#include <string>

class ModuleA {
public:
    void setModuleB(ModuleB* moduleB);
    std::string processData() const;

private:
    ModuleB* moduleB;
};

#endif // MODULE_A_H
```

**ModuleA.cpp**:
```cpp
#include "ModuleA.h"

void ModuleA::setModuleB(ModuleB* moduleB) {
    this->moduleB = moduleB;
}

std::string ModuleA::processData() const {
    if (moduleB) {
        return "Processed " + moduleB->getDataB();
    }
    return "No data";
}
```

**MockModuleB.h** (mock-объект для ModuleB):
```cpp
#ifndef MOCK_MODULE_B_H
#define MOCK_MODULE_B_H

#include "ModuleB.h"
#include "gmock/gmock.h"

class MockModuleB : public ModuleB {
public:
    MOCK_METHOD(std::string, getDataB, (), (const, override));
};

#endif // MOCK_MODULE_B_H
```

### Интеграционные тесты с использованием Google Test и Google Mock

**IntegrationTest.cpp**:
```cpp
#include "gtest/gtest.h"
#include "gmock/gmock.h"
#include "ModuleA.h"
#include "MockModuleB.h"

using ::testing::Return;

TEST(IntegrationTestWithMock, ProcessData) {
    ModuleA moduleA;
    MockModuleB mockModuleB;

    // Настройка mock-объекта
    EXPECT_CALL(mockModuleB, getDataB())
        .WillOnce(Return("Mock data from Module B"));

    moduleA.setModuleB(&mockModuleB);

    std::string result = moduleA.processData();
    EXPECT_EQ(result, "Processed Mock data from Module B");
}

int main(int argc, char **argv) {
    ::testing::InitGoogleMock(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### Сборка и запуск тестов

1. Скомпилируйте проект:
   ```sh
   mkdir build
   cd build
   cmake ..
   make
   ```

2. Запустите тесты:
   ```sh
   ./IntegrationTest
   ```

### Объяснение

1. **MockModuleB**: Мы создали mock-объект для `ModuleB`, используя Google Mock. Этот объект имитирует поведение настоящего `ModuleB` и позволяет нам задавать ожидаемые вызовы и возвращаемые значения.

2. **Интеграционный тест**: В тесте `IntegrationTestWithMock`, мы настроили mock-объект `MockModuleB` таким образом, чтобы метод `getDataB` возвращал заданное значение. Затем мы протестировали `ModuleA`, проверив, что он корректно взаимодействует с mock-объектом.

Использование mock-объектов позволяет изолировать компоненты и тестировать их взаимодействие независимо от реальных реализаций. Это делает процесс тестирования более гибким и эффективным.