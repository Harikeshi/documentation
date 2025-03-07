```cpp
#include <QLineF>
#include <QPointF>
#include <QDebug>
#include <cmath>

// Функция для поворота вектора на заданный угол (в радианах)
QPointF rotateVector(const QPointF& vector, double angle) {
    double cosA = std::cos(angle);
    double sinA = std::sin(angle);
    return QPointF(
        vector.x() * cosA - vector.y() * sinA,
        vector.x() * sinA + vector.y() * cosA
    );
}

int main() {
    // Исходный отрезок
    QLineF originalLine(QPointF(0, 0), QPointF(100, 0)); // Горизонтальный отрезок

    // Направляющий вектор исходного отрезка
    QPointF direction = originalLine.p2() - originalLine.p1();

    // Поворачиваем вектор на 1.5 радианов влево
    QPointF leftDirection = rotateVector(direction, 1.5);

    // Поворачиваем вектор на 1.5 радианов вправо
    QPointF rightDirection = rotateVector(direction, -1.5);

    // Создаем новые отрезки
    QLineF leftLine(originalLine.p2(), originalLine.p2() + leftDirection);
    QLineF rightLine(originalLine.p2(), originalLine.p2() + rightDirection);

    // Выводим результаты
    qDebug() << "Исходный отрезок:" << originalLine;
    qDebug() << "Отрезок, отложенный влево на 1.5 радианов:" << leftLine;
    qDebug() << "Отрезок, отложенный вправо на 1.5 радианов:" << rightLine;

    return 0;
}
```