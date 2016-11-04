---
title: Como configurar o encaminhamento de um circuito ExpressRoute com o Portal do Azure | Microsoft Docs
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2016
ms.author: cherylmc

---
# Criar e modificar o encaminhamento de um circuito ExpressRoute
> [!div class="op_single_selector"]
> [Portal do Azure – Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
> [PowerShell – Resource Manager](expressroute-howto-routing-arm.md)
> [PowerShell – Clássica](expressroute-howto-routing-classic.md)
> 
> 

Este artigo vai orientá-lo nos passos para criar e gerir a configuração de encaminhamento de um circuito ExpressRoute com o Portal do Azure e o modelo de implementação Resource Manager.

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Pré-requisitos da configuração
* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute tem de estar num estado aprovisionado e ativado para que seja capaz de executar os cmdlets descritos abaixo.

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviço que oferece serviços geridos de Camada 3 (normalmente, um VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si. 

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Consulte o seu fornecedor de serviços antes de configurar os peerings de BGP.
> 
> 

Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. 

## Peering privado do Azure
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito ExpressRoute. 

### Para criar um peering privado do Azure
1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:
   
   * Uma sub-rede /30 para a ligação primária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
   * Um hash MD5 se optar por utilizar um. **Isto é opcional**.
3. Selecione a linha de peering privado do Azure, como mostrado abaixo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configure o peering privado. A imagem abaixo mostra um exemplo de configuração.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Guarde a configuração depois de especificar todos os parâmetros. Depois de a configuração ter sido aceite com êxito, verá algo semelhante ao exemplo abaixo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### Para ver os detalhes do peering privado do Azure
Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### Para atualizar a configuração do peering privado do Azure
Pode selecionar a linha para peering e modificar as propriedades do peering. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### Para eliminar um peering privado do Azure
Pode remover a configuração do peering selecionando o ícone eliminar, como mostrado abaixo.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## Peering público do Azure
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito ExpressRoute. 

### Para criar um peering público do Azure
1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de prosseguir.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering público do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:
   
   * Uma sub-rede /30 para a ligação primária. 
   * Uma sub-rede /30 para a ligação secundária. 
   * Todos os endereços IP utilizados para configurar este peering têm de ser endereços IPv4 públicos válidos.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Um hash MD5 se optar por utilizar um. **Isto é opcional**.
3. Selecione a linha de peering público do Azure, como mostrado abaixo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configure o peering público. A imagem abaixo mostra um exemplo de configuração.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Guarde a configuração depois de especificar todos os parâmetros. Depois de a configuração ter sido aceite com êxito, verá algo semelhante ao exemplo abaixo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### Para ver os detalhes do peering público do Azure
Pode ver as propriedades do peering público do Azure ao selecionar o peering.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### Para atualizar a configuração do peering público do Azure
Pode selecionar a linha para peering e modificar as propriedades do peering. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### Para eliminar um peering público do Azure
Pode remover a configuração do peering selecionando o ícone eliminar, como mostrado abaixo.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## Peering da Microsoft
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering da Microsoft para um circuito ExpressRoute. 

### Para criar peering da Microsoft
1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de prosseguir.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * **Prefixos anunciados:** tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Pode enviar uma lista separada por vírgulas se pretender enviar um conjunto de prefixos. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Cliente ASN:** se estiver a anunciar prefixos que não estão registados para o número AS de peering, pode especificar o número AS no qual estão registados. **Isto é opcional**.
   * **Nome do registo de encaminhamento:** pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados. **Isto é opcional.**
   * Um hash MD5, se optar por utilizar um. **Isto é opcional.**
3. Pode selecionar o peering que pretende configurar, como mostrado abaixo. Selecione a linha de peering da Microsoft.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configure o peering da Microsoft. A imagem abaixo mostra um exemplo de configuração.
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Guarde a configuração depois de especificar todos os parâmetros. 
   
    Se o seu circuito chegar a um estado que carece de validação (como mostrado abaixo), tem de abrir um pedido de suporte para demonstrar prova de titularidade dos prefixos à nossa equipa de suporte.  
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    Pode abrir um pedido de suporte diretamente no portal, como mostrado abaixo   

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Depois de a configuração ter sido aceite com êxito, verá algo semelhante ao exemplo abaixo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### Para ver os detalhes do peering da Microsoft
Pode ver as propriedades do peering público do Azure ao selecionar o peering.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### Para atualizar a configuração do peering da Microsoft
Pode selecionar a linha para peering e modificar as propriedades do peering. 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### Para eliminar o peering da Microsoft
Pode remover a configuração do peering selecionando o ícone eliminar, como mostrado abaixo.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## Passos seguintes
Passo seguinte, [Ligar uma VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).

<!--HONumber=Sep16_HO3-->


