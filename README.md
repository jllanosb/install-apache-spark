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
- **Java** 8, 11 o 17 (según versión de Spark)
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

# 1. Descargar Apache Spark

Recomendación estable: Spark 3.5.7 (con Hadoop 3.4.2 prebuilt, compatible con 3.4.x runtime sin conflicto)
```bash
cd /tmp
wget https://dlcdn.apache.org/spark/spark-4.0.1/spark-4.0.1-bin-hadoop3.tgz
sudo mkdir -p /opt/spark
sudo tar -xzf spark-4.0.1-bin-hadoop3.tgz -C /opt/spark --strip-components=1
sudo chown -R hadoop:hadoop /opt/spark
```