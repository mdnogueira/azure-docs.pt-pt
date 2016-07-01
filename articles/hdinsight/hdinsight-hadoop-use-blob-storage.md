<properties
    pageTitle="Consultar dados a partir do Blob Storage compatível com HDFS | Microsoft Azure"
    description="O HDInsight utiliza o Blob Storage do Azure como o arquivo de macrodados para o HDFS. Saiba como consultar dados a partir do Blob Storage e armazenar os resultados da sua análise."
    keywords="blob storage,hdfs,structured data,unstructured data"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/18/2016"
    ms.author="jgao"/>


# Utilizar o Blob Storage do Azure compatível com HDFS com o Hadoop no HDInsight

Saiba como utilizar o Blob Storage de baixo custo do Azure com o HDInsight, como criar uma conta do Storage e um contentor de Blob Storage do Azure e como processar os dados aí guardados.

O Blob Storage do Azure é uma solução de armazenamento para fins gerais robusta que se integra perfeitamente com o HDInsight. Através de uma interface HDFS (Sistema de Ficheiros Distribuído Hadoop), o conjunto completo de componentes do HDInsight pode operar diretamente em dados estruturados ou não estruturados no Blob Storage.

O armazenamento de dados no Blob Storage permite eliminar em segurança os clusters do HDInsight que são utilizados para o cálculo sem que haja perda de dados do utilizador.

> [AZURE.NOTE]  A sintaxe *asv://* não é suportada nos clusters do HDInsight versão 3.0. Como tal, as tarefas submetidas para um cluster do HDInsight versão 3.0 que utilizem explicitamente a sintaxe *asv://* falharão. Em vez disso, deve ser utilizada a sintaxe *wasb://*. Além disso, as tarefas submetidas para os clusters do HDInsight versão 3.0 que tenham sido criadas com um metastore existente que contenha referências explícitas a recursos que utilizam a sintaxe asv:// também falharão. É necessário voltar a criar estes metastores utilizando a sintaxe wasb:// para endereçar os recursos.

> Atualmente, o HDInsight suporta apenas blobs de blocos.

> A maioria dos comandos HDFS (por exemplo, <b>ls</b>, <b>copyFromLocal</b> e <b>mkdir</b>) continua a funcionar conforme esperado. Apenas os comandos específicos da implementação nativa do HDFS (que é conhecida como DFS), tal como <b>fschk</b> e <b>dfsadmin</b>, apresentarão um comportamento diferente no Blob Storage do Azure.

Para obter informações sobre a criação de um cluster do HDInsight, consulte [Introdução ao HDInsight][hdinsight-get-started] ou [Criar clusters do HDInsight][hdinsight-creation].


## Arquitetura de armazenamento do HDInsight
O diagrama seguinte apresenta uma vista abstrata da arquitetura de armazenamento do HDInsight:

