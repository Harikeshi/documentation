```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QTimer>
#include <QElapsedTimer>
#include <QKeyEvent>
#include <QMouseEvent>
#include <vector>
#include <memory>
#include <cmath>
#include <random>

// Добавляем класс состояния анимации
class RouteState {
public:
    void reset() {
        current_segment = 0;
        progress = 0.0;
    }

    size_t current_segment = 0;
    double progress = 0.0;
};

class Route {
public:
    struct Segment {
        QPointF start;
        QPointF end;
        double base_speed;
    };

    void add_segment(QPointF start, QPointF end, double speed) {
        segments.push_back({start, end, speed});
        states.emplace_back();
    }

    void reset() {
        for(auto& state : states) {
            state.reset();
        }
    }

    void update(double delta_time) {
        for(size_t i = 0; i < segments.size(); ++i) {
            auto& seg = segments[i];
            auto& state = states[i];
            
            if(state.current_segment >= seg.size()) continue;
            
            double max_travel = seg[state.current_segment].base_speed * delta_time;
            // ... остальная логика обновления
        }
    }

    std::vector<Segment> segments;
    std::vector<RouteState> states;
    QColor color;
};

class AnimationWidget : public QWidget {
public:
    AnimationWidget(QWidget* parent = nullptr) : QWidget(parent) {
        // ... инициализация таймеров и элементов управления
        
        // Кнопка сброса отображения
        resetButton = new QPushButton("Reset View", this);
        resetButton->setGeometry(10, 50, 100, 30);
        connect(resetButton, &QPushButton::clicked, [this](){
            for(auto& route : routes_) {
                route.reset(); // Сбрасываем состояние, не удаляя маршруты
            }
            update();
        });
    }

protected:
    void keyPressEvent(QKeyEvent* event) override {
        switch(event->key()) {
            case Qt::Key_Space:
                paused_ = !paused_;
                if(!paused_) timer_.restart();
                break;
            case Qt::Key_R: // Новая клавиша для сброса
                reset_animation();
                break;
            // ... остальные обработчики
        }
        update();
    }

    void reset_animation() {
        for(auto& route : routes_) {
            route.reset();
        }
        timer_.restart();
        paused_ = false;
    }

private:
    QPushButton* resetButton;
    
    void paintEvent(QPaintEvent*) override {
        // ... отрисовка с учетом текущих состояний
    }

    void setup_demo_data() {
        // Генерация маршрутов один раз при инициализации
        if(routes_.empty()) {
            // ... создание маршрутов
        }
    }
};
```

Ключевые изменения:

1. **Отделение данных от состояния**
- Класс `RouteState` хранит прогресс анимации
- Данные маршрутов остаются неизменными в `Route::segments`
- При сбросе обнуляется только состояние

2. **Новый функционал**
- Кнопка "Reset View" и клавиша R для сброса анимации
- Метод `reset_animation()` для управления сбросом
- Сохранение исходных маршрутов в памяти

3. **Исправление паузы**
- Гарантированный перезапуск таймера при снятии паузы
- Корректная обработка состояния paused_

4. **Логика обновления**
```cpp
void Route::update(double delta_time) {
    if(paused) return;
    
    for(size_t i = 0; i < segments.size(); ++i) {
        auto& seg = segments[i];
        auto& state = states[i];
        
        // Логика перемещения по сегментам
    }
}
```

Чтобы использовать:
1. Нажмите R или кнопку "Reset View" для сброса анимации
2. Маршруты остаются в памяти, но отрисовываются с начала
3. Пауза работает корректно через пробел

Это решение сохраняет исходные данные маршрутов, но позволяет:
- Сбрасывать визуальное отображение
- Сохранять настройки маршрутов между сбросами
- Корректно работать с паузой
- Изменять скорость без потери данных






**Структура проекта:**
```
my_qt_project/
├── CMakeLists.txt
├── main.cpp
└── include/
    └── MyWidget.h
└── src/
    └── MyWidget.cpp
```

