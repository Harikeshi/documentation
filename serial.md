��� ������������� ������ QtSerialPort � Qt5, ��������� ��������� ����:

�������� ������ QtSerialPort � ��� ������:

� ����� ������� .pro �������� ������:

```cpp
QT += serialport
```
������������ ����������� ������:

� ����� �������� ���� ���������� ����������� ������������ �����:

```cpp
#include <QSerialPort>
#include <QSerialPortInfo>
```

��������� � �������� ���������������� ����:

�������� ��������� ������ QSerialPort � ��������� ��� ���������:

```cpp
QSerialPort serialPort;
serialPort.setPortName("COM3"); // ������� ��� ������ �����
serialPort.setBaudRate(QSerialPort::Baud9600); // ���������� �������� �������� ������
serialPort.setDataBits(QSerialPort::Data8); // ���������� ���������� ������
serialPort.setStopBits(QSerialPort::OneStop); // ���������� ����-����
serialPort.setFlowControl(QSerialPort::NoFlowControl); // ���������� ���������� �������

if (!serialPort.open(QIODevice::ReadWrite)) {
    qDebug() << "�� ������� ������� ����:" << serialPort.errorString();
    return;
}
```
������ � ������ ������:

��� ������������ ������ ������ ����������� ������� � �����:

```cpp
connect(&serialPort, &QSerialPort::readyRead, this, &MainWindow::readData);

void MainWindow::readData() {
    QByteArray data = serialPort.readAll();
    // ��������� ���������� ������
}

// ��� ������ ������
QByteArray data = "Hello, world!";
serialPort.write(data);
```
�������� �����:

�� �������� ������� ���� ����� ���������� ������:

```cpp
serialPort.close
```