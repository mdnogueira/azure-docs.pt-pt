---
title: "Ligar a rede no local a uma rede virtual do Azure: Rede de VPNs (clássico): Portal | Microsoft Docs"
description: "Passos para criar uma ligação IPsec da sua rede no local a uma rede virtual do Azure através da Internet pública. Estes passos ajudam-no a criar uma ligação de Gateway de Rede de VPNs em vários sites com o portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 619ea430b13c16e8e4338413613d5798f36458ba
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Criar uma ligação de Site a Site com o portal do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clássica – Portal Clássico](vpn-gateway-site-to-site-create.md)
>
>


Uma ligação de gateway de VP de Site a Site (S2S) é uma ligação através do túnel VPN IPsec/IKE (S2S ou IKEv1). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público atribuído ao mesmo e não se encontre protegido por um NAT. As ligações site a site podem ser utilizadas para configurações em vários locais e híbridas.

Este artigo explica-lhe como criar uma rede virtual e uma ligação de gateway de Rede de VPNs na sua rede no local com o modelo de implementação clássica e o portal do Azure. 

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementação e métodos para ligações Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos e métodos de implementação atualmente disponíveis para configurações Site a Site. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se pretender ligar VNets entre si, mas não estiver a criar uma ligação para uma localização no local, veja [Configurar uma ligação VNet a VNet](virtual-networks-configure-vnet-to-vnet-connection.md). Se pretender adicionar uma ligação Site a Site a uma VNet que já tem uma ligação, veja [Adicionar uma ligação S2S a uma VNet com uma ligação de gateway de VPN existente](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens:

* Um dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Um endereço IP IPv4 público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](http://azure.microsoft.com/pricing/free-trial).
* Atualmente, o PowerShell é necessário para especificar a chave partilhada e criar a ligação de gateway de VPN. Instale a versão mais recente dos cmdlets do PowerShell da Gestão de Serviço (SM) do Azure. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs). Ao trabalhar com o PowerShell para esta configuração, certifique-se de que está a executar como administrador. 

> [!NOTE]
> Ao configurar uma ligação Site a Site, é preciso um endereço IP IPv4 destinado ao público para o dispositivo VPN.                                                                                                                                                                               
>
>

### <a name="values"></a>Valores de configuração de exemplo para este exercício
Quando utiliza estes passos como um exercício, pode utilizar os valores de configuração de exemplo:

* **Nome da VNet:** TestVNet1
* **Espaço de Endereços:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (opcional para este exercício)
* **Sub-redes:**
  * Front-End: 10.11.0.0/24
  * Back-End: 10.12.0.0/24 (opcional para este exercício)
* **GatewaySubnet:** 10.11.255.0/27
* **Grupo de Recursos:** TestRG1
* **Localização:** E.U.A. Leste
* **Servidor DNS:** 8.8.8.8 (opcional para este exercício)
* **Nome do site local:** Site2

## <a name="CreatVNet"></a>1. Criar uma rede virtual

Quando cria uma rede virtual para utilizar numa ligação de S2S, tem de certificar-se de que os espaços de endereços que especificar não se sobrepõem a nenhum dos espaços de endereços do cliente para os sites locais aos quais pretende ligar. Se tiver sub-redes sobrepostas, a ligação não funcionará corretamente.

* Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. 

