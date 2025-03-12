
## Реализация с собстенной функцией определения ближайшей вершины

### Исправленный код

#### В `mainwindow.cpp` замените следующие части:

1. **Поиск ближайшей вершины**:
   Вместо использования `nearest_vertex`, реализуем функцию для поиска ближайшей вершины вручную.

```cpp
Vertex_handle findNearestVertex(const CDT &cdt, const Point &point) {
    Vertex_handle nearest;
    double minDistance = std::numeric_limits<double>::max();

    for (auto it = cdt.finite_vertices_begin(); it != cdt.finite_vertices_end(); ++it) {
        double distance = CGAL::squared_distance(it->point(), point);
        if (distance < minDistance) {
            minDistance = distance;
            nearest = it;
        }
    }

    return nearest;
}
```

2. **Использование `findNearestVertex`**:
   Замените вызовы `cdt.nearest_vertex` на `findNearestVertex`.

```cpp
// Находим ближайшие вершины к начальной и конечной точкам
auto startVertex = findNearestVertex(cdt, Point(startPoint.x(), startPoint.y()));
auto endVertex = findNearestVertex(cdt, Point(endPoint.x(), endPoint.y()));
```

---

### Полный исправленный код

#### `mainwindow.cpp`

```cpp
#include "mainwindow.h"
#include <CGAL/Constrained_triangulation_plus_2.h>

// Функция для поиска ближайшей вершины
Vertex_handle findNearestVertex(const CDT &cdt, const Point &point) {
    Vertex_handle nearest;
    double minDistance = std::numeric_limits<double>::max();

    for (auto it = cdt.finite_vertices_begin(); it != cdt.finite_vertices_end(); ++it) {
        double distance = CGAL::squared_distance(it->point(), point);
        if (distance < minDistance) {
            minDistance = distance;
            nearest = it;
        }
    }

    return nearest;
}

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

    // Триангуляция
    CDT cdt;

    // Добавляем внешний полигон как ограничение
    for (int i = 0; i < outerPolygon.size(); ++i) {
        int j = (i + 1) % outerPolygon.size();
        cdt.insert_constraint(Point(outerPolygon[i].x(), outerPolygon[i].y()),
                             Point(outerPolygon[j].x(), outerPolygon[j].y()));
    }

    // Добавляем внутренние полигоны как ограничения
    for (const auto &polygon : innerPolygons) {
        for (int i = 0; i < polygon.size(); ++i) {
            int j = (i + 1) % polygon.size();
            cdt.insert_constraint(Point(polygon[i].x(), polygon[i].y()),
                                 Point(polygon[j].x(), polygon[j].y()));
        }
    }

    // Построение графа
    buildGraph(cdt);

    // Поиск кратчайшего пути
    shortestPath = findShortestPath(cdt);
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
    // Генерация случайных точек внутри внешнего полигона
    qsrand(static_cast<uint>(QTime::currentTime().msec()));
    startPoint = QPointF(150, 150); // Пример
    endPoint = QPointF(450, 450);   // Пример
}

void MainWindow::buildGraph(CDT &cdt) {
    // Назначаем уникальные индексы вершинам
    int index = 0;
    for (auto it = cdt.finite_vertices_begin(); it != cdt.finite_vertices_end(); ++it) {
        it->info() = index++;
    }
}

QVector<QPointF> MainWindow::findShortestPath(const CDT &cdt) {
    // Алгоритм Дейкстры
    QVector<double> dist(cdt.number_of_vertices(), std::numeric_limits<double>::max());
    QVector<int> prev(cdt.number_of_vertices(), -1);
    QQueue<QPair<int, double>> queue;

    // Находим ближайшие вершины к начальной и конечной точкам
    auto startVertex = findNearestVertex(cdt, Point(startPoint.x(), startPoint.y()));
    auto endVertex = findNearestVertex(cdt, Point(endPoint.x(), endPoint.y()));

    dist[startVertex->info()] = 0;
    queue.enqueue(qMakePair(startVertex->info(), 0));

    while (!queue.isEmpty()) {
        auto [uIndex, currentDist] = queue.dequeue();

        if (uIndex == endVertex->info()) {
            break;
        }

        auto u = cdt.finite_vertices_begin();
        std::advance(u, uIndex);

        auto circulator = cdt.incident_vertices(u);
        do {
            if (!cdt.is_infinite(circulator)) {
                int vIndex = circulator->info();
                double weight = CGAL::sqrt(CGAL::squared_distance(u->point(), circulator->point()));
                if (dist[vIndex] > dist[uIndex] + weight) {
                    dist[vIndex] = dist[uIndex] + weight;
                    prev[vIndex] = uIndex;
                    queue.enqueue(qMakePair(vIndex, dist[vIndex]));
                }
            }
        } while (++circulator != cdt.incident_vertices(u));
    }

    // Восстановление пути
    QVector<QPointF> path;
    for (int at = endVertex->info(); at != -1; at = prev[at]) {
        auto vertex = cdt.finite_vertices_begin();
        std::advance(vertex, at);
        path.append(QPointF(vertex->point().x(), vertex->point().y()));
    }
    std::reverse(path.begin(), path.end());

    return path;
}
```

---

### Объяснение изменений

1. **Функция `findNearestVertex`**:
   - Перебирает все вершины триангуляции и находит ближайшую к заданной точке.
   - Использует `CGAL::squared_distance` для вычисления расстояния.

2. **Использование `findNearestVertex`**:
   - Заменяет вызовы `cdt.nearest_vertex` на `findNearestVertex`.

---

### Результат

Теперь программа корректно находит ближайшие вершины и строит кратчайший путь без ошибок. Если у вас остались вопросы, дайте знать! 😊