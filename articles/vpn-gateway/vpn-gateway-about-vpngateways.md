<properties 
   pageTitle="Acerca do VPN Gateway do Azure | Microsoft Azure"
   description="Saiba mais sobre as ligações do Gateway de VPN das Redes Virtuais do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Acerca do VPN Gateway


O Gateway de VPN é uma coleção de recursos utilizada para enviar tráfego de rede entre redes virtuais e localizações no local. Os gateways são utilizados para ligações Site a Site, Ponto a Site e ExpressRoute. O Gateway de VPN também é utilizado para enviar tráfego entre várias redes virtuais no Azure (VNet a VNet). 

Para criar uma ligação, adicione um gateway de rede virtual a uma VNet e configure os recursos adicionais do Gateway de VPN e respetivas definições. Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute.

Para obter informações sobre os requisitos de gateways, veja [Requisitos de Gateway](vpn-gateway-about-vpn-gateway-settings.md#requirements). Para ver o débito agregado estimado, veja [About VPN Gateway Settings (Acerca das Definições do Gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md#aggthroughput). Para ver os preços, veja [Preços de Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway). Para saber os limites das subscrições e do serviço, veja [Networking Limits (Limites de rede)](../articles/azure-subscription-service-limits.md#networking-limits).

Ao configurar um Gateway de VPN, as instruções que utilizar dependem do modelo de implementação que utilizou para criar a rede virtual. Por exemplo, se tiver criado a VNet com o modelo de implementação clássica, utilize as diretrizes e as instruções do modelo de implementação clássica para criar e configurar as definições do gateway de VPN. Para obter mais informações, veja [Compreender os modelos de implementação clássica e Resource Manager](../resource-manager-deployment-model.md).

As secções abaixo contêm tabelas que listam as seguintes informações para a configuração:

- modelos de implementação disponíveis
- ferramentas de configuração disponíveis
- ligações que o levam diretamente para um artigo, se disponível


Utilize os diagramas e as descrições para ajudar a selecionar a topologia de configuração para corresponder aos seus requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar configurações mais complexas utilizando os diagramas como orientação. Cada configuração baseia-se nas definições do Gateway de VPN que selecionar.

### Configurar as definições do Gateway de VPN

Uma vez que o Gateway de VPN é uma coleção de recursos, pode configurar alguns dos recursos com uma ferramenta e, em seguida, mudar para outra para configurar outras definições de recursos. Atualmente, não pode configurar todos os recursos do gateway de VPN no portal do Azure. As instruções nos artigos de cada configuração especificam se é necessária uma ferramenta específica. Se estiver a trabalhar com o modelo de implementação clássica, poderá trabalhar no portal clássico ou utilizar o PowerShell neste momento. Para obter informações sobre as definições individuais disponíveis, consulte [Definições do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).



## Site a Site e Multilocal

### Site a Site

Uma ligação Site a Site (S2S) é uma ligação através do túnel VPN IPsec/IKE (S2S ou IKEv1). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público atribuído ao mesmo e não se encontre protegido por um NAT. As ligações S2S podem ser utilizadas para configurações em vários locais e híbridas.   

![Ligação S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")


### Multilocal

Pode criar e configurar uma ligação VPN entre a sua VNet e várias redes locais. Quando trabalha com várias ligações, tem de utilizar um tipo de VPN baseado em rota (gateway dinâmico para VNets clássicas). Uma vez que uma VNet só pode ter um gateway de rede virtual, todas as ligações do gateway partilham a largura de banda disponível. Este tipo de configuração é frequentemente designado ligação “multilocal”.
 

![Ligação multilocal](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### Métodos e modelos de implementação

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## VNet a VNet

A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN do Azure para fornecer um túnel seguro através de IPsec/IKE. Pode, inclusive, combinar uma comunicação VNet a VNet com configurações multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

As VNets que liga podem estar:

- nas mesmas regiões ou em diferentes
- nas mesmas subscrições ou em diferentes 
- nos mesmos modelos de implementação ou em diferentes



![Ligação VNet a VNet](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")



### Ligações entre modelos de implementação

Atualmente, o Azure tem dois modelos de implementação: clássico e Resource Manager. Se tem utilizado o Azure durante algum tempo, provavelmente as VMs do Azure e as funções de instância estão em execução numa VNet clássica. As VMs mais recentes e as instâncias da função poderão estar em execução numa VNet criada no Resource Manager. Pode criar uma ligação entre as VNets para permitir que os recursos numa VNet comuniquem diretamente com recursos de outra.


### Métodos e modelos de implementação

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

### VNet peering

Poderá utilizar o VNet peering para criar a ligação, desde que a configuração da rede virtual cumpra determinados requisitos. O VNet peering não utiliza um gateway de rede virtual. O [VNet peering](../virtual-network/virtual-network-peering-overview.md) está atualmente em Pré-visualização.



## Ponto a Site

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura para a sua rede virtual a partir de um computador cliente individual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol). As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. O utilizador estabelece a ligação VPN iniciando-a a partir do computador cliente. Esta é uma excelente solução quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma VNet. 


![Ligação Site a Site](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### Métodos e modelos de implementação

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Para obter mais informações sobre o ExpressRoute, veja a [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).


## Ligações Site a Site e ExpressRoute coexistentes

O ExpressRoute é uma ligação dedicada direta a partir da WAN (e não através da Internet pública) aos Serviços Microsoft, incluindo ao Azure. O tráfego da Rede de VPNs circula de forma encriptada através da Internet pública. Poder configurar ligações ExpressRoute e de Rede de VPNs para a mesma rede virtual tem várias vantagens.

Pode configurar uma Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que não fazem parte da sua rede, mas que se encontram ligados através do ExpressRoute. Esta configuração requer dois gateways de rede virtual para a mesma rede virtual, um que utilize -GatewayType Vpn e o outro -GatewayType ExpressRoute.


![Ligação coexistente](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### Métodos e modelos de implementação

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## Passos seguintes

Consulte as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações sobre os Gateways de VPN

Ligue a sua localização no local a uma VNet. Consulte [Criar uma Ligação Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).





 



<!--HONumber=ago16_HO5-->


