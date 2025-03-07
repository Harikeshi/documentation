### Создание простого примера MOC

1. База данных
```cpp
   // Database.h
   class Database {
   public:
       virtual bool connect() = 0;
       virtual bool disconnect() = 0;
       virtual int getUserCount() = 0;
   };
```
2. Пользователь
```cpp 
   // User.h
   #include "Database.h"
   
   class User {
   private:
       Database& db;
   public:
       User(Database& database) : db(database) {}
       
       bool init() {
           return db.connect();
       }
       
       bool close() {
           return db.disconnect();
       }

       int getUserCount() {
           return db.getUserCount();
       }
   };
```   
3. Создание мок-класса:
   Используем Google Mock для создания мок-класса:
```cpp
   // MockDatabase.h
   #include "gmock/gmock.h"
   #include "Database.h"
   
   class MockDatabase : public Database {
   public:
       MOCK_METHOD(bool, connect, (), (override));
       MOCK_METHOD(bool, disconnect, (), (override));
       MOCK_METHOD(int, getUserCount, (), (override));
   };
```
4. Написание тестов:
   Тесты для класса User:
```cpp
   // UserTest.cpp
   #include "gtest/gtest.h"
   #include "MockDatabase.h"
   #include "User.h"
   
   TEST(UserTest, InitTest) {
       MockDatabase mockDb;
       User user(mockDb);
       
       EXPECT_CALL(mockDb, connect())
           .Times(1)
           .WillOnce(testing::Return(true));
       
       EXPECT_TRUE(user.init());
   }

   TEST(UserTest, CloseTest) {
       MockDatabase mockDb;
       User user(mockDb);
       
       EXPECT_CALL(mockDb, disconnect())
           .Times(1)
           .WillOnce(testing::Return(true));
       
       EXPECT_TRUE(user.close());
   }

   TEST(UserTest, GetUserCountTest) {
       MockDatabase mockDb;
       User user(mockDb);
       
       EXPECT_CALL(mockDb, getUserCount())
           .Times(1)
           .WillOnce(testing::Return(42));
       
       EXPECT_EQ(user.getUserCount(), 42);
   }
```
5. Сборка и запуск тестов:
   Настройте ваш проект для использования `Google Test` и `Google Mock`, соберите проект и запустите тесты. Убедитесь, что все зависимости корректно добавлены в ваш `CMakeLists.txt` или другой файл сборки.

### Пример `CMakeLists.txt`:
```
cmake_minimum_required(VERSION 3.10)
project(MyProject)

set(CMAKE_CXX_STANDARD 14)
```
### Установите путь к `Google Test` и `Google Mock`
```
add_subdirectory(googletest)

include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})
```
### Исходные файлы проекта
```
add_executable(MyProject main.cpp UserTest.cpp)
```
### Связываем проект с библиотеками `Google Test` и `Google Mock`
```
target_link_libraries(MyProject gtest gtest_main)
target_link_libraries(MyProject gmock gmock_main)
```
### Примеры мок-тестирования без интерфейсов

Вы можете использовать мок-объекты и без интерфейсов, но это потребует некоторой настройки. Допустим, у нас есть класс `Database`, который мы хотим заменить мок-объектом для тестирования.
```cpp
// Database.h
class Database {
public:
    bool connect() { /* Реализация подключения */ }
    bool disconnect() { /* Реализация отключения */ }
    int getUserCount() { /* Реализация получения количества пользователей */ }
};
```
### Мок-объект для класса без интерфейса

Для создания мок-объекта класса `Database` нам нужно будет изменить класс, чтобы позволить замену методов.

1. Используйте наследование:
   Создайте мок-класс, наследующийся от оригинального класса и переопределяющий методы:
```cpp
   // MockDatabase.h
   #include "gmock/gmock.h"
   #include "Database.h"
   
   class MockDatabase : public Database {
   public:
       MOCK_METHOD(bool, connect, (), (override));
       MOCK_METHOD(bool, disconnect, (), (override));
       MOCK_METHOD(int, getUserCount, (), (override));
   };
```  
2. Написание тестов:
   Используйте мок-класс в тестах:
