```cpp
#include <QApplication>
#include <QMainWindow>
#include <QMenuBar>
#include <QMenu>
#include <QAction>
#include <QFileDialog>
#include <QMessageBox>
#include <QJsonDocument>
#include <QJsonObject>
#include <QJsonArray>
#include <QFile>
#include <QPainter>
#include <QWidget>

class Coordinate {
public:
    double x;
    double y;
};

class Segment {
public:
    Coordinate start;
    Coordinate end;
    double speed;
};

class Routes {
public:
    std::vector<std::vector<Segment>> paths;
};

class Perimeter {
public:
    std::vector<std::vector<Coordinate>> polygons;
};

class VisualizationWidget : public QWidget {
    Q_OBJECT

private:
    Routes routes;
    Perimeter perimeter;

public:
    VisualizationWidget(QWidget* parent = nullptr) : QWidget(parent) {}

    void setRoutes(const Routes& r) {
        routes = r;
        update();
    }

    void setPerimeter(const Perimeter& p) {
        perimeter = p;
        update();
    }

protected:
    void paintEvent(QPaintEvent* event) override {
        QPainter painter(this);

        // Draw perimeter
        painter.setPen(Qt::black);
        for (const auto& polygon : perimeter.polygons) {
            for (size_t i = 0; i < polygon.size(); ++i) {
                const auto& start = polygon[i];
                const auto& end = polygon[(i + 1) % polygon.size()];
                painter.drawLine(QPointF(start.x, start.y), QPointF(end.x, end.y));
            }
        }

        // Draw routes
        painter.setPen(Qt::red);
        for (const auto& path : routes.paths) {
            for (const auto& segment : path) {
                painter.drawLine(QPointF(segment.start.x, segment.start.y), QPointF(segment.end.x, segment.end.y));
            }
        }
    }
};

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    MainWindow(QWidget* parent = nullptr) : QMainWindow(parent) {
        QMenu* fileMenu = menuBar()->addMenu("Файл");
        QAction* openAction = new QAction("Открыть JSON файл", this);
        fileMenu->addAction(openAction);

        visualizationWidget = new VisualizationWidget;
        setCentralWidget(visualizationWidget);

        connect(openAction, &QAction::triggered, this, &MainWindow::openJsonFile);
    }

private slots:
    void openJsonFile() {
        QString fileName = QFileDialog::getOpenFileName(this, "Открыть JSON файл", "", "JSON Files (*.json)");
        if (!fileName.isEmpty()) {
            QFile file(fileName);
            if (!file.open(QIODevice::ReadOnly | QIODevice::Text)) {
                QMessageBox::warning(this, "Ошибка", "Не удалось открыть файл");
                return;
            }

            QByteArray fileData = file.readAll();
            file.close();

            QJsonDocument doc = QJsonDocument::fromJson(fileData);
            if (doc.isNull()) {
                QMessageBox::warning(this, "Ошибка", "Не удалось распознать JSON файл");
                return;
            }

            Routes routes;
            Perimeter perimeter;

            if (doc.isObject()) {
                QJsonObject jsonObj = doc.object();
                
                // Parse routes
                if (jsonObj.contains("routes") && jsonObj["routes"].isArray()) {
                    QJsonArray pathsArray = jsonObj["routes"].toArray();
                    for (const auto& pathValue : pathsArray) {
                        if (pathValue.isArray()) {
                            std::vector<Segment> path;
                            QJsonArray segmentsArray = pathValue.toArray();
                            for (const auto& segmentValue : segmentsArray) {
                                if (segmentValue.isObject()) {
                                    QJsonObject segmentObj = segmentValue.toObject();
                                    Segment segment;
                                    segment.start = {segmentObj["start_x"].toDouble(), segmentObj["start_y"].toDouble()};
                                    segment.end = {segmentObj["end_x"].toDouble(), segmentObj["end_y"].toDouble()};
                                    segment.speed = segmentObj["speed"].toDouble();
                                    path.push_back(segment);
                                }
                            }
                            routes.paths.push_back(path);
                        }
                    }
                }
                
                // Parse perimeter
                if (jsonObj.contains("perimeter") && jsonObj["perimeter"].isArray()) {
                    QJsonArray polygonsArray = jsonObj["perimeter"].toArray();
                    for (const auto& polygonValue : polygonsArray) {
                        if (polygonValue.isArray()) {
                            std::vector<Coordinate> polygon;
                            QJsonArray coordinatesArray = polygonValue.toArray();
                            for (const auto& coordinateValue : coordinatesArray) {
                                if (coordinateValue.isObject()) {
                                    QJsonObject coordinateObj = coordinateValue.toObject();
                                    Coordinate coordinate = {coordinateObj["x"].toDouble(), coordinateObj["y"].toDouble()};
                                    polygon.push_back(coordinate);
                                }
                            }
                            perimeter.polygons.push_back(polygon);
                        }
                    }
                }

                visualizationWidget->setRoutes(routes);
                visualizationWidget->setPerimeter(perimeter);

                QMessageBox::information(this, "JSON файл", "JSON файл успешно загружен.");
            } else {
                QMessageBox::warning(this, "Ошибка", "Не удалось распознать JSON файл");
            }
        }
    }

private:
    VisualizationWidget* visualizationWidget;
};

int main(int argc, char* argv[]) {
    QApplication app(argc, argv);

    MainWindow mainWindow;
    mainWindow.resize(800, 600);
    mainWindow.show();

    return app.exec();
}

#include "main.moc"
```

