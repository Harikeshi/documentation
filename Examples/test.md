Конечно, можно сделать так, чтобы время отображалось в днях, часах и минутах в зависимости от величины расстояния и скорости. Мы модифицируем функцию `timerEvent`, чтобы она корректно отображала время в нужном формате.

Вот обновленный код:

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QWidget>
#include <QPainter>
#include <QTimer>
#include <QVBoxLayout>
#include <QLabel>

class LineWidget : public QWidget {
public:
    LineWidget(QWidget *parent = nullptr) : QWidget(parent), pointPosition(0, 0), timeElapsed(0) {
        setMinimumSize(400, 400);

        // Инициализация метки для отображения времени
        timeLabel = new QLabel(this);

        QVBoxLayout *layout = new QVBoxLayout(this);
        layout->addWidget(timeLabel);
        setLayout(layout);

        startTimer(100); // 100 мс интервал таймера
    }

protected:
    void paintEvent(QPaintEvent *event) override {
        QWidget::paintEvent(event);

        QPainter painter(this);

        // Рисуем прямую от (0, 0) до (400, 400)
        painter.drawLine(0, 0, 400, 400);

        // Рисуем движущуюся точку
        painter.setBrush(Qt::red);
        painter.drawEllipse(pointPosition, 5, 5);
    }

    void timerEvent(QTimerEvent *event) override {
        // Увеличиваем время
        timeElapsed += 0.1; // 0.1 минут

        // Переводим время в дни, часы и минуты
        int totalMinutes = static_cast<int>(timeElapsed);
        int days = totalMinutes / (60 * 24);
        int hours = (totalMinutes % (60 * 24)) / 60;
        int minutes = totalMinutes % 60;

        // Обновляем текст метки времени
        timeLabel->setText(QString("Время: %1 дней %2 часов %3 минут").arg(days).arg(hours).arg(minutes));

        // Вычисляем новую позицию точки
        double distance = timeElapsed * 10; // 10 метров в минуту
        double proportion = distance / 10000.0; // Доля от общей длины прямой
        pointPosition.setX(proportion * 400); // Приводим к экранным координатам
        pointPosition.setY(proportion * 400);

        update();
    }

private:
    QPointF pointPosition;
    double timeElapsed; // Время в минутах
    QLabel *timeLabel;  // Метка для отображения времени
};

class MainWindow : public QMainWindow {
public:
    MainWindow() {
        QWidget *centralWidget = new QWidget(this);

        QVBoxLayout *layout = new QVBoxLayout(centralWidget);

        QLabel *label = new QLabel("Движение точки по прямой", this);
        layout->addWidget(label);

        LineWidget *lineWidget = new LineWidget(this);
        layout->addWidget(lineWidget);

        setCentralWidget(centralWidget);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    MainWindow mainWindow;
    mainWindow.show();

    return app.exec();
}
```

В этом обновленном коде:
1. Мы пересчитываем общее количество минут в дни, часы и минуты.
2. Обновляем текст метки `timeLabel` для отображения времени в формате "дни, часы, минуты".

Теперь, если расстояние большое, а скорость маленькая, время будет отображаться в формате дней, часов и минут, что будет соответствовать реальному движению точки по прямой.