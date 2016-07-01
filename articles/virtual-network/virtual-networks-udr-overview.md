<properties 
   pageTitle="O que são Rotas Definidas pelo Utilizador e Reencaminhamento IP?"
   description="Saiba como utilizar Rotas Definidas pelo Utilizador (UDR) e Reencaminhamento IP para reencaminhar tráfego para aplicações virtuais de rede no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# O que são Rotas Definidas pelo Utilizador e Reencaminhamento IP?
Quando adiciona máquinas virtuais (VM) a uma rede virtual (VNet) no Azure, repara que as VM conseguem comunicar entre si através da rede, automaticamente. Não é necessário especificar um gateway, apesar das VM estarem em sub-redes diferentes. O mesmo se verifica para a comunicação entre as VM e a Internet pública, e até mesmo na sua rede no local quando está presente uma ligação híbrida a partir do Azure para o seu centro de dados.

Este fluxo de comunicação é possível porque o Azure utiliza uma série de rotas de sistema para definir como flui o tráfego IP. As rotas de sistema controlam o fluxo de comunicação nos seguintes cenários:

- A partir da mesma sub-rede.
- A partir de uma sub-rede para outra numa VNet.
- A partir das VM para a Internet.
- A partir de uma VNet para outra VNet através de um gateway VPN.
- A partir de um VNet para a sua rede no local através de um gateway VPN.

A figura abaixo mostra uma configuração simples com uma VNet, duas sub-redes e algumas VMs, em conjunto com as rotas de sistema que permitem o fluxo do tráfego IP.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure1.png)

Embora a utilização das rotas de sistema facilita o tráfego automaticamente para a implementação, há casos em que pretende controlar o encaminhamento de pacotes através de uma aplicação virtual. Pode fazê-lo ao criar rotas definidas pelo utilizador que especificam o salto seguinte para os pacotes que fluem para uma sub-rede específica para aceder à sua aplicação virtual disso e ao ativar o reencaminhamento IP para a VM que funciona como aplicação virtual.

A figura abaixo mostra um exemplo de rotas definidas pelo utilizador e reencaminhamento IP para forçar a passagem de pacotes enviados para uma sub-rede de outra através de um dispositivo virtual numa sub-rede terceira.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] As rotas definidas pelo utilizador só se aplicadas ao tráfego que sai de uma sub-rede. Não é possível criar rotas para especificar como o tráfego tem acesso a uma sub-rede a partir da Internet, para a instância. Além disso, o dispositivo para o qual está a reencaminhar tráfego não pode estar na mesma sub-rede de origem do tráfego. Crie sempre uma sub-rede separada para os dispositivos. 

## Recurso da rota
Os pacotes são reencaminhados através de uma rede TCP/IP com base numa tabela de rota definida em cada nó na rede física. Uma tabela de rota é uma coleção de rotas individuais utilizada para decidir onde pretende reencaminhar os pacotes com base no endereço IP de destino. Uma rota consiste no seguinte:

