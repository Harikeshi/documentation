### Создание файла `mainwindow.ui`

1. **Создайте файл `mainwindow.ui`**:
    - Откройте Qt Creator.
    - Создайте новый проект на основе шаблона "Qt Widgets Application".
    - Убедитесь, что в структуре проекта есть файл `mainwindow.ui`.

2. **Настройте интерфейс `mainwindow.ui`**:
    - Откройте `mainwindow.ui` в дизайнере интерфейсов (Qt Designer).
    - Добавьте виджет `QTextEdit` для отображения текстовых данных.
    - Добавьте другие необходимые элементы интерфейса, если нужно.

### Генерация файла `ui_mainwindow.h`

1. **Сохраните и закройте `mainwindow.ui`**: После настройки интерфейса сохраните изменения.

2. **Скомпилируйте проект**:
    - Когда вы скомпилируете проект, Qt Creator автоматически запустит `uic`, который сгенерирует файл `ui_mainwindow.h` на основе вашего `mainwindow.ui`.

Файл `ui_mainwindow.h` будет автоматически сгенерирован и включен в сборку проекта. Вам не нужно вручную редактировать этот файл.

Примерно, ваш файл `ui_mainwindow.h` может выглядеть так:

```cpp
/********************************************************************************
** Form generated from reading UI file 'mainwindow.ui'
**
** Created by: Qt User Interface Compiler version 5.x.x
**
** WARNING! All changes made in this file will be lost when recompiling UI file!
********************************************************************************/

#ifndef UI_MAINWINDOW_H
#define UI_MAINWINDOW_H

#include <QtCore/QVariant>
#include <QtWidgets/QAction>
#include <QtWidgets/QApplication>
#include <QtWidgets/QMainWindow>
#include <QtWidgets/QMenuBar>
#include <QtWidgets/QStatusBar>
#include <QtWidgets/QTextEdit>
#include <QtWidgets/QToolBar>
#include <QtWidgets/QWidget>

QT_BEGIN_NAMESPACE

class Ui_MainWindow
{
public:
    QWidget *centralWidget;
    QTextEdit *textEdit;
    QMenuBar *menuBar;
    QToolBar *mainToolBar;
    QStatusBar *statusBar;

    void setupUi(QMainWindow *MainWindow)
    {
        if (MainWindow->objectName().isEmpty())
            MainWindow->setObjectName(QString::fromUtf8("MainWindow"));
        MainWindow->resize(400, 300);
        centralWidget = new QWidget(MainWindow);
        centralWidget->setObjectName(QString::fromUtf8("centralWidget"));
        textEdit = new QTextEdit(centralWidget);
        textEdit->setObjectName(QString::fromUtf8("textEdit"));
        textEdit->setGeometry(QRect(10, 10, 380, 280));
        MainWindow->setCentralWidget(centralWidget);
        menuBar = new QMenuBar(MainWindow);
        menuBar->setObjectName(QString::fromUtf8("menuBar"));
        MainWindow->setMenuBar(menuBar);
        mainToolBar = new QToolBar(MainWindow);
        mainToolBar->setObjectName(QString::fromUtf8("mainToolBar"));
        MainWindow->addToolBar(Qt::TopToolBarArea, mainToolBar);
        statusBar = new QStatusBar(MainWindow);
        statusBar->setObjectName(QString::fromUtf8("statusBar"));
        MainWindow->setStatusBar(statusBar);

        retranslateUi(MainWindow);

        QMetaObject::connectSlotsByName(MainWindow);
    } // setupUi

    void retranslateUi(QMainWindow *MainWindow)
    {
        MainWindow->setWindowTitle(QCoreApplication::translate("MainWindow", "MainWindow", nullptr));
    } // retranslateUi

};

namespace Ui {
    class MainWindow: public Ui_MainWindow {};
} // namespace Ui

QT_END_NAMESPACE

#endif // UI_MAINWINDOW_H
```