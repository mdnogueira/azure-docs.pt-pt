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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 24a4255a23f0b9b9da5d8c3cefeefb8fe250f2f1
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-copy-data-between-on-premises-and-cloud"></a>Tutorial: Copiar dados entre o local e a cloud
Neste tutorial, vai utilizar o Azure PowerShell para criar um pipeline do Data Factory que copia dados de uma base de dados do SQL Server no local para um armazenamento de Blobs do Azure. Vai criar e utilizar um integration runtime (IR) autoalojado do Azure Data Factory, que permite a integração de arquivos de dados no local e de arquivos de dados na cloud.  Para saber mais sobre como utilizar outras ferramentas/SDKs para criar uma fábrica de dados, veja [Inícios rápidos](quickstart-create-data-factory-dot-net.md).

Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md). 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja a [documentação da versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e dos Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 ou 2016. 
Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de **origem**. Crie uma tabela com o nome **emp** na sua base de dados do SQL Server e insira duas entradas de exemplo na mesma.

1. Inicie o **SQL Server Management Studio**. Se estiver a utilizar o SQL Server 2016, poderá ter de instalar o SQL Server Management Studio separadamente, a partir do [centro de transferências](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Ligue ao SQL Server com as suas credenciais. 
3. Crie uma base de dados de exemplo. Na vista de árvore, clique com o botão direito do rato em **Bases de dados** e clique em **Nova Base de Dados**. Na caixa de diálogo **Nova Base de Dados**, introduza um **nome** para a base de dados e clique em **OK**. 
4. Execute o script de consulta seguinte na base de dados, o que cria a tabela **emp**. Na vista de árvore, clique com o botão direito do rato na **base de dados** que criou e clique em **Nova Consulta**. 

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
2. Execute os comandos seguintes na base de dados, que inserem alguns dados de exemplo na tabela:

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
É utilizada uma Conta de Armazenamento do Azure para fins gerais (especificamente o Armazenamento de Blobs) como um arquivo de dados de **destino/sink** neste tutorial. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account), que explica como criar uma.

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave do nome da sua conta de armazenamento do Azure neste início rápido. O procedimento seguinte disponibiliza os passos para obter o nome e a chave da conta de armazenamento. 

1. Abra um browser e navegue para o [portal do Azure](https://portal.azure.com). Inicie sessão com o seu nome de utilizador e a sua palavra-passe do Azure. 
2. Clique em **Mais serviços >**, no menu do lado esquerdo, filtre com a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Procurar contas de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Na lista das contas de armazenamento, filtre para encontrar a sua conta de armazenamento (se necessário) e selecione **a sua conta de armazenamento**. 
4. Na página **Conta de armazenamento**, selecione **Chaves de acesso**, no menu.

    ![Obter o nome e a chave da conta de armazenamento](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copie os valores dos campos **Nome da conta de armazenamento** e **Key1** para a área de transferência. Cole-os num bloco de notas ou noutro editor e guarde-os. Vai utilizar o nome e a chave da conta de armazenamento no tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contentor adftutorial 
Nesta secção, vai criar um contentor de blobs com o nome “adftutorial” no seu armazenamento de blobs do Azure. 

1. Instale o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), se não o tiver no seu computador. 
2. Inicie o **Explorador de Armazenamento do Microsoft Azure** no seu computador.   
3. Na janela **Ligar ao Armazenamento do Azure**, selecione **Utilizar o nome e a chave de uma conta de armazenamento** e clique em **Seguinte**. Se não vir a janela **Ligar ao Armazenamento do Azure**, clique com o botão direito do rato em **Contas de Armazenamento** na vista de árvore e clique em **Ligar ao armazenamento do Azure**. 

    ![Ligar ao armazenamento do Azure](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. Na janela **Anexar com Nome e Chave**, cole o **Nome da conta** e a **Chave da conta** que guardou no passo anterior. Clique depois em **Seguinte**. 
5. Na janela **Resumo da Ligação**, clique em **Ligar**.
6. Confirme que vê a sua conta de armazenamento na vista de árvore em **(Locais e Anexadas)** -> **Contas de Armazenamento**. 
7. Expanda **Contentores de blobs** e confirme que não existe o contentor de blobs **adftutorial**. Se já existir, ignore os passos seguintes relativos à criação do contentor. 
8. Clique com o botão direito do rato em **Contentores de blobs** e selecione **Criar Contentor de Blobs**.

    ![Criar contentor de blobs](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Introduza **adftutorial** para o nome e prima **ENTER**. 
10. Confirme que o contentor **adftutorial** está selecionado na vista de árvore. O Data Fabric cria automaticamente a pasta de saída neste contentor, pelo que não precisa de a criar. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Instalar o Azure PowerShell
Instale o Azure PowerShell mais recente, se ainda não o tiver no seu computador. 

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

## <a name="create-a-self-hosted-ir"></a>Criar um IR autoalojado

Nesta secção, pode criar um Integration runtime autoalojado e associá-lo a um nó no local (máquina).

1. Crie uma variável para o nome do runtime de integração. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Crie um integration runtime autoalojado. Utilize um nome exclusivo no caso de já existir outro integration runtime com o mesmo nome.

   ```powershell
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
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Instalar o runtime de integração
1. [Transfira](https://www.microsoft.com/download/details.aspx?id=39717) o runtime de integração autoalojado para um computador Windows local e execute a instalação. 
2. Em **Bem-vindo ao Assistente de Configuração do Microsoft Integration Runtime** , clique em **Seguinte**.  
3. Na página **Contrato de Licença do Utilizador Final** , aceite os termos e o contrato de licença e clique em **Seguinte**. 
4. Na página **Pasta de Destino**, clique em **Seguinte**. 
5. Em **Pronto para instalar o Microsoft Integration Runtime**, clique em **Instalar**. 
6. Se vir uma mensagem de erro que diz que o computador está a ser configurado para entrar no modo de suspensão ou hibernação quando não estiver a ser utilizado, clique em **OK**. 
7. Na página **Concluiu o Assistente de Configuração do Microsoft Integration Runtime**, clique em **Concluir**.
8. Na página **Registar o Integration Runtime (Autoalojado)**, cole a chave que guardou na secção anterior e clique em **Registar**. 

   ![Registar o integration runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Quando o integration runtime autoalojado for registado com êxito, verá a mensagem seguinte:

   ![Registado com êxito](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. Na página **Novo Nó de Integration Runtime (Autoalojado)**, clique em **Seguinte**. 

    ![Página Novo Nó de Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. No **Canal de Comunicação da Intranet**, clique em **Ignorar**. Pode selecionar a certificação TLS/SSL para proteger a comunicação intra-nós num ambiente de runtime de integração de vários nós. 

    ![Página de cala de comunicação da Intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. Na página **Registar Integration Runtime (Autoalojado)**, clique em **Configuration Manager**. 
6. Quando o nó for ligado ao serviço cloud, verá a página seguinte:

   ![O nó está ligado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Criar serviços ligados

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Criar um serviço ligado do Armazenamento do Azure (destino/sink)

1. Crie um ficheiro JSON com o nome **AzureStorageLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: crie a pasta ADFv2Tutorial, caso ainda não exista.  

    > [!IMPORTANT]
    > Substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e chave da sua conta de armazenamento do Azure antes de guardar o ficheiro.

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

1. Crie um ficheiro JSON com o nome **SqlServerLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: substitua **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** e **&lt;password>** pelos valores do seu SQL Server antes de guardar o ficheiro. 

    > [!IMPORTANT]
    > Substitua **&lt;integration** **runtime** **name>** pelo nome do seu integration runtime.

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
2. Para encriptar os dados confidenciais do payload JSON no runtime de integração autoalojado no local, execute **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** e transmita o payload JSON acima. Esta encriptação garante que as credenciais são encriptadas com a interface DPAPI (Data Protection Application Programming Interface). As credenciais encriptadas são armazenadas no nó do runtime de integração autoalojado localmente (computador local). O payload de saída pode ser redirecionado para outro ficheiro JSON (neste caso, «encryptedLinkedService.json») que contém credenciais encriptadas.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Execute o seguinte comando utilizando o JSON do passo anterior para criar o **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar os conjuntos de dados de entrada e de saída que representam os dados de entrada e de saída da operação de cópia (base de dados do SQL Server no local => armazenamento de blobs do Azure).

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
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída com o nome `fromonprem` no contentor de blobs `adftutorial`. Confirme que consegue ver o ficheiro **dbo.emp.txt** na pasta de saída. Utilize o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se a saída é criada. 

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e dos Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Para obter uma lista dos arquivos de dados que o Azure Data Factory suporta, veja [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) (Arquivos de Dados suportados).

Avance para o tutorial seguinte para saber como copiar dados em massa de uma origem para um destino:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy.md)
