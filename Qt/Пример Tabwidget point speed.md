Для создания `QTabWidget`, который будет получать данные по скорости и точкам из `QVector`, мы можем использовать два вкладочных виджета: один для отображения данных по скорости и другой для отображения точек. Вот пример, как это можно сделать:

1. **Создание вкладочного виджета (QTabWidget)**:
```cpp
#include <QApplication>
#include <QTabWidget>
#include <QWidget>
#include <QVBoxLayout>
#include <QLabel>
#include <QVector>

class SpeedWidget : public QWidget {
public:
    SpeedWidget(const QVector<double> &speeds, QWidget *parent = nullptr) : QWidget(parent) {
        QVBoxLayout *layout = new QVBoxLayout(this);
        for (double speed : speeds) {
            layout->addWidget(new QLabel(QString::number(speed) + " м/с"));
        }
        setLayout(layout);
    }
};

class PointsWidget : public QWidget {
public:
    PointsWidget(const QVector<QPointF> &points, QWidget *parent = nullptr) : QWidget(parent) {
        QVBoxLayout *layout = new QVBoxLayout(this);
        for (const QPointF &point : points) {
            layout->addWidget(new QLabel(QString("(%1, %2)").arg(point.x()).arg(point.y())));
        }
        setLayout(layout);
    }
};

class TabWidget : public QTabWidget {
public:
    TabWidget(const QVector<double> &speeds, const QVector<QPointF> &points, QWidget *parent = nullptr) 
        : QTabWidget(parent) {
        addTab(new SpeedWidget(speeds), "Скорости");
        addTab(new PointsWidget(points), "Точки");
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QVector<double> speeds = {10.5, 20.3, 15.7};
    QVector<QPointF> points = {{1.0, 1.0}, {2.5, 3.8}, {4.1, 0.7}};

    TabWidget tabWidget(speeds, points);
    tabWidget.show();

    return app.exec();
}
```

В этом примере:

1. **SpeedWidget**: Этот виджет отображает данные по скоростям. Он принимает `QVector<double>` и создает `QLabel` для каждой скорости, отображая ее значение в м/с.

2. **PointsWidget**: Этот виджет отображает точки. Он принимает `QVector<QPointF>` и создает `QLabel` для каждой точки, отображая ее координаты.

3. **TabWidget**: Основной вкладочный виджет (`QTabWidget`), который содержит два виджета: `SpeedWidget` и `PointsWidget`.

4. **main**: В функции `main` создаются примеры данных для скоростей и точек, создается `TabWidget` и отображается.

Вы можете расширить этот пример, добавив больше функциональности, например, динамическое обновление данных или взаимодействие с пользователем.

Если у вас есть дополнительные вопросы или нужны уточнения, дайте знать!