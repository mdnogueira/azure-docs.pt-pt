---
title: "Migrar redes virtuais associado do ExpressRoute do clássico para o Gestor de recursos: Azure: PowerShell | Microsoft Docs"
description: "Esta página descreve como migrar para o Gestor de recursos de redes virtuais associadas depois de mover o seu circuito."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 336f68308f7d4b4dd3c7476a4fabd793939e9e85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrar redes virtuais associado do ExpressRoute do clássico para o Resource Manager

Este artigo explica como migrar redes virtuais do Azure ExpressRoute associados do modelo de implementação clássica para o modelo de implementação Azure Resource Manager depois de mover o seu circuito do ExpressRoute. 


## <a name="before-you-begin"></a>Antes de começar
* Certifique-se de que tem a versão mais recente dos módulos do PowerShell do Azure. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).
* Certifique-se de que reviu a [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações que são fornecidas ao abrigo [mover um circuito ExpressRoute do clássico para o Resource Manager](expressroute-move.md). Certifique-se de que compreende completamente os limites e limitações.
* Certifique-se de que o circuito é totalmente operacional no modelo de implementação clássica.
* Certifique-se de que tem um grupo de recursos que foi criado no modelo de implementação Resource Manager.
* Reveja a seguinte documentação de migração de recursos:

    * [Plataforma suportada a migração de recursos IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Perguntas mais frequentes: Migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Reveja e mitigações de erros mais comuns de migração](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Cenários suportados e não suportados

* Um circuito ExpressRoute pode ser afastado do clássico para o ambiente do Gestor de recursos sem qualquer período de inatividade. Pode mover qualquer circuito ExpressRoute do clássico para o ambiente do Gestor de recursos sem período de indisponibilidade. Siga as instruções em [mover circuitos ExpressRoute do clássico para o modelo de implementação do Resource Manager com o PowerShell](expressroute-howto-move-arm.md). Este é um pré-requisito para mover recursos ligados à rede virtual.
* Implementações associadas dentro da rede virtual que estão anexadas a um circuito de ExpressRoute na mesma subscrição, gateways e redes virtuais podem ser migradas para o ambiente do Gestor de recursos sem qualquer período de inatividade. Pode seguir os passos descritos mais tarde para migrar os recursos, tais como redes virtuais, gateways e máquinas virtuais implementadas dentro da rede virtual. Tem de se certificar de que as redes virtuais estão configuradas corretamente antes de são migrados. 
* Implementações associadas dentro da rede virtual que não estão na mesma subscrição que o circuito do ExpressRoute, gateways e redes virtuais requerem algum período de indisponibilidade para concluir a migração. A última seção do documento descreve os passos ser seguidas para migrar os recursos.
* Não é possível migrar uma rede virtual com o Gateway do ExpressRoute e o Gateway de VPN.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Mover um circuito ExpressRoute do clássico para o Resource Manager
Tem de mover um circuito de ExpressRoute clássico para o ambiente do Gestor de recursos antes de tentar migrar os recursos que estão anexados ao circuito ExpressRoute. Para realizar esta tarefa, consulte os artigos seguintes:

* Reveja as informações que são fornecidas ao abrigo [mover um circuito ExpressRoute do clássico para o Resource Manager](expressroute-move.md).
* [Mover um circuito do clássico para o Resource Manager com o Azure PowerShell](expressroute-howto-move-arm.md).
* Utilize o portal de gestão de serviço do Azure. Pode seguir o fluxo de trabalho [criar um circuito de ExpressRoute novo](expressroute-howto-circuit-portal-resource-manager.md) e selecione a opção de importação. 

Esta operação não envolve o período de indisponibilidade. Pode continuar a transferir dados entre os locais e a Microsoft, enquanto a migração está em curso.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrar implementações associadas, gateways e redes virtuais

Os passos a que seguir para migrar dependem se os recursos se encontram na mesma subscrição, subscrições diferentes ou ambos.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrar implementações associadas na mesma subscrição, como o circuito do ExpressRoute, gateways e redes virtuais
Esta secção descreve os passos para ser seguidas para migrar uma rede virtual, o gateway e implementações associadas na mesma subscrição, como o circuito ExpressRoute. Sem períodos de indisponibilidade estão associado esta migração. Pode continuar a utilizar todos os recursos através do processo de migração. O plane de gestão está bloqueado durante a migração está em curso. 

1. Certifique-se de que o circuito do ExpressRoute foi transferido da clássica para o ambiente do Gestor de recursos.
2. Certifique-se de que a rede virtual foi preparada adequadamente para a migração.
3. Registe a sua subscrição para a migração de recursos. Para registar a sua subscrição para a migração de recursos, utilize o seguinte fragmento de PowerShell:

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Validar, preparar e migrar. Para mover a rede virtual, utilize o seguinte fragmento de PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
  ```

  Também pode abortar migração executando o seguinte cmdlet do PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>Passos seguintes
* [Plataforma suportada a migração de recursos IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Perguntas mais frequentes: Migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Reveja e mitigações de erros mais comuns de migração](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
