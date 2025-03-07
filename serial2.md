Более подробный пример использования последовательного порта в C++ с использованием библиотеки Qt и модуля QtSerialPort. Пример будет включать создание простого приложения, которое будет подключаться к последовательному порту, читать данные и отображать их в текстовом поле.

Шаги по созданию приложения
Создание проекта:

Создайте новый проект в Qt Creator или настройте свой проект CMake.

Конфигурация CMake:

Если вы используете CMake, убедитесь, что ваш CMakeLists.txt файл настроен правильно:

```cmake
cmake_minimum_required(VERSION 3.5)
project(SerialPortExample)

set(CMAKE_CXX_STANDARD 11)

find_package(Qt5 COMPONENTS Widgets SerialPort REQUIRED)

add_executable(SerialPortExample main.cpp mainwindow.cpp mainwindow.h mainwindow.ui)
target_link_libraries(SerialPortExample Qt5::Widgets Qt5::SerialPort)
```
Файлы проекта:

Создайте три файла: main.cpp, mainwindow.h и mainwindow.cpp. Также создайте файл интерфейса mainwindow.ui.

main.cpp:

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
mainwindow.h:

```cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QSerialPort>
#include <QSerialPortInfo>

QT_BEGIN_NAMESPACE
namespace Ui { class MainWindow; }
QT_END_NAMESPACE

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

private slots:
    void readSerialData();

private:
    Ui::MainWindow *ui;
    QSerialPort *serial;
};

#endif // MAINWINDOW_H
```
mainwindow.cpp:

```cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QMessageBox>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
    , serial(new QSerialPort(this))
{
    ui->setupUi(this);

    // Устанавливаем настройки последовательного порта
    serial->setPortName("COM3"); // Укажите ваш порт
    serial->setBaudRate(QSerialPort::Baud9600);
    serial->setDataBits(QSerialPort::Data8);
    serial->setStopBits(QSerialPort::OneStop);
    serial->setFlowControl(QSerialPort::NoFlowControl);

    if (!serial->open(QIODevice::ReadWrite)) {
        QMessageBox::critical(this, "Error", serial->errorString());
        return;
    }

    // Соединяем сигнал readyRead с нашим слотом readSerialData
    connect(serial, &QSerialPort::readyRead, this, &MainWindow::readSerialData);
}

MainWindow::~MainWindow() {
    if (serial->isOpen()) {
        serial->close();
    }
    delete ui;
}

void MainWindow::readSerialData() {
    QByteArray data = serial->readAll();
    ui->textEdit->append(data);
}
```
mainwindow.ui:

Используйте Qt Designer или встроенный редактор интерфейсов в Qt Creator для создания пользовательского интерфейса. Добавьте виджет QTextEdit в главное окно и присвойте ему имя textEdit.

Пояснения к коду
В mainwindow.h мы объявляем класс MainWindow, который наследуется от QMainWindow. Мы также объявляем слоты и переменные для работы с последовательным портом.

В mainwindow.cpp мы настраиваем последовательный порт в конструкторе класса и подключаем сигнал readyRead к слоту readSerialData.

В слоте readSerialData мы читаем данные из последовательного порта и отображаем их в текстовом поле QTextEdit.

Запустите приложение, и оно будет подключаться к указанному последовательному порту, читать данные и отображать их в текстовом поле.