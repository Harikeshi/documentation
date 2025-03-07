Для создания интерфейса в `QMainWindow`, где:
1. **Левую часть** занимает один большой виджет.
2. **Правую часть** занимают три виджета, расположенные вертикально и разделенные `QSplitter`.
3. Вся компоновка разделена с помощью `QSplitter` на левую и правую части.

Мы используем `QSplitter` для создания разделяемых областей. Вот пример реализации:

### Пример кода:

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QSplitter>
#include <QWidget>
#include <QVBoxLayout>
#include <QLabel>

class MainWindow : public QMainWindow {
public:
    MainWindow(QWidget *parent = nullptr) : QMainWindow(parent) {
        // Создаем главный splitter (горизонтальный)
        QSplitter *mainSplitter = new QSplitter(Qt::Horizontal, this);

        // Левый виджет (большой)
        QWidget *leftWidget = new QWidget(this);
        leftWidget->setStyleSheet("background-color: lightblue;");
        QLabel *leftLabel = new QLabel("Левый виджет", leftWidget);
        QVBoxLayout *leftLayout = new QVBoxLayout(leftWidget);
        leftLayout->addWidget(leftLabel);
        leftWidget->setLayout(leftLayout);

        // Правый splitter (вертикальный)
        QSplitter *rightSplitter = new QSplitter(Qt::Vertical, this);

        // Верхний правый виджет
        QWidget *rightTopWidget = new QWidget(this);
        rightTopWidget->setStyleSheet("background-color: lightgreen;");
        QLabel *rightTopLabel = new QLabel("Верхний правый виджет", rightTopWidget);
        QVBoxLayout *rightTopLayout = new QVBoxLayout(rightTopWidget);
        rightTopLayout->addWidget(rightTopLabel);
        rightTopWidget->setLayout(rightTopLayout);

        // Средний правый виджет
        QWidget *rightMiddleWidget = new QWidget(this);
        rightMiddleWidget->setStyleSheet("background-color: lightyellow;");
        QLabel *rightMiddleLabel = new QLabel("Средний правый виджет", rightMiddleWidget);
        QVBoxLayout *rightMiddleLayout = new QVBoxLayout(rightMiddleWidget);
        rightMiddleLayout->addWidget(rightMiddleLabel);
        rightMiddleWidget->setLayout(rightMiddleLayout);

        // Нижний правый виджет
        QWidget *rightBottomWidget = new QWidget(this);
        rightBottomWidget->setStyleSheet("background-color: lightpink;");
        QLabel *rightBottomLabel = new QLabel("Нижний правый виджет", rightBottomWidget);
        QVBoxLayout *rightBottomLayout = new QVBoxLayout(rightBottomWidget);
        rightBottomLayout->addWidget(rightBottomLabel);
        rightBottomWidget->setLayout(rightBottomLayout);

        // Добавляем правые виджеты в правый splitter
        rightSplitter->addWidget(rightTopWidget);
        rightSplitter->addWidget(rightMiddleWidget);
        rightSplitter->addWidget(rightBottomWidget);

        // Добавляем левый и правый splitter в главный splitter
        mainSplitter->addWidget(leftWidget);
        mainSplitter->addWidget(rightSplitter);

        // Устанавливаем главный splitter как центральный виджет
        setCentralWidget(mainSplitter);

        // Настройка размеров splitter
        mainSplitter->setStretchFactor(0, 2); // Левый виджет занимает 2/3 пространства
        mainSplitter->setStretchFactor(1, 1); // Правый splitter занимает 1/3 пространства
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    MainWindow window;
    window.resize(800, 600);
    window.show();

    return app.exec();
}
```

### Объяснение:
1. **Главный `QSplitter`**:
   - Создается горизонтальный `QSplitter`, который разделяет окно на левую и правую части.
   - Левый виджет добавляется в левую часть, а правый вертикальный `QSplitter` — в правую часть.

2. **Правый `QSplitter`**:
   - Создается вертикальный `QSplitter`, который содержит три виджета: верхний, средний и нижний.
   - Каждый из этих виджетов имеет свой цвет фона и метку для наглядности.

3. **Распределение пространства**:
   - Левый виджет занимает большую часть пространства (2/3), а правый `QSplitter` — меньшую часть (1/3).
   - Это достигается с помощью метода `setStretchFactor`.

4. **Центральный виджет**:
   - Главный `QSplitter` устанавливается как центральный виджет `QMainWindow`.

### Результат:
- Окно будет разделено на две части: левую (большую) и правую (три вертикальных виджета).
- Пользователь может изменять размеры областей, перетаскивая разделители.

### Примечания:
- Вы можете заменить `QLabel` и цвета фона на любые другие виджеты, которые вам нужны.
- Если нужно сохранить размеры разделителей при закрытии и открытии приложения, можно использовать `QSettings` для сохранения и восстановления состояния `QSplitter`.