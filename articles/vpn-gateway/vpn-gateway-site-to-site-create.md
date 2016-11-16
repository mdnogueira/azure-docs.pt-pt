---
title: "Criar uma rede virtual com uma ligação de Gateway de Rede de VPNs através do portal clássico do Azure | Microsoft Docs"
description: "Crie uma VNet com uma ligação do VPN Gateway S2S para configurações híbridas e em vários locais através do modelo de implementação clássica."
services: vpn-gateway
documentationcenter: 
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 024ecb29-64de-4ff1-84f1-1a45a8595f0b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc377f80fa6b9df41081b13a51ff15482b2ba8bc


---
# <a name="create-a-vnet-with-a-sitetosite-connection-using-the-azure-classic-portal"></a>Criar uma VNet com uma ligação de Rede de VPNs no portal clássico do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clássica – Portal Clássico](vpn-gateway-site-to-site-create.md)
> 
> 

Este artigo explica-lhe como criar uma rede virtual e uma ligação de gateway de Rede de VPNs para a sua rede no local com o modelo de implementação clássica e o portal clássico. As ligações site a site podem ser utilizadas para configurações em vários locais e híbridas.

![Diagrama Site a Site](./media/vpn-gateway-site-to-site-create/site2site.png "site-to-site")

### <a name="deployment-models-and-methods-for-sitetosite-connections"></a>Modelos de implementação e métodos para ligações Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos e métodos de implementação atualmente disponíveis para configurações Site a Site. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se pretender ligar VNets entre si, veja [Configurar uma ligação VNet a VNet para o modelo de implementação clássica](virtual-networks-configure-vnet-to-vnet-connection.md). Se pretender adicionar uma ligação Site a Site a uma VNet que já tem uma ligação, veja [Adicionar uma ligação S2S a uma VNet com uma ligação de gateway de VPN existente](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Um dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="a-namecreatevnetacreate-your-virtual-network"></a><a name="CreateVNet"></a>Criar a rede virtual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Para criar a VNet, introduza as suas definições de configuração nas seguintes páginas:

## <a name="a-namedetailsavirtual-network-details-page"></a><a name="Details"></a>Página de detalhes da rede virtual
Introduza as seguintes informações:

* **Nome**: atribua um nome à rede virtual. Por exemplo, *EastUSVNet*. Utilizará este nome da rede virtual ao implementar as instâncias de VMs e PaaS, pelo que não deve escolher um nome demasiado complicado.
* **Localização**: a localização está diretamente relacionada com a localização física (região) onde pretende que os recursos (VMs) residam. Por exemplo, se pretender que as VMs que implementa nesta rede virtual estejam fisicamente localizadas nos *EUA Leste*, selecione essa localização. Não é possível alterar a região associada à rede virtual depois de a criar.

## <a name="a-namednsadns-servers-and-vpn-connectivity-page"></a><a name="DNS"></a>Página de conetividade VPN e servidores DNS
Introduza as seguintes informações e clique na seta na parte inferior direita.

* **Servidores DNS**: introduza o endereço IP e o nome do servidor DNS ou selecione um servidor DNS anteriormente registado no menu de atalho. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual.
* **Configurar Rede de VPNs**: selecione a caixa de verificação **Configurar uma rede de VPNs**.
* **Rede Local**: a rede local representa a sua localização física no local. Pode selecionar uma rede local criada anteriormente ou pode criar uma nova. No entanto, se optar por utilizar uma rede local criada anteriormente, aceda à página de configuração **Redes Locais** e certifique-se de que o endereço IP do Dispositivo VPN (endereço IPv4 destinado ao público) do dispositivo VPN está correto.

## <a name="a-nameconnectivityasitetosite-connectivity-page"></a><a name="Connectivity"></a>Página de conetividade site a site
Se estiver a criar uma nova rede local, verá a página **Conetividade Site a Site**. Se pretender utilizar uma rede local criada anteriormente, esta página não aparecerá no assistente e poderá avançar para a secção seguinte.

Introduza as seguintes informações e clique na seta seguinte.

* **Nome**: o nome do site de rede local (no local).
* **Endereço IP do Dispositivo VPN**: o endereço IPv4 destinado ao público do dispositivo VPN no local que utiliza para ligar ao Azure. O dispositivo VPN não pode estar localizado atrás de um NAT.
* **Espaço de Endereços**: inclua o IP Inicial e o CIDR (Contagem de Endereços). Especifique os intervalos de endereços que pretende que sejam enviados através do gateway da rede virtual para a localização local no local. Se um endereço IP de destino estiver dentro dos intervalos que especificar aqui, é encaminhado através do gateway da rede virtual.
* **Adicionar espaço de endereços**: se tiver vários intervalos de endereços que pretende que sejam enviados através do gateway da rede virtual, especifique cada intervalo de endereços adicional. Pode adicionar ou remover intervalos mais tarde na página **Rede Local**.

## <a name="a-nameaddressavirtual-network-address-spaces-page"></a><a name="Address"></a>Página de espaços de endereços da rede virtual
Especifique o intervalo de endereços que pretende utilizar para a rede virtual. Estes são os endereços IP dinâmicos (DIPS) que serão atribuídos às VMs e a outras instâncias de função que implementar nesta rede virtual.

É especialmente importante selecionar um intervalo que não se sobreponha a nenhum intervalo utilizado para a rede no local. Tem de se coordenar com o administrador de rede. O administrador de rede poderá ter de extrair um intervalo de endereços IP do seu espaço de endereços da rede no local para a utilização na sua rede virtual.

Introduza as seguintes informações e clique na marca de verificação na parte inferior direita para configurar a rede.

* **Espaço de Endereços**: inclua o IP Inicial e a Contagem de Endereços. Verifique se os espaços de endereços que especifica não se sobrepõem a qualquer um dos espaços de endereços que tem na rede no local.
* **Adicionar sub-rede**: inclua o IP Inicial e a Contagem de Endereços. Não são necessárias sub-redes adicionais, mas deve criar uma sub-rede separada para as VMs que terão DIPS estáticos. Em alternativa, pode ter as VMs numa sub-rede separada das outras instâncias de função.
* **Adicionar sub-rede do gateway**: clique para adicionar a sub-rede do gateway. A sub-rede do gateway é utilizada apenas para o gateway da rede virtual. Precisa dela para esta configuração.

Clique na marca de verificação na parte inferior da página para a rede virtual começar a ser criada. Quando terminar, verá **Criado** listado em **Estado** na página **Redes** no Portal Clássico do Azure. Após criar a VNet, pode configurar o gateway da rede virtual.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="a-namevnetgatewayaconfigure-your-virtual-network-gateway"></a><a name="VNetGateway"></a>Configurar um gateway de rede virtual
Configure o gateway da rede virtual para criar uma ligação site a site segura. Veja [Configurar um gateway da rede virtual no Portal Clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja a documentação das [Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.




<!--HONumber=Nov16_HO2-->


