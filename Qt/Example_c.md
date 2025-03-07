1. **Создайте новый проект**:
    - Создайте новый проект в Qt Creator или настройте свой проект с использованием CMake.

2. **Конфигурация CMake**:
    - Если вы используете CMake, убедитесь, что ваш `CMakeLists.txt` файл настроен правильно:

    ```cmake
    cmake_minimum_required(VERSION 3.5)
    project(DrawLineWithDelay)

    set(CMAKE_CXX_STANDARD 11)

    find_package(Qt5 COMPONENTS Widgets REQUIRED)

    add_executable(${PROJECT_NAME} main.cpp mainwindow.cpp mainwindow.h)
    target_link_libraries(${PROJECT_NAME} Qt5::Widgets)
    ```

3. **Файлы проекта**:
    - Создайте три файла: `main.cpp`, `mainwindow.h` и `mainwindow.cpp`.

4. **main.cpp**:

    ```cpp
    #include <QApplication>
    #include "mainwindow.h"

    int main(int argc, char *argv[]) {
        QApplication a(argc, argv);
        MainWindow w;
        w.show();
        return a.exec();
    }
    ```

5. **mainwindow.h**:

    ```cpp
    #ifndef MAINWINDOW_H
    #define MAINWINDOW_H

    #include <QMainWindow>
    #include <QTimer>
    #include <QVector>
    #include <QPoint>
    #include <QKeyEvent>

    QT_BEGIN_NAMESPACE
    namespace Ui { class MainWindow; }
    QT_END_NAMESPACE

    class MainWindow : public QMainWindow {
        Q_OBJECT

    public:
        MainWindow(QWidget *parent = nullptr);
        ~MainWindow();

    protected:
        void paintEvent(QPaintEvent *event) override;
        void keyPressEvent(QKeyEvent *event) override;

    private slots:
        void updateLine();

    private:
        Ui::MainWindow *ui;
        QTimer *timer;
        int currentPoint;
        bool isPaused;
        bool isLineHidden;
        QVector<QPoint> points;
        void drawPolygon(QPainter &painter);
    };

    #endif // MAINWINDOW_H
    ```

6. **mainwindow.cpp**:

    ```cpp
    #include "mainwindow.h"
    #include "ui_mainwindow.h"
    #include <QPainter>
    #include <QKeyEvent>

    MainWindow::MainWindow(QWidget *parent)
        : QMainWindow(parent)
        , ui(new Ui::MainWindow)
        , timer(new QTimer(this))
        , currentPoint(0)
        , isPaused(false)
        , isLineHidden(false)
    {
        ui->setupUi(this);

        // Пример точек для линии и полигона
        points = {QPoint(10, 10), QPoint(100, 100), QPoint(200, 50), QPoint(300, 200)};

        // Настраиваем таймер
        connect(timer, &QTimer::timeout, this, &MainWindow::updateLine);
        timer->start(500); // Задержка в миллисекундах (500 мс = 0.5 секунды)
    }

    MainWindow::~MainWindow() {
        delete ui;
    }

    void MainWindow::paintEvent(QPaintEvent *event) {
        Q_UNUSED(event);

        QPainter painter(this);
        QPen pen(Qt::black, 2);
        painter.setPen(pen);

        // Отрисовываем линию до текущей точки
        if (!isLineHidden) {
            for (int i = 1; i <= currentPoint; ++i) {
                if (i < points.size()) {
                    painter.drawLine(points[i - 1], points[i]);
                }
            }
        }

        // Отрисовываем красный круг вокруг текущей точки
        if (currentPoint < points.size()) {
            QPen redPen(Qt::red, 2);
            painter.setPen(redPen);
            painter.setBrush(Qt::NoBrush); // Не заливаем круг
            painter.drawEllipse(points[currentPoint], 5, 5); // Круг радиусом 5 пикселей
        }

        // Отрисовываем периметр
        drawPolygon(painter);
    }

    void MainWindow::keyPressEvent(QKeyEvent *event) {
        if (event->key() == Qt::Key_P) {
            isPaused = !isPaused;
            if (isPaused) {
                timer->stop();
            } else {
                timer->start(500);
            }
        }
        if (event->key() == Qt::Key_L) {
            isLineHidden = !isLineHidden;
            update(); // Перерисовываем виджет
        }
        QMainWindow::keyPressEvent(event);
    }

    void MainWindow::updateLine() {
        if (currentPoint < points.size() - 1) {
            currentPoint++;
            update(); // Перерисовываем виджет
        } else {
            timer->stop(); // Останавливаем таймер, когда линия полностью отрисована
        }
    }

    void MainWindow::drawPolygon(QPainter &painter) {
        if (points.size() > 2) {
            painter.setPen(Qt::blue);
            painter.setBrush(Qt::NoBrush); // Прозрачный фон
            painter.drawPolygon(points.data(), points.size());
        }
    }
    ```

### Пояснения к коду

- **Добавлен вектор `points`**: Хранит координаты точек для линии и полигона.
- **Метод `paintEvent`**: Отрисовывает линию до текущей точки, красный круг вокруг текущей точки и периметр.
- **Метод `keyPressEvent`**: Обрабатывает нажатия клавиш 'P' для паузы/возобновления отрисовки и 'L' для скрытия/показа линий.
- **Метод `updateLine`**: Обновляет текущую точку и перерисовывает виджет.
- **Метод `drawPolygon`**: Отрисовывает периметр из точек вектора `points`.

Запустите приложение, и вы сможете увидеть, как оно отрисовывает линию и периметр с задержкой, а также реагирует на нажатия клавиш 'P' и 'L'.