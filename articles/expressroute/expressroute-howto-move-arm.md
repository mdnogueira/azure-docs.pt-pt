---
title: "Mover circuitos ExpressRoute do clássico para o Gestor de recursos: PowerShell: Azure | Microsoft Docs"
description: "Esta página descreve como mover um circuito clássico para o modelo de implementação do Resource Manager com o PowerShell."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: c407e01e6d881cb8adcfe55faa246468669be883
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>Mover circuitos ExpressRoute do clássico para o modelo de implementação do Resource Manager com o PowerShell

Para utilizar um circuito de ExpressRoute clássico e modelos de implementação do Resource Manager, tem de mover o circuito no modelo de implementação Resource Manager. As secções seguintes ajudam a mover o seu circuito utilizando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

* Certifique-se de que tem a versão mais recente dos módulos do Azure PowerShell (, pelo menos, versão 1.0). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).
* Certifique-se de que reviu a [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações que são fornecidas ao abrigo [mover um circuito ExpressRoute do clássico para o Resource Manager](expressroute-move.md). Certifique-se de que compreende completamente os limites e limitações.
* Certifique-se de que o circuito é totalmente operacional no modelo de implementação clássica.
* Certifique-se de que tem um grupo de recursos que foi criado no modelo de implementação Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Mover um circuito ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Passo 1: Recolher detalhes de circuito, do modelo de implementação clássica

Inicie sessão no ambiente do Azure clássico e reunir a chave do serviço.

1. Inicie sessão sua conta do Azure.

  ```powershell
  Add-AzureAccount
  ```

2. Selecione a subscrição do Azure adequada.

  ```powershell
  Select-AzureSubscription "<Enter Subscription Name here>"
  ```

3. Importe os módulos do PowerShell para o Azure e ExpressRoute.

  ```powershell
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
  ```

4. Utilize o cmdlet abaixo para obter as chaves de serviço para todos os seus circuitos do ExpressRoute. Após a obtenção de chaves, copie o **chave do serviço** do circuito que pretende mover para o modelo de implementação Resource Manager.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Passo 2: A iniciar sessão e criar um grupo de recursos

Inicie sessão no ambiente do Gestor de recursos e criar um novo grupo de recursos.

1. Inicie sessão no seu ambiente do Azure Resource Manager.

  ```powershell
  Login-AzureRmAccount
  ```

2. Selecione a subscrição do Azure adequada.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Modificar o fragmento abaixo para criar um novo grupo de recursos, se ainda não tiver um grupo de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Passo 3: Mover o circuito de ExpressRoute para o modelo de implementação Resource Manager

Agora está pronto para mover o circuito de ExpressRoute do modelo de implementação clássica para o modelo de implementação Resource Manager. Antes de continuar, reveja as informações fornecidas no [mover um circuito ExpressRoute do clássico para o modelo de implementação Resource Manager](expressroute-move.md).

Para mover o seu circuito, modificar e execute o seguinte fragmento:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

> [!NOTE]
> Após concluir a movimentação, o nome do novo que está listado no cmdlet anterior será utilizado para resolver o recurso. O circuito, essencialmente, será possível mudar o nome.
> 

## <a name="modify-circuit-access"></a>Modificar o circuito acesso

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Para ativar o acesso de circuito de ExpressRoute para ambos os modelos de implementação

Depois de mover o seu circuito de ExpressRoute clássico para o modelo de implementação Resource Manager, pode ativar o acesso a ambos os modelos de implementação. Execute os seguintes cmdlets para permitir o acesso a ambos os modelos de implementação:

1. Obter os detalhes do circuito.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Definir "Permitir operações clássicas" como TRUE.

  ```powershell
  $ckt.AllowClassicOperations = $true
  ```

3. Atualize o circuito. Após esta operação foi concluída com êxito, poderá ver o circuito no modelo de implementação clássica.

  ```powershell
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

4. Execute o seguinte cmdlet para obter os detalhes do circuito ExpressRoute. Tem de ser capaz de ver a chave do serviço listada.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Agora, pode gerir ligações para o circuito de ExpressRoute utilizando os comandos de modelo de implementação clássica para as VNets clássicas e os comandos do Gestor de recursos para VNets do Resource Manager. Os seguintes artigos ajudam a gerir ligações para o circuito de ExpressRoute:

    * [Ligar a rede virtual para o seu circuito de ExpressRoute no modelo de implementação Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Ligar a rede virtual para o seu circuito de ExpressRoute no modelo de implementação clássica](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Para desativar o acesso de circuito de ExpressRoute para o modelo de implementação clássica

Execute os seguintes cmdlets para desativar o acesso ao modelo de implementação clássica.

1. Obter os detalhes do circuito ExpressRoute.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Definir "Permitir operações clássicas" como falso.

  ```powershell
  $ckt.AllowClassicOperations = $false
  ```

3. Atualize o circuito. Após esta operação foi concluída com êxito, não poderá ver o circuito no modelo de implementação clássica.

  ```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

## <a name="next-steps"></a>Passos seguintes

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Ligar a rede virtual para o circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)