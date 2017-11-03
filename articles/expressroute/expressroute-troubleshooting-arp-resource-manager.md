---
title: "Obter ARP tabelas: o Gestor de recursos: Resolução de problemas de ExpressRoute do Azure | Microsoft Docs"
description: "Esta página fornece instruções sobre como começar a ARP tabelas para um circuito ExpressRoute"
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Obter ARP tabelas no modelo de implementação Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Clássica](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo explica os passos para obter as tabelas de ARP para o seu circuito do ExpressRoute. 

> [!IMPORTANT]
> Este documento destina-se para o ajudar a diagnosticar e corrigir problemas simples. Não se destina a ser uma substituição para o suporte da Microsoft. Tem de abrir um pedido de suporte com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se não for possível resolver o problema utilizando a orientação descrita abaixo.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protocolo ARP (Resolution Protocol) e ARP tabelas de endereços
Protocolo ARP (Address Resolution Protocol) é um protocolo de camada 2 definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é utilizado para mapear o endereço Ethernet (endereço MAC) com um endereço ip.

A tabela de ARP fornece um mapeamento de endereço ipv4 e endereço MAC para um determinado peering. A tabela de ARP para um circuito de ExpressRoute peering fornece as seguintes informações para cada interface (primário e secundário)

1. Mapeamento de endereço ip de interface de router no local para o endereço MAC
2. Mapeamento de endereço ip de interface de router do ExpressRoute para o endereço MAC
3. Idade do mapeamento

Tabelas de ARP podem ajudar a validar a configuração de camada 2 e resolução de problemas básicos camada 2 problemas de conectividade. 

Tabela de ARP de exemplo: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


A seguinte secção fornece informações sobre como pode ver as tabelas de ARP vistas pelos routers de limite do ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para tabelas de ARP de aprendizagem
Certifique-se de que tem o seguinte antes de progresso adicional

* Um circuito de ExpressRoute válido configurado com, pelo menos, um peering. O circuito deve estar totalmente configurado pelo fornecedor de conectividade. Utilizador (ou o fornecedor de conectividade) tem de ter configurado pelo menos um dos peerings (Azure privado, Azure público e Microsoft) neste circuito.
* Intervalos de endereços IP utilizados para configurar os peerings (Azure privado, Azure público e Microsoft). Reveja os exemplos de atribuição de endereço ip no [página de requisitos de encaminhamento ExpressRoute](expressroute-routing.md) para obter uma compreensão dos como endereços ip são mapeados para interfaces no seu lado e do lado do ExpressRoute. Pode obter informações sobre a configuração do peering revendo o [página de configuração de peering de ExpressRoute](expressroute-howto-routing-arm.md).
* Informações da sua equipa de rede / utilizar o fornecedor de conectividade os endereços MAC das interfaces com estes endereços IP.
* Tem de ter o módulo do PowerShell mais recente do Azure (versão 1.50 ou mais recente).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obter o ARP tabelas para o seu circuito do ExpressRoute
Esta secção fornece instruções sobre como pode ver as tabelas de ARP por peering através do PowerShell. Utilizador ou o fornecedor de conectividade tem de ter configurado o peering antes de evoluir adicional. Cada circuito tem dois caminhos (principais e secundários). Pode verificar a tabela de ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas de ARP para peering privado do Azure
O cmdlet seguinte fornece a ARP tabelas para peering privado do Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Saída de exemplo é mostrada abaixo para um dos caminhos de

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP as tabelas do peering público do Azure
O cmdlet seguinte fornece a ARP tabelas para o peering público do Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Saída de exemplo é mostrada abaixo para um dos caminhos de

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas de ARP para peering da Microsoft
O cmdlet seguinte fornece a ARP tabelas para peering da Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Saída de exemplo é mostrada abaixo para um dos caminhos de

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como utilizar estas informações
A tabela de ARP de um peering pode ser utilizada para determinar a validar a configuração de camada 2, a conectividade. Esta secção fornece uma descrição geral de como tabelas de ARP serão apresentada em diferentes cenários.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela de ARP quando um circuito está no estado operacional (estado esperado)
* A tabela de ARP tem uma entrada para o lado no local com um endereço IP válido e um endereço MAC e uma entrada semelhante para o lado da Microsoft. 
* O octeto última do endereço ip no local será sempre um número ímpar.
* O octeto última do endereço ip Microsoft será sempre um número par.
* O mesmo endereço MAC será apresentado no lado da Microsoft para todos os 3 peerings (primários / secundárias). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela de ARP quando no local / lado de fornecedor de conectividade tem problemas
Se existem problemas com no local ou o fornecedor de conectividade, que poderá ver o que é apenas uma entrada irá aparecer na tabela de ARP ou o endereço de MAC no local irá mostrar incompleta. Isto irá mostrar o mapeamento entre o endereço MAC e o endereço IP utilizado no lado da Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

ou
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Abra um pedido de suporte com o fornecedor de conectividade para esses problemas de depuração. Se a tabela de ARP não tem endereços IP das interfaces mapeados para os endereços MAC, reveja as seguintes informações:
> 
> 1. Se o primeiro endereço IP de/30 sub-rede atribuída para a ligação entre o MSEE ELI e MSEE é utilizada na interface do MSEE PR. Azure utiliza sempre o segundo endereço IP para MSEEs.
> 2. Verifique se o cliente (C-Tag) e as etiquetas VLAN de serviço (S-Tag) coincidem com ambos num par de MSEE ELI e MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela de ARP ao lado da Microsoft tem problemas
* Não verá uma tabela de ARP mostrada para um peering se existem problemas no lado de Microsoft. 
* Abra um pedido de suporte com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique a que tem um problema com conectividade de camada 2. 

## <a name="next-steps"></a>Passos Seguintes
* Validar as configurações de camada 3 para o seu circuito do ExpressRoute
  * Obter a rota de resumo para determinar o estado de sessões BGP 
  * Obter tabela de rotas para determinar os prefixos estão anunciados através do ExpressRoute
* Validar a transferência de dados, revendo os bytes de entrada / saída
* Abra um pedido de suporte com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda ocorrerem problemas.

