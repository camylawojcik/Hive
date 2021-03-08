#### HDFS
- Split do arquivo a ser armazenado em blocos ou chunks, tipicamente de 64MB à 128MB
- Armazena cada bloco em um DataNode
- Replica cada bloco para 3 nodes (default)
  - Fault Tolerance
- Hadoop consegue detectar quando um bloco de dados não foi replicado, fazer a cópia e replicar
#### MapReduce
  - é como seu script é interpretado e traduzido;
  - Map, Shuffle and Sort, Reduce
  - Pode usar um ou mais mapper, dependendo do numero de blocos;
  - na maioria dos casos, um mapper é atribuido para cada bloco.
  - Em um parallel framework, você paralelismo e computação distribuida sem precisar gerenciar esses detalhes por conta. O hadoop mapreduce cuida disso pra você
  - O número de reducer depende da configuração do job e do problema a ser resolvido
  
### Introduction to Hive
  - Opens up Big Data to the masses
  - Provides a SQL-like query language and interfaces
  - Builds on Hadoop core using MapReduce for Execution
  - possibilita o uso de 'schema' em dados não estruturados
  #### Hive Architecture
  - Metastore: a arquitetura do Hive consiste em "Relational Metastore", onde o Hive armazena definições de tabelas, location of underline data, datatypes informations, informações sobre partições e etc. Todas as definições ficam aqui.
    - Derby database: é o default, tem várias limitações, especificamente quando está em multiuser. Muito comum utilizar o MySQL
    - HiveQL: driver responsavel por processar, compilar, otimizar e executar consultas
    - O hive nada mais é do que uma interface que permite executarmos SQL queries que são traduzidas ao MapReduce;
    
  #### Hive Principles - Schema on Read
  - Permite adicionar "alguma" estrutura o caos.
  - A estrutura é aplicada quando o dado é lido;
  - Usa Serializers/Deserializers para ler e escrever 
  - Acesso mais fácil ao dado e abstrações
  #### Hive Warehouse
  - Metadados sobre todos os objetos conhecidos para o hive, persistidos no Metastore
  - __Databases__: abstração para separar tabelas em um domain
  - Tables
    - Partitions: split tables em partições baseado no valor de uma coluna
      - Buckets/Clusters: partições podem conter buckets baseados no hash de uma ou mais colunas. Não são fáceis de gerenciar, mas oferecem beneficios de performance em joins.
    - Localização do Local Hive warehouse: o hive define a localização no HDFS. É através desse warehouse que o Hive distingue quais são as Locally Managed Tables e quais são as Unmaneged external tables;
      - 2 approachs:
        - __Local Hive Warehouse__: 
          managed by hive
          tipicamente no caminho /hive/warehouse/
          Drop table vai dropar o dado e o meta-dado
        - __External Tables:__ 
          Hive manages the meta-data only 
          O dado pode estar em qualquer lugar no HDFS ou outro
          Drop table vai dropar apenas a definição de tabela. O dado permanece intocado.

### HiveQL
  - Select * from where LIMIT;
  - Distinct, alias, expressão regular(segue a sintaxe Java);
  - Translate to mapReduce
  - Possível alterar a sintaxe para FROM XXX select xxxx WHERE;
  - Hive não é case sensitive
  - ; para finalizar o comando
  - Sub queries & Union

### Create Database:
  - Abstração para agroupar as tabelas;
  - /hive/warehouse/: nada mais que o diretório/local especificado onde o hive é "dono" e gerencia os dados; Ao criar uma tabela no hive e carrega-lá, é onde o dado estará;
  - base.db
  - CREATE DATABASE|SCHEMA IF NOT EXISTS database_name campos,LOCATION, WITH DBPROPERTIES
  - Pode conter managed tables ou external tables;
  - USE db_name;
  - DROP DATABASE|SCHEMA if exists xxxx; 
  - Partition External Table: Precisa indicar onde fica a partição assim que particionar
  - ``` CREATE [EXTERNAL] TABLE [IF NOT EXISTS] DB.TABLE_NAME [col_name], [PARTITIONED BY (col_name data_type)] [ROW FORMAT] [STORED AS file_format] [LOCATION hdfs_path] [TBLPROPERTIES (property_name= xxx)]; 
    LOAD DATA INPATH 'XXX/XXX/' INTO TABLE movies;
    ```

