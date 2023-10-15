#Лабораторная работа №2
___
_В рамках первой лабораторной работы необходимо было создать исполняемый файл в соответствии с указанным вариантом._


__Вариант 81.__  ___Создать файл sh и bat, который выполняет следующее:___ 
_На вход пакетному файлу приходит путь к папке и строка (как параметры пакетного файла). В заданной папке найти самый большой файл. Если в найденном файле есть строка, поданная как параметр пакетного файла, то выводим в консоль «String has been found», иначе выводим «String hasn’t been found»._

####[Bat](#batnik) 
* [Пример запуска](#batnikzapusk)
####[Bash](#bash) 
* [Пример запуска](#bashzapusk)

<a id ="batnik"></a>
###Bat-файл
Текст программы:
```
@echo off
set directory=%1
set string=%2
for /f "delims=" %%i in ('dir /a-d/b/o-s %directory%') do (
	find /c  %string% "%directory%\%%i" && ( echo String has been found ) || ( echo String hasn't been found )
	pause 
	exit
)
pause
```
`@echo off` отключает режим отображения на экране информации о работе команд.
`set directory=%1` переменная, содержащая первый параметр (путь к папке с текстовыми файлами).
`set string=%2` переменная со вторым параметром (искомая строка).
`for /f "delims=" %%i in ('dir /a-d/b/o-s %directory%') do (...)` запуск цикла __for__  для файлов в указанной папке. __"delims="__ позволяет обрабатывать строку, содержащую пробелы или символы табуляции, без их разделения на части. С помощью __dir__ получаем список файлов, __/a-d__ исключает из обработки директории, __b__ выводит только имена файлов, а __/o-s__ сортирует файлы по размеру. В результате каждое имя файла будет присвоено переменной __%%i__.
`find /c  %string% "%directory%\%%i" && ( echo String has been found ) || ( echo String hasn't been found )` команда __find__ ищет __%string%__ (указанную строку) в каждом файле. __/c__ считает строки, содержащие указанную параметром _искомую строку_, и отображает общее количество. __%directory%\ %%i__ это полный путь к найденому файлу для корректной работы программы, если заданная папка будет находиться не в одном каталоге с командным файлом. Если строка найдена, то __echo String has been found__, то есть вывод уведомления о том, что строка найдена. В другом случае ( || - или) __echo String hasn't been found__.
`pause` останавливает выполнение скрипта и для продолжения ожидает нажатия клавиши.
`exit` завершает выполнение скрипта.


<a id ="batnikzapusk"></a>
###Пример запуска 
![скриншот с запуском программы](скриншот.jpg)
Переходим в каталог, в котором расположен командный файл. Для этого в консоли вводим:
`cd C:\Work\ПиОИвИС`
Запускаем командный файл с необходимыми параметрами:
`laba1.bat C:\Work\ПиОИвИС\test "rabota ne walk"`
В результате выводится путь к самому большому файлу (1.txt) и ___Strind has been found___, так как строка есть в найденном файле.:)
В правой части скриншота открыты все текстовые файлы расположенные в папке __test__ (путь к которой был передан через первый параметр). Очевидно , что первый файл наибольший и содержит искомую строку.

Что будет, если искомой строки не будет в самом большом файле:
![опять скриншот с запуском программы](скриншот2.jpg)
В таком случае выводится ___String hasn`t been found___. :(
***
<a id ="bash"></a>
###Bash-файл
Текст программы:
```
directory=$1 
string_to_search=$2
largest_file=$(find $directory -type f -exec du -b {} + | sort -nr | head -n 1 | cut -f 2)

if grep -q $string_to_search $largest_file; then
 echo 'String has been found'
else 
 echo "String hasn't been foud"
fi
```
`directory=$1` переменная, содержащая путь к папке (первый параметр).
`string_to_search=$2` переменная с искомой строкой (второй параметр).
`largest_file=$(find $directory -type f -exec du -b {} + | sort -nr | head -n 1 | cut -f 2)` __find__ выполняет поиск всех обычных файлов (__-type f__) в указанном каталоге __$directory__. Результат передается команде __du -b__ для получения размера каждого файла в байтах. Затем вывод передается команде __sort -nr__ для сортировки файлов по размеру в порядке убывания. Далее команда __head -n 1__ выбирает первую строку (то есть самый большой файл), и команда __cut -f 2__ извлекает второе поле из строки (то есть имя файла). Результат присваивается переменной __largest_file__.
`if grep -q $string_to_search $largest_file; then` __grep -q__ ищет строку __string_to_search__ в файле __largest_file__. __-q__ используется, чтобы вывод не отображался на экране. 
`echo 'String has been found'` если предыдущее условие истинно, то __echo__ выводит текст ___String has been found___.
`else ` иначе 
`echo "String hasn't been foud"`
`fi` завершает функцию __if__.

<a id ="bashzapusk"></a>
###Пример запуска 2.0
![скриншот с запуском программы убунту](убунту.jpeg)
С помощью `$pwd` выводим путь к текущей папке.
`$ls` выводит содержимое каталога. Командный файл находится рядом с папкой, которую планируем передать как параметр.
`$bash laba.sh ./test walk` запускаем файл с двумя параметрами.
В результате можно увидеть __String has been found__, значит искомая строка __walk__ есть в самом большом файле папки __test__.