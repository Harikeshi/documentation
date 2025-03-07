Паттерн **Model-View-ViewModel (MVVM)** — это архитектурный подход, который особенно популярен в разработке приложений с использованием современных фреймворков, таких как **Qt Quick** (QML) или **WPF**. MVVM разделяет приложение на три компонента:

1. **Model**:
   - Отвечает за данные и бизнес-логику.
   - Не зависит от интерфейса.

2. **View**:
   - Отвечает за отображение данных и взаимодействие с пользователем.
   - В Qt Quick это QML-файлы.

3. **ViewModel**:
   - Связывает Model и View.
   - Предоставляет данные и команды для View.
   - Не зависит от конкретной реализации View.

---

### Пример приложения с MVVM на Qt Quick (QML)

Рассмотрим простое приложение — список задач (TODO-лист), реализованное с использованием MVVM.

---

#### 1. **Модель (Model)**

Модель будет хранить список задач. Мы создадим класс `TaskModel`, который наследуется от `QAbstractListModel`.

```cpp
// TaskModel.h
#ifndef TASKMODEL_H
#define TASKMODEL_H

#include <QAbstractListModel>
#include <QStringList>

class TaskModel : public QAbstractListModel {
    Q_OBJECT

public:
    explicit TaskModel(QObject* parent = nullptr);

    // Переопределение методов модели
    int rowCount(const QModelIndex& parent = QModelIndex()) const override;
    QVariant data(const QModelIndex& index, int role = Qt::DisplayRole) const override;
    bool setData(const QModelIndex& index, const QVariant& value, int role = Qt::EditRole) override;
    Qt::ItemFlags flags(const QModelIndex& index) const override;

    // Методы для добавления и удаления задач
    Q_INVOKABLE void addTask(const QString& task);
    Q_INVOKABLE void removeTask(int index);

private:
    QStringList m_tasks; // Список задач
};

#endif // TASKMODEL_H
```

Реализация модели:

```cpp
// TaskModel.cpp
#include "TaskModel.h"

TaskModel::TaskModel(QObject* parent) : QAbstractListModel(parent) {}

int TaskModel::rowCount(const QModelIndex& parent) const {
    Q_UNUSED(parent);
    return m_tasks.size();
}

QVariant TaskModel::data(const QModelIndex& index, int role) const {
    if (!index.isValid() || index.row() >= m_tasks.size())
        return QVariant();

    if (role == Qt::DisplayRole || role == Qt::EditRole)
        return m_tasks.at(index.row());

    return QVariant();
}

bool TaskModel::setData(const QModelIndex& index, const QVariant& value, int role) {
    if (index.isValid() && role == Qt::EditRole) {
        m_tasks[index.row()] = value.toString();
        emit dataChanged(index, index, {role});
        return true;
    }
    return false;
}

Qt::ItemFlags TaskModel::flags(const QModelIndex& index) const {
    if (!index.isValid())
        return Qt::ItemIsEnabled;

    return QAbstractListModel::flags(index) | Qt::ItemIsEditable;
}

void TaskModel::addTask(const QString& task) {
    beginInsertRows(QModelIndex(), m_tasks.size(), m_tasks.size());
    m_tasks.append(task);
    endInsertRows();
}

void TaskModel::removeTask(int index) {
    if (index < 0 || index >= m_tasks.size())
        return;

    beginRemoveRows(QModelIndex(), index, index);
    m_tasks.removeAt(index);
    endRemoveRows();
}
```

---

#### 2. **ViewModel**

ViewModel будет связывать Model и View. Мы создадим класс `TaskViewModel`, который предоставляет данные и команды для View.

```cpp
// TaskViewModel.h
#ifndef TASKVIEWMODEL_H
#define TASKVIEWMODEL_H

#include <QObject>
#include "TaskModel.h"

class TaskViewModel : public QObject {
    Q_OBJECT
    Q_PROPERTY(TaskModel* taskModel READ taskModel CONSTANT)

public:
    explicit TaskViewModel(QObject* parent = nullptr);

    TaskModel* taskModel() const;

    Q_INVOKABLE void addTask(const QString& task);
    Q_INVOKABLE void removeTask(int index);

private:
    TaskModel* m_taskModel;
};

#endif // TASKVIEWMODEL_H
```

Реализация ViewModel:

```cpp
// TaskViewModel.cpp
#include "TaskViewModel.h"

TaskViewModel::TaskViewModel(QObject* parent) : QObject(parent) {
    m_taskModel = new TaskModel(this);
}

TaskModel* TaskViewModel::taskModel() const {
    return m_taskModel;
}

void TaskViewModel::addTask(const QString& task) {
    m_taskModel->addTask(task);
}

void TaskViewModel::removeTask(int index) {
    m_taskModel->removeTask(index);
}
```

---

#### 3. **View (QML)**

View реализуется на QML. Мы создадим файл `main.qml`, который будет отображать список задач и предоставлять интерфейс для добавления и удаления задач.

```qml
// main.qml
import QtQuick 2.15
import QtQuick.Controls 2.15

ApplicationWindow {
    visible: true
    width: 400
    height: 600
    title: "TODO List"

    // Связываем ViewModel с View
    TaskViewModel {
        id: taskViewModel
    }

    Column {
        anchors.fill: parent
        spacing: 10

        // Поле для ввода новой задачи
        TextField {
            id: taskInput
            width: parent.width
            placeholderText: "Введите задачу"
            onAccepted: {
                taskViewModel.addTask(text);
                text = "";
            }
        }

        // Список задач
        ListView {
            width: parent.width
            height: parent.height - taskInput.height - removeButton.height - 20
            model: taskViewModel.taskModel
            delegate: Text {
                text: modelData
                font.pixelSize: 18
                padding: 10
            }
        }

        // Кнопка для удаления задачи
        Button {
            id: removeButton
            width: parent.width
            text: "Удалить задачу"
            onClicked: {
                if (ListView.currentIndex >= 0) {
                    taskViewModel.removeTask(ListView.currentIndex);
                }
            }
        }
    }
}
```

---

#### 4. **Регистрация ViewModel в QML**

Чтобы использовать ViewModel в QML, нужно зарегистрировать его как QML-тип. Это делается в `main.cpp`.

```cpp
// main.cpp
#include <QGuiApplication>
#include <QQmlApplicationEngine>
#include "TaskViewModel.h"

int main(int argc, char *argv[]) {
    QGuiApplication app(argc, argv);

    // Регистрируем ViewModel как QML-тип
    qmlRegisterType<TaskViewModel>("com.example", 1, 0, "TaskViewModel");

    QQmlApplicationEngine engine;
    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));

    return app.exec();
}
```

---

### Как это работает

1. **Model**:
   - Хранит список задач.
   - Уведомляет View об изменениях данных.

2. **ViewModel**:
   - Предоставляет данные и команды для View.
   - Связывает Model и View.

3. **View (QML)**:
   - Отображает список задач.
   - Позволяет добавлять и удалять задачи.

---

### Итог

- **MVVM** — это мощный подход для разделения данных, логики и интерфейса.
- В Qt Quick (QML) MVVM реализуется с использованием:
  - **Model** (например, `QAbstractListModel`).
  - **ViewModel** (класс, предоставляющий данные и команды).
  - **View** (QML-файлы).
- Этот подход делает код более модульным, тестируемым и легко расширяемым.