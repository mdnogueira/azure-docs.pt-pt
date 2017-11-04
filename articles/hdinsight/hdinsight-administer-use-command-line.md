---
title: "Gerir clusters do Hadoop através da CLI do Azure - Azure HDInsight | Microsoft Docs"
description: Saiba como utilizar a Interface de linha de comandos do Azure para gerir clusters do Hadoop no Azure HDInsight. A CLI do Azure funciona no Windows, Mac e Linux.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 0e3a2f511d7f573c9591d1bc68fceffccc93526c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gerir clusters do Hadoop no HDInsight com a CLI do Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como utilizar o [Interface de linha de comandos do Azure](../cli-install-nodejs.md) para gerir clusters do Hadoop no Azure HDInsight. A CLI do Azure está implementada no Node.js. Pode ser utilizado em qualquer plataforma que suporte Node.js, incluindo Windows, Mac e Linux. Atualmente não suporta HDInsight [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview).

Este artigo abrange apenas a utilização da CLI do Azure com o HDInsight. Para obter um guia Geral sobre como utilizar a CLI do Azure, consulte [instalar e configurar a CLI do Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI do Azure** -Veja [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md) para obter informações de instalação e configuração.
* **Ligar ao Azure**, utilizando o seguinte comando:

    ```cli
    azure login
    ```
  
    Para mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [Ligar a uma subscrição do Azure a partir da CLI do Azure](../xplat-cli-connect.md).
* **Mude para o modo Azure Resource Manager**, utilizando o seguinte comando:
  
    ```cli
    azure config mode arm
    ```

Para obter ajuda, utilize o **-h** mudar.  Por exemplo:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Criar clusters com a CLI
Consulte [criar clusters do HDInsight utilizando a CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listar e mostrar os detalhes do cluster
Utilize os seguintes comandos para listar e mostrar os detalhes do cluster:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Vista da linha de comandos da lista de cluster][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Eliminar clusters
Utilize o seguinte comando para eliminar um cluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Também pode eliminar um cluster ao eliminar o grupo de recursos que contém o cluster. Tenha em atenção, eliminará todos os recursos no grupo, incluindo a conta do storage predefinida.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters
Para alterar o tamanho do cluster de Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Ativar/desativar o acesso HTTP para um cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Ativar/desativar acesso RDP para um cluster

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu como efetuar diferentes tarefas administrativas de cluster HDInsight. Para obter mais informações, consulte os artigos seguintes:

* [Administrar HDInsight ao utilizar o Portal do Azure][hdinsight-admin-portal]
* [Administrar HDInsight ao utilizar o Azure PowerShell][hdinsight-admin-powershell]
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]
* [Como utilizar a CLI do Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listar e Mostrar clusters"
