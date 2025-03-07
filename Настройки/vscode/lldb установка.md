Для установки LLDB в Linux, выполните следующие шаги:

1. **Обновите пакетный репозиторий**:
   ```bash
   sudo apt-get update
   ```

2. **Установите LLDB**:
   ```bash
   sudo apt-get install lldb
   ```

3. **Проверьте установку**:
   ```bash
   lldb --version
   ```
   Эта команда должна показать версию установленного LLDB.

После установки вы можете использовать LLDB для отладки программ на C++ в Visual Studio Code, настроив `MIMode` в `launch.json` на `"lldb"`.