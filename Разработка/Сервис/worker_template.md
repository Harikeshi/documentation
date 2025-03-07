```cpp
#include <iostream>
#include <thread>
#include <chrono>

// Класс Worker
class Worker {
public:
    // Конструктор принимает задачу и аргументы
    Worker(void(*task)(int), int arg) : task(task), arg(arg) {}

    // Метод запускает выполнение задачи
    void run() {
        std::cout << "Worker started with task\n";
        task(arg);
        std::cout << "Worker finished task\n";
    }

private:
    void(*task)(int);
    int arg;
};

// Пример задачи
void exampleTask(int duration) {
    std::cout << "Task is running for " << duration << " seconds\n";
    std::this_thread::sleep_for(std::chrono::seconds(duration));
    std::cout << "Task completed\n";
}

int main() {
    // Создаем worker и запускаем задачу в отдельном потоке
    Worker worker(exampleTask, 5);
    std::thread workerThread(&Worker::run, &worker);
    
    // Ожидаем завершения потока
    workerThread.join();

    return 0;
}
```

### Описание:

1. **Класс `Worker`**:
   - Конструктор принимает задачу (`task`) в виде указателя на функцию и аргумент (`arg`).
   - Метод `run` запускает выполнение задачи, используя переданный аргумент.

2. **Пример задачи `exampleTask`**:
   - Функция `exampleTask` просто выполняется определенное время (`duration`), используя `std::this_thread::sleep_for`.

3. **Основная программа**:
   - Создаем экземпляр `Worker` и запускаем задачу в отдельном потоке с использованием `std::thread`.
   - Метод `join` ожидает завершения потока, чтобы основная программа не завершилась до завершения задачи.

Этот пример показывает, как можно использовать паттерн "Worker" для выполнения задач в отдельном потоке на языке C++, что позволяет эффективно использовать ресурсы и улучшить производительность приложения.