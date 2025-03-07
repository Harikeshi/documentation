Вот полная программа, реализующая управление отрисовкой пути в `QWidget` с использованием паттерна **Состояние**, где весь код находится в одном файле для удобства.

---

### Полный код программы в одном файле

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QVBoxLayout>
#include <QPushButton>
#include <QWidget>
#include <QPainter>
#include <QVector>
#include <QPoint>
#include <memory>

// Абстрактное состояние отрисовки
class DrawingState {
public:
    virtual void draw(QPainter& painter, const QVector<QPoint>& path, int currentPointIndex) = 0;
    virtual ~DrawingState() = default;
};

// Состояние: Отрисовка движущейся точки
class MovingPointState : public DrawingState {
public:
    void draw(QPainter& painter, const QVector<QPoint>& path, int currentPointIndex) override {
        if (currentPointIndex >= 0 && currentPointIndex < path.size()) {
            painter.setPen(Qt::red);
            painter.drawEllipse(path[currentPointIndex], 5, 5); // Рисуем движущуюся точку
        }
    }
};

// Состояние: Отрисовка всего пути
class FullPathState : public DrawingState {
public:
    void draw(QPainter& painter, const QVector<QPoint>& path, int currentPointIndex) override {
        painter.setPen(Qt::blue);
        for (int i = 1; i < path.size(); ++i) {
            painter.drawLine(path[i - 1], path[i]); // Рисуем линии между точками
        }
    }
};

// Кастомный QWidget для отрисовки пути
class PathWidget : public QWidget {
    Q_OBJECT

public:
    PathWidget(QWidget* parent = nullptr)
        : QWidget(parent), currentPointIndex(-1) {
        setState(std::make_unique<MovingPointState>()); // Начальное состояние
    }

    void setState(std::unique_ptr<DrawingState> newState) {
        state = std::move(newState);
        update(); // Перерисовываем виджет
    }

    void addPoint(const QPoint& point) {
        path.append(point);
        if (currentPointIndex == -1) {
            currentPointIndex = 0; // Начинаем с первой точки
        }
        update(); // Перерисовываем виджет
    }

    void moveToNextPoint() {
        if (currentPointIndex + 1 < path.size()) {
            currentPointIndex++;
        } else {
            currentPointIndex = 0; // Сбрасываем на начало
        }
        update(); // Перерисовываем виджет
    }

protected:
    void paintEvent(QPaintEvent* event) override {
        Q_UNUSED(event);
        QPainter painter(this);
        painter.setRenderHint(QPainter::Antialiasing);

        if (state) {
            state->draw(painter, path, currentPointIndex);
        }
    }

private:
    std::unique_ptr<DrawingState> state;
    QVector<QPoint> path;
    int currentPointIndex;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QMainWindow window;
    QWidget* centralWidget = new QWidget(&window);
    QVBoxLayout* layout = new QVBoxLayout(centralWidget);

    // Создаем виджет для отрисовки пути
    PathWidget* pathWidget = new PathWidget(centralWidget);
    layout->addWidget(pathWidget);

    // Добавляем точки пути
    pathWidget->addPoint(QPoint(50, 50));
    pathWidget->addPoint(QPoint(100, 100));
    pathWidget->addPoint(QPoint(150, 50));
    pathWidget->addPoint(QPoint(200, 100));

    // Кнопки для управления
    QPushButton* movingPointButton = new QPushButton("Движущаяся точка", centralWidget);
    QPushButton* fullPathButton = new QPushButton("Весь путь", centralWidget);
    QPushButton* nextPointButton = new QPushButton("Следующая точка", centralWidget);

    layout->addWidget(movingPointButton);
    layout->addWidget(fullPathButton);
    layout->addWidget(nextPointButton);

    // Подключаем кнопки к слотам
    QObject::connect(movingPointButton, &QPushButton::clicked, [pathWidget]() {
        pathWidget->setState(std::make_unique<MovingPointState>());
    });

    QObject::connect(fullPathButton, &QPushButton::clicked, [pathWidget]() {
        pathWidget->setState(std::make_unique<FullPathState>());
    });

    QObject::connect(nextPointButton, &QPushButton::clicked, [pathWidget]() {
        pathWidget->moveToNextPoint();
    });

    window.setCentralWidget(centralWidget);
    window.resize(400, 300);
    window.show();

    return app.exec();
}

#include "main.moc"
```

---

### Как работает программа

1. **PathWidget**:
   - Это кастомный `QWidget`, который отрисовывает путь с использованием `QPainter`.
   - Он хранит путь (`QVector<QPoint>`) и текущую точку (`currentPointIndex`).
   - В зависимости от текущего состояния (`MovingPointState` или `FullPathState`), он отрисовывает либо движущуюся точку, либо весь путь.

2. **Состояния**:
   - `MovingPointState`: Рисует только текущую точку.
   - `FullPathState`: Рисует весь путь.

3. **Управление**:
   - Кнопки позволяют переключать режимы отрисовки и перемещать точку по пути.

---

### Сборка и запуск

1. Убедитесь, что у вас установлен Qt (например, через `apt-get install qt5-default` на Linux или через установщик Qt на Windows/Mac).
2. Сохраните код в файл `main.cpp`.
3. Соберите программу с помощью команды:
   ```bash
   qmake -project
   qmake
   make
   ```
4. Запустите программу:
   ```bash
   ./PathDrawer
   ```

---

### Итог

Программа демонстрирует:
- Использование паттерна **Состояние** для управления отрисовкой в `QWidget`.
- Переключение между режимами отрисовки (движущаяся точка и весь путь).
- Удобное управление через кнопки в графическом интерфейсе.