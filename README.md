# Экосистема больших данных

Учебное пособие по Hadoop внутри [Cloudera quickstart VM](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.13.0-0-virtualbox.zip).

## Подготовка рабочего места

#### Импорт виртуальной машины

Прежде чем приступить к работе, понадобится работающий кластер Hadoop..

В следующих упражнениях мы будем использовать [Cloudera quickstart VM](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.13.0-0-virtualbox.zip).

* Загрузите и разархивируйте приложение для VirtualBox.

* Импортируйте файл `.ova` в VirtualBox. **не запускайте**, сначало настроить виртуальное окружение.

  * Требуется настроить виртуальную машину на использование как минимум 6-8 ГБ ОЗУ `Configuration > System`. 

* Запустите виртуальную машину с помощью зеленой стрелки.

* Чтобы [включить копирование/вставку](https://www.techrepublic.com/article/how-to-enable-copy-and-paste-in-virtualbox/), выберите `Devices > Shared Clipboard > Bidirectional`.

В первой части урока мы будем взаимодействовать с [trucks geolocation dataset from the Cloudera tutorial](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox.html). 

##  Первые шаги в экосистеме Hadoop

Взаимодействия с кластером Hadoop будет осуществляться через [Hue](https://docs.cloudera.com/documentation/enterprise/5-13-x/topics/hue.html).

* * Откройте Firefox: откройте терминал и введите  `firefox` или `Applications > System tools > Web browser`
* Откройте вкладку `Hue`. Это будет наш основной пользовательский интерфейс для взаимодействия с HDFS.
  * Учетные данные: **cloudera/cloudera**.

####  1. Первые шаги по HDFS 

* Скачать и разархивировать [Geolocation data from Cloudera](https://www.cloudera.com/content/dam/www/marketing/tutorials/getting-started-with-hdp-sandbox/assets/datasets/Geolocation.zip). 
  * Это можно сделать из Firefox, загрузив его в папку на виртуальной машине.
  * Вы также можете воспользоваться терминалом `wget https://www.cloudera.com/content/dam/www/marketing/tutorials/getting-started-with-hdp-sandbox/assets/datasets/Geolocation.zip` и `unzip Geolocation.zip`.
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

### Самостоятельная работа 3

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