### HQL
  - Array<data type>
  - Maps MAP<primitive, data type>
  - Struct <col:data type>
  - Union Type 
  - Cast('13' AS INT)
  - #### Partitions:
    - Managed Tables:
    - ```
      CREAT TABLE page_views(eventTime STRING, userid STRING, page STRING) PARTITIONED BY(dt STRING, applicationtype STRING)
      STORED AS TEXTFILE;
      LOAD DATA INPATH 'mydata/android/Aug_10_2013/pageviews INTO TABLE page_views
      PARTITIONS(dt=2013-08-10', applicationtype='android');```
        - Precisa informar a partição, senão gera exceção;
        - __Virtual Columns__
    - External Partitioned Tables:
      - ```
      CREATE EXTERNAL TABLE page_views(eventTime STRING, userid STRING, page STRING) PARTITIONED BY(dt STRING, applicationtype STRING)
      STORED AS TEXTFILE;
      ALTER TABLE page_views ADD PARTITION(dt='2013-09-09', applicationtype='Windoes Phone 8') LOCATION 'somewhere/on/hdfs/data/2013-09-09/wp8';
      ```
      - explain select * from xxx where id = 13;
      - MSCK REPAIR TABLE xxxx; : o hive scaneia a tabela e encontra as novas partições;
  - #### Multiple Inserts
    - ``` FROM from_Statement
          INSERT OVERWRITE TABLE table1 [PARTITION(partcol1=val1) select_statement1
          INSERT OVERWRITE TABLE table1 [PARTITION(partcol1=val1) select_statement1
      ```
          
    - Interchangeability of blocks;
    - Por default, o Hive cria partições em managed tables 
      - Dynamic Partition
        - Default maximum dynamic partition =1000 (hive.exec.max.dynamic.partitionshive.exec.max.dynamic.partitions.pernode)
        - number of files a data node can service in hdfs-site.xml
          - dfs.datanode.max.xcievers=4096
        - ```CREATE TABLE views_Stg(eventTime String, userID STRING) partitioned by(DT STRING, APPLICATIONTYPE STRING, PAGE STRING);```
        - ``` FROM page_Views src INSERT OVERWRITE TABLE view_Stg PARTITION(dt='2013-09-13', appkicationtype='web, page='home') SELECT src_Eventtime, src.userid, where dt='2013-09-13' AND applicationtype ='Web', page='Home' ```
          - o hive trabalha melhor com arquivos maiores do que com vários menores; Cada arquivo é quebrado em blocos, e cada bloco reserva um mapper. O bloco normalmente tem entre 64mb e 128mb.
          - Uma das razões para blocos maiores é o namenode (bloco responsável por entender onde o dado está), ele precisa conseguir rastrear o dados. E quanto mais dado, maior o rastreamento e maior o uso de memória. E toda essa informação armazenada no namenode em uma estrutura de hash.
          - Quanto maior o tamanho do bloco, menor o número de blocos necessário para representar os arquivos
          - Aumentar o numero maximo de arquivos que um datanode pode servir: hdfs-site.xml 
      - Static Partition
  - __HQL__ 
    - GORUPING SETS ((A,B),A) = Seria a mesma coisa que 2 queries com union agrupando por a e a,b
    - CUBE: ```SELECT A,B,C, SUM(D) FROM T1 GROUP BY A, B, WITH CUBE ```
      - O hive criará todas as combinações de group by, semelhante ao grouping sets
    - ROLLUP: Agrupa em hierarquia considerando a ordem em que as colunas foram listadas, gerando GROUPING SETS = (a,b,c),(a,b),a, ())