В этом коде добавлено меню с опцией загрузки файла JSON. После выбора и загрузки файла данные из JSON используются для обновления виджета `VisualizationWidget`, который отображает маршруты и периметры на форме.

Для тестирования можно использовать следующий пример JSON-файла:

```json
{
    "routes": [
        [
            {"start_x": 0, "start_y": 0, "end_x": 100, "end_y": 100, "speed": 10},
            {"start_x": 100, "start_y": 100, "end_x": 200, "end_y": 50, "speed": 20},
            {"start_x": 200, "start_y": 50, "end_x": 300, "end_y": 150, "speed": 30}
        ]
    ],
    "perimeter": [
        [
            {"x": 0, "y": 0},
            {"x": 400, "y": 0},
            {"x": 400, "y": 300},
            {"x": 0, "y": 300}
        ],
        [
            {"x": 100, "y": 100},
            {"x": 150, "y": 100},
            {"x": 150, "y": 150},
            {"x": 100, "y": 150}
        ]
    ]
}
```

Скопируйте и вставьте этот JSON в файл и используйте его для тестирования программы. Если у вас возникнут вопросы или потребуется дополнительная помощь, дайте знать! 📂✨ [A](https://github.com/kalyanraju/Bujji-Potato/tree/321b8f3b5bfd53095919776a7c13bcf71540f9c9/Build%2Fdata.js?copilot_analytics_metadata=eyJldmVudEluZm9fY29udmVyc2F0aW9uSWQiOiJqZmdLemd0NTgzR3lIanc1RXNZUWsiLCJldmVudEluZm9fY2xpY2tEZXN0aW5hdGlvbiI6Imh0dHBzOlwvXC9naXRodWIuY29tXC9rYWx5YW5yYWp1XC9CdWpqaS1Qb3RhdG9cL3RyZWVcLzMyMWI4ZjNiNWJmZDUzMDk1OTE5Nzc2YTdjMTNiY2Y3MTU0MGY5YzlcL0J1aWxkJTJGZGF0YS5qcyIsImV2ZW50SW5mb19tZXNzYWdlSWQiOiJ4QkpEVXhIOWJpQUF0TkxEVzdzdXciLCJldmVudEluZm9fY2xpY2tTb3VyY2UiOiJjaXRhdGlvbkxpbmsifQ%3D%3D&citationMarker=9F742443-6C92-4C44-BF58-8F5A7C53B6F1)