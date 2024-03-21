# Экосистема больших данных

Основные команды `HDFS` [тут](https://github.com/BosenkoTM/BigDataAnalitic/blob/main/workshop/01_seminar/commands_hdfs.md).

Учебное пособие по Hadoop внутри [Cloudera quickstart VM](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.13.0-0-virtualbox.zip).

## Подготовка рабочего места

#### Импорт виртуальной машины

Прежде чем приступить к работе, понадобится работающий кластер Hadoop..

В следующих упражнениях используем [Cloudera quickstart VM](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.13.0-0-virtualbox.zip).

* Загрузить и разархивировать приложение для VirtualBox.

* Импортируйте файл `.ova` в VirtualBox. **не запускайте**, сначало настроить виртуальное окружение.

  * Требуется установить для виртуальной машины `cloudera-quickstart-vm-5.13.0-0-virtualbox` диапазон виртуальной памяти от 4 до 8 ГБ ОЗУ `Configuration > System`. 

* Запустить виртуальную машину `cloudera-quickstart-vm-5.13.0-0-virtualbox`.

* Чтобы [включить копирование/вставку](https://www.techrepublic.com/article/how-to-enable-copy-and-paste-in-virtualbox/) с ПК, выбрать `Devices > Shared Clipboard > Bidirectional`.

##  1. Взаимодействие с компонентами Hadoop

Взаимодействие с кластером Hadoop будет осуществляться через [Hue](https://docs.cloudera.com/documentation/enterprise/5-13-x/topics/hue.html).

`1.1.` Откройте Firefox: в терминале  введите  `firefox` или через панель управления: `Applications > System tools > Web browser`
* Откройте вкладку `Hue` - основной пользовательский интерфейс для взаимодействия с `HDFS`.
  * Учетные данные: **cloudera/cloudera**.

####  2. Взаимодействие с файловой системой HDFS 

* Скачать [Geolocation data from Cloudera](https://disk.yandex.ru/d/S9VgKivO02B_fA). 
* Вы также можете воспользоваться терминалом `wget https://community.cloudera.com/xgkfq28377/attachments/xgkfq28377/Questions/87306/1/geolocation.zip` и `unzip geolocation.csv`.
* В `Hue`, выберите `Browsers > Files`. 
* Создайте новый каталог в HDFS с именем `data` внутри HDFS из `Hue`.
  * По умолчанию это должно быть создано под `hdfs:///user/cloudera/`.
* Загрузите `Geolocation.csv` и `trucks.csv` в только что созданную папку `data/`..

#### 2. HDFS CLI

* Откройте терминал.
* Вы можете получить доступ к команде hdfs из терминала. Это должно вывести справку из командной строки.
* Отображение версии HDFS с помощью `hdfs version`.

* Просмотрите все папки внутри HDFS с помощью `hdfs dfs -ls`.
   * Если вы наберете только `hdfs dfs -ls`, вы перейдете на `hdfs:///user/cloudera`. Вы должны найти файлы геолокации.
   * По умолчанию, если не укажете абсолютный путь к HDFS, HDFS начнется с вашего домашнего каталога HDFS `hdfs:///user/cloudera`.
* Переименуйте папку `hdfs:///user/cloudera/data` в `hdfs:///user/cloudera/geoloc` с помощью `hdfs dfs -mv` .
   * Помните, что по умолчанию `hdfs dfs -mv data geoloc` эквивалентен `hdfs dfs -mv hdfs:///user/cloudera/data hdfs:///user/cloudera/geoloc`.
* Создайте папку в HDFS с именем «test».
* Чтобы скопировать файлы с вашего локального компьютера в HDFS, существует `hdfs dfs -copyFromLocal <local_file> <path_in_HDFS>`. Попробуйте скопировать файл geolocation.csv с локальной виртуальной машины в папку test HDFS.
* В настоящее время папка `hdfs:///tmp` не имеет разрешений на запись для всех.
   В экосистеме Hadoop суперпользователем является не root, а hdfs. Поэтому нам нужно быть пользователем hdfs, прежде чем запускать установку разрешений. Запустите следующий скрипт.

```sh
sudo su -
su hdfs
hdfs dfs -chmod -R 777 /tmp
```

_PS : не забудьте дважды выйти из системы, чтобы вернуться к пользователю `сloudera`, иначе у вас возникнут ошибки при доступе к папкам `HDFS_`.

#### 3. Map Reduce 

* Запустите пример Pi:
`yarn jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar pi 4 100`

Наблюдайте за потоком. Проверить логи выполнения примера можно в Firefox на вкладке `Hadoop > YARN Resource Manager`.

Задание: подсчитать количество слов в файле внутри HDFS, запустим его для файла `hdfs:///user/cloudera/geoloc/`.

* Запустить `yarn jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar wordcount geoloc/geolocation.csv output`.
   * Команда может быть отменена, если `hdfs:///user/cloudera/output` уже существует, в этом случае удалите папку с помощью `hdfs dfs -rm -r -f output`.
* Проверьте папку `hdfs:///user/cloudera/output`. Вы можете использовать «hdfs dfs -ls output» и «hdfs dfs -cat output/part-r-00000».
   * Также просмотрите журналы на новой вкладке `Hadoop > YARN Resource Manager` в Firefox.

* Там работает только один `reducer`. Вы можете редактировать количество работающих `reducers` с помощью флага `-D mapred.reduce.tasks=10` . Отредактируйте предыдущую команду, чтобы изменить количество работающих `reducers`. Возможно, вам следует удалить или изменить имя папки вывода.
   * Еще раз проверьте выходную папку. Можете ли вы заметить разницу с предыдущим исполнением?

### Самостоятельная работа 3-1. Работа в HDFS в экосистеме cloudera
#### Цель работы
Изучение основных операций для работы с распределенной файловой системой HDFS.
#### Задачи работы
* подготовка окружения
* запуск shell-клиента
* изучение основных shell-команд
1. Развернуть виртуальное окружение.

2. Вывести с помощью команды `help` описание основных команды shell-клиента.

3. Просмотреть корневую директорию `HDFS`.

4. Создать в HDFS в директории `/user/mgpu` поддиректорию `ваше_фио`.

5. Создать в локальной файловой системе случайный текстовый файл размером 10 Mb с именем, образованным вашими инициалами
base64 /dev/urandom | head -c 10000000 > file.txt .

6. Заархивировать созданный текстовый файл gzip -c file.txt > file.gz .

7. Скопировать текстовый файл и архив в директорию /user/mgpu/fio HDFS виртуальной машины.

8. Просмотреть файл и архив с помощью утилит `cat`, `text` в комбинации с каналами и утилитами `head`, `tail` -- привести не менее 3 вариантов команд и просмотра файла. 

9. Создать копию файла file.txt вида date_file.txt, где в начале имени файла-копии указана текущая дата. Вывести листинг.

10. Вывести статистику по директории `/user/mgpu/fio` виртуальной машины.

11. Удалить поддиректорию `/fio` со всем содержимым.

Если в системе ещё нет /dev/random и /dev/urandom, то их можно создать следующими командами:

- mknod -m 644 /dev/random c 1 8 

- mknod -m 644 /dev/urandom c 1 9 

- chown root:root /dev/random /dev/urandom 

Перед выполнением команд желательно создать контрольную точку (в HyperV) для восстановления машины до применения команд. 

12. Подсчитать количество слов в файле внутри HDFS с помощью методологии `Map Reduce` (размер файла не менее 128 Мб).

### Самостоятельная работа 3-2. Аналитика больших данных в экосистеме cloudera

#### Pig


Pig — это расширение для компиляции определенного языка Pig Latin в сценарии MapReduce.

Можно запустить двумя способами:

1. В интерактивном режиме через Терминал, запустив оболочку Pig с помощью `pig` и выполняя команды одну за другой.
2. В `Hue`можно перейти в редактор Pig через `Query > Editor > Pig`. Это предпочтительный метод, если хотим запускать полные сценарии, но выполняется намного дольше, чем оболочка `Pig`.

* Запустите следующий скрипт/команды, чтобы загрузить и отобразить первые десять строк из файла геолокации:

```
geoloc = LOAD 'geoloc/geolocation.csv' USING PigStorage(',') AS (truckid,driverid,event,latitude,longitude,city,state,velocity,event_ind,idling_ind);

geoloc_limit = LIMIT geoloc 10;

DUMP geoloc_limit;
```

* Посчитать статистику по этому файлу. 

```
geoloc = LOAD 'geoloc/geolocation.csv' USING PigStorage(',') AS (truckid:chararray, driverid:chararray, event:chararray, latitude:double, longitude:double, city:chararray, state:chararray, velocity:double, event_ind:long, idling_ind:long);

truck_ids = GROUP geoloc BY truckid;

result = FOREACH truck_ids GENERATE group AS truckid, COUNT(geoloc) as count;

STORE result INTO 'results';

DUMP result;
```

* Анализ:
   * Проверьте папку «results», хранящуюся в HDFS, по строке `STORE result`. Что вы можете сказать по сравнению с количеством слов MapReduce?
   * Также просмотрите журналы на новой вкладке `Hadoop > YARN Resource Manager` в Firefox. Поясните список логов в журнале.
* Можете ли вы подсчитать список различных городов, посещенных каждым идентификатором грузовика, и среднюю скорость для каждого идентификатора грузовика?



#### Hive

Как и в случае с Pig, также есть возможность поработать с инструментом `hive` в терминале или, воспользовавшись `Hue` и перейти к редактору `Hive` через `Query > Editor > Hive`.

* При использовании терминала создайте внешнюю таблицу для папки `geoloc`, содержащую файл geolocation.csv. Возможно, потребуется переместить файл `trucks.csv` из папки `geoloc` с помощью `hdfs dfs -mv geoloc/trucks.csv`. Следующая команда создает таблицу Hive, указывающую на расположение в HDFS. Вы можете удалить таблицу, это не повредит данные в HDFS:

```sql
CREATE EXTERNAL TABLE geolocation (truckid STRING, driverid STRING, event STRING, latitude DOUBLE, longitude DOUBLE, city STRING, state STRING, velocity DOUBLE, event_ind BIGINT, idling_ind BIGINT)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/cloudera/geoloc';
```

```sql
SELECT truckid FROM geolocation LIMIT 10;
```

* Или с помощью Hue: на боковой панели выберите SQL, нажмите кнопку +, а затем вручную выберите файл CSV.
* Можете ли вы снова подсчитать список различных городов, посещенных каждым идентификатором грузовика, и среднюю скорость для каждого идентификатора грузовика?

#### Индивидуальное задание "Создание таблицы в Hive"
1.	Скачайте [датасет](https://github.com/BosenkoTM/cloudera-quickstart/blob/main/data/athlete.snappy.parquet) или [тут](https://storage.googleapis.com/otus_sample_data/athlete.snappy.parquet) 
3.	Через HUE загрузите файл в папку /user/cloudera/athlete
4. В навигационном меню выберите “Files”.
5. Создайте папку.
6. Загрузите файл, нажав Upload.
7.	Перейдите в “Editor > Hive” и выполните запрос:

```sql
CREATE EXTERNAL TABLE athlete (
    ID INT,
    Name STRING,
    Sex STRING,
    Age INT,
    Height INT,
    Weight INT,
    Team STRING,
    NOC STRING,
    Games STRING,
    `Year` INT,
    Season STRING,
    City STRING,
    Sport STRING,
    Event STRING,
    Medal STRING 
)
STORED AS PARQUET
LOCATION '/user/cloudera/athlete'
```

#### Impala

`Apache Impala` — это механизм SQL-запросов с открытым исходным кодом и массово-параллельной обработкой данных, хранящихся в компьютерном кластере под управлением Apache Hadoop.

Чтобы сэкономить время при выполнении запросов, Impala не проверяет постоянно изменения метаданных. Итак, первое, что мы должны сделать, это сообщить `Impala`, что ее метаданные устарели. Затем мы должны увидеть наши таблицы, готовые к запросу:

* Перейдите в редактор `Impala` вместо редактора `Hive` и запустите

```sql
invalidate metadata;

show tables;
```
Перезапустите один из сценариев SQL, которые запускали ранее. Заметили разницу в скорости?

В редакторе слева от таблицы результатов можно увидеть небольшой значок графика. Благодаря этому можно визуализировать результаты на диаграмме, а не в таблице.

* Подсчитайте количество геолокаций для каждого грузовика и отобразите его на гистограмме.
* Выберите грузовик `A80` и нанесите его координаты геолокации в редакторе `Impala`.

