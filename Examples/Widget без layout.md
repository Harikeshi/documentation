Если вы хотите скомпоновать виджеты (например, кнопки и `QLabel`) на `QWidget` без использования компоновки (`layout`), вам нужно вручную задать их положение и размер с помощью методов `setGeometry` или `move` и `resize`. Этот подход требует больше ручной работы, но дает полный контроль над расположением виджетов.

---

### Пример: Ручное размещение виджетов

```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QLabel>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    // Создаем главный виджет
    QWidget window;

    // Создаем QLabel и задаем его положение и размер
    QLabel *label = new QLabel("Hello, World!", &window);
    label->setGeometry(50, 20, 200, 30); // x, y, width, height

    // Создаем кнопки и задаем их положение и размер
    QPushButton *button1 = new QPushButton("Button 1", &window);
    button1->setGeometry(50, 70, 100, 30); // x, y, width, height

    QPushButton *button2 = new QPushButton("Button 2", &window);
    button2->setGeometry(50, 120, 100, 30); // x, y, width, height

    // Показываем главный виджет
    window.setWindowTitle("Manual Widget Placement");
    window.resize(300, 200); // Устанавливаем размер окна
    window.show();

    return app.exec();
}
```

---

### Объяснение:
1. **Создание виджетов**:
   - Мы создаем `QLabel` и два `QPushButton`, передавая родительский виджет (`&window`) в их конструкторы. Это автоматически добавляет их на главный виджет.

2. **Ручное размещение**:
   - Для каждого виджета мы используем метод `setGeometry(x, y, width, height)`, чтобы задать его положение и размер.
     - `x` и `y` — координаты верхнего левого угла виджета относительно родительского виджета.
     - `width` и `height` — ширина и высота виджета.

3. **Изменение размера окна**:
   - Мы задаем размер главного окна с помощью метода `resize(width, height)`.

4. **Отображение окна**:
   - Окно отображается с помощью метода `show()`.

---

### Преимущества и недостатки:
- **Преимущества**:
  - Полный контроль над положением и размером каждого виджета.
  - Подходит для простых интерфейсов с фиксированным расположением элементов.

- **Недостатки**:
  - Требуется ручная настройка положения и размеров.
  - При изменении размера окна виджеты не адаптируются автоматически.
  - Сложно поддерживать и изменять интерфейс.

---

### Как адаптировать под изменение размера окна:
Если вы хотите, чтобы виджеты адаптировались при изменении размера окна, вам нужно переопределить метод `resizeEvent` и вручную обновить положение и размеры виджетов. Например:

```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QLabel>

class CustomWidget : public QWidget {
public:
    CustomWidget(QWidget *parent = nullptr) : QWidget(parent) {
        // Создаем QLabel и кнопки
        label = new QLabel("Hello, World!", this);
        button1 = new QPushButton("Button 1", this);
        button2 = new QPushButton("Button 2", this);

        // Устанавливаем начальное положение и размер
        updateWidgetPositions();
    }

protected:
    void resizeEvent(QResizeEvent *event) override {
        // При изменении размера окна обновляем положение виджетов
        updateWidgetPositions();
        QWidget::resizeEvent(event);
    }

private:
    void updateWidgetPositions() {
        // Обновляем положение и размеры виджетов
        int width = this->width();
        int height = this->height();

        label->setGeometry(50, 20, width - 100, 30);
        button1->setGeometry(50, 70, 100, 30);
        button2->setGeometry(50, 120, 100, 30);
    }

    QLabel *label;
    QPushButton *button1;
    QPushButton *button2;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    CustomWidget window;
    window.setWindowTitle("Manual Widget Placement with Resize");
    window.resize(300, 200);
    window.show();

    return app.exec();
}
```

---

### Что изменилось:
1. **Переопределение `resizeEvent`**:
   - Мы переопределяем метод `resizeEvent`, чтобы обновлять положение и размеры виджетов при изменении размера окна.

2. **Метод `updateWidgetPositions`**:
   - Этот метод вычисляет новые координаты и размеры виджетов на основе текущего размера окна.

---

### Итог:
- Если вы не хотите использовать компоновку (`layout`), вы можете вручную задавать положение и размеры виджетов с помощью `setGeometry`.
- Для адаптации под изменение размера окна переопределите метод `resizeEvent`.
- Этот подход подходит для простых интерфейсов, но для более сложных случаев рекомендуется использовать компоновки.