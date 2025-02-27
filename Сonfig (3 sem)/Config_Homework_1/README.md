# Домашняя работа №1
Разработка эмулятора командной строки
## Вариант 22

### Задание
Разработать эмулятор для языка оболочки ОС. Необходимо сделать работу эмулятора как можно более похожей на сеанс shell в UNIX-подобной ОС. Эмулятор позволяет пользователю взаимодействовать с виртуальной файловой системой в графическом интерфейсе (GUI), имитируя сеанс shell в UNIX-подобной операционной системе.

Эмулятор запускается из реальной командной строки и принимает образ виртуальной файловой системы в виде файла формата `.tar`. 
Конфигурация выполняется через `.csv` файл, который указывает необходимые пути и содержит:
- Путь к архиву виртуальной файловой системы.
- Путь к лог-файлу.
- Путь к стартовому скрипту.

Лог-файл имеет формат `.xml` и содержит все действия во время последнего сеанса работы с эмулятором. Для каждого действия указаны дата и время.
Приложение поддерживает начальный запуск команд из скрипта.

## Описание
Эмулятор командной оболочки, имитирующий работу shell UNIX-подобной операционной системы. Эмулятор предоставляет GUI, поддерживает основные команды для управления файлами и навигации по виртуальной файловой системе, работающей на основе `.tar` архива. Также имеется возможность автоматического выполнения команд из начального скрипта.

### Возможности
Эмулятор поддерживает следующие команды оболочки:
- **ls** — Просмотр содержимого директории.
  - Опциональный флаг:
    - `-l` — формат длинного списка (права доступа, владелец, размер, дата изменения).
- **cd** — Переход в указанную директорию (/, ..).
- **exit** — Завершение сеанса.
- **echo** — Отображение текста в эмуляторе.
- **mv** — Перемещение файла или каталога, переименование.
- **find** — Поиск файлов или директорий по шаблону (-type, -size, -name).

### Подготовка к работе
Для работы эмулятора требуется Python и библиотека `tkinter`. Убедитесь, что `.tar` архив, содержащий файловую систему, подготовлен заранее.

### Файл config.csv
Содержание файла:
Path to VFS Archive, Path to Log File, Path to Start Script

papka.tar, log.xml, start_script.txt

где:
- `papka.tar`: путь к архиву виртуальной файловой системы (формат .tar).
- `log.xml`: путь к лог-файлу для записи действий (формат .xml).
- `start_script.txt`: путь к стартовому скрипту с командами для выполнения при старте (формат .txt).

### Файл start_script.txt
Содержание файла:

echo Starting script is working.

ls


## Классы и методы
### `ShellEmulator`
Это основной и еднственный класс эмулятора командной строки. Он управляет пользовательским интерфейсом и логикой эмуляции команд.

### Методы управления логикой эмулятора:
- `__init__(self, root)`:Инициализирует окно Tkinter, загружает конфигурацию, VFS (виртуальная файловая система), запускает начальный скрипт и инициализирует интерфейс.
- `load_config(self)`: Загружает конфигурацию из файла CSV, которая содержит пути к архивам, логам и скрипту для старта.
- `load_vfs(self)`: Загружает виртуальную файловую систему из tar-архива, создавая соответствующие записи для файлов и каталогов.
- `log_action(self, action)`: Логирует действия в XML-файл.
- `initUI(self)`: Настроивает графический интерфейс пользователя, включая текстовое поле для вывода и ввода команд.
- `run_start_script(self)`: Выполняет команды из скрипта, заданного в конфигурации.
- `execute_command(self, event=None, command=None, from_start_script=False)`: Обрабатывает и выполняет команды, вводимые пользователем или из начального скрипта.
- `set_file_permissions(self)`: Устанавливает права доступа для скрипта.
- `prompt(self)`: Выводит приглашение командной строки.

### Методы для взаимодействия с эмулятором:
Каждая из поддерживаемых команд (ls, cd, exit, echo, mv, find) реализована в отдельном методе. Эти методы используют виртуальную файловую систему и выводят результаты в текстовое поле интерфейса.