![Os clusters do Hadoop utilizam a API do HDFS para aceder e armazenar dados estruturados e não estruturados no Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage Architecture")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, o HDInsight oferece a capacidade de aceder aos dados armazenados no Blob Storage do Azure. A sintaxe é:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


O Hadoop suporta uma noção do sistema de ficheiros predefinido. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação do HDInsight, uma conta do Storage do Azure e um contentor de Blob Storage do Azure específico dessa conta são designados como o sistema de ficheiros predefinido.

Além desta conta do Storage, pode adicionar mais contas do Storage da mesma subscrição do Azure ou de diferentes subscrições do Azure durante o processo de criação. Para obter instruções sobre como adicionar mais contas do Storage, consulte [Criar clusters do HDInsight][hdinsight-creation].

- **Contentores nas contas do Storage ligadas a um cluster:** como o nome e a chave da conta são associados ao cluster durante a criação, tem acesso total aos blobs desses contentores.

- **Contentores públicos ou blobs públicos em contas do Storage NÃO ligadas a um cluster:** tem permissão só de leitura para os blobs dos contentores.

    > [AZURE.NOTE]
        > Os contentores públicos permitem obter uma lista de todos os blobs disponíveis nesse contentor, bem como os metadados do mesmo. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Restringir o acesso a contentores e blobs</a>.

- **Contentores privados em contas do Storage NÃO ligadas a um cluster:** apenas pode aceder aos blobs dos contentores se definir a conta do Storage ao submeter as tarefas de WebHCat. Isto é explicado posteriormente neste artigo.


As contas do Storage definidas durante o processo de criação e as respetivas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. O comportamento predefinido do HDInsight é utilizar as contas do Storage definidas no ficheiro core-site.xml. Não é recomendado editar o ficheiro core-site.xml porque a imagem do nó principal do cluster pode ser recriada ou esse nó pode migrado em qualquer altura, pelo que se perderão as alterações efetuadas a esses ficheiros.

Várias tarefas de WebHCat, incluindo Hive, MapReduce, transmissão em fluxo do Hadoop e Pig, podem conter uma descrição das contas do Storage e metadados. (Atualmente, isto funciona para o Pig com contas do Storage, mas não com metadados.) Na secção [Aceder a blobs com o Azure PowerShell](#powershell) deste artigo, é apresentado um exemplo desta funcionalidade. Para obter mais informações, consulte [Utilizar um Cluster do HDInsight com Contas do Storage e Metastores Alternativos](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

É possível utilizar o Blob Storage para dados estruturados e não estruturados. Os contentores do Blob Storage armazenam dados como pares chave/valor e não existe uma hierarquia de diretórios. No entanto, o caráter de barra (/) pode ser utilizado no nome da chave para fazer com que pareça que um ficheiro está armazenado numa estrutura de diretórios. Por exemplo, a chave de um blob poderá ser *input/log1.txt*. O diretório *input* não existe realmente, mas a presença do caráter de barra no nome da chave cria o aspeto de um caminho de ficheiro.

###<a id="benefits"></a>Vantagens do Blob Storage
O custo de desempenho implícito de não ter clusters de cálculo e recursos de armazenamento colocalizados é atenuado pela forma como os clusters de cálculo são criados perto dos recursos da conta do Storage na região do Azure, onde a rede de alta velocidade permite que os nós de cálculo acedam aos dados do Blob Storage do Azure de forma muito eficiente.

Existem várias vantagens associadas ao armazenamento de dados no Blob Storage do Azure em vez do HDFS:

* **Partilha e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de cálculo. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. É possível aceder aos dados no Blob Storage do Azure através das APIs do HDFS ou através das [APIs REST do Blob Storage][blob-storage-restAPI]. Assim, pode-se utilizar um conjunto maior de ferramentas e aplicações (incluindo outros clusters do HDInsight) para produzir e consumir dados.
* **Arquivo de dados:** o armazenamento de dados no Blob Storage do Azure permite eliminar em segurança os clusters do HDInsight utilizados para o cálculo sem que haja perda de dados do utilizador.
* **Custo do armazenamento de dados:** armazenar os dados no DFS a longo prazo é mais dispendioso do que armazenar os dados no Blob Storage do Azure, uma vez que o custo de um cluster de cálculo é superior ao custo de um contentor de Blob Storage do Azure. Além disso, uma vez que não é necessário recarregar os dados para cada geração de cluster de cálculo, também reduz os custos do carregamento de dados.
* **Aumento horizontal elástico:** embora o HDFS forneça um sistema de ficheiros ampliado horizontalmente, o dimensionamento é determinado pelo número de nós que cria para o cluster. A alteração do dimensionamento pode tornar-se um processo mais complexo do que depender das capacidades de dimensionamento elástico que obtém automaticamente com o Blob Storage do Azure.
* **Georreplicação:** os contentores de Blob Storage do Azure podem ser georreplicados. Apesar de esta funcionalidade oferecer recuperação geográfica e redundância de dados, uma ativação pós-falha para a localização georreplicada afeta seriamente o desempenho e pode implicar custos adicionais. Por isso, recomendamos que escolha a georreplicação de forma sensata e apenas se o valor dos dados justificar o custo adicional.

Determinados pacotes e tarefas de MapReduce podem criar resultados intermédios que não pretende realmente armazenar no Blob Storage do Azure. Nesse caso, pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight utiliza o DFS para vários destes resultados intermédios nas tarefas do Hive e noutros processos.



## Criar contentores de blobs

Para utilizar blobs, primeiro tem de criar uma [Conta do Storage do Azure][azure-storage-create]. Como parte deste processo, deve especificar uma região do Azure que irá armazenar os objetos criados com esta conta. O cluster e a conta do Storage têm de estar alojados na mesma região. A base de dados do SQL Server do metastore do Hive e a base de dados do SQL Server do metastore do Oozie também têm de estar localizadas na mesma região.

Independentemente do local onde se encontre, cada blob que criar pertence a um contentor na sua conta do Storage do Azure. Este contentor pode ser um blob existente que tenha sido criado fora do HDInsight ou um contentor criado para um cluster do HDInsight.


O contentor de blobs predefinido armazena informações específicas do cluster como o histórico de tarefas e os registos. Não partilhe um contentor de blobs predefinido com vários clusters do HDInsight. Tal poderá danificar o histórico de tarefas e o cluster comportar-se-á de forma incorreta. É recomendável utilizar um contentor diferente para cada cluster e colocar os dados partilhados numa conta do Storage ligada especificada na implementação de todos os clusters relevantes em vez da conta do Storage predefinida. Para obter mais informações sobre como configurar contas do Storage ligadas, consulte [Criar clusters do HDInsight][hdinsight-creation]. No entanto, pode reutilizar um contentor de armazenamento predefinido depois de o cluster do HDInsight original ser eliminado. Para clusters do HBase, pode manter os dados e o esquema da tabela do HBase ao criar um novo cluster do HBase com o contentor do Blob Storage predefinido utilizado por um cluster do HBase que foi eliminado.


### Utilizar o Portal do Azure

Ao criar um cluster do HDInsight no Portal, tem a opção de utilizar uma conta do Storage existente ou criar uma nova conta do Storage:

![hdinsight hadoop criação origem de dados](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###Utilizar a CLI do Azure

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Se tiver [instalado e configurado a CLI do Azure](../xplat-cli-install.md), pode utilizar o seguinte comando para uma conta do Storage e um contentor.

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE] O parâmetro `--type` indica a forma como a conta do Storage será replicada. Para obter mais informações, consulte [Replicação do Storage do Azure](../storage/storage-redundancy.md). Não utilize ZRS, uma vez que o ZRS não suporta blob de páginas, ficheiro, tabela ou fila.

É-lhe pedido que especifique a região geográfica na qual a conta do Storage estará localizada. Deve criar a conta do Storage na mesma região em que pretende criar o cluster do HDInsight.

Assim que a conta do Storage for criada, utilize o seguinte comando para obter as chaves da conta do Storage:

    azure storage account keys list <storageaccountname>

Para criar um contentor, utilize o seguinte comando:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### Utilizar o Azure PowerShell

Se tiver [instalado e configurado o Azure PowerShell][powershell-install], pode utilizar o seguinte prompt do Azure PowerShell para criar uma conta do Storage e um contentor:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    
    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID
    
    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location
    
    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 
    
    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

## Endereçar ficheiros no Blob Storage

O esquema URI para aceder a ficheiros no Blob Storage a partir do HDInsight é:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] A sintaxe para endereçar os ficheiros no emulador de armazenamento (em execução no emulador do HDInsight) é <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



O esquema URI fornece acesso não encriptado (com o prefixo *wasb:*) e acesso encriptado por SSL (com *wasbs*). Recomendamos a utilização de *wasbs* sempre que possível, mesmo ao aceder a dados que se encontrem dentro da mesma região no Azure.

O &lt;BlobStorageContainerName&gt; identifica o nome do contentor no Blob Storage do Azure.
O &lt;StorageAccountName&gt; identifica o nome de conta do Storage do Azure. É necessário um nome de domínio completamente qualificado (FQDN).

Se o &lt;BlobStorageContainerName&gt; e o &lt;StorageAccountName&gt; não tiverem sido especificados, é utilizado o sistema de ficheiros predefinido. Para os ficheiros no sistema de ficheiros predefinido, pode utilizar um caminho relativo ou um caminho absoluto. Por exemplo, é possível fazer referência ao ficheiro *hadoop-mapreduce-examples.jar* incluído nos clusters do HDInsight ao utilizar um dos seguintes procedimentos:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] O nome de ficheiro é <i>hadoop-examples.jar</i> nos clusters do HDInsight versões 2.1 e 1.6.