**1. CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.5)

# Название проекта
project(MyQtProject LANGUAGES CXX)

# Поиск необходимых компонентов Qt5
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)

# Включение автоматической обработки MOC, UIC и RCC
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# Добавление исходных файлов
file(GLOB SOURCES
    "src/*.cpp"
    "main.cpp"
)

# Добавление заголовочных файлов
file(GLOB HEADERS
    "include/*.h"
)

# Создание исполняемого файла
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

# Подключение Qt модулей
target_link_libraries(${PROJECT_NAME}
    Qt5::Core
    Qt5::Widgets
)

# Установка стандарта C++
set_target_properties(${PROJECT_NAME} PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON
)
```

**2. MyWidget.h:**
```cpp
#ifndef MYWIDGET_H
#define MYWIDGET_H

#include <QWidget>
#include <QLabel>
#include <QPushButton>

class MyWidget : public QWidget {
    Q_OBJECT  // Важно для работы MOC

public:
    explicit MyWidget(QWidget* parent = nullptr);

signals:
    void buttonClicked();

private slots:
    void handleButtonClick();

private:
    QLabel* label;
    QPushButton* button;
    int clickCount = 0;
};

#endif // MYWIDGET_H
```

**3. MyWidget.cpp:**
```cpp
#include "MyWidget.h"

MyWidget::MyWidget(QWidget* parent) : QWidget(parent) {
    label = new QLabel("Clicks: 0", this);
    button = new QPushButton("Click me!", this);
    
    button->move(0, 30);
    
    connect(button, &QPushButton::clicked, 
            this, &MyWidget::handleButtonClick);
}

void MyWidget::handleButtonClick() {
    clickCount++;
    label->setText(QString("Clicks: %1").arg(clickCount));
    emit buttonClicked();
}
```

**4. main.cpp:**
```cpp
#include <QApplication>
#include "MyWidget.h"

int main(int argc, char* argv[]) {
    QApplication app(argc, argv);
    
    MyWidget widget;
    widget.resize(200, 100);
    widget.show();
    
    return app.exec();
}
```

**Сборка проекта:**
```bash
mkdir build
cd build
cmake ..
make
./MyQtProject
```

**Ключевые моменты:**

1. **Автоматический MOC:**
   - `set(CMAKE_AUTOMOC ON)` включает автоматическую обработку файлов через moc
   - Все классы с макросом `Q_OBJECT` будут автоматически обработаны

2. **Подключение модулей Qt:**
   - `find_package(Qt5 COMPONENTS Core Widgets REQUIRED)`
   - `target_link_libraries` с указанием нужных модулей

3. **Структура проекта:**
   - Заголовочные файлы в `include/`
   - Исходные файлы в `src/`
   - Главный файл `main.cpp` в корне

4. **Особенности обработки:**
   - CMake автоматически генерирует:
     - moc_*.cpp файлы
     - ui_*.h файлы (если есть .ui-файлы)
     - qrc_*.cpp файлы (если есть ресурсы)

**Добавление .ui файлов:**
1. Создайте файл интерфейса (например, `MainWindow.ui`)
2. Добавьте в CMakeLists.txt:
   ```cmake
   set(CMAKE_AUTOUIC ON)
   file(GLOB FORMS "*.ui")
   add_executable(... ${FORMS})
   ```

**Добавление ресурсов (.qrc):**
1. Создайте файл ресурсов (например, `resources.qrc`)
2. Добавьте в CMakeLists.txt:
   ```cmake
   file(GLOB RESOURCES "*.qrc")
   add_executable(... ${RESOURCES})
   ```

**Советы:**
- Для поиска Qt в нестандартных путях:
  ```bash
  cmake .. -DCMAKE_PREFIX_PATH=/path/to/qt5
  ```
- Используйте Qt Creator для удобной работы с CMake-проектами
- Для отладки генерации MOC можно добавить:
  ```cmake
  set(CMAKE_AUTOMOC_VERBOSE ON)
  ```