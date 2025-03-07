`QTransform` — это класс в Qt, который представляет собой 2D-аффинное преобразование. Он используется для выполнения различных геометрических преобразований, таких как перемещение, масштабирование, поворот, сдвиг и отражение. Эти преобразования применяются к координатам объектов (например, точек, линий, полигонов) при отрисовке.

### Основные методы `QTransform`

1. **`translate(dx, dy)`**  
   Перемещает координаты на `dx` по оси X и `dy` по оси Y.  
   Пример:
   ```cpp
   QTransform transform;
   transform.translate(100, 50); // Сдвиг на 100 по X и 50 по Y
   ```

2. **`scale(sx, sy)`**  
   Масштабирует координаты по осям X и Y. Если `sx` или `sy` отрицательные, происходит отражение.  
   Пример:
   ```cpp
   QTransform transform;
   transform.scale(2, 0.5); // Увеличивает по X в 2 раза, уменьшает по Y в 2 раза
   ```

3. **`rotate(angle)`**  
   Поворачивает координаты на заданный угол (в градусах) вокруг начала координат.  
   Пример:
   ```cpp
   QTransform transform;
   transform.rotate(45); // Поворот на 45 градусов
   ```

4. **`shear(sh, sv)`**  
   Применяет сдвиг (наклон) по осям X и Y.  
   Пример:
   ```cpp
   QTransform transform;
   transform.shear(0.5, 0); // Сдвиг по X на 0.5
   ```

5. **`reset()`**  
   Сбрасывает преобразование в единичную матрицу (т.е. отменяет все преобразования).  
   Пример:
   ```cpp
   QTransform transform;
   transform.translate(100, 50);
   transform.reset(); // Возврат к начальному состоянию
   ```

6. **`map(point)`**  
   Применяет преобразование к точке и возвращает новую точку.  
   Пример:
   ```cpp
   QTransform transform;
   transform.translate(100, 50);
   QPointF point(10, 20);
   QPointF transformedPoint = transform.map(point); // (110, 70)
   ```

7. **`mapRect(rect)`**  
   Применяет преобразование к прямоугольнику и возвращает новый прямоугольник.  
   Пример:
   ```cpp
   QTransform transform;
   transform.scale(2, 2);
   QRectF rect(10, 10, 50, 50);
   QRectF transformedRect = transform.mapRect(rect); // (20, 20, 100, 100)
   ```

8. **`inverted()`**  
   Возвращает обратное преобразование.  
   Пример:
   ```cpp
   QTransform transform;
   transform.translate(100, 50);
   QTransform inverseTransform = transform.inverted(); // Обратное преобразование
   ```

9. **`setMatrix(m11, m12, m13, m21, m22, m23, m31, m32, m33)`**  
   Устанавливает матрицу преобразования вручную.  
   Пример:
   ```cpp
   QTransform transform;
   transform.setMatrix(2, 0, 0, 0, 2, 0, 0, 0, 1); // Масштабирование в 2 раза
   ```

10. **`isIdentity()`**  
    Проверяет, является ли преобразование единичным (т.е. не изменяет координаты).  
    Пример:
    ```cpp
    QTransform transform;
    if (transform.isIdentity()) {
        qDebug() << "Преобразование не изменяет координаты";
    }
    ```

---

### Пример использования `QTransform`

```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QTransform>

class MyWidget : public QWidget {
protected:
    void paintEvent(QPaintEvent *event) override {
        Q_UNUSED(event);

        QPainter painter(this);

        // Создаем прямоугольник
        QRectF rect(50, 50, 100, 100);

        // Применяем преобразования
        QTransform transform;
        transform.translate(200, 100); // Сдвиг
        transform.rotate(45);          // Поворот на 45 градусов
        transform.scale(1.5, 1.5);     // Масштабирование

        // Применяем преобразование к painter
        painter.setTransform(transform);

        // Отрисовываем прямоугольник
        painter.drawRect(rect);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    MyWidget widget;
    widget.resize(400, 300);
    widget.show();

    return app.exec();
}
```

---

### Как работает `QTransform`

`QTransform` использует матрицу 3x3 для выполнения аффинных преобразований. Матрица выглядит так:

```
| m11  m12  m13 |
| m21  m22  m23 |
| m31  m32  m33 |
```

- **m11, m12, m21, m22** — отвечают за масштабирование, поворот и сдвиг.
- **m13, m23** — отвечают за перемещение.
- **m31, m32, m33** — используются для перспективных преобразований (обычно не используются в 2D).

Каждое преобразование (перемещение, масштабирование, поворот) изменяет значения этой матрицы. Когда вы применяете преобразование к точке, оно умножается на эту матрицу.

---

### Пример с отражением координат

Если нужно, чтобы начало координат было в левом нижнем углу (как в вашем случае), можно использовать `QTransform` для отражения по оси Y:

```cpp
QTransform transform;
transform.translate(0, height()); // Перемещаем начало координат вниз
transform.scale(1, -1);           // Отражаем по оси Y
painter.setTransform(transform);
```

Теперь координаты будут отсчитываться от левого нижнего угла, а ось Y будет направлена вверх.