```cpp
   // UserTest.cpp
   #include "gtest/gtest.h"
   #include "MockDatabase.h"
   #include "User.h"
   
   TEST(UserTest, InitTest) {
       MockDatabase mockDb;
       User user(mockDb);
       
       EXPECT_CALL(mockDb, connect())
           .Times(1)
           .WillOnce(testing::Return(true));
       
       EXPECT_TRUE(user.init());
   }

   TEST(UserTest, CloseTest) {
       MockDatabase mockDb;
       User user(mockDb);
       
       EXPECT_CALL(mockDb, disconnect())
           .Times(1)
           .WillOnce(testing::Return(true));
       
       EXPECT_TRUE(user.close());
   }

   TEST(UserTest, GetUserCountTest) {
       MockDatabase mockDb;
       User user(mockDb);
       
       EXPECT_CALL(mockDb, getUserCount())
           .Times(1)
           .WillOnce(testing::Return(42));
       
       EXPECT_EQ(user.getUserCount(), 42);
   }
```   
### Преимущества использования интерфейсов:

- Гибкость и тестируемость: Интерфейсы облегчают замену зависимостей мок-объектами или другими реализациями.
- Снижение связности: Разделение интерфейса и реализации уменьшает связанность кода, делая его более модульным и удобным для тестирования.

## Тестирование исключений ##
### Структура проекта:
- `ExceptionClass.h`: Заголовочный файл с объявлением класса.
- `ExceptionClass.cpp`: Исходный файл с реализацией класса.
- `ExceptionTest.cpp`: Файл с тестами для проверки проброски исключений.
- `main.cpp`: Файл с функцией `main()` для запуска тестов.

### Пример кода:

```cpp
// ExceptionClass.h:
#ifndef EXCEPTIONCLASS_H
#define EXCEPTIONCLASS_H

#include <stdexcept>
#include <string>

class ExceptionClass {
public:
    void throwException(const std::string& message) {
        throw std::runtime_error(message);
    }

    void throwSpecificException(int code) {
        if (code == 1) {
            throw std::invalid_argument("Invalid argument");
        } else if (code == 2) {
            throw std::out_of_range("Out of range");
        } else {
            throw std::exception();
        }
    }
};

#endif // EXCEPTIONCLASS_H
```
```cpp
// ExceptionClass.cpp:
#include "ExceptionClass.h"

// Реализация методов, если потребуется дополнительная логика

#### ExceptionTest.cpp:
#include "gtest/gtest.h"
#include "ExceptionClass.h"

TEST(ExceptionClassTest, ThrowExceptionTest) {
    ExceptionClass obj;
    
    EXPECT_THROW({
        obj.throwException("Test exception");
    }, std::runtime_error);
}

TEST(ExceptionClassTest, ThrowSpecificExceptionInvalidArgumentTest) {
    ExceptionClass obj;
    
    EXPECT_THROW({
        obj.throwSpecificException(1);
    }, std::invalid_argument);
}

TEST(ExceptionClassTest, ThrowSpecificExceptionOutOfRangeTest) {
    ExceptionClass obj;
    
    EXPECT_THROW({
        obj.throwSpecificException(2);
    }, std::out_of_range);
}

TEST(ExceptionClassTest, ThrowSpecificExceptionGenericTest) {
    ExceptionClass obj;
    
    EXPECT_THROW({
        obj.throwSpecificException(3);
    }, std::exception);
}
```
```cpp
// main.cpp:
#include "gtest/gtest.h"

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
### Сборка проекта:
Настройте CMakeLists.txt для сборки вашего проекта:
```
cmake_minimum_required(VERSION 3.10)
project(ExceptionTestProject)

set(CMAKE_CXX_STANDARD 14)

add_subdirectory(googletest)

include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

add_executable(ExceptionTestProject main.cpp ExceptionClass.cpp ExceptionTest.cpp)

target_link_libraries(ExceptionTestProject gtest gtest_main)
target_link_libraries(ExceptionTestProject gmock gmock_main)
```

### Запуск тестов:
1. Создайте директорию для сборки и перейдите в нее:
```sh  
   mkdir build
   cd build
```   
2. Запуск cmake и make для сборки проекта:
```sh  
   cmake ..
   make
```
3. Запуск тестов:
  
   ./ExceptionTestProject

## Исключения MOC
### Структура проекта:
- `ExceptionClass.h`: Заголовочный файл с объявлением интерфейса и класса.
- `ExceptionClass.cpp`: Исходный файл с реализацией класса.
- `MockExceptionClass.h`: Файл с мок-классом.
- `ExceptionTest.cpp`: Файл с тестами.
- `main.cpp`: Файл с функцией `main()` для запуска тестов.

### Пример кода:
```cpp
// ExceptionClass.h:
#ifndef EXCEPTIONCLASS_H
#define EXCEPTIONCLASS_H

