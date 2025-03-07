**GDB pretty printer** для `nlohmann::json`

### Шаги для настройки pretty printer для nlohmann::json в VS Code

1. **Создайте файл `nlohmannjson.py`** с следующим содержимым:
    ```python
    import gdb.printing

    class NlohmannJsonPrinter(gdb.printing.PrettyPrinter):
        def __init__(self, val):
            self.val = val

        def to_string(self):
            eval_string = "(*(" + str(self.val.type) + "*)(" + str(self.val.address) + ")).dump(2, ' ', false, nlohmann::detail::error_handler_t::strict).c_str()"
            result = gdb.parse_and_eval(eval_string)
            return result.string()

        def display_hint(self):
            return 'JSON'

    def build_pretty_printer():
        pp = gdb.printing.RegexpCollectionPrettyPrinter('nlohmann::basic_json')
        pp.add_printer('JSON', '^nlohmann::basic_json<.*>$', NlohmannJsonPrinter)
        return pp

    def register_json_printer():
        gdb.printing.register_pretty_printer(gdb.current_objfile(), build_pretty_printer())

    register_json_printer()
    ```

2. **Сохраните файл `nlohmannjson.py`** в удобное место, например, в домашней директории.

3. **Добавьте путь к файлу `nlohmannjson.py`** в ваш `.gdbinit`:
    ```sh
    sys.path.append('/home/your/path/to/nlohmannjson.py')
    from nlohmannjson import register_json_printer
    register_json_printer()
    ```

4. **Перезапустите GDB** или перезапустите отладку в VS Code.
