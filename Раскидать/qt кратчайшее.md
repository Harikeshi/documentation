### 1. **Установка Qt**
Убедитесь, что у вас установлен Qt и настроена среда разработки (например, Qt Creator).

---

### 2. **Структура программы**
- **Графический интерфейс**: Отображает полигоны, точки и путь.
- **Логика**: Реализует алгоритм поиска пути с использованием Boost.Geometry или встроенных функций Qt для геометрических вычислений.

---

### 3. **Пример кода**

#### a) **main.cpp**
```cpp
#include <QApplication>
#include "mainwindow.h"

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    MainWindow window;
    window.show();
    return app.exec();
}
```

#### b) **mainwindow.h**
```cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QPainter>
#include <QPolygonF>
#include <QVector>
#include <QPointF>
#include <QQueue>
#include <QPair>
#include <QDebug>

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

protected:
    void paintEvent(QPaintEvent *event) override;

private:
    // Полигоны
    QPolygonF outerPolygon;
    QVector<QPolygonF> innerPolygons;

    // Точки начала и конца
    QPointF startPoint;
    QPointF endPoint;

    // Кратчайший путь
    QVector<QPointF> shortestPath;

    // Функции
    void generateRandomPoints();
    bool isEdgeValid(const QPointF &p1, const QPointF &p2) const;
    QVector<QPointF> findShortestPath();
};

#endif // MAINWINDOW_H
```

#### c) **mainwindow.cpp**
```cpp
#include "mainwindow.h"
#include <QtMath>
#include <QPainterPath>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent) {
    // Задаем внешний полигон
    outerPolygon << QPointF(100, 100) << QPointF(500, 100) << QPointF(500, 500) << QPointF(100, 500);

    // Задаем внутренние полигоны
    QPolygonF innerPolygon1;
    innerPolygon1 << QPointF(200, 200) << QPointF(300, 200) << QPointF(300, 300) << QPointF(200, 300);
    innerPolygons.append(innerPolygon1);

    // Генерация случайных точек
    generateRandomPoints();

    // Поиск кратчайшего пути
    shortestPath = findShortestPath();
}

MainWindow::~MainWindow() {}

void MainWindow::paintEvent(QPaintEvent *event) {
    Q_UNUSED(event);
    QPainter painter(this);

    // Рисуем внешний полигон
    painter.setPen(Qt::black);
    painter.setBrush(Qt::white);
    painter.drawPolygon(outerPolygon);

    // Рисуем внутренние полигоны
    painter.setBrush(Qt::gray);
    for (const auto &polygon : innerPolygons) {
        painter.drawPolygon(polygon);
    }

    // Рисуем точки начала и конца
    painter.setPen(Qt::red);
    painter.setBrush(Qt::red);
    painter.drawEllipse(startPoint, 5, 5);
    painter.drawEllipse(endPoint, 5, 5);

    // Рисуем кратчайший путь
    painter.setPen(Qt::blue);
    for (int i = 1; i < shortestPath.size(); ++i) {
        painter.drawLine(shortestPath[i - 1], shortestPath[i]);
    }
}

void MainWindow::generateRandomPoints() {
    // Генерация случайных точек на внешнем полигоне
    qsrand(static_cast<uint>(QTime::currentTime().msec()));
    int index1 = qrand() % outerPolygon.size();
    int index2 = qrand() % outerPolygon.size();
    while (index2 == index1) {
        index2 = qrand() % outerPolygon.size();
    }
    startPoint = outerPolygon[index1];
    endPoint = outerPolygon[index2];
}

bool MainWindow::isEdgeValid(const QPointF &p1, const QPointF &p2) const {
    QLineF edge(p1, p2);
    for (const auto &polygon : innerPolygons) {
        for (int i = 0; i < polygon.size(); ++i) {
            QLineF polygonEdge(polygon[i], polygon[(i + 1) % polygon.size()]);
            if (edge.intersect(polygonEdge, nullptr) == QLineF::BoundedIntersection) {
                return false;
            }
        }
    }
    return true;
}

QVector<QPointF> MainWindow::findShortestPath() {
    // Алгоритм Дейкстры
    QVector<double> dist(outerPolygon.size(), std::numeric_limits<double>::max());
    QVector<int> prev(outerPolygon.size(), -1);
    QQueue<QPair<int, double>> queue;

    // Находим индексы стартовой и конечной точек
    int startIndex = outerPolygon.indexOf(startPoint);
    int endIndex = outerPolygon.indexOf(endPoint);

    dist[startIndex] = 0;
    queue.enqueue(qMakePair(startIndex, 0));

    while (!queue.isEmpty()) {
        auto [u, currentDist] = queue.dequeue();

        if (u == endIndex) {
            break;
        }

        for (int v = 0; v < outerPolygon.size(); ++v) {
            if (u != v) {
                double weight = QLineF(outerPolygon[u], outerPolygon[v]).length();
                if (isEdgeValid(outerPolygon[u], outerPolygon[v]) {
                    if (dist[v] > dist[u] + weight) {
                        dist[v] = dist[u] + weight;
                        prev[v] = u;
                        queue.enqueue(qMakePair(v, dist[v]));
                    }
                }
            }
        }
    }

    // Восстановление пути
    QVector<QPointF> path;
    for (int at = endIndex; at != -1; at = prev[at]) {
        path.append(outerPolygon[at]);
    }
    std::reverse(path.begin(), path.end());

    return path;
}
```

---

### 4. **Объяснение кода**

1. **Графический интерфейс**:
   - Используется `QPainter` для отрисовки полигонов, точек и пути.
   - Внешний полигон и внутренние полигоны задаются как `QPolygonF`.

2. **Генерация случайных точек**:
   - Точки начала и конца выбираются случайным образом на внешнем полигоне.

3. **Проверка пересечений**:
   - Используется метод `QLineF::intersect` для проверки пересечения ребра с внутренними полигонами.

4. **Алгоритм Дейкстры**:
   - Реализован для поиска кратчайшего пути на графе, где вершины — это точки внешнего полигона.

5. **Восстановление пути**:
   - Путь восстанавливается с помощью массива `prev`.

---

### 5. **Запуск программы**
1. Создайте проект в Qt Creator.
2. Добавьте файлы `main.cpp`, `mainwindow.h` и `mainwindow.cpp`.
3. Соберите и запустите проект.

---

### 6. **Результат**
- Программа отображает внешний полигон, внутренние полигоны, точки начала и конца, а также кратчайший путь между ними, который не пересекает внутренние полигоны.

---

Этот код является базовым примером. Для более сложных случаев можно использовать дополнительные оптимизации, такие как пространственные индексы или алгоритм A*.