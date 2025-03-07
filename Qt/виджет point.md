Для создания формы в Qt, которая включает:
1. **Label** для отображения точки на плоскости.
2. **Два поля** для ввода координат точки.
3. **Одно поле** для ввода скорости.
4. **Два поля** для вывода текущих координат и скорости.
5. **Четыре кнопки**: "Точка", "Пуск", "Сброс", "Загрузить из JSON файла".

Мы создадим пользовательский виджет (`QWidget`), который будет содержать все эти элементы. Также добавим логику для обработки нажатий кнопок и загрузки данных из JSON файла.

### Пример реализации:

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QHBoxLayout>
#include <QLabel>
#include <QLineEdit>
#include <QPushButton>
#include <QFileDialog>
#include <QFile>
#include <QJsonDocument>
#include <QJsonObject>
#include <QJsonValue>
#include <QMessageBox>

class PointWidget : public QWidget {
    Q_OBJECT

public:
    PointWidget(QWidget *parent = nullptr) : QWidget(parent) {
        // Основной layout
        QVBoxLayout *mainLayout = new QVBoxLayout(this);

        // Label для отображения точки
        pointLabel = new QLabel("Точка: (0, 0)", this);
        mainLayout->addWidget(pointLabel);

        // Поля для ввода координат
        QHBoxLayout *inputLayout = new QHBoxLayout();
        inputLayout->addWidget(new QLabel("X:", this));
        xInput = new QLineEdit(this);
        inputLayout->addWidget(xInput);

        inputLayout->addWidget(new QLabel("Y:", this));
        yInput = new QLineEdit(this);
        inputLayout->addWidget(yInput);

        mainLayout->addLayout(inputLayout);

        // Поле для ввода скорости
        QHBoxLayout *speedLayout = new QHBoxLayout();
        speedLayout->addWidget(new QLabel("Скорость:", this));
        speedInput = new QLineEdit(this);
        speedLayout->addWidget(speedInput);

        mainLayout->addLayout(speedLayout);

        // Поля для вывода текущих координат и скорости
        QHBoxLayout *outputLayout = new QHBoxLayout();
        outputLayout->addWidget(new QLabel("Текущие координаты:", this));
        currentCoordsLabel = new QLabel("(0, 0)", this);
        outputLayout->addWidget(currentCoordsLabel);

        outputLayout->addWidget(new QLabel("Текущая скорость:", this));
        currentSpeedLabel = new QLabel("0", this);
        outputLayout->addWidget(currentSpeedLabel);

        mainLayout->addLayout(outputLayout);

        // Кнопки
        QHBoxLayout *buttonLayout = new QHBoxLayout();
        QPushButton *pointButton = new QPushButton("Точка", this);
        QPushButton *startButton = new QPushButton("Пуск", this);
        QPushButton *resetButton = new QPushButton("Сброс", this);
        QPushButton *loadButton = new QPushButton("Загрузить из JSON", this);

        buttonLayout->addWidget(pointButton);
        buttonLayout->addWidget(startButton);
        buttonLayout->addWidget(resetButton);
        buttonLayout->addWidget(loadButton);

        mainLayout->addLayout(buttonLayout);

        // Подключение сигналов кнопок к слотам
        connect(pointButton, &QPushButton::clicked, this, &PointWidget::setPoint);
        connect(startButton, &QPushButton::clicked, this, &PointWidget::start);
        connect(resetButton, &QPushButton::clicked, this, &PointWidget::reset);
        connect(loadButton, &QPushButton::clicked, this, &PointWidget::loadFromJson);
    }

private slots:
    // Установка точки
    void setPoint() {
        bool okX, okY;
        qreal x = xInput->text().toDouble(&okX);
        qreal y = yInput->text().toDouble(&okY);

        if (okX && okY) {
            pointLabel->setText(QString("Точка: (%1, %2)").arg(x).arg(y));
            currentCoordsLabel->setText(QString("(%1, %2)").arg(x).arg(y));
        } else {
            QMessageBox::warning(this, "Ошибка", "Некорректные координаты");
        }
    }

    // Пуск
    void start() {
        bool okSpeed;
        qreal speed = speedInput->text().toDouble(&okSpeed);

        if (okSpeed) {
            currentSpeedLabel->setText(QString::number(speed));
        } else {
            QMessageBox::warning(this, "Ошибка", "Некорректная скорость");
        }
    }

    // Сброс
    void reset() {
        xInput->clear();
        yInput->clear();
        speedInput->clear();
        pointLabel->setText("Точка: (0, 0)");
        currentCoordsLabel->setText("(0, 0)");
        currentSpeedLabel->setText("0");
    }

    // Загрузка из JSON
    void loadFromJson() {
        QString fileName = QFileDialog::getOpenFileName(this, "Открыть JSON файл", "", "JSON Files (*.json)");
        if (fileName.isEmpty()) return;

        QFile file(fileName);
        if (!file.open(QIODevice::ReadOnly)) {
            QMessageBox::warning(this, "Ошибка", "Не удалось открыть файл");
            return;
        }

        QByteArray data = file.readAll();
        file.close();

        QJsonDocument doc = QJsonDocument::fromJson(data);
        if (doc.isNull()) {
            QMessageBox::warning(this, "Ошибка", "Некорректный JSON файл");
            return;
        }

        QJsonObject json = doc.object();
        if (json.contains("x") && json.contains("y") && json.contains("speed")) {
            qreal x = json["x"].toDouble();
            qreal y = json["y"].toDouble();
            qreal speed = json["speed"].toDouble();

            xInput->setText(QString::number(x));
            yInput->setText(QString::number(y));
            speedInput->setText(QString::number(speed));

            setPoint();
            start();
        } else {
            QMessageBox::warning(this, "Ошибка", "JSON файл не содержит необходимых данных");
        }
    }

private:
    QLabel *pointLabel;
    QLineEdit *xInput;
    QLineEdit *yInput;
    QLineEdit *speedInput;
    QLabel *currentCoordsLabel;
    QLabel *currentSpeedLabel;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    PointWidget widget;
    widget.resize(400, 300);
    widget.show();

    return app.exec();
}

#include "main.moc"
```

### Объяснение:
1. **Интерфейс**:
   - `pointLabel` — отображает текущую точку.
   - `xInput`, `yInput` — поля для ввода координат.
   - `speedInput` — поле для ввода скорости.
   - `currentCoordsLabel`, `currentSpeedLabel` — отображают текущие координаты и скорость.
   - Кнопки "Точка", "Пуск", "Сброс", "Загрузить из JSON".

2. **Логика**:
   - **Точка**: При нажатии кнопки "Точка" координаты из полей ввода применяются к точке.
   - **Пуск**: При нажатии кнопки "Пуск" скорость из поля ввода применяется к текущей скорости.
   - **Сброс**: При нажатии кнопки "Сброс" все поля очищаются, и значения сбрасываются к начальным.
   - **Загрузить из JSON**: При нажатии кнопки "Загрузить из JSON" открывается диалог выбора файла, данные из JSON файла загружаются в поля.

3. **JSON файл**:
   - Ожидается, что JSON файл будет содержать ключи `x`, `y` и `speed` с числовыми значениями.

### Пример JSON файла:
```json
{
    "x": 100,
    "y": 200,
    "speed": 50
}
```

### Примечания:
- Вы можете расширить функциональность, добавив валидацию ввода, обработку ошибок и другие улучшения.
- Если нужно сохранять данные в JSON, можно добавить соответствующую функциональность.