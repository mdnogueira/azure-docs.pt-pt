---
title: "Utilize a ação de Script para instalar o Solr no HDInsight baseado em Linux - Azure | Microsoft Docs"
description: "Saiba como instalar Solr nos clusters do Hadoop do HDInsight baseado em Linux utilizando as ações de Script."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: larryfr
ms.openlocfilehash: c7a911474d6fb90f45565c90a72bfd407898ceba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar Solr em clusters do HDInsight Hadoop

Saiba como instalar Solr no Azure HDInsight ao utilizar a ação de Script. Solr é uma plataforma de pesquisa de elevado desempenho e fornece capacidades de pesquisa de nível empresarial nos dados geridos pelo Hadoop.

> [!IMPORTANT]
    > Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

> [!IMPORTANT]
> O script de exemplo utilizado neste documento instala Solr 4.9 com uma configuração específica. Se pretender configurar o cluster Solr com diferentes coleções, shards, esquemas, as réplicas, etc., tem de modificar o script e Solr binários.

## <a name="whatis"></a>O que é Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) é uma plataforma de pesquisa de empresa que lhe permite poderosa pesquisa em texto completo em dados. Enquanto Hadoop permite armazenar e gerir grandes quantidades de dados, o Apache Solr fornece as capacidades de pesquisa para obter rapidamente os dados.

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportados pela Microsoft.
>
> Componentes personalizados, tais como Solr, recebem suporte comercialmente razoável para ajudar a resolver o problema. Suporte da Microsoft pode não conseguir resolver problemas com componentes personalizados. Poderá ter de interagir com as Comunidades de código aberto para obter assistência. Por exemplo, existem vários sites de Comunidade que podem ser utilizadas, como: [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos do Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>O que faz o script

Este script faz as seguintes alterações ao cluster do HDInsight:

* Instala Solr 4.9 em`/usr/hdp/current/solr`
* Cria um utilizador, **solrusr**, que é utilizada para executar o serviço de Solr
* Conjuntos de **solruser** como proprietário do`/usr/hdp/current/solr`
* Adiciona um [Upstart](http://upstart.ubuntu.com/) configuração inicia Solr automaticamente.

## <a name="install"></a>Instalar Solr utilizando ações de Script

Um script de exemplo para instalar Solr num cluster do HDInsight está disponível na seguinte localização:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Para criar um cluster com Solr instalado, utilize os passos a [criar clusters do HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md) documento. Durante o processo de criação, utilize os seguintes passos para instalar Solr:

1. Do __resumo do Cluster__ secção, settings__ select__Advanced, em seguida, __ações de Script__. Utilize as seguintes informações para preencher o formulário:

   * **NOME**: introduza um nome amigável para a ação de script.
   * **URI de SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: esta opção de verificação
   * **TRABALHO**: esta opção de verificação
   * **ZOOKEEPER**: confirme esta opção para instalar no nó Zookeeper
   * **Os parâmetros**: deixe este campo em branco

2. Na parte inferior do **ações de Script** secção, utilize o **selecione** botão para guardar a configuração. Por último, utilize o **seguinte** botão para regressar ao __resumo do Cluster__

3. Do __resumo do Cluster__ página, selecione __criar__ para criar o cluster.

## <a name="usesolr"></a>Como utilizar o Solr no HDInsight

> [!IMPORTANT]
> Os passos desta secção demonstram funcionalidade Solr básica. Para obter mais informações sobre como utilizar Solr, consulte o [Apache Solr site](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Dados do índice

Utilize os seguintes passos para adicionar dados de exemplo para Solr e, em seguida, a consultar:

1. Ligar ao cluster do HDInsight com o SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Passos mais à frente neste documento utilizam um túnel SSL para estabelecer ligação com a IU da web de Solr. Para utilizar estes passos, tem de estabelecer um túnel SSL e, em seguida, configure os seu browser para utilizá-lo.
     >
     > Para obter mais informações, consulte o [utilize túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) documento.

2. Utilize os seguintes comandos para que os dados de exemplo de índice Solr:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    O seguinte resultado é devolvido para a consola:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    O `post.jar` utilitário adiciona o **solr.xml** e **monitor.xml** documentos para o índice.
  
3. Utilize o seguinte comando para consultar a API de REST Solr:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Este comando procura **collection1** para documentos correspondente  **\*:\***  (codificado como \*% 3A\* na cadeia de consulta). O documento JSON seguinte é um exemplo de resposta:

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

### <a name="using-the-solr-dashboard"></a>Utilizar o dashboard Solr

O dashboard de Solr é uma IU da web do que permite-lhe trabalhar com Solr através do web browser. O dashboard Solr não está exposto diretamente na Internet do seu cluster HDInsight. Pode utilizar um túnel SSH para aceder ao mesmo. Para obter mais informações sobre como utilizar um túnel SSH, consulte o [utilize túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) documento.

Depois de estabelecer um túnel SSH, utilize os seguintes passos para utilizar o dashboard Solr:

1. Determine o nome de anfitrião para o headnode primário:

   1. Utilize o SSH para ligar ao nó principal do cluster. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Para obter mais informações sobre como utilizar o SSH, consulte o [utilizar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Utilize o seguinte comando para obter o nome de anfitrião totalmente qualificado:

        ```bash
        hostname -f
        ```

        Este comando devolve um valor semelhante ao seguinte nome de anfitrião:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Guarde o valor devolvido, porque é utilizado mais tarde.

2. No seu browser, ligue ao **http://HOSTNAME:8983/solr / #/**, onde **HOSTNAME** é o nome determinado nos passos anteriores.

    O pedido é encaminhado através do túnel SSH para a web Solr IU no seu cluster. A página apresentado de forma semelhante a imagem seguinte:

    ![Imagem do Solr dashboard](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. No painel esquerdo, utilize o **Core Seletor** pendente para selecionar **collection1**. Várias entradas-los aparecer abaixo **collection1**.

4. Partir das entradas abaixo **collection1**, selecione **consulta**. Utilize os seguintes valores para preencher a página de pesquisa:

   * No **q** texto, introduza  **\*:**\*. Esta consulta devolve todos os documentos que são indexados Solr. Se pretende procurar uma cadeia específica dentro os documentos, pode introduzir essa cadeia aqui.
   * No **wt** texto caixa, selecione o formato de saída. Predefinição é **json**.

     Por fim, selecione o **executar consulta** na parte inferior do pate a pesquisa.

     ![Utilize a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     A saída devolve dois documentos que adicionou anteriormente para o índice. O resultado será semelhante para o documento JSON seguinte:

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

### <a name="starting-and-stopping-solr"></a>Iniciar e parar Solr

Utilize os seguintes comandos para parar e iniciar Solr manualmente:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Cópia de segurança de dados indexados

Utilize os seguintes passos para criar cópias de segurança Solr dados para o armazenamento de predefinido para o cluster:

1. Ligar ao cluster através de SSH e, em seguida, utilize o seguinte comando para obter o nome de anfitrião do nó principal:

    ```bash
    hostname -f
    ```

2. Utilize o seguinte comando para criar um instantâneo dos dados indexados. Substitua **HOSTNAME** com o nome devolvido pelo comando anterior:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    A resposta é semelhante ao seguinte XML:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Altere os diretórios para `/usr/hdp/current/solr/example/solr`. Não há um subdiretório para cada coleção. Diretório de cada coleção contém um `data` diretório que contém o instantâneo para a coleção.

4. Para criar um arquivo comprimido da pasta de instantâneos, utilize o seguinte comando:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Substitua o `snapshot.20150806185338855` valores com o nome do instantâneo para a coleção.

    Este comando cria um arquivo denominado **snapshot.20150806185338855.tgz**, que contém o conteúdo a **snapshot.20150806185338855** diretório.

5. Em seguida, pode armazenar o arquivo para o armazenamento de principal do cluster utilizando o seguinte comando:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Para obter mais informações sobre como trabalhar com Solr cópia de segurança e restauros, consulte [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Passos seguintes

* [Instalar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilize personalização de cluster para instalar Giraph em clusters do HDInsight Hadoop. Giraph permite-lhe efetuar o processamento através da utilização do Hadoop e pode ser utilizado com o Azure HDInsight.

* [Instalar a Hue nos clusters do HDInsight](hdinsight-hadoop-hue-linux.md). Utilize a personalização de cluster para instalar a Hue em clusters do HDInsight Hadoop. Hue é um conjunto de aplicações Web utilizado para interagir com um cluster do Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
