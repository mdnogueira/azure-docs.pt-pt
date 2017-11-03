---
title: "Obter ARP tabelas: clássico: Resolução de problemas de ExpressRoute do Azure | Microsoft Docs"
description: "Esta página fornece instruções para obter o ARP tabelas para um circuito ExpressRoute."
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: b5856acf-03c2-4933-8111-6ce12998d92a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: fcc847b7e30fd55ca759830e0254ab7542e7663e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Obter ARP tabelas no modelo de implementação clássica
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Clássica](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo explica os passos para obter as tabelas de protocolo ARP (Address Resolution Protocol) para o seu circuito de ExpressRoute do Azure.

> [!IMPORTANT]
> Este documento destina-se para o ajudar a diagnosticar e corrigir problemas simples. Não se destina a ser uma substituição para o suporte da Microsoft. Se não conseguir resolver o problema utilizando as seguintes orientações, abra um pedido de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protocolo ARP (Resolution Protocol) e ARP tabelas de endereços
ARP é um protocolo de camada 2 que está definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é utilizado para mapear um endereço Ethernet (endereço MAC) para um endereço IP.

Uma tabela de ARP fornece um mapeamento de endereço IPv4 e de endereço MAC para um determinado peering. A tabela de ARP para um circuito de ExpressRoute peering fornece as seguintes informações para cada interface (principal e secundário):

1. Mapeamento de um endereço IP de interface router no local para um endereço MAC
2. Mapeamento de um endereço IP do interface do router do ExpressRoute para um endereço MAC
3. A antiguidade do mapeamento

Tabelas de ARP podem ajudar a validar a configuração de camada 2 e resolução de problemas de conectividade de camada 2 básicos.

Segue-se um exemplo de uma tabela de ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A seguinte secção fornece informações sobre como ver as tabelas de ARP são visualizadas pelos routers de limite do ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Pré-requisitos para utilizar tabelas de ARP
Certifique-se de que tem o seguinte antes de continuar:

* Um circuito de ExpressRoute válido que esteja configurado com pelo menos um peering. O circuito deve estar totalmente configurado pelo fornecedor de conectividade. Utilizador (ou o fornecedor de conectividade) tem de configurar pelo menos um dos peerings (Azure privado, Azure público ou Microsoft) neste circuito.
* Intervalos de endereços IP que são utilizados para configurar os peerings (Azure privado, Azure público e Microsoft). Reveja os exemplos de atribuição de endereço IP no [página de requisitos de encaminhamento ExpressRoute](expressroute-routing.md) para obter uma compreensão dos como endereços IP são mapeados para interfaces no seu aise e do lado do ExpressRoute. Pode obter informações sobre a configuração do peering revendo o [página de configuração de peering de ExpressRoute](expressroute-howto-routing-classic.md).
* Informações do seu fornecedor de equipa ou conectividade de rede sobre os endereços MAC das interfaces que são utilizadas com estes endereços IP.
* O módulo Windows PowerShell mais recente do Azure (versão 1.50 ou posterior).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabelas de ARP para o seu circuito do ExpressRoute
Esta secção fornece instruções sobre como ver as tabelas de ARP para cada tipo de peering através do PowerShell. Antes de continuar, ou o fornecedor de conectividade são precisa de configurar o peering. Cada circuito tem dois caminhos (principais e secundários). Pode verificar a tabela de ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas de ARP para peering privado do Azure
O cmdlet seguinte fornece a ARP tabelas para peering privado do Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Segue-se a saída de exemplo para um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP as tabelas do peering público do Azure:
O cmdlet seguinte fornece a ARP tabelas para o peering público do Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Segue-se a saída de exemplo para um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Segue-se a saída de exemplo para um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas de ARP para peering da Microsoft
O cmdlet seguinte fornece a ARP tabelas para peering da Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Saída de exemplo é mostrada abaixo, para um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como utilizar estas informações
A tabela de ARP de um peering pode ser utilizada para validar a configuração de camada 2, a conectividade. Esta secção fornece uma descrição geral do aspeto das tabelas de ARP em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela de ARP quando um circuito está num estado operacional (esperado)
* A tabela de ARP tem uma entrada para o lado no local com um endereço MAC e IP válido e uma entrada semelhante para o lado da Microsoft.
* O octeto última do endereço IP no local é sempre um número ímpar.
* O octeto última do endereço IP da Microsoft é sempre um número par.
* O mesmo endereço MAC é apresentado no lado da Microsoft para todos os três peerings (primário/secundário).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabela de ARP quando está no local ou quando o lado do fornecedor de conectividade tem problemas
 É apresentada apenas uma entrada na tabela de ARP. Mostra o mapeamento entre o endereço MAC e o endereço IP que é utilizado no lado de Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Se ocorrer um problema, como tal, abra um pedido de suporte com o seu fornecedor de conectividade resolvê-lo.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela de ARP ao lado da Microsoft tem problemas
* Não verá uma tabela de ARP mostrada para um peering se existem problemas no lado de Microsoft.
* Abra um pedido de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique a que tem um problema com conectividade de camada 2.

## <a name="next-steps"></a>Passos seguintes
* Valide as configurações de camada 3 para o seu circuito de ExpressRoute:
  * Obter uma rota de resumo para determinar o estado de sessões BGP.
  * Obter uma tabela de rota para determinar os prefixos estão anunciados através do ExpressRoute.
* Valide a transferência de dados, revendo bytes e terminar.
* Abra um pedido de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda ocorrerem problemas.

