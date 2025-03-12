В более новых версиях **GEOS** класс `CoordinateArraySequence` был удалён, и вместо него рекомендуется использовать `CoordinateSequence`. Вам нужно адаптировать код для работы с `CoordinateSequence`. Вот как это можно сделать:

---

### Замена `CoordinateArraySequence` на `CoordinateSequence`

1. **Создание `CoordinateSequence`**:
   - Используйте `GeometryFactory::createCoordinateSequence()` для создания последовательности координат.

2. **Добавление координат**:
   - Вместо `CoordinateArraySequence::add()` используйте `CoordinateSequence::setAt()`.

3. **Получение координат**:
   - Используйте `CoordinateSequence::getAt()` для доступа к координатам.

---

### Пример кода с использованием `CoordinateSequence`

#### 1. **Создание внешнего и внутреннего полигонов**

```cpp
#include <geos/geom/GeometryFactory.h>
#include <geos/geom/Polygon.h>
#include <geos/geom/LinearRing.h>
#include <geos/geom/CoordinateSequence.h>
#include <geos/triangulate/DelaunayTriangulationBuilder.h>
#include <iostream>
#include <vector>
#include <memory>

using namespace geos::geom;

int main() {
    // Создаем фабрику геометрии
    GeometryFactory::Ptr factory = GeometryFactory::create();

    // Создаем координаты для внешнего полигона
    auto outerCoords = factory->getCoordinateSequenceFactory()->create(5, 2); // 5 точек, 2D
    outerCoords->setAt(Coordinate(0, 0), 0);
    outerCoords->setAt(Coordinate(10, 0), 1);
    outerCoords->setAt(Coordinate(10, 10), 2);
    outerCoords->setAt(Coordinate(0, 10), 3);
    outerCoords->setAt(Coordinate(0, 0), 4); // Замыкаем полигон

    // Создаем внешний полигон
    LinearRing* outerRing = factory->createLinearRing(outerCoords.release());
    Polygon* outerPolygon = factory->createPolygon(outerRing, nullptr);

    // Создаем координаты для внутреннего полигона
    auto innerCoords = factory->getCoordinateSequenceFactory()->create(5, 2); // 5 точек, 2D
    innerCoords->setAt(Coordinate(2, 2), 0);
    innerCoords->setAt(Coordinate(4, 2), 1);
    innerCoords->setAt(Coordinate(4, 4), 2);
    innerCoords->setAt(Coordinate(2, 4), 3);
    innerCoords->setAt(Coordinate(2, 2), 4); // Замыкаем полигон

    // Создаем внутренний полигон
    LinearRing* innerRing = factory->createLinearRing(innerCoords.release());
    Polygon* innerPolygon = factory->createPolygon(innerRing, nullptr);

    // Создаем коллекцию внутренних полигонов
    std::vector<const Geometry*> holes;
    holes.push_back(innerPolygon);

    // Создаем полигон с отверстиями
    Polygon* polygonWithHoles = factory->createPolygon(outerRing, holes);

    // Триангуляция
    DelaunayTriangulationBuilder builder;
    builder.setSites(*polygonWithHoles);
    std::unique_ptr<Geometry> triangles = builder.getTriangles(*factory);

    // Выводим результат триангуляции
    std::cout << "Триангуляция: " << triangles->toString() << std::endl;

    return 0;
}
```

---

### 2. **Преобразование треугольников в граф**

Теперь адаптируем функцию `trianglesToGraph` для работы с `CoordinateSequence`.

