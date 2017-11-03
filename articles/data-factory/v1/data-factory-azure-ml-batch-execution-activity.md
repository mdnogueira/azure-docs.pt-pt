---
title: Criar pipelines de dados preditiva utilizando o Azure Data Factory | Microsoft Docs
description: Descreve como criar criar pipelines preditivos com o Azure Data Factory e o Azure Machine Learning
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3169584bc884107ccd34b01264683d8c73c0fecb
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Criar pipelines preditivos com o Azure Machine Learning e o Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do ramo de registo](data-factory-hive-activity.md) 
> * [Atividade do PIg](data-factory-pig-activity.md)
> * [Atividade de MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introdução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados através de aprendizagem na versão 2 do Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[O Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe criar, testar e implementar soluções de Análise Preditiva. Do ponto de vista alto nível, é efetuada em três passos:

1. **Criar uma experimentação de preparação**. Execute este passo utilizando o Azure ML Studio. ML studio é um ambiente de desenvolvimento de visual de colaboração que utilizar para formação e testar um modelo de Análise Preditiva utilizando dados de preparação.
2. **Convertê-lo para uma experimentação preditiva**. Depois do seu modelo tem sido preparado com a dados existentes e está pronto para utilizá-lo para pontuar novos dados, pode prepara e simplificar a sua experimentação para classificação.
3. **Implementá-lo como um serviço web**. Pode publicar a sua experimentação de classificação como um serviço web do Azure. Pode enviar dados para o seu modelo através deste ponto de final de serviço web e receber predições resultado fro o modelo.  

### <a name="azure-data-factory"></a>Azure Data Factory
O Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o **movimento** e a **transformação** de dados. Pode criar soluções de integração de dados utilizando o Azure Data Factory que podem ingerir dados a partir de vários arquivos de dados, transformação/processe os dados e publicar dados de resultados para os arquivos de dados.

O serviço Data Factory permite-lhe criar pipelines de dados que movimentam e transformam dados e, depois, executar esses pipelines numa agenda especificada (hora a hora, diariamente, semanalmente, etc.). Também proporciona visualizações ricas para apresentar a linhagem e as dependências entre os pipelines de dados e monitorizá-los a partir de uma vista unificada única, para identificar facilmente problemas e configurar alertas de monitorização.

Consulte [introdução ao Azure Data Factory](data-factory-introduction.md) e [construir o seu primeiro pipeline](data-factory-build-your-first-pipeline.md) artigos para rapidamente começar com o serviço do Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Fábrica de dados e de Machine Learning em conjunto
O Azure Data Factory permite-lhe facilmente criar pipelines que utilizam um publicados [Azure Machine Learning] [ azure-machine-learning] web service para Análise Preditiva. Utilizar o **atividade de execução de lote** um pipeline do Azure Data Factory, pode invocar um serviço web do Azure ML para tornar as predições nos dados no batch. Consulte [invocar um serviço de web do Azure ML com a atividade de execução de lote](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) secção para obter detalhes.

Ao longo do tempo, os modelos preditivos do Azure ML experimentações da classificação tem de ser retrained utilizando conjuntos de dados de entrada novo. Pode a reparametrização de um modelo do Azure ML de um pipeline do Data Factory efetuando os seguintes passos:

1. Publica a experimentação de preparação (experimentação preditiva não) como um serviço web. Efetue este passo no Azure ML Studio tal como fez para expor experimentação preditiva como um serviço web no cenário anterior.
2. Utilize a atividade de execução de lote do Azure ML para invocar o serviço web para a experimentação de preparação. Basicamente, pode utilizar a atividade de execução de lote do Azure ML invocar serviço web de formação e classificação serviço web.

Depois de terminar com reparametrização, atualize o serviço web de classificação (experimentação preditiva exposta como um serviço web) com o modelo treinado recentemente utilizando o **da atividade de recursos de atualização do Azure ML**. Consulte [atualizar modelos de atividade do recurso da atualização](data-factory-azure-ml-update-resource-activity.md) artigo para obter detalhes.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocar um serviço web utilizando a atividade de execução de lote
Utilizar o Azure Data Factory para orquestrar o movimento de dados e de processamento e, em seguida, efetuar a execução de lote através do Azure Machine Learning. Eis os passos de nível superior:

1. Crie um serviço ligado do Azure Machine Learning. Terá dos seguintes valores:

   1. **URI de pedido** para a execução de lote API. Pode encontrar o URI de pedido clicando a **de execução de lote** ligação na página de serviços web.
   2. **Chave de API** para o Azure Machine Learning publicados serviço web. Pode encontrar a chave de API clicando ao serviço web que tiver publicado.
   3. Utilize o **AzureMLBatchExecution** atividade.

      ![Dashboard do Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![URI do batch](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: Experimentações com o Web service entradas/saídas que fazem referência a dados no Blob Storage do Azure
Neste cenário, o serviço Web do Azure Machine Learning torna predições utilizando os dados de um ficheiro no armazenamento de Blobs do Azure e armazena os resultados de predição no armazenamento de Blobs. O JSON seguinte define um pipeline do Data Factory com uma atividade AzureMLBatchExecution. A atividade tem o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como resultado. O **DecisionTreeInputBlob** é transmitida como uma entrada para o serviço web utilizando o **webServiceInput** propriedade JSON. O **DecisionTreeResultBlob** é transmitida como um resultado para o serviço Web utilizando o **webServiceOutputs** propriedade JSON.  

> [!IMPORTANT]
> Se o serviço web tem várias entradas, utilize o **webServiceInputs** propriedade em vez de utilizar **webServiceInput**. Consulte o [serviço Web requer várias entradas](#web-service-requires-multiple-inputs) secção para obter um exemplo de utilização da propriedade webServiceInputs.
>
> Conjuntos de dados que são referenciados pelo **webServiceInput**/**webServiceInputs** e **webServiceOutputs** propriedades (no **typeProperties**) também têm de ser incluídos na atividade **entradas** e **produz**.
>
> Na sua experimentação do Azure ML, entrada de serviço web e portas de saída e parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes a que utilizar para webServiceInputs, webServiceOutputs e definições de globalParameters devem corresponder exatamente os nomes de experimentações. Pode ver o payload de pedido de exemplo na página de ajuda de execução de Batch para o ponto de final do Azure ML verificar o mapeamento esperado.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }                
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Apenas entradas e saídas da atividade AzureMLBatchExecution podem ser transmitidas como parâmetros para o serviço Web. Por exemplo, no fragmento JSON acima, DecisionTreeInputBlob for uma entrada para a atividade AzureMLBatchExecution, que é transmitida como uma entrada para o serviço Web através do parâmetro webServiceInput.   
>
>

### <a name="example"></a>Exemplo
Este exemplo utiliza o armazenamento do Azure para armazenar dados entrados e de saída.

Recomendamos que leia o [construir o seu primeiro pipeline com o Data Factory] [ adf-build-1st-pipeline] tutorial antes de passar neste exemplo. Utilize o Editor do Data Factory para criar artefactos do Data Factory (serviços ligados, conjuntos de dados, pipeline) neste exemplo.   

1. Criar um **serviço ligado** para sua **Storage do Azure**. Se os ficheiros de entrada e saídos estão em contas de armazenamento diferente, terá dois serviços ligados. Eis um exemplo JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Criar o **entrada** do Azure Data Factory **dataset**. Ao contrário de algumas outros Data Factory conjuntos de dados, estes conjuntos de dados tem de conter ambos **folderPath** e **fileName** valores. Pode utilizar a criação de partições para fazer com que cada execução de lote (cada setor de dados) processar ou produzir exclusiva entrada e saída de ficheiros. Terá de incluir alguma atividade a montante para transformar a entrada para o formato de ficheiro CSV e colocá-lo na conta do storage para cada setor. Nesse caso, não inclui o **externo** e **externalData** definições mostradas no exemplo seguinte e o DecisionTreeInputBlob seria o conjunto de dados de saída de uma atividade diferente.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    O ficheiro csv de entrada tem de ter a linha de cabeçalho de coluna. Se estiver a utilizar o **atividade de cópia** para criar/mover o csv para o armazenamento de BLOBs, deve definir a propriedade sink **blobWriterAddHeader** para **verdadeiro**. Por exemplo:

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    Se o ficheiro csv não tiver a linha de cabeçalho, poderá ver o seguinte erro: **erro na atividade: erro ao ler a cadeia. Token inesperado: StartObject. Caminho ', linha 1, posição 1**.
3. Criar o **saída** do Azure Data Factory **dataset**. Este exemplo utiliza a criação de partições para criar um caminho de saída exclusivo para cada execução do setor. Sem a criação de partições, a atividade substituiria o ficheiro.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Criar um **serviço ligado** do tipo: **AzureMLLinkedService**, fornecendo a chave de API e URL da execução de lote de modelo.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Por fim, criar um pipeline que contém um **AzureMLBatchExecution** atividade. Em runtime, o pipeline efetua os seguintes passos:

   1. Obtém a localização do ficheiro de entrada a partir do seu conjuntos de dados de entrada.
   2. Invoca a execução de lote do Azure Machine Learning API
   3. Copia o resultado da execução de batch para o blob fornecido no seu conjunto de dados de saída.

      > [!NOTE]
      > Atividade de AzureMLBatchExecution pode ter zero ou mais entradas e saídas de um ou mais.
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      Ambos **iniciar** e **final** DateTime tem de constar [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **final** tempo é opcional. Se não especificar valor para o **final** propriedade, esta é calculada como "**início + 48 horas.**" Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

      > [!NOTE]
      > A especificação de entrada para o AzureMLBatchExecution atividade é opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: Experimentações com módulos de leitor/escritor para fazer referência aos dados no armazenamento de vários
Outro cenário típico possível quando criar experimentações do Azure ML é utilizar módulos leitor e escritor. Módulo leitor é utilizado para carregar dados para uma experimentação e é o módulo de escritor guardar os dados a partir das suas experimentações. Para obter detalhes sobre o leitor e escritor módulos, consulte [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) tópicos da biblioteca MSDN.     

Quando utilizar os módulos de leitor e escritor, é boa prática para utilizar um parâmetro de serviço Web para cada propriedade estes módulos de leitor/escritor. Estes parâmetros web permitem-lhe configurar os valores durante o tempo de execução. Por exemplo, pode criar uma experimentação com um módulo leitor de que utiliza uma base de dados do SQL do Azure: XXX.database.windows.net. Depois de implementar o serviço web, que pretende permitir que os consumidores do serviço web especificar outro servidor de SQL do Azure chamado YYY.database.windows.net. Pode utilizar um parâmetro de serviço Web para permitir que este valor para ser configurado.

> [!NOTE]
> Entrada de serviço Web e de saída são diferentes dos parâmetros de serviço Web. O primeiro cenário, constatou como uma entrada e saída podem ser especificados para um serviço Web do Azure ML. Neste cenário, passar parâmetros para um serviço Web que correspondem às propriedades de módulos de leitor/escritor.
>
>

Vamos ver um cenário de utilização de parâmetros de serviço Web. Tem um serviço implementado de web Azure Machine Learning que utiliza um módulo leitor para ler dados a partir de uma das origens de dados suportadas pelo Azure Machine Learning (por exemplo: SQL Database do Azure). Após a execução de batch é efetuada, os resultados são escritos utilizando um módulo de escritor (SQL Database do Azure).  Não existem entradas de serviço web e saídas estão definidas em experimentações. Neste caso, recomendamos que configure parâmetros de serviço web relevantes para os módulos de leitor e escritor. Esta configuração permite que o escritor/leitor módulos quando utilizar a atividade de AzureMLBatchExecution. Especifique os parâmetros do serviço Web no **globalParameters** secção no JSON de atividade da seguinte forma.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Também pode utilizar [funções da fábrica de dados](data-factory-functions-variables.md) na transmissão de valores para o Web service parâmetros, conforme mostrado no exemplo seguinte:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web são maiúsculas e minúsculas, por isso, certifique-se de que os nomes que especificar na atividade JSON corresponder aos perfis expostos pelo serviço Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Utilizar um módulo leitor para ler os dados de vários ficheiros no Blob do Azure
Macrodados pipelines com atividades, tais como o Pig e ramo de registo pode produzir uma ou mais ficheiros de saída com nenhuma extensão. Por exemplo, quando especificar uma tabela externa do ramo de registo, os dados para a tabela de Hive externa podem ser armazenados no blob storage do Azure com o seguinte 000000_0 de nome. Pode utilizar o módulo de leitor numa experimentação para ler vários ficheiros e utilizá-los para as predições.

Quando utilizar o módulo leitor de uma experimentação do Azure Machine Learning, pode especificar o Blob do Azure como entrada. Os ficheiros no blob storage do Azure podem ser os ficheiros de saída (exemplo: 000000_0) que são produzidos pelo script Pig e ramo de registo em execução no HDInsight. Módulo leitor permite-lhe ler os ficheiros (com nenhuma extensão) configurando o **caminho para o contentor, diretório/blob**. O **caminho para o contentor** aponta para o contentor e **diretório/blob** aponta para a pasta que contém os ficheiros, conforme mostrado na imagem seguinte. O asterisco ou seja, \*) **Especifica que todos os ficheiros na pasta/contentor (ou seja, dados/aggregateddata/ano = mês/2014-6 /\*)** sejam de leitura como parte da experimentação.

![Propriedades de Blob do Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline com atividade AzureMLBatchExecution com parâmetros de serviço Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }              
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

No exemplo acima de JSON:

* O serviço Web do Azure Machine Learning implementado utiliza um leitor e um módulo de escritor de dados de/para uma base de dados do SQL do Azure de leitura/escrita. Este serviço Web expõe os seguintes quatro parâmetros: nome do servidor, nome de base de dados, nome de conta de utilizador do servidor e palavra-passe de conta de utilizador servidor de base de dados.  
* Ambos **iniciar** e **final** DateTime tem de constar [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **final** tempo é opcional. Se não especificar valor para o **final** propriedade, esta é calculada como "**início + 48 horas.**" Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

### <a name="other-scenarios"></a>Outros cenários
#### <a name="web-service-requires-multiple-inputs"></a>Serviço Web requer várias entradas
Se o serviço web tem várias entradas, utilize o **webServiceInputs** propriedade em vez de utilizar **webServiceInput**. Conjuntos de dados que são referenciados pelo **webServiceInputs** também têm de ser incluídos na atividade **entradas**.

Na sua experimentação do Azure ML, entrada de serviço web e portas de saída e parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes a que utilizar para webServiceInputs, webServiceOutputs e definições de globalParameters devem corresponder exatamente os nomes de experimentações. Pode ver o payload de pedido de exemplo na página de ajuda de execução de Batch para o ponto de final do Azure ML verificar o mapeamento esperado.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Serviço Web não necessita de uma entrada
Serviços do web de execução de lote do Azure ML podem ser utilizados para executar quaisquer fluxos de trabalho, para R ou Python scripts de exemplo, que não podem exigir a quaisquer entradas. Em alternativa, a experimentação pode ser configurada com um módulo de leitor não expõe qualquer GlobalParameters. Nesse caso, a atividade de AzureMLBatchExecution seria configurada da seguinte forma:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }              
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Serviço Web não necessita de uma entrada/saída
O serviço de web de execução de lote do Azure ML poderá não ter qualquer saída de serviço Web configurada. Neste exemplo, não há nenhum serviço Web de entrada ou saída nem qualquer GlobalParameters configurados. Ainda há uma saída configurada na atividade si próprio, mas não está a ser fornecido como um webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Leitores de utilizações de serviço Web e escritores e a execução de atividade apenas quando outras atividades êxito
Os Azure ML web leitor e escritor módulos do serviço podem ser configurados para executar com ou sem quaisquer GlobalParameters. No entanto, poderá querer incorporar chamadas de serviço com um pipeline que utiliza as dependências de conjunto de dados para invocar o serviço apenas quando concluir algumas processamento a montante. Também pode acionar qualquer outra ação uma vez concluída a execução de lote através desta abordagem. Nesse caso, pode express as dependências utilizando entradas de atividade e saídas, sem qualquer um deles de nomenclatura como serviço Web entradas ou saídas.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

O **takeaways** são:

* Se o ponto final de experimentação utiliza um webServiceInput: Este é representada por um conjunto de dados de blob e está incluído nas entradas de atividade e a propriedade webServiceInput. Caso contrário, a propriedade webServiceInput for omitida.
* Se o ponto final de experimentação utiliza webServiceOutput(s): estes são representados por conjuntos de dados de blob e incluídas nas saídas da atividade e na propriedade webServiceOutputs. A atividade produz e webServiceOutputs estão mapeados com o nome de cada resultado na experimentação. Caso contrário, a propriedade webServiceOutputs for omitida.
* Se o ponto final de experimentação expõe globalParameter(s), são fornecidos na propriedade globalParameters atividade como chave, pares de valor. Caso contrário, a propriedade globalParameters for omitida. As chaves são sensíveis a maiúsculas. [Funções do Azure Data Factory](data-factory-functions-variables.md) podem ser utilizados os valores.
* Conjuntos de dados adicionais podem ser incluídos nas propriedades de entradas e saídas da atividade, sem a ser referenciado em typeProperties a atividade. Estes conjuntos de dados governem execução utilizando dependências de setor mas caso contrário, são ignorados pela atividade AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Atualizar Modelos de atividade do recurso da atualização
Depois de terminar com reparametrização, atualize o serviço web de classificação (experimentação preditiva exposta como um serviço web) com o modelo treinado recentemente utilizando o **da atividade de recursos de atualização do Azure ML**. Consulte [atualizar modelos de atividade do recurso da atualização](data-factory-azure-ml-update-resource-activity.md) artigo para obter detalhes.

### <a name="reader-and-writer-modules"></a>Leitor e escritor módulos
Um cenário comum para utilizar os parâmetros do serviço Web é a utilização de leitores de SQL do Azure e escritores. Módulo leitor é utilizado para carregar dados para uma experimentação de serviços de gestão de dados fora do Azure Machine Learning Studio. O módulo de escritor está guardar os dados a partir das suas experimentações para os serviços de gestão de dados fora do Azure Machine Learning Studio.  

Para obter detalhes sobre o Azure Blob/Azure SQL. o leitor/escritor, consulte [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) tópicos da biblioteca MSDN. O exemplo na secção anterior utilizado o leitor de Blob do Azure e o escritor de Blob do Azure. Esta secção explica como utilizar o leitor de SQL do Azure e o escritor SQL do Azure.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**P:** tiver vários ficheiros que foram gerados por meu pipelines de macrodados. Pode utilizar a atividade de AzureMLBatchExecution para trabalhar em todos os ficheiros?

**R:** Sim. Consulte o **utilizando um módulo leitor de ler os dados de vários ficheiros no Blob do Azure** secção para obter detalhes.

## <a name="azure-ml-batch-scoring-activity"></a>Atividade de classificação de lote do Azure ML
Se estiver a utilizar o **AzureMLBatchScoring** atividade para integrar com o Azure Machine Learning, recomendamos que utilize a versão mais recente **AzureMLBatchExecution** atividade.

A atividade de AzureMLBatchExecution é introduzida na versão de Agosto de 2015 do SDK do Azure e o Azure PowerShell.

Se pretender continuar a utilizar a atividade de AzureMLBatchScoring, continue a ler através desta secção.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Classificação de lote ML atividade do Azure utilizando o armazenamento do Azure para a entrada/saída

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parâmetros do serviço Web
Para especificar valores para parâmetros de serviço Web, adicione um **typeProperties** secção para o **AzureMLBatchScoringActivty** secção no pipeline JSON, conforme mostrado no exemplo seguinte:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Também pode utilizar [funções da fábrica de dados](data-factory-functions-variables.md) na transmissão de valores para o Web service parâmetros, conforme mostrado no exemplo seguinte:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web são maiúsculas e minúsculas, por isso, certifique-se de que os nomes que especificar na atividade JSON corresponder aos perfis expostos pelo serviço Web.
>
>

## <a name="see-also"></a>Veja Também
* [Mensagem de blogue do Azure: introdução ao Azure Data Factory e o Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
