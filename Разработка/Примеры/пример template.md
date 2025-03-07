```cpp
// Функция регистрации фабрики
template <typename SchemeClass, typename InputClass, typename MapType>
void registerFactory(MapType& map, int scheme) {
    map[scheme] = [](std::shared_ptr<const Input> input) {
        return std::make_shared<SchemeClass>(std::dynamic_pointer_cast<const InputClass>(input));
    };
}
```