```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QVector>
#include <QPolygonF>
#include <algorithm>

#include <limits>

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
    ImageWidget(QVector<QPolygonF> polygons, QWidget *parent = nullptr)
        : QWidget(parent), polygons(polygons), coordSystem(polygons) {}

protected:
    void paintEvent(QPaintEvent *event) override {
        QPainter painter(this);
        coordSystem.applyTransformation(painter, width(), height());

        // Отрисовка полигонов
        for (const QPolygonF &polygon : polygons) {
            painter.drawPolygon(polygon);
        }
    }

private:
    QVector<QPolygonF> polygons;
    CoordinateSystem coordSystem;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QPolygonF outer{QVector{QPointF{-10,0},QPointF{0, 30},QPointF{30,30},QPointF{30, 0}}};
    QPolygonF inner{QVector{QPointF{5,5},QPointF{5, 7},QPointF{10,7},QPointF{10, 5}}};

    QVector<QPolygonF> polygons;
    polygons.push_back(outer);
    polygons.push_back(inner);
    // = {
    //     {QPointF(0, 0), QPointF(1, 2), QPointF(2, 1)},
    //     {QPointF(3, 3), QPointF(4, 5), QPointF(5, 4)}
    // };

    ImageWidget widget(polygons);
    widget.show();

    return app.exec();
}
```