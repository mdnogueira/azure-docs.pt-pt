---
title: "Computação ambientes suportados pelo Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre os ambientes de computação que pode utilizar em pipelines do Azure Data Factory (por exemplo, o Azure HDInsight) para dados de transformação ou processo."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2017
ms.author: shengc
ms.openlocfilehash: a530b08c276596ddbffafc21e6cffdd9e0e9e3fa
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Suportado pelo Azure Data Factory de ambientes de computação
Este artigo explica vários ambientes de computação que pode utilizar para o processo ou de transformação de dados. Também fornece detalhes sobre as configurações diferentes (a pedido vs traga o seu próprio) suportados pela fábrica de dados, quando configurar serviços ligados de ligação estes computação ambientes para um Azure data factory.

A tabela seguinte fornece uma lista de ambientes de computação suportada pela fábrica de dados e as atividades que podem ser executadas nos mesmos. 

| Ambiente de computação                      | atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster do HDInsight a pedido](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster do HDInsight](#azure-hdinsight-linked-service) | [Ramo de registo](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md) |
| [O Azure Batch](#azure-batch-linked-service) | [Personalizada](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](transform-data-using-machine-learning.md) |
| [Análise do Azure Data Lake](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [SQL do Azure](#azure-sql-linked-service), [do armazém de dados SQL do Azure](#azure-sql-data-warehouse-linked-service), [do SQL Server](#sql-server-linked-service) | [Procedimento Armazenado](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Ambiente de computação a pedido
Este tipo de configuração, o ambiente informático totalmente é gerido pelo serviço do Azure Data Factory. É automaticamente criada pelo serviço Data Factory antes de uma tarefa é submetida para processar dados e removida quando a tarefa estiver concluída. Pode criar um serviço ligado para o ambiente de computação a pedido, configurá-lo e controlar as definições do granulares para execução da tarefa, gestão de clusters e bootstrapping ações.

> [!NOTE]
> A configuração a pedido atualmente só é suportada para clusters do HDInsight do Azure.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço ligado do Azure HDInsight a pedido
O serviço do Azure Data Factory automaticamente pode criar um cluster do HDInsight a pedido para processamento de dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName no JSON) associada com o cluster. A conta de armazenamento tem de ser uma conta de armazenamento do Azure standard para fins gerais. 

Tenha em atenção o seguinte **importante** serviço ligado de pontos de HDInsight a pedido:

* O cluster do HDInsight a pedido é criado na sua subscrição do Azure. São capazes de ver o cluster no portal do Azure, quando o cluster está operacional e em execução. 
* Os registos para tarefas que são executadas num cluster do HDInsight a pedido são copiados para a conta de armazenamento associada com o cluster do HDInsight. O clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definida na definição do serviço ligado são utilizadas para iniciar sessão para o cluster para resolução de problemas detalhada durante o ciclo de vida do cluster. 
* São-lhe cobrados apenas para a hora quando o cluster do HDInsight está ativo e tarefas em execução.

> [!IMPORTANT]
> Normalmente demora **20 minutos** ou mais para aprovisionar um cluster do Azure HDInsight a pedido.
>
> 

### <a name="example"></a>Exemplo
O JSON seguinte define um serviço ligado do HDInsight baseado em Linux da pedido. O serviço Data Factory cria automaticamente um **baseado em Linux** cluster do HDInsight para processar a atividade necessária. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento. 
>
> Como mais a atividade é executada, verá muitos contentores no seu armazenamento de Blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | A propriedade de tipo deve ser definida como **HDInsightOnDemand**. | Sim      |
| ClusterSize                  | Número de nós de trabalho/dados do cluster. O cluster do HDInsight é criado com 2 nós principais, juntamente com o número de nós de trabalho que especificar para esta propriedade. Os nós são de tamanho Standard_D3 tem 4 núcleos, pelo que um cluster de nós de 4 trabalho demora 24 núcleos (4\*4 = 16 núcleos para nós de trabalho plus 2\*4 = 8 núcleos de nós principais). Consulte [configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter mais detalhes. | Sim      |
| linkedServiceName            | Serviço ligado do Storage do Azure a ser utilizado pelo cluster a pedido para armazenar e processar dados. O cluster do HDInsight é criado na mesma região que esta conta de armazenamento do Azure. O Azure HDInsight tem limitação do número total de núcleos que pode utilizar em cada região do Azure que suporta. Certifique-se que tem suficiente quotas de núcleos nessa região do Azure para satisfazer o clusterSize necessária. Para obter detalhes, consulte [configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Atualmente, não é possível criar um cluster do HDInsight a pedido que utiliza um Azure Data Lake Store, como o armazenamento. Se pretende armazenar os dados de resultado do HDInsight em processamento no Azure Data Lake Store, utilize uma atividade de cópia para copiar os dados de armazenamento de Blobs do Azure para o Azure Data Lake Store. </p> | Sim      |
| clusterResourceGroup         | O cluster do HDInsight é criado neste grupo de recursos. | Sim      |
| TimeToLive                   | O tempo de inatividade permitido para o cluster do HDInsight a pedido. Especifica o tempo durante o qual o cluster do HDInsight a pedido permanece alive após a conclusão de uma atividade executar se existirem outras tarefas ativas no cluster. Permitido o valor mínimo é 5 minutos (00: 05:00).<br/><br/>Por exemplo, se executar uma atividade demora 6 minutos e timetolive está definido para 5 minutos, o cluster permanece ativo durante 5 minutos após a execução de 6 minutos de atividade de processamento. Se executar de outra atividade é executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>Criar um cluster do HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo), por isso, utilize esta definição como necessários para melhorar o desempenho de uma fábrica de dados através da reutilização de um cluster do HDInsight a pedido.<br/><br/>Se definir o valor de timetolive para 0, o cluster é eliminado, assim que concluir a execução de atividade. Enquanto que, se definir um valor elevado, o cluster pode permanecer inativo para que possa iniciar sessão para algumas resolução de problemas objetivo, mas pode resultar em custos elevados. Por conseguinte, é importante que defina o valor adequado com base nas suas necessidades.<br/><br/>Se o valor da propriedade timetolive adequadamente estiver definido, pipelines vários podem partilhar a instância de cluster do HDInsight a pedido. | Sim      |
| clusterType                  | O tipo de cluster do HDInsight a ser criado. Valores permitidos são "hadoop" e "spark". Se não for especificado, o valor predefinido é hadoop. | Não       |
| Versão                      | Versão do cluster do HDInsight. Se não for especificado, está a utilizar a versão atual do HDInsight definido. | Não       |
| hostSubscriptionId           | O ID de subscrição do Azure utilizado para criar cluster do HDInsight. Se não for especificado, utiliza o ID de subscrição do contexto de início de sessão do Azure. | Não       |
| clusterNamePrefix           | O prefixo do nome do cluster HDI, um timestamp será acrescentado automaticamente no fim do nome do cluster| Não       |
| sparkVersion                 | A versão do spark se o tipo de cluster é "Spark" | Não       |
| additionalLinkedServiceNames | Especifica a contas de armazenamento adicional para o HDInsight serviço ligado para que o serviço fábrica de dados pode registá-los em seu nome. Estas contas de armazenamento tem de ser na mesma região que o cluster do HDInsight, que é criado na mesma região que a conta de armazenamento especificada por linkedServiceName. | Não       |
| osType                       | Tipo de sistema operativo. Valores permitidos são: Linux e Windows (para o HDInsight 3.3 apenas). Predefinição é Linux. | Não       |
| hcatalogLinkedServiceName    | O nome do SQL do Azure ligado serviço que apontam para a base de dados do HCatalog. O cluster do HDInsight a pedido é criado utilizando a base de dados SQL do Azure como o metastore. | Não       |
| connectVia                   | O tempo de execução de integração a ser utilizado para emitir as atividades para este serviço ligado de HDInsight. Para o serviço ligado de HDInsight a pedido só suporta Runtime de integração do Azure. Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. | Não       |
| clusterUserName                   | O nome de utilizador para aceder ao cluster. | Não       |
| clusterPassword                   | A palavra-passe no tipo de cadeia segura para aceder ao cluster. | Não       |
| clusterSshUserName         | O nome de utilizador SSH ligar remotamente ao nó do cluster (para Linux). | Não       |
| clusterSshPassword         | A palavra-passe no tipo de cadeia segura para SSH ligar remotamente nó do cluster (para Linux). | Não       |


> [!IMPORTANT]
> HDInsight suporta várias versões de cluster de Hadoop que podem ser implementadas. Cada opção de versão cria uma versão específica da distribuição Hortonworks Data Platform (HDP) e um conjunto de componentes que estão contidas dentro dessa distribuição. A lista de versões suportadas do HDInsight mantém a ser atualizada para fornecer mais recentes componentes ecossistema do Hadoop e correções. Certifique-se de que sempre referir-se a informações mais recentes do [suportado HDInsight versão e tipo de SO](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para se certificar de que está a utilizar uma versão suportada do HDInsight. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>exemplo JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

O serviço ligado de HDInsight a pedido no requer uma autenticação principal de serviço para criar clusters do HDInsight em seu nome. Para utilizar a autenticação principal de serviço, registar uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder a **contribuinte** função de subscrição ou o grupo de recursos no qual o cluster do HDInsight é criado. Para obter passos detalhados, consulte [portal de utilização para criar um Azure Active Directory principal de serviço e aplicação que pode aceder aos recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Tome nota dos seguintes valores que utilizar para definir o serviço ligado:

- ID da aplicação
- Chave da aplicação 
- ID do inquilino

Utilize a autenticação principal de serviço, especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique o ID de cliente. da aplicação     | Sim      |
| **servicePrincipalKey** | Especifique a chave da aplicação.           | Sim      |
| **inquilino**              | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Sim      |

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
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
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
Consulte o [tamanhos das Virtual Machines](../virtual-machines/linux/sizes.md) artigo para valores de cadeia tem de especificar para as propriedades mencionadas na secção anterior. Os valores têm de estar em conformidade com a **CMDLETs & APIS** referenciado no artigo. Como pode ver no artigo, o nó de dados de tamanho grande (predefinição) tem 7 GB de memória, que pode não ser suficientemente boa para o seu cenário. 

Se pretender criar D4 um tamanho de nós principais e nós de trabalho, especifique **Standard_D4** como o valor das propriedades headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se especificar um valor incorreto para estas propriedades, poderá receber o seguinte **erro:** Falha ao criar o cluster. Exceção: não foi possível concluir a operação de criação do cluster. A operação falhou com o código "400". Estado do cluster não concluído: "Erro". Mensagem: 'PreClusterCreationValidationFailure'. Quando receber este erro, certifique-se de que está a utilizar o **CMDLET & APIS** nome da tabela no [tamanhos das Virtual Machines](../virtual-machines/linux/sizes.md) artigo.        

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
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
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
| connectVia        | O tempo de execução de integração a ser utilizado para emitir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração. Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. | Não       |

> [!IMPORTANT]
> HDInsight suporta várias versões de cluster de Hadoop que podem ser implementadas. Cada opção de versão cria uma versão específica da distribuição Hortonworks Data Platform (HDP) e um conjunto de componentes que estão contidas dentro dessa distribuição. A lista de versões suportadas do HDInsight mantém a ser atualizada para fornecer mais recentes componentes ecossistema do Hadoop e correções. Certifique-se de que sempre referir-se a informações mais recentes do [suportado HDInsight versão e tipo de SO](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para se certificar de que está a utilizar uma versão suportada do HDInsight. 
>

## <a name="azure-batch-linked-service"></a>Serviço de Batch ligado do Azure

Pode criar um serviço ligado do Azure Batch para registar um conjunto do Batch de máquinas virtuais (VMs) a uma fábrica de dados. Pode executar a atividade personalizada através do Azure Batch.

Consulte os seguintes tópicos se estiver familiarizado com o serviço Azure Batch:

* [Noções básicas de lote do Azure](../batch/batch-technical-overview.md) para uma descrição geral do serviço Azure Batch.
* [Novo-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) cmdlet para criar uma conta do Azure Batch (ou) [portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do Azure Batch através do portal do Azure. Consulte [utilizando o PowerShell para gerir a conta do Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) tópico para obter instruções detalhadas sobre como utilizar o cmdlet.
* [Novo-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) cmdlet para criar um conjunto do Batch do Azure.

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | A propriedade de tipo deve ser definida como **AzureBatch**. | Sim      |
| accountName       | Nome da conta do Azure Batch.         | Sim      |
| accessKey         | Chave de acesso para a conta do Azure Batch.  | Sim      |
| batchUri          | URL para a sua conta do Azure Batch, no formato de https://*batchaccountname.region*. batch.azure.com. | Sim      |
| poolName          | Nome do conjunto de máquinas virtuais.    | Sim      |
| linkedServiceName | Nome do Storage do Azure ligado serviço associado este serviço ligado do Azure Batch. Este serviço ligado é utilizado para os ficheiros necessários para executar a atividade de teste. | Sim      |
| connectVia        | O tempo de execução de integração a ser utilizado para emitir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração. Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. | Não       |

## <a name="azure-machine-learning-linked-service"></a>Serviço de Machine Learning ligado do Azure
Criar um serviço ligado do Azure Machine Learning para registar um lote de Machine Learning a classificação de ponto final de uma fábrica de dados.

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Propriedades
| Propriedade               | Descrição                              | Necessário                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Tipo                   | A propriedade de tipo deve ser definida como: **AzureML**. | Sim                                      |
| mlEndpoint             | O URL de classificação de lote.                   | Sim                                      |
| ApiKey                 | API o modelo de área de trabalho publicado.     | Sim                                      |
| updateResourceEndpoint | O URL de recurso atualizar para um ponto final de serviço Web do Azure ML utilizado para atualizar o serviço Web Preditiva com o ficheiro de modelo treinado | Não                                       |
| servicePrincipalId     | Especifique o ID de cliente. da aplicação     | Necessário se updateResourceEndpoint for especificado |
| servicePrincipalKey    | Especifique a chave da aplicação.           | Necessário se updateResourceEndpoint for especificado |
| Inquilino                 | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Necessário se updateResourceEndpoint for especificado |
| connectVia             | O tempo de execução de integração a ser utilizado para emitir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração. Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. | Não                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço do Azure Data Lake Analytics ligado
Criar um **Azure Data Lake Analytics** um Azure data factory do serviço de computação de serviço ligado para ligar um Azure Data Lake Analytics. A atividade U-SQL do Data Lake Analytics no pipeline refere-se a este serviço ligado. 

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Propriedades

| Propriedade             | Descrição                              | Necessário                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| tipo                 | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | Nome de conta do Azure Data Lake Analytics.  | Sim                                      |
| dataLakeAnalyticsUri | URI do Azure Data Lake Analytics.           | Não                                       |
| subscriptionId       | Id de subscrição do Azure                    | Não (se não for especificado, a subscrição do data factory é utilizada). |
| resourceGroupName    | Nome do grupo de recursos do Azure                | Não (se não for especificado, grupo de recursos do data factory é utilizado). |
| servicePrincipalId   | Especifique o ID de cliente. da aplicação     | Sim                                      |
| servicePrincipalKey  | Especifique a chave da aplicação.           | Sim                                      |
| Inquilino               | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Sim                                      |
| connectVia           | O tempo de execução de integração a ser utilizado para emitir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração. Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. | Não                                       |



## <a name="azure-sql-database-linked-service"></a>Serviço ligado da Base de Dados SQL do Azure
Criar um serviço ligado SQL do Azure e utilizá-la com o [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Consulte [conector do SQL Azure](connector-azure-sql-database.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço ligado do SQL Data Warehouse do Azure
Criar um serviço ligado do Azure SQL Data Warehouse e utilizá-la com o [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Consulte [conector de armazém de dados do Azure SQL](connector-azure-sql-data-warehouse.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="sql-server-linked-service"></a>Serviço ligado do SQL Server
Criar um serviço ligado do SQL Server e utilizá-la com o [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Consulte [conector do SQL Server](connector-sql-server.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="azure-data-factory---naming-rules"></a>O Azure Data Factory - as regras de nomenclatura
A tabela seguinte fornece regras de nomenclatura artefactos do Data Factory de dados.

| Nome                             | Exclusividade de nome                          | Verificações de validação                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data Factory                     | Exclusivo em todo o Microsoft Azure. Os nomes são sensível, ou seja, `MyDF` e `mydf` Consulte para a mesma fábrica de dados. | <ul><li>Cada fábrica de dados está associada ao exatamente uma subscrição do Azure.</li><li>Nomes de objeto tem de começar com uma letra ou um número e só podem conter letras, números e o caráter de travessão (-).</li><li>Cada caráter de travessão (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contentor.</li><li>Nome pode ter entre 3 e 63 carateres.</li></ul> |
| Os serviços ligados/tabelas/pipelines | Exclusivo com uma fábrica de dados. Os nomes são sensível. | <ul><li>Número máximo de carateres num nome de tabela: a 260.</li><li>Nomes de objeto tem de começar com uma letra, o número ou um caráter de sublinhado (_).</li><li>Os seguintes carateres não são permitidos: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Grupo de Recursos                   | Exclusivo em todo o Microsoft Azure. Os nomes são sensível. | <ul><li>Número máximo de carateres: 1000.</li><li>O nome pode conter letras, dígitos e os seguintes carateres: "-", "_",","e"."</li></ul> |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista das atividades de transformação suportado pelo Azure Data Factory, consulte [transformar dados](transform-data.md).
