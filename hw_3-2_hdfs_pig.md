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
