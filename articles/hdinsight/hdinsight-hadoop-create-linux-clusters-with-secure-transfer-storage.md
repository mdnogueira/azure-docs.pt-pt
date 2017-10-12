---
title: "Criar clusters Hadoop com contas de armazenamento de transferência segura no Azure HDInsight | Microsoft Docs"
description: "Saiba como criar clusters do HDInsight com contas de armazenamento do Azure com transferência segura ativada."
keywords: hadoop getting started,hadoop linux,hadoop quickstart,secure transfer,azure storage account
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/21/2017
ms.author: jgao
ms.openlocfilehash: 370b2f081930fe88527436a1a127309aed6681f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Criar clusters Hadoop com contas de armazenamento de transferência segura no Azure HDInsight

A funcionalidade [Transferência segura necessária](../storage/common/storage-require-secure-transfer.md) melhora a segurança da sua conta da Armazenamento do Azure ao impor todos os pedidos à sua conta através de uma ligação segura. Esta funcionalidade e o esquema wasbs só são suportados pelo clusters do HDInsight versão 3.6 ou mais recente. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes elementos:

* **Subscrição do Azure**: para criar uma conta de avaliação gratuita de um mês, aceda a [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Conta de Armazenamento do Azure com transferência segura ativada**. Para obter instruções, veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) e [Requer transferência segura](../storage/common/storage-require-secure-transfer.md).
* **Contentor de blobs na conta de armazenamento**. 
## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Nesta secção, vai criar um cluster do Hadoop no HDInsight com um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). O modelo também está localizado no [Gibhub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Não precisa de ter experiência no modelo do Resource Manager para seguir este tutorial. Para conhecer outros métodos de criação de clusters e compreender as propriedades utilizadas neste tutorial, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Siga as instruções para criar o cluster com as seguintes especificações: 

    - Especifique o HDInsight versão 3.6.  A versão predefinida é a 3.5. É necessária a versão 3.6 ou mais recente.
    - Especifique uma conta de armazenamento com transferência segura ativada.
    - Utilize um nome abreviado para a conta de armazenamento.
    - A conta de armazenamento e o contentor de blobs têm de ser criados previamente. 

    Para obter instruções, veja [Criar cluster](./hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 

Se utilizar a ação de script para fornecer os seus próprios ficheiros de configuração, tem de utilizar wasbs nas seguintes definições:

- fs.defaultFS (site principal)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Existem várias opções para adicionar mais contas de armazenamento com transferência segura ativada:

- Modifique o modelo do Azure Resource Manager na última secção.
- Crie um cluster através do [portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento associada.
- Utilize a ação de script para adicionar mais contas de armazenamento com transferência segura ativada a um cluster do HDInsight existente.  Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar um cluster do HDInsight e a ativar a transferência segura para as contas de armazenamento.

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para saber mais sobre como utilizar o Hive com o HDInsight, incluindo como executar consultas do Hive a partir do Visual Studio, consulte [Utilizar o Hive com o HDInsight][hdinsight-use-hive].
* Para saber mais sobre o Pig, uma linguagem utilizada para transformar dados, consulte [Utilizar o Pig com o HDInsight][hdinsight-use-pig].
* Para saber mais sobre o MapReduce, uma forma de escrever programas que processam dados no Hadoop, consulte [Utilizar o MapReduce com o HDInsight][hdinsight-use-mapreduce].
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Para saber mais sobre como o HDInsight armazena os dados ou como obter dados para o HDInsight, veja os seguintes artigos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, consulte [Carregar dados para o HDInsight][hdinsight-upload-data].

Para saber mais sobre como criar ou gerir um cluster do HDInsight, veja os seguintes artigos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, consulte [Gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).
* Se estiver familiarizado com o Linux e o Hadoop, mas pretender obter informações específicas sobre o Hadoop no HDInsight, consulte [Trabalhar com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Este artigo disponibiliza informações como:
  
  * URLs para serviços alojados no cluster, como Ambari e WebHCat
  * A localização de ficheiros do Hadoop e exemplos no sistema de ficheiros local
  * A utilização do Storage do Azure (WASB) em vez do HDFS como o arquivo de dados predefinido

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


