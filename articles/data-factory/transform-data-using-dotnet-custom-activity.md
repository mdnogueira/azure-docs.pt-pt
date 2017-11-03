---
title: Utilizar atividades personalizadas num pipeline do Azure Data Factory
description: "Saiba como criar atividades personalizadas e utilizá-los um pipeline do Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.openlocfilehash: e470071ca0ff45fce0a410b18ea9a91e1925af4b
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-use-custom-activities.md)
> * [Versão 2 - Pré-visualização](transform-data-using-dotnet-custom-activity.md)

Existem dois tipos de atividades que pode utilizar um pipeline do Azure Data Factory.

- [Atividades de movimentos de dados](copy-activity-overview.md) para mover dados entre [arquivos de dados de origem e dependente suportados](copy-activity-overview.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](transform-data.md) para transformar dados de utilização de serviços de computação, tais como o Azure HDInsight, do Azure Batch e do Azure Machine Learning. 

Mover para/de dados de arquivo de dados que não suporta a fábrica de dados ou para o processo/para transformar dados de uma forma que não é suportada pela fábrica de dados, pode criar um **atividade personalizada** com as suas próprias movimento de dados ou a lógica de transformação e a utilização a atividade num pipeline. A atividade personalizada é executada a lógica de código personalizado num **do Azure Batch** conjunto de máquinas virtuais.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [(personalizado) DotNet atividade no V1](v1/data-factory-use-custom-activities.md).
 

Consulte os seguintes tópicos se estiver familiarizado com o serviço Azure Batch:

