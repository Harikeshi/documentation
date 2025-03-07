**Реализация std::function с переменным числом аргументов.**

```cpp
#include <iostream>
#include <functional>

// Шаблонный класс функтор с переменным числом аргументов
template<typename... Args>
class Functor {
public:
    Functor(std::function<void(Args...)> func) : func_(func) {}

    void operator()(Args... args) {
        func_(args...);
    }

private:
    std::function<void(Args...)> func_;
};

int main() {
    // Создание функтора с переменным числом аргументов
    auto print = Functor<int, double, std::string>([](int a, double b, std::string c) {
        std::cout << "a: " << a << ", b: " << b << ", c: " << c << std::endl;
    });

    // Вызов функтора
    print(42, 3.14, "Hello");

    return 0;
}
```

**Вариативные шаблоны.**

```cpp
#include <iostream>

// Вариативная шаблонная функция
template<typename... Args>
void print(Args... args) {
    (std::cout << ... << args) << std::endl; // Используем оператор свертки для вывода
}

int main() {
    // Вызов функции с различным числом аргументов
    print(42, 3.14, "Hello");
    print("One", "Two", "Three");
    print(1, 2, 3, 4, 5);

    return 0;
}
```