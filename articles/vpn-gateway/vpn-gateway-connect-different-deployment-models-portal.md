---
title: "Ligar redes virtuais clássicas do Azure Resource Manager VNets: Portal | Microsoft Docs"
description: "Saiba como criar uma ligação VPN entre clássico VNets e as VNets do Resource Manager utilizando o portal e do Gateway de VPN"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2017
ms.author: cherylmc
ms.openlocfilehash: 2100b2b8710207ddb5d1848f11f4d6133f1dfd91
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Ligar redes virtuais a partir de modelos de implementação diferentes, utilizando o portal

Este artigo mostra como ligar VNets clássicas para VNets do Resource Manager para permitir que os recursos localizados nos modelos de implementação separado para comunicar entre si. Os passos neste artigo principalmente utilizam o portal do Azure, mas também pode criar esta configuração utilizando o PowerShell, selecionando o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar uma VNet clássica a uma VNet do Resource Manager é semelhante à ligação VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em subscrições diferentes e em regiões diferentes. Também pode ligar VNets que já tem ligações a redes no local, enquanto o gateway que tenham sido configurados com é dinâmico ou baseado na rota. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se as suas VNets estiverem na mesma região, poderá pretender em vez disso, considere a ligá-las a utilização de VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md). 

### <a name="before"></a>Antes de começar

* Estes passos partem do princípio de que ambas as VNets já foram criadas. Se estiver a utilizar este artigo como um exercício e não tiver as VNets, existem ligações nos passos para o ajudar a criá-los.
* Certifique-se de que os intervalos de endereços para as VNets não sobrepor-se entre si, ou se sobrepõe a qualquer um dos intervalos de outras ligações que os gateways podem ser ligados à.
* Instale os mais recentes cmdlets do PowerShell do Resource Manager e gestão de serviço (clássica). Neste artigo, utilizamos o portal do Azure e o PowerShell. PowerShell é necessário para criar a ligação da VNet clássica para a VNet do Resource Manager. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). 

### <a name="values"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**VNet clássica**

Nome da VNet = ClassicVNet <br>
Espaço de endereços = 10.0.0.0/24 <br>
Sub-rede 1 = 10.0.0.0/27 <br>
Grupo de recursos = ClassicRG <br>
Localização = EUA oeste <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**VNet do Resource Manager**

Nome da VNet = RMVNet <br>
Espaço de endereços = 192.168.0.0/16 <br>
Sub-rede 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Grupo de recursos = RG1 <br>
Localização = EUA leste <br>
O nome de gateway de rede virtual = RMGateway <br>
Tipo de gateway = VPN <br>
Tipo de VPN = baseado na rota <br>
Nome de endereço IP público do gateway = rmgwpip <br>
Gateway de rede local = ClassicVNetLocal <br>
Nome da ligação = RMtoClassic

### <a name="connection-overview"></a>Descrição geral da ligação

Para esta configuração, crie uma ligação de gateway VPN através de um túnel VPN IPsec/IKE entre as redes virtuais. Certifique-se de que nenhuma das suas intervalos de VNet sobrepor-se entre si, ou com todas as redes locais que se ligam ao.

A tabela seguinte mostra um exemplo de como o exemplo VNets e os sites locais são definidos:

| Rede Virtual | Espaço de endereços | Região | Estabelece ligação ao site de rede local |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |EUA Oeste | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |EUA Leste |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Secção 1 – configurar as definições da VNet clássicas

