Конечно, давайте создадим `QTabWidget`, который будет работать с `QVector`, содержащим структуры `{QPointF point, double speed}`. Мы создадим два вкладочных виджета: один для отображения данных по скорости и другой для отображения точек.

1. **Создание структуры для хранения данных**:
```cpp
struct DataPoint {
    QPointF point;
    double speed;
};
```

2. **Создание вкладочных виджетов**:
```cpp
#include <QApplication>
#include <QTabWidget>
#include <QWidget>
#include <QVBoxLayout>
#include <QLabel>
#include <QVector>

struct DataPoint {
    QPointF point;
    double speed;
};

class SpeedWidget : public QWidget {
public:
    SpeedWidget(const QVector<DataPoint> &data, QWidget *parent = nullptr) : QWidget(parent) {
        QVBoxLayout *layout = new QVBoxLayout(this);
        for (const DataPoint &dp : data) {
            layout->addWidget(new QLabel(QString("Speed: %1 м/с").arg(dp.speed)));
        }
        setLayout(layout);
    }
};

class PointsWidget : public QWidget {
public:
    PointsWidget(const QVector<DataPoint> &data, QWidget *parent = nullptr) : QWidget(parent) {
        QVBoxLayout *layout = new QVBoxLayout(this);
        for (const DataPoint &dp : data) {
            layout->addWidget(new QLabel(QString("Point: (%1, %2)").arg(dp.point.x()).arg(dp.point.y())));
        }
        setLayout(layout);
    }
};

class TabWidget : public QTabWidget {
public:
    TabWidget(const QVector<DataPoint> &data, QWidget *parent = nullptr)
        : QTabWidget(parent) {
        addTab(new SpeedWidget(data), "Скорости");
        addTab(new PointsWidget(data), "Точки");
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QVector<DataPoint> data = {
        {QPointF(1.0, 1.0), 10.5},
        {QPointF(2.5, 3.8), 20.3},
        {QPointF(4.1, 0.7), 15.7}
    };

    TabWidget tabWidget(data);
    tabWidget.show();

    return app.exec();
}
```

В этом примере:

1. **DataPoint**: Мы создаем структуру `DataPoint`, которая содержит точку (`QPointF`) и скорость (`double`).

2. **SpeedWidget**: Этот виджет отображает данные по скоростям, используя `QVector<DataPoint>` и создавая `QLabel` для каждой скорости.

3. **PointsWidget**: Этот виджет отображает точки, используя `QVector<DataPoint>` и создавая `QLabel` для каждой точки.

4. **TabWidget**: Основной вкладочный виджет (`QTabWidget`), который содержит два виджета: `SpeedWidget` и `PointsWidget`.

5. **main**: В функции `main` создается пример данных `QVector<DataPoint>`, создается `TabWidget` и отображается.

Теперь ваш `QTabWidget` будет отображать как скорости, так и точки, используя структуру данных `DataPoint`.

Если у вас есть дополнительные вопросы или нужны уточнения, дайте знать!