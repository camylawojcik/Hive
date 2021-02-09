HDFS
- Slplit do arquivo a ser armazenado em blocos ou chunks, tipicamente de 64MB à 128MB
- Armazena cada bloco em um DataNode
- Replica cada bloco para 3 nodes (default)
  - Fault Tolerance
- Hadoop consegue detectar quando um bloco de dados não foi replicado, fazer a cópia e replicar
