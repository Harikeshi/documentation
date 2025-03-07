Паттерн **Worker** (или Worker Thread) используется для выполнения задач в отдельных потоках, что позволяет основному потоку приложения оставаться отзывчивым.

### Шаги реализации паттерна Worker

Создание класса Worker: Этот класс будет представлять собой рабочий поток, который выполняет задачи.

```cpp
#include <iostream>
#include <thread>
#include <queue>
#include <mutex>
#include <condition_variable>
#include <functional>

class Worker {
public:
    Worker() : stop(false) {
        workerThread = std::thread(&Worker::processTasks, this);
    }

    ~Worker() {
        {
            std::unique_lock<std::mutex> lock(queueMutex);
            stop = true;
        }
        condition.notify_all();
        workerThread.join();
    }

    void addTask(std::function<void()> task) {
        {
            std::unique_lock<std::mutex> lock(queueMutex);
            tasks.push(task);
        }
        condition.notify_one();
    }

private:
    void processTasks() {
        while (true) {
            std::function<void()> task;
            {
                std::unique_lock<std::mutex> lock(queueMutex);
                condition.wait(lock, [this] { return !tasks.empty() || stop; });
                if (stop && tasks.empty()) return;
                task = std::move(tasks.front());
                tasks.pop();
            }
            task();
        }
    }

    std::thread workerThread;
    std::queue<std::function<void()>> tasks;
    std::mutex queueMutex;
    std::condition_variable condition;
    bool stop;
};
```

Использование класса Worker: Создайте объект Worker и добавьте задачи для выполнения.

```cpp
int main() {
    Worker worker;

    worker.addTask([]() {
        std::cout << "Task 1 is running" << std::endl;
    });

    worker.addTask([]() {
        std::cout << "Task 2 is running" << std::endl;
    });

    std::this_thread::sleep_for(std::chrono::seconds(1)); // Даем время для выполнения задач

    return 0;
}
```
### Объяснение кода
### Класс Worker:
- `workerThread`: Поток, в котором выполняются задачи.
- `tasks`: Очередь задач, которые нужно выполнить.
- `queueMutex`: Мьютекс для защиты доступа к очереди задач.
- `condition`: Условная переменная для уведомления потока о новых задачах.
- `stop`: Флаг для остановки рабочего потока.

### Методы:
- `addTask`: Добавляет новую задачу в очередь и уведомляет рабочий поток.
- `processTasks`: Основной метод рабочего потока, который извлекает задачи из очереди и выполняет их.

Этот паттерн полезен для выполнения фоновых задач, таких как обработка данных, сетевые запросы или любые другие длительные операции, не блокируя основной поток приложения.