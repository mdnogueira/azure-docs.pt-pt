---
title: Eliminar um cluster do Azure e os respetivos recursos | Microsoft Docs
description: "Saiba como eliminar completamente uma infraestrutura de serviço de cluster ou eliminar o grupo de recursos que contém o cluster ou eliminando seletivamente os recursos."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: chackdan
ms.openlocfilehash: 7672aa12421fbe4ad86e7315d6a7a06c2ff5124d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Eliminar um cluster do Service Fabric no Azure e os recursos utiliza
Um cluster do Service Fabric é constituído por muitos outros recursos do Azure, para além do recurso de cluster em si. Por isso, para eliminar completamente um cluster do Service Fabric também tem de eliminar todos os recursos de que é constituído.
Tem duas opções: um eliminar o grupo de recursos que o cluster está a ser (que elimina o recurso de cluster e quaisquer outros recursos no grupo de recursos) ou especificamente eliminar o recurso de cluster e está associado recursos (mas não a outros recursos no grupo de recursos).

> [!NOTE]
> A eliminar o recurso de cluster **não** eliminar todos os outros recursos do cluster do Service Fabric é composto.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Eliminar o grupo de recursos completo (RG) que está a ser o cluster do Service Fabric
Esta é a forma mais fácil para se certificar de que elimina todos os recursos associados com o cluster, incluindo o grupo de recursos. Pode eliminar o grupo de recursos com o PowerShell ou através do portal do Azure. Se o grupo de recursos tiver recursos que não estão relacionadas com o cluster do Service fabric, em seguida, pode eliminar recursos específicos.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Eliminar o grupo de recursos com o Azure PowerShell
Também pode eliminar o grupo de recursos, executando os seguintes cmdlets PowerShell do Azure. Certifique-se de que o Azure PowerShell 1.0 ou posterior está instalado no seu computador. Se não fez este antes, siga os passos descritos em [como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

Abra uma janela do PowerShell e execute os seguintes cmdlets do PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Receberá uma mensagem para confirmar a eliminação se não utilizou o *-Force* opção. No confirmação o RG e todos os recursos que nele contidos são eliminados.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Eliminar um grupo de recursos no portal do Azure
1. Início de sessão para o [portal do Azure](https://portal.azure.com).
2. Navegue para o cluster do Service Fabric que pretende eliminar.
3. Clique no nome do grupo de recursos na página de essentials de cluster.
4. Aparece o **Essentials do grupo de recursos** página.
5. Clique em **Eliminar**.
6. Siga as instruções nessa página para concluir a eliminação do grupo de recursos.

![Eliminar grupo de recursos][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Eliminar o recurso de cluster e os recursos que utiliza, mas não a outros recursos no grupo de recursos
Se o grupo de recursos tem apenas os recursos que estão relacionados com o cluster do Service Fabric que pretende eliminar, em seguida, é mais fácil eliminar o grupo de recursos completo. Se pretender eliminar seletivamente os recursos um por um grupo de recursos, em seguida, siga estes passos.

Se implementou o cluster utilizando o portal ou utilizando um dos modelos de serviço Gestor de recursos de infraestrutura da galeria do modelo, em seguida, todos os recursos que o cluster utiliza são etiquetados com as seguintes duas etiquetas. Pode utilizá-los para decidir quais os recursos que pretende eliminar.

***Etiqueta n. º 1:*** chave = clusterName, valor = 'nome do cluster de'

***Etiqueta n. º 2:*** chave = resourceName, valor = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Eliminar recursos específicos no portal do Azure
1. Início de sessão para o [portal do Azure](https://portal.azure.com).
2. Navegue para o cluster do Service Fabric que pretende eliminar.
3. Aceda a **todas as definições** no painel essentials.
4. Clique em **etiquetas** em **gestão de recursos** no painel Definições.
5. Clique do **etiquetas** no painel de etiquetas para obter uma lista de todos os recursos com essa tag.
   
    ![Etiquetas de Recursos][ResourceTags]
6. Assim que tiver a lista de recursos marcados, clique em cada um dos recursos e eliminá-los.
   
    ![Recursos marcados][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Eliminar os recursos com o Azure PowerShell
Pode eliminar os recursos de um de cada executando os seguintes cmdlets PowerShell do Azure. Certifique-se de que o Azure PowerShell 1.0 ou posterior está instalado no seu computador. Se não fez este antes, siga os passos descritos em [como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

Abra uma janela do PowerShell e execute os seguintes cmdlets do PS:

```powershell
Login-AzureRmAccount
```
Para cada um dos recursos que pretende eliminar, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Para eliminar o recurso de cluster, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Passos seguintes
Leia o seguinte para também saber mais sobre a atualização de um cluster e a criação de partições de serviços:

* [Saiba mais sobre as atualizações de cluster](service-fabric-cluster-upgrade.md)
* [Saiba mais sobre a criação de partições de serviços com monitorização de estado para dimensionamento máximo](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