#include <stdexcept>
#include <string>

class IExceptionClass {
public:
    virtual ~IExceptionClass() = default;
    virtual void throwException(const std::string& message) = 0;
    virtual void throwSpecificException(int code) = 0;
};

class ExceptionClass : public IExceptionClass {
public:
    void throwException(const std::string& message) override {
        throw std::runtime_error(message);
    }

    void throwSpecificException(int code) override {
        if (code == 1) {
            throw std::invalid_argument("Invalid argument");
        } else if (code == 2) {
            throw std::out_of_range("Out of range");
        } else {
            throw std::exception();
        }
    }
};

#endif // EXCEPTIONCLASS_H
```
```cpp
// MockExceptionClass.h:
#ifndef MOCKEXCEPTIONCLASS_H
#define MOCKEXCEPTIONCLASS_H

#include "gmock/gmock.h"
#include "ExceptionClass.h"

class MockExceptionClass : public IExceptionClass {
public:
    MOCK_METHOD(void, throwException, (const std::string& message), (override));
    MOCK_METHOD(void, throwSpecificException, (int code), (override));
};

#endif // MOCKEXCEPTIONCLASS_H

#### ExceptionTest.cpp:
#include "gtest/gtest.h"
#include "MockExceptionClass.h"

TEST(ExceptionClassTest, ThrowExceptionTest) {
    MockExceptionClass mockObj;
    
    EXPECT_CALL(mockObj, throwException("Test exception"))
        .Times(1)
        .WillOnce(testing::Throw(std::runtime_error("Test exception")));
    
    EXPECT_THROW(mockObj.throwException("Test exception"), std::runtime_error);
}

TEST(ExceptionClassTest, ThrowSpecificExceptionInvalidArgumentTest) {
    MockExceptionClass mockObj;
    
    EXPECT_CALL(mockObj, throwSpecificException(1))
        .Times(1)
        .WillOnce(testing::Throw(std::invalid_argument("Invalid argument")));
    
    EXPECT_THROW(mockObj.throwSpecificException(1), std::invalid_argument);
}

TEST(ExceptionClassTest, ThrowSpecificExceptionOutOfRangeTest) {
    MockExceptionClass mockObj;
    
    EXPECT_CALL(mockObj, throwSpecificException(2))
        .Times(1)
        .WillOnce(testing::Throw(std::out_of_range("Out of range")));
    
    EXPECT_THROW(mockObj.throwSpecificException(2), std::out_of_range);
}

TEST(ExceptionClassTest, ThrowSpecificExceptionGenericTest) {
    MockExceptionClass mockObj;
    
    EXPECT_CALL(mockObj, throwSpecificException(3))
        .Times(1)
        .WillOnce(testing::Throw(std::exception()));
    
    EXPECT_THROW(mockObj.throwSpecificException(3), std::exception);
}
```
```cpp
### main.cpp:
#include "gtest/gtest.h"

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
### Сборка проекта:
Настройте CMakeLists.txt для сборки проекта:
```
cmake_minimum_required(VERSION 3.10)
project(ExceptionTestProject)

set(CMAKE_CXX_STANDARD 14)

add_subdirectory(googletest)

include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

add_executable(ExceptionTestProject main.cpp ExceptionClass.cpp ExceptionTest.cpp)

target_link_libraries(ExceptionTestProject gtest gtest_main)
target_link_libraries(ExceptionTestProject gmock gmock_main)
```
### Запуск тестов:
1. Создайте директорию для сборки и перейдите в нее:
```sh  
   mkdir build
   cd build
```
2. Запустите cmake и make для сборки проекта:
```sh
   cmake ..
   make
```
3. Запустите тесты:
 
 ### Основные виды исключений ###
 
### Основные типы исключений

1. `std::exception`:
   Это базовый класс для всех стандартных исключений в C++. Тестирование этого типа исключений проверяет, что общее исключение обрабатывается правильно.
```cpp
   EXPECT_THROW({
       throw std::exception();
   }, std::exception);
```
2. `std::runtime_error`:
   Используется для обработки ошибок, которые могут возникнуть во время выполнения программы.
