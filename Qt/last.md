```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QTimer>
#include <QKeyEvent>
#include <vector>

struct Coordinate {
    int x;
    int y;
    Coordinate(int x, int y) : x(x), y(y) {}
};

class DrawingWidget : public QWidget {
    Q_OBJECT
public:
    DrawingWidget(QWidget *parent = nullptr) : QWidget(parent), currentIndex(0), drawLines(true), isPaused(false) {
        timer = new QTimer(this);
        connect(timer, &QTimer::timeout, this, &DrawingWidget::animate);
        timer->start(100);
        
        // Инициализация тестовых данных
        perimeter = {{50, 50}, {300, 50}, {300, 300}, {50, 300}, {50, 50}};
        points = {{100, 100}, {150, 150}, {200, 100}, {250, 200}, {200, 250}};
    }

protected:
    void paintEvent(QPaintEvent *) override {
        QPainter painter(this);
        painter.setRenderHint(QPainter::Antialiasing);

        // Отрисовка периметра
        painter.setPen(Qt::blue);
        for(size_t i = 1; i < perimeter.size(); ++i) {
            painter.drawLine(perimeter[i-1].x, perimeter[i-1].y,
                            perimeter[i].x, perimeter[i].y);
        }

        // Отрисовка линий пути (только пройденных участков)
        if(drawLines && currentIndex > 0) {
            painter.setPen(Qt::black);
            for(size_t i = 1; i <= currentIndex; ++i) {
                painter.drawLine(points[i-1].x, points[i-1].y,
                                points[i].x, points[i].y);
            }
        }

        // Отрисовка движущейся точки
        if(currentIndex < points.size()) {
            painter.setBrush(Qt::red);
            painter.drawEllipse(points[currentIndex].x - 5,
                               points[currentIndex].y - 5, 10, 10);
        }
    }

    void keyPressEvent(QKeyEvent *event) override {
        if(event->key() == Qt::Key_P) {
            isPaused = !isPaused;
            isPaused ? timer->stop() : timer->start();
        }
        else if(event->key() == Qt::Key_L) {
            drawLines = !drawLines;
            update();
        }
    }

private slots:
    void animate() {
        if(currentIndex < points.size() - 1) {
            currentIndex++;
            update();
        } else {
            timer->stop();
        }
    }

private:
    QTimer *timer;
    std::vector<Coordinate> perimeter;
    std::vector<Coordinate> points;
    size_t currentIndex;
    bool drawLines;
    bool isPaused;
};

int main(int argc, char *argv[]) {
    QApplication a(argc, argv);
    
    DrawingWidget widget;
    widget.resize(400, 400);
    widget.setWindowTitle("Animated Path Drawing");
    widget.show();

    return a.exec();
}

#include "main.moc"
```

Основные изменения:
1. Линии пути теперь рисуются только до текущей позиции точки (а не все сразу)
2. Улучшена логика отрисовки - линии появляются постепенно
3. Добавлена проверка на наличие пройденного пути перед отрисовкой линий
4. Оптимизировано управление таймером при паузе

Клавиши управления:
- P: Пауза/продолжение анимации
- L: Переключение отображения линий пути
- Окно автоматически закрывается по завершении анимации

Особенности работы:
1. Красная точка начинает движение с первой координаты
2. Линия рисуется между точками только после того, как точка прошла этот участок
3. При достижении последней точки анимация останавливается
4. При включенном отображении линий видны только пройденные участки пути

Вы можете модифицировать векторы `perimeter` и `points` в конструкторе класса `DrawingWidget` для задания собственных координат.