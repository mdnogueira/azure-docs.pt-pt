---
title: Criar clusters do Hadoop, utilizando modelos - Azure HDInsight | Microsoft Docs
description: Saiba como criar clusters de HDInsight utilizando modelos do Resource Manager
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 38a60a64c8b2207a4795a63cfeb3527dedc8aa91
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Criar clusters do Hadoop no HDInsight com modelos do Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, saiba várias formas para criar clusters do Azure HDInsight com modelos Azure Resource Manager. Para obter mais informações, consulte [implementar uma aplicação com o modelo Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Para mais informações sobre outras funcionalidades e ferramentas de criação do cluster, clique o Seletor de separador no topo nesta página ou consulte [métodos de criação do Cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para seguir as instruções neste artigo, tem de:

* Um [subscrição do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* CLI do Azure PowerShell e/ou do Azure.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager
Um modelo do Resource Manager torna mais fácil de criar o resoruces seguintes para a sua aplicação numa operação única e coordenada:
* Clusters do HDInsight e os recursos dependentes (como a conta de armazenamento predefinido)
* Outros recursos (por exemplo, o Azure a base de dados de SQL Server para utilizar o Apache Sqoop)

No modelo, é possível definir os recursos que são necessárias para a aplicação. Também especificar parâmetros de implementação para introduzir os valores para os diferentes ambientes. O modelo é constituído por JSON e expressões que utilizar para construir valores para a sua implementação.

Pode encontrar exemplos de modelo do HDInsight em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Utilizar várias plataformas [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) com o [extensão do Gestor de recursos](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou editor de texto para guardar o modelo para um ficheiro na sua estação de trabalho. 

Para obter mais informações sobre modelos do Resource Manager, consulte os artigos seguintes:

* [Modelos do Azure Resource Manager autor](../azure-resource-manager/resource-group-authoring-templates.md)
* [Implementar uma aplicação com modelos Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Gerar modelos

O Resource Manager permite-lhe exportar um modelo do Resource Manager a partir dos recursos existentes na sua subscrição utilizando ferramentas diferentes. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário.

- Portal do Azure: consulte [exportar um modelo Azure Resource Manager a partir dos recursos existentes](../azure-resource-manager/resource-manager-export-template.md).
- O Azure PowerShell: Consulte [modelos de exportar o Azure Resource Manager com o PowerShell](../azure-resource-manager/resource-manager-export-template-powershell.md).
- CLI do Azure: Consulte [modelos de exportar o Azure Resource Manager com a CLI do Azure](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Implementar através do portal

Pode implementar um modelo do Resource Manager no portal do Azure. Para obter mais informações, consulte [implementar recursos do modelo personalizado](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Implementar utilizando o PowerShell

Pode implementar um modelo do Resource Manager com o Azure PowerShell. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [implementar privado modelo do Resource Manager com o SAS token e o Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-cli"></a>Implementar a utilizar a CLI

Pode implementar um modelo do Resource Manager utilizando a CLI do Azure. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e [implementar privado modelo do Resource Manager com o CLI do Azure e o SAS token](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Implementar utilizando a API REST
Pode implementar um modelo do Resource Manager utilizando a REST API. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a API de REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio
 Utilize o Visual Studio para criar um projeto do grupo de recursos e implementá-la no Azure através da interface de utilizador. Selecione o tipo de recursos para incluir no seu projeto. Esses recursos são adicionados automaticamente para o modelo do Resource Manager. O projeto também fornece um script do PowerShell para implementar o modelo.

Para uma introdução ao utilizar o Visual Studio com grupos de recursos, consulte [criar e implementar grupos de recursos do Azure através do Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a várias formas de criar um cluster do HDInsight. Para obter mais informações, consulte os artigos seguintes:

* Para o HDInsight mais relacionados modelos, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Para obter um exemplo de implementar recursos através da biblioteca de cliente .NET, consulte [implementar recursos com bibliotecas .NET e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter um exemplo detalhado para implementar uma aplicação, consulte [aprovisionar e implementar micro-serviços previsibilidade no Azure](../app-service/app-service-deploy-complex-application-predictably.md).
* Para obter orientações sobre a implementação da sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e teste no Microsoft Azure](../solution-dev-test-environments.md).
* Para saber mais sobre as secções do modelo Azure Resource Manager, consulte [criação de modelos](../azure-resource-manager/resource-group-authoring-templates.md).
* Para obter uma lista das funções que pode utilizar um modelo Azure Resource Manager, consulte [funções de modelo](../azure-resource-manager/resource-group-template-functions.md).