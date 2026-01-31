# INSTALACIÓN DE APACHE SPARK EN UBUNTU
Apache Spark es un framework de procesamiento de datos de código abierto diseñado para trabajar con grandes volúmenes de información de manera rápida, distribuida y tolerante a fallos.
Es una evolución de Hadoop MapReduce, ofreciendo un rendimiento mucho mayor gracias a su uso intensivo de procesamiento en memoria (in-memory computing).

Se utiliza en:
- Big Data Analytics
- Machine Learning
- Procesamiento de flujos en tiempo real
- SQL distribuido
- Integración con bases de datos y sistemas de almacenamiento distribuidos (HDFS, S3, Cassandra, etc.)

## 🧩 Componentes principales de Apache Spark
1. ⭐ Spark Core

Es el corazón del sistema.
Proporciona:
- Gestión de tareas distribuidas
- Gestión de memoria
- Recuperación ante fallos
- API para RDD (Resilient Distributed Datasets)

2. 🗄️ Spark SQL

Permite ejecutar:
- Consultas SQL
- Operaciones sobre DataFrames y Datasets
- Integración con catálogos de datos (Hive, etc.)

3. 📊 Spark Streaming / Structured Streaming

Para procesamiento de datos en tiempo real.
Structured Streaming es la versión moderna y más robusta.

4. 🧠 MLlib

Biblioteca de Machine Learning distribuido:
- Clasificación
- Regresión
- Clustering
- Recomendación

5. 🌐 GraphX

Para trabajar con grafos de forma distribuida (PageRank, análisis de redes, etc.)

6. 🚀 Cluster Managers

Spark puede ejecutarse sobre varios sistemas de gestión de clúster:
- **Standalone** (incluido con Spark)
- **YARN** (Hadoop)
- **Mesos**
- **Kubernetes**

# 🖥️ Requisitos mínimos para instalar Apache Spark
✔️ Requisitos de software
- **Java** 11 o 17 (según versión de Spark)
- **Python** (si planeas usar PySpark): 3.7 o superior
- **Scala** (opcional): usualmente 2.12 o 2.13
- **Hadoop** (opcional): solo necesario si usarás HDFS o YARN

Spark puede correr **sin Hadoop** en modo Standalone.

# 🧰 Requisitos mínimos de hardware (modo local)

Para pruebas y aprendizaje en tu PC:

🔸 Mínimo funcional:
- 1 CPU
- 4 GB RAM
- 10 GB de espacio en disco

🔸 Recomendado:
- 4 CPU
- 8–16 GB de RAM
- SSD, NVME si posible

## 🏭 Requisitos típicos para un clúster Spark (producción)

Depende de la carga, pero comúnmente:
- **Nodos de trabajo (Workers)**: 4–16 CPU cada uno
- **RAM por nodo**: entre 32–128 GB
- **Red**: 10 Gbps
- **Almacenamiento**: HDFS o S3

