---
title: "Gerar recomendações utilizando Mahout HDInsight a partir do PowerShell - Azure | Microsoft Docs"
description: "Saiba como utilizar a Apache Mahout biblioteca de machine learning para gerar recomendações de filmes com o HDInsight (Hadoop) a partir de um script do PowerShell em execução no cliente."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: larryfr
ms.openlocfilehash: 934de9ca2df48b29ef7a56d5729d59d77875ea7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Gerar recomendações de filmes ao utilizar o Apache Mahout com o Hadoop no HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como utilizar o [Apache Mahout](http://mahout.apache.org) biblioteca de aprendizagem máquina com o Azure HDInsight para gerar recomendações de filmes. O exemplo neste documento utiliza o Azure PowerShell para executar tarefas de Mahout.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux. Para obter informações sobre como criar um, consulte [começar a utilizar o Hadoop baseado em Linux no HDInsight][getstarted].

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Gerar recomendações utilizando o Azure PowerShell

> [!WARNING]
> A tarefa nesta secção funciona com o Azure PowerShell. Muitas das classes fornecidas com o Mahout não funcionam atualmente com o Azure PowerShell. Para obter uma lista de classes que não funcionam com o Azure PowerShell, consulte o [resolução de problemas](#troubleshooting) secção.
>
> Para obter um exemplo de utilização do SSH para ligar ao HDInsight e exemplos de Mahout executados diretamente no cluster, consulte [gerar recomendações de filmes com o Mahout e HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

Uma das funções que é fornecida pela Mahout é um motor de recomendação. Este motor aceita dados no formato de `userID`, `itemId`, e `prefValue` (de preferência de utilizadores para o item). Mahout utiliza os dados para determinar os utilizadores com as preferências de item semelhante, que podem ser utilizados para efetuar recomendações.

O exemplo seguinte é uma passagem simplificada como funciona o processo de recomendação:

* **ocorrência conjunta**: Joe, Alice e Bernardo todos os gostou *estrela Wars*, *o Empire Strikes novamente*, e *retorno do Jedi*. Mahout determina que os utilizadores que como qualquer uma destes filmes também como as outras duas.

* **ocorrência conjunta**: João e Alice também gostou *o Phantom Menace*, *ataque dos Clones*, e *Revenge do Sith*. Mahout determina que os utilizadores que gostou também os três filmes anteriores, como estes filmes.

* **Recomendação de semelhança**: Joe porque gostou os três primeiros filmes, Mahout observa filmes que outras pessoas com as preferências semelhantes que gostou, mas Joe não foi observada (gostou/classificados). Neste caso, recomenda Mahout *o Phantom Menace*, *ataque dos Clones*, e *Revenge do Sith*.

### <a name="understanding-the-data"></a>Compreender os dados

[GroupLens Research] [ movielens] fornece dados de classificação de filmes num formato que é compatível com o Mahout. Estes dados ficarem disponíveis no armazenamento de predefinido para o cluster em `/HdiSamples//HdiSamples/MahoutMovieData`.

Existem dois ficheiros, `moviedb.txt` (informações sobre os filmes) e `user-ratings.txt`. O `user-ratings.txt` ficheiro é utilizado durante a análise. O `moviedb.txt` ficheiro é utilizado para fornecer intuitivo texto quando se apresenta os resultados da análise.

Os dados contidos no utilizador ratings.txt tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que informa-nos como altamente cada utilizador classificados um filme. Eis um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Executar a tarefa

Utilize o seguinte script do Windows PowerShell para executar uma tarefa que utiliza o motor de recomendação Mahout com os dados de filmes:

> [!NOTE]
> Este ficheiro pede-lhe informações que são utilizadas para ligar ao cluster do HDInsight e executar tarefas. Pode demorar alguns minutos para que as tarefas concluídas e transfira o ficheiro output.txt.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Tarefas de mahout não remover os dados temporários que são criados durante o processamento da tarefa. O `--tempDir` parâmetro for especificado na tarefa de exemplo para isolar os ficheiros temporários para um diretório específico.

A tarefa de Mahout não devolve o resultado para STDOUT. Em vez disso, armazena-lo no diretório de saída especificado como **parte-r-00000**. O script transfere este ficheiro para **output.txt** no diretório atual na estação de trabalho.

O texto seguinte é um exemplo do conteúdo deste ficheiro:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

A primeira coluna é a `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

O script também transfere o `moviedb.txt` e `user-ratings.txt` ficheiros, que são necessárias para formatar a saída para ser mais legível.

### <a name="view-the-output"></a>Ver o resultado

Embora o resultado gerado pode ser OK para utilização numa aplicação, não é amigável de utilizador. O `moviedb.txt` do servidor pode ser utilizado para resolver o `movieId` para um nome de filmes. Utilize o seguinte script do PowerShell para apresentar as recomendações com nomes de filmes:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Utilize o seguinte comando para apresentar as recomendações no formato amigável de utilizador: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

O resultado é semelhante ao seguinte texto:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="cannot-overwrite-files"></a>Não é possível substituir ficheiros

Efetue as tarefas de mahout não limpar os ficheiros temporários criados durante o processamento. Além disso, as tarefas não substituir o ficheiro de saída existente.

Para evitar erros ao executar tarefas de Mahout, elimine ficheiros temporários e de saída entre é executado. Para remover os ficheiros criados pelos scripts anteriores neste documento, utilize o seguinte script do PowerShell:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Classes que não funcionam com o Azure PowerShell

Tarefas de mahout que utilizem as classes seguintes devolvem vários mensagens de erro quando utilizado a partir do Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para executar tarefas que utilizam estas classes, estabelecer ligação ao cluster do HDInsight através de SSH e execute as tarefas na linha de comandos. Para obter um exemplo de utilização do SSH para executar tarefas de Mahout, consulte [gerar recomendações de filmes com o Mahout e HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como utilizar o Mahout, detete outras formas de trabalhar com dados no HDInsight:

* [O Hive com o HDInsight](hdinsight-use-hive.md)
* [O PIg com o HDInsight](hdinsight-use-pig.md)
* [MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
