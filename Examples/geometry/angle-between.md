```cpp
#include <QLineF>
#include <QPointF>
#include <QDebug>
#include <cmath>

// Функция для вычисления угла между двумя отрезками (в градусах)
double angleBetweenLines(const QLineF& line1, const QLineF& line2) {
    // Находим направляющие векторы отрезков
    QPointF vector1 = line1.p2() - line1.p1();
    QPointF vector2 = line2.p2() - line2.p1();

    // Вычисляем скалярное произведение векторов
    double dotProduct = vector1.x() * vector2.x() + vector1.y() * vector2.y();

    // Вычисляем длины векторов
    double length1 = std::sqrt(vector1.x() * vector1.x() + vector1.y() * vector1.y());
    double length2 = std::sqrt(vector2.x() * vector2.x() + vector2.y() * vector2.y());

    // Вычисляем косинус угла
    double cosTheta = dotProduct / (length1 * length2);

    // Возвращаем угол в градусах
    return std::acos(cosTheta) * 180.0 / M_PI;
}

int main() {
    // Создаем два отрезка
    QLineF line1(QPointF(0, 0), QPointF(1, 0)); // Горизонтальный отрезок
    QLineF line2(QPointF(0, 0), QPointF(0, 1)); // Вертикальный отрезок

    // Вычисляем угол между отрезками
    double angle = angleBetweenLines(line1, line2);
    qDebug() << "Угол между отрезками:" << angle << "градусов";

    return 0;
}
```