* Se ainda não tiver uma rede virtual, crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Novo**. No campo **Procurar no Marketplace**, escreva "Rede Virtual". Localize **Rede Virtual** na lista devolvida e clique para abrir o painel **Rede Virtual**.

    ![Painel Procurar rede virtual](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. Perto da parte inferior do painel Rede Virtual, na lista **Selecionar um modelo de implementação**, selecione **Clássica** e clique em **Criar**.

    ![Selecionar modelo de implementação](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. No painel **Criar rede virtual**, configure as definições da VNet. Neste painel, deve adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais.

    ![Painel Criar rede virtual](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Painel Criar rede virtual")
5. Verifique se a **Subscrição** está correta. Pode utilizar o menu pendente para mudar de subscrição.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo escrevendo um nome para o novo grupo de recursos. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em seguida, selecione as definições **Localização** para a sua VNet. A localização determina onde é que os recursos que implementar nesta VNet vão residir.
8. Selecione **Afixar ao dashboard** se pretender conseguir localizar mais facilmente as suas VNet no dashboard e clique em **Criar**.

    ![Afixar ao dashboard](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Afixar ao dashboard")
9. Depois de clicar em "Criar", verá um mosaico no dashboard que irá refletir o progresso da VNet. O mosaico muda enquanto a VNet está a ser criada.

    ![Mosaico A criar a rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "A criar a rede virtual")

Assim que criar a rede virtual, verá **Criado** listado no **Estado** na página de redes do Portal Clássico do Azure.

## <a name="additionaladdress"></a>2. Adicionar espaço de endereços adicional

Depois de criar a rede virtual, pode adicionar espaço de endereços adicional. A adição de espaço de endereços adicional não é uma parte obrigatória de uma configuração de S2S, mas se precisar de vários espaços de endereços, utilize os seguintes passos:

1. Localize as redes virtuais no portal.
2. No painel da sua rede virtual, na secção **Definições**, clique em **Espaço de endereços**.
3. No painel Espaço de endereços, clique em **+Adicionar** e introduza o espaço de endereços adicional.
 
## <a name="dns"></a>3. Especificar um servidor DNS
As definições de DNS não são uma parte obrigatória de uma configuração de S2S, mas o DNS é necessário se quiser resolução de nomes.

Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS, de modo a lidar com a resolução de nomes. Abra as definições da rede virtual, clique em Servidores DNS e adicione o endereço IP do servidor DNS que quer utilizar para a resolução de nomes. Esta definição não cria um servidor DNS. Nas definições de exemplo, utilizamos um servidor DNS público. Normalmente, deve utilizar um servidor DNS privado. Certifique-se de que adiciona um servidor DNS com o qual os seus recursos possam comunicar.

1. Localize as redes virtuais no portal.
2. No painel da sua rede virtual, na secção **Definições**, clique em **Servidores DNS**.
3. Adicione um servidor DNS.
4. Clique em **Guardar** no início da página para guardar as definições.
 
## <a name="localsite"></a>4. Configurar o site local

O site local refere-se, normalmente, à sua localização no local. Contém o endereço IP do dispositivo VPN para o qual irá criar uma ligação e os intervalos de endereços IP que serão encaminhados através do gateway de VPN para o dispositivo VPN.

1. No portal, navegue para a rede virtual para a qual pretende criar um gateway.
2. No painel da rede virtual, no painel **Descrição Geral**, na secção de ligações VPN, clique em **Gateway** para abrir o painel **Nova Ligação VPN**.

    ![Clique para configurar as definições do gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Clique para configurar as definições do gateway")
3. No painel **Nova Ligação VPN**, selecione **Site a site**.

    ![Clique em site a site](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "site a site")
4. Clique em **Site local – Configurar definições necessárias** para abrir o painel **Site local**. Configure as definições e, em seguida, clique em **OK** para guardar as definições.
    - **Nome:** crie um nome para o seu site local para que o consiga identificar facilmente.
    - **Endereço IP do gateway de VPN:** este é o endereço IP público do dispositivo VPN para a rede no local. O dispositivo VPN requer um endereço IP público IPv4. Especifique um endereço IP público válido para o dispositivo VPN ao qual pretende ligar. Não pode estar protegido por NAT e tem de estar acessível ao Azure.
    - **Espaço de endereços de cliente:** lista os intervalos de endereços IP que pretende que sejam encaminhados para a rede no local através deste gateway. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especifica aqui não se sobrepõem aos intervalos de outras redes às quais a rede virtual se liga ou aos intervalos de endereços da própria rede virtual.

    ![Site local](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Configurar o site local")

## <a name="gatewaysubnet"></a>5. Configurar a sub-rede do gateway

Tem de criar uma sub-rede de gateway para o gateway de VPN. A sub-rede do gateway contém os endereços IP que os serviços do gateway de VPN irão utilizar.

1. No painel **Nova Ligação VPN**, selecione a caixa de verificação **Criar gateway de imediato**. Será apresentado o painel de "Configuração de gateway opcional". Se não selecionar a caixa de verificação, não verá o painel para configurar a sub-rede do gateway.

    ![Configuração do gateway - Sub-rede, tamanho, tipo de encaminhamento](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Configuração do gateway - Sub-rede, tamanho, tipo de encaminhamento")
2. Clique em **Configuração de gateway opcional - Sub-rede, tamanho, tipo de encaminhamento** para abrir o painel **Configuração do gateway**.
3. No painel **Configuração do Gateway**, clique em **Sub-rede - Configurar definições necessárias** para abrir o painel **Adicionar sub-rede**.

    ![Configuração do gateway - sub-rede do gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Configuração do gateway - sub-rede do gateway")
4. No painel **Adicionar sub-rede**, adicione a sub-rede do gateway. Ao adicionar a sub-rede do gateway, se possível, é melhor criar uma sub-rede do gateway com um bloco CIDR de /28 ou /27. Isto irá garantir que tem endereços IP suficientes para acomodar os requisitos de configuração futura adicionais.  Clique em **OK** para guardar as definições.

    ![Adicionar sub-rede do gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Adicionar sub-rede do gateway")

## <a name="sku"></a>6. Especificar o SKU e o tipo de VPN
1. Selecione o **Tamanho** do gateway. Este é o SKU de gateway que vai utilizar para criar o gateway de rede virtual. No portal, o 'SKU Predefinido' = **Basic**. Para obter mais informações sobre os SKUs de gateway, veja [About VPN Gateway Settings (Acerca das Definições do Gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![Selecionar o SKU e o tipo de VPN](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Selecionar o SKU e o tipo de VPN")
2. Selecione o **Tipo de Encaminhamento** do gateway. Também é conhecido como o tipo de VPN. É importante selecionar o tipo de gateway correto porque não é possível converter o gateway de um tipo para outro. O dispositivo VPN tem de ser compatível com o tipo de encaminhamento que selecionar. Para obter mais informações sobre o tipo de VPN, veja [About VPN Gateway (Acerca do Gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Poderá ver artigos que fazem referência aos tipos de VPN 'RouteBased' e 'PolicyBased'. 'Dynamic' corresponde a 'RouteBased' e 'Static' corresponde a 'PolicyBased'.
3. Clique em **OK** para guardar as definições.
4. No painel **Nova Ligação de VPN**, clique em **OK**, na parte inferior, para começar a criar o gateway de rede virtual. Esta ação pode demorar até 45 minutos a concluir.

## <a name="vpndevice"></a>7. Configurar o dispositivo VPN

Contacte o fabricante do dispositivo para obter as informações de configuração específicas e configurar o dispositivo. Veja [Dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obter mais informações sobre os dispositivos VPN que funcionam bem com o Azure. Além disso, verifique a existência de [Problemas de compatibilidade de dispositivos conhecidos](vpn-gateway-about-vpn-devices.md#known) para o dispositivo VPN que pretende utilizar. 

Quando configurar o seu dispositivo VPN, irá precisar do endereço IP do gateway de VPN que criou. Para localizá-lo, aceda ao painel **Descrição geral** da sua rede virtual.

## <a name="CreateConnection"></a>8. Criar a ligação
Neste passo, vai definir a chave partilhada e criar a ligação. A chave que definir tem de ser a mesma chave que foi utilizada na configuração do seu dispositivo VPN.

> [!NOTE]
> Atualmente, este passo não está disponível no portal do Azure. Terá de utilizar a versão da Gestão de Serviço (SM) dos cmdlets do Azure PowerShell.                                                                                                                                                                             
>
>

### <a name="step-1-connect-to-your-azure-account"></a>Passo 1. Ligar à sua conta do Azure

1. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

        Login-AzureRmAccount
2. Verifique as subscrições da conta.

        Get-AzureRmSubscription
3. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Adicione a versão de SM dos cmdlets do PowerShell.

        Add-AzureAccount

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Passo 2. Definir a chave partilhada e criar a ligação

Ao trabalhar com o PowerShell e o modelo de implementação clássica, por vezes, os nomes dos recursos no portal não são os nomes que o Azure espera ver quando utiliza o PowerShell. Os passos seguintes irão ajudá-lo a exportar o ficheiro de configuração de rede para obter os valores exatos para os nomes.

1. Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

         Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
2. Abra o ficheiro de configuração de rede com um editor de xml e verifique os valores de 'LocalNetworkSite name' e 'VirtualNetworkSite name'. Modifique o exemplo para refletir os valores. Ao especificar um nome que contenha espaços, utilize plicas à volta do valor.

3. Defina a chave partilhada e crie a ligação. '-SharedKey' é um valor que gera e especifica. No exemplo, utilizámos 'abc123', mas pode gerar e (deve) utilizar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor que especificou quando configurou o seu dispositivo VPN.

        Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
        -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
Quando a ligação é criada, o resultado é: **Estado: Com êxito**.

## <a name="verify"></a>9. Verificar a ligação

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


