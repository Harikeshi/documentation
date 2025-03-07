Использование паттерна **Модель-Представление-Контроллер (MVC)** в Qt — это мощный способ организации кода, особенно для приложений, работающих с данными. В Qt этот паттерн часто реализуется через **Модель-Представление (Model-View)**, где контроллер интегрирован в представление или модель. Давайте разберём, как это работает.

---

### Основные компоненты MVC в Qt

1. **Модель (Model)**:
   - Отвечает за данные и бизнес-логику.
   - В Qt модели реализуются через классы, унаследованные от `QAbstractItemModel` (например, `QStandardItemModel`, `QSqlTableModel`).

2. **Представление (View)**:
   - Отвечает за отображение данных.
   - В Qt представления реализуются через классы, унаследованные от `QAbstractItemView` (например, `QTableView`, `QListView`, `QTreeView`).

3. **Контроллер (Controller)**:
   - Управляет взаимодействием между моделью и представлением.
   - В Qt контроллер часто интегрирован в представление или реализуется через сигналы и слоты.

---

### Пример реализации MVC в Qt

Рассмотрим пример приложения, которое отображает список задач (TODO-лист) с использованием MVC.

---

#### 1. **Модель (Model)**

Модель будет хранить список задач. Мы создадим класс `TaskModel`, унаследованный от `QAbstractListModel`.

```cpp
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
    void addTask(const QString& task);
    void removeTask(int index);

private:
    QStringList m_tasks; // Список задач
};
```

Реализация модели:

```cpp
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

#### 2. **Представление (View)**

Представление будет отображать список задач. Мы используем `QListView`.

```cpp
#include <QListView>
#include <QStringListModel>

class TaskView : public QListView {
    Q_OBJECT

public:
    explicit TaskView(QWidget* parent = nullptr);
};
```

Реализация представления:

```cpp
#include "TaskView.h"

TaskView::TaskView(QWidget* parent) : QListView(parent) {
    setEditTriggers(QAbstractItemView::DoubleClicked | QAbstractItemView::EditKeyPressed);
}
```

---

#### 3. **Контроллер (Controller)**

Контроллер будет управлять взаимодействием между моделью и представлением. Мы создадим класс `TaskController`.

```cpp
#include <QObject>

class TaskModel;
class TaskView;

class TaskController : public QObject {
    Q_OBJECT

public:
    explicit TaskController(TaskModel* model, TaskView* view, QObject* parent = nullptr);

public slots:
    void addTask(const QString& task);
    void removeTask(int index);

private:
    TaskModel* m_model;
    TaskView* m_view;
};
```

Реализация контроллера:

```cpp
#include "TaskController.h"
#include "TaskModel.h"
#include "TaskView.h"

TaskController::TaskController(TaskModel* model, TaskView* view, QObject* parent)
    : QObject(parent), m_model(model), m_view(view) {
    // Связываем представление с моделью
    m_view->setModel(m_model);
}

void TaskController::addTask(const QString& task) {
    m_model->addTask(task);
}

void TaskController::removeTask(int index) {
    m_model->removeTask(index);
}
```

---

#### 4. **Основное окно приложения**

Создадим основное окно приложения, где будут использоваться модель, представление и контроллер.

```cpp
#include <QMainWindow>
#include <QVBoxLayout>
#include <QPushButton>
#include <QLineEdit>

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    explicit MainWindow(QWidget* parent = nullptr);

private slots:
    void onAddTask();
    void onRemoveTask();

private:
    TaskModel* m_model;
    TaskView* m_view;
    TaskController* m_controller;
    QLineEdit* m_taskInput;
};
```

Реализация основного окна:

```cpp
#include "MainWindow.h"
#include "TaskModel.h"
#include "TaskView.h"
#include "TaskController.h"

MainWindow::MainWindow(QWidget* parent) : QMainWindow(parent) {
    // Создаем модель, представление и контроллер
    m_model = new TaskModel(this);
    m_view = new TaskView(this);
    m_controller = new TaskController(m_model, m_view, this);

    // Создаем интерфейс
    QWidget* centralWidget = new QWidget(this);
    QVBoxLayout* layout = new QVBoxLayout(centralWidget);

    m_taskInput = new QLineEdit(this);
    QPushButton* addButton = new QPushButton("Добавить задачу", this);
    QPushButton* removeButton = new QPushButton("Удалить задачу", this);

    layout->addWidget(m_view);
    layout->addWidget(m_taskInput);
    layout->addWidget(addButton);
    layout->addWidget(removeButton);

    centralWidget->setLayout(layout);
    setCentralWidget(centralWidget);

    // Подключаем кнопки к слотам
    connect(addButton, &QPushButton::clicked, this, &MainWindow::onAddTask);
    connect(removeButton, &QPushButton::clicked, this, &MainWindow::onRemoveTask);
}

void MainWindow::onAddTask() {
    QString task = m_taskInput->text();
    if (!task.isEmpty()) {
        m_controller->addTask(task);
        m_taskInput->clear();
    }
}

void MainWindow::onRemoveTask() {
    QModelIndex index = m_view->currentIndex();
    if (index.isValid()) {
        m_controller->removeTask(index.row());
    }
}
```

---

#### 5. **Запуск приложения**

```cpp
#include <QApplication>
#include "MainWindow.h"

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    MainWindow window;
    window.show();

    return app.exec();
}
```

---

### Как это работает

1. **Модель**:
   - Хранит список задач.
   - Уведомляет представление об изменениях данных.

2. **Представление**:
   - Отображает список задач.
   - Позволяет редактировать задачи.

3. **Контроллер**:
   - Управляет добавлением и удалением задач.
   - Связывает модель и представление.

4. **Основное окно**:
   - Содержит интерфейс для взаимодействия с пользователем.
   - Использует контроллер для управления данными.

---

### Итог

- Паттерн MVC в Qt позволяет разделить данные, интерфейс и логику управления.
- Модель (`TaskModel`) отвечает за данные.
- Представление (`TaskView`) отображает данные.
- Контроллер (`TaskController`) управляет взаимодействием между моделью и представлением.
