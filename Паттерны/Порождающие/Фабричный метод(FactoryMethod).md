Фабричный метод **"Factory Method"** — это паттерн проектирования, который определяет интерфейс для создания объектов, но позволяет подклассам решать, какой класс инстанцировать. Этот паттерн позволяет классу делегировать создание объектов подклассам.

Рассмотрим пример создания различных типов документов с использованием Фабричного метода.

1. Определите интерфейс продукта
```cpp
#include <iostream>

class Document {
public:
    virtual void open() = 0;
    virtual ~Document() = default;
};
```
2. Создайте конкретные продукты
```cpp
class WordDocument : public Document {
public:
    void open() override {
        std::cout << "Opening Word document.\n";
    }
};

class PdfDocument : public Document {
public:
    void open() override {
        std::cout << "Opening PDF document.\n";
    }
};
```
3. Определите интерфейс создателя с фабричным методом
```cpp
class Application {
public:
    virtual Document* createDocument() = 0;
    void newDocument() {
        Document* doc = createDocument();
        doc->open();
        delete doc;
    }
    virtual ~Application() = default;
};
```
4. Создайте конкретные классы создателей, реализующие фабричный метод
```cpp
class WordApplication : public Application {
public:
    Document* createDocument() override {
        return new WordDocument();
    }
};

class PdfApplication : public Application {
public:
    Document* createDocument() override {
        return new PdfDocument();
    }
};
```
5. Использование фабричного метода
```cpp
int main() {
    Application* app = new WordApplication();
    app->newDocument(); // Создает и открывает Word документ
    delete app;

    app = new PdfApplication();
    app->newDocument(); // Создает и открывает PDF документ
    delete app;

    return 0;
}
```
### Объяснение кода:
- Интерфейс продукта: `Document` определяет интерфейс для различных типов документов.
- Конкретные продукты: `WordDocument` и `PdfDocument реализуют интерфейс `Document`.
- Интерфейс создателя: `Application` определяет интерфейс с фабричным методом `createDocument`.
- Конкретные создатели: `WordApplication` и `PdfApplication` реализуют фабричный метод для создания конкретных документов.