
1. **Файл `points.h`**:

    ```cpp
    #ifndef POINTS_H
    #define POINTS_H

    #include "coordinate.h"
    #include <vector>
    #include <QPainter>

    class Points {
    public:
        Points(const std::vector<std::vector<Coordinate>>& points);
        void draw(QPainter& painter, const std::function<QPoint(const Coordinate&)>& scale, bool drawLines) const;
        void advance();
        void reset();

    private:
        std::vector<std::vector<Coordinate>> points;
        mutable size_t currentPath;
        mutable size_t currentIndex;
    };

    #endif // POINTS_H
    ```

2. **Файл `points.cpp`**:

    ```cpp
    #include "points.h"

    Points::Points(const std::vector<std::vector<Coordinate>>& points) 
        : points(points), currentPath(0), currentIndex(0) {}

    void Points::draw(QPainter& painter, const std::function<QPoint(const Coordinate&)>& scale, bool drawLines) const {
        if (drawLines) {
            for (size_t path = 0; path <= currentPath; ++path) {
                painter.setPen(Qt::black);
                for (size_t i = 1; i <= (path == currentPath ? currentIndex : points[path].size() - 1); ++i) {
                    QPoint p1 = scale(points[path][i-1]);
                    QPoint p2 = scale(points[path][i]);
                    painter.drawLine(p1, p2);
                }
            }
        }

        if (currentPath < points.size() && currentIndex < points[currentPath].size()) {
            QPoint p = scale(points[currentPath][currentIndex]);
            painter.setBrush(Qt::red);
            painter.drawEllipse(p, 5, 5);
        }
    }

    void Points::advance() {
        if (currentPath < points.size()) {
            if (currentIndex < points[currentPath].size() - 1) {
                currentIndex++;
            } else {
                currentIndex = 0;
                currentPath++;
            }
        }
    }

    void Points::reset() {
        currentPath = 0;
        currentIndex = 0;
    }
    ```

