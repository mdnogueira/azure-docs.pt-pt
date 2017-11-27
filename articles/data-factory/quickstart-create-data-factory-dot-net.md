---
title: "Criar uma fábrica de dados do Azure com .NET | Microsoft Docs"
description: "Crie uma fábrica de dados do Azure para copiar dados de uma localização num Armazenamento de Blobs do Azure para outra localização no mesmo Armazenamento de Blobs."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: jingwang
ms.openlocfilehash: 5345c0fa6212127e9821adccc8cb4c339ce7ae28
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Criar uma fábrica de dados e um pipeline com o SDK .NET
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 - Pré-visualização](quickstart-create-data-factory-dot-net.md)

Este início rápido descreve como utilizar o .NET SDK para criar uma fábrica de dados do Azure. O pipeline que criar nesta fábrica de dados **copia** dados de uma pasta para outra pasta num armazenamento de blobs do Azure. Para ter acesso a um tutorial sobre como **transformar** dados com o Azure Data Factory, veja [Tutorial: Transformar dados com o Spark](transform-data-using-spark.md). 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja a [introdução à versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser um membro das funções **contribuidor** ou **proprietário**, ou um **administrador** da subscrição do Azure. No portal do Azure, clique no seu **nome de utilizador** no canto superior direito e selecione **Permissões** para ver as permissões que tem na subscrição. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja o artigo [Add roles](../billing/billing-add-change-azure-subscription-administrator.md) (Adicionar funções).

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Neste início rápido, vai utilizar uma Conta de Armazenamento do Azure para fins gerais (especificamente o Armazenamento de Blobs) como arquivo de dados de **origem** e de **destino**. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account), que explica como criar uma. 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave da sua conta de armazenamento do Azure neste início rápido. O procedimento seguinte disponibiliza os passos para obter o nome e a chave da conta de armazenamento. 

