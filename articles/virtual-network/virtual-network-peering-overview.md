<properties
   pageTitle="Peering da Rede Virtual do Azure | Microsoft Azure"
   description="Saiba mais sobre VNet Peering no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="narayanannamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# VNet Peering

O VNet Peering é um mecanismo para ligar duas Redes Virtuais na mesma região através da rede principal do Azure. Uma vez executado o peering, as duas Redes Virtuais aparecerão como uma única para todos os fins de conectividade. Continuarão a ser geridas como recursos separados, mas as máquinas virtuais nestas VNets podem comunicar diretamente entre si através de um endereço IP privado. O tráfego entre Máquinas virtuais nas VNets em modo de peering será encaminhado através da Infraestrutura do Azure à semelhança do tráfego entre VMs na mesma VNet. Seguem-se algumas das vantagens da utilização de VNet Peering:

- Baixa latência, ligação de largura de banda alta entre recursos em VNets diferentes.
- Capacidade para utilizar recursos como aplicações virtuais de rede e gateways de VPN na VNet em modo de peering (Trânsito).
- Ligar a VNet do Resource Manager a uma VNet clássica e ativar a conectividade completa entre os recursos nestas VNets

Requisitos e aspetos fundamentais do VNet peering:

- As duas Redes Virtuais que são colocadas em modo de peering devem estar na mesma região do Azure
- As VNets que são colocadas em modo de peering devem ter espaços de Endereços IP não sobrepostos
- O VNet Peering é feito entre duas redes virtuais e não existe qualquer relação transitiva derivada, ou seja, se a VNet A for colocada em modo de peering com a VNet B e a VNet B for colocada em modo de peering com a VNet C, isso não significa que a VNet A é colocada em modo de peering com a VNet C
- O peering pode ser estabelecido entre Redes virtuais em duas subscrições diferentes desde que um utilizador com privilégios das respetivas subscrições autorize o peering
- Uma VNet do Resource Manager pode ser colocada em modo de peering com outra VNet do Resource Manager ou com uma VNet clássica, mas não é possível colocar duas VNets clássicas em modo de peering
- Apesar de a comunicação entre as Máquinas virtuais em Vnets em modo de peering não ter restrições de largura de banda adicionais, continua a aplicar-se o limite de largura de banda baseado no tamanho da VM. 


![Bases do VNet Peering](./media/virtual-networks-peering-overview/figure01.png)

## Conectividade 
Depois de duas VNets serem colocadas em modo de peering, uma máquina virtual (função da Web/de trabalho) na Vnet pode ligar-se diretamente a outras máquinas virtuais na VNet em modo de peering. Terão conectividade completa a nível de IP. A latência de rede para efetuar a viagem de ida e volta entre duas Máquinas virtuais nas Vnets em modo de peering será a mesma que dentro da VNet local. O débito da rede será baseado na largura de banda permitida para a Máquina virtual, proporcional ao seu tamanho, e não haverá qualquer restrição adicional à largura de banda permitida. O tráfego entre as Máquinas virtuais nas Vnets em modo de peering é encaminhado diretamente através da infraestrutura de back-end do Azure e não através de um gateway.

As Máquinas virtuais numa VNet poderão aceder aos Pontos finais com balanceamento de carga interno (ILB) na VNet em modo de peering. Os Grupos de Segurança da Rede podem ser aplicados a qualquer das Vnets para bloquear o acesso à outra Vnet ou sub-rede, se assim o desejar. Quando o utilizador configura o peering pode optar por abrir ou fechar as regras do Grupo de Segurança de Rede entre as VNets. Se o utilizador optar por uma conectividade totalmente aberta entre as VNets em modo de peering (opção predefinida), pode depois utilizar NSGs em sub-redes ou Máquinas virtuais específicas para bloquear ou negar o acesso específico.

A resolução de nomes DNS internos fornecida pelo Azure para Máquinas virtuais não funcionará em VNets em modo de peering. As Máquinas virtuais terão nomes DNS internos resolvíveis apenas dentro da Rede Virtual local. No entanto, os utilizadores podem configurar Máquinas virtuais em execução em Vnets em modo de peering como servidores DNS para uma Rede virtual. 

