---
title: "Computação ambientes suportados pelo Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre os ambientes de computação que pode utilizar em pipelines do Azure Data Factory (por exemplo, o Azure HDInsight) para dados de transformação ou processo."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 1547b5c3a5c629b85ff5fa9de6b39b25531d9ec9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Suportado pelo Azure Data Factory de ambientes de computação
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [V2 serviços ligados de computação](../compute-linked-services.md).

Este artigo explica vários ambientes de computação que pode utilizar para o processo ou de transformação de dados. Também fornece detalhes sobre as configurações diferentes (a pedido vs traga o seu próprio) suportados pela fábrica de dados, quando configurar serviços ligados de ligação estes computação ambientes para um Azure data factory.

A tabela seguinte fornece uma lista de ambientes de computação suportada pela fábrica de dados e as atividades que podem ser executadas nos mesmos. 

| Ambiente de computação                      | atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster do HDInsight a pedido](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster do HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md) |
| [O Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](data-factory-azure-ml-batch-execution-activity.md) |
| [Análise do Azure Data Lake](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [SQL do Azure](#azure-sql-linked-service), [do armazém de dados SQL do Azure](#azure-sql-data-warehouse-linked-service), [do SQL Server](#sql-server-linked-service) | [Procedimento Armazenado](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versões suportadas do HDInsight no Azure Data Factory
O Azure HDInsight suporta várias versões de cluster de Hadoop que podem ser implementadas em qualquer altura. Cada opção de versão cria uma versão específica da distribuição Hortonworks Data Platform (HDP) e um conjunto de componentes que estão contidas dentro dessa distribuição. Microsoft mantém a atualizar a lista de versões suportadas do HDInsight para fornecer mais recentes componentes ecossistema do Hadoop e correções. Para obter informações detalhadas, consulte [HDInsight versões](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> O HDInsight baseado em Linux versão 3.3 foi reformada no dia 31 de Julho de 2017. Os clientes do Data Factory v1 serviços ligados de HDInsight a pedido foram dada até 15 de Dezembro de 2017, testar e atualizar para uma versão posterior do HDInsight. O HDInsight baseado em Windows será descontinuado no 31 de Julho de 2018.
>
> 

**O que irá ocorrer após a data de retirada** 

Após 15 de Dezembro de 2017:

- Já não será possível criar o HDInsight baseado em Linux versão 3.3 (ou versões anteriores) clusters com o serviço ligado do HDInsight a pedido no Azure Data Factory v1. 

- Se o [osType e/ou propriedade Version](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) não especificados explicitamente no existentes do Azure Data Factory v1 JSON de serviço ligado HDInsight a pedido as definições, o valor predefinido será alterado de **versão = 3.1, osType = Windows** para **versão = 3.6, osType = Linux**.

Após a 31 de Julho de 2018:

- Já não será possível criar a qualquer versão de clusters do HDInsight baseados em Windows utilizando o serviço ligado do HDInsight a pedido no Azure Data Factory v1. 

 **Ações recomendadas** 

- Atualização do [osType e/ou propriedade Version](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) das definições de serviço ligado do HDInsight a pedido do Azure Data Factory v1 afetadas para o HDInsight baseado em Linux mais recente versões (HDInsight 3.6) para se certificar de podem utilizar o Hadoop mais recente componentes do ecossistema e correções. 
- Antes de testar a 15 de Dezembro de 2017 V1 do Azure Data Factory do Hive, Pig, MapReduce e Hadoop atividades que referenciam o serviço ligado afetados para se certificar de transmissão em fluxo sejam compatíveis com o novo *osType* e/ou  *Versão* valor predefinido (versão = 3.6, osType = Linux) ou a explícito HDInsight e a versão osType que estiver a atualizar. Para saber mais sobre a compatibilidade, reveja o [migrar de um cluster do HDInsight baseado em Windows para um cluster baseado em Linux](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) e [quais são os componentes do Hadoop e as versões disponíveis com o HDInsight?](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) páginas Web de documentação. 
- Defina explicitamente o osType Windows antes do dia 15 de Dezembro de 2017, se gostaria de continuar a utilizar o serviço ligado do Azure Data Factory a pedido v1On HDInsight para criar clusters do HDInsight baseados em Windows. No entanto, recomendamos a migrar para clusters do HDInsight baseado em Linux antes de 31 de Julho de 2018. 
- Atualize a definição de JSON de atividade do DotNet personalizado para utilizar um serviço ligado do Azure Batch em vez disso, se estiver a utilizar o serviço ligado do HDInsight a pedido para executar a atividade do Azure Data Factory v1DotNet personalizada. Saber mais sobre o [utilizar atividades personalizadas num pipeline do Azure DataFactory](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-use-custom-activities) página Web de documentação. 

>[!Note]
>Para os clientes que utilizam o existente traga seu próprio Cluster (BYOC) serviço ligado de HDInsight no Azure Data Factory v1 ou a quem estão a utilizar BYOC e a pedido HDInsightLinked serviço no Azure Data Factory v2, a versão mais recente suporta política ofAzure HDInsight clusters já é imposta, por conseguinte, é necessária nenhuma ação. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente de computação a pedido
Este tipo de configuração, o ambiente informático totalmente é gerido pelo serviço do Azure Data Factory. É automaticamente criada pelo serviço Data Factory antes de uma tarefa é submetida para processar dados e removida quando a tarefa estiver concluída. Pode criar um serviço ligado para o ambiente de computação a pedido, configurá-lo e controlar as definições do granulares para execução da tarefa, gestão de clusters e bootstrapping ações.

> [!NOTE]
> A configuração a pedido atualmente só é suportada para clusters do HDInsight do Azure.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço ligado do Azure HDInsight a pedido
O serviço do Azure Data Factory automaticamente pode criar um cluster do HDInsight baseado em Windows/Linux a pedido para processamento de dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName no JSON) associada com o cluster.

Tenha em atenção o seguinte **importante** serviço ligado de pontos de HDInsight a pedido:

* Não vir o cluster do HDInsight a pedido criado na sua subscrição do Azure. o serviço de Azure Data Factory gere o cluster do HDInsight a pedido em seu nome.
* Os registos para tarefas que são executadas num cluster do HDInsight a pedido são copiados para a conta de armazenamento associada com o cluster do HDInsight. Pode aceder a estes registos do portal do Azure no **detalhes da execução da atividade** painel. Consulte [monitorizar e gerir Pipelines](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes.
* São-lhe cobrados apenas para a hora quando o cluster do HDInsight está ativo e tarefas em execução.

> [!IMPORTANT]
> Normalmente demora **20 minutos** ou mais para aprovisionar um cluster do Azure HDInsight a pedido.
>
> 

### <a name="example"></a>Exemplo
O JSON seguinte define um serviço ligado do HDInsight baseado em Linux da pedido. O serviço Data Factory cria automaticamente um **baseado em Linux** cluster do HDInsight ao processar um setor de dados. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Para utilizar um cluster do HDInsight baseados em Windows, defina **osType** para **windows** ou não utilize a propriedade como o valor predefinido é: windows.  

> [!IMPORTANT]
> O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento. 
>
> À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | A propriedade de tipo deve ser definida como **HDInsightOnDemand**. | Sim      |
| ClusterSize                  | Número de nós de trabalho/dados do cluster. O cluster do HDInsight é criado com 2 nós principais, juntamente com o número de nós de trabalho que especificar para esta propriedade. Os nós são de tamanho Standard_D3 tem 4 núcleos, pelo que um cluster de nós de 4 trabalho demora 24 núcleos (4\*4 = 16 núcleos para nós de trabalho plus 2\*4 = 8 núcleos de nós principais). Consulte [clusters do Hadoop baseados em criar Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre a camada de Standard_D3. | Sim      |
| TimeToLive                   | O tempo de inatividade permitido para o cluster do HDInsight a pedido. Especifica o tempo durante o qual o cluster do HDInsight a pedido permanece alive após a conclusão de uma atividade executar se existirem outras tarefas ativas no cluster.<br/><br/>Por exemplo, se executar uma atividade demora 6 minutos e timetolive está definido para 5 minutos, o cluster permanece ativo durante 5 minutos após a execução de 6 minutos de atividade de processamento. Se executar de outra atividade é executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>Criar um cluster do HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo), por isso, utilize esta definição como necessários para melhorar o desempenho de uma fábrica de dados através da reutilização de um cluster do HDInsight a pedido.<br/><br/>Se definir o valor de timetolive para 0, o cluster é eliminado, assim que concluir a execução de atividade. Enquanto que, se definir um valor elevado, o cluster pode permanecer inativo, desnecessariamente, resultando em custos elevados. Por conseguinte, é importante que defina o valor adequado com base nas suas necessidades.<br/><br/>Se o valor da propriedade timetolive adequadamente estiver definido, pipelines vários podem partilhar a instância de cluster do HDInsight a pedido. | Sim      |
| Versão                      | Versão do cluster do HDInsight. O valor predefinido é 3.1 para o cluster do Windows e 3.2 para o cluster do Linux. | Não       |
| linkedServiceName            | Serviço ligado do Storage do Azure a ser utilizado pelo cluster a pedido para armazenar e processar dados. O cluster do HDInsight é criado na mesma região que esta conta de armazenamento do Azure.<p>Atualmente, não é possível criar um cluster do HDInsight a pedido que utiliza um Azure Data Lake Store, como o armazenamento. Se pretende armazenar os dados de resultado do HDInsight em processamento no Azure Data Lake Store, utilize uma atividade de cópia para copiar os dados de armazenamento de Blobs do Azure para o Azure Data Lake Store. </p> | Sim      |
| additionalLinkedServiceNames | Especifica a contas de armazenamento adicional para o HDInsight serviço ligado para que o serviço fábrica de dados pode registá-los em seu nome. Estas contas de armazenamento tem de ser na mesma região que o cluster do HDInsight, que é criado na mesma região que a conta de armazenamento especificada por linkedServiceName. | Não       |
| osType                       | Tipo de sistema operativo. Valores permitidos são: (predefinição) do Windows e Linux | Não       |
| hcatalogLinkedServiceName    | O nome do SQL do Azure ligado serviço que apontam para a base de dados do HCatalog. O cluster do HDInsight a pedido é criado utilizando a base de dados SQL do Azure como o metastore. | Não       |

#### <a name="additionallinkedservicenames-json-example"></a>exemplo JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriedades avançadas
Também pode especificar as seguintes propriedades para a configuração do cluster de HDInsight a pedido granular.

| Propriedade               | Descrição                              | Necessário |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração de núcleos (tal como no Core-site.xml) para o cluster do HDInsight a ser criado. | Não       |
| hBaseConfiguration     | Especifica os parâmetros de configuração de HBase (hbase-site.xml) para o cluster do HDInsight. | Não       |
| hdfsConfiguration      | Especifica os parâmetros de configuração do HDFS (hdfs site.xml) para o cluster do HDInsight. | Não       |
| hiveConfiguration      | Especifica os parâmetros de configuração do ramo de registo (hive site.xml) para o cluster do HDInsight. | Não       |
| mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred site.xml) para o cluster do HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração do Oozie (oozie site.xml) para o cluster do HDInsight. | Não       |
| stormConfiguration     | Especifica os parâmetros de configuração de Storm (storm-site.xml) para o cluster do HDInsight. | Não       |
| yarnConfiguration      | Especifica os parâmetros de configuração Yarn (yarn site.xml) para o cluster do HDInsight. | Não       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo – configuração de cluster do HDInsight a pedido com propriedades avançadas

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Tamanhos de nó
Pode especificar os tamanhos de head, dados e nós de zookeeper utilizando as seguintes propriedades: 

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Especifica o tamanho de nó principal. O valor predefinido é: Standard_D3. Consulte o **especificando os tamanhos de nó** secção para obter detalhes. | Não       |
| dataNodeSize      | Especifica o tamanho do nó de dados. O valor predefinido é: Standard_D3. | Não       |
| zookeeperNodeSize | Especifica o tamanho do nó Zoo Keeper. O valor predefinido é: Standard_D3. | Não       |

#### <a name="specifying-node-sizes"></a>Especificar os tamanhos de nó
Consulte o [tamanhos das Virtual Machines](../../virtual-machines/linux/sizes.md) artigo para valores de cadeia tem de especificar para as propriedades mencionadas na secção anterior. Os valores têm de estar em conformidade com a **CMDLETs & APIS** referenciado no artigo. Como pode ver no artigo, o nó de dados de tamanho grande (predefinição) tem 7 GB de memória, que pode não ser suficientemente boa para o seu cenário. 

Se pretender criar D4 um tamanho de nós principais e nós de trabalho, especifique **Standard_D4** como o valor das propriedades headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se especificar um valor incorreto para estas propriedades, poderá receber o seguinte **erro:** Falha ao criar o cluster. Exceção: não foi possível concluir a operação de criação do cluster. A operação falhou com o código "400". Estado do cluster não concluído: "Erro". Mensagem: 'PreClusterCreationValidationFailure'. Quando receber este erro, certifique-se de que está a utilizar o **CMDLET & APIS** nome da tabela no [tamanhos das Virtual Machines](../../virtual-machines/linux/sizes.md) artigo.  

> [!NOTE]
> Fábrica de dados do Azure não suporta atualmente clusters do HDInsight através do Azure Data Lake Store, como arquivo primário. Utilize o Storage do Azure como armazenamento primário para clusters do HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Traga o seu ambiente de computação
Este tipo de configuração, os utilizadores podem registar um ambiente informático já existente como um serviço ligado no Factory de dados. O ambiente informático é gerido pelo utilizador e o serviço fábrica de dados utiliza-o para executar as atividades.

Este tipo de configuração é suportado para os ambientes de computação seguintes:

* O Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Base de dados SQL do Azure, o armazém de dados SQL do Azure, o SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço ligado de HDInsight do Azure
Pode criar um serviço ligado do Azure HDInsight para registar o seu próprio cluster do HDInsight com o Data Factory.

### <a name="example"></a>Exemplo

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | A propriedade de tipo deve ser definida como **HDInsight**. | Sim      |
| clusterUri        | O URI de cluster do HDInsight.        | Sim      |
| o nome de utilizador          | Especifique o nome do utilizador a ser utilizado para ligar a um cluster do HDInsight existente. | Sim      |
| palavra-passe          | Especifique a palavra-passe da conta de utilizador.   | Sim      |
| linkedServiceName | Nome do serviço ligado do Storage do Azure que se refere ao blob storage do Azure utilizado pelo HDInsight cluster. <p>Atualmente, não é possível especificar o que serviço para esta propriedade de ligado um Azure Data Lake Store. Se o cluster do HDInsight tem acesso ao Data Lake Store, pode aceder a dados no Azure Data Lake Store de scripts Hive/Pig. </p> | Sim      |

## <a name="azure-batch-linked-service"></a>Serviço ligado do Azure Batch
Pode criar um serviço ligado do Azure Batch para registar um conjunto do Batch de máquinas virtuais (VMs) a uma fábrica de dados. Pode executar atividades personalizadas do .NET utilizando o Azure Batch ou para o Azure HDInsight.

Consulte os seguintes tópicos se estiver familiarizado com o serviço Azure Batch:

* [Noções básicas de lote do Azure](../../batch/batch-technical-overview.md) para uma descrição geral do serviço Azure Batch.
* [Novo AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet para criar uma conta do Azure Batch (ou) [portal do Azure](../../batch/batch-account-create-portal.md) para criar a conta do Azure Batch através do portal do Azure. Consulte [utilizando o PowerShell para gerir a conta do Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) tópico para obter instruções detalhadas sobre como utilizar o cmdlet.
* [Novo-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet para criar um conjunto do Batch do Azure.

### <a name="example"></a>Exemplo

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Acrescentar "**.\< nome de região\>**"para o nome da sua conta do batch para o **accountName** propriedade. Exemplo:

```json
"accountName": "mybatchaccount.eastus"
```

Outra opção consiste em fornecer o ponto final batchUri conforme mostrado no seguinte exemplo:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | A propriedade de tipo deve ser definida como **AzureBatch**. | Sim      |
| accountName       | Nome da conta do Azure Batch.         | Sim      |
| accessKey         | Chave de acesso para a conta do Azure Batch.  | Sim      |
| poolName          | Nome do conjunto de máquinas virtuais.    | Sim      |
| linkedServiceName | Nome do Storage do Azure ligado serviço associado este serviço ligado do Azure Batch. Este serviço ligado é utilizado para os ficheiros de testes necessários para executar a atividade e armazenar os registos de execução da atividade. | Sim      |

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado do Azure Machine Learning
Criar um serviço ligado do Azure Machine Learning para registar um lote de Machine Learning a classificação de ponto final de uma fábrica de dados.

### <a name="example"></a>Exemplo

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Propriedades
| Propriedade   | Descrição                              | Necessário |
| ---------- | ---------------------------------------- | -------- |
| Tipo       | A propriedade de tipo deve ser definida como: **AzureML**. | Sim      |
| mlEndpoint | O URL de classificação de lote.                   | Sim      |
| ApiKey     | API o modelo de área de trabalho publicado.     | Sim      |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado do Azure Data Lake Analytics
Criar um **Azure Data Lake Analytics** um Azure data factory do serviço de computação de serviço ligado para ligar um Azure Data Lake Analytics. A atividade U-SQL do Data Lake Analytics no pipeline refere-se a este serviço ligado. 

A tabela seguinte fornece descrições para as propriedades genéricas utilizadas na definição JSON. Ainda pode escolher entre o principal de serviço e a autenticação de credenciais do utilizador.

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **tipo**                 | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| **accountName**          | Nome de conta do Azure Data Lake Analytics.  | Sim                                      |
| **dataLakeAnalyticsUri** | URI do Azure Data Lake Analytics.           | Não                                       |
| **subscriptionId**       | Id de subscrição do Azure                    | Não (se não for especificado, a subscrição do data factory é utilizada). |
| **resourceGroupName**    | Nome do grupo de recursos do Azure                | Não (se não for especificado, grupo de recursos do data factory é utilizado). |

### <a name="service-principal-authentication-recommended"></a>Autenticação principal de serviço (recomendada)
Para utilizar a autenticação principal de serviço, registe uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder o acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação de serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do inquilino

Utilize a autenticação principal de serviço, especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique o ID de cliente. da aplicação     | Sim      |
| **servicePrincipalKey** | Especifique a chave da aplicação.           | Sim      |
| **inquilino**              | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Sim      |

**Exemplo: Autenticação principal do serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticação de credenciais de utilizador
Em alternativa, pode utilizar a autenticação de credencial de utilizador para o Data Lake Analytics especificando as seguintes propriedades:

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| **autorização** | Clique em de **autorizar** botão no Editor do Data Factory e introduza as credenciais que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim      |
| **ID de sessão**     | ID de sessão OAuth da sessão de autorização do OAuth. Cada ID de sessão é exclusivo e pode ser utilizado apenas uma vez. Esta definição é gerada automaticamente quando utiliza o Editor do Data Factory. | Sim      |

**Exemplo: Autenticação de credenciais de utilizador**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização que gerou utilizando o **autorizar** botão expira após algum tempo. Consulte a tabela seguinte para os tempos de expiração para diferentes tipos de contas de utilizador. Poderá ver o seguinte erro mensagem quando a autenticação **token expira**: erro de operação de credencial: invalid_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: A concessão de acesso fornecida está expirada ou revogada. ID de rastreio: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

| Tipo de utilizador                                | Expira após                            |
| :--------------------------------------- | :--------------------------------------- |
| Contas de utilizador não são geridas pelo Azure Active Directory (@hotmail.com, @live.com, etc.) | 12 horas                                 |
| Contas de utilizadores geridas através do Azure Active Directory (AAD) | executar nos últimos 14 dias após o último setor. <br/><br/>90 dias, se um setor com base no serviço ligado com base em OAuth é executado, pelo menos, uma vez a cada 14 dias. |

Para evitar/resolva este erro, a reautorização utilizando o **autorizar** botão quando o **token expira** e reimplemente o serviço ligado. Também pode gerar valores para **sessionId** e **autorização** propriedades através de programação utilizando o código da seguinte forma:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Consulte [AzureDataLakeStoreLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), e [AuthorizationSessionGetResponse classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tópicos para obter detalhes sobre as classes de fábrica de dados utilizadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe de WindowsFormsWebAuthenticationDialog. 

## <a name="azure-sql-linked-service"></a>Serviço ligado de SQL do Azure
Criar um serviço ligado SQL do Azure e utilizá-la com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Consulte [conector do SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço ligado do armazém de dados SQL do Azure
Criar um serviço ligado do Azure SQL Data Warehouse e utilizá-la com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Consulte [conector de armazém de dados do Azure SQL](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="sql-server-linked-service"></a>Serviço ligado do SQL Server
Criar um serviço ligado do SQL Server e utilizá-la com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Consulte [conector do SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

