---
title: "Planeamento e design para ligações em vários locais: Gateway de VPN do Azure | Microsoft Docs"
description: "Saiba mais sobre o planeamento de Gateway de VPN e de design para vários locais, híbridas e ligações VNet a VNet"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2017
ms.author: cherylmc
ms.openlocfilehash: 0ebc3ef4a64432e993dd6ed69766bb64544fe433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planear e conceber para o Gateway de VPN

Planear e conceber a sua em vários locais e VNet a VNet configurações podem ser simples ou complicadas, consoante as suas necessidades de rede. Este artigo explica básicas considerações de planeamento e design.

## <a name="planning"></a>Planeamento

### <a name="compare"></a>Opções de conectividade em vários locais

Se pretender ligar os sites no local de forma segura a uma rede virtual, tem três formas diferentes para fazê-lo: Site a Site, ponto a Site e ExpressRoute. Compare as ligações entre locais diferentes que estão disponíveis. A opção que escolher pode depender de várias considerações, tais como:

* Que tipo de débito requer a sua solução?
* Pretende comunicar através da Internet pública via VPN segura ou através de uma ligação privada?
* Tem um endereço IP público disponível para utilização?
* Está a planear utilizar um dispositivo VPN? Se assim for, é compatível?
* Está a ligar apenas alguns computadores ou pretende uma ligação persistente para o seu site?
* Que tipo de gateway de VPN é necessário para a solução que pretende criar?
* O SKU de gateway deve utilizar?

### <a name="planningtable"></a>Tabela de planeamento

A tabela seguinte pode ajudar a decidir a melhor opção de conectividade para a sua solução.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Fluxo de trabalho

A lista seguinte descreve o fluxo de trabalho comuns para a conectividade de cloud:

1. Conceber e planear a topologia de conectividade e liste os espaços de endereços para todas as redes que pretende ligar.
2. Crie uma rede virtual do Azure. 
3. Crie um gateway VPN para a rede virtual.
4. Criar e configurar ligações a redes no local ou outras redes virtuais (conforme necessário).
5. Criar e configurar uma ligação ponto a Site para o gateway de VPN do Azure (conforme necessário).

## <a name="design"></a>Design
### <a name="topologies"></a>Topologias da ligação

Iniciar observando os diagramas no [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) artigo. O artigo contém diagramas básicos, os modelos de implementação para cada topologia e as ferramentas de implementação disponíveis que pode utilizar para implementar a configuração.

### <a name="designbasics"></a>Noções básicas de design

As secções seguintes abordam as noções básicas de gateway VPN. 

#### <a name="servicelimits"></a>Limites de serviços de rede

Desloque-se através de tabelas para ver [limites de serviços de redes](../azure-subscription-service-limits.md#networking-limits). Os limites indicados podem ter impacto na sua estrutura.

#### <a name="subnets"></a>Sobre sub-redes

Quando estiver a criar ligações, tem de considerar os intervalos de sub-rede. Não pode ter a sobreposição de intervalos de endereços de sub-rede. Uma sub-rede sobreposta é quando uma localização no local ou de rede virtual contém o mesmo espaço de endereços que contém a outra localização. Isto significa que terá dos engenheiros de rede para as redes locais no local para extrair um intervalo para que possa utilizar para o IP de Azure endereçamento espaço/sub-redes. É necessário espaço de endereço que não está a ser utilizado na rede local no local.

Evitar a sobreposição de sub-redes é também importante quando estiver a trabalhar com ligações VNet a VNet. Se existir sobreposição das sub-redes e um endereço IP existe de envio e de destino VNets, ligações VNet a VNet falharem. Azure não é possível encaminhar os dados para outra VNet, porque o endereço de destino faz parte da VNet envio.

Gateways de VPN necessitam de uma sub-rede específica, denominada uma sub-rede de gateway. Para funcionarem corretamente, todas as sub-redes do gateway têm de ter o nome GatewaySubnet. Lembre-se de que não nome à sub-rede do gateway um nome diferente e não implemente VMs ou mais nada para a sub-rede do gateway. Consulte [sub-redes de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Sobre os gateways de rede local

O gateway de rede local refere-se normalmente à sua localização no local. No modelo de implementação clássica, o gateway de rede local é referido como um Site de rede Local. Quando configurar um gateway de rede local, atribua um nome, especifique o endereço IP público do dispositivo VPN no local e, especifique os prefixos de endereços que estão na localização no local. Azure analisa os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que tenha especificado para o gateway de rede local e encaminha os pacotes em conformidade. Pode modificar os prefixos de endereço, conforme necessário. Para obter mais informações, consulte [gateways de rede Local](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Sobre os tipos de gateway

Selecionar o tipo de gateway correto para a topologia é fundamental. Se selecionar o tipo errado, o gateway não funcionará corretamente. O tipo de gateway especifica o modo como o gateway em si se liga e é uma configuração obrigatória no modelo de implementação Resource Manager.

Os tipos de gateway são:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>Sobre os tipos de ligação

Cada configuração exige um tipo específico de ligação. Os tipos de ligação são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Sobre os tipos de VPN

Cada configuração exige um tipo específico de VPN. Se está a combinar duas configurações, por exemplo, a criar uma ligação Site a Site e uma ligação Ponto a Site para a mesma VNet, tem de utilizar um tipo de VPN que cumpra ambos os requisitos de ligação.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

As tabelas seguintes mostram o tipo de VPN, que mapeia para cada configuração da ligação. Certifique-se de que o tipo de VPN para o seu gateway corresponde a configuração que pretende criar. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Dispositivos VPN para ligações Site a Site

Para configurar uma ligação Site a Site, independentemente do modelo de implementação, terá dos seguintes itens:

* Um dispositivo VPN que é compatível com gateways de VPN do Azure
* Um endereço IPv4 IP destinado ao público que não está protegido por um NAT

Tem de ter experiência de configuração do dispositivo VPN ou que alguém que pode configurar o dispositivo para si.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Considere o encaminhamento de imposição de túnel

Para a maioria das configurações, pode configurar a imposição do túnel. Imposição do túnel permite-lhe redirecionamento ou "forçar" todo o tráfego vinculado à Internet de volta para a sua localização no local através de um túnel VPN Site a Site para inspeção e auditoria. Este é um requisito de segurança críticas para TI empresariais de maioria das políticas. 

Sem a imposição do túnel, o tráfego vinculado à Internet do seu VMs no Azure serão atravessar sempre da infraestrutura de rede do Azure diretamente saída à Internet, sem a opção para permitir a inspecionar ou o tráfego de auditoria. Acesso à Internet não autorizado, potencialmente, pode levar a divulgação de informações ou outros tipos de falhas de segurança.

Uma ligação de túnel forçada pode ser configurada em ambos os modelos de implementação e utilizando ferramentas diferentes. Para obter mais informações, consulte [configurar imposição do túnel](vpn-gateway-forced-tunneling-rm.md).

**Forçado diagrama túnel**

![Diagrama de túnel forçado o Gateway de VPN do Azure](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Passos seguintes

Consulte o [FAQs do VPN Gateway](vpn-gateway-vpn-faq.md) e [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) artigos para obter mais informações para ajudá-lo com a estrutura.

Para obter mais informações sobre as definições do gateway específico, consulte [sobre o VPN Gateway definições](vpn-gateway-about-vpn-gateway-settings.md).