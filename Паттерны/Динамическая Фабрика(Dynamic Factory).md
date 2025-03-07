1. Определяем базовый класс и группы классов
```cpp
#include <iostream>
#include <memory>
#include <string>
#include <unordered_map>
#include <vector>

// Базовый класс
class BaseClass
{
public:
    virtual void execute() = 0;
    virtual ~BaseClass()   = default;
};

// Группа bycall
class A : public BaseClass
{
    int data;

public:
    A(int d)
        : data(d)
    {
    }
    void execute() override
    {
        std::cout << "Executing class A with data " << data << std::endl;
    }
};

class B : public BaseClass
{
    double data;

public:
    B(double d)
        : data(d)
    {
    }
    void execute() override
    {
        std::cout << "Executing class B with data " << data << std::endl;
    }
};

// Группа inregion
class C : public BaseClass
{
    std::string data;

public:
    C(const std::string& d)
        : data(d)
    {
    }
    void execute() override
    {
        std::cout << "Executing class C with data " << data << std::endl;
    }
};

class D : public BaseClass
{
    bool data;

public:
    D(bool d)
        : data(d)
    {
    }
    void execute() override
    {
        std::cout << "Executing class D with data " << data << std::endl;
    }
};

// Группа onborder
class E : public BaseClass
{
    char data;

public:
    E(char d)
        : data(d)
    {
    }
    void execute() override
    {
        std::cout << "Executing class E with data " << data << std::endl;
    }
};

class F : public BaseClass
{
    float data;

public:
    F(float d)
        : data(d)
    {
    }
    void execute() override
    {
        std::cout << "Executing class F with data " << data << std::endl;
    }
};
```
2. Создаем фабрику для динамического создания объектов
```cpp
class Factory
{
public:
    using CreateFunction = std::unique_ptr<BaseClass> (*)(void*);

    static Factory& getInstance()
    {
        static Factory instance;
        return instance;
    }

    void registerClass(const std::string& className, CreateFunction createFunc)
    {
        creators[className] = createFunc;
    }

    std::unique_ptr<BaseClass> create(const std::string& className, void* data)
    {
        auto it = creators.find(className);
        if (it != creators.end())
        {
            return it->second(data);
        }
        return nullptr;
    }

    std::vector<std::unique_ptr<BaseClass>> createGroup(const std::string& groupName, void* data)
    {
        std::vector<std::unique_ptr<BaseClass>> group;
        for (const auto& creator : groupCreators[groupName])
        {
            group.push_back(creator(data));
        }
        return group;
    }

    void registerGroup(const std::string& groupName, const std::vector<std::string>& classNames)
    {
        for (const auto& className : classNames)
        {
            if (creators.find(className) != creators.end())
            {
                groupCreators[groupName].push_back(creators[className]);
            }
        }
    }

private:
    Factory() = default;
    std::unordered_map<std::string, CreateFunction> creators;
    std::unordered_map<std::string, std::vector<CreateFunction>> groupCreators;
};

// Регистрация классов в фабрике
#define REGISTER_CLASS(CLASS_NAME, DATA_TYPE) \
    Factory::getInstance().registerClass(#CLASS_NAME, [](void* data) -> std::unique_ptr<BaseClass> { return std::make_unique<CLASS_NAME>(*reinterpret_cast<DATA_TYPE*>(data)); })

// Регистрация конкретных классов
REGISTER_CLASS(A, int);
REGISTER_CLASS(B, double);
REGISTER_CLASS(C, std::string);
REGISTER_CLASS(D, bool);
REGISTER_CLASS(E, char);
REGISTER_CLASS(F, float);

// Регистрация групп классов
Factory::getInstance().registerGroup("bycall", {"A", "B"});
Factory::getInstance().registerGroup("inregion", {"C", "D"});
Factory::getInstance().registerGroup("onborder", {"E", "F"});
```
3. Использование фабрики для создания и вызова классов
```cpp
int main()
{
    // Вызов конкретного класса по имени
    std::string className;
    std::cout << "Enter class name: ";
    std::cin >> className;

    // Ввод данных для создания объекта класса
    if (className == "A")
    {
        int data;
        std::cout << "Enter int data for class A: ";
        std::cin >> data;
        auto obj = Factory::getInstance().create(className, &data);
        if (obj)
        {
            obj->execute();
        }
        else
        {
            std::cout << "Class not found!" << std::endl;
        }
    }
    else if (className == "B")
    {
        double data;
        std::cout << "Enter double data for class B: ";
        std::cin >> data;
        auto obj = Factory::getInstance().create(className, &data);
        if (obj)
        {
            obj->execute();
        }
        else
        {
            std::cout << "Class not found!" << std::endl;
        }
    }
    else if (className == "C")
    {
        std::string data;
        std::cout << "Enter string data for class C: ";
        std::cin >> data;
        auto obj = Factory::getInstance().create(className, &data);
        if (obj)
        {
            obj->execute();
        }
        else
        {
            std::cout << "Class not found!" << std::endl;
        }
    }
    else if (className == "D")
    {
        bool data;
        std::cout << "Enter bool data for class D (0 or 1): ";
        std::cin >> data;
        auto obj = Factory::getInstance().create(className, &data);
        if (obj)
        {
            obj->execute();
        }
        else
        {
            std::cout << "Class not found!" << std::endl;
        }
    }
    else if (className == "E")
    {
        char data;
        std::cout << "Enter char data for class E: ";
        std::cin >> data;
        auto obj = Factory::getInstance().create(className, &data);
        if (obj)
        {
            obj->execute();
        }
        else
        {
            std::cout << "Class not found!" << std::endl;
        }
    }
    else if (className == "F")
    {
        float data;
        std::cout << "Enter float data for class F: ";
        std::cin >> data;
        auto obj = Factory::getInstance().create(className, &data);
        if (obj)
        {
            obj->execute();
        }
        else
        {
            std::cout << "Class not found!" << std::endl;
        }
    }

    // Вызов всех классов из конкретной группы
    std::string groupName = "";

    if (groupName == "bycall")
    {
        int data;
        std::cout << "Enter int data for group bycall: ";
        std::cin >> data;
        auto group = Factory::getInstance().createGroup(groupName, &data);
        if (!group.empty())
        {
            for (const auto& obj : group)
            {
                obj->execute();
            }
        }
        else
        {
            std::cout << "Group not found!" << std::endl;
        }
    }
    else if (groupName == "inregion")
    {
        std::string data;
        std::cout << "Enter string data for group inregion: ";
        std::cin >> data;
        auto group = Factory::getInstance().createGroup(groupName, &data);
        if (!group.empty())
        {
            for (const auto& obj : group)
            {
                obj->execute();
            }
        }
        else
        {
            std::cout << "Group not found!" << std::endl;
        }
    }
    else if (groupName == "onborder")
    {
        float data;
        std::cout << "Enter float data for group onborder: ";
        std::cin >> data;
        auto group = Factory::getInstance().createGroup(groupName, &data);
        if (!group.empty())
        {
            for (const auto& obj : group)
            {
                obj->execute();
            }
        }
        else
        {
            std::cout << "Group not found!" << std::endl;
        }
    }

    return 0;
}
```