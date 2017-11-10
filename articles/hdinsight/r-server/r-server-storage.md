---
title: "Soluções de armazenamento do Azure para o servidor R no HDInsight - Azure | Microsoft Docs"
description: "Saiba mais sobre as opções de armazenamento diferentes disponíveis para os utilizadores com o servidor R no HDInsight"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: aafcc818af4c6e5d141d3633b31b913802a21752
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>Soluções de armazenamento do Azure para o servidor R no HDInsight

Microsoft R Server no HDInsight tem uma variedade de soluções de armazenamento para manter os dados, código ou objetos que contêm os resultados da análise. Estas incluem as seguintes opções:

- [Blob do Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)
- [File storage do Azure](https://azure.microsoft.com/services/storage/files/)

Também tem a opção de aceder a várias contas de armazenamento do Azure ou contentores com o cluster HDI. File storage do Azure é uma opção de armazenamento de dados conveniente para utilização no nó de extremidade que lhe permite montar uma partilha de ficheiros de armazenamento do Azure, por exemplo, o sistema de ficheiros do Linux. Mas partilhas de ficheiros do Azure podem ser montadas e utilizadas por qualquer sistema com um SO suportado, tais como o Windows ou Linux. 

Quando cria um cluster do Hadoop no HDInsight, especificou um **storage do Azure** conta ou um **arquivo Data Lake**. Um contentor de armazenamento específico da conta de que contém o sistema de ficheiros para o cluster que criou (por exemplo, o sistema de ficheiros distribuído Hadoop). Para obter mais informações e orientações, consulte:

- [Utilizar o armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Utilizar o Data Lake Store com o Azure HDInsight clusters](../hdinsight-hadoop-use-data-lake-store.md). 

Para obter mais informações sobre as soluções de armazenamento do Azure, consulte [introdução ao Storage do Microsoft Azure](../../storage/common/storage-introduction.md). 

Para obter orientações sobre a selecionando a opção de armazenamento mais adequada para utilizar para o seu cenário, consulte [decidir quando deve utilizar Blobs do Azure, ficheiros do Azure ou os discos de dados do Azure](../../storage/common/storage-decide-blobs-files-disks.md) 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>Utilizar contas de armazenamento de Blobs do Azure com o servidor de R

Se necessário, pode aceder a várias contas de armazenamento do Azure ou contentores com o cluster HDI. Para tal, tem de especificar as contas de armazenamento adicional na IU ao criar o cluster e, em seguida, siga estes passos para utilizá-los com o servidor R.

> [!WARNING]
> Por motivos de desempenho, o cluster do HDInsight é criado no mesmo centro de dados como a conta de armazenamento primário que especificar. Não é suportada a utilização de uma conta de armazenamento numa localização diferente que o cluster do HDInsight.

1. Criar um cluster do HDInsight com um nome de conta de armazenamento de **storage1** e um contentor predefinido denominada **container1**.
2. Especifique uma conta de armazenamento adicional chamada **storage2**.  
3. Copie o ficheiro mycsv.csv para o diretório de /share e executar uma análise em que o ficheiro.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. No código do R, defina o nó de nome **predefinido,** e definir o diretório e o ficheiro a processar.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Todas as referências de ficheiro e diretório apontam para a conta de armazenamento wasb://container1@storage1.blob.core.windows.net. Este é o **predefinido a conta de armazenamento** que associado ao cluster do HDInsight.

Agora, suponha que pretende processar um ficheiro chamado mySpecial.csv que está localizado no /private diretório de **container2** no **storage2**.

No seu código de R, ponto de referência de nome de nó para o **storage2** conta de armazenamento.


    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Todas as referências de ficheiro e diretório agora apontam para a conta de armazenamento wasb://container2@storage2.blob.core.windows.net. Este é o **nome de nó** que tiver especificado.

Tem de configurar o /user RevoShare/<SSH username> diretório **storage2** da seguinte forma:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>Utilizar um arquivo Azure Data Lake com o servidor de R

Para utilizar arquivos de Data Lake com a sua conta do HDInsight, terá de conceder o acesso de cluster a cada Azure Data Lake store que pretende utilizar. Para obter instruções sobre como utilizar o portal do Azure para criar um cluster do HDInsight com uma conta do Azure Data Lake Store, como o armazenamento de predefinido ou como um arquivo de adicional, consulte [criar um cluster do HDInsight com o Data Lake Store utilizando o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Em seguida, utilizar o arquivo no R script muito como fez uma conta do storage do Azure secundária, tal como descrito no procedimento anterior.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Adicionar acesso de cluster para os arquivos de Azure Data Lake
Aceder a um arquivo Data Lake, utilizando um Principal de serviço do Azure Active Directory (Azure AD) associado ao cluster do HDInsight.

Para adicionar um Principal de serviço do Azure AD:

1. Quando criar o cluster do HDInsight, selecione **identidade do AAD Cluster** do **origem de dados** separador.

2. No **identidade do AAD Cluster** caixa de diálogo em **selecione Principal de serviço do AD**, selecione **criar nova**.

Depois de dar um nome de Principal de serviço e criar uma palavra-passe para o mesmo, clique em **gerir o acesso ADLS** para associar o Principal de serviço com os arquivos de Data Lake.

Também é possível adicionar o acesso do cluster para um ou mais arquivos de Data Lake seguir a criação do cluster. Abra a entrada de portal do Azure para um arquivo Data Lake e aceda à **Explorador de dados > acesso > Adicionar**. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>Como aceder à loja do Data Lake do servidor R

Depois de concedeu acesso a um arquivo Data Lake, pode utilizar o arquivo do servidor R no HDInsight a forma como o faria com uma conta do storage do Azure secundário. A única diferença é que o prefixo **wasb: / /** alterações **adl: / /** da seguinte forma:


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


Os comandos seguintes são utilizados para configurar a conta de armazenamento do Data Lake com o diretório de RevoShare e adicione o ficheiro. csv de exemplo do exemplo anterior:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>Utilizar o File storage do Azure com o servidor R

Há também uma opção de armazenamento de dados conveniente para utilização no nó de extremidade chamado [ficheiros do Azure] ((https://azure.microsoft.com/services/storage/files/). Permite-lhe montar uma partilha de ficheiros de armazenamento do Azure para o sistema de ficheiros do Linux. Esta opção pode ser útil para armazenar ficheiros de dados, os scripts de R e objetos de resultado que poderão ser necessário posteriormente, especialmente quando faz sentido utilizam o sistema de ficheiros nativo no nó de extremidade em vez de HDFS. 

Uma vantagem principal de ficheiros do Azure é que as partilhas de ficheiros podem ser montadas e utilizadas por qualquer sistema com um SO suportado, tais como o Windows ou Linux. Por exemplo, pode ser utilizado por outro cluster de HDInsight foi, ou alguém na sua equipa, pela VM do Azure ou mesmo um sistema local. Para obter mais informações, consulte:

- [How to use Azure File Storage with Linux (Como utilizar o Armazenamento de Ficheiros do Azure com o Linux)](../../storage/files/storage-how-to-use-files-linux.md)
- [Como utilizar o File storage do Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Passos seguintes

Agora que compreende as opções de armazenamento do Azure, utilize as seguintes ligações para detetar formas de obter as tarefas de ciência de dados feitas com o servidor R no HDInsight.

* [Descrição geral do servidor R no HDInsight](r-server-overview.md)
* [Introdução ao servidor R no Hadoop](r-server-get-started.md)
* [Compute context options for R Server on HDInsight (Opções do contexto de cálculo para o R Server no HDInsight)](r-server-compute-contexts.md)

