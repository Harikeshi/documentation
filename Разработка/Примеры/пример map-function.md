```cpp
#include <iostream>
#include <memory>
#include <map>
#include <functional>

// Базовые классы
class Input {
public:
    virtual ~Input() = default;
};

class Scheme {
public:
    virtual ~Scheme() = default;
};

// Пример конкретного класса Input
class InputA : public Input {
public:
    // Поля и методы для InputA
};

// Пример конкретного класса Scheme
class SchemeA : public Scheme {
    const InputA& input;
public:
    SchemeA(const InputA& input) : input(input) {}

    // Методы для SchemeA
};

// Шаблонная функция для регистрации фабрики
template <typename SchemeClass, typename InputClass, typename MapType>
void registerFactory(MapType& map, int scheme) {
    map[scheme] = [](const Input& input) -> std::unique_ptr<Scheme> {
        const auto& specificInput = dynamic_cast<const InputClass&>(input);
        return std::make_unique<SchemeClass>(specificInput);
    };
}

int main() {
    std::map<int, std::function<std::unique_ptr<Scheme>(const Input&)>> schemeFactoryMap;

    // Регистрация фабрики с использованием шаблона
    registerFactory<SchemeA, InputA>(schemeFactoryMap, 0);

    // Пример использования
    InputA input;
    std::unique_ptr<Scheme> scheme = schemeFactoryMap[0](input);

    return 0;
}
```