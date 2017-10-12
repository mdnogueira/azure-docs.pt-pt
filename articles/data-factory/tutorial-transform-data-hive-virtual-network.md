---
title: Transformar dados com o Hive na Rede Virtual do Azure | Microsoft Docs
description: "Este tutorial fornece instruções passo a passo para transformar dados através da atividade do Hive no Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/10/2017
ms.author: shengc
ms.openlocfilehash: ec472ea9f45a4c8bc18d065001eae381a275d185
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformar dados na Rede Virtual do Azure com a atividade do Hive no Azure Data Factory
O Azure Data Factory é um serviço de integração de dados baseado na cloud que lhe permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI). 

Neste tutorial, vai utilizar o Azure PowerShell para criar um pipeline do Data Factory que transforma os dados com a Atividade do Hive num cluster HDInsight que se encontra numa Rede Virtual do Azure. Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e configurar o integration runtime autoalojado.
> * Criar e implementar serviços ligados.
> * Criar e implementar um pipeline que contém uma atividade do Hive.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline. 
> * Verificar a saída. 


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
- **Conta de Armazenamento do Azure**. Tem de criar um script do Hive e carregá-lo para o armazenamento do Azure. A saída do script do Hive é armazenada nesta conta de armazenamento. Neste exemplo, o cluster HDInsight utiliza esta conta de Armazenamento do Azure como armazenamento primário. 
- **Rede Virtual do Azure.** Se não tiver uma rede virtual do Azure, crie-a seguindo [estas instruções](../virtual-network/virtual-network-get-started-vnet-subnet.md). Neste exemplo, o HDInsight está numa Rede Virtual do Azure. Eis um exemplo de configuração da Rede Virtual do Azure. 

    ![Criar a rede virtual](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **Cluster HDInsight.** Crie um cluster HDInsight e associe-o à rede virtual que criou no passo anterior, seguindo este artigo: [Extend Azure HDInsight using an Azure Virtual Network (Expandir o Azure HDInsight com uma Rede Virtual do Azure)](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Eis um exemplo de configuração do HDInsight numa rede virtual. 

    ![HDInsight numa rede virtual](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-azurerm-ps).

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Carregar o script do Hive para uma conta de Armazenamento de Blobs

1. Crie um ficheiro SQL do Hive com o nome **hivescript.hql** com o seguinte conteúdo:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. No Armazenamento de Blobs do Azure, crie um contentor com o nome **adftutorial**, caso ainda não exista.
3. Crie uma pasta com o nome `hivescripts`.
4. Carregue o ficheiro `hivescript.hql` para a subpasta `hivescripts`.

 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados


1. Defina as variáveis uma a uma.

    ```powershell
    $subscriptionID = "<subscription ID>" # Your Azure subscription ID
    $resourceGroupName = "ADFTutorialResourceGroup" # Name of the resource group
    $dataFactoryName = "MyDataFactory09142017" # Globally unique name of the data factory
    $pipelineName = "MyHivePipeline" # Name of the pipeline
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" # make it a unique name. 
    ```
2. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste início rápido. Se o fechar e reabrir, terá de executar os comandos novamente.

    Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzureRmSubscription
    ```
    Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **SubscriptionId** pelo ID da sua subscrição do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Crie o grupo de recursos: ADFTutorialResourceGroup, caso ainda não exista na sua subscrição. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Crie a fábrica de dados. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Execute o seguinte comando para ver a saída: 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>Criar o IR autoalojado
Nesta secção, vai criar um integration runtime autoalojado e associá-lo a uma VM do Azure na mesma Rede Virtual do Azure onde o cluster HDInsight se encontra.

1. Crie o integration runtime autoalojado. Utilize um nome exclusivo no caso de já existir outro integration runtime com o mesmo nome.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Este comando cria um registo lógico do integration runtime autoalojado. 
2. Utilize o PowerShell para obter chaves de autenticação para registar o integration runtime autoalojado. Copie uma das chaves para registar o integration runtime autoalojado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Segue-se o resultado do exemplo: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Tome nota do valor de **AuthKey1** sem a aspa. 
3. Crie uma VM do Azure e associe-a à mesma rede virtual que contém o cluster HDInsight. Para obter mais detalhes, veja [Como criar máquinas virtuais](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vms). Associe-as a uma Rede Virtual do Azure. 
4. Na VM do Azure, transfira o [integration runtime autoalojado](https://www.microsoft.com/download/details.aspx?id=39717). Utilize a Chave de Autenticação que obteve no passo anterior para registar manualmente o integration runtime autoalojado. 

   ![Registar o integration runtime](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Quando o integration runtime autoalojado for registado com êxito, verá a mensagem seguinte: ![Registado com êxito](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   Quando o nó for ligado ao serviço cloud, verá a página seguinte: ![O nó está ligado](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>Criar serviços ligados

Nesta secção, vai criar e implementar dois Serviços Ligados:
- Um Serviço Ligado do Armazenamento do Azure que liga uma conta de Armazenamento do Azure à fábrica de dados. Este armazenamento é o armazenamento primário utilizado pelo cluster HDInsight. Neste caso, também utilizamos esta conta de Armazenamento do Azure para manter o script do Hive e a saída do script.
- Um Serviço Ligado do HDInsight. O Azure Data Factory submete o script do Hive a este cluster HDInsight para execução.

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

Crie um ficheiro JSON com o seu editor preferencial, copie a seguinte definição JSON de um serviço ligado do Armazenamento do Azure e, em seguida, guarde o ficheiro como **MyStorageLinkedService.json**.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Substitua **&lt;accountname&gt; e &lt;accountkey&gt;** pelo nome e chave da sua conta de Armazenamento do Azure.

### <a name="hdinsight-linked-service"></a>Serviço ligado do HDInsight

Crie um ficheiro JSON com o seu editor preferencial, copie a seguinte definição JSON de um serviço ligado do Azure HDInsight e guarde o ficheiro como **MyHDInsightLinkedService.json**.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Atualize os valores para as seguintes propriedades na definição de serviço ligado:

- **userName**. Nome do utilizador de início de sessão do cluster que especificou quando criou o cluster. 
- **password**. A palavra-passe do utilizador.
- **clusterUri**. Especifique o URL do cluster HDInsight no formato https://<clustername>.azurehdinsight.net.  Este artigo pressupõe que tem acesso ao cluster através da Internet. Por exemplo, que pode ligar ao cluster em `https://clustername.azurehdinsight.net`. Este endereço utiliza o gateway público, que não está disponível se tiver utilizado grupos de segurança de rede (NSGs) ou rotas definidas pelo utilizador (UDRs) para restringir o acesso a partir da Internet. Para que o Data Factory consiga submeter tarefas ao cluster HDInsight na Rede Virtual do Azure, precisa de configurar a Rede Virtual do Azure de forma a que o URL possa ser resolvido para o endereço IP privado do gateway utilizado pelo HDInsight.

  1. A partir do portal do Azure, abra a Rede Virtual onde o HDInsight se encontra. Abra a interface de rede com o nome começado por `nic-gateway-0`. Tome nota do endereço IP privado. Por exemplo, 10.6.0.15. 
  2. Se a sua Rede Virtual do Azure tiver um servidor DNS, atualize o registo DNS de forma a que o URL do cluster HDInsight `https://<clustername>.azurehdinsight.net` possa ser resolvido para `10.6.0.15`. Esta é a abordagem recomendada. Se não tiver um servidor DNS na sua Rede Virtual do Azure, pode solucionar temporariamente este problema, editando o ficheiro de anfitriões (C:\Windows\System32\drivers\etc) de todas as VMs registadas como nós de integration runtime autoalojado, adicionando uma entrada semelhante a: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

Mude para a pasta onde criou os ficheiros JSON e execute o seguinte comando para implementar os serviços ligados: 


```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"

Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDILinkedService" -File "MyHDILinkedService.json"
```

## <a name="author-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um novo pipeline com uma atividade do Hive. A atividade executa o script do Hive para devolver dados de uma tabela de exemplo e guardá-los no caminho que definiu. Crie um ficheiro JSON no seu editor preferencial, copie a seguinte definição de JSON de uma definição de pipeline e guarde-a como **MyHiveOnDemandPipeline.json**.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

Tenha em atenção os seguintes pontos:

- **scriptPath** aponta para o caminho do script do Hive na Conta de Armazenamento do Azure que utilizou para MyStorageLinkedService. O caminho é sensível a maiúsculas e minúsculas.
- **Output** é um argumento utilizado no script do Hive. Utilize o formato `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` para apontá-lo para uma pasta existente no seu Armazenamento do Azure. O caminho é sensível a maiúsculas e minúsculas. 

Mude para a pasta onde criou os ficheiros JSON e execute o seguinte comando para implementar o pipeline: 


```powershell
Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>Iniciar o pipeline 

1. Iniciar uma execução de pipeline. Também captura o ID de execução do pipeline para monitorização futura.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até terminar.

    ```powershell
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

   Eis o resultado da execução de exemplo:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Verifique na pasta `outputfolder` a existência do novo ficheiro criado como o resultado da consulta do Hive. Deverá ser semelhante à saída de exemplo seguinte: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e configurar o integration runtime autoalojado.
> * Criar e implementar serviços ligados.
> * Criar e implementar um pipeline que contém uma atividade do Hive.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline. 
> * Verificar a saída. 

Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Ramificação e encadeamento de um fluxo de controlo no Data Factory](tutorial-control-flow.md)



