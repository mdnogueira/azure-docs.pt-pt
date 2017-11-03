---
title: "Descrição geral de IPv6 para o Balanceador de carga do Azure | Microsoft Docs"
description: "Noções sobre o suporte de IPv6 para o Azure Load Balancer e as VMs com balanceamento de carga."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móveis, iot"
ms.assetid: 6a1d583f-a305-40fd-a94b-fa42e1943bbb
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 1902475c81c4f83f8ba69a05f9564bc65a5de833
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Descrição geral de IPv6 para o Balanceador de carga do Azure


[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Balanceadores de carga com acesso à Internet podem ser implementados com um endereço IPv6. Para além de conectividade de IPv4, isto permite que as seguintes capacidades:

* Nativa ponto-a-ponto conetividade IPv6 entre os clientes de Internet públicos e máquinas de virtuais (VMs) do Azure através do Balanceador de carga.
* Nativa ponto-a-ponto saída conetividade IPv6 entre VMs e os clientes com Internet IPv6 ativado públicos.

A imagem seguinte ilustra a funcionalidade de IPv6 para o Balanceador de carga do Azure.

![Balanceador de carga do Azure com o IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Depois de implementada, um cliente IPv4 ou IPv6 ativado Internet pode comunicar com endereços IPv4 ou IPv6 público (ou os nomes de anfitrião) do Balanceador de carga do Azure através da Internet. O load balancer encaminha os pacotes de IPv6 para os endereços IPv6 privados das VMs com a tradução de endereços de rede (NAT). O cliente de IPv6 Internet não é possível comunicar diretamente com o endereço IPv6 das VMs.

## <a name="features"></a>Funcionalidades

Suporte IPv6 nativo para as VMs implementadas através do Azure Resource Manager fornece:

1. Serviços com balanceamento de carga de IPv6 para IPv6 clientes na Internet
2. IPv6 nativo e IPv4 pontos finais em VMs ("dual empilhada")
3. Entrada e saída iniciar ligações de IPv6 nativas
4. Protocolos suportados, tais como TCP, UDP e HTTP (S), ativar uma gama completa de arquiteturas de serviço

## <a name="benefits"></a>Benefícios

Esta funcionalidade permite que as principais vantagens seguintes:

* Cumprir os regulamentos government que requerem que seja acessível aos clientes apenas de IPv6 novas aplicações
* Ativar mobile e Internet das coisas (IOT) que os programadores de utilizar máquinas virtuais do Azure (IPv4 + IPv6) dual-empilhadas para abordar a mobile crescente & IOT mercados

## <a name="details-and-limitations"></a>Os detalhes e limitações

Detalhes

* O serviço DNS do Azure contém registos de nome de um de IPv4 e IPv6 AAAA e responde com ambos os registos para o Balanceador de carga. O cliente escolhe qual o endereço (IPv4 ou IPv6) para comunicar com.
* Quando uma VM inicia uma ligação a um dispositivo com ligação à Internet IPv6 público, origem a VM endereço IPv6 é endereço de rede traduzido (NAT) para o endereço IPv6 público do load balancer.
* As VMs com o sistema operativo Linux tem de ser configuradas para receber um endereço IP do IPv6 através de DHCP. Muitas das imagens de Linux na galeria do Azure já estão configuradas para suportar IPv6 sem modificação. Para obter mais informações, consulte [configurar DHCPv6 para VMs com Linux](load-balancer-ipv6-for-linux.md)
* Se optar por utilizar uma sonda do Estado de funcionamento com o Balanceador de carga, crie uma pesquisa de IPv4 e utilizá-la com pontos finais de IPv4 e IPv6. Se o serviço na sua VM ficar inativo, pontos finais de IPv4 e IPv6 são retirados rotação.

Limitações

* Não é possível adicionar regras de balanceamento de carga de IPv6 no portal do Azure. As regras só podem ser criadas através do modelo, CLI, do PowerShell.
* Não pode atualizar as VMs existentes para utilizar endereços IPv6. Tem de implementar novas VMs.
* Um único endereço de IPv6 pode ser atribuído a uma única interface de rede em cada VM.
* Não não possível atribuir os endereços IPv6 públicos para uma VM. Só podem ser atribuídos a um balanceador de carga.
* Não é possível configurar a pesquisa inversa de DNS para os endereços IPv6 públicos.
* As VMs com os endereços IPv6 não podem ser membros de um serviço em nuvem do Azure. Estes podem ser ligadas a uma rede Virtual do Azure (VNet) e comunicarem entre si através dos respetivos endereços de IPv4.
* Endereços IPv6 privada podem ser implementados em VMs individuais num grupo de recursos, mas não podem ser implementados para um grupo de recursos através de conjuntos de dimensionamento.
* Não é possível ligar as VMs do Azure através de IPv6 para as outras VMs, Azure ou outros serviços, dispositivos no local. Só pode comunicar com o Balanceador de carga do Azure através de IPv6. No entanto, pode comunicam com estes outros recursos com IPv4.
* Proteção de grupo de segurança de rede (NSG) para o IPv4 é suportada nas implementações de pilha dupla (IPv4 + IPv6). Os NSGs não se aplicam aos pontos finais do IPv6.
* O ponto final de IPv6 na VM não está exposto diretamente à internet. É por trás de um balanceador de carga. Apenas as portas especificadas nas regras de Balanceador de carga estão acessíveis através de IPv6.
* Alterar o parâmetro IdleTimeout para IPv6 é **não suportada atualmente**. A predefinição é de quatro minutos.
* Alterar o parâmetro loadDistributionMethod para IPv6 é **não suportada atualmente**.
* Reservado IPv6 IPs (onde IPAllocationMethod = estático) são **não suportada atualmente**.

## <a name="next-steps"></a>Passos seguintes

Saiba como implementar um balanceador de carga com o IPv6.

* [Disponibilidade de IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implementar um balanceador de carga com o IPv6 através de um modelo](load-balancer-ipv6-internet-template.md)
* [Implementar um balanceador de carga com o IPv6 com o Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Implementar um balanceador de carga com o IPv6, utilizando a CLI do Azure](load-balancer-ipv6-internet-cli.md)