1. Abra um browser e navegue para o [portal do Azure](https://portal.azure.com). Inicie sessão com o seu nome de utilizador e a sua palavra-passe do Azure. 
2. Clique em **Mais serviços >**, no menu do lado esquerdo, filtre com a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Procurar contas de armazenamento](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. Na lista das contas de armazenamento, filtre para encontrar a sua conta de armazenamento (se necessário) e selecione **a sua conta de armazenamento**. 
4. Na página **Conta de armazenamento**, selecione **Chaves de acesso**, no menu.

    ![Obter o nome e a chave da conta de armazenamento](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Copie os valores dos campos **Nome da conta de armazenamento** e **Key1** para a área de transferência. Cole-os num bloco de notas ou noutro editor e guarde-os.  

#### <a name="create-input-folder-and-files"></a>Criar pasta e ficheiros de entrada
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no seu armazenamento de blobs do Azure. Depois, vai criar uma pasta chamada **entrada** no contentor e, em seguida, vai carregar um ficheiro de exemplo para essa pasta. 

1. Na página **Conta de armazenamento**, mude para a **Descrição Geral** e, em seguida, clique em **Blobs**. 

    ![Selecionar a opção Blobs](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. Na página **Serviço Blob** página, clique em **+ Contentor** na barra de ferramentas. 

    ![Adicionar o botão de contentor](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. Na caixa de diálogo **Novo contentor**, introduza **adftutorial** para o nome e clique em **OK**. 

    ![Introduzir o nome do contentor](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. Clique em **adftutorial** na lista de contentores. 

    ![Selecionar o contentor](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. Na página **Contentor**, clique em **Carregar** na barra de ferramentas.  

    ![Botão Carregar](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. Na página **Carregar blob**, clique em **Avançadas**.

    ![Clicar na ligação Avançadas](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Inicie o **Bloco de Notas** e crie um ficheiro chamado **emp.txt** com o seguinte conteúdo: Guarde-o na pasta **c:\ADFv2QuickStartPSH**: Crie a pasta **ADFv2QuickStartPSH** caso esta ainda não exista.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. No portal do Azure, na página **Carregar blob**, procure e selecione o ficheiro **emp.txt** para o campo **Ficheiros**. 
9. Introduza **entrada** como um valor no campo **Carregar para a pasta**. 

    ![Carregar definições de blob](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Confirme se a pasta é **entrada** e se o ficheiro é **emp.txt**, e clique em **Carregar**.
11. Deverá ver o ficheiro **emp.txt** e o estado do carregamento na lista. 
12. Feche a página **Carregar blob** clicando no **X** no canto. 

    ![Fechar a página Carregar blob](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Mantenha a página do **contentor** aberta. Vai utilizá-la para verificar a saída no final deste início rápido.

### <a name="visual-studio"></a>Visual Studio
As instruções neste artigo utilizam o Visual Studio 2017. Também pode utilizar o Visual Studio 2013 ou 2015.

### <a name="azure-net-sdk"></a>SDK do Azure para .NET
Transfira e instale o [SDK do Azure para .NET](http://azure.microsoft.com/downloads/) no seu computador.

### <a name="create-an-application-in-azure-active-directory"></a>Criar uma Aplicação no Azure Active Directory
Siga as instruções apresentadas [neste artigo](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) para realizar as seguintes tarefas: 

1. **Crie uma aplicação no Azure Active Directory**. Crie uma aplicação no Azure Active Directory que represente a aplicação .NET que está a criar neste tutorial. Para o URL de início de sessão, pode fornecer um URL fictício conforme mostrado no artigo (`https://contoso.org/exampleapp`).
2. Obtenha o **ID da aplicação** e a **chave de autenticação*** com base nas instruções da secção **Obter o ID da aplicação e a chave de autenticação** no artigo. Tome nota destes valores que irá utilizar posteriormente neste tutorial. 
3. Obtenha o **ID do inquilino** com base nas instruções da secção **Obter ID do inquilino** no artigo. Tome nota deste valor. 
4. Atribua a aplicação à função **Contribuidor** ao nível da subscrição para que a aplicação possa criar fábricas de dados na subscrição. Siga as instruções apresentadas na secção **Atribuir a aplicação a uma função** do artigo. 

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Com o Visual Studio 2013/2015/2017, crie uma aplicação de consola de C# .NET.

1. Inicie o **Visual Studio**.
2. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**.
3. Selecione **Consola de Aplicação** -> **de Visual C# (.NET Framework)** na lista de tipos de projeto à direita. É necessária a versão 4.5.2 ou superior do .NET.
4. Introduza **ADFv2QuickStart** para o Nome.
5. Clique em **OK** para criar o projeto.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

1. clique em **Ferramentas** -> **Gestor de Pacotes NuGet** -> **Consola de Gestor de Pacotes**.
2. Na **Consola do Gestor de Pacotes**, execute os comandos seguintes para instalar os pacotes:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

    ```

## <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

1. Abra **Program.cs** e inclua as seguintes instruções para adicionar referências aos espaços de nomes.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adicione o código seguinte ao método **Principal** que define as variáveis. Substitua os marcadores de posição pelos seus próprios valores. Atualmente, o Data Factory V2 só permite criar fábricas de dados nas regiões E.U.A. Leste, E.U.A. Leste 2 e Europa Ocidental. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Adicione o código seguinte ao método **Main** que cria uma instância da classe **DataFactoryManagementClient**. Utilize este objeto para criar uma fábrica de dados, um serviço ligado, conjuntos de dados e um pipeline. Também pode utilizar este objeto para monitorizar os detalhes de execução do pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Adicione o código seguinte ao método **Main** que cria uma **fábrica de dados**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Criar um serviço ligado

Adicione o código seguinte ao método **Main** que cria um **serviço ligado do Armazenamento do Azure**.

Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste Início rápido, só precisa de criar um serviço ligado do Armazenamento do Azure para o arquivo de dados de origem e de sink para cópia, que tem o nome "AzureStorageLinkedService" no exemplo.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Adicione o código seguinte ao método **Main** que cria um **conjunto de dados do blob do Azure**.

Defina um conjunto de dados que represente os dados a copiar de uma origem para um sink. Neste exemplo, este conjunto de dados de Blobs refere-se ao serviço ligado do Armazenamento do Azure que criou no passo anterior. O conjunto de dados assume um parâmetro cujo valor é definido numa atividade que consome o conjunto de dados. O parâmetro é utilizado para criar o "folderPath" que aponta para a localização onde os dados residem ou estão armazenados.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Adicione o código seguinte ao método **Main** que cria um **pipeline com uma atividade de cópia**.

Neste exemplo, este pipeline contém uma atividade e assume dois parâmetros: o caminho do blob de entrada e o caminho do blob de saída. Os valores destes parâmetros são definidos quando o pipeline é acionado/executado. A atividade de cópia refere-se ao mesmo conjunto de dados de blobs criado no passo anterior como entrada e saída. Quando o conjunto de dados é utilizado como conjunto de dados de entrada, o caminho de entrada é especificado. Da mesma forma, quando o conjunto de dados é utilizado como conjunto de dados de saída, o caminho de saída é especificado. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código seguinte ao método **Main** que **aciona uma execução de pipeline**.

Este código também define os valores dos parâmetros **inputPath** e **outputPath** especificados no pipeline com os valores reais dos caminhos dos blobs de origem e de sink.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

1. Adicione o código seguinte ao método **Main** para verificar continuamente o estado até terminar de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o código seguinte ao método **Main** que obtém os detalhes da execução da atividade de cópia; por exemplo, o tamanho dos dados lidos/escritos.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Executar o código

Crie e inicie a aplicação e, em seguida, verifique a execução de pipeline.

A consola imprime o progresso da criação da fábrica de dados, o serviço ligado, os conjuntos de dados, o pipeline e a execução de pipeline. Em seguida, verifica o estado de execução do pipeline. Aguarde até ver os detalhes da execução da atividade de cópia com o tamanho dos dados lidos/escritos. Em seguida, utilize ferramentas como o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados para "outputBlobPath" a partir de "inputBlobPath", conforme especificou nas variáveis.

### <a name="sample-output"></a>Resultado do exemplo: 
```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
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
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
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
        "name": "CopyFromBlobToBlob"
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
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída no contentor de blobs adftutorial. Em seguida, copia o ficheiro emp.txt da pasta de entrada para a pasta de saída. 

1. No portal do Azure, na página do contentor **adftutorial**, clique em **Atualizar** para ver a pasta de saída. 
    
    ![Atualizar](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. Clique em **saída** na lista de pastas. 
2. Confirme se o ficheiro **emp.txt** foi copiado para a pasta de saída. 

    ![Atualizar](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Limpar recursos
Para eliminar a fábrica de dados programaticamente, adicione as seguintes linhas de código ao programa: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários. 
