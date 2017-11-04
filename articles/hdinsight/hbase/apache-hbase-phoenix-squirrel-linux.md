---
title: Utilizar o Apache Phoenix e SQLLine com o HBase no HDInsight do Azure | Microsoft Docs
description: "Saiba como utilizar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar SQLLine no seu computador para ligar a um cluster de HBase no HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: 4ec2d5d82c5a6fa0b4a6a485bcacf592ba28a89c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Utilizar o Apache Phoenix com clusters de HBase baseado em Linux no HDInsight
Saiba como utilizar [Apache Phoenix](http://phoenix.apache.org/) no Azure HDInsight e como utilizar SQLLine. Para obter mais informações sobre o Phoenix, consulte [Phoenix em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para a gramática Phoenix, consulte [gramática Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Para informações de versão Phoenix sobre HDInsight, consulte [Novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Utilizar SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário da linha de comandos para executar o SQL Server.

### <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar SQLLine, tem de ter os seguintes itens:

* **Um cluster de HBase no HDInsight**. Para criar um, consulte [introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Quando ligar a um cluster HBase, terá de ligar a uma das ZooKeeper VMs. Cada cluster de HDInsight tem três ZooKeeper VMs.

**Para obter o nome de anfitrião ZooKeeper**

1. Abrir o Ambari, navegando até **https://\<nome do cluster\>. azurehdinsight.net**.
2. Para iniciar sessão, introduza o nome de utilizador HTTP (cluster) e a palavra-passe.
3. No menu à esquerda, selecione **ZooKeeper**. Três **servidor ZooKeeper** são apresentadas as instâncias.
4. Selecione uma do **servidor ZooKeeper** instâncias. No **resumo** painel, localizar o **Hostname**. Aspeto semelhante ao *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Para utilizar SQLLine**

1. Liga ao cluster através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. No SSH, utilize os seguintes comandos para executar SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Para criar uma tabela de HBase e inserir alguns dados, execute os seguintes comandos:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Para obter mais informações, consulte o [SQLLine manual](http://sqlline.sourceforge.net/#manual) e [gramática Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o Apache Phoenix no HDInsight. Para obter mais informações, consulte estes artigos:

* [Descrição geral do HBase do HDInsight][hdinsight-hbase-overview].
  O HBase é uma base de dados para os NoSQL de open source, Apache, que se incorporada no Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados.
* [Aprovisionar clusters do HBase no Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Com a integração de rede virtual, clusters de HBase podem ser implementados para a mesma rede virtual que as suas aplicações, pelo que as aplicações podem comunicar diretamente com o HBase.
* [Configurar a replicação do HBase no HDInsight](apache-hbase-replication.md). Saiba como configurar a replicação do HBase entre dois centros de dados do Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png
