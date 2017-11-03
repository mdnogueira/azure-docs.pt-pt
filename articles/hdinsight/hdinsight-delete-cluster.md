---
title: Como eliminar um cluster do HDInsight - Azure | Microsoft Docs
description: "Informações sobre as várias formas que pode eliminar um cluster do HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6bacd40627b815c949491b70f8290e40b79e488c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminar um cluster do HDInsight utilizando o seu browser, PowerShell ou a CLI do Azure

Faturação de cluster do HDInsight é iniciado depois de um cluster é criado e para quando o cluster é eliminado. Faturação é calculada por minuto, pelo que deve sempre eliminar o cluster quando já não está a ser utilizado. Neste documento, irá aprender a eliminar um cluster utilizando o portal do Azure, Azure PowerShell e a CLI do Azure 1.0.

> [!IMPORTANT]
> Eliminar um cluster do HDInsight não elimina as contas de armazenamento do Azure ou Data Lake Store associadas ao cluster. Pode reutilizar os dados armazenados nesses serviços no futuro.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e selecione o cluster do HDInsight. Se o cluster do HDInsight não é afixado ao dashboard, pode procurá-lo por nome, utilizando o campo de pesquisa.
   
    ![pesquisa de portal](./media/hdinsight-delete-cluster/navbar.png)

2. A partir das definições do cluster, selecione o **eliminar** ícone. Quando lhe for pedido, selecione **Sim** para eliminar o cluster.
   
    ![Ícone Eliminar](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

A partir de uma linha de comandos do PowerShell, utilize o seguinte comando para eliminar o cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.

## <a name="azure-cli-10"></a>CLI do Azure 1.0

A partir de uma linha de comandos, utilize o seguinte para eliminar o cluster:

    azure hdinsight cluster delete CLUSTERNAME

Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.

> [!NOTE]
> 2.0 do CLI do Azure não suporta a eliminar os clusters do HDInsight neste momento (23 de Outubro de 2017).