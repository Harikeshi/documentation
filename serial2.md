����� ��������� ������ ������������� ����������������� ����� � C++ � �������������� ���������� Qt � ������ QtSerialPort. ������ ����� �������� �������� �������� ����������, ������� ����� ������������ � ����������������� �����, ������ ������ � ���������� �� � ��������� ����.

���� �� �������� ����������
�������� �������:

�������� ����� ������ � Qt Creator ��� ��������� ���� ������ CMake.

������������ CMake:

���� �� ����������� CMake, ���������, ��� ��� CMakeLists.txt ���� �������� ���������:

```cmake
cmake_minimum_required(VERSION 3.5)
project(SerialPortExample)

set(CMAKE_CXX_STANDARD 11)

find_package(Qt5 COMPONENTS Widgets SerialPort REQUIRED)

add_executable(SerialPortExample main.cpp mainwindow.cpp mainwindow.h mainwindow.ui)
target_link_libraries(SerialPortExample Qt5::Widgets Qt5::SerialPort)
```
����� �������:

�������� ��� �����: main.cpp, mainwindow.h � mainwindow.cpp. ����� �������� ���� ���������� mainwindow.ui.

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

    // ������������� ��������� ����������������� �����
    serial->setPortName("COM3"); // ������� ��� ����
    serial->setBaudRate(QSerialPort::Baud9600);
    serial->setDataBits(QSerialPort::Data8);
    serial->setStopBits(QSerialPort::OneStop);
    serial->setFlowControl(QSerialPort::NoFlowControl);

    if (!serial->open(QIODevice::ReadWrite)) {
        QMessageBox::critical(this, "Error", serial->errorString());
        return;
    }

    // ��������� ������ readyRead � ����� ������ readSerialData
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

����������� Qt Designer ��� ���������� �������� ����������� � Qt Creator ��� �������� ����������������� ����������. �������� ������ QTextEdit � ������� ���� � ��������� ��� ��� textEdit.

��������� � ����
� mainwindow.h �� ��������� ����� MainWindow, ������� ����������� �� QMainWindow. �� ����� ��������� ����� � ���������� ��� ������ � ���������������� ������.

� mainwindow.cpp �� ����������� ���������������� ���� � ������������ ������ � ���������� ������ readyRead � ����� readSerialData.

� ����� readSerialData �� ������ ������ �� ����������������� ����� � ���������� �� � ��������� ���� QTextEdit.

��������� ����������, � ��� ����� ������������ � ���������� ����������������� �����, ������ ������ � ���������� �� � ��������� ����.