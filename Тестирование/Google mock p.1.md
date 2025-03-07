Mock-тесты позволяют изолировать тестируемый код от его зависимостей, заменяя эти зависимости mock-объектами. Это особенно полезно для тестирования взаимодействий между объектами или тестирования кода, который зависит от внешних сервисов. Давайте рассмотрим несколько примеров mock-тестов с использованием Google Test и Google Mock.

### Пример 1: Тестирование вызовов методов

Допустим, у нас есть интерфейс Database и класс UserService, который использует этот интерфейс для работы с данными пользователей.

database.h
```cpp
// database.h
#ifndef DATABASE_H
#define DATABASE_H

#include <string>

class Database {
public:
    virtual ~Database() {}
    virtual bool saveUser(const std::string& username) = 0;
};

#endif // DATABASE_H
```
user_service.h
```cpp
// user_service.h
#ifndef USER_SERVICE_H
#define USER_SERVICE_H

#include "database.h"

class UserService {
public:
    UserService(Database* db) : db_(db) {}
    bool createUser(const std::string& username) {
        return db_->saveUser(username);
    }
private:
    Database* db_;
};

#endif // USER_SERVICE_H
```
Мы создадим mock-класс для интерфейса Database и напишем тест для UserService.
```cpp
// mock_database.h
#ifndef MOCK_DATABASE_H
#define MOCK_DATABASE_H

#include "database.h"
#include <gmock/gmock.h>

class MockDatabase : public Database {
public:
    MOCK_METHOD(bool, saveUser, (const std::string& username), (override));
};

#endif // MOCK_DATABASE_H
```
test_user_service.cpp
```cpp
// test_user_service.cpp
#include "user_service.h"
#include "mock_database.h"
#include <gtest/gtest.h>

TEST(UserServiceTest, CreateUser) {
    MockDatabase mockDb;
    UserService userService(&mockDb);

    EXPECT_CALL(mockDb, saveUser("JohnDoe"))
        .WillOnce(::testing::Return(true));

    EXPECT_TRUE(userService.createUser("JohnDoe"));
}
```
main.cpp
```cpp
int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
### Пример 2: Тестирование исключений

Допустим, у нас есть интерфейс FileSystem и класс FileReader, который использует этот интерфейс для чтения файлов. Мы хотим протестировать, как FileReader обрабатывает исключения.
filesystem.h
```cpp
#ifndef FILESYSTEM_H
#define FILESYSTEM_H

#include <string>

class FileSystem {
public:
    virtual ~FileSystem() {}
    virtual std::string readFile(const std::string& path) = 0;
};

#endif // FILESYSTEM_H
```
file_reader.h
```cpp
#ifndef FILE_READER_H
#define FILE_READER_H

#include "filesystem.h"

class FileReader {
public:
    FileReader(FileSystem* fs) : fs_(fs) {}
    std::string loadFile(const std::string& path) {
        try {
            return fs_->readFile(path);
        } catch (const std::exception& e) {
            return "Error: " + std::string(e.what());
        }
    }
private:
    FileSystem* fs_;
};

#endif // FILE_READER_H
```
Создадим mock-класс для интерфейса FileSystem и напишем тест для FileReader.
mock_filesystem.h
```cpp
#ifndef MOCK_FILESYSTEM_H
#define MOCK_FILESYSTEM_H

#include "filesystem.h"
#include <gmock/gmock.h>

class MockFileSystem : public FileSystem {
public:
    MOCK_METHOD(std::string, readFile, (const std::string& path), (override));
};

#endif // MOCK_FILESYSTEM_H
```
test_file_reader.cpp
```cpp
#include "file_reader.h"
#include "mock_filesystem.h"
#include <gtest/gtest.h>

TEST(FileReaderTest, LoadFileHandlesException) {
    MockFileSystem mockFs;
    FileReader fileReader(&mockFs);

    EXPECT_CALL(mockFs, readFile("/path/to/file"))
        .WillOnce(::testing::Throw(std::runtime_error("File not found")));

    EXPECT_EQ(fileReader.loadFile("/path/to/file"), "Error: File not found");
}
```
main.cpp
```cpp
int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
### Пример 3: Тестирование последовательности вызовов

Если важно проверить, что методы вызываются в определенном порядке, можно использовать Google Mock для тестирования последовательности вызовов.
network.h
```cpp
#ifndef NETWORK_H
#define NETWORK_H

#include <string>

class Network {
public:
    virtual ~Network() {}
    virtual void connect() = 0;
    virtual std::string sendData(const std::string& data) = 0;
    virtual void disconnect() = 0;
};

#endif // NETWORK_H
```
client.h
```cpp
#ifndef CLIENT_H
#define CLIENT_H

#include "network.h"

class Client {
public:
    Client(Network* network) : network_(network) {}
    std::string performTask(const std::string& data) {
        network_->connect();
        std::string response = network_->sendData(data);
        network_->disconnect();
        return response;
    }
private:
    Network* network_;
};

#endif // CLIENT_H
```
Создадим mock-класс для интерфейса Network и напишем тест для Client.
mock_network.h
```cpp
#ifndef MOCK_NETWORK_H
#define MOCK_NETWORK_H

#include "network.h"
#include <gmock/gmock.h>

class MockNetwork : public Network {
public:
    MOCK_METHOD(void, connect, (), (override));
    MOCK_METHOD(std::string, sendData, (const std::string& data), (override));
    MOCK_METHOD(void, disconnect, (), (override));
};

#endif // MOCK_NETWORK_H
```
test_client.cpp
```cpp
#include "client.h"
#include "mock_network.h"
#include <gtest/gtest.h>

TEST(ClientTest, PerformTask) {
    MockNetwork mockNetwork;
    Client client(&mockNetwork);
    testing::InSequence s;

    EXPECT_CALL(mockNetwork, connect());
    EXPECT_CALL(mockNetwork, sendData("hello"))
        .WillOnce(::testing::Return("response"));
    EXPECT_CALL(mockNetwork, disconnect());

    EXPECT_EQ(client.performTask("hello"), "response");
}
```
main.cpp
```cpp
int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
### Объяснение:

1. Тестирование вызовов методов: В этом примере мы создаем mock-объект для интерфейса Database и проверяем, что метод saveUser вызывается с правильным параметром и возвращает ожидаемое значение.
2. Тестирование исключений: Мы создаем mock-объект для интерфейса FileSystem и проверяем, что метод loadFile корректно обрабатывает исключения.
3. Тестирование последовательности вызовов: Мы создаем mock-объект для интерфейса Network и проверяем, что методы connect, sendData и disconnect вызываются в правильном порядке.