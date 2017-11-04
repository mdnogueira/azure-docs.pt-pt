---
title: Utilizar atividades personalizadas num pipeline do Azure Data Factory
description: "Saiba como criar atividades personalizadas e utilizá-los um pipeline do Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 0794952fdfbcc49cc66273be2d46484014ae1677
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-use-custom-activities.md)
> * [Versão 2 - Pré-visualização](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [atividades personalizadas V2](../transform-data-using-dotnet-custom-activity.md).

Existem dois tipos de atividades que pode utilizar um pipeline do Azure Data Factory.

- [Atividades de movimentos de dados](data-factory-data-movement-activities.md) para mover dados entre [arquivos de dados de origem e dependente suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para transformar dados de utilização de serviços de computação, tais como o Azure HDInsight, do Azure Batch e do Azure Machine Learning. 

Para mover dados para/de um arquivo de dados que não suporta a fábrica de dados, crie um **atividade personalizada** com a sua própria lógica de movimento de dados e utilize a atividade num pipeline. Do mesmo modo, processo/para transformar dados de uma forma que não é suportada pela fábrica de dados, criar uma atividade personalizada com a suas próprias lógica de transformação de dados e utilize a atividade num pipeline. 

Pode configurar uma atividade personalizada para executar um **do Azure Batch** conjunto de máquinas virtuais ou uma baseados em Windows **Azure HDInsight** cluster. Ao utilizar o Azure Batch, pode utilizar apenas um conjunto existente do Azure Batch. Enquanto que, quando utilizar o HDInsight, pode utilizar um cluster do HDInsight existente ou um cluster que é criado automaticamente para a pedido no tempo de execução.  

As instruções seguintes fornece instruções passo a passo para criar uma atividade personalizada do .NET e utilizando a atividade personalizada num pipeline. As instruções utilizam um **do Azure Batch** serviço ligado. Para utilizar um Azure HDInsight serviço ligado em vez disso, crie um serviço ligado do tipo **HDInsight** (seu próprio cluster do HDInsight) ou **HDInsightOnDemand** (Data Factory cria um cluster do HDInsight pedido). Em seguida, configure uma atividade personalizada a utilizar o serviço ligado de HDInsight. Consulte [serviços ligados de utilização do Azure HDInsight](#use-hdinsight-compute-service) secção para obter detalhes sobre como utilizar o Azure HDInsight para executar a atividade personalizada.

> [!IMPORTANT]
> - As atividades personalizadas do .NET executam apenas nos clusters do HDInsight baseados em Windows. Uma solução para esta limitação é para utilizar a atividade de reduzir o mapa para executar código Java personalizado num cluster do HDInsight baseado em Linux. Outra opção consiste em utilizar um conjunto do Azure Batch de VMs para executar atividades personalizadas em vez de utilizar um cluster do HDInsight.
> - Não é possível utilizar um Gateway de gestão de dados de uma atividade personalizada para aceder às origens de dados no local. Atualmente, [Data Management Gateway](data-factory-data-management-gateway.md) suporta apenas a atividade de cópia e a atividade de procedimento armazenado na fábrica de dados.   

## <a name="walkthrough-create-a-custom-activity"></a>Instruções: criar uma atividade personalizada
### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012/2013/2015
* Transferir e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Pré-requisitos de lote do Azure
As instruções, execute as atividades personalizadas do .NET utilizando o Azure Batch como um recurso de computação. **O Azure Batch** é informática de uma plataforma de serviço para a execução de paralelo em grande escala e de elevado desempenho (HPC) as aplicações forma eficiente na nuvem. O Azure Batch agendas de trabalho de computação intensiva para executar um gerido **coleção de máquinas virtuais**, e pode dimensionar automaticamente de computação recursos para satisfazer as necessidades das suas tarefas. Consulte [Noções básicas do Azure Batch] [ batch-technical-overview] artigo para obter uma descrição detalhada do serviço Azure Batch.

Para o tutorial, crie uma conta do Azure Batch com um conjunto de VMs. Eis os passos:

1. Criar um **conta do Azure Batch** utilizando o [portal do Azure](http://portal.azure.com). Consulte [criar e gerir uma conta do Azure Batch] [ batch-create-account] artigo para obter instruções.
2. Tenha em atenção pendente o nome da conta do Azure Batch, chave de conta, URI e o nome do agrupamento. É necessário para criar um serviço ligado do Azure Batch.
    1. Na página inicial para a conta do Azure Batch, verá um **URL** no seguinte formato: `https://myaccount.westus.batch.azure.com`. Neste exemplo, **myaccount** é o nome da conta do Azure Batch. URI que utilize a definição de serviço ligado é o URL sem o nome da conta. Por exemplo: `https://<region>.batch.azure.com`.
    2. Clique em **chaves** no menu à esquerda e copiar a **chave de acesso primária**.
    3. Para utilizar um conjunto existente, clique em **agrupamentos** no menu e tome nota do **ID** do conjunto. Se não tiver um conjunto existente, mova para o passo seguinte.     
2. Criar um **conjunto do Azure Batch**.

   1. No [portal do Azure](https://portal.azure.com), clique em **procurar** no menu à esquerda e clique **contas do Batch**.
   2. Selecione a sua conta do Azure Batch para abrir o **conta do Batch** painel.
   3. Clique em **agrupamentos** mosaico.
   4. No **agrupamentos** painel, clique em Adicionar botão na barra de ferramentas para adicionar um conjunto.
      1. Introduza um ID para o conjunto (ID do conjunto). Tenha em atenção o **ID do conjunto**; apenas quando criar a solução de fábrica de dados.
      2. Especifique **Windows Server 2012 R2** para a definição de família do sistema operativo.
      3. Selecione um **escalão de preço de nó**.
      4. Introduza **2** como valor para o **destino dedicado** definição.
      5. Introduza **2** como valor para o **máx. tarefas por nó** definição.
   5. Clique em **OK** para criar o conjunto.
   6. Tome nota do **ID** do conjunto. 



### <a name="high-level-steps"></a>Passos de alto nível
Seguem-se os dois passos de alto nível que efetuar como parte destas instruções: 

1. Crie uma atividade personalizada que contém a lógica de transformação/processamento de dados simples.
2. Crie um Azure data factory com um pipeline que utiliza a atividade personalizada.

### <a name="create-a-custom-activity"></a>Criar uma atividade personalizada
Para criar uma atividade personalizada do .NET, criar um **biblioteca de classe de .NET** projeto com uma classe que implementa que **IDotNetActivity** interface. Esta interface tem apenas um método: [executar](https://msdn.microsoft.com/library/azure/mt603945.aspx) e a assinatura é:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


O método utiliza quatro parâmetros:

- **linkedServices**. Esta propriedade é uma lista enumeráveis de serviços de ligado do arquivo de dados referenciada por conjuntos de dados de entrada/saída da atividade.   
- **conjuntos de dados**. Esta propriedade é uma lista enumeráveis de conjuntos de dados de entrada/saída da atividade. Pode utilizar este parâmetro para obter as localizações e esquemas definidas conjuntos de dados de entrada e de saída.
- **atividade**. Esta propriedade representa a atividade atual. Pode ser utilizado para aceder às propriedades expandidas associadas com a atividade personalizada. Consulte [acesso propriedades expandidas](#access-extended-properties) para obter mais detalhes.
- **registo**. Este objeto permite-lhe escrever comentários de depuração esse superfície no registo de utilizador para o pipeline.

O método devolve um dicionário que pode ser utilizado encadear atividades personalizadas em conjunto no futuro. Esta funcionalidade ainda não está implementada, por isso, devolver um dicionário vazio do método.  

### <a name="procedure"></a>Procedimento
1. Criar um **biblioteca de classe de .NET** projeto.
   <ol type="a">
     <li>Iniciar <b>Visual Studio 2017</b> ou <b>Visual Studio 2015</b> ou <b>Visual Studio 2013</b> ou <b>Visual Studio 2012</b>.</li>
     <li>Clique em <b>Ficheiro</b>, aponte para <b>Novo</b> e, em seguida, clique em <b>Projeto</b>.</li>
     <li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Nestas instruções, utilizar c#, mas pode utilizar qualquer linguagem .NET para desenvolver a atividade personalizada.</li>
     <li>Selecione <b>biblioteca de classes</b> da lista de tipos de projeto à direita. No VS 2017, escolha <b>biblioteca de classes (.NET Framework)</b> </li>
     <li>Introduza <b>MyDotNetActivity</b> para o <b>nome</b>.</li>
     <li>Selecione <b>C:\ADFGetStarted</b> para o <b>localização</b>.</li>
     <li>Clique em <b>OK</b> para criar o projeto.</li>
   </ol>
2.Clique em **ferramentas**, aponte para **Gestor de pacotes NuGet**e clique em **consola do Gestor de pacotes**.
3. Na consola do Gestor de pacotes, execute o seguinte comando para importar **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importar o **Storage do Azure** pacote NuGet ao projeto.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Iniciador de serviço da fábrica de dados requer a versão 4.3 do Windowsazure. Se adicionar uma referência a uma versão posterior da assemblagem de armazenamento do Azure no seu projeto de atividades personalizadas, verá um erro quando executa a atividade. Para resolver o erro, consulte [isolamento de Appdomain](#appdomain-isolation) secção. 
5. Adicione o seguinte **utilizando** instruções para o ficheiro de origem no projeto.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Alterar o nome do **espaço de nomes** para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Alterar o nome de classe para **MyDotNetActivity** e derivar-à partir de **IDotNetActivity** interface conforme mostrado no seguinte fragmento de código:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementar (adicionar) a **executar** método do **IDotNetActivity** interface para o **MyDotNetActivity** classe e copie o seguinte código de exemplo para o método.

    O exemplo seguinte conta o número de ocorrências do termo de pesquisa ("Microsoft") em cada blob associado a um setor de dados.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    
    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log information, use the logger object
        // log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;
        
        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset 
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // get the first Azure Storate linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;
    
        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.
    
        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);
    
            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
               // with the data slice. definition of the method is shown in the next step.
    
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
        } while (continuationToken != null);
    
        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path   
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);
    
        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.  
    
        return new Dictionary<string, string>();
    }
    ```
9. Adicione os seguintes métodos de programa auxiliar: 

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    
    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset   
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.   
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }
    
        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    O método de GetFolderPath devolve o caminho para a pasta que o dataset aponta para e o método GetFileName devolve o nome do que o dataset aponta para o ficheiro/blob. Se havefolderPath define a utilização de variáveis como {Year}, {Month} devolve {Day} etc., o método a cadeia que é sem substituir com os valores de tempo de execução. Consulte [acesso propriedades expandidas](#access-extended-properties) secção para obter detalhes sobre aceder SliceStart, SliceEnd, etc.    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    O método de Calculate calcula o número de instâncias de palavra-chave Microsoft nos ficheiros de entrada (blobs na pasta). O termo de pesquisa ("Microsoft") é "hard-coded" no código.
10. Compile o projeto. Clique em **criar** do menu e clique em **compilar solução**.

    > [!IMPORTANT]
    > Versão de conjunto 4.5.2 do .NET Framework como o framework de destino para o seu projeto: clique com o botão direito no projeto e clique em **propriedades** para definir o framework de destino. Fábrica de dados não suporta as atividades personalizadas compiladas posteriores ao 4.5.2 em relação a versões do .NET Framework.

11. Iniciar **Explorador do Windows**e navegue para **bin\debug** ou **bin\release** pasta dependendo do tipo de compilação.
12. Criar um ficheiro zip **MyDotNetActivity.zip** que contém todos os binários no <project folder>\bin\Debug pasta. Incluir o **MyDotNetActivity.pdb** para que obtenha detalhes adicionais, tais como o número de linha com o código de origem que causou o problema se tiver ocorrido uma falha de ficheiros. 

    > [!IMPORTANT]
    > Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.

    ![Ficheiros de saída binário](./media/data-factory-use-custom-activities/Binaries.png)
14. Criar um contentor do blob denominado **customactivitycontainer** se já existir. 
15. Carregar MyDotNetActivity.zip como um blob para customactivitycontainer num **para fins gerais** blob storage do Azure (armazenamento de BLOBs de acesso frequente não/útil) que está referido por AzureStorageLinkedService.  

> [!IMPORTANT]
> Se adicionar este projeto de atividade .NET para uma solução no Visual Studio que contém um projeto do Data Factory e adicione uma referência ao projeto de atividade .NET do projeto de aplicação fábrica de dados, não terá de efetuar os dois últimos passos da criação manual zip o ficheiro e carregá-lo para o armazenamento de Blobs do Azure para fins gerais. Quando publicar entidades do Data Factory com o Visual Studio, estes passos são automaticamente efetuados pelo processo de publicação. Para obter mais informações, consulte [projeto do Data Factory no Visual Studio](#data-factory-project-in-visual-studio) secção.

## <a name="create-a-pipeline-with-custom-activity"></a>Criar um pipeline com atividade personalizada
Criou uma atividade personalizada e carregado o ficheiro zip com binários para um contentor do blob num **para fins gerais** conta do Storage do Azure. Nesta secção, vai criar um Azure data factory com um pipeline que utiliza a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa blobs (ficheiros) na pasta customactivityinput do contentor de adftutorial no armazenamento de Blobs. O conjunto de dados de saída para a atividade representa blobs de saída na pasta customactivityoutput do contentor de adftutorial no armazenamento de Blobs.

Criar **file.txt** com o seguinte conteúdo do ficheiro e carregá-la para **customactivityinput** pasta o **adftutorial** contentor. Crie o contentor de adftutorial se não existir já. 

```
test custom activity Microsoft test custom activity Microsoft
```

A pasta de entrada corresponde a um setor no Azure Data Factory, mesmo se a pasta tem dois ou mais ficheiros. Quando cada setor é processado pelo pipeline, a atividade personalizada itera através de todos os blobs na pasta de entrada para esse setor.

Consulte um ficheiro na pasta adftutorial\customactivityoutput de saída com uma ou mais linhas (igual ao número de blobs na pasta de entrada):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Eis os passos que efetuar nesta secção:

1. Criar um **fábrica de dados**.
2. Criar **serviços ligados** para o conjunto do Azure Batch de VMs no qual a atividade personalizada é executada e o armazenamento do Azure que contém os blobs de entrada/saída.
3. Criar a entrada e saída **conjuntos de dados** que representam a entrada e saída da atividade personalizada.
4. Criar um **pipeline** que utiliza a atividade personalizada.

> [!NOTE]
> Criar o **file.txt** e carregá-la para um contentor de blob, se ainda não o tiver feito. Ver as instruções na secção anterior.   

### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Após iniciar sessão no portal do Azure, efetue os seguintes passos:
   1. Clique em **NOVO** no menu da esquerda.
   2. Clique em **dados + análise** no **novo** painel.
   3. Clique em **Data Factory** no painel **Análise de dados**.
   
    ![Menu novo do Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. No **nova fábrica de dados** painel, introduza **CustomActivityFactory** para o nome. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **nome da fábrica de dados "CustomActivityFactory" não está disponível**, alterar o nome da fábrica de dados (por exemplo, **yournameCustomActivityFactory**) e tente criar novamente.

    ![Painel nova fábrica de dados do Azure](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Clique em **nome do grupo de recursos**e selecione um grupo de recursos existente ou crie um grupo de recursos.
4. Certifique-se de que está a utilizar o correto **subscrição** e **região** onde pretende que a fábrica de dados ser criada.
5. Clique em **Criar** no painel **Nova fábrica de dados**.
6. Consulte a fábrica de dados a ser criada no **Dashboard** do portal do Azure.
7. Depois da fábrica de dados foi criada com êxito, verá o painel de fábrica de dados, que mostra o conteúdo do data factory.
    
    ![Painel Data Factory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Neste passo, ligar a conta de armazenamento do Azure e a conta do Azure Batch à fábrica de dados.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. Clique em de **autor e implementar** mosaico a **DATA FACTORY** painel **CustomActivityFactory**. Consulte o Editor do Data Factory.
2. Clique em **novo arquivo de dados** no comando barra e escolha **storage do Azure**. Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor.
    
    ![Novo arquivo de dados - Storage do Azure](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Substitua `<accountname>` com o nome da sua conta de armazenamento do Azure e `<accountkey>` com a chave de acesso da conta do storage do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    ![Serviço de gostou de armazenamento do Azure](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="create-azure-batch-linked-service"></a>Criar o serviço ligado do Azure Batch
1. No Editor de fábrica de dados, clique em **... Mais** na barra de comandos, clique em **nova computação**e, em seguida, selecione **do Azure Batch** no menu.

    ![Nova computação - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Efetue as seguintes alterações para o script JSON:

   1. Especifique o nome da conta do Azure Batch para o **accountName** propriedade. O **URL** do **painel de conta do Azure Batch** é o seguinte formato: `http://accountname.region.batch.azure.com`. Para o **batchUri** propriedade no JSON, terá de remover `accountname.` do URL e utilize o `accountname` para o `accountName` propriedade JSON.
   2. Especifique a chave de conta do Azure Batch para o **accessKey** propriedade.
   3. Especifique o nome do conjunto que criou como parte da pré-requisitos para o **poolName** propriedade. Também pode especificar o ID do conjunto em vez do nome do conjunto.
   4. Especifique o URI de lote do Azure para o **batchUri** propriedade. Exemplo: `https://westus.batch.azure.com`.  
   5. Especifique o **AzureStorageLinkedService** para o **linkedServiceName** propriedade.

        ```json
        {
         "name": "AzureBatchLinkedService",
         "properties": {
           "type": "AzureBatch",
           "typeProperties": {
             "accountName": "myazurebatchaccount",
             "batchUri": "https://westus.batch.azure.com",
             "accessKey": "<yourbatchaccountkey>",
             "poolName": "myazurebatchpool",
             "linkedServiceName": "AzureStorageLinkedService"
           }
         }
        }
        ```

       Para o **poolName** propriedade, também pode especificar o ID do conjunto em vez do nome do conjunto.

      > [!IMPORTANT]
      > O serviço fábrica de dados não suporta uma opção a pedido para o Azure Batch como para o HDInsight. Só pode utilizar o seu próprio conjunto do Azure Batch num Azure data factory.   
    

### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e de saída.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** do Data Factory, clique em **... Mais** na barra de comandos, clique em **novo conjunto de dados**e, em seguida, selecione **Blob storage do Azure** no menu pendente.
2. Substitua JSON no painel à direita com o fragmento JSON seguinte:

    ```json
    {
     "name": "InputDataset",
     "properties": {
         "type": "AzureBlob",
         "linkedServiceName": "AzureStorageLinkedService",
         "typeProperties": {
             "folderPath": "adftutorial/customactivityinput/",
             "format": {
                 "type": "TextFormat"
             }
         },
         "availability": {
             "frequency": "Hour",
             "interval": 1
         },
         "external": true,
         "policy": {}
     }
    }
    ```

   Pode cria um pipeline mais tarde esta explicação passo a passo com a hora de início: 2016-11-tempo 16T00:00:00Z e de fim: 2016-11-16T05:00:00Z. Está agendada para produzir os dados de hora a hora, pelo que existem cinco setores de entrada/saída (entre **00**: 00:00 -> **05**: 00:00).

   O **frequência** e **intervalo** o conjunto de dados de entrada está definida como **hora** e **1**, o que significa que o setor de entrada está disponível por hora. Neste exemplo, é o mesmo ficheiro (file.txt) a intputfolder.

   Seguem-se as horas de início de cada setor, que é representada pela variável de sistema do SliceStart no fragmento JSON acima.
3. Clique em **implementar** na barra de ferramentas para criar e implementar o **InputDataset**. Confirme que vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

#### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
1. No **editor do Data Factory**, clique em **... Mais** na barra de comandos, clique em **novo conjunto de dados**e, em seguida, selecione **Blob storage do Azure**.
2. Substitua o script JSON no painel à direita com o seguinte script JSON:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     Localização de saída é **adftutorial/customactivityoutput/** e nome de ficheiro de saída é aaaa-MM-dd-HH.txt onde AAAA-MM-dd HH o ano, mês, data e hora do setor que está a ser produzido. Consulte [referência para programadores] [ adf-developer-reference] para obter mais detalhes.

    Um ficheiro/blob de saída é gerado para cada setor de entrada. Eis como um ficheiro de saída é designado para cada setor. Todos os ficheiros de saída são gerados por uma pasta de saída: **adftutorial\customactivityoutput**.

   | Setor | Hora de início | Ficheiro de saída |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Lembre-se de que todos os ficheiros numa pasta entrada fazem parte de um setor com as horas de início mencionados acima. Quando este setor é processado, a atividade personalizada analisa através de cada ficheiro e produz uma linha no ficheiro de saída com o número de ocorrências do termo de pesquisa ("Microsoft"). Se existirem três ficheiros de inputfolder, existem três linhas no ficheiro de saída para cada setor por hora: 2016-11-16-00.txt 2016-11-16:01:00:00.txt, etc.
3. Para implementar o **OutputDataset**, clique em **implementar** na barra de comandos.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Criar e executar um pipeline que utiliza a atividade personalizada
1. No Editor de fábrica de dados, clique em **... Mais**e, em seguida, selecione **novo pipeline** na barra de comandos. 
2. Substitua JSON no painel à direita com o seguinte script JSON:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Tenha em atenção os seguintes pontos:

   * **Simultaneidade** está definido como **2** para que os duas setores são processados em paralelo por 2 VMs num conjunto do Azure Batch.
   * Não existe uma atividade na secção atividades e é do tipo: **DotNetActivity**.
   * **AssemblyName** está definido como o nome da DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** está definido como **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** está definido como **AzureStorageLinkedService** que aponta para o armazenamento de blob que contém o ficheiro zip de atividade personalizado. Se estiver a utilizar diferentes contas de armazenamento do Azure para ficheiros de entrada/saída e o ficheiro zip de atividades personalizadas, crie outro serviço ligado do Storage do Azure. Este artigo pressupõe que está a utilizar a mesma conta de armazenamento do Azure.
   * **PackageFile** está definido como **customactivitycontainer/MyDotNetActivity.zip**. Está no formato: containerforthezip/nameofthezip.zip.
   * A atividade personalizada demora **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade linkedServiceName da atividade personalizada aponta para o **AzureBatchLinkedService**, que indica que o Azure Data Factory que a atividade personalizada tem de executar em VMs do Azure Batch.
   * **isPaused** propriedade está definida como **falso** por predefinição. O pipeline executa imediatamente neste exemplo, porque os setores de início no passado. Pode definir esta propriedade para verdadeiro para colocar em pausa o pipeline e configure-o para false para reiniciar.
   * O **iniciar** tempo e **final** tempos são **cinco** , à excepção de horas e setores são produzidos por hora, pelo que os cinco setores são produzidos pelo pipeline.
3. Para implementar o pipeline, clique em **implementar** na barra de comandos.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. No painel do Data Factory no portal do Azure, clique em **diagrama**.

    ![Mosaico do diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Na vista de diagrama, clique agora o OutputDataset.

    ![Vista de diagrama](./media/data-factory-use-custom-activities/diagram.png)
3. Deverá ver que os setores de cinco saída estão no estado pronto. Se não estão no estado pronto, ainda não foram produzidos ainda. 

   ![Setores de saída](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Certifique-se de que os ficheiros de saída são gerados no armazenamento de BLOBs no **adftutorial** contentor.

   ![o resultado da atividade personalizada][image-data-factory-ouput-from-custom-activity]
5. Se abrir o ficheiro de saída, deverá ver um resultado semelhante ao seguinte resultado:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Utilize o [portal do Azure] [ azure-preview-portal] ou cmdlets do PowerShell do Azure para monitorizar a sua fábrica de dados, pipelines e conjuntos de dados. Pode ver as mensagens do **ActivityLogger** no código para a atividade personalizada nos registos de (especificamente utilizador-0.log) que pode transferir o portal ou com os cmdlets.

   ![transferir os registos de atividade personalizada][image-data-factory-download-logs-from-custom-activity]

Consulte [monitorizar e gerir Pipelines](data-factory-monitor-manage-pipelines.md) para obter passos detalhados para a monitorização de conjuntos de dados e pipelines.      

## <a name="data-factory-project-in-visual-studio"></a>Projeto do Data Factory no Visual Studio  
Pode criar e publicar entidades do Data Factory com o Visual Studio em vez de utilizar o portal do Azure. Para obter informações detalhadas sobre como criar e publicar entidades do Data Factory com o Visual Studio, consulte [construir o seu primeiro pipeline com o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) e [copiar dados de Blobs do Azure para o Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) artigos.

Efetue os seguintes passos adicionais se estiver a criar o projeto do Data Factory no Visual Studio:
 
1. Adicione o projeto do Data Factory para a solução do Visual Studio que contém o projeto de atividade personalizado. 
2. Adicione uma referência para o projeto de atividade .NET do projeto do Data Factory. Clique no projeto do Data Factory, aponte para **adicionar**e, em seguida, clique em **referência**. 
3. No **Adicionar referência** caixa de diálogo, selecione o **MyDotNetActivity** projeto e clique em **OK**.
4. Criar e publicar a solução.

    > [!IMPORTANT]
    > Quando publicar entidades do Data Factory, um ficheiro zip é criado automaticamente para si e que é carregado para o contentor do blob: customactivitycontainer. Se o contentor de blob não existir, será automaticamente criado demasiado.  


## <a name="data-factory-and-batch-integration"></a>Integração de fábrica de dados e de Batch
O serviço Data Factory cria uma tarefa no Azure Batch com o nome: **adf poolname: tarefa xxx**. Clique em **tarefas** no menu esquerdo. 

![O Azure Data Factory - tarefas do Batch](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Uma tarefa é criada para cada execução de atividade de um setor. Se existirem cinco setores prontos a ser processado, são criadas cinco tarefas nesta tarefa. Se existirem vários nós de computação num conjunto do Batch, podem executar dois ou mais setores em paralelo. Se as tarefas por nó de computação máximas for definido como > 1, também pode ter mais do que um setor ser executado na computação mesma.

![O Azure Data Factory - tarefas de lote](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

O diagrama seguinte ilustra a relação entre as tarefas do Azure Data Factory e Batch.

![Fábrica de dados & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Resolver problemas de falhas
Resolução de problemas é composta por alguns técnicas básicas:

1. Se vir o seguinte erro, pode utilizar um armazenamento de BLOBs de frequente/útil em vez de utilizar um armazenamento de Blobs do Azure para fins gerais. Carregue o ficheiro zip para um **conta do Storage do Azure para fins gerais**. 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. Se vir o seguinte erro, confirme que o nome da classe no ficheiro de CS corresponde ao nome especificado para o **EntryPoint** propriedade no JSON do pipeline. As instruções, é o nome da classe: MyDotNetActivity e o ponto de entrada no JSON é: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se os nomes correspondem, confirme que todos os binários estão a ser o **pasta raiz** do ficheiro zip. Ou seja, quando abrir o ficheiro zip, deverá ver todos os ficheiros na pasta raiz e não na quaisquer subpastas.   
3. Se o setor de entrada não está definido como **pronto**, confirme que a estrutura da pasta de entrada está correta e **file.txt** existe nas pastas de entrada.
3. No **executar** método da atividade personalizada, utilize o **IActivityLogger** objeto para registar informações que o ajuda a resolver problemas. As mensagens anteriormente registadas apareçam nos ficheiros de registo de utilizador (um ou mais ficheiros com o nome: 0.log de utilizador, utilizador 1.log, 2.log de utilizador, etc.).

   No **OutputDataset** painel, clique o setor para ver o **SETOR de dados** painel para essa setor. Verá **execuções de atividade** para esse setor. Deverá ver uma atividade executar para o setor. Se clicar em execução na barra de comando, pode começar a executar o setor de mesmo de outra atividade.

   Ao clicar a execução da atividade, consulte o **detalhes da execução da ATIVIDADE** painel com uma lista de ficheiros de registo. Consulte as mensagens anteriormente registadas no ficheiro user_0.log. Quando ocorre um erro, consulte três execuções de atividade porque a contagem de repetições está definida para 3 no pipeline/JSON de atividade. Ao clicar a execução de atividade, consulte os ficheiros de registo que poderá consultar para resolver o erro.

   Na lista de ficheiros de registo, clique o **utilizador 0.log**. No painel à direita são os resultados de utilizar o **IActivityLogger.Write** método. Se não vir todas as mensagens, verifique se tiver mais ficheiros de registo com o nome: user_1.log, user_2.log etc. Caso contrário, o código pode não ter conseguido após a última sessão iniciada a mensagem.

   Além disso, verifique **sistema 0.log** para quaisquer mensagens de erro de sistema e exceções.
4. Incluir o **PDB** no ficheiro zip do ficheiro para que os detalhes do erro tem informações como **pilha de chamadas** quando ocorre um erro.
5. Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.
6. Certifique-se de que o **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), e **packageLinkedService** (devem apontar para o **para fins gerais**blob storage do Azure que contém o ficheiro zip) estão definidos para valores corretos.
7. Se corrigir um erro e pretender processar de novo o setor, clique com o botão direito do rato no setor no painel **OutputDataset** e clique em **Executar**.
8. Se vir o seguinte erro, está a utilizar o pacote de armazenamento do Azure de versão > 4.3.0. Iniciador de serviço da fábrica de dados requer a versão 4.3 do Windowsazure. Consulte [isolamento de Appdomain](#appdomain-isolation) secção para um trabalho-around se tiver de utilizar a versão posterior da assemblagem de armazenamento do Azure. 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Se pode utilizar o 4.3.0 versão do pacote de armazenamento do Azure, remova a referência ao pacote de armazenamento do Azure de versão > 4.3.0 existente. Em seguida, execute o seguinte comando a partir da consola do Gestor de pacotes NuGet. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Crie o projeto. Elimine Azure.Storage assemblagem de versão > 4.3.0 da pasta bin\Debug. Crie um ficheiro zip com os binários e o ficheiro PDB. Substitua o ficheiro zip antigo por no contentor do blob (customactivitycontainer). Volte a executar os setores de falharam (com o botão direito setor e clique em executar).   
8. A atividade personalizada não utiliza o **App. config** ficheiro a partir do seu pacote. Por conseguinte, se o seu código lê quaisquer cadeias de ligação no ficheiro de configuração, não funciona no tempo de execução. A melhor prática, quando utilizar o Azure Batch é reter os segredos num **Azure KeyVault**, utilize um principal de serviço baseada em certificado para proteger o **keyvault**e distribuir o certificado para o Azure Batch conjunto. A atividade personalizada de .NET pode, então, aceder aos segredos do Cofre de Chaves durante o tempo de execução. Esta solução é uma solução genérica e pode dimensionar para qualquer tipo de segredo, não apenas a cadeia de ligação.

   Há uma solução mais fácil (mas não uma boa prática): pode criar um **serviço ligado de SQL do Azure** com as definições de cadeia de ligação, crie um conjunto de dados que utiliza o serviço ligado e encadeiam o conjunto de dados como um conjunto de dados de entrada fictício para o atividade personalizada do .NET. Em seguida, pode aceder a cadeia de ligação de serviço ligado com o código de atividade personalizado.  

## <a name="update-custom-activity"></a>Atualizar a atividade personalizada
Se atualizar o código para a atividade personalizada, crie-lo e carregar o ficheiro zip que contém os binários de novo para o armazenamento de Blobs.

## <a name="appdomain-isolation"></a>Isolamento de AppDomain
Consulte [cruzada exemplo de AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) que mostra como criar uma atividade personalizada que não está restringida para versões de assemblagem utilizadas pelo iniciador da fábrica de dados (exemplo: Windowsazure v4.3.0, newtonsoft v6.0.x, etc.).

## <a name="access-extended-properties"></a>Propriedades expandidas de acesso
Podem declarar propriedades expandidas no JSON, conforme mostrado no seguinte exemplo de atividade:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```


No exemplo, existem duas propriedades expandidas: **SliceStart** e **DataFactoryName**. O valor do SliceStart baseia-se a variável de sistema do SliceStart. Consulte [variáveis do sistema](data-factory-functions-variables.md) para obter uma lista de variáveis suportadas do sistema. O valor de DataFactoryName é "hard-coded" para CustomActivityFactory.

Para aceder a estas propriedades no expandidas o **executar** método, código de utilização semelhante para o seguinte código:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties                               
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

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

Consulte [automaticamente Dimensionar nós de computação num conjunto do Azure Batch](../../batch/batch-automatic-scaling.md) para obter mais detalhes.

Se o agrupamento está a utilizar a predefinição [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), o serviço Batch pode demorar 15-30 minutos para preparar a VM antes de executar a atividade personalizada.  Se o agrupamento está a utilizar um autoScaleEvaluationInterval diferentes, o serviço Batch pode demorar autoScaleEvaluationInterval + 10 minutos.

## <a name="use-hdinsight-compute-service"></a>Utilizar o serviço de computação do HDInsight
As instruções, utilizou computação do Azure Batch para executar a atividade personalizada. Também pode utilizar o seu próprio cluster do HDInsight baseado em Windows ou ter o Data Factory, crie uma pedido cluster do HDInsight baseados em Windows e ter a atividade personalizada executar no cluster do HDInsight. Eis os passos de alto nível para utilizar um cluster do HDInsight.

> [!IMPORTANT]
> As atividades personalizadas do .NET executam apenas nos clusters do HDInsight baseados em Windows. Uma solução para esta limitação é para utilizar a atividade de reduzir o mapa para executar código Java personalizado num cluster do HDInsight baseado em Linux. Outra opção consiste em utilizar um conjunto do Azure Batch de VMs para executar atividades personalizadas em vez de utilizar um cluster do HDInsight.
 

1. Crie um serviço ligado do Azure HDInsight.   
2. Serviço em vez de ligado de HDInsight utilize **AzureBatchLinkedService** no JSON do pipeline.

Se pretender testá-lo com as instruções, altere **iniciar** e **final** exceder o tempo para o pipeline, para que possa testar o cenário com o serviço do Azure HDInsight.

#### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço ligado do Azure HDInsight
O serviço fábrica de dados do Azure suporta a criação de um cluster a pedido e utilizá-lo para processar a entrada produzir os dados de saída. Também pode utilizar o seu próprio cluster para executar a mesma. Quando utiliza o cluster do HDInsight a pedido, um cluster é criado para cada setor. Enquanto que, se utilizar o seu próprio cluster do HDInsight, o cluster está pronto para processar o setor imediatamente. Por conseguinte, quando utilizar cluster a pedido, poderá não ver os dados de saída mais rapidamente quando utiliza o seu próprio cluster.

> [!NOTE]
> Em runtime, uma instância de uma atividade de .NET é executado apenas um nó de trabalho no cluster do HDInsight; não pode ser ampliada para ser executada em vários nós. Várias instâncias de atividade .NET podem executar em paralelo em diferentes nós do cluster do HDInsight.
>
>

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>Para utilizar um cluster de HDInsight a pedido
1. No **portal do Azure**, clique em **criar e implementar** na home page do Data Factory.
2. No Editor de fábrica de dados, clique em **nova computação** da barra de comandos e selecione **cluster do HDInsight a pedido** no menu.
3. Efetue as seguintes alterações para o script JSON:

   1. Para o **clusterSize** propriedade, especifique o tamanho do cluster do HDInsight.
   2. Para o **timeToLive** propriedade, especifique quanto o cliente pode estar inativo antes de ser eliminado.
   3. Para o **versão** propriedade, especifique a versão do HDInsight que pretende utilizar. Se excluir esta propriedade, é utilizada a versão mais recente.  
   4. Para o **linkedServiceName**, especifique **AzureStorageLinkedService**.

        ```JSON
        {
           "name": "HDInsightOnDemandLinkedService",
           "properties": {
               "type": "HDInsightOnDemand",
               "typeProperties": {
                   "clusterSize": 4,
                   "timeToLive": "00:05:00",
                   "osType": "Windows",
                   "linkedServiceName": "AzureStorageLinkedService",
               }
           }
        }
        ```

    > [!IMPORTANT]
    > As atividades personalizadas do .NET executam apenas nos clusters do HDInsight baseados em Windows. Uma solução para esta limitação é para utilizar a atividade de reduzir o mapa para executar código Java personalizado num cluster do HDInsight baseado em Linux. Outra opção consiste em utilizar um conjunto do Azure Batch de VMs para executar atividades personalizadas em vez de utilizar um cluster do HDInsight.

4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

##### <a name="to-use-your-own-hdinsight-cluster"></a>Para utilizar o seu próprio cluster do HDInsight:
1. No **portal do Azure**, clique em **criar e implementar** na home page do Data Factory.
2. No **Editor do Data Factory**, clique em **nova computação** da barra de comandos e selecione **cluster do HDInsight** no menu.
3. Efetue as seguintes alterações para o script JSON:

   1. Para o **clusterUri** propriedade, introduza o URL para o HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
   2. Para o **UserName** propriedade, introduza o nome de utilizador que tenha acesso ao cluster do HDInsight.
   3. Para o **palavra-passe** propriedade, introduza a palavra-passe para o utilizador.
   4. Para o **LinkedServiceName** propriedade, introduza **AzureStorageLinkedService**.
4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

Consulte [serviços ligados de computação](data-factory-compute-linked-services.md) para obter mais detalhes.

No **pipeline JSON**, utilizar o HDInsight (a pedido ou os seus próprios) serviço ligado:

```JSON
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "HDInsightOnDemandLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00Z",
    "end": "2016-11-16T05:00:00Z",
    "isPaused": false
  }
}
```

## <a name="create-a-custom-activity-by-using-net-sdk"></a>Criar uma atividade personalizada utilizando o .NET SDK
As instruções neste artigo, vai criar uma fábrica de dados com um pipeline que utiliza a atividade personalizada através do portal do Azure. O código seguinte mostra como criar a fábrica de dados, utilizando em vez disso, o .NET SDK. Pode encontrar mais detalhes sobre como utilizar o SDK para programaticamente Criar pipelines de [criar um pipeline com atividade de cópia utilizando a .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) artigo. 

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

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
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
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
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Depurar a atividade personalizada no Visual Studio
O [do Azure Data Factory - ambiente local](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) exemplo no GitHub inclui uma ferramenta que permite-lhe depurar as atividades personalizadas do .NET no Visual Studio.  


## <a name="sample-custom-activities-on-github"></a>Atividades personalizadas de exemplo no GitHub
| Exemplo | Que atividade personalizada faz |
| --- | --- |
| [Transferência de dados de HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Transfere dados a partir de um ponto final de HTTP para armazenamento de Blobs do Azure com c# atividade personalizada na fábrica de dados. |
| [Exemplo de análise de dados de sentimento do twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Invoca um modelo do Azure ML e efetue sentimento análise, a classificação, predição etc. |
| [Executar Script do R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Invoca o R script executando RScript.exe no cluster do HDInsight que já tenha o R instalado no mesmo. |
| [Cruzada AppDomain .NET atividade](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Utiliza as versões da assemblagem diferentes de aqueles utilizada pelo iniciador da fábrica de dados |
| [Reprocessar um modelo no Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Processa novamente um modelo no Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
