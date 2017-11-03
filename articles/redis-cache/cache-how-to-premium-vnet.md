---
title: Configurar uma rede Virtual para uma Cache de Redis do Azure Premium | Microsoft Docs
description: "Saiba como criar e gerir o suporte de rede Virtual para as instâncias de Cache de Redis do Azure do escalão Premium"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: sdanie
ms.openlocfilehash: 59d46990e02c0719d2b4df01e216a97fd649c509
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Como configurar o suporte da Virtual Network para uma Cache de Redis do Azure Premium
Cache de Redis do Azure tem ofertas de cache diferente, que fornecem flexibilidade na escolha de funcionalidades, incluindo funcionalidades do escalão Premium, tais como clustering, persistência e suporte da virtual network e tamanho da cache. Uma VNet é uma rede privada na nuvem. Quando uma instância da Cache de Redis do Azure está configurada com uma VNet, não é acessível publicamente e só pode ser acedido a partir de máquinas virtuais e aplicações dentro da VNet. Este artigo descreve como configurar o suporte de rede virtual para uma instância de Cache de Redis do Azure premium.

> [!NOTE]
> Cache de Redis do Azure suporta tanto clássico e as VNets do Resource Manager.
> 
> 

Para obter informações sobre outras funcionalidades de cache premium, consulte [introdução para o escalão Premium do Azure Redis Cache](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que motivo VNet?
[Rede Virtual do Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) implementação proporciona maior segurança e isolamento para a Cache de Redis do Azure, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringem o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual
Suporte da virtual Network (VNet) é configurado no **nova Cache de Redis** painel durante a criação da cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Assim que tiver selecionado um escalão de preços premium, pode configurar a integração de Redis VNet selecionando uma VNet que está na mesma subscrição e localização da cache. Para utilizar uma nova VNet, criá-lo primeiro ao seguir os passos em [criar uma rede virtual com o portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [criar uma rede virtual (clássica) utilizando o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e, em seguida, volte à **nova Cache de Redis** painel para criar e configurar a cache de premium.

Para configurar a VNet para a nova cache, clique em **rede Virtual** no **nova Cache de Redis** painel e selecione a VNet pretendida na lista pendente.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede pretendida do **sub-rede** pendente lista e especifique o pretendido **endereço IP estático**. Se estiver a utilizar uma VNet clássica a **endereço IP estático** campo é opcional e, se for especificado nenhum, um é escolhido de à sub-rede selecionada.

> [!IMPORTANT]
> Quando implementar uma Cache de Redis do Azure para uma VNet do Resource Manager, tem de ser a cache numa sub-rede dedicada que contém outros recursos de exceto instâncias de Cache de Redis do Azure. Se for efetuada uma tentativa para implementar uma Cache de Redis do Azure para uma VNet do Resource Manager a uma sub-rede que contém outros recursos, a implementação falhará.
> 
> 

![Rede virtual][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure reserva-se alguns endereços IP dentro de cada sub-rede e estes endereços não podem ser utilizados. Os endereços IP primeiro e últimos das sub-redes estão reservados para compatibilidade com o protocolo, juntamente com três endereços mais utilizados para serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como utilizar estas sub-redes de endereços IP?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Para além dos endereços IP utilizados pela infraestrutura do Azure VNET, cada Redis instância os endereços IP do sub-rede utiliza dois por ID de partição horizontal e um endereço IP adicional para o Balanceador de carga. É considerada uma cache agrupado com um ID de partição horizontal.
> 
> 

Depois da cache é criada, pode ver a configuração para a VNet clicando **rede Virtual** do **menu recursos**.

![Rede virtual][redis-cache-vnet-info]

Para ligar à sua instância da cache de Redis do Azure ao utilizar uma VNet, especifique o nome de anfitrião da cache na cadeia de ligação, conforme mostrado no exemplo seguinte:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Cache de Redis do Azure VNet FAQ
A lista seguinte contém as respostas a perguntas mais comuns sobre o dimensionamento de Cache de Redis do Azure.

* [Quais são alguns problemas de configuração incorreta comum com a Cache de Redis do Azure e as VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Como verificar se a minha cache está a funcionar numa VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Pode utilizar as VNets com uma cache básica ou padrão?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Por que motivo criar uma cache de Redis falha na algumas sub-redes, mas não a outros utilizadores?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todas as funcionalidades de cache funcionam quando alojam uma cache numa VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quais são alguns problemas de configuração incorreta comum com a Cache de Redis do Azure e as VNets?
Quando a Cache de Redis do Azure está alojada numa VNet, as portas nas tabelas seguintes são utilizadas. 

>[!IMPORTANT]
>Se as portas nas tabelas seguintes são bloqueadas, a cache poderá não funcionar corretamente. Um ou mais destas portas bloqueadas é ter o problema de configuração incorreta mais comuns ao utilizar a Cache de Redis do Azure numa VNet.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Existem sete requisitos de porta de saída.

- Se podem ser efectuadas ligações pretendidas, toda a saída à internet através de um cliente no local auditoria do dispositivo.
- Três das portas encaminhar o tráfego para pontos finais Azure Storage do Azure e o DNS do Azure de manutenção.
- Os intervalos de portas restantes e para comunicações de sub-rede internas do Redis. Não existem regras NSG de sub-rede são necessárias para comunicações de sub-rede internas do Redis.

| Portas | Direção | Protocolo de transporte | Objetivo | Local IP | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Redis dependências no Azure armazenamento/PKI (Internet) | (Redis sub-rede) |* |
| 53 |Saída |TCP/UDP |Redis dependências no DNS (Internet/VNet) | (Redis sub-rede) |* |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) | (Redis sub-rede) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) | (Redis sub-rede) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |


### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Existem oito requisitos de intervalo de portas de entrada. Pedidos de entrada destes intervalos são internos para as comunicações de sub-rede Redis ou entrada de outros serviços alojados na mesma VNET.

| Portas | Direção | Protocolo de transporte | Objetivo | Local IP | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com Redis, balanceamento de carga do Azure | (Redis sub-rede) |Rede virtual, o Balanceador de carga do Azure |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |
| 8500 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Redis sub-rede) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede), o Balanceador de carga do Azure |
| 13000-13999 |Entrada |TCP |Comunicação do cliente com Clusters de Redis, balanceamento de carga do Azure | (Redis sub-rede) |Rede virtual, o Balanceador de carga do Azure |
| 15000-15999 |Entrada |TCP |Comunicação do cliente com Clusters de Redis, balanceamento de carga do Azure | (Redis sub-rede) |Rede virtual, o Balanceador de carga do Azure |
| 16001 |Entrada |TCP/UDP |Balanceamento de carga do Azure | (Redis sub-rede) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Redis sub-rede) |(Redis sub-rede) |

### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos de conectividade de rede VNET adicionais

Existem requisitos de conectividade de rede do Azure Redis Cache que não pode ser cumprido inicialmente numa rede virtual. Cache de Redis do Azure requer que todos os itens seguintes para funcionar corretamente quando utilizada dentro de uma rede virtual.

* Conectividade de rede de saída para os pontos finais de armazenamento do Azure em todo o mundo. Isto inclui pontos finais localizados na mesma região que a instância da Cache de Redis do Azure, bem como pontos finais de armazenamento localizados na **outros** regiões do Azure. Pontos finais de armazenamento do Azure resolver em seguintes domínios DNS: *w*, *w*, *q*, e *w*. 
* Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com*, e *crl.microsoft.com*. Este conectividade é necessário para suportar a funcionalidade SSL.
* A configuração de DNS para a rede virtual tem de ser capaz de resolver todos os pontos finais e domínios mencionados em pontos de anteriores. Estes requisitos de DNS podem ser cumpridos ao garantir uma infraestrutura de DNS válida está configurada e mantida para a rede virtual.
* Conectividade de rede de saída para as seguintes Azure pontos finais de monitorização, que resolver em seguintes domínios DNS: shoebox2 black.shoebox2.metrics.nsatc.net prod2.prod2.metrics.nsatc.net norte, azglobal black.azglobal.metrics.nsatc.net, shoebox2 red.shoebox2.metrics.nsatc.net, Leste prod2.prod2.metrics.nsatc.net, azglobal red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como verificar se a minha cache está a funcionar numa VNET?

>[!IMPORTANT]
>Ao ligar a uma instância da Cache de Redis do Azure que está alojada numa VNET, os clientes de cache tem de ser na mesma VNET, incluindo aplicações de teste ou ferramentas de diagnóstico efetuando o ping.
>
>

Depois dos requisitos de porta estão configurados conforme descrito na secção anterior, pode verificar a sua cache está a funcionar, efetuando os seguintes passos.

