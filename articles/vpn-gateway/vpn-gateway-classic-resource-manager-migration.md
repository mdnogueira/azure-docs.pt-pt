---
title: "Gateway VPN clássico para migração do Gestor de recursos | Microsoft Docs"
description: "Esta página fornece uma descrição geral do clássico de Gateway de VPN para a migração do Gestor de recursos."
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: 1164fc24355657af22b6befaad74685ebbc2b5cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Clássica do Gateway de VPN para a migração do Gestor de recursos
Gateways de VPN agora podem ser migrados do clássico para o modelo de implementação do Resource Manager. Pode ler mais sobre o Azure Resource Manager [funcionalidades e vantagens](../azure-resource-manager/resource-group-overview.md). Neste artigo, vamos detalhe como migrar a partir de implementações clássicas ao modelo de Gestor de recursos com base mais recente. 

Gateways de VPN são migrados como parte da migração de VNet do clássico para o Gestor de recursos. Esta migração é efetuada uma VNet a uma hora. Não há nenhum requisito adicional em termos de ferramentas ou pré-requisitos para migração. Passos de migração são idênticos para a migração de VNet existente e estão documentados em [página de migração de recursos IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Não existe nenhum período de indisponibilidade do caminho de dados durante a migração e, por conseguinte, as cargas de trabalho existentes iriam continuar a funcionar sem perda de conectividade no local durante a migração. O endereço IP público associado ao gateway VPN não altera durante o processo de migração. Isto implica que não terá de reconfigurar o router no local depois de concluída a migração.  

O modelo no Gestor de recursos é diferente do modelo clássico e é composto de gateways de rede virtual, de gateways de rede local e de recursos de ligação. Estes representam o próprio gateway de VPN, o site local representando no espaço de endereços no local, a conectividade entre as duas, respetivamente. Depois de concluída a migração não seria disponíveis no modelo clássico seus gateways e todas as operações de gestão em gateways de rede virtual, gateways de rede local e objetos de ligação tem de ser efetuadas utilizando o modelo do Resource Manager.

## <a name="supported-scenarios"></a>Cenários suportados
Cenários mais comuns de conectividade VPN estão abrangidos pelos clássico para migração do Gestor de recursos. Os cenários suportados incluem-

* Aponte para conectividade do site
* Site a conectividade do site com o Gateway de VPN ligadas numa localização local
* VNet a VNet conectividade entre duas VNets com gateways de VPN
* Várias VNets ligadas mesma numa localização local
* Conetividade multilocal
* Imposição do túnel ativada VNets

Os cenários que não são suportados incluem-  

* VNet com o Gateway do ExpressRoute e o Gateway de VPN não é atualmente suportada.
* Trânsito cenários em que as extensões VM estão ligadas aos servidores no local. Limitações de conectividade VPN de trânsito detalhadas abaixo.

> [!NOTE]
> Validação de CIDR no modelo do Resource Manager é mais restrita do que um modelo de clássico. Antes de migrar Certifique-se de que os intervalos de endereços clássico fornecidos está em conformidade com formato CIDR válido antes de iniciar a migração. CIDR possa ser validada utilizando qualquer validações CIDR comuns. VNet ou sites locais com intervalos CIDR inválidos quando migrada resultaria num Estado com falhas.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet para VNet a migração de conectividade
VNet a VNet conectividade no clássico foi alcançada através da criação de uma representação de local site da VNet ligada. Os clientes era necessário criar dois sites locais representado as duas VNets que necessários para ser ligadas em conjunto. Em seguida, estes foram ligados para as VNets correspondentes com o túnel IPsec para estabelecer conectividade entre as duas VNets. Este modelo tem desafios de capacidade de gestão, uma vez que as alterações de intervalo de endereços na um VNet também têm de ser mantidas em representação de local site correspondente. No modelo do Resource Manager esta solução já não é necessária. A ligação entre duas VNets pode ser conseguida de diretamente com o tipo de ligação 'Vnet2Vnet' no recurso de ligação. 

![Captura de ecrã da VNet para VNet a migração.](./media/vpn-gateway-migration/migration1.png)

Durante a migração de VNet detectarmos que a entidade ligada ao gateway de VPN da VNet atual é outra VNet e certifique-se de que, depois de concluída a migração de ambas as VNets, já não seria ver dois sites locais que representa a outra VNet. O modelo clássico de dois gateways de VPN, os dois sites locais e duas ligações entre eles é transformado para o modelo do Resource Manager com dois gateways de VPN e duas ligações do tipo Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Conectividade VPN de trânsito
Pode configurar gateways de VPN numa topologia de forma a que conectividade no local para uma VNet é conseguida ao ligar a outra VNet que está diretamente ligado no local. Este é trânsito conectividade VPN onde instâncias na primeira VNet estão ligadas aos recursos no local através de trânsito para o gateway VPN na VNet ligado que estejam ligado diretamente no local. Para alcançar esta configuração no modelo de implementação clássica, terá de criar um site local que foi agregada prefixos que representa a VNet ligada e o espaço de endereços no local. Este site local representational está ligado, em seguida, para a VNet para alcançar a conectividade de trânsito. Este modelo clássico tem também semelhante desafios de capacidade de gestão, uma vez que qualquer alteração no intervalo de endereços no local também têm de ser mantida no site local que representa a agregação de VNet e no local. Introdução de suporte BGP nos gateways de Gestor de recursos suportados simplifica a capacidade de gestão, uma vez que os gateways ligados podem saber as rotas no local sem modificação manual para prefixos.

![Captura de ecrã do cenário de encaminhamento de trânsito.](./media/vpn-gateway-migration/migration2.png)

Uma vez que vamos transformar VNet a VNet conectividade sem necessidade de sites locais, o cenário de trânsito perde a conectividade de no local para a VNet que está ligada indiretamente no local. A perda de conectividade pode ser mitigada duas formas seguintes, depois de concluída a migração- 

* Ative o BGP nos gateways de VPN que estão ligados em conjunto e no local. Ativar o BGP restaura a conectividade sem qualquer alteração de configuração desde rotas aprendidas e anunciadas entre os gateways de VNet. Tenha em atenção que a opção de BGP só está disponível no SKUs padrão e planos superiores.
* Estabelece uma ligação explícita do VNet afetado para o gateway de rede local que representa a localização no local. Isto também exigiriam configuração no router no local para criar e configurar o túnel IPsec.

## <a name="next-steps"></a>Passos seguintes
Depois de aprender mais sobre o suporte de migração de gateway VPN, aceda a [plataforma suportada a migração de recursos IaaS do clássico para o Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) para começar a utilizar.

