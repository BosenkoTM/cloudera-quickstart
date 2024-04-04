# Экосистема больших данных

Основные команды `HDFS` [тут](https://github.com/BosenkoTM/BigDataAnalitic/blob/main/workshop/01_seminar/commands_hdfs.md).

Учебное пособие по Hadoop внутри [Cloudera quickstart VM](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.13.0-0-virtualbox.zip).

## Подготовка рабочего места

#### Импорт виртуальной машины

Прежде чем приступить к работе, понадобится работающий кластер Hadoop..

В следующих упражнениях используем [Cloudera quickstart VM](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.13.0-0-virtualbox.zip).

* Загрузить и разархивировать приложение для VirtualBox.

* Импортировать файл `.ova` в VirtualBox. **не запускайте**, сначало настроить виртуальное окружение.

* Установить для виртуальной машины `cloudera-quickstart-vm-5.13.0-0-virtualbox` диапазон виртуальной памяти от 4 до 8 ГБ ОЗУ `Configuration > System`. 

* Запустить виртуальную машину `cloudera-quickstart-vm-5.13.0-0-virtualbox`.

* Чтобы [включить копирование/вставку](https://www.techrepublic.com/article/how-to-enable-copy-and-paste-in-virtualbox/) с ПК, выбрать `Devices > Shared Clipboard > Bidirectional`.

В `Cloudera quickstart VM` папка `hdfs:///tmp` не имеет разрешений на запись. В экосистеме `Hadoop` суперпользователем является не `root`, а `hdfs`. Установим разрешения на запись пользователем `hdfs`. Запустите следующий скрипт.


```bash
sudo su -
su hdfs
hdfs dfs -chmod -R 777 /tmp
```


##  1. Взаимодействие с компонентами Hadoop

Взаимодействие с кластером `Hadoop` будет осуществляться через [Hue](https://gethue.com/). 

`1.1.` Откройте Firefox: в терминале  введите  `firefox` или через панель управления: `Applications > System tools > Web browser`
* Откройте вкладку `Hue` - основной пользовательский интерфейс для взаимодействия с `HDFS`.
  * Учетные данные: **cloudera/cloudera**.
 
##  2. Hue
`Hue` — это интерактивный веб-редактор запросов в стеке `Hadoop`, который позволяет визуализировать данные и обмениваться ими. Hue предоставляет веб-интерфейс пользователя вместе с путем к файлу для просмотра HDFS. Наиболее важными функциями Hue являются браузер заданий, оболочка Hadoop, права администратора пользователя, редактор `Impala`, браузер файлов `HDFS`, редактор `Pig`, редактор `Hive`, веб-интерфейс `Ozzie` и доступ к `Hadoop API`. Этот макет веб-интерфейса помогает пользователям просматривать файлы, подобно тому, как пользователь `Windows` находит свои файлы на своем компьютере. `Hue` предоставляет пользовательский веб-интерфейс для языков программирования, который является удобным инструментом для пользователей, позволяющим избежать синтаксических ошибок при выполнении запросов. `Hue` можно установить или настроить только с помощью веб-браузера.

![hue_4.5](/images/hue_4.5.png)

Выбрав один из нескольких интерпретаторов для `Apache Hive`, `Apache Impala`, `Presto Apache Flink SQL`, `SparkSQL`, `Apache Phoenix`, `ksqlDB`, `Elastic Search`, `Apache Druid`, `PostgreSQL`, `Redshift`, `BigQuery`, позволяет подключиться ко всем базам данных.

##  3. Взаимодействие с файловой системой HDFS 

##  HDFS CLI

3.1. Открыть  терминал.

3.2. Проверить версию `HDFS` с помощью 

```bash
hdfs version
```
3.3. Просмотреть все папки внутри HDFS с помощью

```bash
hdfs dfs -ls /
```
   * Если вы наберете только `hdfs dfs -ls`, вы перейдете на `hdfs:///user/cloudera`. Необходимо найти файлы геолокации.
   * По умолчанию, если не указать абсолютный путь к HDFS, будет отображен домашний каталог HDFS `hdfs:///user/cloudera`.
     
3.4. Переименовать папку `hdfs:///user/cloudera/data` в `hdfs:///user/cloudera/geoloc` с помощью `hdfs dfs -mv` .
   * Помните, что по умолчанию

```bash   
     hdfs dfs -mv data geoloc
```
эквивалентен `hdfs dfs -mv hdfs:///user/cloudera/data hdfs:///user/cloudera/geoloc`.

3.5. Создать папку в HDFS с именем «test».
* Чтобы скопировать файлы с локального компьютера в HDFS, используют скрипт
```bash
  hdfs dfs -copyFromLocal <local_file> <path_in_HDFS>
```
  Попробуйте скопировать файл geolocation.csv с локальной виртуальной машины в папку test HDFS.
* По умолчанию каталог `hdfs:///tmp` не имеет разрешений на запись для всех. В экосистеме Hadoop суперпользователем является не root, а hdfs. Поэтому нам нужно быть пользователем hdfs, прежде чем запускать установку разрешений. Запустите следующий скрипт.

```bash
sudo su -
su hdfs
hdfs dfs -chmod -R 777 /tmp
```

_PS : не забудьте дважды выйти из системы, чтобы вернуться к пользователю `сloudera`, иначе у вас возникнут ошибки при доступе к папкам `HDFS_`.

### 4. Map Reduce 

4.1. Запустить пример Pi:
```bash
yarn jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar pi 4 100
```
4.2. Проверить результат выполнения в YARN. Проверить логи выполнения примера можно в Firefox на вкладке `Hadoop > YARN Resource Manager`.

4.3. Подсчитать количество слов в файле `geolocation.csv`, размещенного в каталоге `hdfs:///user/cloudera/geoloc/`.

```bash
yarn jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar wordcount geoloc/geolocation.csv output
```
Команда может быть отменена, если `hdfs:///user/cloudera/output` уже существует, в этом случае удалите папку с помощью
```bash
hdfs dfs -rm -r -f output
```
4.4. Проверить каталог `hdfs:///user/cloudera/output`. Использовать 
```bash
hdfs dfs -ls output
```
```bash
hdfs dfs -cat output/part-r-00000
```

Также просмотреть журналы на новой вкладке `Hadoop > YARN Resource Manager` в Firefox.

* Выполняет работу только один `reducer`. Вы можете редактировать количество работающих `reducers` с помощью флага `-D mapred.reduce.tasks=10` . Отредактируйте предыдущую команду, чтобы изменить количество работающих `reducers`. Возможно, вам следует удалить или изменить имя папки вывода.

## 5. Pig

`Pig` состоит из двух частей:

- язык для описания потоков `Pig Latin`;
- исполнительная среда для запуска сценариев `Pig Latin` (доступны два варианта: запуск на локальной `JVM` или исполнение в кластере Hadoop).
  
Сценарий `Pig` включает серию операций (преобразований), которые необходимо применить к входным данным, чтобы получить выходные данные. Эти операции описывают поток данных, который затем преобразуется (компилируется) исполнительной средой `Pig` в исполняемое представление и запускается для выполнения. Во внутренней реализации `Pig` трансформирует преобразования в серию заданий `MapReduce`.

Изначально `Pig` был создан для работы из консоли (оболочка `Grunt Shell`). В реализации от `Cloudera` работа с `Pig` осуществляется через простой и удобный веб-интерфейс. Открыть его можно через интерфейс `Hue`:

<p align="center">
  <img src="https://github.com/BosenkoTM/cloudera-quickstart/blob/main/images/pig_01.png" />
</p>

 ![pig_01](/images/pig_01.png)

<p align="center">
Разница между <b>Pig</b> и <b>Hue</b>
</p>

| Pig | Hue |
| --- | --- |
| Pig работает на клиентской стороне кластера. | Hue — это веб-интерфейс пользователя, который предоставляет ряд сервисов. Hue — это платформа взаимодействия с элементами экосистемы Hadoop. |
| Он не поддерживает JDBC. | Он поддерживает JDBC. |
| Pig реализован и доступен с помощью веб-интерфейса пользовательского интерфейса или интерфейса командной строки. | Hue реализован в веб-браузере, где мы можем получить доступ к множеству программ, установленных в Cloudera. |
| Pig не поддерживает схему для хранения данных. | Hue поддерживает схему вставки данных в таблицы. |
| Его используют исследователи и программисты. | В основном он используется аналитиками данных. |
| Он используется для обработки структурированных и полуструктурированных данных. | В основном он используется для обработки структурированных данных. |
| Он не поддерживает ODBC. | Он поддерживает ODBC. |
| Он поддерживает формат файлов Avro. | Он не поддерживает формат файлов Avro. |
| Pig использует алгоритм mapreduce для обработки и анализа данных. | Hue предоставляет редактор веб-интерфейса, доступ к которому можно получить из Hive и других языков программирования. |


## 6. Hive

Как и в случае с Pig, также есть возможность поработать с инструментом `hive` в терминале или, воспользовавшись `Hue` и перейти к редактору `Hive` через `Query > Editor > Hive`.

6.1 С использованием терминала, создайте внешнюю таблицу для папки `geoloc`, содержащую файл geolocation.csv. Возможно, потребуется переместить файл `trucks.csv` из папки `geoloc` с помощью `hdfs dfs -mv geoloc/trucks.csv`. Следующая команда создает таблицу Hive, указывающую на расположение в HDFS. Таблицу `Hive` можно удалять, это не изменит данных в HDFS:

```sql
CREATE EXTERNAL TABLE geolocation (truckid STRING, driverid STRING, event STRING, latitude DOUBLE, longitude DOUBLE, city STRING, state STRING, velocity DOUBLE, event_ind BIGINT, idling_ind BIGINT)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/cloudera/geoloc';
```

```sql
SELECT truckid FROM geolocation LIMIT 10;
```

6.2 Создание таблицы с помощью Hue: на боковой панели выберите SQL, нажмите кнопку +, а затем вручную выберите файл CSV.
6.3 Можете ли вы снова подсчитать список различных городов, посещенных каждым идентификатором грузовика, и среднюю скорость для каждого идентификатора грузовика?

## 7. Impala

`Apache Impala` — это механизм SQL-запросов с открытым исходным кодом и массово-параллельной обработкой данных, хранящихся в компьютерном кластере под управлением Apache Hadoop.

Чтобы сэкономить время при выполнении запросов, Impala не проверяет постоянно изменения метаданных. Итак, первое, что мы должны сделать, это сообщить `Impala`, что ее метаданные устарели. Затем мы должны увидеть наши таблицы, готовые к запросу:

7.1 Перейдите в редактор `Impala` вместо редактора `Hive` и запустите

```sql
invalidate metadata;
show tables;
```
7.2 Перезапустите один из сценариев SQL, которые запускали ранее. 

7.3 Проверить скорость выполнения запроса.

* Подсчитайте количество геолокаций для каждого грузовика и отобразите его на гистограмме.
 
* Выберите грузовик `A80` и нанесите его координаты геолокации в редакторе `Impala`.

## Самостоятельная работа 3-1. Работа в HDFS в экосистеме cloudera
### Цель работы
Изучение основных операций для работы с распределенной файловой системой HDFS.
### Задачи работы
* подготовка окружения,
* запуск shell-клиента,
* изучение основных shell-команд,

3.1.1. Развернуть виртуальное окружение.

3.1.2. Вывести с помощью команды `help` описание основных команды shell-клиента.

3.1.3. Просмотреть корневую директорию `HDFS`.

3.1.4. Создать в HDFS в директории `/user/mgpu` поддиректорию `ваше_фио`.

3.1.5. Создать в локальной файловой системе случайный текстовый файл размером 10 Mb с именем, образованным вашими инициалами
base64 /dev/urandom | head -c 10000000 > file.txt .

3.1.6. Заархивировать созданный текстовый файл gzip -c file.txt > file.gz .

3.1.7. Скопировать текстовый файл и архив в директорию /user/mgpu/fio HDFS виртуальной машины.

3.1.8. Просмотреть файл и архив с помощью утилит `cat`, `text` в комбинации с каналами и утилитами `head`, `tail` -- привести не менее 3 вариантов команд и просмотра файла. 

3.1.9. Создать копию файла file.txt вида date_file.txt, где в начале имени файла-копии указана текущая дата. Вывести листинг.

3.1.10. Вывести статистику по директории `/user/mgpu/fio` виртуальной машины.

3.1.11. Удалить поддиректорию `/fio` со всем содержимым.

Если в системе ещё нет /dev/random и /dev/urandom, то их можно создать следующими командами:

- mknod -m 644 /dev/random c 1 8 

- mknod -m 644 /dev/urandom c 1 9 

- chown root:root /dev/random /dev/urandom 

Перед выполнением команд желательно создать контрольную точку (в HyperV) для восстановления машины до применения команд. 

3.1.12. Подсчитать количество слов в файле внутри HDFS с помощью методологии `Map Reduce` (размер файла не менее 128 Мб).

`3.1.13` Создание таблицы в `Hive`
1.	Скачать [датасет](https://github.com/BosenkoTM/cloudera-quickstart/blob/main/data/athlete.snappy.parquet) или [тут](https://storage.googleapis.com/otus_sample_data/athlete.snappy.parquet) 
3.	Через `HUE` загрузите файл в папку `/user/cloudera/athlete`.
4. В навигационном меню выберите `Files`.
5. Создайте папку.
6. Загрузите файл в `HDFS`, нажав `Upload`.
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
- Sql запрос и результаты запроса отобразить в отчете.

`3.1.14` Проанализировать и визуализировать данные с помощью `Impala`(высокоскоростной механизм запросов SQL) или `Hive`. 
- Загрузить и разархивировать [babs_open_data_year_1.zip](https://disk.yandex.ru/d/JrboaizPXSh0Mg).
- Перенести данные `201402_trip_data.csv` в `HDFS`.
- Создать таблицу в Hive  с привязкой к внешним данным `201402_trip_data.csv`.
- выполнить запрос
```bash
select `startstation`, `endstation`, count(*) as trips 
from `default`.`201402_trip_data` 
group by `startstation`, `endstation` 
order by trips desc;
```
- Создать гистограмму, щелкнув значок «Hue Bar»:
- Установить ось X в качестве начальной станции, а ось Y — в качестве маршрута.
Установить лимит `10`.
- Выгрузить результаты, выбрав `CSV` или `Excel`.

## Результаты работы представить в виде файла ФИО-3-01.pdf (выгрузить в `moodle`), в котором отражены следующие результаты:
- постановка задачи;
- скрины хода выполнения работы при выполнении `Задание 3-01.1` - `Задание 3-01.14` ;
- прикрепить также следующие файлы:
  -  лог-файл работы `HDFS`;
  - скрины, подтверждающие выпознение работы в `Hadoop`.
  - Все выполненные команды оформить отдельным файлом  в формате `ФИО-3-01_группа.txt`.


## Самостоятельная работа 3-2. Аналитика больших данных в экосистеме cloudera

Пример выполнения в `Pig` 

### Pig

- В `Hue` перейти в редактор `Pig` через `Query > Editor > Pig`. Это предпочтительный метод, если хотим запускать полные сценарии, но выполняется намного дольше, чем оболочка `Pig`.

`3-2.1`  Скачать архив [Geolocation github zip](https://github.com/BosenkoTM/cloudera-quickstart/blob/main/data/geolocation.zip) или  [Geolocation data из Cloudera](https://disk.yandex.ru/d/aEkr5l-XClpYww).

Создать каталог `ex_3_2`:

```bash
mkdir ex_3_2
```
Пререйти в каталог `ex_3_2`:

```bash
cd  ex_3_2
```
Скачать данные `Geolocation data`:

```bash
wget https://github.com/BosenkoTM/cloudera-quickstart/blob/main/data/geolocation.zip
```
разархивировать данные
```bash
unzip geolocation.zip
```

`3-2.2` В `Hue`, выбрать `Browsers > Files`. 

Создайте новый каталог в `HDFS` с именем `data` внутри` HDFS` из `Hue`.
По умолчанию это должно быть создано под `hdfs:///user/cloudera/`.

![im_01](/images/im_01.jpg)

Загрузите `Geolocation.csv` и `trucks.csv` в только что созданную папку `geoloc/`.

![im_02](/images/im_02.jpg)


3-2.3 Запустить  скрипт/команды, чтобы загрузить и отобразить первые десять строк из файла 'geoloc/geolocation.csv'  в каталог 'results-geoloc'(он будет создан автоматически, после выполнения скрипта)  в редакторе `Pig` через Hue: `Query > Editor > Pig`:

Сначала убедиться в запуске `Dataflow` инструмента `Oozie`. На панели инструментов линк `Oozie` должен быть активным.

![oozie_flow_01](/images/oozie_flow_01.png)

```bash
geoloc = LOAD 'geoloc/geolocation.csv' USING PigStorage(',') AS (truckid:chararray, 
driverid:chararray, event:chararray, latitude:double, longitude:double, city:chararray, 
state:chararray, velocity:double, event_ind:long, idling_ind:long);
geoloc_limit = LIMIT geoloc 10;
STORE geoloc_limit INTO 'results-geoloc'; 
DUMP geoloc_limit;
```
Просмотреть Job DAG `Oozie` во время выполнения запроса к `Pig`:

![oozie_flow_02](/images/oozie_flow_02.png)

После получения метаданных выполнения запроса 

![pig_02](/images/pig_02.png)

Провирить в каталоге 'results-geoloc' файл  'part-r-00000'

![pig_03](/images/pig_03.png)

3-2.4 Посчитать статистику по файлу. 
```bash
geoloc = LOAD 'geoloc/geolocation.csv' USING PigStorage(',') AS (truckid:chararray, driverid:chararray, event:chararray, latitude:double, longitude:double, city:chararray, state:chararray, velocity:double, event_ind:long, idling_ind:long);
truck_ids = GROUP geoloc BY truckid;
result = FOREACH truck_ids GENERATE group AS truckid, COUNT(geoloc) as count;
STORE result INTO 'results2';
DUMP result;
```
Просмотреть Job DAG `Oozie` во время выполнения запроса к `Pig`

Провирить в каталоге 'results2' файл  'part-r-00000'

![pig_04](/images/pig_04.png)

3-2.5 Анализ.
- Провести анализ журналов `Hadoop > YARN Resource Manager` в Firefox. Продоставить в виде скринов все задачи, выполенные в п 3-2.1 - 3-2.4. 
- Подсчитать количество уникальных городов, в которых был уникальный грузовик по его `truckid`, среднюю скорость грузовика из файла [trucks.csv](https://github.com/BosenkoTM/cloudera-quickstart/blob/main/data/trucks.csv)?

## Результаты работы представить в виде файла ФИО-3-02.pdf (выгрузить в `moodle`), в котором отражены следующие результаты:
- постановка задачи;
- скрины хода выполнения работы при выполнении `Задание 3-02.1` - `Задание 3-02.5` ;
- прикрепить также следующие файлы:
  -  лог-файл работы `HDFS`;
  - скрины, подтверждающие выпознение работы в `Hadoop`.
  - Все выполненные команды оформить отдельным файлом  в формате `ФИО-3-02_группа.txt`.