- `ls(self, args)`: Выводит список файлов в текущей директории. Может работать с флагом `-l` (подробный вывод информации о файле).

  ![image](https://github.com/user-attachments/assets/fa443e22-090e-42c8-8257-0f2511518d39)
- `ls_recursive(self, path, files)`: Рекурсивно выводит содержимое всех подкаталогов.
- `cd(self, args)`: Перемещает пользователя между директориями (поддерживает аргументы .., /).

  ![image](https://github.com/user-attachments/assets/3bd6d51f-1a34-40eb-8a98-2e835cc025c6)
- `echo(self, args)`: Выводит текст, переданный в качестве аргумента.

  ![image](https://github.com/user-attachments/assets/9fde4cc9-3a47-4fd9-9b2a-f0ce28d1617d)
- `mv(self, args)`: Перемещает или переименовывает файлы.

  ![image](https://github.com/user-attachments/assets/226d5d68-8661-49a5-9941-4971bd29ee3a)
- `find(self, args)`: Ищет файлы по указанным критериям, таким как имя, тип или размер.

  ![image](https://github.com/user-attachments/assets/12893ee0-692c-4ecf-8b9a-845dfeab44d1)


### Обработка виртуальной файловой системы (VFS):
Код использует tar-архив для хранения структуры файлов. Каждый файл в архиве представляется в vfs как словарь с содержимым, размером и временем последнего изменения.
- `load_vfs(self)`: Загружает архив с файлами в словарь vfs, где каждый файл или папка хранится с метаданными.
- `get_size_recursive(self, path)`: Рассчитывает общий размер всех файлов в каталоге и его подкаталогах.
- `human_readable_size(self, size)`: Преобразует размер файла в человекочитаемый формат (например, KB, MB).
- `recursive_search(self, current_path)`: Рекурсивный поиск файлов в виртуальной файловой системе.

### Работа с интерфейсом (Tkinter):
Для создания интерфейса используется Tkinter:

- `output_text`: Поле для вывода результатов команд.
- `input_text`: Поле для ввода команд пользователем.
- `prompt()`: Метод, который обновляет приглашение командной строки в текстовом поле.

### Main
Функция main является точкой входа в программу и запускает эмулятор оболочки. Она выполняет все начальные настройки и запускает главный цикл графического интерфейса. В частности, в main выполняются следующие действия:

Создание экземпляра Tkinter root:

Создается корневое окно Tkinter, которое используется для отображения интерфейса.
Запуск эмулятора:

Инициализируется объект ShellEmulator с переданным корневым окном root, которое инициализирует весь интерфейс и внутреннюю логику оболочки.
Запуск интерфейса:

Вызывается метод mainloop(), который начинает главный цикл событий Tkinter, обеспечивая реакцию на действия пользователя (например, ввод команд).

![image](https://github.com/user-attachments/assets/700499f6-528a-41cc-bc28-b6e0bbbb73e9)

### Стартовый скрипт
Стартовый скрипт выполняется при старте эмулятора. Он представляет собой набор команд, которые должны быть выполнены автоматически при запуске программы.

Скрипт считывается из конфигурационного файла. В методе run_start_script() эмулятор поочередно выполняет команды из скрипта, выводя результат в консоль и в лог.
Скрипт выполняется до того, как пользователь сможет начать вводить свои команды.

## Тестирование эмулятора
Модуль тестирования содержит комплексный набор тестов для класса ShellEmulator, который предназначен для эмуляции основных команд командной строки в графическом интерфейсе пользователя (GUI) на основе Tkinter. Тесты реализованы с использованием библиотеки unittest на Python, с широкой применением patch и MagicMock для имитации вызовов методов.
Выполяется тестирование команд варианта: ls, cd, echo, mv, find.

![image](https://github.com/user-attachments/assets/70871d84-cf76-45a5-bd8e-1563fb8355c3)
