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
  
#### Introduction to Hive
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

      
    - External Tables: 
    
    
