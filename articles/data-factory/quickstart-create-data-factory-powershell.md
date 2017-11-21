---
title: Copiar os dados no Armazenamento de Blobs com o Azure Data Factory | Microsoft Docs
description: "Crie uma fábrica de dados do Azure para copiar dados de uma pasta para outra pasta num Armazenamento de Blobs do Azure para outra localização no mesmo Armazenamento de Blobs."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 8ee2f48db009da4660a03f91194c4e99f6ecac4a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Criar uma fábrica de dados do Azure com o PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 - Pré-visualização](quickstart-create-data-factory-powershell.md)

Este início rápido descreve como utilizar o PowerShell para criar uma fábrica de dados do Azure. O pipeline que criar nesta fábrica de dados copia dados de uma pasta para outra pasta num armazenamento de blobs do Azure. Para ver um tutorial sobre como transformar dados com o Azure Data Factory, veja [Tutorial: Transform data using Spark](transform-data-using-spark.md) (Tutorial: Transformar dados com Spark). 

Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja a [introdução à versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
É utilizada uma Conta de Armazenamento do Azure para fins gerais (especificamente o Armazenamento de Blobs) como um arquivo de dados **de origem** e **sink/destino** neste início rápido. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account), que explica como criar uma. 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave da sua conta de armazenamento do Azure neste início rápido. O procedimento seguinte disponibiliza os passos para obter o nome e a chave da conta de armazenamento. 

