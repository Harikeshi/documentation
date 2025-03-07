Для создания виджета `InformationWidget`, который будет отображать всю информацию, поступающую из других виджетов, ошибки, успешные загрузки и прочее, можно использовать сигнал-сигнальный механизм Qt. Этот подход позволит отправлять сообщения между виджетами и обновлять текстовый виджет с информацией.

Вот пример, как это можно реализовать:

1. **Создание InformationWidget**:
```cpp
#include <QApplication>
#include <QWidget>
#include <QTextEdit>
#include <QVBoxLayout>
#include <QString>

class InformationWidget : public QWidget {
    Q_OBJECT

public:
    InformationWidget(QWidget *parent = nullptr) : QWidget(parent) {
        QVBoxLayout *layout = new QVBoxLayout(this);
        infoTextEdit = new QTextEdit(this);
        infoTextEdit->setReadOnly(true);
        layout->addWidget(infoTextEdit);
        setLayout(layout);
    }

public slots:
    void logMessage(const QString &message) {
        infoTextEdit->append(message);
    }

private:
    QTextEdit *infoTextEdit;
};
```

2. **Создание другого виджета для отправки сообщений**:
```cpp
#include <QPushButton>

class TestWidget : public QWidget {
    Q_OBJECT

public:
    TestWidget(QWidget *parent = nullptr) : QWidget(parent) {
        QPushButton *button = new QPushButton("Send Message", this);
        connect(button, &QPushButton::clicked, this, &TestWidget::sendMessage);
    }

signals:
    void newMessage(const QString &message);

private slots:
    void sendMessage() {
        emit newMessage("Test message from TestWidget");
    }
};
```

3. **Основной файл для связывания виджетов**:
```cpp
#include <QApplication>
#include "InformationWidget.h"
#include "TestWidget.h"

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    InformationWidget infoWidget;
    TestWidget testWidget;

    QObject::connect(&testWidget, &TestWidget::newMessage, &infoWidget, &InformationWidget::logMessage);

    infoWidget.show();
    testWidget.show();

    return app.exec();
}
```

Этот код создает два виджета: `InformationWidget`, который отображает информацию в `QTextEdit`, и `TestWidget`, который отправляет сообщение при нажатии на кнопку. Используется сигнал-сигнальный механизм Qt для связывания этих виджетов.

Вы можете расширить этот пример, добавляя больше виджетов, отправляющих сообщения с разной информацией (ошибки, успешные загрузки, отсутствие флагов, исключения, старт и т.д.), и подключая их сигналы к слоту `logMessage` в `InformationWidget`.

Если у вас есть вопросы или нужна дополнительная помощь, дайте знать!