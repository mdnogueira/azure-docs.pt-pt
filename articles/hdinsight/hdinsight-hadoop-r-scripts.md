---
title: Utilize R no HDInsight para personalizar clusters - Azure | Microsoft Docs
description: "Saiba como instalar o R através da ação de Script e utilizar o R nos clusters do HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 666b51970bf04634708cbf65b8bca0c05412934b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar R em clusters do HDInsight Hadoop

Saiba como personalizar Windows baseado em cluster do HDInsight com R através da ação de Script, clusters e de como utilizar o R no HDInsight. O [HDInsight oferta](https://azure.microsoft.com/pricing/details/hdinsight/) inclui o R Server como parte do cluster do HDInsight. Isto permite que os scripts de R utilizar o MapReduce e Spark para executar cálculos distribuídos. Para obter mais informações, consulte [Começar a utilizar o Servidor R no HDInsight](r-server/r-server-get-started.md). Para obter informações sobre como utilizar o R com um cluster baseado em Linux, consulte [instalar e utilizar R na clusters do HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).

Pode instalar o R qualquer tipo de cluster (Hadoop, Storm, HBase, Spark) no Azure HDInsight utilizando *ação de Script*. Um script de exemplo para instalar o R num cluster do HDInsight está disponível a partir de um blob de armazenamento do Azure só de leitura em [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Artigos relacionados**

* [Instalar e utilizar o R em clusters do HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Criar clusters do Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informações gerais sobre a criação de clusters do HDInsight
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização clusters do HDInsight através da ação de Script
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>O que é R?
O <a href="http://www.r-project.org/" target="_blank">R projeto para o cálculo estatístico</a> é um ambiente para o cálculo estatístico e abra idioma da origem. R fornece centenas de compilação no funções estatísticas e a suas próprias linguagem de programação que combina aspetos funciona e orientado para objetos de programação. Também fornece extensas capacidades de gráficas. R é o ambiente de programação preferencial para profissionais mais statisticians e cientistas numa grande variedade de campos.

R é compatível com o armazenamento de Blobs do Azure (WASB), para que os dados armazenados existe podem ser processados utilizando R no HDInsight.  

## <a name="install-r"></a>Instalar o R
A [script de exemplo](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) para instalar o R no HDInsight a um cluster está disponível a partir do blob no Storage do Azure só de leitura. Esta secção fornece instruções sobre como utilizar o script de exemplo ao criar o cluster utilizando o Portal do Azure.

> [!NOTE]
> O script de exemplo foi introduzido com clusters do HDInsight versão 3.1. Para mais informações sobre as versões de cluster do HDInsight, consulte [as versões de cluster do HDInsight](hdinsight-component-versioning.md).
>
>

1. Quando criar um cluster do HDInsight a partir do Portal, clique em **configuração opcional**e, em seguida, clique em **ações de Script**.
2. No **ações de Script** página, introduza os seguintes valores:

    ![Utilize a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "utilize a ação de Script para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script, por exemplo, <b>instalar R</b>.</td></tr>
        <tr><td>URI de script</td>
            <td>Especifique o URI para o script que é invocado para personalizar o cluster, por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Tipo de Nó</td>
            <td>Especifique os nós em que o script de personalização é executado. Pode escolher <b>todos os nós</b>, <b>apenas nós principais</b>, ou <b>nós de trabalho</b> apenas.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário, o script. No entanto, o script para instalar o R não requer quaisquer parâmetros, pelo que pode deixar isto em branco.</td></tr>
    </table>

    Pode adicionar mais do que uma ação de script para instalar vários componentes no cluster. Depois de ter adicionado os scripts, clique na marca de verificação para iniciar a crating o cluster.

Também pode utilizar o script para instalar o R no HDInsight ao utilizar o Azure PowerShell ou o SDK .NET do HDInsight. Neste artigo, são fornecidas instruções para estes procedimentos.

## <a name="run-r-scripts"></a>Executar scripts de R
Esta secção descreve como executar um script do R no cluster de Hadoop com o HDInsight.

1. **Estabelecer uma ligação de ambiente de trabalho remoto para o cluster**: do Portal, ativar o ambiente de trabalho remoto para o cluster que criou com o R instalado e, em seguida, ligar ao cluster. Para obter instruções, consulte [ligar a clusters do HDInsight através de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Abra a consola de R**: instalação do R o coloca uma ligação para a consola de R no ambiente de trabalho do nó principal. Clique na mesma para abrir a consola do R.
3. **Execute o script do R**: script do R pode ser executado diretamente a partir da consola do R colando-lo, selecionando-e premindo ENTER. Eis um script de exemplo simples que gera os números 1 e 100 e, em seguida, multiplica-los por 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

As primeiras duas linhas chamar as bibliotecas de RHadoop que são instaladas com R. A linha final imprime os resultados para a consola. A saída deve ter o seguinte aspeto:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Instalar o R com o Azure PowerShell
Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  O exemplo demonstra como instalar o Spark com o Azure PowerShell. Tem de personalizar o script a utilizar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Instalar o R com o .NET SDK
Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). O exemplo demonstra como instalar o Spark utilizando o SDK .NET. Tem de personalizar o script a utilizar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>Consultar também
* [Instalar e utilizar o R em clusters do HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Criar clusters do Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informações gerais sobre a criação de clusters do HDInsight
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização clusters do HDInsight através da ação de Script
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md)
* [Instalar e utilizar o Spark nos clusters do HDInsight][hdinsight-install-spark]: exemplo de ação de Script sobre a instalação do Spark
* [Instalar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install.md): exemplo de ação de Script sobre como instalar Giraph
* [Instalar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install-linux.md): exemplo de ação de Script sobre como instalar Solr.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]:spark/apache-spark-jupyter-spark-sql.md
