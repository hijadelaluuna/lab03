## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```ShellSession
$ open https://cmake.org/
```

## Tasks

- [X] 1. Создать публичный репозиторий с названием **lab03** на сервисе **GitHub**
- [X] 2. Ознакомиться со ссылками учебного материала
- [X] 3. Выполнить инструкцию учебного материала
- [X] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial


$ export GITHUB_USERNAME=hijadelaluuna
```
**Подготовка рабочего пространства

$ cd $hijadelaluuna/workspace
$ pushd .
$ source scripts/activate
```
**Копирование lab02 в lab03

$ git clone https://github.com/$hijadelaluuna/lab02.git projects/lab03
$ cd projects/lab03
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```
**Компиляция примера 1

$ g++ -std=c++11 -I./include -c sources/print.cpp # Компиляция source/print.cpp
$ ls print.o # Проверка наличия файла
$ nm print.o | grep print #Вывод содержимого файла
$ ar rvs print.a print.o # Создание архива
$ file print.a  # Печать информации о файле print.a
$ g++ -std=c++11 -I./include -c examples/example1.cpp  # Компиляция source/example1.cpp
$ ls example1.o  # Проверка наличия файла
$ g++ example1.o print.a -o example1  # Линковка файла и библиотеки. Вывод в example1
$ ./example1 && echo # Запуск файла 
```
**Компиляция примера 2
```ShellSession
$ g++ -std=c++11 -I./include -c examples/example2.cpp # Компиляция source/example2.cpp
$ nm example2.o # Вывод содержимого файла
                 U __cxa_atexit
                 U __dso_handle
0000000000000000 V DW.ref.__gxx_personality_v0
                 U _GLOBAL_OFFSET_TABLE_
0000000000000163 t _GLOBAL__sub_I_main
                 U __gxx_personality_v0
0000000000000000 T main
                 U __stack_chk_fail
                 U _Unwind_Resume
000000000000011a t _Z41__static_initialization_and_destruction_0ii
                 U _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E
                 U _ZNSaIcEC1Ev
                 U _ZNSaIcED1Ev
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEEC1EPKcSt13_Ios_Openmode
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEED1Ev
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEC1EPKcRKS3_
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEED1Ev
                 U _ZNSt8ios_base4InitC1Ev
                 U _ZNSt8ios_base4InitD1Ev
0000000000000000 r _ZStL19piecewise_construct
0000000000000000 b _ZStL8__ioinit
$ g++ example2.o print.a -o example2 # Линковка файла и библиотеки. Вывод в example2
$ ./example2 
$ cat log.txt && echo # Вывод в консоль
hello
```

**Удаление ненужных файлов

$ rm -rf example1.o example2.o print.o 
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt


**Запись в CMakeLists.txt функций: информация о минимально допустимой версии и название проекта
```ShellSession
$ cat > CMakeLists.txt <<EOF # Запись в файл
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```
**Запись в CMakeLists.tx функций: установление версии С++ и обязательного стандарта
```ShellSession
$ cat >> CMakeLists.txt <<EOF# Дописывание в файл
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```
**Добавление в CMakeLists.txt функций: установление задачи создания статической библиотеки
```ShellSession
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```


**Добавление в CMakeLists.txt функций: установка использования заголовков из ./include
```ShellSession
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```
 **Компиляция статической библиотеки
```ShellSession
$ cmake -H. -B_build  # Конфигурирование
# Результат помещается в _build
-- The C compiler identification is GNU 8.3.0
-- The CXX compiler identification is GNU 8.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to:
/home/absinthetoxin/CrazyOverdose/workspace/projects/lab03/_build
$ cmake --build _build # Выполнение задач из CMakeLists.txt
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
```
**Добавление в CMakeLists.txt функций: создание исполняемых файлов
```ShellSession
$ cat >> CMakeLists.txt <<EOF # Дописывание в файл

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```
**Добавление в CMakeLists.txt функций: установление линковки
```ShellSession
$ cat >> CMakeLists.txt <<EOF # Дописывание в файл

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```
**Компиляция статической библиотеки и исполняемых файлов
```ShellSession
$ cmake --build _build # Выполнение задач из CMakeLists.txt
- Configuring done
-- Generating done
-- Build files have been written to:
/home/absinthetoxin/CrazyOverdose/workspace/projects/lab03/_build
[ 33%] Built target print
Scanning dependencies of target example2
[ 50%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
[ 66%] Linking CXX executable example2
[ 66%] Built target example2
Scanning dependencies of target example1
[ 83%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
[100%] Linking CXX executable example1
[100%] Built target example1
$ cmake --build _build --target print # Выполнение задач из CMakeLists.txt
[100%] Built target print
$ cmake --build _build --target example1 # Выполнение задач из CMakeLists.txt
[ 50%] Built target print
[100%] Built target example1
$ cmake --build _build --target example2 # Выполнение задач из CMakeLists.txt
[ 50%] Built target print
[100%] Built target example2
```
Проверка исполняемых файлов
```ShellSession
$ ls -la _build/libprint.a #Проверка на наличия файла и вывод информации о нём
$ _build/example1 && echo # Выполнение example1
hello
$ _build/example2 # Выполнение example2
$ cat log.txt && echo # Печать log.txt в консоль
hello
$ rm -rf log.txt # Удаление log.txt
```
Скачивание CMakeLists.txt из репозитория
```ShellSession
$ git clone https://github.com/tp-labs/lab03 tmp # Скачивание репозитория в каталог tmp
Клонирование в «tmp»…
remote: Enumerating objects: 73, done.
remote: Total 73 (delta 0), reused 0 (delta 0), pack-reused 73
Распаковка объектов: 100% (73/73), готово.
$ mv -f tmp/CMakeLists.txt . # Перемещение CMakeLists.txt в текущую директорию

$ rm -rf tmp # Удаление  каталога tmp
```
**Компиляция с использованием нового CMakeLists.txt
```ShellSession
$ cat CMakeLists.txt #Вывод содержимого CMakeLists.txt 
cmake_minimum_required(VERSION 3.4) 

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install # Конфигурирование
-- Configuring done
-- Generating done
-- Build files have been written to:
/home/luna/hijadelaluuna/workspace/projects/lab03/_build
$ cmake --build _build --target install  # Выполнение задач из CMakeLists.txt

$ tree _install # Дерево файлов
install
├── cmake
│   ├── print-config.cmake
│   └── print-config-noconfig.cmake
├── include
│   └── print.hpp
└── lib
    └── libprint.a
3 directories, 4 files
```
**Отправка изменений в удаленный репозиторий


```ShellSession
$ git add CMakeLists.txt # Фиксирование изменений в файле
[master 5fd534b] added CMakeLists.txt
 1 file changed, 36 insertions(+)
 create mode 100644 CMakeLists.txt
$ git commit -m"added CMakeLists.txt" 
$ git push origin master # Отправление изменений
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

**Удачной стажировки!**

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
