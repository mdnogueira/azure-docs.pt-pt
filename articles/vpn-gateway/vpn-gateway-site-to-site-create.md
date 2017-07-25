---
title: "Ligar a rede no local a uma rede virtual do Azure: Rede de VPNs: Portal Clássico | Microsoft Docs"
description: "Passos para criar uma ligação IPsec da sua rede no local a uma rede virtual do Azure através da Internet pública. Estes passos ajudam-no a criar uma ligação de Gateway de Rede de VPNs em vários sites com o portal Clássico e o modelo de implementação clássica."
services: vpn-gateway
documentationcenter: 
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 024ecb29-64de-4ff1-84f1-1a45a8595f0b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 04cf35d89937cd0e6aee03ed8b6db3ff79ce1819
ms.contentlocale: pt-pt
ms.lasthandoff: 07/17/2017

---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-classic-portal-classic"></a>Criar uma VNet com uma ligação de Rede de VPNs no portal clássico (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra-lhe como utilizar o portal clássico para criar uma ligação de gateway de VPN de Site para Site a partir da sua rede no local para a VNet. Os passos neste artigo aplicam-se ao modelo de implementação clássica. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Portal clássico (clássico)](vpn-gateway-site-to-site-create.md)
> 
>

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-site-to-site-create/site-to-site-connection-diagram.png)


Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

#### <a name="additional-configurations"></a>Configurações adicionais

Se pretender ligar VNets entre si, veja [Configurar uma ligação VNet a VNet para o modelo de implementação clássica](virtual-networks-configure-vnet-to-vnet-connection.md). Se pretender adicionar uma ligação Site a Site a uma VNet que já tem uma ligação, veja [Adicionar uma ligação S2S a uma VNet com uma ligação de gateway de VPN existente](vpn-gateway-multi-site.md).
## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Antes de iniciar a configuração, verifique se tem os seguintes itens:

* Confirme que pretende trabalhar com o modelo de implementação clássica. Se quiser trabalhar com o modelo de implementação Resource Manager, veja [Criar uma ligação Site a Site (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Quando possível, recomendamos que utilize o modelo de implementação Resource Manager.
* Certifique-se de que tem um dispositivo VPN compatível e alguém que o possa configurar. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Verifique se tem um endereço IP IPv4 público com acesso exterior para o seu dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar.

## <a name="CreateVNet"></a>Criar a rede virtual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Para criar a VNet, introduza as suas definições de configuração nas seguintes páginas:

## <a name="Details"></a>Página de detalhes da rede virtual
Introduza as seguintes informações:

* **Nome**: atribua um nome à rede virtual. Por exemplo, *EastUSVNet*. Utilizará este nome da rede virtual ao implementar as instâncias de VMs e PaaS, pelo que não deve escolher um nome demasiado complicado.
* **Localização**: a localização está diretamente relacionada com a localização física (região) onde pretende que os recursos (VMs) residam. Por exemplo, se pretender que as VMs que implementa nesta rede virtual estejam fisicamente localizadas nos *EUA Leste*, selecione essa localização. Não é possível alterar a região associada à rede virtual depois de a criar.

## <a name="DNS"></a>Página de conetividade VPN e servidores DNS
Introduza as seguintes informações e clique na seta na parte inferior direita.

* **Servidores DNS**: introduza o endereço IP e o nome do servidor DNS ou selecione um servidor DNS anteriormente registado no menu de atalho. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual.
* **Configurar Rede de VPNs**: selecione a caixa de verificação **Configurar uma rede de VPNs**.
* **Rede Local**: a rede local representa a sua localização física no local. Pode selecionar uma rede local criada anteriormente ou pode criar uma nova. No entanto, se optar por utilizar uma rede local criada anteriormente, aceda à página de configuração **Redes Locais** e certifique-se de que o endereço IP do Dispositivo VPN (endereço IPv4 destinado ao público) do dispositivo VPN está correto.

## <a name="Connectivity"></a>Página de conetividade site a site
Se estiver a criar uma nova rede local, veja a página **Conectividade Site a Site**. Se pretender utilizar uma rede local criada anteriormente, esta página não aparecerá no assistente e poderá avançar para a secção seguinte.

Introduza as seguintes informações e clique na seta seguinte.

* **Nome**: o nome do site de rede local (no local).
* **Endereço IP do Dispositivo VPN**: o endereço IPv4 destinado ao público do dispositivo VPN no local que utiliza para ligar ao Azure. O dispositivo VPN não pode estar localizado atrás de um NAT.
* **Espaço de Endereços**: inclua o IP Inicial e o CIDR (Contagem de Endereços). Especifique os intervalos de endereços que pretende que sejam enviados através do gateway da rede virtual para a localização local no local. Se um endereço IP de destino estiver dentro dos intervalos que especificar aqui, é encaminhado através do gateway da rede virtual.
* **Adicionar espaço de endereços**: se tiver vários intervalos de endereços que pretende que sejam enviados através do gateway da rede virtual, especifique cada intervalo de endereços adicional. Pode adicionar ou remover intervalos mais tarde na página **Rede Local**.

## <a name="Address"></a>Página de espaços de endereços da rede virtual
Especifique o intervalo de endereços que pretende utilizar para a rede virtual. Estes são os endereços IP dinâmicos (DIPS) que serão atribuídos às VMs e a outras instâncias de função que implementar nesta rede virtual.

É especialmente importante selecionar um intervalo que não se sobreponha a nenhum intervalo utilizado para a rede no local. Tem de se coordenar com o administrador de rede. O administrador de rede poderá ter de extrair um intervalo de endereços IP do seu espaço de endereços da rede no local para a utilização na sua rede virtual.

Introduza as seguintes informações e clique na marca de verificação na parte inferior direita para configurar a rede.

* **Espaço de Endereços**: inclua o IP Inicial e a Contagem de Endereços. Verifique se os espaços de endereços que especifica não se sobrepõem a qualquer um dos espaços de endereços que tem na rede no local.
* **Adicionar sub-rede**: inclua o IP Inicial e a Contagem de Endereços. Não são necessárias sub-redes adicionais, mas deve criar uma sub-rede separada para as VMs que terão DIPS estáticos. Em alternativa, pode ter as VMs numa sub-rede separada das outras instâncias de função.
* **Adicionar sub-rede do gateway**: clique para adicionar a sub-rede do gateway. A sub-rede do gateway é utilizada apenas para o gateway da rede virtual. Precisa dela para esta configuração.

Clique na marca de verificação na parte inferior da página para criar a rede virtual. Quando terminar, veja **Criado** listado em **Estado** na página **Redes** no Portal Clássico do Azure. Após criar a VNet, pode configurar o gateway da rede virtual.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="VNetGateway"></a>Configurar um gateway de rede virtual
Configure o gateway da rede virtual para criar uma ligação site a site segura. Veja [Configurar um gateway da rede virtual no Portal Clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Passos seguintes
 Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


