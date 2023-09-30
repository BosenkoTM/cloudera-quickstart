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


Виртуальная машина должна запуститься

* Чтобы [включить копирование/вставку](https://www.techrepublic.com/article/how-to-enable-copy-and-paste-in-virtualbox/), выберите `Devices > Shared Clipboard > Bidirectional`.

В первой части урока мы будем взаимодействовать с [trucks geolocation dataset from the Cloudera tutorial](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox.html). 



##  Задание 2-1 - Первые шаги в экосистеме Hadoop

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

### Самостоятельная работа

1. Развернуть виртуальное окружение

2. Вывести с помощью команды `help` описание основных команды shell-клиента.

3. Просмотреть корневую директорию `HDFS`.

4. Создать в HDFS в директории /user/mgpu поддиректорию `ваше_фио`.

5. Создать в локальной файловой системе случайный текстовый файл размером 10 Mb с именем, образованным вашими инициалами
base64 /dev/urandom | head -c 10000000 > file.txt .

6. Заархивировать созданный текстовый файл
gzip -c file.txt > file.gz .

7. Скопировать текстовый файл и архив в директорию /user/mgpu/fio HDFS виртуальной машины.

8. Просмотреть файл и архив с помощью утилит `cat`, `text` в комбинации с каналами и утилитами `head`, `tail` -- привести не менее 3 вариантов команд и просмотра файла. 

9. Создать копию файла file.txt вида date_file.txt, где в начале имени файла-копии указана текущая дата. Вывести листинг
10. Вывести статистику по директории `/user/mgpu/fio` виртуальной машины.

11. Удалить поддиректорию `/fio` со всем содержимым.

Если в системе ещё нет /dev/random и /dev/urandom, то их можно создать следующими командами:

- mknod -m 644 /dev/random c 1 8 

- mknod -m 644 /dev/urandom c 1 9 

- chown root:root /dev/random /dev/urandom 

Перед выполнением команд желательно создать контрольную точку (в HyperV) для восстановления машины до применения команд. 

#### 3. Map Reduce 

Now that storage is over, time to compute stuff.

* Run the Pi example : `yarn jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar pi 4 100`.  Observe the flow. You can check all logs in Firefox, in the `Hadoop > YARN Resource Manager` tab.

Now we want to run a wordcount on a file inside HDFS, let's run it on files inside `hdfs:///user/cloudera/geoloc/`. 

* Run `yarn jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar wordcount 
  geoloc/geolocation.csv output`.
  * The command may file if `hdfs:///user/cloudera/output` already exists, in that case remove the folder with `hdfs dfs -rm -r -f output`.
* Examine the `hdfs:///user/cloudera/output` folder. You can use `hdfs dfs -ls output` and `hdfs dfs -cat output/part-r-00000`. Explain
  * Also see the logs in a new tab to the favorite `Hadoop > YARN Resource Manager` in Firefox. Explain.

* Only one reducer is working there. You can edit the number of reducers running with the flag `-D mapred.reduce.tasks=10` . Edit the previous command to change the number of reducers working. You should maybe remove or change the name of the `output` folder.
  * Examine the output folder again. Can you note a difference with the previous execution ?



#### 4. Pig

Pig is an extension to compile Pig Latin, a specific language, into MapReduce scripts.

The following can be run in two ways :

1. Interactively through the Terminal, by launching a Pig shell with `pig `and run commands one by one.
2. In Hue, you can go to the Pig Editor via `Query > Editor > Pig`. This is the preferred method if you want to run full scripts but is much longer to run than the Pig shell.

*  Run the following script/commands to load and display the first ten lines from the geolocation file :

```
geoloc = LOAD 'geoloc/geolocation.csv' USING PigStorage(',') AS (truckid,driverid,event,latitude,longitude,city,state,velocity,event_ind,idling_ind);

geoloc_limit = LIMIT geoloc 10;

DUMP geoloc_limit;
```

* Let's try to compute some stats on this file. 

```
geoloc = LOAD 'geoloc/geolocation.csv' USING PigStorage(',') AS (truckid:chararray, driverid:chararray, event:chararray, latitude:double, longitude:double, city:chararray, state:chararray, velocity:double, event_ind:long, idling_ind:long);

truck_ids = GROUP geoloc BY truckid;

result = FOREACH truck_ids GENERATE group AS truckid, COUNT(geoloc) as count;

STORE result INTO 'results';

DUMP result;
```

* Analysis :
  * Check the `results` folder stored in HDFS by the `STORE result` line. What can you say compared to the MapReduce wordcount ?
  * Also see the logs in a new tab to the favorite `Hadoop > YARN Resource Manager` in Firefox. Explain.
* Are you able to count the list of distinct cities visited per truckid, and mean velocity per truckid ?



#### 5. Hive

Like for Pig, you have the choice between the `hive` command in the Terminal, or in Hue go to the Hive Editor via `Query > Editor > Hive`.

* If using the Terminal, create an external table for the `geoloc` folder which contains geolocation.csv. Maybe you should also move the `trucks.csv` file outside of the `geoloc` folder with `hdfs dfs -mv geoloc/trucks.csv`. The following command creates a Hive table pointing to a HDFS location. You can drop it, it won't destroy the data in HDFS : 

```sql
CREATE EXTERNAL TABLE geolocation (truckid STRING, driverid STRING, event STRING, latitude DOUBLE, longitude DOUBLE, city STRING, state STRING, velocity DOUBLE, event_ind BIGINT, idling_ind BIGINT)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/cloudera/geoloc';
```

```sql
SELECT truckid FROM geolocation LIMIT 10;
```

* Or with Hue, on the sidebar select SQL, click on the + button, and then manually select the CSV file.


* Are you again able to count the list of distinct cities visited per truckid, and mean velocity per truckid ?

  


#### 6. Impala

Apache Impala is an open source massively parallel processing SQL query engine for data stored in a  computer cluster running Apache Hadoop 

To save time during queries, Impala does not poll constantly for metadata changes. So the first thing we must do is tell Impala that its metadata is out of date. Then we should see our tables show up, ready to be queried: 

* Go to the Impala editor instead of Hive editor and run

```sql
invalidate metadata;

show tables;
```

* Rerun one of the SQL scripts you run before. Can you notice the speed difference ?

In the editor, on the left of results table you may notice a small graph icon. With this, you can visualize the results on a chart instead of a table. 

* Count the number of geolocations per trucks and display it on a bar chart.
* Select the truck `A80` and plot its geolocation coordinates in the Impala editor.



## TD2 - Unstructured data analytics

### Log exploration


Let's analyze some log files provided by the Cloudera VM. Those are provided locally in `/opt/examples/log_files/access.log.2`. 

_PS: if you are still stuck on this tutorial, you can consult the [original piece](https://www.cloudera.com/developers/get-started-with-hadoop-tutorial/exercise-2.html) to start._

* Import the data into HDFS, for example inside the folder `/user/cloudera/logs`.
* On HDFS, you can read the last data with `hdfs dfs -tail <path_to_file>`. Read the latest lines of the log file. What format is it ? How could you extract data from this ?

#### Impala analysis

Let's preprocess the Apache logs file with Hive before using Impala for analysis.

* Create an external Hive table `intermediate_access_logs`

```
CREATE EXTERNAL TABLE intermediate_access_logs (
    ip STRING,
    date STRING,
    method STRING,
    url STRING,
    http_version STRING,
    code1 STRING,
    code2 STRING,
    dash STRING,
    user_agent STRING)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.RegexSerDe'
WITH SERDEPROPERTIES (
    'input.regex' = '([^ ]*) - - \\[([^\\]]*)\\] "([^\ ]*) ([^\ ]*) ([^\ ]*)" (\\d*) (\\d*) "([^"]*)" "([^"]*)"',
    'output.format.string' = "%1$$s %2$$s %3$$s %4$$s %5$$s %6$$s %7$$s %8$$s %9$$s")
LOCATION '/user/cloudera/logs';
```

You may notice the difference with the previous table, we have changed the SERDE from CSV to regex. This SERDE is not provided by default, you need to add it via Hive with the following command :

```
ADD JAR /usr/lib/hive/lib/hive-contrib.jar;
```

* Let's create a table with the data preprocessed by the regex, so we can use it in Impala : 

```
CREATE EXTERNAL TABLE tokenized_access_logs (
    ip STRING,
    date STRING,
    method STRING,
    url STRING,
    http_version STRING,
    code1 STRING,
    code2 STRING,
    dash STRING,
    user_agent STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION '/user/hive/warehouse/tokenized_access_logs';

INSERT OVERWRITE TABLE tokenized_access_logs SELECT * FROM intermediate_access_logs;
```

* Update the metadata in Impala :

```
invalidate metadata;

show tables;
```

* Display how many times each product has been bought ?
* What percentage of `IP addresses`  went to checkout their basket ?
* If you case the date as a `Date` you should be able to build a web journey of an IP address on the website. For all IP adresses that went to checkout, compute the number of products each has bought before.

* **Bonus** - Try to connect your PowerBI to Impala, by connecting on `localhost:21000`.



#### Bonus - MapReduce code

You can find a [good Youtube video](https://www.youtube.com/watch?v=l3MssCo2eSU) on this matter.

* Open Eclispe. There you will find a `StubMapper`, `StubReducer` and `StubDriver`.

You can try to count how many times each product has been bought by completing those Stub classes. For that, in the `map` you must parse the log with the same regex as the Hive one, find if the url contains a product and put the product in key with value 1. 

* For that you will need to implement the `map` function in `StubMapper`, the `reduce` in `StubReducer` and the `main` in the `StubDriver`. The following are some examples :

```java
@Override
public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
    String v = value.toString();
    for (int i = 0; i < v.length(); i++) {
        character.set(v.substring(i, i + 1));
        context.write(character, one);
    }
}
```

``` java
@Override
public void reduce(Text key, Iterable<LongWritable> values, Context context) throws IOException, InterruptedException {
    long sum = 0;
    for (LongWritable val : values) {
        sum += val.get();
    }
    result.set(sum);
    context.write(key, result);
}
```

```java
public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new Configuration(), new StubDriver(), args);
    System.exit(res);
}
 
 @Override
 public int run(String[] args) throws Exception {

    Configuration conf = this.getConf();

    // Create job
    Job job = Job.getInstance(conf, "Job");
    job.setJarByClass(StubDriver.class);

    job.setMapperClass(StubMapper.class);
    job.setReducerClass(StubReducer.class);

    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(LongWritable.class);

    FileInputFormat.addInputPath(job, new Path(args[0]));
    job.setInputFormatClass(TextInputFormat.class);

    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setOutputFormatClass(TextOutputFormat.class);

    return job.waitForCompletion(true) ? 0 : 1;
}
```

* Create a `.jar` out of the project with all dependencies.
* Run the code through the `yarn jar ...` command.



## TD3 - Analytics on scraped websites

#### Prerequisites

We are going to install a Python 3 setup to freely play with Pyspark on data in HDFS. Follow the instructions carefully !

###### Miniconda

* Install [Miniconda](https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh)

```shell
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
conda init # if you did not choose to init conda during install
```



###### Python packages

* Open a new Terminal. You should see _(base)_ at the beginning of the command line.
* Install all the packages inside the base environment _(let's not create a conda environment today, if your environment gets corrupted you can delete the VM and start over)_ :

```
pip install numpy pandas jupyter scikit-learn matplotlib seaborn requests beautifulsoup4 scrapy 
```



###### Install Java 8

We need Java 8 to run Spark 2.0.

```shell
conda install -c cyclus java-jdk
export JAVA_HOME="/home/cloudera/miniconda3/jre"
```



###### Install Spark 2

* Look for the Hadoop version : `hadoop version`. It should be `2.6.0`
* Install Spark 2.4.5 prebuilt for Hadoop 2.6 :

```shell
wget https://archive.apache.org/dist/spark/spark-2.4.5/spark-2.4.5-bin-hadoop2.6.tgz
tar -xvf spark-2.4.5-bin-hadoop2.6.tgz
sudo mv spark-2.4.5-bin-hadoop2.6 /usr/local/spark
export SPARK_HOME="/usr/local/spark"
```

We should change 3 files below and replace Spark home path with new version’s path.

> /usr/bin/pyspark
> /usr/bin/spark-shell
> /usr/bin/spark-submit

* So let’s change `/usr/lib/spark` to `/usr/local/spark` in the previous files



#### Test Pyspark install

* Launch a Pyspark shell :

```shell
pyspark
```

* Run some pyspark code :

``` python
sc.textFile("/opt/examples/log_files/access.log.2").take(10)
```

* Exit the shell and add path to Hadoop configuration files to access HDFS :

``` shell
HADOOP_CONF_DIR="/etc/hadoop/conf" pyspark
```
* Run some pyspark code :

```python
sc.textFile("hdfs:///user/cloudera/logs/*").take(10)
```

* Exit the shell `exit()` and relaunch the command but using the notebook this time :

```shell
HADOOP_CONF_DIR="/etc/hadoop/conf" PYSPARK_PYTHON="/home/cloudera/miniconda3/bin/python" PYSPARK_DRIVER_PYTHON_OPTS="/home/cloudera/miniconda3/bin/jupyter-notebook --port=9099 --NotebookApp.token=''" pyspark
```

This creates a Jupyter notebook on port 9099, you can access it on Firefox through `http://localhost:9099`.

* In your first cell you can try :

```python
sc = SparkContext.getOrCreate()
def mod(x):
    import numpy as np
    return (x, np.mod(x, 2))

rdd = sc.parallelize(range(1000)).map(mod).take(10)
print(rdd)
```

Note how we use numpy inside a function inside a `map`.

**Beware :** actually this setup is not optimal because our Spark cluster doesn't run on YARN here so data from HDFS should normally not be accessible. But we are on a single node Cloudera VM so the data can be moved on the same machine and we are not going to manipulate huge data volumes so who cares ? In real life, Spark will be properly configured to HDFS.



#### Testing Pyspark on Apache logs

Let's analyze some log files provided by the Cloudera VM. Those are provided locally in `/opt/examples/log_files/access.log.2` and previously imported to HDFS.

* Run a test Spark function :

```python
sc.textFile("hdfs:///user/cloudera/logs/*").take(10)
```

* Let's apply the regex from our Hive exercise inside a Spark map to extract IP addresses. Run the following in a new cell :

```python
import re
p = re.compile('([^ ]*) - - \\[([^\\]]*)\\] "([^\ ]*) ([^\ ]*) ([^\ ]*)" (\\d*) (\\d*) "([^"]*)" "([^"]*)"')

def extract_ip(sentence): # you can test this function on a single Apache log to test
    m = p.match(sentence)
    return (m.group(1))

rdd.map(extract_ip).take(10)
```

* Using this as base, compute how many times each product has been bought. 
  * Don't forget you can test your extract function on a single datum before going into the map function. For example take one line of the Apache log with `rdd.take(1)[0]`, see hat happens when you pass it to `extract_ip` and when you know the result, use the function in `rdd.map` to apply it to every log ! 
  * Recall you should `return (key,value)` pairs from the function inside the `rdd.map` so you can use `rdd.reduceByKey`.
* We can extract all the data from the file into a Spark dataframe :

```python
import re
p = re.compile('([^ ]*) - - \\[([^\\]]*)\\] "([^\ ]*) ([^\ ]*) ([^\ ]*)" (\\d*) (\\d*) "([^"]*)" "([^"]*)"')

def extract_all(sentence):
    m = p.match(sentence)
    return m.groups()

data = rdd.map(extract_all)
df = data.toDF(["ip","date","method","url","http_version","code1","code2","dash","user_agent"])
df.show()
```

* Now do the unstructured log analytics questions in pyspark on this dataframe :
  * Display how many times each product has been bought ?
  * What percentage of `IP addresses`  went to checkout their basket ?
  * If you case the date as a `Date` you should be able to build a web journey of an IP address on the website. For all IP adresses that went to checkout, compute the number of products each has bought before.



#### Studying Wikipedia file dumps 

* Read a bit on https://en.wikipedia.org/wiki/Wikipedia:Database_download. You can find the full [dump archives here](https://dumps.wikimedia.org/enwiki/latest/).

* Get [pagelinks](https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pagelinks.sql.gz) data. You can find the structure in [this page](https://www.mediawiki.org/wiki/Manual:Pagelinks_table).
  * Download and unzipping takes a long time, it's 6GB compressed, 17GB uncompressed...

```
wget https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pagelinks.sql.gz
gzip -d enwiki-latest-pagelinks.sql.gz
```

* Get a [sample](https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles1.xml-p10p30302.bz2) of wikipedia page and the [full one](https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2). Unzip them.
* Use [spark-xml](https://github.com/databricks/spark-xml) on the <page> tag to retrieve each page has a row.



## Appendix

### Ambari installation

For the more adventurous one, there are other ways to install a Hadoop cluster :

* Download the [Hortonworks](https://www.cloudera.com/downloads/hortonworks-sandbox.html) sandbox. https://archive.cloudera.com/hwx-sandbox/hdp/hdp-3.0.1/HDP_3.0.1_virtualbox_181205.ova.
* Using a cloud provider. On AWS, you can spin EC2 instances, so you can use your free AWS credits, or go on [Amazon EMR](https://aws.amazon.com/emr/).
* Using [Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Quick+Start+Guide). 
* Going full manual.

The following steps indicate my take on the Ambari method inside 3 generated VMs by Vagrant.



#### Setup Ambari cluster through Vagrant

```
git clone https://github.com/u39kun/ambari-vagrant.git
cat ambari-vagrant/append-to-etc-hosts.txt >> C:\Windows\System32\drivers\etc\hosts
cd ambari-vagrant
cd centos6.8 # ubuntu18.4

cp ~/.vagrant.d/insecure_private_key .

vagrant up c6801 # u1801
vagrant up c6802 # u1802
vagrant up c6803 # u1803

vagrant ssh c6801 # u1801
sudo su -
wget -O /etc/yum.repos.d/ambari.repo http://public-repo-1.hortonworks.com/ambari/centos6/2.x/updates/2.6.2.0/ambari.repo
yum install ambari-server -y

# wget -O /etc/apt/sources.list.d/ambari.list http://public-repo-1.hortonworks.com/ambari/ubuntu18/2.x/updates/2.7.4.0/ambari.list
# apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 B9733A7A07513CAD
# apt-get update
# apt-get install ambari-server -y

ambari-server setup -s
ambari-server start
```

Once Ambari Server is started, open port 8080 on Virtualbox then hit http://localhost:8080 from your browser on your local computer.

Note that Ambari Server can take some time to fully come up and ready  to accept connections. Keep hitting the URL until you get the login  page. 

Once you are at the login page, login with the default username **admin** and password **admin**. 

###### Create a Cluster

Select HDP 3.1.4

On the Install Options page, use the FQDNs of the VMs. For example:

```
c6801.ambari.apache.org
c6802.ambari.apache.org
c6803.ambari.apache.org
```

Specify the the non-root SSH user **vagrant**, and upload **insecure_private_key** file that you copied earlier as the private key.

Follow the on-screen instructions to install your cluster. Just Install HDFS, YARN, Tez, Hive, Zookeeper,  Kafka, Spark, Spark2, Zeppelin, Ambari Metrics

Let defaults everywhere. Where you need to configure services put passwords everywhere.

 **vagrant suspend** and  **vagrant resume** when over.

When done testing, run **vagrant destroy -f** to purge the VMs.

**Recommendations**

If you would like to save state for a period of time and you plan to stop using your Mac during that time, if you sleep your Mac the cluster  should continue from where it left off after you wake the Mac. 

When stopping a set of VMs--if you don't need to save cluster state--it can  be helpful to stop all services first, stop ambari-server (`ambari-server stop`), and then issue a Vagrant `halt` or `suspend` command.

When restarting a cluster after halting or taking a snapshot, check Ambari server status and restart it if necessary:

```
ambari-server status   
ambari-server start
```

After logging into the Ambari Web UI, expect to see alert warnings or errors  due to timeout conditions. Check the associated messages to determine  whether they might affect your use of the virtual cluster. If so, it can be helpful to stop and restart one or more associated components. 