```cpp
   EXPECT_THROW({
       throw std::runtime_error("Runtime error");
   }, std::runtime_error);
```
3. `std::logic_error`:
   Используется для обработки ошибок логики, которые могут быть обнаружены в процессе выполнения программы.
```cpp
   EXPECT_THROW({
       throw std::logic_error("Logic error");
   }, std::logic_error);
```   
4. `std::invalid_argument`:
   Выбрасывается при передаче функции или методу неправильного аргумента.
```cpp
   EXPECT_THROW({
       throw std::invalid_argument("Invalid argument");
   }, std::invalid_argument);
```   
5. `std::out_of_range`:
   Выбрасывается при доступе к элементу за пределами допустимого диапазона.
```cpp  
   EXPECT_THROW({
       throw std::out_of_range("Out of range");
   }, std::out_of_range);
```   
6. `std::bad_alloc`:
   Выбрасывается, когда не удается выделить память (например, оператор new не может выделить память).
```cpp
   EXPECT_THROW({
       throw std::bad_alloc();
   }, std::bad_alloc);
```   
### Примеры тестирования исключений

#### Пример теста для функции, выбрасывающей std::invalid_argument:
```cpp
void functionThatThrowsInvalidArgument() {
    throw std::invalid_argument("Invalid argument");
}

TEST(ExampleTest, InvalidArgumentTest) {
    EXPECT_THROW(functionThatThrowsInvalidArgument(), std::invalid_argument);
}
```
#### Пример теста для функции, выбрасывающей std::out_of_range:
```cpp
void functionThatThrowsOutOfRange() {
    throw std::out_of_range("Out of range");
}

TEST(ExampleTest, OutOfRangeTest) {
    EXPECT_THROW(functionThatThrowsOutOfRange(), std::out_of_range);
}
```
### Советы по написанию тестов на исключения

- Ясность и краткость: Убедитесь, что ваши тесты на исключения ясны и охватывают конкретные сценарии.
- Ожидание типа исключения: Используйте макросы EXPECT_THROW или ASSERT_THROW для проверки выброса определенного типа исключения.
- Проверка сообщений об ошибках: Если нужно, проверяйте сообщения об ошибках, чтобы убедиться в корректности выброса исключения.


### Небольшой проект
### Структура проекта
- Book.h: Заголовочный файл с объявлением класса Book.
- Book.cpp: Реализация класса Book.
- User.h: Заголовочный файл с объявлением класса User.
- User.cpp: Реализация класса User.
- Library.h: Заголовочный файл с объявлением класса Library.
- Library.cpp: Реализация класса Library.
- LibraryTest.cpp: Файл с тестами для класса Library.
- main.cpp: Файл с функцией main() для запуска тестов.

### Классы и их реализация

