```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QPolygon>
#include <QPoint>
#include <vector>

// Кастомный виджет для отрисовки полигона
class PolygonWidget : public QWidget {
public:
    // Конструктор, принимающий вектор точек
    explicit PolygonWidget(const std::vector<QPoint>& points, QWidget* parent = nullptr)
        : QWidget(parent), polygonPoints(points) {}

protected:
    void paintEvent(QPaintEvent* event) override {
        Q_UNUSED(event);

        QPainter painter(this); // Создаем объект QPainter для отрисовки

        // Настраиваем кисть и перо
        painter.setPen(Qt::black); // Цвет границы полигона
        painter.setBrush(Qt::blue); // Цвет заливки полигона

        // Преобразуем вектор точек в QPolygon
        QPolygon polygon;
        for (const auto& point : polygonPoints) {
            polygon << point;
        }

        // Рисуем полигон
        painter.drawPolygon(polygon);
    }

private:
    std::vector<QPoint> polygonPoints; // Вектор точек полигона
};

int main(int argc, char* argv[]) {
    QApplication app(argc, argv);

    // Создаем главное окно
    QWidget window;
    window.setWindowTitle("Polygon Widget Example");
    window.resize(400, 400); // Размер окна

    // Создаем вектор точек для полигона
    std::vector<QPoint> points = {
        QPoint(100, 50),  // Верхняя точка
        QPoint(200, 150), // Правая точка
        QPoint(150, 250), // Нижняя правая точка
        QPoint(50, 250),  // Нижняя левая точка
        QPoint(0, 150)    // Левая точка
    };

    // Создаем кастомный виджет для отрисовки полигона
    PolygonWidget polygonWidget(points);
    polygonWidget.setParent(&window); // Устанавливаем родительское окно
    polygonWidget.setGeometry(0, 0, 400, 400); // Устанавливаем размер виджета

    // Показываем окно
    window.show();

    return app.exec();
}
```

### Объяснение кода:
1. **PolygonWidget** — это кастомный виджет, который принимает вектор точек (`std::vector<QPoint>`) в конструкторе.
2. В методе `paintEvent` мы используем `QPainter` для отрисовки полигона. Точки из вектора преобразуются в `QPolygon`.
3. Полигон рисуется с помощью метода `drawPolygon`.
4. Вектор точек задается вручную в `main`, но вы можете динамически изменять его в зависимости от ваших нужд.
5. Виджет добавляется в главное окно (`QWidget`), и его размеры настраиваются с помощью `setGeometry`.

### Как собрать проект:
1. Убедитесь, что у вас установлен Qt (например, через Qt Creator или вручную).
2. Создайте файл `.pro` для сборки проекта:
   ```plaintext
   QT += widgets
   SOURCES += main.cpp
   ```
3. Соберите проект с помощью `qmake` и `make`:
   ```bash
   qmake
   make
   ```
4. Запустите программу.

### Результат:
Программа создаст окно с пятиугольником (полигоном), который будет отрисован на основе заданных точек. Вы можете изменить точки в векторе `points`, чтобы создать другой полигон.

---

### Динамическое изменение полигона
Если вам нужно динамически изменять полигон (например, добавлять или удалять точки), вы можете добавить метод для обновления вектора точек и вызвать `update()` для перерисовки виджета:

```cpp
class PolygonWidget : public QWidget {
public:
    explicit PolygonWidget(QWidget* parent = nullptr) : QWidget(parent) {}

    // Метод для обновления точек полигона
    void setPoints(const std::vector<QPoint>& points) {
        polygonPoints = points;
        update(); // Перерисовываем виджет
    }

protected:
    void paintEvent(QPaintEvent* event) override {
        Q_UNUSED(event);

        QPainter painter(this);
        painter.setPen(Qt::black);
        painter.setBrush(Qt::blue);

        QPolygon polygon;
        for (const auto& point : polygonPoints) {
            polygon << point;
        }

        painter.drawPolygon(polygon);
    }

private:
    std::vector<QPoint> polygonPoints;
};
```

Теперь вы можете вызывать `setPoints` для обновления полигона в runtime. Например:

```cpp
polygonWidget.setPoints(newPoints); // newPoints — новый вектор точек
```