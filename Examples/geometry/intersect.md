```cpp
#include <QLineF>
#include <QPolygonF>
#include <QDebug>
#include <cmath>
#include <limits>

int main() {
    // Основной отрезок
    QLineF mainLine(QPointF(50, 50), QPointF(400, 400));

    // Полигон (например, треугольник)
    QPolygonF polygon;
    polygon << QPointF(100, 100) << QPointF(300, 150) << QPointF(200, 300) << QPointF(100, 100);

    // Находим точки пересечения и выбираем ближайшую
    QPointF closestIntersectionPoint;
    double minDistance = std::numeric_limits<double>::max();

    for (int i = 0; i < polygon.size() - 1; ++i) {
        QLineF polygonEdge(polygon[i], polygon[i + 1]);

        QPointF intersectionPoint;
        QLineF::IntersectType intersectType = mainLine.intersect(polygonEdge, &intersectionPoint);

        if (intersectType == QLineF::BoundedIntersection) {
            double distance = QLineF(mainLine.p1(), intersectionPoint).length();
            if (distance < minDistance) {
                minDistance = distance;
                closestIntersectionPoint = intersectionPoint;
            }
        }
    }

    // Выводим результат
    if (minDistance != std::numeric_limits<double>::max()) {
        qDebug() << "Ближайшая точка пересечения:" << closestIntersectionPoint;
        qDebug() << "Расстояние до точки:" << minDistance;
    } else {
        qDebug() << "Пересечений не найдено.";
    }

    return 0;
}
```