Nota. Tener instalado Ubuntu 24.04 / usuario hadoop / Java 11 con [Hadoop](https://github.com/jllanosb/install-apache-hadoop) + [Hive Metastore PostgreSQL](https://github.com/jllanosb/install-apache-hive)

# Habilitar Java 17

🔹 Paso 1: Instalar OpenJDK 17
```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
```
Exportar la ruta manualmente en tu entorno:
```bash
sudo nano ~/.bashrc
```
Java 17
```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```
y luego recarga el entorno:
```bash
source ~/.bashrc
```
Ejecuta:
```
java -version
```
# 1. Descargar Apache Spark

Recomendación estable: Spark 3.5.8 (con Hadoop 3.4.2 prebuilt, compatible con 3.4.x runtime sin conflicto) [Consulta nuevas versiones](https://spark.apache.org/downloads.html) Instalaremos `Apache Spark 4.0.1`:
```bash
cd /tmp
wget https://dlcdn.apache.org/spark/spark-4.0.1/spark-4.0.1-bin-hadoop3.tgz
sudo mkdir -p /opt/spark
sudo tar -xzf spark-4.0.1-bin-hadoop3.tgz -C /opt/spark --strip-components=1
sudo chown -R hadoop:hadoop /opt/spark
```

# 2. Variables de entorno Spark
```bash
sudo -u hadoop nano ~/.bashrc
```
agregar:
```bash
# Apache Spark
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
export PYSPARK_PYTHON=/usr/bin/python3
export HADOOP_CONF_DIR=/opt/hadoop/etc/hadoop
export YARN_CONF_DIR=/opt/hadoop/etc/hadoop
```
```bash
source ~/.bashrc
```
# 3. Integración Spark + Hadoop

Spark debe apuntar al mismo config de Hadoop para que YARN lo gestione.
```bash
sudo -u hadoop cp $SPARK_HOME/conf/spark-env.sh.template $SPARK_HOME/conf/spark-env.sh
sudo -u hadoop nano $SPARK_HOME/conf/spark-env.sh
```
agregar:
```bash
# Java For Spark
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export HADOOP_CONF_DIR=/opt/hadoop/etc/hadoop
export YARN_CONF_DIR=/opt/hadoop/etc/hadoop
```
# 4. Integrar Hive Metastore con Spark SQL
```bash
sudo -u hadoop cp /opt/hive/conf/hive-site.xml /opt/spark/conf/
```
Spark ya puede leer Metastore PostgreSQL.
# 5. Prueba avanzada Spark on YARN

Levantar YARN y DFS (si no está arriba)
```bash
start-dfs.sh
start-yarn.sh
```
Verificar
```bash
hdfs dfs -ls /user/hadoop/
hdfs dfs -chmod -R 755 /user/hadoop
hdfs dfs -rm -r /user/hadoop/.sparkStaging
```
Revisar la configuracion yarn-env.sh
```bash 
sudo nano $HADOOP_HOME/etc/hadoop/yarn-env.sh
```

Verificar y/o agregar 
```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
```

Verificar jars
```bash
ls /opt/spark/examples/jars/
```
Identifica que version de jar se tiene _2.12-3.5.7.jar or _2.13-4.0.1.jar
```
spark-examples_2.13-4.0.1.jar
```
# 6. Probar Funcionamiento de Spark
Ejecutar job spark ejemplo sobre YARN: segun la version jar (2.12-3.5.7)
```bash
spark-submit --master yarn --deploy-mode client \
  --class org.apache.spark.examples.SparkPi \
  $SPARK_HOME/examples/jars/spark-examples_2.12-3.5.7.jar 1000
```

Ejecuta el siguiente codigo si falla el codigo anterior al configurar el tamaño de memoria
```bash
spark-submit \
  --master yarn \
  --deploy-mode client \
  --conf spark.yarn.jars=local:///opt/spark/jars/* \
  --class org.apache.spark.examples.SparkPi \
  $SPARK_HOME/examples/jars/spark-examples_2.12-3.5.7.jar 1000
```

Ejecutar job spark ejemplo sobre YARN: segun la version jar (2.13-4.0.1)
```bash
spark-submit --master yarn --deploy-mode client \
  --class org.apache.spark.examples.SparkPi \
  $SPARK_HOME/examples/jars/spark-examples_2.13-4.0.1.jar 1000
```
Ejecuta el siguiente codigo si falla el codigo anterior al configurar el tamaño de memoria
```bash
spark-submit \
  --master yarn \
  --deploy-mode client \
  --conf spark.yarn.jars=local:///opt/spark/jars/* \
  --class org.apache.spark.examples.SparkPi \
  $SPARK_HOME/examples/jars/spark-examples_2.13-4.0.1.jar 1000
```

Si corre sin error → Spark on Yarn OK + integración Hadoop OK.

Se tiene:
- Hadoop /opt/hadoop	✅
- Hive Metastore PostgreSQL	✅
- Spark on YARN /opt/spark	✅ integrado

