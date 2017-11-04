---
title: "Utilize a ação de Script para instalar Solr num cluster de Hadoop - Azure | Microsoft Docs"
description: "Saiba como personalizar o cluster do HDInsight com Solr através da ação de Script."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b1e6f338-8ac1-4b38-bbb5-2f7388b9de3b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 6efb7ea26c3cdf7748fff4b02b5810c85cc41e1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-solr-on-windows-based-hdinsight-clusters"></a>Instalar e utilizar Solr nos clusters do HDInsight baseados em Windows

Saiba como personalizar o cluster do HDInsight baseados em Windows com Solr através da ação de Script e como utilizar Solr para procurar dados.

> [!IMPORTANT]
> Os passos neste documento apenas trabalham com clusters do HDInsight baseados em Windows. HDInsight só está disponível no Windows para as versões inferiores ao HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). Para obter informações sobre a utilização Solr com um cluster baseado em Linux, consulte [instalar e utilizar Solr nos clusters do HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).


Pode instalar Solr em qualquer tipo de cluster (Hadoop, Storm, HBase, Spark) no Azure HDInsight utilizando *ação de Script*. Um script de exemplo para instalar Solr num cluster do HDInsight está disponível a partir de um blob de armazenamento do Azure só de leitura em [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

O script de amostra funciona apenas com clusters do HDInsight versão 3.1. Para obter mais informações sobre as versões de cluster do HDInsight, consulte [as versões de cluster do HDInsight](hdinsight-component-versioning.md).

O script de exemplo utilizado neste tópico cria um cluster baseado em Windows Solr com uma configuração específica. Se pretender configurar o cluster Solr com diferentes coleções, shards, esquemas, as réplicas, etc., tem de modificar o script e os binários de Solr em conformidade.

**Artigos relacionados**

* [Instalar e utilizar Solr em clusters do HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Criar clusters do Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de clusters do HDInsight.
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização clusters do HDInsight através da ação de Script.
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>O que é Solr?
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> é uma plataforma de pesquisa de empresa que lhe permite poderosa pesquisa em texto completo em dados. Enquanto Hadoop permite armazenar e gerir grandes quantidades de dados, o Apache Solr fornece as capacidades de pesquisa para obter rapidamente os dados.

## <a name="install-solr-using-portal"></a>Instalar Solr através do portal
1. Iniciar criação de um cluster utilizando o **criação personalizada** opção, conforme descrito em [clusters do Hadoop criar no HDInsight](hdinsight-provision-clusters.md).
2. No **ações de Script** página do assistente, clique em **adicionar ação de script** para fornecer os detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Utilize a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "utilize a ação de Script para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script. Por exemplo, <b>instalar Solr</b>.</td></tr>
        <tr><td>URI de script</td>
            <td>Especifique o Uniform Resource Identifier (URI) para o script que é invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de Nó</td>
            <td>Especifique os nós em que o script de personalização é executado. Pode escolher <b>todos os nós</b>, <b>apenas nós principais</b>, ou <b>apenas nós de trabalho</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário, o script. O script para instalar Solr não requer quaisquer parâmetros, pelo que pode deixar isto em branco.</td></tr>
    </table>

    Pode adicionar mais do que uma ação de script para instalar vários componentes no cluster. Depois de ter adicionado os scripts, clique na marca de verificação para começar a criar o cluster.

## <a name="use-solr"></a>Utilizar Solr
Tem de começar com indexação Solr com alguns ficheiros de dados. Em seguida, pode utilizar Solr para executar consultas de pesquisa nos dados indexados. Execute os seguintes passos para utilizar Solr no cluster do HDInsight:

1. **Utilizar o protocolo RDP (Remote Desktop Protocol) para remoto para o cluster do HDInsight com Solr instalado**. No portal do Azure, ative o ambiente de trabalho remoto para o cluster que criou com Solr instalado e, em seguida, remoto para o cluster. Para obter instruções, consulte [ligar a clusters do HDInsight através de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Solr índice através do carregamento de ficheiros de dados**. Quando o índice Solr, coloque documentos no mesmo que poderá ter de procura. Índice Solr, utilizar o RDP para remoto para o cluster, navegue para o ambiente de trabalho, abra a linha de comandos do Hadoop e navegue para **C:\apps\dist\solr-4.7.2\example\exampledocs**. Execute o seguinte comando:

        java -jar post.jar solr.xml monitor.xml

    Irá ver o seguinte resultado na consola:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    O utilitário de post.jar indexa Solr com dois documentos de exemplo, **solr.xml** e **monitor.xml**. O utilitário de post.jar e os documentos de exemplo estão disponíveis com a instalação de Solr.
3. **Utilize o dashboard de Solr para procurar nos documentos indexados**. Na sessão do RDP para o cluster do HDInsight, abra o Internet Explorer e iniciar o dashboard Solr em **http://headnodehost:8983/solr / #/**. No painel esquerdo, do **Core Seletor** pendente, selecione **collection1**e em que, clique em **consulta**. Por exemplo, para selecionar e devolver todos os documentos na Solr, forneça os valores seguintes:

   * No **q** texto, introduza  **\*:**\*. Esta ação irá devolver todos os documentos que são indexados no Solr. Se pretende procurar uma cadeia específica dentro os documentos, pode introduzir essa cadeia aqui.
   * No **wt** texto caixa, selecione o formato de saída. Predefinição é **json**. Clique em **executar a consulta**.

     ![Utilize a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "executar uma consulta num Solr dashboard")

     A saída devolve os dois docs que utilizámos para indexação Solr. O resultado é semelhante a:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }
4. **Recomendado: Cópia de segurança os dados indexados do Solr ao Blob storage do Azure associado com o cluster do HDInsight**. Como uma boa prática, deve copiar em segurança os dados indexados de nós de cluster Solr no Blob storage do Azure. Execute os seguintes passos para o fazer:

   1. Da sessão do RDP, abra o Internet Explorer e aponte para o seguinte URL:

           http://localhost:8983/solr/replication?command=backup

       Deverá ver uma resposta como esta:

           <?xml version="1.0" encoding="UTF-8"?>
           <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
             <str name="status">OK</str>
           </response>
   2. Na sessão remota, navegue para {SOLR_HOME}\{coleção} \data. Para o cluster criado através do script de exemplo, este deve ser **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. Nesta localização, deverá ver uma pasta de instantâneos criada com um nome semelhante ao  **instantâneo.* Timestamp** *.
   3. Zip a pasta de instantâneos e carregue-o no Blob storage do Azure. A linha de comandos do Hadoop, navegue para a localização da pasta de instantâneos de usando o seguinte comando:

             hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

       Este comando copia o instantâneo /example/data/no recipiente do dentro a predefinição de conta de armazenamento associada ao cluster.

## <a name="install-solr-using-aure-powershell"></a>Instalar Solr com o Azure PowerShell
Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  O exemplo demonstra como instalar o Spark com o Azure PowerShell. Tem de personalizar o script a utilizar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Instalar Solr com o .NET SDK
Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). O exemplo demonstra como instalar o Spark utilizando o SDK .NET. Tem de personalizar o script a utilizar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Consultar também
* [Instalar e utilizar Solr em clusters do HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Criar clusters do Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de clusters do HDInsight.
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização clusters do HDInsight através da ação de Script.
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalar e utilizar o Spark nos clusters do HDInsight][hdinsight-install-spark]: exemplo de ação de Script sobre a instalação do Spark.
* [Instalar o R nos clusters do HDInsight][hdinsight-install-r]: exemplo de ação de Script sobre a instalação do R.
* [Instalar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install.md): exemplo de ação de Script sobre como instalar Giraph.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