1. Abra um browser e navegue para o [portal do Azure](https://portal.azure.com). Inicie sessão com o seu nome de utilizador e a sua palavra-passe do Azure. 
2. Clique em **Mais serviços >**, no menu do lado esquerdo, filtre com a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Procurar contas de armazenamento](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. Na lista das contas de armazenamento, filtre para encontrar a sua conta de armazenamento (se necessário) e selecione **a sua conta de armazenamento**. 
4. Na página **Conta de armazenamento**, selecione **Chaves de acesso**, no menu.

    ![Obter o nome e a chave da conta de armazenamento](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Copie os valores dos campos **Nome da conta de armazenamento** e **Key1** para a área de transferência. Cole-os num bloco de notas ou noutro editor e guarde-os.  

#### <a name="create-input-folder-and-files"></a>Criar pasta e ficheiros de entrada
Nesta secção, vai criar um contentor de blobs com o nome “adftutorial” no seu armazenamento de blobs do Azure. Depois, vai criar uma pasta chamada “input” no contentor e carrega um ficheiro de exemplo para essa pasta. 

1. Instale o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), se não o tiver no seu computador. 
2. Inicie o **Explorador de Armazenamento do Microsoft Azure** no seu computador.   
3. Na janela **Ligar ao Armazenamento do Azure**, selecione **Utilizar o nome e a chave de uma conta de armazenamento** e clique em **Seguinte**. Se não vir a janela **Ligar ao Armazenamento do Azure**, clique com o botão direito do rato em **Contas de Armazenamento** na vista de árvore e clique em **Ligar ao armazenamento do Azure**. 

    ![Ligar ao armazenamento do Azure](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. Na janela **Anexar com Nome e Chave**, cole o **Nome da conta** e a **Chave da conta** que guardou no passo anterior. Clique depois em **Seguinte**. 
5. Na janela **Resumo da Ligação**, clique em **Ligar**.
6. Confirme que vê a sua conta de armazenamento na vista de árvore em **(Locais e Anexadas)** -> **Contas de Armazenamento**. 
7. Expanda **Contentores de blobs** e confirme que o contentor de blobs **adftutorial** não existe. Se já existir, ignore os passos seguintes relativos à criação do contentor. 
8. Clique com o botão direito do rato em **Contentores de blobs** e selecione **Criar Contentor de Blobs**.

    ![Criar contentor de blobs](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. Introduza **adftutorial** para o nome e prima **ENTER**. 
10. Confirme que o contentor **adftutorial** está selecionado na vista de árvore. 
11. Clique em **Nova Pasta** na barra de ferramentas. 

    ![Botão criar pasta](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. Na janela **Criar Novo Diretório Virtual**, introduza **input** em **Nome** e clique em **OK**. 

    ![Caixa de diálogo Criar Diretório](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. Inicie o **Bloco de Notas** e crie um ficheiro com o nome **emp.txt** com os conteúdos seguintes: 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    Guarde-o na pasta **c:\ADFv2QuickStartPSH**: crie a pasta **ADFv2QuickStartPSH**, se ainda não existir. 
14. Clique no botão **Carregar**, na barra de ferramentas, e selecione **Carregar Ficheiros**. 

    ![Botão Carregar](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. Na janela **Carregar ficheiros**, em **Ficheiros**, selecione `...`. 
16. Na janela **Selecionar pasta a carregar**, navegue para a pasta com **emp.txt** e selecione o ficheiro. 

    ![Caixa de diálogo Carregar ficheiros](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. Na janela **Carregar ficheiros**, clique em **Carregar**. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Instalar o Azure PowerShell
Instale o Azure PowerShell mais recente, se não o tiver no seu computador. 

1. No browser, navegue para a página [Transferir SDKs e Ferramentas](https://azure.microsoft.com/downloads/). 
2. Clique em **Instalação para Windows**, na secção **Ferramentas de linha de comandos** -> **PowerShell**. 
3. Para instalar o Azure PowerShell, execute o ficheiro **MSI**. 

Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell
Inicie o **PowerShell** no seu computador. Mantenha o Azure PowerShell aberto até ao fim deste início rápido. Se o fechar e reabrir, terá de executar os comandos novamente.

1. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe do Azure que utiliza para iniciar sessão no portal do Azure.
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Se tiver várias subscrições do Azure, execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **SubscriptionId** pelo ID da sua subscrição do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com aspas duplas e execute o comando. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Defina uma variável para o nome da fábrica de dados que possa utilizar em comandos do PowerShell mais tarde. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Defina uma variável para a localização da fábrica de dados: 

    ```powershell
    $location = "East US"
    ```
4. Para criar o grupo de recursos do Azure, execute o comando abaixo: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$resourceGroupName` e tente novamente. Se quiser partilhar o grupo de recursos com outros, avance para o passo seguinte. 
5. Para criar a fábrica de dados, execute o cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para criar instâncias do Data Factory, tem de ser **contribuidor** ou **administrador** da subscrição do Azure.
* Atualmente, a versão 2 do Data Factory só permite criar fábricas de dados nas regiões E.U.A. Leste, E.U.A. Leste 2 e Europa Ocidental. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

## <a name="create-a-linked-service"></a>Criar um serviço ligado

Crie os serviços ligados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste início rápido, só precisa de criar um serviço ligado do Armazenamento do Azure para ser utilizado como arquivo de origem e de sink, que tem o nome "AzureStorageLinkedService" neste exemplo.

1. Crie um ficheiro JSON com o nome **AzureStorageLinkedService.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo: (crie a pasta ADFv2QuickStartPSH, caso ainda não exista.). 

    > [!IMPORTANT]
    > Substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e chave da sua conta de armazenamento do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. No **Azure PowerShell**, mude para a pasta **ADFv2QuickStartPSH**.

3. Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço ligado: **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Defina um conjunto de dados que represente os dados a copiar de uma origem para um sink. Neste exemplo, este conjunto de dados de Blobs refere-se ao serviço ligado do Armazenamento do Azure que criou no passo anterior. O conjunto de dados assume um parâmetro cujo valor é definido numa atividade que consome o conjunto de dados. O parâmetro é utilizado para criar o **folderPath** que aponta para a localização onde os dados residem ou estão armazenados.

1. Crie um ficheiro JSON com o nome **BlobDataset.json** na pasta **C:\ADFv2QuickStartPSH**, com o seguinte conteúdo:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Para criar o conjunto de dados: **BlobDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
  
Neste exemplo, este pipeline contém uma atividade e assume dois parâmetros: o caminho do blob de entrada e o caminho do blob de saída. Os valores destes parâmetros são definidos quando o pipeline é acionado/executado. A atividade de cópia utiliza o mesmo conjunto de dados de blobs criado no passo anterior como entrada e saída. Quando o conjunto de dados é utilizado como conjunto de dados de entrada, o caminho de entrada é especificado. Da mesma forma, quando o conjunto de dados é utilizado como conjunto de dados de saída, o caminho de saída é especificado. 

1. Crie um ficheiro JSON com o nome **Adfv2QuickStartPipeline.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Para criar o pipeline: **Adfv2QuickStartPipeline**, execute o cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Segue-se o resultado do exemplo:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Neste passo, vai definir os valores para os parâmetros do pipeline:  **inputPath** e **outputPath** com valores reais dos caminhos dos blobs de origem e de sink. Em seguida, vai criar uma execução de pipeline através destes argumentos. 

1. Crie um ficheiro JSON com o nome **PipelineParameters.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    Substitua o valor de **inputPath** e **outputPath** pelos caminhos dos blobs de origem e de sink, se estiver a utilizar contentores e pastas diferentes.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```

2. Execute o cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** para criar uma execução de pipeline e introduzir os valores dos parâmetros. Também captura o ID de execução do pipeline para monitorização futura.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

1. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    Eis a saída de exemplo da execução de pipeline:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. Execute o script seguinte para obter os detalhes da execução da atividade de cópia, como, por exemplo, o tamanho dos dados lidos/escritos.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Confirme se vê uma saída semelhante à seguinte saída de exemplo de resultado da execução de atividade:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída no contentor de blobs adftutorial. Em seguida, copia o ficheiro emp.txt da pasta de entrada para a pasta de saída. Utilize o [explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs em inputBlobPath são copiados para outputBlobPath. 

## <a name="clean-up-resources"></a>Limpar recursos
Pode limpar os recursos que criou no Guia de Introdução de duas formas. Pode eliminar o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se quiser manter os outros recursos intactos, elimine apenas a fábrica de dados que criou neste tutorial.

Execute o seguinte comando para eliminar todo o grupo de recursos: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Execute o seguinte comando para eliminar apenas a fábrica de dados: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários. 