- [Reiniciar](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessário não podem ser alcançadas (conforme documentado no [requisitos de portas de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e [requisitos de porta de saída](cache-how-to-premium-vnet.md#outbound-port-requirements)), a cache não conseguir reiniciar com êxito.
- Assim que os nós de cache reiniciaram (conforme comunicado pelo Estado da cache no portal do Azure), pode efetuar os seguintes testes:
  - enviar um ping o cache ponto final (utilizando a porta 6380) de uma máquina que se encontra na mesma VNET, como a cache, utilizando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se o `tcping` ferramenta relatórios que a porta está aberta, a cache está disponível para a ligação de clientes na VNET.

  - Outra forma de testar consiste em criar um cliente de cache de teste (que pode ser uma simples aplicação de consola com stackexchange. redis) que liga à cache e adiciona e obtém alguns itens da cache. Instale a aplicação de cliente de exemplo para uma VM que está a ser a mesma VNET, como a cache e executá-la para verificar a conectividade à cache.


### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Pode utilizar as VNets com uma cache básica ou padrão?
As VNets só pode ser utilizadas com premium caches.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Por que motivo criar uma cache de Redis falha na algumas sub-redes, mas não a outros utilizadores?
Se estiver a implementar uma Cache de Redis do Azure para uma VNet do Resource Manager, tem de ser a cache numa sub-rede dedicada com nenhum outro tipo de recurso. Se for efetuada uma tentativa para implementar uma Cache de Redis do Azure a uma sub-rede da VNet do Resource Manager que contém outros recursos, a implementação falhará. Tem de eliminar os recursos dentro da sub-rede existentes antes de poder criar uma nova cache de Redis.

Pode implementar vários tipos de recursos para uma VNet clássica, desde que tem endereços IP suficientes disponíveis.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?
Azure reserva-se alguns endereços IP dentro de cada sub-rede e estes endereços não podem ser utilizados. Os endereços IP primeiro e últimos das sub-redes estão reservados para compatibilidade com o protocolo, juntamente com três endereços mais utilizados para serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como utilizar estas sub-redes de endereços IP?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Para além dos endereços IP utilizados pela infraestrutura do Azure VNET, cada Redis instância os endereços IP do sub-rede utiliza dois por ID de partição horizontal e um endereço IP adicional para o Balanceador de carga. É considerada uma cache agrupado com um ID de partição horizontal.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todas as funcionalidades de cache funcionam quando alojam uma cache numa VNET?
Quando a cache faz parte de uma VNET, apenas os clientes na VNET podem aceder à cache. Como resultado, as seguintes funcionalidades de gestão de cache não funcionam neste momento.

* Consola de redis - porque consola Redis é executado no seu browser local, o que está fora da VNET, não é possível ligar à sua cache.

## <a name="use-expressroute-with-azure-redis-cache"></a>Utilizar o ExpressRoute com a Cache de Redis do Azure
Os clientes podem ligar-se um [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuito a respetiva infraestrutura de rede virtuais, assim expandir a sua rede no local para o Azure. 

Por predefinição, um circuito ExpressRoute criado recentemente não pratica a imposição do túnel (anúncio de uma rota predefinida, 0.0.0.0/0) na VNET. Como resultado, a conectividade de Internet de saída é permitida diretamente a partir de VNET e aplicações cliente conseguem estabelecer ligação com outros pontos finais do Azure, incluindo a Cache de Redis do Azure.

No entanto uma configuração de cliente comum consiste em utilizar a imposição do túnel (anunciar uma rota predefinida) que força o tráfego de Internet de saída para o fluxo em vez disso, no local. Este fluxo de tráfego de quebras de conectividade com a Cache de Redis do Azure se o tráfego de saída, em seguida, bloqueado no local, de modo a que a instância da Cache de Redis do Azure não consegue comunicar com as respetivas dependências.

A solução é definir um (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém a Cache de Redis do Azure. Um UDR define as rotas de sub-rede específicos que serão cumpridas em vez da rota predefinida.

Se for possível, recomenda-se para utilizar a seguinte configuração:

* A configuração do ExpressRoute anuncia 0.0.0.0/0 e por predefinição, force túneis todos os tráfego de saída no local.
* UDR aplicado à sub-rede que contém a Cache de Redis do Azure define 0.0.0.0/0 com uma rota de trabalho para o tráfego de TCP/IP para a internet pública; Por exemplo por definir o tipo de próximo salto "Internet".

O efeito combinado destes passos é que o nível de sub-rede UDR tem precedência sobre o ExpressRoute forçado túnel, que garante a saída acesso à Internet da Cache de Redis do Azure.

Ligar a uma instância da Cache de Redis do Azure a partir de uma aplicação no local com o ExpressRoute não é um cenário de utilização normal devido a razões de desempenho (para um melhor desempenho os clientes devem estar na mesma região que a Cache de Redis do Azure a Cache de Redis do Azure).

>[!IMPORTANT] 
>As rotas definidas num UDR **tem** ser específico o suficiente para têm precedência sobre qualquer rotas anunciadas na configuração do ExpressRoute. O exemplo seguinte utiliza o intervalo de endereços 0.0.0.0/0 abrangente e como tal, pode potencialmente ser acidentalmente substituído pelo anúncios de rota utilizando intervalos de endereços mais específicos.

>[!WARNING]  
>Cache de Redis do Azure não é suportado com configurações de ExpressRoute que **incorretamente cross-anunciar rotas a partir do caminho de peering público para o caminho de peering privado**. Configurações de ExpressRoute que tenham o peering público configurado, receber anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se estes intervalos de endereços são incorretamente cross-anunciados no caminho de peering privado, o resultado é que todos os pacotes de rede de saída da sub-rede a instância Cache de Redis do Azure estão em incorretamente impor o túnel à infraestrutura de rede no local do cliente. Este fluxo de rede de quebras de Cache de Redis do Azure. A solução para este problema está a parar as rotas entre publicidade do caminho de peering público para o caminho de peering privado.


Informações de fundo sobre rotas definidas pelo utilizador estão disponíveis neste [descrição geral](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o ExpressRoute, consulte [descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar as funcionalidades de cache do mais premium.

* [Introdução ao escalão Premium de Cache de Redis do Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

