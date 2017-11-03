---
title: Utilize os modelos do Azure para criar HDInsight e Data Lake Store | Microsoft Docs
description: Utilizar modelos Azure Resource Manager para criar e utilizar clusters do HDInsight com o Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: f6121a447acec69d354f5c1fbc7499561f471197
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Criar um cluster do HDInsight com o Data Lake Store utilizando o modelo Azure Resource Manager
> [!div class="op_single_selector"]
> * [Utilizar o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Através do PowerShell (para armazenamento de predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilizar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Com o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como utilizar o Azure PowerShell para configurar um cluster do HDInsight com o Azure Data Lake Store, **como armazenamento adicional**.

Para tipos de cluster suportadas, a Data Lake Store ser utilizado como um armazenamento de predefinido ou uma conta de armazenamento adicional. Quando o Data Lake Store é utilizado como armazenamento adicional, a conta do storage predefinida para os clusters continuarão a estar Blobs de armazenamento do Azure (WASB) e os ficheiros relacionados com o cluster (por exemplo, os registos, etc.) ainda são escritos para o armazenamento de predefinido, enquanto os dados que pretende processar podem ser armazenados numa conta do Data Lake Store. Utilizar o Data Lake Store como uma conta de armazenamento adicional não impacto no desempenho ou a capacidade de leitura/escrita para o armazenamento do cluster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Utilizar o Data Lake Store para armazenamento de cluster do HDInsight

Seguem-se algumas considerações importantes para utilizar o HDInsight com o Data Lake Store:

* Opção para criar clusters do HDInsight com acesso ao Data Lake Store, como armazenamento de predefinição está disponível para o HDInsight versão 3.5 e 3.6.

* Opção para criar clusters do HDInsight com acesso ao Data Lake Store, como é disponível para as versões do HDInsight 3.2, 3.4, 3.5 e 3.6 armazenamento adicional.

Neste artigo, vamos aprovisionar um cluster de Hadoop com o Data Lake Store como armazenamento adicional. Para obter instruções sobre como criar um cluster de Hadoop com o Data Lake Store, como armazenamento de predefinido, consulte [criar um cluster do HDInsight com o Data Lake Store utilizando o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* **Principal de serviço de diretório do Azure Active Directory**. Os passos neste tutorial fornecem instruções sobre como criar um principal de serviço no Azure AD. No entanto, tem de ser um administrador do Azure AD para poder criar um principal de serviço. Se for um administrador do Azure AD, pode ignorar este pré-requisito e continue com o tutorial.

    **Se não for um administrador do Azure AD**, não será capaz de executar os passos necessários para criar um principal de serviço. Nesse caso, o administrador do Azure AD tem primeiro de criar um principal de serviço antes de poder criar um cluster do HDInsight com o Data Lake Store. Além disso, o principal de serviço tem de ser criado utilizando um certificado, conforme descrito em [criar um principal de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Criar um cluster do HDInsight com o Azure Data Lake Store
O modelo do Resource Manager e os pré-requisitos para utilizar o modelo, estão disponíveis no GitHub em [implementar um cluster do HDInsight com Linux com o novo Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Siga as instruções fornecidas nesta hiperligação para criar um cluster do HDInsight com o Azure Data Lake Store, como o armazenamento adicional.

As instruções apresentadas em ligação mencionado necessitam do PowerShell. Antes de começar com estas instruções, certifique-se de que a sua conta do Azure. Do ambiente de trabalho, abra uma nova janela do Azure PowerShell e introduza os seguintes fragmentos. Quando lhe for pedido para iniciar sessão, certifique-se de que o faz como um dos admininistradores/proprietário da subscrição:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Carregar dados de exemplo para o Azure Data Lake Store
O modelo do Resource Manager cria uma nova conta de Data Lake Store e associa-o cluster do HDInsight. Agora tem de carregar alguns dados de exemplo para o Data Lake Store. Precisará destes dados mais tarde no tutorial para executar tarefas de um cluster do HDInsight que acedem aos dados no Data Lake Store. Para obter instruções sobre como carregar dados, consulte [carregar um ficheiro para o Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Definir ACLs relevantes nos dados de exemplo
Para certificar-se de que os dados de exemplo que carregar são acessíveis a partir do cluster do HDInsight, tem de garantir que a aplicação do Azure AD que é utilizada para estabelecer a identidade entre o cluster do HDInsight e a Data Lake Store tem acesso ao ficheiro/pasta que está a tentar aceder. Para tal, execute os seguintes passos.

1. Localize o nome da aplicação do Azure AD que está associado ao cluster do HDInsight e a Data Lake Store. Uma forma de consultar para o nome é abrir o painel de cluster do HDInsight que criou utilizando o modelo do Resource Manager, clique em de **identidade AAD do Cluster** separador e procure o valor de **nome a apresentar do Principal de serviço**.
2. Agora, fornecem acesso a esta aplicação do Azure AD no ficheiro/pasta que pretende aceder a partir do cluster do HDInsight. Para definir as ACLs corretas no ficheiro/pasta no Data Lake Store, consulte [proteger dados no Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Executar tarefas de teste no cluster do HDInsight ao utilizar o Data Lake Store
Depois de configurar um cluster do HDInsight, pode executar tarefas de teste no cluster para testar que o cluster do HDInsight pode aceder ao Data Lake Store. Para tal, iremos executar uma tarefa do Hive de exemplo que cria uma tabela utilizando os dados de exemplo que carregou anteriormente para o Data Lake Store.

Nesta secção, irá SSH para um cluster do HDInsight com Linux e execute a uma consulta do Hive de exemplo. Se estiver a utilizar um cliente Windows, recomendamos que utilize **PuTTY**, que podem ser transferidos do [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como utilizar o PuTTY, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Assim que estiver ligado, inicie a CLI de ramo de registo utilizando o seguinte comando:

   ```
   hive
   ```
2. Com a CLI do AZURE, introduza as seguintes instruções para criar uma nova tabela com o nome **veículos** utilizando os dados de exemplo no Data Lake Store:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Deverá ver um resultado semelhante ao seguinte:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-store-using-hdfs-commands"></a>Acesso Data Lake Store utilizando comandos HDFS
Assim que tiver configurado o cluster do HDInsight ao utilizar o Data Lake Store, pode utilizar os comandos da shell HDFS para aceder ao arquivo.

Nesta secção, irá SSH no Linux HDInsight cluster e executar os comandos HDFS. Se estiver a utilizar um cliente Windows, recomendamos que utilize **PuTTY**, que podem ser transferidos do [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como utilizar o PuTTY, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Assim que estiver ligado, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros no Data Lake Store.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Isto deve listar o ficheiro que carregou anteriormente para o Data Lake Store.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para o Data Lake Store e, em seguida, utilize `hdfs dfs -ls` para verificar se os ficheiros foram carregados com êxito.


## <a name="next-steps"></a>Passos seguintes
* [Copiar dados de Blobs de armazenamento do Azure para o Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