* [Noções básicas de lote do Azure](../batch/batch-technical-overview.md) para uma descrição geral do serviço Azure Batch.
* [Novo-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) cmdlet para criar uma conta do Azure Batch (ou) [portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do Azure Batch através do portal do Azure. Consulte [utilizando o PowerShell para gerir a conta do Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) tópico para obter instruções detalhadas sobre como utilizar o cmdlet.
* [Novo-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) cmdlet para criar um conjunto do Batch do Azure.

## <a name="azure-batch-linked-service"></a>Serviço de Batch ligado do Azure 
O JSON seguinte define uma amostra de serviço ligado do Azure Batch. Para obter mais informações, consulte [suportados pelo Azure Data Factory de ambientes de computação](compute-linked-services.md)

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
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Para saber mais sobre o serviço ligado do Azure Batch, consulte o artigo [serviços ligados de computação](compute-linked-services.md) artigo. 

## <a name="custom-activity"></a>Atividade personalizada

O fragmento JSON seguinte define um pipeline com uma atividade personalizada simples. Definição da atividade tem uma referência para o serviço ligado do Azure Batch. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

Neste exemplo, o helloworld.exe é uma aplicação personalizada armazenada na pasta customactv2/olámundo da conta do Storage do Azure utilizada no resourceLinkedService. A atividade personalizada submete esta aplicação personalizada para ser executada no Azure Batch. Pode substituir o comando para qualquer aplicação preferido que pode ser executado no destino do sistema operativo de nós do conjunto do Batch do Azure. 

A tabela seguinte descreve os nomes e descrições das propriedades que são específicas para esta atividade. 

| Propriedade              | Descrição                              | Necessário |
| :-------------------- | :--------------------------------------- | :------- |
| nome                  | Nome da atividade no pipeline     | Sim      |
| descrição           | Texto que descreve o que faz a atividade.  | Não       |
| tipo                  | Para a atividade personalizada, o tipo de atividade é **personalizada**. | Sim      |
| linkedServiceName     | Serviço ligado ao Azure Batch. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo.  | Sim      |
| Comando               | Comando da aplicação personalizada para ser executada. Se a aplicação já se encontra disponível no nó de conjunto de lote do Azure, o resourceLinkedService e folderPath pode ser ignorado. Por exemplo, pode especificar o comando ser `cmd /c dir`, que é suportado nativamente pelo nó de conjunto de Batch do Windows. | Sim      |
| resourceLinkedService | Serviço ligado do Storage do Azure para a conta de armazenamento onde está armazenada a aplicação personalizada | Não       |
| folderPath            | Caminho para a pasta da aplicação personalizada e todas as suas dependências | Não       |
| referenceObjects      | Uma matriz de conjuntos de dados e serviços ligados existentes. Os serviços ligados e conjuntos de dados referenciada são transmitidos para a aplicação personalizada no formato JSON para que o seu código personalizado pode referenciar recursos do Data Factory | Não       |
| ExtendedProperties    | Propriedades definidas pelo utilizador que podem ser transmitidas para a aplicação personalizada no formato JSON para que o seu código personalizado pode referenciar propriedades adicionais | Não       |

## <a name="executing-commands"></a>Executar comandos

Pode executar diretamente um comando através de atividade personalizado. No exemplo seguinte, execute um comando de "Olá mundo de eco" em nós do conjunto do Batch do Azure de destino e imprima o resultado para stdout. 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>Passar objetos e propriedades

Este exemplo mostra como pode utilizar o referenceObjects e extendedProperties passar objetos de fábrica de dados e as propriedades definidas pelo utilizador para a sua aplicação personalizada. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

Quando a atividade é executada, referenceObjects e extendedProperties são armazenadas nos seguintes ficheiros que são implementados na mesma pasta de execução do SampleApp.exe: 

- Activity.JSON

  Armazena extendedProperties e propriedades da atividade personalizada. 

- linkedServices.json

  Arquivos de uma matriz de serviços ligados definido na propriedade referenceObjects. 

- DataSets.JSON

  Arquivos de uma matriz de conjuntos de dados definido na propriedade referenceObjects. 

Seguindo o código de exemplo demonstram como o SampleApp.exe pode aceder as informações necessárias a partir ficheiros JSON: 

```C#
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Obter saídas de execução

  Pode iniciar uma execução de pipeline com o seguinte comando do PowerShell: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Quando o pipeline está em execução, pode verificar o resultado da execução utilizando os seguintes comandos: 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  O **stdout** e **stderr** da sua aplicação personalizada são guardados para a **adfjobs** contentor no serviço ligado de armazenamento de Azure que definiu durante a criação do Azure Batch ligado Serviço com um GUID da tarefa. Pode obter o caminho de detalhado da saída da atividade executada conforme mostrado no seguinte fragmento: 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Se desejar consumir o conteúdo de stdout.txt em atividades a jusante, pode obter o caminho para o ficheiro de stdout.txt na expressão "@activity.output.outputs ('MyCustomActivity') [0]". 

  > [!IMPORTANT]
  > - O activity.json, linkedServices.json e datasets.json são armazenadas na pasta de tempo de execução da tarefa Bath. Para este exemplo, o activity.json, linkedServices.json e datasets.json são armazenados em "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/" caminho. Se necessário, terá de limpá-las separadamente. 
  > - Para o tempo de execução do Self-Hosted integração, as informações confidenciais, como chaves ou palavras-passe são encriptados pelo Runtime integração Self-Hosted para garantir que a credencial de utilizações de serviços ligados permanece no cliente definido de ambiente de rede privada. Alguns campos confidenciais podem estar em falta quando referenciado pelo código da aplicação personalizadas desta forma. Utilize SecureString extendedProperties em vez de utilizar a referência do serviço ligado, se necessário. 

## <a name="difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1"></a>Diferença entre a atividade personalizada no Azure Data Factory V2 e a atividade de DotNet (personalizado) no Azure Data Factory V1 

  No Azure Data Factory V1, implementar código de atividade de DotNet (personalizado), criando um .Net projeto da biblioteca de classe com uma classe que implementa o método de execução da IDotNetActivity interface. Os serviços ligados, conjuntos de dados e propriedades expandidas no payload JSON de atividade do DotNet (personalizado) são transmitidas para o método de execução como objetos de tipo seguros. Para obter detalhes, consulte [DotNet (personalizado) no V1](v1/data-factory-use-custom-activities.md). Devido a que, código personalizado tem de ser escritos no .net Framework 4.5.2 e ser executado em nós do conjunto do Batch baseado no Windows Azure. 

  No Azure Data Factory V2 personalizada atividade, não são necessários para implementar uma interface de .net. Pode agora diretamente executar comandos de scripts e executar a suas próprias personalizada código complied como executável. Alcançar, especificando a propriedade do comando juntamente com a propriedade folderPath. Atividade personalizada carrega o executável e dependências no folderpath e executa o comando para si. 

  Os serviços ligados, conjuntos de dados (definidos no referenceObjects) e propriedades expandidas definido no payload JSON de atividade personalizado podem ser acedidas pelo seu executável como ficheiros JSON. Pode aceder a propriedades necessárias utilizando o serializador JSON, conforme mostrado no exemplo de código SampleApp.exe anterior. 

  As alterações introduzidas no atividade personalizada do Azure Data Factory V2, está livre para escrever a lógica de código personalizado no seu idioma preferido e execute-los no Windows e sistemas de operativos Linux suportados pelo Azure Batch. 

  Se tiver existente .net código escrito para V1 atividade do DotNet (personalizado), tem de modificar o código para os mesmos trabalhar com a atividade personalizada de V2 com as seguintes diretrizes de alto nível:  

  > - Altere o projeto de um .net biblioteca de classes para uma aplicação de consola. 
  > - Iniciar a aplicação com o método principal, o método de execução da IDotNetActivity interface já não é necessário. 
  > - Ler e analisar os serviços ligados, conjuntos de dados e a atividade com o serializador JSON em vez de objetos com tipo seguro para passar os valores das propriedades necessárias para a lógica principal código personalizado. Consulte a precedente SampleApp.exe código como um exemplo. 
  > - O objeto de registo já não é suportado, saídas executeable pode ser impressão para a consola e é guardado stdout.txt. 
  > - Pacote Microsoft.Azure.Management.DataFactories NuGet já não é necessária. 
  > - Compilar o código, carregar o executável e dependências para o Storage do Azure e definir o caminho na propriedade folderPath. 

## <a name="auto-scaling-of-azure-batch"></a>Dimensionamento automático do Azure Batch
Também pode criar um conjunto do Azure Batch com **dimensionamento automático** funcionalidade. Por exemplo, pode criar um conjunto do batch do azure com VMs dedicadas 0 e uma fórmula de dimensionamento automático com base no número de tarefas pendentes. 

A fórmula de exemplo aqui distribui o comportamento seguinte: quando o conjunto for criado inicialmente, começa com 1 VM. Métrica de $PendingTasks define o número de tarefas em execução + Active Directory (em fila) Estado.  A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated em conformidade. Garante que nunca chegam TargetDedicated para além de 25 VMs. Por isso, como novas tarefas são submetidas, agrupamento automaticamente o crescimentos e as tarefas são concluídas, VMs fiquem livre um por um e o dimensionamento automático diminui dessas VMs. startingNumberOfVMs e maxNumberofVMs pode ser ajustado para as suas necessidades.

Fórmula de dimensionamento automático:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [automaticamente Dimensionar nós de computação num conjunto do Azure Batch](../batch/batch-automatic-scaling.md) para obter mais detalhes.

Se o agrupamento está a utilizar a predefinição [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), o serviço Batch pode demorar 15-30 minutos para preparar a VM antes de executar a atividade personalizada.  Se o agrupamento está a utilizar um autoScaleEvaluationInterval diferentes, o serviço Batch pode demorar autoScaleEvaluationInterval + 10 minutos.


## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do ramo de registo](transform-data-using-hadoop-hive.md)
* [Atividade do PIg](transform-data-using-hadoop-pig.md)
* [Atividade de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade de execução de lote de aprendizagem máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