```cpp
#include <geos/geom/Geometry.h>
#include <geos/geom/Polygon.h>
#include <geos/geom/LineString.h>
#include <geos/geom/CoordinateSequence.h>
#include <geos/geom/GeometryFactory.h>
#include <geos/triangulate/DelaunayTriangulationBuilder.h>
#include <iostream>
#include <vector>
#include <map>
#include <cmath>

// Функция для вычисления расстояния между двумя точками
double distance(const Coordinate& p1, const Coordinate& p2) {
    double dx = p1.x - p2.x;
    double dy = p1.y - p2.y;
    return std::sqrt(dx * dx + dy * dy);
}

// Функция для преобразования треугольников в граф
Graph trianglesToGraph(const Geometry* triangles, const std::vector<const Geometry*>& holes, const GeometryFactory::Ptr& factory) {
    Graph graph;
    std::map<Coordinate, int> vertexToIndex; // Хэш-таблица для хранения индексов вершин
    int index = 0;

    // Извлекаем координаты треугольников
    const CoordinateSequence* coords = triangles->getCoordinates();

    // Проходим по всем треугольникам
    for (size_t i = 0; i < coords->size(); i += 3) {
        Coordinate p1 = coords->getAt(i);
        Coordinate p2 = coords->getAt(i + 1);
        Coordinate p3 = coords->getAt(i + 2);

        // Добавляем вершины в граф, если они ещё не добавлены
        if (vertexToIndex.find(p1) == vertexToIndex.end()) {
            vertexToIndex[p1] = index++;
            graph.push_back({});
        }
        if (vertexToIndex.find(p2) == vertexToIndex.end()) {
            vertexToIndex[p2] = index++;
            graph.push_back({});
        }
        if (vertexToIndex.find(p3) == vertexToIndex.end()) {
            vertexToIndex[p3] = index++;
            graph.push_back({});
        }

        // Получаем индексы вершин
        int idx1 = vertexToIndex[p1];
        int idx2 = vertexToIndex[p2];
        int idx3 = vertexToIndex[p3];

        // Добавляем рёбра в граф (если они не пересекают внутренние полигоны)
        bool edge12Valid = true;
        bool edge23Valid = true;
        bool edge31Valid = true;

        // Проверяем, пересекает ли ребро внутренние полигоны
        for (const Geometry* hole : holes) {
            auto edge12 = factory->createLineString({p1, p2});
            auto edge23 = factory->createLineString({p2, p3});
            auto edge31 = factory->createLineString({p3, p1});

            if (edge12->intersects(hole)) edge12Valid = false;
            if (edge23->intersects(hole)) edge23Valid = false;
            if (edge31->intersects(hole)) edge31Valid = false;

            factory->destroyGeometry(edge12);
            factory->destroyGeometry(edge23);
            factory->destroyGeometry(edge31);
        }

        // Добавляем рёбра в граф
        if (edge12Valid) {
            graph[idx1].push_back({idx2, distance(p1, p2)});
            graph[idx2].push_back({idx1, distance(p1, p2)});
        }
        if (edge23Valid) {
            graph[idx2].push_back({idx3, distance(p2, p3)});
            graph[idx3].push_back({idx2, distance(p2, p3)});
        }
        if (edge31Valid) {
            graph[idx3].push_back({idx1, distance(p3, p1)});
            graph[idx1].push_back({idx3, distance(p3, p1)});
        }
    }

    return graph;
}
```

---

### 3. **Использование функции в программе**

```cpp
int main() {
    // Создаем фабрику геометрии
    GeometryFactory::Ptr factory = GeometryFactory::create();

    // Создаем внешний полигон
    auto outerCoords = factory->getCoordinateSequenceFactory()->create(5, 2); // 5 точек, 2D
    outerCoords->setAt(Coordinate(0, 0), 0);
    outerCoords->setAt(Coordinate(10, 0), 1);
    outerCoords->setAt(Coordinate(10, 10), 2);
    outerCoords->setAt(Coordinate(0, 10), 3);
    outerCoords->setAt(Coordinate(0, 0), 4); // Замыкаем полигон

    LinearRing* outerRing = factory->createLinearRing(outerCoords.release());
    Polygon* outerPolygon = factory->createPolygon(outerRing, nullptr);

    // Создаем внутренний полигон
    auto innerCoords = factory->getCoordinateSequenceFactory()->create(5, 2); // 5 точек, 2D
    innerCoords->setAt(Coordinate(2, 2), 0);
    innerCoords->setAt(Coordinate(4, 2), 1);
    innerCoords->setAt(Coordinate(4, 4), 2);
    innerCoords->setAt(Coordinate(2, 4), 3);
    innerCoords->setAt(Coordinate(2, 2), 4); // Замыкаем полигон

    LinearRing* innerRing = factory->createLinearRing(innerCoords.release());
    Polygon* innerPolygon = factory->createPolygon(innerRing, nullptr);

    // Создаем коллекцию внутренних полигонов
    std::vector<const Geometry*> holes;
    holes.push_back(innerPolygon);

    // Создаем полигон с отверстиями
    Polygon* polygonWithHoles = factory->createPolygon(outerRing, holes);

    // Триангуляция
    DelaunayTriangulationBuilder builder;
    builder.setSites(*polygonWithHoles);
    std::unique_ptr<Geometry> triangles = builder.getTriangles(*factory);

    // Преобразуем треугольники в граф
    Graph graph = trianglesToGraph(triangles.get(), holes, factory);

    // Выводим граф
    for (size_t i = 0; i < graph.size(); ++i) {
        std::cout << "Вершина " << i << " соединена с: ";
        for (const Edge& edge : graph[i]) {
            std::cout << edge.target << " (вес " << edge.weight << "), ";
        }
        std::cout << std::endl;
    }

    return 0;
}
```

---

### Объяснение изменений

1. **Использование `CoordinateSequence`**:
   - Вместо `CoordinateArraySequence` используется `CoordinateSequence`.
   - Координаты добавляются с помощью `setAt()`.

2. **Уничтожение геометрии**:
   - Используется `factory->destroyGeometry()` для освобождения памяти.

---

Теперь программа работает с актуальной версией GEOS. Если у вас есть вопросы, дайте знать! 😊