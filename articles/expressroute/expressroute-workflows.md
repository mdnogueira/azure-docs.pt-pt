---
title: "Fluxos de trabalho para a configuração de um circuito do ExpressRoute | Microsoft Docs"
description: "Esta página explica como os fluxos de trabalho para configurar o circuito do ExpressRoute e peerings"
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: cba1b2cfee379e7d2b079bcb3089981ef1044d66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para o aprovisionamento e estados dos circuitos
Esta página orienta-o serviço de encaminhamento de fluxos de trabalho de configuração a um nível elevado e aprovisionamento.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A figura seguinte e correspondentes passos mostram as tarefas, terá de seguir para ter um circuito ExpressRoute aprovisionado ponto-a-ponto. 

1. Utilize o PowerShell para configurar um circuito ExpressRoute. Siga as instruções no [circuitos ExpressRoute criar](expressroute-howto-circuit-classic.md) artigo para obter mais detalhes.
2. Ordem de conectividade do fornecedor de serviço. Este processo varia. Contacte o fornecedor de conectividade para obter mais detalhes sobre como ordenar conectividade.
3. Certifique-se de que o circuito ter sido aprovisionado com êxito, verificando o circuito de ExpressRoute estado através do PowerShell de aprovisionamento. 
4. Configure domínios de encaminhamento. Se o seu fornecedor de conectividade gere camada 3 para si, irá configurar encaminhamento para o seu circuito. Se o seu fornecedor de conectividade oferecer apenas os serviços de camada 2, deve configurar o encaminhamento por diretrizes descritas a [requisitos de encaminhamento](expressroute-routing.md) e [configuração de encaminhamento](expressroute-howto-routing-classic.md) páginas.
   
   * Ative o peering privado do Azure - tem de ativar este peering ligar a VMs / implementados dentro de redes virtuais de serviços em nuvem.
   * Ative o peering público do Azure - tem de ativar o peering público do Azure se pretender ligar aos serviços do Azure alojados em endereços IP públicos. Este é um requisito para aceder aos recursos do Azure, se tiver escolhido para ativar o encaminhamento predefinido para o peering privado do Azure.
   * Ative o peering da Microsoft - tem de ativar este para o acesso do Office 365 e Dynamics 365. 
     
     > [!IMPORTANT]
     > Tem de garantir que utilizar um proxy separado / periferia para se ligar à Microsoft que pode utilizar para a Internet. Utilizar o mesmo limite para o ExpressRoute e a Internet causam assimétrica encaminhamento e provocar falhas de conectividade da rede.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Ligar redes virtuais para circuitos do ExpressRoute - pode ligar redes virtuais para o circuito do ExpressRoute. Siga as instruções [ligar VNets](expressroute-howto-linkvnet-arm.md) para o seu circuito. Nestas VNets podem ser na mesma subscrição do Azure como o circuito do ExpressRoute, ou pode ser numa subscrição diferente.

## <a name="expressroute-circuit-provisioning-states"></a>Estados de aprovisionamento de circuito de ExpressRoute
Cada circuito do ExpressRoute tem dois Estados:

* Estado de aprovisionamento do fornecedor de serviço
* Estado

Estado representa o estado de aprovisionamento da Microsoft. Esta propriedade for definida como ativado quando cria um circuito do Expressroute

O estado de aprovisionamento de fornecedor de conectividade representa o estado do lado do fornecedor de conectividade. Este pode ser *NotProvisioned*, *aprovisionamento*, ou *aprovisionado*. O circuito ExpressRoute tem de estar no Estado aprovisionado para poder utilizá-la.

### <a name="possible-states-of-an-expressroute-circuit"></a>Estados possíveis de um circuito ExpressRoute
Esta secção lista os os possíveis Estados de um circuito ExpressRoute.

**No momento de criação**

Verá o circuito de ExpressRoute no seguinte estado assim que executa o cmdlet do PowerShell para criar o circuito ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade está no processo de aprovisionamento do circuito**

Verá o circuito de ExpressRoute no seguinte estado assim que passa a chave do serviço para o fornecedor de conectividade e de iniciou o processo de aprovisionamento.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quando o fornecedor de conectividade concluiu o processo de aprovisionamento**

Verá o circuito de ExpressRoute no seguinte estado assim que o fornecedor de conectividade concluiu o processo de aprovisionamento.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Aprovisionado e ativado é que o estado apenas o circuito pode estar em para poder utilizá-la. Se estiver a utilizar um fornecedor de camada 2, pode configurar o encaminhamento para o seu circuito, apenas quando é neste estado.

**Quando o fornecedor de conectividade é desaprovisionamento o circuito**

Se tiver solicitado o fornecedor de serviços para retirar o aprovisionamento do circuito ExpressRoute, verá o circuito definida para o estado do seguinte depois do fornecedor de serviços concluiu o processo de desaprovisionamento.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Pode escolher a reativá-la se necessário, ou executar cmdlets do PowerShell para eliminar o circuito.  

> [!IMPORTANT]
> Se executar o cmdlet do PowerShell para eliminar o circuito quando aprovisionar o o ServiceProviderProvisioningState ou aprovisionado a operação falhará. . Funciona com o seu fornecedor de conectividade para retirar o aprovisionamento do circuito ExpressRoute primeiro e, em seguida, eliminar o circuito. A Microsoft irá continuar bem ao faturar o circuito, até executar o cmdlet PowerShell para eliminar o circuito.
> 
> 

## <a name="routing-session-configuration-state"></a>Encaminhamento estado da configuração de sessão
O BGP, o estado de aprovisionamento permite-lhe saber se tiver sido ativada a sessão de BGP no limite Microsoft. O estado tem de estar ativado para que possa ser capazes de utilizar o peering.

É importante verificar o estado da sessão BGP especialmente para peering da Microsoft. Para além de BGP de estado de aprovisionamento, há outro Estado chamado *anunciados Estado prefixos públicos*. O estado de prefixos públicos anunciado tem de constar *configurado* Estado, tanto para a sessão de BGP para cópia de segurança e para o encaminhamento para trabalhar ponto-a-ponto. 

Se o estado de prefixo pública anunciado está definido como um *validação necessária* de estado, a sessão de BGP não estiver ativada, como os prefixos anunciados não corresponde ao número AS em qualquer um dos registos de encaminhamento. 

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciado é de *validação manual* Estado, tem de abrir um pedido de suporte com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer provas de que é proprietário os endereços IP anunciados juntamente com o número de sistema autónomo associado.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Configurar a ligação do ExpressRoute.
  
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

