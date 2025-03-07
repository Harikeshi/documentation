Mock-тестирование — это техника тестирования, при которой реальные объекты заменяются специальными имитаторами (mock-объектами), которые ведут себя так же, как и настоящие объекты, но позволяют контролировать их поведение и результаты. Это особенно полезно, когда нужно изолировать тестируемый компонент и проверять его поведение независимо от других компонентов.

### Пример использования Google Mock

```cpp
   #include <gtest/gtest.h>
   #include <gmock/gmock.h>

   // Интерфейс
   class Foo {
   public:
       virtual ~Foo() {}
       virtual int doSomething(int x) = 0;
   };

   // Mock-класс
   class MockFoo : public Foo {
   public:
       MOCK_METHOD(int, doSomething, (int x), (override));
   };

   // Класс, который использует Foo
   class Bar {
   public:
       Bar(Foo* foo) : foo_(foo) {}
       int performTask(int x) {
           return foo_->doSomething(x) + 1;
       }
   private:
       Foo* foo_;
   };

   // Тесты с использованием Google Mock
   TEST(BarTest, PerformTask) {
       MockFoo mockFoo;
       Bar bar(&mockFoo);

       EXPECT_CALL(mockFoo, doSomething(5))
           .WillOnce(::testing::Return(10));

       EXPECT_EQ(bar.performTask(5), 11);
   }

   int main(int argc, char** argv) {
       ::testing::InitGoogleTest(&argc, argv);
       return RUN_ALL_TESTS();
   }
```