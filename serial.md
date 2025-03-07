Для использования модуля QtSerialPort в Qt5, выполните следующие шаги:

Добавьте модуль QtSerialPort в ваш проект:

В файле проекта .pro добавьте строку:

```cpp
QT += serialport
```
Импортируйте необходимые классы:

В вашем исходном коде подключите необходимые заголовочные файлы:

```cpp
#include <QSerialPort>
#include <QSerialPortInfo>
```

Настройте и откройте последовательный порт:

Создайте экземпляр класса QSerialPort и настройте его параметры:

```cpp
QSerialPort serialPort;
serialPort.setPortName("COM3"); // Укажите имя вашего порта
serialPort.setBaudRate(QSerialPort::Baud9600); // Установите скорость передачи данных
serialPort.setDataBits(QSerialPort::Data8); // Установите количество данных
serialPort.setStopBits(QSerialPort::OneStop); // Установите стоп-биты
serialPort.setFlowControl(QSerialPort::NoFlowControl); // Установите управление потоком

if (!serialPort.open(QIODevice::ReadWrite)) {
    qDebug() << "Не удалось открыть порт:" << serialPort.errorString();
    return;
}
```
Чтение и запись данных:

Для асинхронного чтения данных используйте сигналы и слоты:

```cpp
connect(&serialPort, &QSerialPort::readyRead, this, &MainWindow::readData);

void MainWindow::readData() {
    QByteArray data = serialPort.readAll();
    // Обработка полученных данных
}

// Для записи данных
QByteArray data = "Hello, world!";
serialPort.write(data);
```
Закрытие порта:

Не забудьте закрыть порт после завершения работы:

```cpp
serialPort.close
```