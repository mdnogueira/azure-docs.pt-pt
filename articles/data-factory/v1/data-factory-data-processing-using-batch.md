---
title: "Processar os conjuntos de dados em grande escala, utilizando a fábrica de dados e Batch | Microsoft Docs"
description: "Descreve como processar quantidades enormes de dados de um pipeline do Azure Data Factory através da utilização de capacidade de processamento paralelo de lote do Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: e2987b37d0146a68635c9190cf42ac7aeac48ed5
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Processar grandes conjuntos de dados com o Data Factory e o Batch
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [atividades personalizadas no Factory de dados versão 2](../transform-data-using-dotnet-custom-activity.md).

Este artigo descreve uma arquitetura de uma solução de exemplo que se move e processa os conjuntos de dados em grande escala de forma automática e agendada. Também fornece uma instruções ponto a ponto para implementar a solução utilizando o Azure Data Factory e do Azure Batch.

Este artigo é maior do que o nosso artigo típico porque contém uma explicação passo a passo de uma solução de exemplo completo. Se estiver familiarizado com o Batch e do Data Factory, pode saber sobre estes serviços e como funcionam em conjunto. Se souber algo sobre os serviços e são estruturar/arquitetar uma solução, pode concentrar-se apenas nos [secção de arquitetura](#architecture-of-sample-solution) do artigo e se estiver a desenvolver um protótipo ou uma solução, pode também pretender experimentar instruções passo a passo o [instruções](#implementation-of-sample-solution). Convidamo-os seus comentários sobre este conteúdo e como utilizá-lo.

Em primeiro lugar, vamos ver como podem ajudar a fábrica de dados e Batch serviços com processamento grandes conjuntos de dados na nuvem.     

## <a name="why-azure-batch"></a>Por que motivo o Azure Batch?
O Azure Batch permite-lhe executar aplicações de computação de alto desempenho (HPC) e paralelas em larga escala, de forma eficaz na nuvem. É um serviço de plataforma que agenda trabalho de computação intensivo para ser executado numa coleção gerida de máquinas virtuais, e pode dimensionar automaticamente recursos de computação para satisfazer as necessidades das suas tarefas.

Com o serviço Batch, define os recursos de computação do Azure para executar as aplicações em paralelo e com dimensionamento. Pode executar tarefas a pedido ou agendadas e não precisa de criar, configurar e gerir at manualmente um cluster HPC, máquinas virtuais individuais, redes virtuais ou uma tarefa complexa e a infraestrutura de agendamento de tarefas.

Consulte os artigos seguintes se não estiver familiarizado com o Azure Batch como ajuda com Noções sobre a arquitetura de implementação da solução descrita neste artigo.   

* [Noções básicas do Azure Batch](../../batch/batch-technical-overview.md)
* [Descrição geral da funcionalidade do Batch](../../batch/batch-api-basics.md)

(opcional) Para saber mais acerca do Azure Batch, consulte o [percurso de aprendizagem para o Azure Batch](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Por que motivo fábrica de dados do Azure?
Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Utilizar o serviço fábrica de dados, pode criar pipelines de dados geridos que movem os dados no local e na nuvem arquivos de dados para um arquivo de dados centralizada (por exemplo: Blob Storage do Azure) e o processo/transformação dados através de serviços como o Azure HDInsight e do Azure Machine Learning. Também pode agendar pipelines de dados para executar numa forma agendada (hora a hora, diária, semanal, etc.) e monitor e geri-los de forma rápida para identificar problemas e tomar medidas.

Consulte os artigos seguintes se não estiver familiarizado com o Azure Data Factory como ajuda com Noções sobre a arquitetura de implementação da solução descrita neste artigo.  

* [Introdução ao Azure Data Factory](data-factory-introduction.md)
* [Criar o seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md)   

(opcional) Para saber mais sobre o Azure Data Factory, consulte o [percurso de aprendizagem do Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Fábrica de dados e em conjunto do Batch
Fábrica de dados inclui atividades incorporadas, tais como a atividade de cópia para copiar/mover dados de um arquivo de dados de origem para um arquivo de dados de destino e a atividade do ramo de registo para processar dados com clusters do Hadoop (HDInsight) no Azure. Consulte [atividades de transformação de dados](data-factory-data-transformation-activities.md) para uma lista de atividades de transformação suportados.

Também permite-lhe criar atividades personalizadas do .NET para mover ou processar os dados com a sua própria lógica e executar estas atividades num cluster do HDInsight do Azure ou num conjunto de VMs do Azure Batch. Quando utilizar o Azure Batch, pode configurar o conjunto de dimensionamento automático (adicionar ou remover VMs com base na carga de trabalho) com base numa fórmula que fornecer.     

## <a name="architecture-of-sample-solution"></a>Arquitetura de solução de exemplo
Apesar da arquitetura descrita neste artigo destina-se uma solução simples, é relevante para cenários complexos, tais como a modelação de serviços financeiros, processamento de imagem e composição e análise genomic riscos.

O diagrama ilustra 1) como o Data Factory orquestra movimento de dados e de processamento e 2) como o Azure Batch processa os dados de forma paralela. Transferir e imprimir o diagrama para fácil referência (11 x 17 pol. ou tamanho A3): [HPC e de dados orchestration através do Azure Batch e do Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagrama de processamento de dados em grande escala](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

A lista seguinte fornece os passos básicos do processo. A solução inclui código e explicações para compilar a solução ponto-a-ponto.

1. **Configurar o Azure Batch com um conjunto de nós de computação (VMs)**. Pode especificar o número de nós e o tamanho de cada nó.
2. **Criar uma instância do Azure Data Factory** que está configurado com entidades que representam o blob storage do Azure, serviço de computação do Azure Batch, dados de entrada/saída e um fluxo de trabalho pipeline com atividades que mover e transforme dados.
3. **Criar uma atividade personalizada do .NET no pipeline de fábrica de dados**. A atividade é o código de utilizador que é executado no conjunto do Azure Batch.
4. **Armazenar grandes quantidades de dados de entrada que os blobs no armazenamento do Azure**. Dados estão divididos em setores lógicas (normalmente por hora).
5. **Fábrica de dados copia os dados que estão a ser processados em paralelo** para a localização secundária.
6. **Fábrica de dados é executada a atividade personalizada a utilizar o agrupamento atribuído pelo Batch**. Fábrica de dados pode ser executados atividades em simultâneo. Cada atividade processa um setor de dados. Os resultados são armazenados no armazenamento do Azure.
7. **Fábrica de dados move os resultados finais para uma localização terceira**, para distribuição através de uma aplicação ou para processamento adicional por outras ferramentas.

## <a name="implementation-of-sample-solution"></a>Implementação da solução de exemplo
A solução de exemplo é intencionalmente simple e é para lhe mostrar como utilizar o Data Factory e lote em conjunto para processar os conjuntos de dados. A solução de conta simplesmente o número de ocorrências de um termo de pesquisa ("Microsoft") nos ficheiros de entrada organizados de uma série de tempo. -Devolve a contagem de ficheiros de saída.

**Tempo**: Se estiver familiarizado com as noções básicas do Azure, a fábrica de dados e o Batch e concluiu os pré-requisitos listados abaixo, iremos fazer a estimativa esta solução assume 1-2 horas a concluir.

### <a name="prerequisites"></a>Pré-requisitos
#### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição do Azure, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Utilize uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account). A solução de exemplo utiliza o blob storage.

#### <a name="azure-batch-account"></a>Conta de Batch do Azure
Criar uma conta de Batch do Azure utilizando o [portal do Azure](http://portal.azure.com/). Consulte [criar e gerir uma conta do Azure Batch](../../batch/batch-account-create-portal.md). Tenha em atenção a chave de conta e o nome de conta do Azure Batch. Também pode utilizar [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) cmdlet para criar uma conta do Azure Batch. Consulte [introdução aos cmdlets do Azure Batch PowerShell](../../batch/batch-powershell-cmdlets-get-started.md) para obter instruções detalhadas sobre como utilizar este cmdlet.

A solução de exemplo utiliza o Azure Batch (indiretamente através de um pipeline do Azure Data Factory) para processar dados de forma parallel num conjunto de nós de computação (coleção gerida de máquinas virtuais).

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>Conjunto do Batch do Azure de máquinas virtuais (VMs)
Criar um **conjunto do Azure Batch** com, pelo menos, 2 nós de computação.

1. No [portal do Azure](https://portal.azure.com), clique em **procurar** no menu à esquerda e clique **contas do Batch**.
2. Selecione a sua conta do Azure Batch para abrir o **conta do Batch** painel.
3. Clique em **agrupamentos** mosaico.
4. No **agrupamentos** painel, clique em Adicionar botão na barra de ferramentas para adicionar um conjunto.
   1. Introduza um ID para o conjunto (**ID do conjunto**). Tenha em atenção o **ID do conjunto**; apenas quando criar a solução de fábrica de dados.
   2. Especifique **Windows Server 2012 R2** para a definição de família do sistema operativo.
   3. Selecione um **escalão de preço de nó**.
   4. Introduza **2** como valor para o **destino dedicado** definição.
   5. Introduza **2** como valor para o **máx. tarefas por nó** definição.
   6. Clique em **OK** para criar o conjunto.

#### <a name="azure-storage-explorer"></a>Explorador do Storage do Azure
[6 do Explorador de armazenamento do Azure (ferramenta)](https://azurestorageexplorer.codeplex.com/) ou [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (a partir do ClumsyLeaf Software). Utilizar estas ferramentas para inspecionar e alterar os dados nos seus projetos de armazenamento do Azure, incluindo os registos das suas aplicações alojadas na nuvem.

1. Criar um contentor com o nome **mycontainer** com acesso privado (sem acesso anónimo)
2. Se estiver a utilizar **CloudXplorer**, criar pastas e subpastas com a estrutura seguinte:

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`e `outputfolder` são pastas de nível superior no `mycontainer`. O `inputfolder` tem subpastas com carimbos de data / hora (AAAA-MM-DD-HH).

   Se estiver a utilizar **Explorador de armazenamento do Azure**, no próximo passo, terá de carregar ficheiros com nomes: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` e assim sucessivamente. Este passo cria automaticamente as pastas.
3. Criar um ficheiro de texto **file.txt** no seu computador com o conteúdo que tenha a palavra-chave **Microsoft**. Por exemplo: "atividades personalizadas do atividade personalizada Microsoft teste Microsoft de teste".
4. Carregue o ficheiro para as seguintes pastas de entrada no armazenamento de Blobs do Azure.

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   Se estiver a utilizar **Explorador de armazenamento do Azure**, carregue o ficheiro **file.txt** para **mycontainer**. Clique em **cópia** na barra de ferramentas para criar uma cópia do blob. No **copiar Blob** caixa de diálogo, altere o **nome do blob de destino** para `inputfolder/2015-11-16-00/file.txt`. Repita este passo para criar `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` e assim sucessivamente. Esta ação cria automaticamente as pastas.
5. Criar outro contentor com o nome: `customactivitycontainer`. Carregue o ficheiro zip de atividade personalizado para este contentor.

#### <a name="visual-studio"></a>Visual Studio
Instale o Microsoft Visual Studio 2012 ou posterior ao criar a atividade de lote personalizada para ser utilizado na solução de fábrica de dados.

### <a name="high-level-steps-to-create-the-solution"></a>Passos de alto nível para criar a solução
1. Crie uma atividade personalizada que contém a lógica de processamento de dados.
2. Crie um Azure data factory que utiliza a atividade personalizada:

### <a name="create-the-custom-activity"></a>Criar a atividade personalizada
A atividade personalizada da fábrica de dados é o heart desta solução de exemplo. A solução de exemplo utiliza o Azure Batch para executar a atividade personalizada. Consulte [utilizar atividades personalizadas num pipeline do Azure Data Factory](data-factory-use-custom-activities.md) para obter as informações básicas desenvolver atividades personalizadas e utilizá-los no Azure Data Factory pipelines.

Para criar uma atividade personalizada do .NET que pode utilizar um pipeline do Azure Data Factory, tem de criar um **biblioteca de classe de .NET** projeto com uma classe que implementa que **IDotNetActivity** interface. Esta interface tem apenas um método: **executar**. Segue-se a assinatura do método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

O método tem alguns componentes chave que precisa para compreender.

* O método utiliza quatro parâmetros:

  1. **linkedServices**. Uma lista enumeráveis de serviços ligados para ligar a origens de dados de entrada/saída (por exemplo: Blob Storage do Azure) à fábrica de dados. Neste exemplo, não há apenas um serviço ligado do tipo de armazenamento do Azure utilizados para a entrada e saída.
  2. **conjuntos de dados**. Esta é uma lista enumeráveis de conjuntos de dados. Pode utilizar este parâmetro para obter as localizações e esquemas definidas conjuntos de dados de entrada e de saída.
  3. **atividade**. Este parâmetro representa a atual computação entidade - neste caso, um serviço Azure Batch.
  4. **registo**. O registo permite-lhe escrever comentários de depuração esse superfície como o registo de "Utilizador" para o pipeline.
* O método devolve um dicionário que pode ser utilizado encadear atividades personalizadas em conjunto no futuro. Esta funcionalidade ainda não está implementada, por isso, devolver um dicionário vazio do método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimento: Criar a atividade personalizada
1. Crie um projeto da biblioteca de classe de .NET no Visual Studio.

   1. Iniciar **Visual Studio 2012**/**2013/2015**.
   2. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**.
   3. Expanda **modelos**e selecione **Visual C\#**. Nestas instruções, utilizar o C\#, mas pode utilizar qualquer linguagem .NET para desenvolver a atividade personalizada.
   4. Selecione **biblioteca de classes** da lista de tipos de projeto à direita.
   5. Introduza **MyDotNetActivity** para o **nome**.
   6. Selecione **c:\\ADF** para o **localização**. Criar a pasta **ADF** se não existir.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gestor de Pacotes NuGet** e clique em **Consola do Gestor de Pacotes**.
3. No **consola do Gestor de pacotes**, execute o seguinte comando para importar **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importar o **Storage do Azure** pacote NuGet ao projeto. Terá deste pacote porque utilizam o armazenamento de BLOBs API neste exemplo.

    ```powershell
    Install-Package Azure.Storage
    ```
5. Adicione o seguinte **utilizando** diretivas para o ficheiro de origem no projeto.

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Alterar o nome do **espaço de nomes** para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Alterar o nome de classe para **MyDotNetActivity** e derivar-à partir de **IDotNetActivity** interface conforme mostrado abaixo.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementar (adicionar) a **executar** método do **IDotNetActivity** interface para o **MyDotNetActivity** classe e copie o seguinte código de exemplo para o método. Consulte o [executar o método](#execute-method) na secção explicação para a lógica utilizada neste método.

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
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
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
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
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
9. Adicione os seguintes métodos de programa auxiliar para a classe. Estes métodos são invocados pelo **executar** método. Mais importante ainda, a **Calculate** método isola o código que itera através de cada blob.

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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    O **GetFolderPath** método devolve o caminho para a pasta que o dataset aponta para e a **GetFileName** método devolve o nome do que o dataset aponta para o ficheiro/blob.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    O **Calculate** método calcula o número de instâncias de palavra-chave **Microsoft** nos ficheiros de entrada (blobs na pasta). O termo de pesquisa ("Microsoft") é "hard-coded" no código.

1. Compile o projeto. Clique em **criar** do menu e clique em **compilar solução**.
2. Iniciar **Explorador do Windows**e navegue para **bin\\depurar** ou **bin\\versão** pasta dependendo do tipo de compilação.
3. Criar um ficheiro zip **MyDotNetActivity.zip** que contém todos os binários no  **\\bin\\depurar** pasta. Pretende incluir o MyDotNetActivity. **pdb** para que obtenha detalhes adicionais, tais como o número de linha com o código de origem que causou o problema, quando ocorre uma falha de ficheiros.

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. Carregar **MyDotNetActivity.zip** como um blob para o contentor do blob: `customactivitycontainer` do Azure de armazenamento de BLOBs que o **StorageLinkedService** serviço no ligado a  **ADFTutorialDataFactory** utiliza. Criar o contentor de blob `customactivitycontainer` se já existir.

#### <a name="execute-method"></a>Executar o método
Esta secção fornece mais detalhes e notas sobre o código no método de execução.

1. Os membros para repetir a coleção de entrada se encontram no [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) espaço de nomes. Repetir a coleção de blob requer a utilização de **BlobContinuationToken** classe. Essencialmente, tem de utilizar um efetue-ao ciclo com o token de como o mecanismo para sair do ciclo. Para obter mais informações, consulte [como utilizar o Blob storage a partir do .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Um ciclo básico é mostrado aqui:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Consulte a documentação para o [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) método para obter mais detalhes.
2. O código para trabalhar com o conjunto de blobs logicamente passa dentro ao não-ao ciclo. No **executar** método, ao não-enquanto ciclo transfere a lista de blobs para um método denominado **Calculate**. O método devolve uma variável de cadeia denominada **saída** que é o resultado da ter iterated através de todos os blobs no segmento.

   Devolve o número de ocorrências do termo de pesquisa (**Microsoft**) no blob transmitido para o **Calculate** método.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Uma vez a **Calculate** método tem de terminar o trabalho, têm de ser escrito para um blob de novo. Por isso, para cada conjunto de blobs processados, um novo blob pode ser escrito com os resultados. Para escrever um novo blob, localize primeiro o conjunto de dados de saída.

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. O código também chama um método de programa auxiliar: **GetFolderPath** para obter o caminho da pasta (o nome do contentor de armazenamento).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   O **GetFolderPath** casts o objeto do conjunto de dados para um AzureBlobDataSet, que tem uma propriedade chamada FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. As chamadas de código a **GetFileName** método para obter o nome de ficheiro (nome do blob). O código é semelhante para o código acima para obter o caminho da pasta.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. O nome do ficheiro é escrito através da criação de um objeto URI. O construtor URI utiliza o **BlobEndpoint** propriedade para devolver o nome do contentor. O nome de ficheiro e caminho da pasta são adicionados ao construir o URI de blob de saída.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. O nome do ficheiro foi escrito e agora pode escrever a cadeia de saída do **Calculate** método para um novo blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Criar fábrica de dados
No [criar a atividade personalizada](#create-the-custom-activity) secção, criou uma atividade personalizada e carregado o ficheiro zip com binários e o ficheiro PDB para um contentor de Blobs do Azure. Nesta secção, vai criar um Azure **fábrica de dados** com um **pipeline** que utiliza o **atividade personalizada**.

O conjunto de dados de entrada para a atividade personalizada representa os blobs (ficheiros) na pasta de entrada (`mycontainer\\inputfolder`) no armazenamento de Blobs. O conjunto de dados de saída para a atividade representa os blobs de saída na pasta de saída (`mycontainer\\outputfolder`) no armazenamento de Blobs.

Remova um ou mais ficheiros em pastas de entrada:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Por exemplo, remova um ficheiro (file.txt) com o seguinte conteúdo para cada uma das pastas.

```
test custom activity Microsoft test custom activity Microsoft
```

Cada pasta de entrada corresponde a um setor no Azure Data Factory, mesmo se a pasta tiver 2 ou mais ficheiros. Quando cada setor é processado pelo pipeline, a atividade personalizada itera através de todos os blobs na pasta de entrada para esse setor.

Pode ver os cinco ficheiros de saída com o mesmo conteúdo. Por exemplo, o ficheiro de saída do processamento do ficheiro na pasta 2015-11-16-00 tem o seguinte conteúdo:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se remover vários ficheiros (file.txt file2.txt, file3.txt) com os mesmos conteúdos para a pasta de entrada, consulte o conteúdo seguinte no ficheiro de saída. Cada pasta (2015-11-16-00, etc.) corresponde a um setor neste exemplo, apesar da pasta tem vários ficheiros de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

O ficheiro de saída tem três linhas agora, um para cada ficheiro de entrada (blob) na pasta associado o setor (2015-11-16-00).

Uma tarefa é criada para cada execução da atividade. Neste exemplo, não há apenas uma atividade no pipeline. Quando um setor é processado pelo pipeline, a atividade personalizada é executada no Azure Batch para processar o setor. Uma vez que existem cinco setores (cada setor pode ter vários blobs ou ficheiro), existem cinco tarefas criadas no Azure Batch. Quando é executada uma tarefa do batch, está na realidade, a atividade personalizada que está a executar.

As instruções seguintes fornece detalhes adicionais.

#### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Após iniciar sessão para o [portal do Azure](https://portal.azure.com/), efetue os seguintes passos:

   1. Clique em **NOVO** no menu da esquerda.
   2. Clique em **dados + análise** no **novo** painel.
   3. Clique em **Data Factory** no painel **Análise de dados**.
2. No **nova fábrica de dados** painel, introduza **CustomActivityFactory** para o nome. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **nome da fábrica de dados "CustomActivityFactory" não está disponível**, alterar o nome da fábrica de dados (por exemplo, **yournameCustomActivityFactory**) e tente criar novamente.
3. Clique em **nome do grupo de recursos**e selecione um grupo de recursos existente ou crie um grupo de recursos.
4. Certifique-se de que está a utilizar a subscrição correta e a região onde pretende que a fábrica de dados ser criada.
5. Clique em **Criar** no painel **Nova fábrica de dados**.
6. Consulte a fábrica de dados a ser criada no **Dashboard** do portal do Azure.
7. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo.

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Neste passo, ligue o **Storage do Azure** conta e **do Azure Batch** conta à fábrica de dados.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. Clique em de **autor e implementar** mosaico a **DATA FACTORY** painel **CustomActivityFactory**. Consulte o Editor do Data Factory.
2. Clique em **novo arquivo de dados** no comando barra e escolha **storage do Azure.** Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor.

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. Substitua o **nome da conta** pelo nome da sua conta de armazenamento do Azure e a **chave da conta** pela chave de acesso da conta de armazenamento do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Criar o serviço ligado do Azure Batch
Neste passo, vai criar um serviço ligado para sua **do Azure Batch** conta que é utilizada para executar a atividade personalizada da fábrica de dados.

1. Clique em **nova computação** no comando barra e escolha **do Azure Batch.** Deverá ver o script JSON para criar um serviço ligado do Azure Batch no editor.
2. O script de JSON:

   1. Substitua **nome da conta** com o nome da sua conta do Azure Batch.
   2. Substitua **chave de acesso** com a chave de acesso da conta do Azure Batch.
   3. Introduza o ID do conjunto para o **poolName** propriedade**.** Para esta propriedade, pode especificar o nome do conjunto ou agrupamento ID.
   4. Introduza o URI do lote para o **batchUri** propriedade JSON.

      > [!IMPORTANT]
      > O **URL** do **painel de conta do Azure Batch** é o seguinte formato: \<accountname\>.\< região\>. batch.azure.com. Para o **batchUri** propriedade no JSON, terá de **remover "accountname."** partir do URL. Exemplo: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      Para o **poolName** propriedade, também pode especificar o ID do conjunto em vez do nome do conjunto.

      > [!NOTE]
      > O serviço fábrica de dados não suporta uma opção a pedido para o Azure Batch como para o HDInsight. Só pode utilizar o seu próprio conjunto do Azure Batch num Azure data factory.
      >
      >
   5. Especifique **StorageLinkedService** para o **linkedServiceName** propriedade. Este serviço ligado que criou no passo anterior. Este tipo de armazenamento é utilizado como uma área de transição para ficheiros e registos.
3. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e de saída.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** do Data Factory, clique em **novo conjunto de dados** botão na barra de ferramentas e clique em **Blob storage do Azure** no menu pendente.
2. Substitua JSON no painel à direita com o fragmento JSON seguinte:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    Pode cria um pipeline mais tarde esta explicação passo a passo com a hora de início: 2015-11-tempo 16T00:00:00Z e de fim: 2015-11-16T05:00:00Z. Está agendada para produzir dados **hora a hora**, pelo que existem 5 setores de entrada/saída (entre **00**: 00:00 -\> **05**: 00:00).

    O **frequência** e **intervalo** o conjunto de dados de entrada está definida como **hora** e **1**, o que significa que o setor de entrada está disponível por hora.

    Seguem-se as horas de início de cada setor, que são representadas por **SliceStart** variável de sistema no fragmento JSON acima.

    | **Setor** | **Hora de início**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**: 00:00 |
    | 2         | 2015-11-16T**01**: 00:00 |
    | 3         | 2015-11-16T**02**: 00:00 |
    | 4         | 2015-11-16T**03**: 00:00 |
    | 5         | 2015-11-16T**04**: 00:00 |

    O **folderPath** é calculado utilizando a parte ano, mês, dia e hora a hora de início do setor (**SliceStart**). Por conseguinte, eis como uma pasta de entrada está mapeada para um setor.

    | **Setor** | **Hora de início**          | **Pasta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16-**04** |

1. Clique em **implementar** na barra de ferramentas para criar e implementar o **InputDataset** tabela.

#### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Neste passo, crie outro conjunto de dados do tipo AzureBlob para representar os dados de saída.

1. No **Editor** do Data Factory, clique em **novo conjunto de dados** botão na barra de ferramentas e clique em **Blob storage do Azure** no menu pendente.
2. Substitua JSON no painel à direita com o fragmento JSON seguinte:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Um ficheiro/blob de saída é gerado para cada setor de entrada. Eis como um ficheiro de saída é designado para cada setor. Todos os ficheiros de saída são gerados por uma pasta de saída: `mycontainer\\outputfolder`.

    | **Setor** | **Hora de início**          | **Ficheiro de saída**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16 -**00. txt** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16 -**01. txt** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16 -**02. txt** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16 -**03. txt** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16 -**04. txt** |

    Lembre-se de que todos os ficheiros numa pasta entrada (por exemplo: 2015-11-16-00) fazem parte de um setor com a hora de início: 2015-11-16-00. Quando este setor é processado, a atividade personalizada analisa através de cada ficheiro e produz uma linha no ficheiro de saída com o número de ocorrências do termo de pesquisa ("Microsoft"). Se existirem três ficheiros na pasta 2015-11-16-00, existem três linhas no ficheiro de saída: 2015-11-16-00.txt.

1. Clique em **implementar** na barra de ferramentas para criar e implementar o **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>Passo 4: Criar e executar o pipeline com atividade personalizada
Neste passo, vai criar um pipeline com uma atividade, a atividade personalizada que criou anteriormente.

> [!IMPORTANT]
> Se ainda não adicionou o **file.txt** para pastas no contentor do blob de entrada, fazer antes de criar o pipeline. O **isPaused** propriedade está definida como false no pipeline JSON, pelo que o pipeline é executado imediatamente como o **iniciar** data está no passado.
>
>

1. No Editor de fábrica de dados, clique em **novo pipeline** na barra de comandos. Se não vir o comando, clique em **... (Reticências)**  para vê-lo.
2. Substitua JSON no painel à direita com o seguinte script JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Tenha em atenção os seguintes pontos:

   * Não existe apenas uma atividade no pipeline e que é do tipo: **DotNetActivity**.
   * **AssemblyName** está definido como o nome da DLL: **MyDotNetActivity.dll**.
   * **EntryPoint** está definido como **MyDotNetActivityNS.MyDotNetActivity**. É basicamente \<espaço de nomes\>.\< ClassName\> no seu código.
   * **PackageLinkedService** está definido como **StorageLinkedService** que aponta para o armazenamento de blob que contém o ficheiro zip de atividade personalizado. Se estiver a utilizar diferentes contas de armazenamento do Azure para ficheiros de entrada/saída e o ficheiro zip de atividade personalizado, terá de criar outro serviço ligado do Storage do Azure. Este artigo pressupõe que está a utilizar a mesma conta de armazenamento do Azure.
   * **PackageFile** está definido como **customactivitycontainer/MyDotNetActivity.zip**. Está no formato: \<containerforthezip\>/\<nameofthezip.zip\>.
   * A atividade personalizada demora **InputDataset** como entrada e **OutputDataset** como saída.
   * O **linkedServiceName** propriedade da atividade personalizada aponta para o **AzureBatchLinkedService**, que indica que o Azure Data Factory que a atividade personalizada tem de executar no Azure Batch.
   * O **simultaneidade** definição é importante. Se utilizar o valor predefinido é 1, mesmo se tiver de 2 ou mais nós de computação num conjunto do Azure Batch, são processados os setores umas a seguir. Por conseguinte, não são tirar partido da capacidade de processamento paralelo de lote do Azure. Se definir **simultaneidade** para um valor superior, diga 2, significa que dois setores (corresponde à duas tarefas no Azure Batch) podem ser processados em simultâneo, caso em que ambas as VMs do Azure Batch são utilizados de agrupamento. Por conseguinte, adequadamente a definir a propriedade de concorrência.
   * Por predefinição, apenas uma tarefa (setor) é executada numa VM em qualquer momento. O motivo é que, por predefinição, o **máximo de tarefas por VM** está definido como 1 para um conjunto do Azure Batch. Como parte da pré-requisitos, criou um conjunto com esta propriedade definida como 2, pelo que podem executar dois ciclos de fábrica de dados numa VM ao mesmo tempo.

    -   **isPaused** propriedade está definida como false, por predefinição. O pipeline executa imediatamente neste exemplo, porque os setores de início no passado. Pode definir esta propriedade para verdadeiro para colocar em pausa o pipeline e configure-o para false para reiniciar.

    -   O **iniciar** tempo e **final** vezes são, à excepção de cinco horas e setores são produzidos de hora a hora, pelo que os cinco setores são produzidos pelo pipeline.

1. Clique em **Implementar** na barra de comandos para implementar o pipeline.

#### <a name="step-5-test-the-pipeline"></a>Passo 5: Testar o pipeline
Neste passo, teste o pipeline, arrastando ficheiros para as pastas de entrada. Vamos começar a utilizar o pipeline de teste com um ficheiro por uma pasta de entrada.

1. No painel do Data Factory no portal do Azure, clique em **diagrama**.

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. Na vista de diagrama, faça duplo clique em conjunto de dados de entrada: **InputDataset**.

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. Deverá ver o **InputDataset** painel com todos os cinco setores pronto. Tenha em atenção o **hora de início do SETOR** e **hora de fim de SETOR** para cada setor.

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. No **vista de diagrama**, agora, clique em **OutputDataset**.
5. Deverá ver que os setores de cinco saída estão no estado pronto se que já foram produzidos.

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. Utilize o portal do Azure para ver o **tarefas** associados a **setores** e ver que VM cada setor é executado no. Consulte [integração com o Data Factory e Batch](#data-factory-and-batch-integration) secção para obter detalhes.
7. Deverá ver os ficheiros de saída no `outputfolder` de `mycontainer` no seu Azure armazenamento de Blobs.

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   Deverá ver cinco ficheiros de saída, uma para cada setor de entrada. Cada ficheiro de saída deve ter conteúdo semelhante ao seguinte resultado:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   O diagrama seguinte ilustra a forma como os setores do Data Factory mapeiam tarefas no Batch do Azure. Neste exemplo, um setor tem apenas uma execução.

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. Agora, vamos tentar com vários ficheiros numa pasta. Criar ficheiros: **file2.txt**, **file3.txt**, **file4.txt**, e **file5.txt** com os mesmos conteúdos como file.txt na pasta:  **2015-11-06-01**.
9. Na pasta de saída, **eliminar** o ficheiro de saída: **2015-11-16-01.txt**.
10. Agora, no **OutputDataset** painel, o setor com o botão direito do rato **hora de início do SETOR** definido como **11/16/2015 01:00:00 AM**e clique em **executar** para Volte a executar/re-process o setor. Agora, o setor tem cinco ficheiros em vez de um ficheiro.

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. Depois de executa o setor e o respetivo estado é **pronto**, verifique o conteúdo no ficheiro de saída neste setor (**2015-11-16-01.txt**) no `outputfolder` de `mycontainer` no seu armazenamento de Blobs. Deve ser uma linha para cada ficheiro do setor.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se não foi possível eliminar a saída ficheiro 2015-11-16-01.txt antes de tentar com cinco ficheiros de entrada, verá uma linha de execução anterior do setor e cinco linhas da execução do setor atual. Por predefinição, o conteúdo é acrescentado ao ficheiro de saída, se já existir.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integração de fábrica de dados e de Batch
O serviço Data Factory cria uma tarefa no Azure Batch com o nome: `adf-poolname:job-xxx`.

![O Azure Data Factory - tarefas do Batch](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Uma tarefa a tarefa é criada para cada execução de atividade de um setor. Se existirem 10 setores prontos a ser processado, são criadas 10 tarefas na tarefa. Pode ter mais do que um setor ser executado em paralelo se tiver vários nós de computação no conjunto. Se as tarefas por nó de computação máximas for definido como > 1, podem existir mais do que um setor ser executado na computação mesma.

Neste exemplo, existem cinco setores, por isso, cinco tarefas no Azure Batch. Com o **simultaneidade** definido como **5** no pipeline JSON no Azure Data Factory e **máximo de tarefas por VM** definido como **2** no conjunto do Azure Batch com **2** VMs, as rápida de execuções de tarefas (Verifique os tempos de início e de fim para tarefas).

Utilizar o portal para ver a tarefa do Batch e das respetivas tarefas associadas a **setores** e ver que VM cada setor é executado no.

![O Azure Data Factory - tarefas de lote](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depurar o pipeline
A depuração inclui algumas técnicas básicas:

1. Se o setor de entrada não está definido como **pronto**, confirme que a estrutura da pasta de entrada está correta e file.txt existe nas pastas de entrada.

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. No **executar** método da atividade personalizada, utilize o **IActivityLogger** objeto para registar informações que o ajuda a resolver problemas. As mensagens anteriormente registadas apresentada ao utilizador\_0. ficheiro de registo.

   No **OutputDataset** painel, clique o setor para ver o **SETOR de dados** painel para essa setor. Verá **execuções de atividade** para esse setor. Deverá ver uma atividade executar para o setor. Se clicar em **executar** na barra de comandos, pode começar a executar para o mesmo setor de outra atividade.

   Ao clicar a execução da atividade, consulte o **detalhes da execução da ATIVIDADE** painel com uma lista de ficheiros de registo. Consulte as mensagens anteriormente registadas no **utilizador\_0. registo** ficheiro. Quando ocorre um erro, consulte três execuções de atividade porque a contagem de repetições está definida para 3 no pipeline/JSON de atividade. Ao clicar a execução de atividade, consulte os ficheiros de registo que poderá consultar para resolver o erro.

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   Na lista de ficheiros de registo, clique o **utilizador 0.log**. No painel à direita são os resultados de utilizar o **IActivityLogger.Write** método.

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   Verifique o sistema-0.log para quaisquer mensagens de erro de sistema e exceções.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Incluir o **PDB** no ficheiro zip do ficheiro para que os detalhes do erro tem informações como **pilha de chamadas** quando ocorre um erro.
4. Todos os ficheiros no ficheiro zip para a atividade personalizada tem de estar no **principais nível** com sem subpastas.

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. Certifique-se de que o **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), e **packageLinkedService** (devem apontar para o armazenamento de Blobs do Azure que contém o ficheiro zip) estão definidos para valores corretos.
6. Se corrigir um erro e pretender processar de novo o setor, clique com o botão direito do rato no setor no painel **OutputDataset** e clique em **Executar**.

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Verá um **contentor** no seu armazenamento de Blobs do Azure com o nome: `adfjobs`. Este contentor não é eliminado automaticamente, mas pode eliminar em segurança depois de terminar a solução de teste. Da mesma forma, a solução Data Factory cria um Azure Batch **tarefa** com o nome: `adf-\<pool ID/name\>:job-0000000001`. É possível eliminar esta tarefa depois de testar a solução se assim o desejar.
   >
   >
7. A atividade personalizada não utiliza o **App. config** ficheiro a partir do seu pacote. Por conseguinte, se o seu código lê quaisquer cadeias de ligação no ficheiro de configuração, não funciona no tempo de execução. A melhor prática, quando utilizar o Azure Batch é reter os segredos num **Azure KeyVault**, utilize um principal de serviço baseada em certificado para proteger o keyvault e distribuir o certificado para o conjunto do Azure Batch. A atividade personalizada de .NET pode, então, aceder aos segredos do Cofre de Chaves durante o tempo de execução. Esta solução é um genérico e pode dimensionar para qualquer tipo de segredo, não apenas a cadeia de ligação.

    Há uma solução mais fácil (mas não uma boa prática): pode criar um **serviço ligado de SQL do Azure** com as definições de cadeia de ligação, crie um conjunto de dados que utiliza o serviço ligado e encadeiam o conjunto de dados como um conjunto de dados de entrada fictício para o atividade personalizada do .NET. Em seguida, pode aceder a cadeia de ligação de serviço ligado com o código de atividades personalizadas e deverão funcionar ajustar no tempo de execução.  

#### <a name="extend-the-sample"></a>Expandir o exemplo
Pode expandir este exemplo para obter mais informações sobre as funcionalidades do Azure Data Factory e do Azure Batch. Por exemplo, para processar setores um intervalo de hora diferente, efetue os seguintes passos:

1. Adicione as seguintes subpastas no `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07, 2011-11-16-08, 2015-11-16-09 e coloque ficheiros de entrada nessas pastas. Alterar a hora de fim para o pipeline de `2015-11-16T05:00:00Z` para `2015-11-16T10:00:00Z`. No **vista de diagrama**, faça duplo clique o **InputDataset**e confirme que os setores de entrada estão prontos. Faça duplo clique em **OuptutDataset** para ver o estado de setores de saída. Se forem no estado pronto, verifique a pasta de saída para os ficheiros de saída.
2. Aumentar ou diminuir o **simultaneidade** definição para compreender como afeta o desempenho da sua solução, especialmente o processamento ocorre no Azure Batch. (Consulte o passo 4: criar e executar o pipeline para obter mais informações o **simultaneidade** definição.)
3. Criar um conjunto com maior/menor **máximo de tarefas por VM**. Para utilizar o novo conjunto que criou, atualize o serviço ligado do Azure Batch na solução de fábrica de dados. (Consulte o passo 4: criar e executar o pipeline para obter mais informações o **máximo de tarefas por VM** definição.)
4. Criar um conjunto do Azure Batch com **dimensionamento automático** funcionalidade. Dimensionar automaticamente nós de computação num conjunto do Azure Batch é o ajustamento dinâmico do processamento de energia utilizada pela sua aplicação. 

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
5. Na solução de exemplo, o **executar** invoca o método de **Calculate** método que processe um setor de dados de entrada para produzir um setor de dados de saída. Pode escrever o seu próprio método para processar dados de entrada e substitua a chamada de método Calculate no método de execução de uma chamada para o método.

### <a name="next-steps-consume-the-data"></a>Próximos passos: consumir dados
Depois de processar dados, pode consumi-lo com ferramentas online como **Microsoft Power BI**. Seguem-se ligações para o ajudar a compreender o Power BI e como utilizá-la no Azure:

* [Explore um conjunto de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Atualizar dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e o Power BI - descrição geral básica](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referências
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introdução ao serviço Azure Data Factory](data-factory-introduction.md)
  * [Introdução ao Azure Data Factory](data-factory-build-your-first-pipeline.md)
  * [Utilizar atividades personalizadas num pipeline do Azure Data Factory (Use custom activities in an Azure Data Factory pipeline)](data-factory-use-custom-activities.md)
* [O Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Noções básicas do Azure Batch](../../batch/batch-technical-overview.md)
  * [Descrição geral das funcionalidades do Azure Batch](../../batch/batch-api-basics.md)
  * [Criar e gerir a conta do Azure Batch no portal do Azure](../../batch/batch-account-create-portal.md)
  * [Introdução à biblioteca .NET do Azure Batch](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
