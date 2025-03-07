```cmake
# LeakSanitizer
target_compile_definitions(${PROJECT_NAME} PRIVATE $<$<CONFIG:DEBUG>:DEBUG>)
```

```cpp
#include <gtest/gtest.h>

#ifdef DEBUG

extern "C" int __lsan_is_turned_off()
{
    return 1;
}
#endif

int main(int argc, char** argv)
{
    setlocale(LC_ALL, "Russian");
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```