Для моделирования движения объекта по пути с использованием вектора (`Vector2D`), включая движение вперед, повороты на заданный угол и остановку, можно создать класс `Vector2D`, который будет представлять позицию и направление объекта. Затем мы добавим методы для управления движением и поворотами.

---

### Полный код программы

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QVBoxLayout>
#include <QPushButton>
#include <QWidget>
#include <QPainter>
#include <QTimer>
#include <cmath>

// Класс Vector2D для представления позиции и направления
class Vector2D {
public:
    Vector2D(double x = 0, double y = 0) : x(x), y(y) {}

    // Оператор сложения векторов
    Vector2D operator+(const Vector2D& other) const {
        return Vector2D(x + other.x, y + other.y);
    }

    // Оператор умножения на скаляр
    Vector2D operator*(double scalar) const {
        return Vector2D(x * scalar, y * scalar);
    }

    // Нормализация вектора
    Vector2D normalized() const {
        double length = std::sqrt(x * x + y * y);
        if (length > 0) {
            return Vector2D(x / length, y / length);
        }
        return *this;
    }

    // Поворот вектора на заданный угол (в радианах)
    Vector2D rotated(double angle) const {
        double cosA = std::cos(angle);
        double sinA = std::sin(angle);
        return Vector2D(x * cosA - y * sinA, x * sinA + y * cosA);
    }

    double x, y;
};

// Кастомный QWidget для отрисовки пути и объекта
class PathWidget : public QWidget {
    Q_OBJECT

public:
    PathWidget(QWidget* parent = nullptr)
        : QWidget(parent), position(400, 300), direction(1, 0), speed(0), angle(0) {
        timer = new QTimer(this);
        connect(timer, &QTimer::timeout, this, &PathWidget::updatePosition);
        timer->start(16); // ~60 FPS
    }

    // Управление движением
    void moveForward() {
        speed = 2.0; // Скорость движения вперед
    }

    void stop() {
        speed = 0.0; // Остановка
    }

    void turnLeft() {
        angle -= 0.1; // Поворот влево на 0.1 радиан (~5.7 градусов)
    }

    void turnRight() {
        angle += 0.1; // Поворот вправо на 0.1 радиан (~5.7 градусов)
    }

protected:
    void paintEvent(QPaintEvent* event) override {
        Q_UNUSED(event);
        QPainter painter(this);
        painter.setRenderHint(QPainter::Antialiasing);

        // Отрисовка объекта
        painter.setPen(Qt::blue);
        painter.setBrush(Qt::blue);
        painter.drawEllipse(QPointF(position.x, position.y), 10, 10);

        // Отрисовка направления
        Vector2D endPoint = position + direction * 30;
        painter.drawLine(QPointF(position.x, position.y), QPointF(endPoint.x, endPoint.y));
    }

private slots:
    void updatePosition() {
        if (speed != 0) {
            // Обновляем направление с учетом угла поворота
            direction = direction.rotated(angle).normalized();
            // Обновляем позицию
            position = position + direction * speed;
            angle = 0; // Сбрасываем угол после поворота
        }
        update(); // Перерисовываем виджет
    }

private:
    Vector2D position; // Позиция объекта
    Vector2D direction; // Направление движения
    double speed; // Скорость движения
    double angle; // Угол поворота
    QTimer* timer;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QMainWindow window;
    QWidget* centralWidget = new QWidget(&window);
    QVBoxLayout* layout = new QVBoxLayout(centralWidget);

    // Создаем виджет для отрисовки пути и объекта
    PathWidget* pathWidget = new PathWidget(centralWidget);
    layout->addWidget(pathWidget);

    // Кнопки для управления
    QPushButton* moveForwardButton = new QPushButton("Движение вперед", centralWidget);
    QPushButton* stopButton = new QPushButton("Остановка", centralWidget);
    QPushButton* turnLeftButton = new QPushButton("Поворот влево", centralWidget);
    QPushButton* turnRightButton = new QPushButton("Поворот вправо", centralWidget);

    layout->addWidget(moveForwardButton);
    layout->addWidget(stopButton);
    layout->addWidget(turnLeftButton);
    layout->addWidget(turnRightButton);

    // Подключаем кнопки к слотам
    QObject::connect(moveForwardButton, &QPushButton::clicked, [pathWidget]() {
        pathWidget->moveForward();
    });

    QObject::connect(stopButton, &QPushButton::clicked, [pathWidget]() {
        pathWidget->stop();
    });

    QObject::connect(turnLeftButton, &QPushButton::clicked, [pathWidget]() {
        pathWidget->turnLeft();
    });

    QObject::connect(turnRightButton, &QPushButton::clicked, [pathWidget]() {
        pathWidget->turnRight();
    });

    window.setCentralWidget(centralWidget);
    window.resize(800, 600);
    window.show();

    return app.exec();
}

#include "main.moc"
```

---

### Как работает программа

1. **Vector2D**:
   - Класс для представления позиции и направления объекта.
   - Включает методы для сложения векторов, умножения на скаляр, нормализации и поворота.

2. **PathWidget**:
   - Кастомный `QWidget`, который отрисовывает объект и его направление.
   - Использует `Vector2D` для управления позицией и направлением.
   - Включает методы для движения вперед, остановки и поворотов.

3. **Управление**:
   - Кнопки позволяют управлять движением объекта:
     - **Движение вперед**: Устанавливает скорость движения.
     - **Остановка**: Останавливает движение.