O &lt;path&gt; é o nome do caminho do HDFS do ficheiro ou do diretório. Como os contentores do Blob Storage do Azure são simplesmente arquivos de chave-valor, não existe qualquer sistema de ficheiros hierárquico verdadeiro. Um caráter de barra (/) numa chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob de *hadoop-mapreduce-examples.jar* é:

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhece o formato WASB e, em vez disso, espera um formato de caminho básico, tal como `example/jars/hadoop-mapreduce-examples.jar`.

## Aceder a blobs com a CLI do Azure

Utilize o seguinte comando para listar os comandos relacionados com blobs:

    azure storage blob

**Exemplo de utilização da CLI do Azure para carregar um ficheiro**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemplo de utilização da CLI do Azure para transferir um ficheiro**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemplo de utilização da CLI do Azure para eliminar um ficheiro**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemplo de utilização da CLI do Azure para listar ficheiros**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## Aceder a blobs com o Azure PowerShell

> [AZURE.NOTE] Os comandos nesta secção fornecem um exemplo básico da utilização do PowerShell para aceder a dados armazenados em blobs. Para obter um exemplo mais completo personalizado para trabalhar com o HDInsight, consulte as [Ferramentas do HDInsight](https://github.com/Blackmist/hdinsight-tools).

Utilize o seguinte comando para listar os cmdlets relacionados com blobs:

    Get-Command *blob*

![Lista de cmdlets do PowerShell relacionados com blobs.][img-hdi-powershell-blobcommands]

###Carregar ficheiros

Consulte [Carregar dados para o HDInsight][hdinsight-upload-data].

###Transferir ficheiros

O script seguinte transfere um blob de blocos para a pasta atual. Antes de executar o script, altere o diretório para uma pasta em que tenha permissões de escrita.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

Ao fornecer o nome do grupo de recursos e o nome do cluster, pode utilizar o seguinte código:

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

###Eliminar ficheiros


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###Listar ficheiros

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###Executar consultas do Hive com uma conta do Storage não definida

Este exemplo mostra como listar uma pasta de uma conta do Storage que não é definida durante o processo de criação.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## Passos seguintes

Neste artigo, aprendeu a utilizar o Blob Storage do Azure compatível com HDFS com o HDInsight e que o Blob Storage do Azure é um componente fundamental do HDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis com o Blob Storage do Azure e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Carregar dados para o HDInsight][hdinsight-upload-data]
* [Utilizar o Hive com o HDInsight][hdinsight-use-hive]
* [Utilizar o Pig com o HDInsight][hdinsight-use-pig]
* [Utilizar Assinaturas de Acesso Partilhado do Storage do Azure para restringir o acesso aos dados com o HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  



<!--HONumber=Jun16_HO2-->