|Propriedade|Descrição|Restrições|Considerações|
|---|---|---|---|
| Prefixo do endereço | O CIDR de destino aos quais se aplica a rota, por exemplo, 10.1.0.0/16.|Tem de ser um intervalo CIDR válido que represente endereços na Internet pública, rede virtual do Azure ou centro de dados no local.|Certifique-se de que o **Prefixo do endereço** não contém o endereço para o **Valor Nexthop**, caso contrário os pacotes são introduzidos num ciclo que vai da origem para o salto seguinte sem chegarem ao destino. |
| Tipo de salto seguinte | O tipo de salto Azure para o qual o pacote deve ser enviado. | Tem de ser um dos seguintes valores: <br/> **Local**. Representa a rede virtual local. Por exemplo, se tiver duas sub-redes, 10.1.0.0/16 e 10.2.0.0/16 na mesma rede virtual, a rota de cada sub-rede na tabela de rota terá um valor de salto seguinte do *Local*. <br/> **VPN Gateway**. Representa um VPN Gateway S2S do Azure. <br/> **Internet**. Representa o gateway de Internet predefinido fornecido pela infraestrutura do Azure. <br/> **Aplicação Virtual**. Representa uma aplicação virtual que adicionou à sua rede virtual do Azure. <br/> **NULO**. Representa um buraco negro. Os pacotes reencaminhados para um buraco negro não são reencaminhados de modo algum.| Considere a utilização de um tipo **NULO** para impedir o fluxo para um destino indicado. | 
| Valor de Nexthop | O valor de salto seguinte contém os endereços IP para os quais os pacotes devem ser reencaminhados. Os valores de salto seguintes só são permitidos em rotas onde está o tipo de salto seguinte é *Aplicação Virtual*.| Tem de ser um endereço IP acessível. | Se o endereço IP representa uma VM, certifique-se de que ativa [reencaminhamento IP](#IP-forwarding) no Azure para a VM. |

### Rotas de sistema
Cada sub-rede criada numa rede virtual é associada automaticamente a uma tabela de rota que contém as seguintes regras de rota de sistema:

- **Regra de Vnet Local**: esta regra é criada automaticamente para cada sub-rede numa rede virtual. Especifica que existe uma ligação direta entre as VM na VNet e não existe qualquer passo seguinte intermédio.
- **Regra no local**: esta regra aplica-se a todo o tráfego destinado ao intervalo de endereços no local e utiliza o gateway VPN como destino de salto seguinte.
- **Regra de Internet**: esta regra processa todo o tráfego destinado à Internet pública e utiliza destinado a gateway para a Internet da infraestrutura como salto seguinte para todo o tráfego à Internet.

### Rotas Definidas pelo Utilizador
Para a maior parte dos ambientes, só necessita das rotas de sistema já definidas pelo Azure. No entanto, poderá ter de criar uma tabela de rota e adicionar um ou mais rotas em casos específicos, tais como:

- Forçar o túnel à Internet através da rede no local.
- Utilização de aparelhos virtuais no seu ambiente do Azure.

Nos cenários acima, terá de criar uma tabela de rota e adicionar-lhe rotas definidas pelo utilizador. Pode ter várias tabelas de rota e a mesma tabela de rota pode ser associada a uma ou mais sub-redes. Além disso, cada sub-rede apenas pode ser associada a uma tabela de rota única. Todas as VM e serviços em nuvem numa sub-rede utilizam a tabela de rota associada a essa sub-rede.

As sub-redes dependem de rotas de sistema até uma tabela de rota ser associada à sub-rede. Quando existe uma associação, o encaminhamento é efetuado com base na Maior Correspondência de Prefixo (LPM) entre ambas as rotas de sistema e as rotas definidas pelo utilizador. Se houver mais de uma rota com a mesma correspondência LPM, uma rota é selecionada com base na respetiva origem pela seguinte ordem:

1. Rota definida pelo utilizador
1. Rota BGP (quando é utilizado o ExpressRoute)
1. Rota de sistema

Para saber como criar rotas definidas pelo utilizador, consulte o artigo [Como Criar Rotas e Permitir o Reencaminhamento IP no Azure](virtual-networks-udr-how-to.md#How-to-manage-routes).

>[AZURE.IMPORTANT] As rotas definidas pelo utilizador só são aplicadas às VM do Azure e aos serviços em nuvem. Por exemplo, se pretender adicionar uma aplicação virtual de firewall entre a rede no local e o Azure, terá de criar uma rota definida pelo utilizador para as suas tabelas de rota Azure que reencaminham todo o tráfego destinado ao espaço de endereço no local para a aplicação virtual. No entanto, o tráfego de entrada do espaço de endereço no local irá fluir pelo gateway VPN ou do circuito ExpressRoute diretamente para o ambiente do Azure, ignorando a aplicação virtual.

### Rotas BGP
Se tiver uma ligação ExpressRoute entre a rede no local e o Azure, pode ativar a BGP para propagar rotas da sua rede no local para o Azure. Estes rotas BGP são utilizadas da mesma forma que as rotas de sistema e as rotas definidas pelo utilizador em cada sub-rede do Azure. Para obter mais informações, consulte [Introdução ao ExpressRoute](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Pode configurar o seu ambiente do Azure para utilizar imposição de túnel através da rede no local ao criar uma rota definida pelo utilizador para a sub-rede 0.0.0.0/0 que utiliza o gateway VPN como salto seguinte. No entanto, isto só funciona se utilizar um gateway VPN, não o ExpressRoute. Para o ExpressRoute, a imposição de protocolo é configurada através da BGP.

## Reencaminhamento IP
Como descrito acima, uma das principais razões para criar uma rota definido de utilizador é reencaminhar tráfego para uma aplicação virtual. Uma aplicação virtual não é mais do que uma VM que executa uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT.

Este VM de aplicação virtual deve ser capaz de receber tráfego de entrada que não esteja endereçada a si mesma. Para permitir que uma VM receba tráfego endereçado a outros destinos, tem de ativar o Reencaminhamento IP para a VM. Isto é uma definição do Azure, não se trata de uma definição no sistema operativo convidado.

## Passos Seguintes

- Saiba como [criar rotas no modelo de implementação do Gestor de recursos](virtual-network-create-udr-arm-template.md) e associá-las a sub-redes. 
- Saiba como [criar rotas no modelo de implementação clássico](virtual-network-create-udr-classic-ps.md) e associá-las a sub-redes.



<!--HONumber=Jun16_HO2-->