## Encadeamento de Serviços
Os utilizadores podem configurar tabelas de rota definidas pelo utilizador que apontam para Máquinas virtuais em Vnets em modo de peering como salto seguinte (como se mostra no diagrama abaixo). Isto permite aos utilizadores obter encadeamento de serviços, através do qual podem direcionar o tráfego de uma VNet para uma Aplicação virtual em execução numa VNet em modo de peering através de tabelas de rota definidas pelo utilizador. Os utilizadores podem também criar eficazmente ambientes do tipo Hub-and-spoke onde o Hub pode alojar o componente de infraestrutura como a aplicação Virtual de Rede e todas as VNets spoke podem ser colocadas em modo de peering com ele e direcionar um subconjunto de tráfego para aplicações em execução na VNet hub. Resumindo, o VNet peering permite que o Endereço IP do salto seguinte na “Tabela de rota definida pelo utilizador” seja o de uma máquina virtual na VNet em modo de peering.

## Gateways e Conectividade no local
Cada Rede Virtual, independentemente de estar ou não em modo de peering com outra Vnet, continua a poder ter o seu próprio gateway e a utilizá-lo para se ligar no local. Os utilizadores também podem configurar uma ligação VNet a VNet (fornecer ligação) através de gateways, apesar de as VNets estarem em modo de peering. Quando ambas as opções de conectividade entre VNets estiverem configuradas, o tráfego entre as VNets irá fluir através da configuração de peering (ou seja, através da estrutura principal do Azure). 

Quando as VNets estão em modo de peering, os utilizadores também podem configurar para utilizar o gateway na Vnet em modo de peering como um ponto de trânsito para o local. Neste caso, a VNet que está a utilizar um gateway remoto não pode ter um gateway próprio, ou seja, uma VNet pode ter apenas um gateway. Este pode ser um gateway local ou um gateway remoto (na Vnet em modo de peering), como se mostra na imagem abaixo. O Trânsito do Gateway não é suportado entre uma Vnet do Resource Manager e uma Vnet clássica. Ambas as Vnets na relação de peering devem ser Vnets do Resource Manager para que o trânsito do gateway funcione.
Quando as VNets que partilham um único circuito de ER estão em modo de peering, o tráfego entre as mesmas irá passar pela relação de peering (ou seja, pela rede principal do Azure). Os utilizadores podem continuar a utilizar gateways locais em cada Vnet estabelecer ligação ao circuito no local ou utilizar um gateway partilhado e configurar trânsito para conectividade no local.

![Trânsito do VNet Peering](./media/virtual-networks-peering-overview/figure02.png)

## Aprovisionamento
O VNet Peering é uma operação com privilégios. É uma função separada sob o espaço de nomes Rede Virtual. Podem ser concedidos direitos específicos a um utilizador para autorizar o peering. Um utilizador que tem acesso de leitura/escrita na VNet herdá-los-á automaticamente. Um utilizador que seja um administrador ou um utilizador com privilégios da capacidade de peering pode iniciar uma operação de peering para outra VNet. Se existir um pedido de correspondência para peering no outro lado e forem cumpridos outros requisitos, o peering será estabelecido. 

Consulte os artigos de Procedimentos para saber mais sobre como estabelecer VNet peering entre duas Redes Virtuais.

## Limites
Existem limites sobre o número de peerings permitidos para uma única Rede virtual. Veja [Limites de Redes do Azure](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## Preços
O VNet Peering não será cobrado durante o período de avaliação. Uma vez lançado para Disponibilidade Geral, haverá uma cobrança nominal sobre o tráfego de entrada e saída que utiliza o peering. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network) 


## Passos seguintes
- [Configuração de peering entre Redes Virtuais](virtual-networks-create-vnetpeering-arm-portal.md).
- Saiba mais sobre [NSGs](virtual-networks-nsg.md).
- Saiba mais sobre [rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).



<!--HONumber=Aug16_HO1-->