```cpp
// Book.h
#ifndef BOOK_H
#define BOOK_H

#include <string>

class Book {
public:
    Book(int id, const std::string& title, const std::string& author);

    int getId() const;
    std::string getTitle() const;
    std::string getAuthor() const;

private:
    int id;
    std::string title;
    std::string author;
};

#endif // BOOK_H
```
```cpp
// Book.cpp
#include "Book.h"

Book::Book(int id, const std::string& title, const std::string& author)
    : id(id), title(title), author(author) {}

int Book::getId() const {
    return id;
}

std::string Book::getTitle() const {
    return title;
}

std::string Book::getAuthor() const {
    return author;
}
```
```cpp
// User.h
#ifndef USER_H
#define USER_H

#include <string>

class User {
public:
    User(int id, const std::string& name);

    int getId() const;
    std::string getName() const;

private:
    int id;
    std::string name;
};

#endif // USER_H
```
```cpp
// User.cpp
#include "User.h"

User::User(int id, const std::string& name) : id(id), name(name) {}

int User::getId() const {
    return id;
}

std::string User::getName() const {
    return name;
}
```
```cpp
// Library.h
#ifndef LIBRARY_H
#define LIBRARY_H

#include "Book.h"
#include "User.h"
#include <map>
#include <vector>

class Library {
public:
    void addBook(const Book& book);
    void addUser(const User& user);
    void borrowBook(int userId, int bookId);
    void returnBook(int userId, int bookId);
    std::vector<Book> getBorrowedBooks(int userId) const;

private:
    std::map<int, Book> books;
    std::map<int, User> users;
    std::map<int, std::vector<int>> borrowedBooks; // userId -> list of bookIds
};

#endif // LIBRARY_H
```
```cpp
// Library.cpp
#include "Library.h"
#include <stdexcept>

void Library::addBook(const Book& book) {
    books[book.getId()] = book;
}

void Library::addUser(const User& user) {
    users[user.getId()] = user;
}

void Library::borrowBook(int userId, int bookId) {
    if (users.find(userId) == users.end()) {
        throw std::invalid_argument("User not found");
    }
    if (books.find(bookId) == books.end()) {
        throw std::invalid_argument("Book not found");
    }
    if (std::find(borrowedBooks[userId].begin(), borrowedBooks[userId].end(), bookId) != borrowedBooks[userId].end()) {
        throw std::runtime_error("Book already borrowed by this user");
    }
    borrowedBooks[userId].push_back(bookId);
}

void Library::returnBook(int userId, int bookId) {
    if (borrowedBooks.find(userId) == borrowedBooks.end()) {
        throw std::invalid_argument("User has no borrowed books");
    }
    auto& userBooks = borrowedBooks[userId];
    auto it = std::find(userBooks.begin(), userBooks.end(), bookId);
    if (it == userBooks.end()) {
        throw std::invalid_argument("Book not found in user's borrowed books");
    }
    userBooks.erase(it);
}

std::vector<Book> Library::getBorrowedBooks(int userId) const {
    std::vector<Book> result;
    if (borrowedBooks.find(userId) != borrowedBooks.end()) {
        for (int bookId : borrowedBooks.at(userId)) {
            result.push_back(books.at(bookId));
        }
    }
    return result;
}
```
### Тесты
```cpp
// LibraryTest.cpp

#include "gtest/gtest.h"
#include "Library.h"

TEST(LibraryTest, AddBookTest) {
    Library library;
    Book book(1, "The Catcher in the Rye", "J.D. Salinger");
    library.addBook(book);
    // Проверка, что книга была добавлена
}

TEST(LibraryTest, AddUserTest) {
    Library library;
    User user(1, "John Doe");
    library.addUser(user);

Sergey Shchegolikhin, [12/25/2024 8:55 PM]
// Проверка, что пользователь был добавлен
}

TEST(LibraryTest, BorrowBookTest) {
    Library library;
    User user(1, "John Doe");
    Book book(1, "The Catcher in the Rye", "J.D. Salinger");
    library.addUser(user);
    library.addBook(book);
    library.borrowBook(1, 1);
    // Проверка, что книга была взята в аренду
}

TEST(LibraryTest, ReturnBookTest) {
    Library library;
    User user(1, "John Doe");
    Book book(1, "The Catcher in the Rye", "J.D. Salinger");
    library.addUser(user);
    library.addBook(book);
    library.borrowBook(1, 1);
    library.returnBook(1, 1);
    // Проверка, что книга была возвращена
}

TEST(LibraryTest, BorrowBookExceptionTest) {
    Library library;
    User user(1, "John Doe");
    Book book(1, "The Catcher in the Rye", "J.D. Salinger");
    library.addUser(user);
    // library.addBook(book); // Книга не добавлена

    EXPECT_THROW(library.borrowBook(1, 1), std::invalid_argument);
}

TEST(LibraryTest, ReturnBookExceptionTest) {
    Library library;
    User user(1, "John Doe");
    Book book(1, "The Catcher in the Rye", "J.D. Salinger");
    library.addUser(user);
    library.addBook(book);
    library.borrowBook(1, 1);
    
    EXPECT_THROW(library.returnBook(1, 2), std::invalid_argument);
}
```
```cpp
// main.cpp

#include "gtest/gtest.h"

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
### Сборка проекта

Создать CMakeLists.txt для сборки проекта:
```cmake
cmake_minimum_required(VERSION 3.10)
project(LibraryManagementSystem)

set(CMAKE_CXX_STANDARD 14)

add_subdirectory(googletest)

include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

add_executable(LibraryManagementSystem main.cpp Book.cpp User.cpp Library.cpp LibraryTest.cpp)

target_link_libraries(LibraryManagementSystem gtest gtest_main)
target_link_libraries(LibraryManagementSystem gmock gmock_main)
```