Nesta secção, vai criar local (local site) de rede e o gateway de rede virtual para a sua VNet clássica. Se não tiver uma VNet clássica e estiver a executar estes passos como um exercício, pode criar uma VNet utilizando [neste artigo](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e [exemplo](#values) valores das definições de acima.

Quando utilizar o portal para criar uma rede virtual clássica, terá de navegar para a página de rede virtual, utilizando os passos seguintes, caso contrário, não é apresentada a opção para criar uma rede virtual clássica:

1. Clique em de '+' para abrir a página 'New'.
2. No campo 'Procurar no marketplace', escreva 'Rede Virtual'. Se selecionar em vez disso, funcionamento em rede -> rede Virtual, não será apresentada a opção para criar uma VNet clássica.
3. Localize 'Rede Virtual' da lista devolvida e clique nele para abrir a página de rede Virtual. 
4. Na página de rede virtual, selecione 'Clássico' para criar uma VNet clássica. 

Se já tiver uma VNet com um gateway VPN, certifique-se de que o gateway é dinâmico. Se for estático, deve primeiro eliminar o gateway VPN e continuar.

As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus ou utilize o [exemplo](#values) valores.

### 1. <a name="local"></a>Configurar o site local

Abra o [portal do Azure](https://ms.portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue para **todos os recursos** e localize o **ClassicVNet** na lista.
2. No **descrição geral** na página de **ligações VPN** secção, clique no **Gateway** gráfico para criar um gateway.

    ![Configurar um gateway VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "configurar um gateway VPN")
3. No **nova ligação VPN** página, para **tipo de ligação**, selecione **Site para site**.
4. Para **Local site**, clique em **configurar definições necessárias**. Esta ação abre o **Local site** página.
5. No **Local site** página, crie um nome para indicar a VNet do Resource Manager. Por exemplo, 'RMVNetLocal'.
6. Se o gateway VPN para a VNet do Resource Manager já tem um endereço IP público, utilize o valor para o **endereço IP do gateway de VPN** campo. Se estiver a efetuar estes passos como um exercício, ou ainda não tiver um gateway de rede virtual para a sua VNet do Resource Manager, pode certificar-se um endereço IP do marcador de posição. Certifique-se de que o endereço IP do marcador de posição utiliza um formato válido. Mais tarde, substitua o endereço IP do marcador de posição com o endereço IP público do gateway de rede virtual do Gestor de recursos.
7. Para **espaço de endereços de cliente**, utilize os valores para os espaços de endereços IP de rede virtual para a VNet do Resource Manager. Esta definição é utilizada para especificar os espaços de endereços para encaminhar para a rede virtual do Gestor de recursos.
8. Clique em **OK** para guardar os valores e voltar para o **nova ligação VPN** página.

### <a name="classicgw"></a>2. Criar o gateway de rede virtual

1. No **nova ligação VPN** página, selecione o **criar gateway de imediato** caixa de verificação e clique em **configuração do gateway opcional** para abrir o **Gateway configuração** página. 

    ![Página de configuração do gateway abra](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "página de configuração do gateway aberta")
2. Clique em **sub-rede - configurar as definições necessárias** para abrir o **adicionar sub-rede** página. O **nome** já está configurado com o valor necessário **GatewaySubnet**.
3. O **intervalo de endereços** refere-se ao intervalo para a sub-rede do gateway. Embora possa criar uma sub-rede de gateway/29 com o endereço do intervalo (3 endereços), recomendamos a criação de uma sub-rede de gateway que contém mais endereços IP. Isto irá suportar configurações futuras que poderão precisar de endereços IP disponíveis mais. Se possível, utilize/27 ou /28. Se estiver a utilizar estes passos como um exercício, pode consultar o [exemplo](#values) valores. Clique em **OK** para criar a sub-rede do gateway.
4. No **configuração do Gateway** página, **tamanho** refere-se para o SKU de gateway. Selecione o SKU de gateway para o gateway de VPN.
5. Certifique-se do **tipo de encaminhamento** é **dinâmica**, em seguida, clique em **OK** para regressar ao **nova ligação VPN** página.
6. No **nova ligação VPN** página, clique em **OK** para começar a criar o gateway de VPN. Criar um gateway VPN, pode demorar até 45 minutos a concluir.

### <a name="ip"></a>3. Copie o endereço IP público do gateway de rede virtual

Depois do gateway de rede virtual foi criado, pode ver o endereço IP do gateway. 

1. Navegue para a sua VNet clássica e clique em **descrição geral**.
2. Clique em **ligações VPN** para abrir a página de ligações VPN. Na página de ligações VPN, pode ver o endereço IP público. Este é o endereço IP público atribuído ao gateway da rede virtual. 
3. Anote ou copie o endereço IP. Utilize-lo em passos posteriores quando trabalha com as definições de configuração do gateway de rede local do Resource Manager. Também pode ver o estado das suas ligações de gateway. Tenha em atenção de que o site de rede local que criou está listado como "Ligar". O estado será alterado depois de criar as suas ligações.
4. Feche a página depois de copiar o endereço IP do gateway.

## <a name="rmvnet"></a>Secção 2 - configurar as definições da VNet do Resource Manager

Nesta secção, vai criar o gateway de rede virtual e o gateway de rede local para a sua VNet do Resource Manager. Se não tiver uma VNet do Resource Manager e está a executar estes passos como um exercício, pode criar uma VNet utilizando [neste artigo](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) e [exemplo](#values) valores das definições de acima.

As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus ou utilize o [exemplo](#values) valores.

### <a name="1-create-a-gateway-subnet"></a>1. Criar uma sub-rede do gateway

Antes de criar um gateway de rede virtual, terá primeiro de criar a sub-rede do gateway. Crie uma sub-rede de gateway com a contagem CIDR de/28 ou superior. (/ 27, / 26, etc.)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="creategw"></a>2. Criar um gateway de rede virtual

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>3. Criar um gateway de rede local

O gateway de rede local Especifica o intervalo de endereços e o endereço IP público associados à sua VNet clássica e o gateway de rede virtual.

Se estão a fazer estes passos como um exercício, consulte estas definições:

| Rede Virtual | Espaço de endereços | Região | Estabelece ligação ao site de rede local |Endereço IP público do gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |EUA Oeste | RMVNetLocal (192.168.0.0/16) |O endereço IP público que é atribuído ao ClassicVNet gateway|
| RMVNet | (192.168.0.0/16) |EUA Leste |ClassicVNetLocal (10.0.0.0/24) |O endereço IP público que é atribuído ao RMVNet gateway.|

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Secção 3 - modifique as definições de local site VNet clássicas

Nesta secção, substitua o endereço IP do marcador de posição que utilizou quando especificar as definições de local site, com o endereço IP do gateway de VPN do Resource Manager. Esta secção utiliza os cmdlets do PowerShell (SM) clássicos.

1. No portal do Azure, navegue para a rede virtual clássica.
2. Na página para a rede virtual, clique em **descrição geral**.
3. No **ligações VPN** secção, clique no nome do seu site local no gráfico.

    ![Ligações de VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "ligações VPN")
4. No **ligações VPN de Site para site** página, clique no nome do site.

    ![Nome do site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "nome do Local site")
5. Na página de ligação para o site local, clique no nome do site local para abrir o **Local site** página.

    ![Abra local local](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "abra site local")
6. No **Local site** página, substitua o **endereço IP do gateway de VPN** com o endereço IP do gateway do Resource Manager.

    ![Endereço ip do gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "endereço IP do Gateway")
7. Clique em **OK** para atualizar o endereço IP.

## <a name="RMtoclassic"></a>Secção 4 - criar o Resource Manager para ligação clássica

Estes passos, pode configurar a ligação entre a VNet do Resource Manager e clássica VNet no portal do Azure.

1. No **todos os recursos**, localize o gateway de rede local. No nosso exemplo, o gateway de rede local está **ClassicVNetLocal**.
2. Clique em **configuração** e certifique-se de que o valor do endereço IP é o gateway VPN para a VNet clássica. Atualizar, se necessário, em seguida, clique em **guardar**. Feche a página.
3. No **todos os recursos**, clique em gateway de rede local.
4. Clique em **ligações** para abrir a página de ligações.
5. No **ligações** página, clique em  **+**  para adicionar uma ligação.
6. No **adicionar ligação** página, dê um nome de ligação. Por exemplo, 'RMtoClassic'.
7. **Site-Site** já selecionou nesta página.
8. Selecione o gateway de rede virtual que pretende associar este site.
9. Criar um **chave partilhada**. Esta chave também é utilizada na ligação que criou a VNet clássica para a VNet do Resource Manager. Pode gerar a chave ou efetuar uma cópia de segurança. No nosso exemplo, utilizamos 'abc123', mas pode (e deve) utilizar algo mais complexo.
10. Clique em **OK** para criar a ligação.

##<a name="classictoRM"></a>Secção 5 - criar clássico para ligação do Gestor de recursos

Estes passos, pode configurar a ligação entre a VNet clássica e a VNet do Resource Manager. Estes passos necessitam do PowerShell. Não é possível criar esta ligação no portal. Certifique-se de que transferiu e instalou o clássica (SM) e os cmdlets do PowerShell do Gestor de recursos (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Ligar à sua conta do Azure

Abra a consola do PowerShell com direitos elevados e inicie sessão na sua conta do Azure. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta do Azure. Após iniciar sessão, as definições da sua conta são transferidas para que fiquem disponíveis para o Azure PowerShell.

```powershell
Login-AzureRmAccount
```
   
Obter uma lista das suas subscrições do Azure, se tiver mais do que uma subscrição.

```powershell
Get-AzureRmSubscription
```

Especifique a subscrição que pretende utilizar. 

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

Adicione a conta do Azure para utilizar os cmdlets do PowerShell Clássicos (SM). Para tal, pode utilizar o seguinte comando:

```powershell
Add-AzureAccount
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Ver os valores de ficheiro de configuração de rede

Quando cria uma VNet no portal do Azure, o nome completo que utiliza o Azure não está visível no portal do Azure. Por exemplo, uma VNet que parecem ser com 'ClassicVNet' o nome no portal do Azure pode ter um nome de tempo muito no ficheiro de configuração de rede. O nome poderá ter um aspeto semelhante: 'Grupo ClassicRG ClassicVNet'. Nestes passos, transfira o ficheiro de configuração de rede e ver os valores.

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e ver o nome para a sua VNet clássica. Utilize os nomes no ficheiro de configuração de rede ao executar os cmdlets do PowerShell.

- VNet nomes estão listados como **VirtualNetworkSite name =**
- Os nomes de site são listados como **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. Criar a ligação

Defina a chave partilhada e criar a ligação da VNet clássica para a VNet do Resource Manager. Não é possível definir a chave partilhada a utilizar o portal. Certifique-se de que executar estes passos enquanto tem sessão iniciada no utilizando a versão clássica dos cmdlets do PowerShell. Para tal, utilize **Add-AzureAccount**. Caso contrário, não poderá definir a '-AzureVNetGatewayKey'.

- Neste exemplo, **- VNetName** é o nome da VNet clássica como encontrada no ficheiro de configuração de rede. 
- O **- LocalNetworkSiteName** é o nome especificado para o site local, como encontrado no ficheiro de configuração de rede.
- O **- SharedKey** é um valor que geram e especificar. Neste exemplo, utilizámos *abc123*, mas pode gerar algo mais complexo. O mais importante é que o valor que especificar aqui tem de ser o mesmo valor que especificou ao criar o Gestor de recursos para ligação clássica.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

##<a name="verify"></a>Secção 6 - Certifique-se as suas ligações

Pode verificar as suas ligações, utilizando o portal do Azure ou o PowerShell. Ao verificar, poderá ter de aguardar um minuto ou dois como a ligação está a ser criada. Quando uma ligação é bem-sucedida, o estado de conectividade é alterado de 'Ligação' para 'Ligado'.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a ligação da sua VNet clássica a vnet do Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a ligação de VNet do Resource Manager para a sua VNet clássica

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
