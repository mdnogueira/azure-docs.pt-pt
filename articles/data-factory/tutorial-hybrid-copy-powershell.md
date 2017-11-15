---
title: Copiar dados no local para a cloud com o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de um arquivo de dados no local para a cloud do Azure através do integration runtime autoalojado no Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: jingwang
ms.openlocfilehash: 12ead91f6729aa3eb631cc453180ddea9bafe3df
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>Copiar dados entre o local e a cloud

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>Este tutorial

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja a [documentação da versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Neste tutorial, vai utilizar o Azure PowerShell para criar um pipeline do Data Factory que copia dados de uma base de dados do SQL Server no local para um armazenamento de Blobs do Azure. Vai criar e utilizar um integration runtime (IR) autoalojado do Azure Data Factory, que permite a integração de arquivos de dados no local e de arquivos de dados na cloud.  Para saber mais sobre como utilizar outras ferramentas/SDKs para criar uma fábrica de dados, veja [Inícios rápidos](quickstart-create-data-factory-dot-net.md).

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar o integration runtime autoalojado.
> * Criar e encriptar um serviço ligado do SQL Server no local no Integration runtime autoalojado.
> * Criar o serviço ligado do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e do Armazenamento do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e a execução de atividade.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **SQL Server**. Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de **origem**.
* **Conta de Armazenamento do Azure**. Neste tutorial, vai utilizar o armazenamento de blobs do Azure como um arquivo de dados de **destino/sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter os passos para criar uma.
* **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.

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
2. Execute o cmdlet **Set-AzureRmDataFactoryV2** para criar uma fábrica de dados. Substitua os marcadores de posição pelos seus próprios valores antes de executar o comando.

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Tenha em atenção os seguintes pontos:

    * O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Para criar instâncias do Data Factory, tem de ser Contribuidor ou Administrador da subscrição do Azure.
    * Atualmente, o Data Factory V2 só permite criar fábricas de dados nas regiões E.U.A. Leste, E.U.A. Leste 2 e Europa Ocidental. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

## <a name="create-a-self-hosted-ir"></a>Criar um IR autoalojado

Nesta secção, pode criar um Integration runtime autoalojado e associá-lo a um nó no local (máquina).

1. Crie um integration runtime autoalojado. Utilize um nome exclusivo no caso de já existir outro integration runtime com o mesmo nome.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Segue-se o resultado do exemplo:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Execute o seguinte comando para obter o estado do integration runtime criado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Segue-se o resultado do exemplo:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Execute o seguinte comando para obter as chaves de autenticação para registar o integration runtime autoalojado no serviço Data Factory na cloud. Copie uma das chaves para registar o integration runtime autoalojado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Segue-se o resultado do exemplo:

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Transfira](https://www.microsoft.com/download/details.aspx?id=39717) o Integration runtime autoalojado num computador Windows local e utilize a Chave de Autenticação obtida no passo anterior para registar manualmente o integration runtime autoalojado.

   ![Registar o integration runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   Quando o integration runtime autoalojado for registado com êxito, verá a mensagem seguinte:

   ![Registado com êxito](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   Quando o nó for ligado ao serviço cloud, verá a página seguinte:

   ![O nó está ligado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Criar serviços ligados

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Criar um serviço ligado do Armazenamento do Azure (destino/sink)

1. Crie um ficheiro JSON com o nome **AzureStorageLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo. Se ainda não existir, crie a pasta ADFv2Tutorial.  Substitua &lt;accountname&gt; e &lt;accountkey&gt; pelo nome e chave da sua conta de Armazenamento do Azure.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. No **Azure PowerShell**, mude para a pasta **ADFv2Tutorial**.

   Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço ligado: **AzureStorageLinkedService**. Os cmdlets utilizados neste tutorial aceitam valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. Em alternativa, pode passar o objeto **DataFactory** devolvido pelo cmdlet Set-AzureRmDataFactoryV2 sem escrever ResourceGroupName e DataFactoryName sempre que executar um cmdlet.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Eis uma saída de exemplo:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Criar e encriptar um serviço ligado do SQL Server (origem)

1. Crie um ficheiro JSON com o nome **SqlServerLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: substitua **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** e **&lt;password>** pelos valores do seu SQL Server antes de guardar o ficheiro. Substitua **&lt;integration** **runtime** **name>** pelo nome do seu integration runtime.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Para encriptar os dados confidenciais do payload JSON no runtime de integração autoalojado no local, podemos executar **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** e transmitir o payload JSON acima. Esta encriptação garante que as credenciais são encriptadas com a interface DPAPI (Data Protection Application Programming Interface) e armazenadas no nó do integration runtime autoalojado localmente. O payload de saída pode ser redirecionado para outro ficheiro JSON (neste caso, «encryptedLinkedService.json») que contém credenciais encriptadas.

    Substitua **&lt;integration runtime name&gt;** pelo nome do seu integration runtime antes de executar o comando.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Execute o seguinte comando utilizando o JSON do passo anterior para criar o **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Criar conjuntos de dados

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Preparar um SQL Server no local para o tutorial

Neste passo, vai criar os conjuntos de dados de entrada e de saída que representam os dados de entrada e de saída da operação de cópia (base de dados do SQL Server no local => armazenamento de blobs do Azure). Antes de criar os conjuntos de dados, execute os seguintes passos (os passos detalhados são apresentados a seguir à lista):

- Crie uma tabela com o nome **emp** na Base de Dados do SQL Server que adicionou como um serviço ligado à fábrica de dados e insira algumas entradas de exemplo na tabela.
- Crie um contentor de blobs com o nome **adftutorial** na conta de armazenamento de blobs do Azure que adicionou como um serviço ligado à fábrica de dados.


1. Na base de dados que especificou para o serviço ligado do SQL Server no local (**SqlServerLinkedService**), utilize o seguinte script SQL para criar a tabela **emp** na base de dados.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Insira alguns exemplos na tabela:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para a Base de Dados SQL de origem

1. Crie um ficheiro JSON com o nome **SqlServerDataset.json** na pasta **C:\ADFv2Tutorial**, com o seguinte conteúdo:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Para criar o conjunto de dados: **SqlServerDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Criar um conjunto de dados para o Armazenamento de Blobs do Azure de sink

1. Crie um ficheiro JSON com o nome **AzureBlobDataset.json** na pasta **C:\ADFv2Tutorial**, com o seguinte conteúdo:

    > [!IMPORTANT]
    > Este código de exemplo parte do princípio de que tem um contentor com o nome **adftutorial** no Armazenamento de Blobs do Azure.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Para criar o conjunto de dados: **AzureBlobDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Criar pipelines

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Criar o pipeline "SqlServerToBlobPipeline"

1. Crie um ficheiro JSON com o nome **SqlServerToBlobPipeline.json** na pasta **C:\ADFv2Tutorial**, com o seguinte conteúdo:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Para criar o pipeline: **SQLServerToBlobPipeline**, execute o cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Iniciar e monitorizar uma execução de pipeline

1. Inicie uma execução de pipeline para o pipeline "SQLServerToBlobPipeline" e capture o ID de execução do pipeline para monitorização futura.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Execute o seguinte script para verificar continuamente o estado de execução do pipeline "**SQLServerToBlobPipeline**" e imprima o resultado final.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Eis o resultado da execução de exemplo:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Pode obter o ID de execução do pipeline "**SQLServerToBlobPipeline**" e verificar o resultado da execução de atividade detalhada da seguinte forma.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Eis o resultado da execução de exemplo:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Ligue ao seu armazenamento de Blobs do Azure de sink e confirme se os dados foram copiados corretamente da Base de Dados SQL do Azure.

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar o Integration runtime autoalojado.
> * Criar e encriptar um serviço ligado do SQL Server no local no Integration runtime autoalojado.
> * Criar o serviço ligado do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e do Armazenamento do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e a execução de atividade.

Veja o artigo [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) para obter uma lista dos arquivos de dados suportados pelo Azure Data Factory como origens e sinks.

Avance para o tutorial seguinte para saber como copiar dados em massa de uma origem para um destino:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy.md)
