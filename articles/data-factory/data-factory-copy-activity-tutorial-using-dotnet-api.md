---
title: "Tutorial: Criar um pipeline com a Atividade de Cópia através da API .NET | Microsoft Docs"
description: "Neste tutorial, vai criar um pipeline do Azure Data Factory com uma Atividade de Cópia através da API .NET."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 4b29fd1c188c76a7c65c4dcff02dc9efdf3ebaee
ms.openlocfilehash: 733c151012e3d896f720fbc64120432aca594bda


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: Criar um pipeline com a Atividade de Cópia com a API .NET
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados do Azure com a API .NET. O pipeline na fábrica de dados utiliza uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para uma Base de Dados SQL do Azure.

A Atividade de Cópia executa o movimento de dados no Azure Data Factory. A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

> [!NOTE]
> Este artigo não abrange toda a API .NET do Data Factory. Veja [Data Factory .NET API Reference (Referência da API .NET do Data Factory)](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre o SDK .NET do Data Factory.
> 
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Não transforma dados de entrada para produzir dados de saída. Para ver um tutorial sobre como transformar dados através do Azure Data Factory, consulte [Tutorial: Build a pipeline to transform data using Hadoop cluster (Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop)](data-factory-build-your-first-pipeline.md).


## <a name="prerequisites"></a>Pré-requisitos
* Leia a [Descrição Geral e Pré-requisitos do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma descrição geral do tutorial e concluir os passos de **pré-requisitos**.
* Visual Studio 2012 ou 2013 ou 2015
* Transferir e instalar o [SDK .NET do Azure](http://azure.microsoft.com/downloads/)
* Azure PowerShell. Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar o Azure PowerShell no seu computador. Utilize o Azure PowerShell para criar uma aplicação do Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Criar uma Aplicação no Azure Active Directory
Crie uma aplicação no Azure Active Directory, crie um principal de serviço para a aplicação e atribua-a à função **Contribuinte do Data Factory**.

1. Inicie o **Azure PowerShell**.
2. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no portal do Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Execute o comando seguinte para ver todas as subscrições para esta conta.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua subscrição do Azure.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > Tome nota do **SubscriptionId** e **TenantId** a partir do resultado deste comando.

5. Crie um grupo de recursos do Azure com o nome **ADFTutorialResourceGroup**, executando o comando seguinte no PowerShell.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Se o grupo de recursos já existir, especifique se pretende atualizá-lo (Y) ou mantê-lo como está (N).

    Se utilizar um grupo de recursos diferente, terá de utilizar o nome do grupo de recursos em vez de ADFTutorialResourceGroup neste tutorial.
6. Crie uma aplicação do Azure Active Directory.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Se obtiver o seguinte erro, especifique um URL diferente e execute o comando novamente.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Crie o principal de serviço do AD.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Adicione o principal de serviço à função **Contribuinte do Data Factory**.

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Obtenha o ID da aplicação.

    ```PowerShell
    $azureAdApplication 
    ```
    Tome nota do ID da aplicação (**applicationID** a partir do resultado).

Deve obter os quatro valores seguintes destes passos:

* ID do inquilino
* ID da subscrição
* ID da aplicação
* Palavra-passe (especificada no primeiro comando)

## <a name="walkthrough"></a>Instruções
1. Com o Visual Studio 2012/2013/2015, crie uma aplicação de consola de C# .NET.
   1. Inicie o **Visual Studio** 2012/2013/2015.
   2. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**.
   3. Expanda **Modelos** e selecione **Visual C#**. Nestas instruções, utiliza C#, mas pode utilizar qualquer linguagem .NET.
   4. Selecione **Aplicação de Consola** na lista de tipos de projeto à direita.
   5. Introduza **DataFactoryAPITestApp** para o Nome.
   6. Selecione **C:\ADFGetStarted** para a Localização.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gestor de Pacotes Nuget** e clique em **Consola do Gestor de Pacotes**.
3. Na **Consola do Gestor de Pacotes**, realize os seguintes passos:
   1. Execute o seguinte comando para instalar o pacote do Data Factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Execute o seguinte comando para instalar o pacote do Azure Active Directory (utilize a API do Azure Active Directory no código): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Adicione a secção **appSetttings** seguinte ao ficheiro **App.config**. Estas definições são utilizadas pelo método de ajuda: **GetAuthorizationHeader**.

    Substitua os valores de **&lt;ID da aplicação&gt;**, **&lt;Palavra-passe&gt;**, **&lt;ID da subscrição&gt;** e **&lt;ID de inquilino&gt;** pelos seus próprios valores.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <startup>
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
        </startup>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Adicione o seguinte ao **utilizar** instruções para o ficheiro de origem (Program.cs) no projeto.

    ```csharp
    using System.Threading;
    using System.Configuration;
    using System.Collections.ObjectModel;

    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure;
    ```

6. Adicione o código seguinte, que cria uma instância de classe **DataPipelineManagementClient**, ao método **Principal**. Utilize este objeto para criar uma fábrica de dados, um serviço ligado, conjuntos de dados de entrada e de saída e um pipeline. Utilize-o também para monitorizar setores de um conjunto de dados no tempo de execução.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Substitua o valor do **resourceGroupName** pelo nome do seu grupo de recursos do Azure.
   >
   > Atualize o nome da fábrica de dados (**dataFactoryName**) para que seja exclusivo. O nome da fábrica de dados deve ser globalmente exclusivo. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.

7. Adicione o código seguinte, que cria uma **fábrica de dados**, ao método **Principal**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties() { }
            }
        }
    );
    ```

8. Adicione o código seguinte, que cria um **serviço ligado do Armazenamento do Azure**, ao método **Principal**.

   > [!IMPORTANT]
   > Substitua **storageaccountname** e **accountkey** pelo nome e chave da sua conta de Armazenamento do Azure.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

9. Adicione o código seguinte, que cria um **serviço ligado SQL do Azure**, ao método **Principal**.

   > [!IMPORTANT]
   > Substitua **servername**, **databasename**, **username** e **password** pelos nomes do seu servidor, base de dados, utilizador e palavra-passe do SQL do Azure.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

10. Adicione o código seguinte, que cria **conjuntos de dados de entrada e saída**, ao método **Principal**.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetAzureSqlDestination";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },

                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```

