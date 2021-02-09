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
  
