Для использования библиотеки **GEOS** (Geometry Engine - Open Source) в проекте на C++ с **Qt**, нужно выполнить следующие шаги:

1. **Установка GEOS**:
   - На Linux (Ubuntu/Debian): `sudo apt install libgeos-dev`
   - На macOS (с использованием Homebrew): `brew install geos`
   - На Windows: Скачайте и установите GEOS с официального сайта или используйте vcpkg.

2. **Интеграция с CMake**:
   Добавьте GEOS в ваш `CMakeLists.txt`.

3. **Пример кода**:
   Используем GEOS для триангуляции и поиска кратчайшего пути.

---

### Пример проекта с использованием GEOS

#### 1. **CMakeLists.txt**

```cmake
cmake_minimum_required(VERSION 3.14)
project(ShortestPathProject)

# Настройка Qt
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# Поиск необходимых пакетов
find_package(Qt6 REQUIRED COMPONENTS Core Gui Widgets)
find_package(GEOS REQUIRED)

# Добавление исходных файлов
set(SOURCES
    main.cpp
    mainwindow.cpp
    mainwindow.h
)

# Создание исполняемого файла
add_executable(ShortestPathProject ${SOURCES})

# Подключение библиотек
target_link_libraries(ShortestPathProject
    Qt6::Core
    Qt6::Gui
    Qt6::Widgets
    GEOS::GEOS
)
```

---

#### 2. **main.cpp**

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

---

#### 3. **mainwindow.h**

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
#include <geos/geom/GeometryFactory.h>
#include <geos/geom/Polygon.h>
#include <geos/geom/CoordinateSequence.h>
#include <geos/geom/CoordinateArraySequence.h>
#include <geos/triangulate/DelaunayTriangulationBuilder.h>
#include <geos/geom/Geometry.h>
#include <geos/geom/LineString.h>

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
    void triangulate();
    QVector<QPointF> findShortestPath();
};

#endif // MAINWINDOW_H
```

---

#### 4. **mainwindow.cpp**

```cpp
#include "mainwindow.h"
#include <geos/geom/GeometryFactory.h>
#include <geos/geom/Polygon.h>
#include <geos/geom/CoordinateSequence.h>
#include <geos/geom/CoordinateArraySequence.h>
#include <geos/triangulate/DelaunayTriangulationBuilder.h>
#include <geos/geom/Geometry.h>
#include <geos/geom/LineString.h>

using namespace geos::geom;
using namespace geos::triangulate;

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
    triangulate();

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
    // Генерация случайных точек внутри внешнего полигона
    qsrand(static_cast<uint>(QTime::currentTime().msec()));
    startPoint = QPointF(150, 150); // Пример
    endPoint = QPointF(450, 450);   // Пример
}

void MainWindow::triangulate() {
    // Создаем фабрику геометрии
    GeometryFactory::Ptr factory = GeometryFactory::create();

    // Создаем координаты для внешнего полигона
    CoordinateArraySequence outerCoords;
    for (const auto &point : outerPolygon) {
        outerCoords.add(Coordinate(point.x(), point.y()));
    }
    outerCoords.add(Coordinate(outerPolygon[0].x(), outerPolygon[0].y())); // Замыкаем полигон

    // Создаем внешний полигон
    LinearRing *outerRing = factory->createLinearRing(outerCoords.clone());
    Polygon *polygon = factory->createPolygon(outerRing, nullptr);

    // Создаем триангуляцию
    DelaunayTriangulationBuilder builder;
    builder.setSites(*polygon);

    // Получаем триангуляцию
    std::unique_ptr<Geometry> triangles = builder.getTriangles(*factory);

    // Выводим результат (пример)
    qDebug() << "Triangles:" << triangles->toString().c_str();
}

QVector<QPointF> MainWindow::findShortestPath() {
    // Алгоритм Дейкстры (пример)
    QVector<QPointF> path;
    path << startPoint << endPoint;
    return path;
}
```

---

### Объяснение кода

1. **GEOS**:
   - Используется для создания геометрических объектов (полигоны, линии, точки).
   - `DelaunayTriangulationBuilder` выполняет триангуляцию Делоне.

2. **Триангуляция**:
   - Внешний полигон передается в `DelaunayTriangulationBuilder`.
   - Результат триангуляции возвращается как `Geometry`.

3. **Поиск пути**:
   - В этом примере путь просто соединяет начальную и конечную точки. Для реализации алгоритма Дейкстры нужно построить граф на основе триангуляции.

---

### Сборка и запуск

1. Создайте директорию для сборки:
   ```bash
   mkdir build
   cd build
   ```

2. Запустите CMake:
   ```bash
   cmake ..
   ```

3. Соберите проект:
   ```bash
   cmake --build .
   ```

4. Запустите программу:
   ```bash
   ./ShortestPathProject
   ```

---

### Результат

- Программа отображает внешний полигон, внутренние полигоны, точки начала и конца, а также результат триангуляции.
- Для поиска кратчайшего пути нужно доработать алгоритм Дейкстры на основе графа, построенного из триангуляции.

Если у вас есть вопросы, дайте знать! 😊