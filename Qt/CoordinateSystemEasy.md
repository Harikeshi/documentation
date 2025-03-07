```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QVector>
#include <QPolygonF>
#include <QLineF>
#include <QPointF>
#include <algorithm>

class CoordinateSystem {
public:
    CoordinateSystem(const QVector<QPolygonF> &polygons) {
        // Вычисление минимальных и максимальных значений X и Y
        for (const QPolygonF &polygon : polygons) {
            for (const QPointF &point : polygon) {
                minX = std::min(minX, point.x());
                minY = std::min(minY, point.y());
                maxX = std::max(maxX, point.x());
                maxY = std::max(maxY, point.y());
            }
        }
    }

    void applyTransformation(QPainter &painter, int widgetWidth, int widgetHeight) const {
        double scaleX = widgetWidth / (maxX - minX);
        double scaleY = widgetHeight / (maxY - minY);

        // Сдвиг начала координат в точку (minX, minY) и переворот изображения
        painter.translate(-minX * scaleX, widgetHeight + minY * scaleY);
        painter.scale(scaleX, -scaleY);
    }

private:
    double minX = std::numeric_limits<double>::max();
    double minY = std::numeric_limits<double>::max();
    double maxX = std::numeric_limits<double>::lowest();
    double maxY = std::numeric_limits<double>::lowest();
};

class ImageWidget : public QWidget {
public:
    ImageWidget(QVector<QPolygonF> polygons, QVector<QPointF> points, QVector<QLineF> lines, QWidget *parent = nullptr)
        : QWidget(parent), polygons(polygons), points(points), lines(lines), coordSystem(polygons) {}

protected:
    void paintEvent(QPaintEvent *event) override {
        QPainter painter(this);
        coordSystem.applyTransformation(painter, width(), height());

        // Отрисовка полигонов
        for (const QPolygonF &polygon : polygons) {
            painter.drawPolygon(polygon);
        }

        // Отрисовка точек
        painter.setPen(Qt::red);
        for (const QPointF &point : points) {
            painter.drawEllipse(point, 3, 3); // Радиус точки 3 пикселя
        }

        // Отрисовка линий
        painter.setPen(Qt::blue);
        for (const QLineF &line : lines) {
            painter.drawLine(line);
        }
    }

private:
    QVector<QPolygonF> polygons;
    QVector<QPointF> points;
    QVector<QLineF> lines;
    CoordinateSystem coordSystem;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QVector<QPolygonF> polygons = {
        {QPointF(0, 0), QPointF(1, 2), QPointF(2, 1)},
        {QPointF(3, 3), QPointF(4, 5), QPointF(5, 4)}
    };

    QVector<QPointF> points = {
        {QPointF(1, 1)}, {QPointF(2, 2)}, {QPointF(3, 4)}
    };

    QVector<QLineF> lines = {
        {QPointF(0, 0), QPointF(1, 1)},
        {QPointF(2, 2), QPointF(3, 3)}
    };

    ImageWidget widget(polygons, points, lines);
    widget.show();

    return app.exec();
}
```
