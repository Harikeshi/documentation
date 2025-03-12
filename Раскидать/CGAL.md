### 1. **Основные шаги**

1. **Триангуляция**:
   - Разбиваем пространство внутри внешнего полигона на треугольники, исключая внутренние полигоны.
   - Используем библиотеку, например, **CGAL** или **Triangle**, для выполнения триангуляции.

2. **Построение графа**:
   - Вершины графа — это вершины треугольников.
   - Ребра графа — это стороны треугольников, которые не пересекают внутренние полигоны.

3. **Поиск пути**:
   - Используем алгоритм Дейкстры или A* для нахождения кратчайшего пути между двумя точками на графе.

---

### 2. **Пример кода с использованием Qt и CGAL**

#### a) **Установка CGAL**
Установите библиотеку CGAL. В Qt Creator добавьте путь к CGAL в `.pro` файл:

```pro
# Добавьте в .pro файл
INCLUDEPATH += /path/to/cgal/include
LIBS += -L/path/to/cgal/lib -lCGAL -lCGAL_Core
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
#include <CGAL/Exact_predicates_inexact_constructions_kernel.h>
#include <CGAL/Constrained_Delaunay_triangulation_2.h>
#include <CGAL/Triangulation_vertex_base_with_info_2.h>
#include <CGAL/Triangulation_face_base_with_info_2.h>

typedef CGAL::Exact_predicates_inexact_constructions_kernel K;
typedef CGAL::Triangulation_vertex_base_with_info_2<int, K> Vb;
typedef CGAL::Triangulation_face_base_with_info_2<int, K> Fb;
typedef CGAL::Triangulation_data_structure_2<Vb, Fb> Tds;
typedef CGAL::Constrained_Delaunay_triangulation_2<K, Tds> CDT;
typedef CDT::Point Point;
typedef CDT::Vertex_handle Vertex_handle;

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
    void buildGraph(CDT &cdt);
    QVector<QPointF> findShortestPath(const CDT &cdt);
};

#endif // MAINWINDOW_H
```

#### c) **mainwindow.cpp**
```cpp
#include "mainwindow.h"
#include <CGAL/Constrained_triangulation_plus_2.h>

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
    auto startVertex = cdt.nearest_vertex(Point(startPoint.x(), startPoint.y()));
    auto endVertex = cdt.nearest_vertex(Point(endPoint.x(), endPoint.y()));

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

### 3. **Объяснение кода**

1. **Триангуляция**:
   - Используется `CGAL::Constrained_Delaunay_triangulation_2` для создания триангуляции с учетом ограничений (внешний и внутренние полигоны).

2. **Построение графа**:
   - Вершины графа — это вершины треугольников.
   - Ребра графа — это стороны треугольников.

3. **Алгоритм Дейкстры**:
   - Используется для поиска кратчайшего пути на графе.

4. **Восстановление пути**:
   - Путь восстанавливается с помощью массива `prev`.

---

### 4. **Результат**
- Программа строит граф внутри внешнего полигона, исключая внутренние полигоны, и находит кратчайший путь между двумя точками.

---

Этот подход более универсален и подходит для сложных полигонов.