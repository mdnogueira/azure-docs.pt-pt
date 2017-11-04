---
title: Instalar e utilizar Giraph em clusters do Hadoop no HDInsight - Azure | Microsoft Docs
description: Saiba como personalizar o cluster do HDInsight com Giraph e como utilizar Giraph.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 77a1d0e0-55de-4e61-98a0-060914fb7ca0
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f0eb5c1f457380600463a370043f03e6d655a02c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-giraph-on-windows-based-hdinsight-clusters"></a>Instalar e utilizar Giraph nos clusters do HDInsight baseados em Windows

Saiba como personalizar o cluster do HDInsight baseado em Windows com Giraph através da ação de Script e como utilizar Giraph para processar gráficos em grande escala. Para obter informações sobre a utilização Giraph com um cluster baseado em Linux, consulte [Giraph instalar em clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]
> Os passos neste documento apenas trabalham com clusters do HDInsight baseados em Windows. HDInsight só está disponível no Windows para as versões inferiores ao HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). Para obter informações sobre como instalar Giraph num cluster do HDInsight baseado em Linux, consulte [Giraph instalar em clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Pode instalar Giraph em qualquer tipo de cluster (Hadoop, Storm, HBase, Spark) no Azure HDInsight utilizando *ação de Script*. Um script de exemplo para instalar Giraph num cluster do HDInsight está disponível a partir de um blob de armazenamento do Azure só de leitura em [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). O script de amostra funciona apenas com clusters do HDInsight versão 3.1. Para obter mais informações sobre as versões de cluster do HDInsight, consulte [as versões de cluster do HDInsight](hdinsight-component-versioning.md).

**Artigos relacionados**

* [Instalar Giraph em clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Criar clusters do Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de clusters do HDInsight.
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização clusters do HDInsight através da ação de Script.
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>O que é Giraph?
<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permite-lhe efetuar o processamento através da utilização do Hadoop e pode ser utilizado com o Azure HDInsight. Gráficos modelar relações entre objetos, tais como as ligações entre routers numa rede grande, como a Internet ou relações entre as pessoas em redes sociais (algumas vezes referidas como um gráfico de redes social). Processamento de gráfico permite-lhe razão sobre as relações entre objetos de um gráfico, tais como:

* Identificar potenciais amigos com base na sua relações atuais.
* Identificar a rota mais curta entre dois computadores numa rede.
* Calcular a classificação de página de páginas Web.

## <a name="install-giraph-using-portal"></a>Instalar Giraph através do portal
1. Iniciar criação de um cluster utilizando o **criação personalizada** opção, conforme descrito em [clusters do Hadoop criar no HDInsight](hdinsight-provision-clusters.md).
2. No **ações de Script** página do assistente, clique em **adicionar ação de script** para fornecer os detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Utilize a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "utilize a ação de Script para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script. Por exemplo, <b>instalar Giraph</b>.</td></tr>
        <tr><td>URI de script</td>
            <td>Especifique o Uniform Resource Identifier (URI) para o script que é invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de Nó</td>
            <td>Especifique os nós em que o script de personalização é executado. Pode escolher <b>todos os nós</b>, <b>apenas nós principais</b>, ou <b>apenas nós de trabalho</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário, o script. O script para instalar Giraph não requer quaisquer parâmetros, pelo que pode deixar isto em branco.</td></tr>
    </table>

    Pode adicionar mais do que uma ação de script para instalar vários componentes no cluster. Depois de ter adicionado os scripts, clique na marca de verificação para começar a criar o cluster.

## <a name="use-giraph"></a>Utilizar Giraph
Utilizamos o exemplo de SimpleShortestPathsComputation para demonstrar o basic <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementação para localizar o caminho mais curto entre objetos de um gráfico. Utilize os seguintes passos para carregar os dados de exemplo e o jar de exemplo, execute uma tarefa utilizando o exemplo de SimpleShortestPathsComputation e, em seguida, ver os resultados.

1. Carregar um ficheiro de dados de exemplo para o Blob storage do Azure. Na estação de trabalho local, crie um novo ficheiro designado **tiny_graph.txt**. Esta deve conter as seguintes linhas:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Carregue o ficheiro de tiny_graph.txt para o armazenamento primário para o seu cluster HDInsight. Para obter instruções sobre como carregar dados, consulte [carregar dados para tarefas do Hadoop no HDInsight](hdinsight-upload-data.md).

    Estes dados descreve uma relação entre objetos de um gráfico direcionado, utilizando o formato [origem\_id, origem\_valor, [[dest\_id], [edge\_valor],...]]. Cada linha representa uma relação entre um **origem\_id** objeto e um ou mais **dest\_id** objetos. O **edge\_valor** (ou ponderação) pode considerar como a força ou distância da ligação entre **source_id** e **dest\_id**.

    Desenhado horizontalmente, e utilizar o valor (ou ponderação) como a distância entre objetos, os dados acima podem ter o seguinte aspeto:

    ![tiny_graph.txt desenhado como circles com linhas de variando distância entre](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Execute o exemplo de SimpleShortestPathsComputation. Utilize os seguintes cmdlets PowerShell do Azure para executar o exemplo, utilizando o ficheiro de tiny_graph.txt como entrada.

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerir recursos do HDInsight com o Gestor de Serviços do Azure está **preterido**, e foi removido a 1 de janeiro de 2017. Os passos neste documento utilizam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga os passos em [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se tiver scripts que tenham de ser modificados para utilizar os novos cmdlets que funcionam com o Azure Resource Manager, veja [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    No exemplo acima, substitua **clustername** com o nome do cluster do HDInsight que tenha Giraph instalado.
3. Veja os resultados. Depois de concluída a tarefa, os resultados serão armazenados nos dois ficheiros de saída no **wasb: / / / exemplo/out/shotestpaths** pasta. Os ficheiros são denominados **parte-m-00001** e **parte-m-00002**. Execute os seguintes passos para transferir e ver o resultado:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Esta ação irá criar o **exemplo/saída/shortestpaths** estrutura de diretórios no diretório atual na sua estação de trabalho e transferir os dois ficheiros de saída para essa localização.

    Utilize o **Cat** cmdlet para ver os conteúdos dos ficheiros:

        Cat example/output/shortestpaths/part*

    O resultado deverá ser semelhante ao seguinte:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exemplo é difícil coded começar com 1 de ID de objeto e localizar o caminho mais curto para outros objetos. Para que o resultado deve ser lido como `destination_id distance`, onde a distância é o valor (ou ponderação) das margens entre 1 de ID de objeto e o ID de destino.

    Visualizar isto, pode verificar os resultados por estiverem em deslocação os caminhos mais curto entre 1 de ID e todos os outros objetos. Tenha em atenção que o caminho mais curto entre ID 1 e ID 4 é 5. Este é a total distância entre <span style="color:orange">ID 1 e 3</span>e, em seguida, <span style="color:red">ID 3 e 4</span>.

    ![Desenho de objetos como circles com mais curto caminhos desenhados entre](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Instalar Giraph com o Azure PowerShell
Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  O exemplo demonstra como instalar o Spark com o Azure PowerShell. Tem de personalizar o script a utilizar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Instalar Giraph com o .NET SDK
Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). O exemplo demonstra como instalar o Spark utilizando o SDK .NET. Tem de personalizar o script a utilizar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Consultar também
* [Instalar Giraph em clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Criar clusters do Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de clusters do HDInsight.
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização clusters do HDInsight através da ação de Script.
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalar e utilizar o Spark nos clusters do HDInsight][hdinsight-install-spark]: exemplo de ação de Script sobre a instalação do Spark.
* [Instalar o R nos clusters do HDInsight][hdinsight-install-r]: exemplo de ação de Script sobre a instalação do R.
* [Instalar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install.md): exemplo de ação de Script sobre como instalar Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
