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

    void applyTransformation(QPainter &painter, const QRect &contentRect) const {
        double scaleX = contentRect.width() / (maxX - minX);
        double scaleY = contentRect.height() / (maxY - minY);

        // Сдвиг и масштабирование с учетом contentRect
        painter.translate(contentRect.left() - minX * scaleX,
                          contentRect.bottom() + minY * scaleY);
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
    ImageWidget(QVector<QPolygonF> polygons,
                QVector<QPointF> points,
                QVector<QLineF> lines,
                QWidget *parent = nullptr)
        : QWidget(parent),
          polygons(std::move(polygons)),
          points(std::move(points)),
          lines(std::move(lines)),
          coordSystem(this->polygons) {}

protected:
    void paintEvent(QPaintEvent *event) override {
        QPainter painter(this);
        // Используем contentsRect для трансформации
        coordSystem.applyTransformation(painter, contentsRect());

        // Отрисовка полигонов
        painter.setPen(Qt::black);
        for (const QPolygonF &polygon : polygons) {
            painter.drawPolygon(polygon);
        }

        // Отрисовка точек с фиксированным размером в пикселях
        painter.resetTransform(); // Сброс трансформации для пиксельных операций
        painter.setPen(Qt::red);
        for (const QPointF &point : points) {
            QPointF transformedPoint = mapToWidget(point);
            painter.drawEllipse(transformedPoint, 3, 3);
        }

        // Отрисовка линий
        painter.setPen(Qt::blue);
        painter.setTransform(coordSystem.getTransform(contentsRect()), true);
        for (const QLineF &line : lines) {
            painter.drawLine(line);
        }
    }

private:
    QPointF mapToWidget(const QPointF &point) const {
        double scaleX = contentsRect().width() / (coordSystem.maxX - coordSystem.minX);
        double scaleY = contentsRect().height() / (coordSystem.maxY - coordSystem.minY);

        double x = (point.x() - coordSystem.minX) * scaleX + contentsRect().left();
        double y = contentsRect().bottom() - (point.y() - coordSystem.minY) * scaleY;

        return QPointF(x, y);
    }

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
        QPointF(1, 1), QPointF(2, 2), QPointF(3, 4)
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