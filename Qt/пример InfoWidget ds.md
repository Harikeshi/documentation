### 1. **Структура проекта**
- Виджет `WidgetInformation` будет представлять собой текстовое поле (`QTextEdit`), в которое будут записываться все сообщения.
- Другие виджеты или компоненты системы будут отправлять сообщения в `WidgetInformation` через сигналы и слоты.

### 2. **Класс `WidgetInformation`**
Создадим класс `WidgetInformation`, который будет наследоваться от `QWidget` и содержать `QTextEdit` для отображения сообщений.

```cpp
#include <QWidget>
#include <QTextEdit>
#include <QVBoxLayout>
#include <QString>

class WidgetInformation : public QWidget {
    Q_OBJECT

public:
    explicit WidgetInformation(QWidget *parent = nullptr) : QWidget(parent) {
        // Создаем текстовое поле для отображения сообщений
        textEdit = new QTextEdit(this);
        textEdit->setReadOnly(true); // Запрещаем редактирование

        // Настраиваем layout
        QVBoxLayout *layout = new QVBoxLayout(this);
        layout->addWidget(textEdit);
        setLayout(layout);
    }

public slots:
    // Слот для добавления сообщений
    void logMessage(const QString &message, const QString &level = "INFO") {
        QString formattedMessage = QString("[%1] %2\n").arg(level).arg(message);
        textEdit->append(formattedMessage); // Добавляем сообщение в текстовое поле
    }

private:
    QTextEdit *textEdit; // Текстовое поле для отображения сообщений
};
```

### 3. **Использование `WidgetInformation`**
Теперь создадим главное окно приложения, в котором будет использоваться `WidgetInformation`, и продемонстрируем, как другие виджеты могут отправлять сообщения.

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QPushButton>
#include <QDebug>

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    MainWindow(QWidget *parent = nullptr) : QMainWindow(parent) {
        // Создаем виджет информации
        widgetInformation = new WidgetInformation(this);
        setCentralWidget(widgetInformation);

        // Создаем кнопку для имитации событий
        QPushButton *button = new QPushButton("Simulate Events", this);
        connect(button, &QPushButton::clicked, this, &MainWindow::simulateEvents);

        // Добавляем кнопку в layout
        QVBoxLayout *layout = new QVBoxLayout();
        layout->addWidget(button);
        layout->addWidget(widgetInformation);

        QWidget *centralWidget = new QWidget(this);
        centralWidget->setLayout(layout);
        setCentralWidget(centralWidget);
    }

private slots:
    // Слот для имитации событий
    void simulateEvents() {
        widgetInformation->logMessage("Приложение запущено", "INFO");
        widgetInformation->logMessage("Файл успешно загружен", "INFO");
        widgetInformation->logMessage("Ошибка открытия файла: файл не найден", "ERROR");
        widgetInformation->logMessage("Отсутствует необходимый флаг", "WARNING");
        widgetInformation->logMessage("Исключение: деление на ноль", "ERROR");
    }

private:
    WidgetInformation *widgetInformation; // Виджет для отображения информации
};
```

### 4. **Основная функция `main`**
Запустим приложение.

```cpp
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    MainWindow mainWindow;
    mainWindow.show();

    return app.exec();
}

#include "main.moc"
```

### 5. **Объяснение кода**
1. **Класс `WidgetInformation`**:
   - Содержит `QTextEdit` для отображения сообщений.
   - Метод `logMessage` добавляет сообщение в текстовое поле с указанием уровня (INFO, WARNING, ERROR).

2. **Главное окно `MainWindow`**:
   - Содержит `WidgetInformation` и кнопку для имитации событий.
   - При нажатии на кнопку вызывается слот `simulateEvents`, который отправляет различные сообщения в `WidgetInformation`.

3. **Сигналы и слоты**:
   - В реальном приложении другие виджеты могут отправлять сообщения в `WidgetInformation` через сигналы и слоты.

4. **Форматирование сообщений**:
   - Сообщения форматируются с указанием уровня (INFO, WARNING, ERROR) и добавляются в `QTextEdit`.

### 6. **Результат**
При запуске приложения откроется окно с текстовым полем и кнопкой. При нажатии на кнопку в текстовое поле будут добавлены сообщения:
```
[INFO] Приложение запущено
[INFO] Файл успешно загружен
[ERROR] Ошибка открытия файла: файл не найден
[WARNING] Отсутствует необходимый флаг
[ERROR] Исключение: деление на ноль
```

### 7. **Расширение функциональности**
- **Цвета для уровней сообщений**: Можно добавить цвета для разных уровней сообщений (например, красный для ERROR, оранжевый для WARNING, зеленый для INFO).
- **Фильтрация сообщений**: Добавьте возможность фильтровать сообщения по уровню.
- **Сохранение логов**: Реализуйте возможность сохранения всех сообщений в файл.