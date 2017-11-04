---
title: Carregar dados para tarefas do Hadoop no HDInsight | Microsoft Docs
description: Saiba como carregar e aceder a dados para tarefas do Hadoop no HDInsight utilizando a CLI do Azure, o Explorador de armazenamento do Azure, Azure PowerShell, a linha de comandos do Hadoop ou Sqoop.
keywords: etl hadoop, ao obter dados no hadoop, hadoop carregar dados
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 9cdf41bd5c15abca3570a08584dc0958497848a7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Carregue dados para tarefas Hadoop no HDInsight

O Azure HDInsight fornece um sistema de ficheiros do Hadoop distribuído de completo (HDFS) através de armazenamento do Azure e o Azure Data Lake Store. Armazenamento do Azure e a Data lake Store foram concebidos para fornecer uma experiência totalmente integrada aos clientes como uma extensão HDFS. Se ativar o conjunto completo de componentes do ecossistema do Hadoop para operar diretamente em dados que gere. Armazenamento do Azure e a Data Lake Store são os sistemas de ficheiros distintos que estão otimizados para armazenamento de dados e cálculos nesses dados. Para obter informações sobre as vantagens da utilização do armazenamento do Azure, consulte [utilizar o Storage do Azure com o HDInsight] [ hdinsight-storage] e [utilize Data Lake Store com o HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha em atenção os seguintes requisitos:

* Um cluster do Azure HDInsight. Para obter instruções, consulte [introdução ao Azure HDInsight] [ hdinsight-get-started] ou [criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Dados de conhecimento dos dois seguintes artigos:

    - [Utilizar o armazenamento do Azure com o HDInsight][hdinsight-storage]
    - [Utilizar o Data Lake Store com o HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Carregar dados para o Storage do Azure

### <a name="command-line-utilities"></a>Utilitários da linha de comandos
A Microsoft fornece os seguintes utilitários para trabalhar com o Storage do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interface de linha de comandos do Azure][azurecli] |✔ |✔ |✔ |
| [O Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Comando de Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Enquanto a CLI do Azure, o Azure PowerShell e o AzCopy podem todos ser utilizado a partir do Azure fora, o comando de Hadoop só está disponível no cluster do HDInsight. E o comando só permite o carregamento de dados do sistema de ficheiros local para o armazenamento do Azure.
>
>

#### <a id="xplatcli"></a>CLI do Azure
A CLI do Azure é uma ferramenta de plataforma que permite-lhe gerir os serviços do Azure. Utilize os seguintes passos para carregar dados para o Storage do Azure:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalar e configurar a CLI do Azure para Mac, Linux e Windows](../cli-install-nodejs.md).
2. Abra uma linha de comandos, bash ou outro shell e utilize o seguinte para autenticar a sua subscrição do Azure.

    ```cli
    azure login
    ```

    Quando lhe for pedido, introduza o nome de utilizador e palavra-passe para a sua subscrição.
3. Introduza o seguinte comando para listar as contas de armazenamento para a sua subscrição:

    ```cli
    azure storage account list
    ```

4. Selecione a conta de armazenamento que contém o blob que pretende trabalhar e, em seguida, utilize o seguinte comando para obter a chave para esta conta:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Este comando devolve o **primário** e **secundário** chaves. Copiar o **primário** valor de chave, porque irá ser utilizada nos passos.
5. Utilize o seguinte comando para obter uma lista de contentores de BLOBs numa conta de armazenamento:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Utilize os seguintes comandos para carregar e transferir ficheiros para o blob:

   * Para carregar um ficheiro:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Para transferir um ficheiro:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Se sempre trabalhar com a mesma conta de armazenamento, pode definir as seguintes variáveis de ambiente em vez de especificar a conta e chave para cada comando:
>
> * **AZURE\_armazenamento\_conta**: O nome da conta de armazenamento
> * **AZURE\_armazenamento\_acesso\_chave**: A chave de conta de armazenamento
>
>

#### <a id="powershell"></a>O Azure PowerShell
O Azure PowerShell é um ambiente de script que pode utilizar para controlar e automatizar a implementação e gestão das cargas de trabalho no Azure. Para obter informações sobre como configurar a sua estação de trabalho para executar o Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Carregar um ficheiro local para o Storage do Azure**

1. Abra a consola do PowerShell do Azure com as instruções na [instalar e configurar o Azure PowerShell](/powershell/azure/overview).
2. Defina os valores das variáveis de cinco pela primeira vez no seguinte script:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Cole o script a consola do Azure PowerShell para executá-la para copiar o ficheiro.

Por exemplo scripts do PowerShell criadas para trabalhar com o HDInsight, consulte [as ferramentas do HDInsight](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
O AzCopy é uma ferramenta da linha de comandos que foi concebida para simplificar a tarefa de transferência de dados dentro e fora de uma conta de armazenamento do Azure. Pode utilizá-la como uma ferramenta autónoma ou incorporar esta ferramenta numa aplicação existente. [Transferir o AzCopy][azure-azcopy-download].

A sintaxe do AzCopy é:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Para obter mais informações, consulte [AzCopy - carregar/transferir ficheiros para Blobs do Azure][azure-azcopy].

Azcopy na pré-visualização de Linux está disponível.  Consulte [anunciar AzCopy na pré-visualização do Linux](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Linha de comandos de Hadoop
A linha de comandos do Hadoop só é útil para armazenar dados no blob storage do Azure, quando os dados já estão presentes no nó principal do cluster.

Para utilizar o comando de Hadoop, tem de ligar pela primeira vez ao headnode utilizando um dos seguintes métodos:

* **HDInsight baseado em Windows**: [estabeleçam ligação através do ambiente de trabalho remoto](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight baseado em Linux**: ligar utilizando [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Assim que estiver ligado, pode utilizar a sintaxe seguinte para carregar um ficheiro para o armazenamento.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Porque o sistema de ficheiros para o HDInsight no armazenamento do Azure, /example/data.txt é, na verdade, no armazenamento do Azure. Também pode consultar o ficheiro como:

    wasb:///example/data/data.txt

ou

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obter uma lista de outro Hadoop os comandos que funcionam com ficheiros, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> Em clusters de HBase, a predefinição tamanho do bloco utilizado quando a escrita de dados é 256 KB. Enquanto funciona bem quando utilizar APIs de HBase ou REST APIs, utilizando o `hadoop` ou `hdfs dfs` comandos para escrever dados superiores ~ 12 GB resulta num erro. Para obter mais informações, consulte o [exceção de armazenamento para escrita no blob](#storageexception) secção deste artigo.
>
>

### <a name="graphical-clients"></a>Clientes gráficos
Também existem várias aplicações que fornecem uma interface gráfica para trabalhar com o Storage do Azure. A tabela seguinte é uma lista de algumas destas aplicações:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas do Microsoft Visual Studio para o HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Explorador do Armazenamento do Azure](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Armazenamento de nuvem Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Explorador do Azure](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Ferramentas do Visual Studio para o HDInsight
Para obter mais informações, consulte [navegar nos recursos ligados](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

#### <a id="storageexplorer"></a>Explorador de armazenamento do Azure
*Explorador de armazenamento do Azure* é uma ferramenta útil para inspecionar e alterar os dados em blobs. É uma ferramenta de origem livre, abra que pode ser transferida a partir do [http://storageexplorer.com/](http://storageexplorer.com/). O código de origem está disponível no bem esta ligação.

Antes de utilizar a ferramenta, tem de conhecer a chave de conta e o nome da conta de armazenamento do Azure. Para obter instruções sobre como obter estas informações, consulte o "como: ver, copiar e armazenamento voltar a gerar chaves de acesso" secção [criar, gerir ou eliminar uma conta de armazenamento][azure-create-storage-account].

1. Execute o Explorador de armazenamento do Azure. Se for a primeira tiver executado o Explorador de armazenamento, se lhe for pedido para o **nome da conta de armazenamento da _** e **chave da conta de armazenamento**. Se tem de executá-la antes, utilize o **adicionar** botão para adicionar um novo nome da conta de armazenamento e a chave.

    Introduza o nome e a chave para a conta de armazenamento utilizada pelo cluster do HDInsight e, em seguida, selecione **Guardar & OPEN**.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]
2. Na lista de contentores para a esquerda da interface, clique no nome do contentor que está associado ao cluster do HDInsight. Por predefinição, este é o nome do cluster do HDInsight, mas pode ser diferente se introduziu um nome específico ao criar o cluster.
3. Na barra de ferramentas, selecione o ícone de carregamento.

    ![Barra de ferramentas com ícone de carregamento realçado](./media/hdinsight-upload-data/toolbar.png)
4. Especifique um ficheiro para carregar e, em seguida, clique em **abra**. Quando lhe for pedido, selecione **carregar** carregar o ficheiro na raiz do contentor de armazenamento. Se pretender carregar o ficheiro para um caminho específico, introduza o caminho no **destino** campo e, em seguida, selecione **carregar**.

    ![Caixa de diálogo de carregamento de ficheiros](./media/hdinsight-upload-data/fileupload.png)

    Depois do ficheiro concluiu o carregamento, pode utilizá-lo das tarefas no cluster do HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Armazenamento do Azure de montagem de unidade Local
Consulte [armazenamento do Azure de montagem de unidade Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Carregar utilizando os serviços
#### <a name="azure-data-factory"></a>Azure Data Factory
O serviço do Azure Data Factory é um serviço completamente gerido para composição de serviços de movimento de dados, processamento de dados e armazenamento de dados para dados simplificada, escalável e fiável pipelines de produção.

Pode ser utilizado o Azure Data Factory para mover dados para o Storage do Azure ou para criar pipelines de dados que utilizam o HDInsight funcionalidades, tais como o Hive e do Pig diretamente.

Para obter mais informações, consulte o [documentação do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>O Apache Sqoop
Sqoop é uma ferramenta concebida para transferir dados entre o Hadoop e bases de dados relacionais. Pode utilizá-lo para importar dados a partir de um sistema de gestão de base de dados relacional (RDBMS), tal como o SQL Server, MySQL ou Oracle para o sistema de ficheiros distribuído do Hadoop (HDFS), transformar os dados no Hadoop com o MapReduce ou Hive e, em seguida, exportar os dados de volta para um RDBMS.

Para obter mais informações, consulte [utilize Sqoop com o HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>SDKs de desenvolvimento
Armazenamento do Azure também pode ser acedido através de um SDK do Azure de linguagens de programação seguintes:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [transferências do Azure](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>Resolução de problemas
#### <a id="storageexception"></a>Exceção de armazenamento para escrita no blob
**Sintomas**: ao utilizar o `hadoop` ou `hdfs dfs` comandos para escrever ficheiros que estão ~ 12 GB ou superior num HBase cluster, poderá encontrar o seguinte erro:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Causa**: HBase no HDInsight clusters predefinido para um tamanho de bloco de 256 KB ao escrever para o armazenamento do Azure. Enquanto funciona para as APIs do HBase ou REST APIs, que resulta num erro ao utilizar o `hadoop` ou `hdfs dfs` utilitários da linha de comandos.

**Resolução**: Utilize `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Para fazer isto numa base por utilização, utilizando o `-D` parâmetro. O comando seguinte é um exemplo de utilização deste parâmetro com o `hadoop` comando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Também pode aumentar o valor de `fs.azure.write.request.size` globalmente utilizando Ambari. Os passos seguintes podem ser utilizados para alterar o valor na IU da Web do Ambari:

1. No seu browser, aceda à IU da Web do Ambari para o cluster. Este é https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster.

    Quando lhe for pedido, introduza o nome de administrador e a palavra-passe para o cluster.
2. Do lado esquerdo do ecrã, selecione **HDFS**e, em seguida, selecione o **folhas** separador.
3. No **filtro...**  campo, introduza `fs.azure.write.request.size`. Esta ação apresenta o campo e o valor atual no meio da página.
4. Altere o valor de 262144 (256KB) para o novo valor. Por exemplo, 4194304 (4MB).

![Imagem da alteração do valor através da IU da Web do Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para obter mais informações sobre a utilização do Ambari, consulte [gerir clusters do HDInsight utilizando a IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Passos seguintes
Agora que sabe como carregar dados para o HDInsight, leia os seguintes artigos para saber como executar uma análise:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]
* [Submeter tarefas do Hadoop através de programação][hdinsight-submit-jobs]
* [Use Hive with HDInsight (Utilizar o Hive com o HDInsight)][hdinsight-use-hive]
* [Use Pig with HDInsight (Utilizar o Pig com o HDInsight)][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
