<properties 
   pageTitle="Topologias da ligação do VPN Gateway | Microsoft Azure"
   description="Veja as topologias da ligação do VPN Gateway, as ferramentas de configuração disponíveis e os modelos de implementação."
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
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Topologias da ligação do VPN Gateway do Azure

Este artigo mostra as topologias da ligação do gateway de VPN da linha de base. Pode utilizar os gráficos e as descrições para ajudar a selecionar a topologia de configuração para corresponder aos seus requisitos. Apesar deste artigo abordar as principais topologias de linha de base, é possível criar topologias mais complexas utilizando os diagramas como orientação.

Cada topologia contém uma tabela que lista o modelo de implementação para o qual a topologia está disponível, as ferramentas de implementação que pode utilizar para configurar cada topologia e um link direto para um artigo, se existir. Atualizamos as tabelas com frequência, à medida que novos artigos e ferramentas de implementação ficam disponíveis para utilização.

Se pretender obter mais informações sobre os gateways de VPN, veja [Acerca dos VPN Gateways](vpn-gateway-about-vpngateways.md).



## Site a Site e Multilocal

Uma ligação Site a Site é uma ligação através do túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação precisa dum dispositivo VPN ou Windows Server RRAS no local. As ligações site a site podem ser utilizadas para configurações em vários locais e híbridas.   


**Diagrama S2S**

![Ligação S2S](./media/vpn-gateway-topology/site2site.png "site-to-site")

Pode criar e configurar mais de uma ligação VPN S2S para as suas redes no local se estiver a utilizar VPNs Baseadas na Rota do Azure. Este tipo de configuração é frequentemente designado ligação “multilocal”.
 

**Diagrama Multilocal**

![Ligação multilocal](./media/vpn-gateway-topology/multisite.png "multi-site")


**Métodos e modelos de implementação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## VNet a VNet

A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é muito semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN do Azure para fornecer um túnel seguro através de IPsec/IKE. As VNets que liga podem ser em regiões diferentes ou em diferentes subscrições. Pode, inclusive, combinar uma comunicação VNet a VNet com configurações multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

Atualmente, o Azure tem dois modelos de implementação: a Gestão do Serviço do Azure (referida como clássica) e o Azure Resource Manager. Se utiliza o Azure há algum tempo, é provável que tenha VMs do Azure e funções de instância em execução numa VNet clássica, enquanto as VMs mais recentes e as instâncias de função podem estar em execução numa VNet criada no ARM. Pode criar uma ligação entre redes virtuais, mesmo que se encontrem em modelos de implementação diferentes.


**Diagrama VNet2VNet**

![Ligação VNet a VNet](./media/vpn-gateway-topology/vnet2vnet.png "vnet-to-vnet")


**Métodos e modelos de implementação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 



## Ligações Site a Site e ExpressRoute coexistentes

O ExpressRoute é uma ligação dedicada direta aos Serviços Microsoft, incluindo ao Azure, a partir da WAN e não através da Internet pública. O tráfego da Rede de VPNs circula de forma encriptada através da Internet pública. Ter a capacidade de configurar ligações ExpressRoute e de Rede de VPNs para a mesma rede virtual tem várias vantagens. Pode configurar a Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que não fazem parte da sua rede, mas que se encontram ligados através do ExpressRoute. 


**Diagrama de ligações coexistentes**

![Ligação coexistente](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**Métodos e modelos de implementação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## Ponto a Site

Uma configuração Ponto a Site permite-lhe criar uma ligação segura para a sua rede virtual a partir de um computador cliente, individualmente. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. Esta é uma excelente solução quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. 

Uma ligação Ponto a Site é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol). As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. 

**Diagrama P2S**

![Ligação Site a Site](./media/vpn-gateway-topology/point2site.png "point-to-site")

**Métodos e modelos de implementação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## Passos seguintes

Deverá familiarizar-se com os itens nos artigos [Acerca dos VPN Gateways](vpn-gateway-about-vpngateways.md) e [FAQs do VPN Gateway](vpn-gateway-vpn-faq.md) para melhor compreender os gateways de VPN antes de prosseguir com o planeamento e a conceção da ligação.





 



<!--HONumber=Jun16_HO2-->


