### Домашнее задание №9 (BASH)
#### Домашнее задание выполнено в следующих условиях:

- Скрипт написан в PyCharm;
- Дистрибутив Ubuntu;
- Для парсинга используется лог access.log;
____

#### 1. Основные файлы скрипта:

- _parsing-script.sh_ - основной файл скрипта;
- _access.log_ - предоставленный файл лога;
- _result_file_ - файл с результатами парсинга файла access.log;
- _last_launch_ - файл,хранящий время последнего запуска скрипта;
- _test_results_ - временный файл, хранящий лог с момента последнего запуска скрипта по настоящее время;
- _launch_ - файл, существующий вовремя процесса работы скрипта в целях не допущения повторного запуска;

#### 2. Принцип работы скрипта:

- __Шаг 1. Входной информацией для скрипта выступает access.log:__
```commandline
kita@DevOps:~/PycharmProjects/Otus_Unit_9_BASH$ bash parsing-script access.log 
```
- __Шаг 2. Проверка отсутствия запуска несколких копий скрипта.__ Проверка производится следующим ообразом:
  + При запуске скрипта проверяется наличие файла _launch_ в директории со скриптом. При отсутствии данного файла: файл создается, скрипт выполняется.
  + При наличии данного файла: скрипт завершается, выводится сообщение о наличии запущенного процесса.
  + При завершении работы скрипта файл _launch_ удаляется.
```commandline
if [ ! -e "FILE" ] ; then
    do
    script
else
    echo ""===Script already launched!!!===""
    exit
fi
```
- __Шаг 3. Парсинг файла лога при отсутствии файла _last_launch_.__
  + В начале выполнения скрипта производится считывание файла _last_launch_, содержащий информацию о последнем запуске скрипта.
Файл находится в одной директории со скриптом. __При отсутствии данного файла__, файл создается, в него вписывается время в формате linux time (2018 год).
  + Cкрипт парсит ВЕСЬ лог  в файл result_file (с момента запуска до настоящего момента), вписывает время запуска скрипта в файл last_launch.
- __Шаг 4. Парсинг файла лога при наличии файла last_launch:__ 
  + Скрипт считывает время последнего запуска скрипта;  
  + Циклом _for_ перебирается каждая строчка лог-файла, парсится время и переводится в unix-time;
  + Отпарсенное время сравнивается с прошлым временем запуска и current временем. Все строчки лога между этими временами помещаются во временный файл test_results; 
  + Скрипт парсит временный файл в файл result_file;
  + Временный файл удаляется;
  + Вписывается время запуска скрипта в файл last_launch.

#### 3. В файле скрипта присутствует закомментированная команда отправки файла лога на электронную почту.
```commandline
# echo 'Script' | mail -s 'Subject attachment message' -a /home/kita/PycharmProjects/Otus_Unit_9_BASH/result_file totheotus@otus.ru
```

#### 4. Информация, которую парсит скрипт:

- IP-адресы, количество самых частых адресов, выводятся 5 самых частых IP-адреса;
- HTTP адресы, количество самых частых адресов, выводятся 5 самых частых HTTP-адреса;
- Все ответы веб-сервера;
- Все ошибки веб-сервера;
- Скрипт выводит информацию времени начала и окончания парсинга лог-файла;
- Скрипт выводит информацию, если за последнее время запуска ни одного нового сообщения в логе нет;
- Вывод result_file (после выполнения скрипта):
```commandline
This script parsed access.log from  Пн 24 дек 2018 21:22:34 MSK  to  Вс 18 сен 2022 15:28:01 MSK
   ---===   Unique IP addresses and quantity in log file   ===--- 
93.158.167.130 - 45
109.236.252.130 - 39
212.57.117.19 - 37
188.43.241.106 - 32
87.250.233.68 - 31
   ---===   Unique URL addresses and quantity in log file   ===---   
https://dbadmins.ru/ - 141
http://dbadmins.ru/ - 14
https://www.google.ru/ - 2
https://yandex.ru/ - 1
   ---===   Unique HTTP codes and quantity in log file   ===---   
200 - 497
301 - 95
404 - 51
400 - 7
500 - 3
   ---===   Unique HTTP codes error and quantity in log file   ===---   
404 - 51
400 - 7
500 - 3
405 - 1
403 - 1
================================END==========================================

```

#### 4. Проведенные тесты для скрипта с выводами результатов.

- Для теста было выбрано 3 ситуации при работе скрипта:
  + 1 ситуация. Скрипт ни разу не запускался:
```commandline
kita@DevOps:~/PycharmProjects/Otus_Unit_9_BASH$ bash parsing-script access.log 
kita@DevOps:~/PycharmProjects/Otus_Unit_9_BASH$ cat result_file 
This script parsed access.log from  Пн 24 дек 2018 21:22:34 MSK  to  Вс 18 сен 2022 15:28:01 MSK
   ---===   Unique IP addresses and quantity in log file   ===--- 
93.158.167.130 - 45
109.236.252.130 - 39
212.57.117.19 - 37
188.43.241.106 - 32
87.250.233.68 - 31
   ---===   Unique URL addresses and quantity in log file   ===---   
https://dbadmins.ru/ - 141
http://dbadmins.ru/ - 14
https://www.google.ru/ - 2
https://yandex.ru/ - 1
   ---===   Unique HTTP codes and quantity in log file   ===---   
200 - 497
301 - 95
404 - 51
400 - 7
500 - 3
   ---===   Unique HTTP codes error and quantity in log file   ===---   
404 - 51
400 - 7
500 - 3
405 - 1
403 - 1
================================END==========================================

```

  + 2 ситуация. При последующих запусках скрипта (настоящее время):
```commandline
This script parsed access.log from  Вс 18 сен 2022 15:28:01 MSK  to  Вс 18 сен 2022 15:28:16 MSK
======From last hour access.log is empty======
================================END==========================================

```

  + 3 ситуация. Изменим время последнего запуска скрипта в файле last_launch на время около окончания лог-файла:
```commandline
kita@DevOps:~/PycharmProjects/Otus_Unit_9_BASH$ echo 1565810280  > last_launch 
kita@DevOps:~/PycharmProjects/Otus_Unit_9_BASH$ bash parsing-script access.log 
kita@DevOps:~/PycharmProjects/Otus_Unit_9_BASH$ cat result_file 
This script parsed access.log from  Ср 14 авг 2019 22:18:00 MSK  to  Вс 18 сен 2022 17:18:21 MSK
   ---===   Unique IP addresses and quantity in log file   ===--- 
87.250.233.68 - 3
54.208.102.37 - 3
209.124.74.244 - 3
167.86.96.137 - 3
167.71.136.40 - 3
   ---===   Unique URL addresses and quantity in log file   ===---   
https://dbadmins.ru/ - 4
http://dbadmins.ru/ - 1
   ---===   Unique HTTP codes and quantity in log file   ===---   
200 - 30
404 - 5
301 - 4
405 - 1
================================END==========================================
```