11. Adicione o código seguinte, que **cria e ativa um pipeline**, ao método **Principal**. Este pipeline tem um **CopyActivity** que assume **BlobSource** como uma origem e **BlobSink** como um sink.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

            // Initial value for pipeline's active period. With this, you won't need to set slice status
            Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    },
                }
            }
        });
    ```

12. Adicione o código seguinte ao método **Principal** para obter o estado de um setor de dados do conjunto de dados de saída. Neste exemplo, é esperado apenas um setor.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Adicione o código seguinte, para obter os detalhes de execução de um setor de dados, ao método **Principal**.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. Adicione o método de ajuda seguinte, utilizado pelo método **Principal**, à classe de **Programa**.

    ```csharp
    public static string GetAuthorizationHeader()
    {
        AuthenticationResult result = null;
        var thread = new Thread(() =>
        {
            try
            {
                var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
            }
            catch (Exception threadEx)
            {
                Console.WriteLine(threadEx.Message);
            }
        });

        thread.SetApartmentState(ApartmentState.STA);
        thread.Name = "AcquireTokenThread";
        thread.Start();
        thread.Join();

        if (result != null)
        {
            return result.AccessToken;
        }

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. No Explorador de Soluções, expanda o projeto (**DataFactoryAPITestApp**), clique com o botão direito do rato em **Referências** e clique em **Adicionar Referência**. Selecione a caixa de verificação da assemblagem "**System.Configuration**" e clique em **OK**.
16. Crie a aplicação da consola. Clique em **Criar** no menu e clique em **Criar Solução**.
17. Confirme se existe pelo menos um ficheiro no contentor **adftutorial** do seu armazenamento de blobs do Azure. Se não existir, crie um ficheiro **Emp.txt** no Bloco de Notas com o seguinte conteúdo e carregue-o para o contentor adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
18. Execute o exemplo, clicando em **Depurar** -> **Iniciar Depuração** no menu. Quando vir **A obter detalhes da execução de um setor de dados**, aguarde alguns minutos e prima **ENTER**.
19. Utilize o portal do Azure para verificar se a fábrica de dados **APITutorialFactory** foi criada com os artefactos seguintes:
   * Serviço ligado: **LinkedService_AzureStorage**
   * Conjunto de dados: **DatasetBlobSource** e **DatasetBlobDestination**.
   * Pipeline: **PipelineBlobSample**
20. Verifique se os registos dos dois colaboradores são criados na tabela “**emp**” na base de dados SQL do Azure especificada.

## <a name="next-steps"></a>Passos Seguintes
| Tópico | Descrição |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
[Referência da API .NET do Data Factory](/dotnet/api/) | Fornece detalhes sobre o SDK .NET do Data Factory (procure Microsoft.Azure.Management.DataFactories.Models na vista de árvore). 






<!--HONumber=Feb17_HO1-->