3. **Файл `drawingwidget.cpp`** (обновление для работы с новым `Points`):

    ```cpp
    #include "drawingwidget.h"
    #include <QPainter>
    #include <QKeyEvent>
    #include <algorithm>
    #include <cmath>

    DrawingWidget::DrawingWidget(QWidget *parent)
        : QWidget(parent), drawLines(true), isPaused(false) {
        timer = new QTimer(this);
        connect(timer, &QTimer::timeout, this, &DrawingWidget::animate);
        timer->start(100);

        perimeter = new Perimeter({{50, 50}, {300, 50}, {300, 300}, {50, 300}, {50, 50}});
        points = new Points({
            {{100, 100}, {150, 150}, {200, 100}},
            {{250, 200}, {200, 250}, {150, 200}}
        });

        calculateBounds();
    }

    void DrawingWidget::paintEvent(QPaintEvent *) {
        QPainter painter(this);
        painter.setRenderHint(QPainter::Antialiasing);

        drawGrid(painter);

        auto scale = [this](const Coordinate& c) -> QPoint {
            double x = margin + (c.x - minX) * scaleX;
            double y = height() - margin - (c.y - minY) * scaleY;
            return QPoint(static_cast<int>(x), static_cast<int>(y));
        };

        perimeter->draw(painter, scale);
        points->draw(painter, scale, drawLines);
    }

    void DrawingWidget::keyPressEvent(QKeyEvent *event) {
        if(event->key() == Qt::Key_P) {
            isPaused = !isPaused;
            isPaused ? timer->stop() : timer->start();
        }
        else if(event->key() == Qt::Key_L) {
            drawLines = !drawLines;
            update();
        }
    }

    void DrawingWidget::resizeEvent(QResizeEvent *event) {
        QWidget::resizeEvent(event);
        calculateBounds();
        update();
    }

    void DrawingWidget::animate() {
        points->advance();
        update();
    }

    void DrawingWidget::calculateBounds() {
        auto [minX_it, maxX_it] = std::minmax_element(points->begin(), points->end(),
            [](const Coordinate& a, const Coordinate& b) { return a.x < b.x; });
        auto [minY_it, maxY_it] = std::minmax_element(points->begin(), points->end(),
            [](const Coordinate& a, const Coordinate& b) { return a.y < b.y; });

        minX = std::min(minX_it->x, std::min_element(perimeter->begin(), perimeter->end(),
            [](const Coordinate& a, const Coordinate& b) { return a.x < b.x; })->x);
        maxX = std::max(maxX_it->x, std::max_element(perimeter->begin(), perimeter->end(),
            [](const Coordinate& a, const Coordinate& b) { return a.x < b.x; })->x);
        minY = std::min(minY_it->y, std::min_element(perimeter->begin(), perimeter->end(),
            [](const Coordinate& a, const Coordinate& b) { return a.y < b.y; })->y);
        maxY = std::max(maxY_it->y, std::max_element(perimeter->begin(), perimeter->end(),
            [](const Coordinate& a, const Coordinate& b) { return a.y < b.y; })->y);

        scaleX = (width() - 2 * margin) / (maxX - minX);
        scaleY = (height() - 2 * margin) / (maxY - minY);
    }

    void DrawingWidget::drawGrid(QPainter& painter) {
        painter.setPen(Qt::gray);
        QFont font = painter.font();
        font.setPointSize(8);
        painter.setFont(font);

        double stepX = (maxX - minX) / 10;
        for(int i = 0; i <= 10; ++i) {
            double x = minX + i * stepX;
            int px = margin + static_cast<int>((x - minX) * scaleX);
            painter.drawLine(px, margin, px, height() - margin);
            painter.drawText(px - 10, height() - margin + 15, QString::number(x, 'f', 1));
        }

        double stepY = (maxY - minY) / 10;
        for(int i = 0; i <= 10; ++i) {
            double y = minY + i * stepY;
            int py = height() - margin - static_cast<int>((y - minY) * scaleY);
            painter.drawLine(margin, py, width() - margin, py);
            painter.drawText(margin - 40, py + 5, QString::number(y, 'f', 1));
        }
    }
    ```

### Подключение библиотеки в вашем проекте

Теперь давайте создадим проект-приложение, которое будет использовать эту библиотеку.

1. **Создайте структуру проекта приложения**:
    - Создайте структуру папок для приложения:
      ```
      drawing_app/
      ├── CMakeLists.txt
      └── main.cpp
      ```

2. **Создание файла `CMakeLists.txt` для приложения**:
    - В корне проекта приложения создайте файл `CMakeLists.txt` со следующим содержимым:
      ```cmake
      cmake_minimum_required(VERSION 3.5)
      project(DrawingApp VERSION 1.0 LANGUAGES CXX)

      set(CMAKE_CXX_STANDARD 11)
      set(CMAKE_CXX_STANDARD_REQUIRED ON)

      find_package(Qt5 COMPONENTS Widgets REQUIRED)

      add_executable(DrawingApp main.cpp)

      target_link_libraries(DrawingApp DrawingLibrary Qt5::Widgets)

      # Убедитесь, что путь к библиотеке DrawingLibrary корректен
      target_include_directories(DrawingApp PUBLIC ../drawing_library/include)
      ```

3. **Файл `main.cpp`**:

    ```cpp
    #include <QApplication>
    #include "drawingwidget.h"

    int main(int argc, char *argv[]) {
        QApplication a(argc, argv);

        DrawingWidget widget;
        widget.resize(800, 800);
        widget.setWindowTitle("Scalable Animated Path Drawing with Grid");
        widget.show();

        return a.exec();
    }
    ```

Теперь у вас есть проект библиотеки и проект-приложение, которое использует эту библиотеку. Сборка и запуск приложения будет включать в себя библиотеку и все необходимые компоненты для отрисовки линий и периметра. Если у вас возникнут дополнительные вопросы или проблемы, дайте знать, и я постараюсь помочь! 😊