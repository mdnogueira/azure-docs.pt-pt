## O que é Blob Storage?
O Blob Storage do Azure é um serviço para armazenar grandes quantidades de dados de objetos não estruturados, como texto ou dados binários, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Pode utilizar o armazenamento de Blobs para expor publicamente os dados ao mundo ou para armazenar dados da aplicação em privado.

Utilizações comuns do armazenamento de Blobs:

* Entrega de imagens ou documentos diretamente a um browser
* Armazenamento de ficheiros para acesso distribuído
* Transmissão de áudio e vídeo
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure

## Conceitos do serviço Blob
O serviço Blob contém os seguintes componentes:

![Blob1][Blob1]

* **Conta de Armazenamento:** todos os acessos ao Storage do Azure são efetuados através de uma conta de armazenamento. Esta conta de armazenamento pode ser uma **Conta de armazenamento para fins gerais** ou uma **Conta de armazenamento de Blobs**, que é especializada para armazenar objetos/blobs. Para obter mais informações sobre as contas de armazenamento, veja [Conta de armazenamento do Azure](../articles/storage/storage-create-storage-account.md).
* **Contentor:** um contentor fornece um agrupamento de um conjunto de blobs. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs. Tenha em atenção que o nome do contentor tem de ser em minúsculas.
* **Blob:** um ficheiro de qualquer tipo e tamanho. O Storage do Azure oferece três tipos de blobs: blobs de blocos, blobs de páginas e blobs de acréscimo.
  
    Os *blobs de blocos* são ideais para armazenar ficheiros de texto ou binários, como documentos e ficheiros multimédia. Os *blobs de acréscimo* são semelhantes aos blobs de blocos na medida em que são constituídos por blocos, mas estão otimizados para operações de acréscimo, sendo pois úteis para cenários de registo. Um único blob de blocos ou de acréscimo pode conter até 50 000 blocos com um máximo de 4 MB cada, para um tamanho total ligeiramente acima de 195 GB (4 MB X 50 000).
  
    Os *blobs de páginas* podem ter até 1 TB de tamanho e são mais eficientes para operações de leitura/escrita frequentes. As Virtual Machines do Azure utilizam blobs de páginas como discos de dados e SO.
  
    Para obter detalhes sobre os nomes dos contentores e dos blobs, veja [Naming and Referencing Containers, Blobs, and Metadata (Nomenclatura e Referência de Contentores, Blobs e Metadados)](https://msdn.microsoft.com/library/azure/dd135715.aspx).

[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg



<!--HONumber=sep16_HO2-->


