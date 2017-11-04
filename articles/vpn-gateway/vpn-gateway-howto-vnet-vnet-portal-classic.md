---
title: "Criar uma ligação entre as VNets: clássico: portal do Azure | Microsoft Docs"
description: "Como ligar redes virtuais do Azure em conjunto com o PowerShell e o portal clássico do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: 77097d59077cd8e199acdb5dc0d8427369565eea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configurar uma ligação VNet a VNet (clássica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra-lhe como criar uma ligação de gateway de VPN entre redes virtuais. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. Os passos neste artigo aplicam-se ao modelo de implementação clássica e o portal do Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet a VNet conectividade diagrama](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações VNet a VNet

Ligar uma rede virtual a outra rede virtual (VNet a VNet) no modelo de implementação clássica com um gateway de VPN é semelhante à ligação uma rede virtual a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

As VNets que liga podem estar em diferentes subscrições e regiões diferentes. Pode combinar VNet para uma comunicação VNet com configurações multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

![VNet a VNet ligações](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Por que motivo ligar redes virtuais?

Poderá pretender ligar redes virtuais pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Balanceador de carga do Azure e da Microsoft ou de tecnologia de clustering de terceiros, pode configurar elevada carga de trabalho com georredundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações multicamadas regionais com limites de isolamento seguro**

  * Na mesma região, pode configurar aplicações de várias camadas com várias VNets ligadas, juntamente com forte isolamento e de uma comunicação segura entre camadas.
* **Entre a subscrição, comunicação entre organização no Azure**

  * Se tiver várias subscrições do Azure, pode ligar cargas de trabalho de diferentes subscrições em conjunto com segurança entre redes virtuais.
  * Para empresas ou fornecedores de serviços, pode ativar a comunicação entre organização com a tecnologia de VPN segura no Azure.

Para obter mais informações sobre ligações de VNet a VNet, veja [Considerações de VNet a VNet](#faq) no final deste artigo.

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este exercício, transfira e instale a versão mais recente dos cmdlets do PowerShell de gestão de serviço do Azure (SM). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Podemos utilizar o portal para a maior parte dos passos, mas é necessário utilizar o PowerShell para criar ligações entre as VNets. Não é possível criar as ligações no portal do Azure.

## <a name="plan"></a>Passo 1 - Planear os intervalos de endereços IP

É importante decidir os intervalos que irá utilizar para configurar as redes virtuais. Para esta configuração, tem de se certificar de que nenhuma das suas intervalos de VNet sobrepor-se entre si, ou com todas as redes locais que se ligam ao.

A tabela seguinte mostra um exemplo de como definir as suas VNets. Utilize os intervalos de uma ligação ininterrupta apenas. Anote os intervalos para as redes virtuais. Estas informações são necessárias para os passos seguintes.

**Exemplo**

| Rede Virtual | Espaço de endereços | Região | Estabelece ligação ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |EUA Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |EUA Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Passo 2 - criar as redes virtuais

Criar duas redes virtuais no [portal do Azure](https://portal.azure.com). Para obter os passos criar redes virtuais clássicas, consulte [criar uma rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Quando utilizar o portal para criar uma rede virtual clássica, terá de navegar para o painel de rede virtual, utilizando os passos seguintes, caso contrário, não é apresentada a opção para criar uma rede virtual clássica:

1. Clique em de '+' para abrir o painel 'New'.
2. No campo 'Procurar no marketplace', escreva 'Rede Virtual'. Se selecionar em vez disso, funcionamento em rede -> rede Virtual, não será apresentada a opção para criar uma VNet clássica.
3. Localize 'Rede Virtual' da lista devolvida e clique nele para abrir o painel de rede Virtual. 
4. No painel de rede virtual, selecione 'Clássico' para criar uma VNet clássica. 

Se estiver a utilizar este artigo como um exercício, pode utilizar os seguintes valores de exemplo:

**Valores da TestVNet1**

Nome: TestVNet1<br>
Espaço de endereços: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nome da sub-rede: predefinido<br>
Intervalo de endereços da sub-rede: 10.11.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: EUA Leste<br>
GatewaySubnet: 10.11.1.0/27

**Valores da TestVNet4**

Nome: TestVNet4<br>
Espaço de endereços: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nome da sub-rede: predefinido<br>
Intervalo de endereços da sub-rede: 10.41.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: EUA Oeste<br>
GatewaySubnet: 10.41.1.0/27

**Ao criar as suas VNets, tenha em atenção as seguintes definições:**

* **Espaços de endereços de rede virtual** – na página de espaços de endereços de rede Virtual, especifique o intervalo de endereços que pretende utilizar para a rede virtual. Estes são os endereços IP dinâmicos que serão atribuídos para as VMs e outras instâncias de função que implementar nesta rede virtual.<br>Os espaços de endereços que selecionou não é possível sobrepor-se com os espaços de endereços de qualquer um das outras localizações VNets ou no local que ligará nesta VNet.

* **Localização** – ao criar uma rede virtual, associá-la com uma localização do Azure (região). Por exemplo, se pretender que as VMs que são implementadas para a rede virtual estejam fisicamente localizadas nos EUA oeste, selecione essa localização. Não é possível alterar a localização associadas à rede virtual depois de a criar.

**Depois de criar as suas VNets, pode adicionar as seguintes definições:**

* **Espaço de endereços** – o espaço de endereços adicionais não é necessário para esta configuração, mas pode adicionar espaço de endereços adicionais depois de criar a VNet.

* **Sub-redes** – sub-redes adicionais não são necessárias para esta configuração, mas pode querer ter as VMs numa sub-rede separada das outras instâncias de função.

* **Servidores DNS** – introduza o nome do servidor DNS e endereço IP. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual.

Nesta secção, configure o tipo de ligação, o site local e criar o gateway.

## <a name="localsite"></a>Passo 3 - configurar o site local

Azure utiliza as definições especificadas em cada site de rede local para determinar como encaminhar o tráfego entre as VNets. Tem de apontar para a respetiva rede local que pretende para encaminhar o tráfego para cada VNet. Determinar o nome que pretende utilizar para fazer referência a cada site de rede local. É melhor utilizar algo descritivo.

Por exemplo, TestVNet1 estabelece ligação a um site de rede local que criar com o nome 'VNet4Local'. As definições para VNet4Local contêm os prefixos de endereço da TestVNet4.

O site local para cada VNet é outra VNet. Os valores de exemplo seguintes são utilizados para a configuração:

| Rede Virtual | Espaço de endereços | Região | Estabelece ligação ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |EUA Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |EUA Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Localize a TestVNet1 no portal do Azure. No **ligações VPN** secção do painel, clique em **Gateway**.

    ![Nenhum gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. No **nova ligação VPN** página, selecione **Site a Site**.
3. Clique em **Local site** para abrir a página de Local site e configurar as definições.
4. No **Local site** página, nome do site local. No nosso exemplo, Atribuímos nomes de site local 'VNet4Local'.
5. Para **endereço IP do gateway de VPN**, pode utilizar qualquer endereço IP que pretende, desde que faz parte de um formato válido. Normalmente, utilizará o endereço IP externo real para um dispositivo VPN. No entanto, para uma configuração de VNet a VNet clássica, pode utiliza o endereço IP público, que é atribuído ao gateway para a sua VNet. Uma vez que ainda não tiver criado o gateway de rede virtual, especifique qualquer endereço IP público válido como um marcador de posição.<br>Não deixe em branco - não é opcional para esta configuração. Num passo posterior, volte atrás para estas definições e configurá-los com os endereços IP de gateway de rede virtual correspondentes assim que o Azure gera-lo.
6. Para **espaço de endereços de cliente**, utilize o espaço de endereços da outra VNet. Consulte o exemplo de planeamento. Clique em **OK** para guardar as definições e voltar para o **nova ligação VPN** painel.

    ![local site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Passo 4 - criar o gateway de rede virtual

Cada rede virtual tem de ter um gateway de rede virtual. O gateway de rede virtual encaminha e encripta o tráfego.

1. No painel **Nova Ligação VPN**, selecione a caixa de verificação **Criar gateway de imediato**.
2. Clique em **sub-rede, tamanho e tipo de encaminhamento**. No **configuração do Gateway** painel, clique em **sub-rede**.
3. O nome de sub-rede do gateway é preenchido automaticamente com o nome necessário "GatewaySubnet". O **intervalo de endereços** contém os endereços IP que são atribuídos para os serviços de gateway VPN. Algumas configurações de permitem uma sub-rede de gateway de /29, mas é melhor utilizar/28 ou /27 para acomodar configurações futuras que poderão precisar de mais endereços IP para os serviços de gateway. Nas definições nosso exemplo, utilizamos 10.11.1.0/27. Ajustar o espaço de endereço, em seguida, clique em **OK**.
4. Configurar o **tamanho Gateway**. Esta definição refere-se ao [SKU de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configurar o **tipo de encaminhamento**. O encaminhamento de tipo para esta configuração tem de ser **dinâmica**. Não é possível alterar o tipo de encaminhamento mais tarde, a menos que fechar as o gateway e criar um novo.
6. Clique em **OK**.
7. No **nova ligação VPN** painel, clique em **OK** para começar a criar o gateway de rede virtual. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

## <a name="vnet4settings"></a>Passo 5 - configurar as definições da TestVNet4

Repita os passos para [criar um site local](#localsite) e [criar o gateway de rede virtual](#gw) para configurar a TestVNet4, substituindo os valores quando for necessário. Se estiver fazê-lo como um exercício, utilize o [valores de exemplo](#vnetvalues).

## <a name="updatelocal"></a>Passo 6 - atualizar os sites locais

Depois de criar gateways de rede virtual para ambas as VNets, terá de ajustar os sites locais **endereço IP do gateway de VPN** valores.

|Nome da VNet|Site ligado|Endereço IP do gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP do VPN gateway da TestVNet4|
|TestVNet4|VNet1Local|Endereço IP do VPN gateway da TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1 - obter o endereço IP da público de gateway rede virtual

1. Localize a rede virtual no portal do Azure.
2. Clique para abrir a VNet **descrição geral** painel. No painel, no **ligações VPN**, pode ver o endereço IP do gateway da rede virtual.

  ![IP público](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copie o endereço IP. Irá utilizá-lo na secção seguinte.
4. Repita estes passos para a TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2 - modificar os sites locais

1. Localize a rede virtual no portal do Azure.
2. O vnet **descrição geral** painel, clique no site local.

  ![Site local criada](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. No **ligações de VPN de Site para Site** painel, clique no nome do site local que pretende modificar.

  ![Abra site local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Clique em de **Local site** que pretende modificar.

  ![modificar o site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Atualização do **endereço IP do gateway de VPN** e clique em **OK** para guardar as definições.

  ![IP do gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Feche os outros painéis.
7. Repita estes passos para a TestVNet4.

## <a name="getvalues"></a>Passo 7 - obter valores do ficheiro de configuração de rede

Quando cria as VNets clássicas no portal do Azure, o nome que visualiza não é o nome completo que utiliza para o PowerShell. Por exemplo, uma VNet que parecem ser designada **TestVNet1** no portal, pode ter um nome de tempo muito no ficheiro de configuração de rede. O nome poderá ter um aspeto semelhante: **grupo ClassicRG TestVNet1**. Quando criar as suas ligações, é importante utilizar os valores que vê no ficheiro de configuração de rede.

Nos passos seguintes, irá ligar à sua conta do Azure e transfira e veja o ficheiro de configuração de rede para obter os valores que são necessários para as suas ligações.

1. Transfira e instale a versão mais recente dos cmdlets do PowerShell de gestão de serviço do Azure (SM). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

2. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

  ```powershell
  Login-AzureRmAccount
  ```

  Verifique as subscrições da conta.

  ```powershell
  Get-AzureRmSubscription
  ```

  Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  Em seguida, utilize o cmdlet seguinte para adicionar a sua subscrição do Azure para o PowerShell para o modelo de implementação clássica.

  ```powershell
  Add-AzureAccount
  ```
3. Exporte e veja o ficheiro de configuração de rede. Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Abra o ficheiro com um editor de texto e ver os nomes para as suas VNets e sites. Estes irão ser o nome a utiliza quando criar as suas ligações.<br>VNet nomes estão listados como **VirtualNetworkSite name =**<br>Os nomes de site são listados como **LocalNetworkSiteRef name =**

## <a name="createconnections"></a>Passo 8 - criar as ligações de gateway VPN

Quando todos os passos anteriores foram concluídos, pode definir as chaves de pré-partilhada IPsec/IKE e criar a ligação. Este conjunto de passos utiliza PowerShell. Não não possível configurar ligações VNet a VNet para o modelo de implementação clássica no portal do Azure.

Nos exemplos, tenha em atenção que a chave partilhada é exatamente o mesmo. A chave partilhada tem de corresponder sempre. Não se esqueça de substituir os valores nestes exemplos com os nomes de exatos para as suas VNets e Sites de rede Local.

1. Criar a ligação da TestVNet1 para a TestVNet4.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
  -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
  ```
2. Criar a ligação da TestVNet4 para a TestVNet1.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
  -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
  ```
3. Aguarde que as ligações ao inicializar. Depois do gateway foi iniciado, o estado é 'Successful'.

  ```
  Error          :
  HttpStatusCode : OK
  Id             :
  Status         : Successful
  RequestId      :
  StatusCode     : OK
  ```

## <a name="faq"></a>Considerações de VNet a VNet para as VNets clássicas
* As redes virtuais podem estar nas subscrições idêntica ou diferentes.
* As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.
* Um serviço cloud ou um ponto final de balanceamento de carga não pode abranger várias redes virtuais, mesmo se estiverem ligadas em conjunto.
* Ligar a várias redes virtuais em conjunto não requer quaisquer dispositivos VPN.
* VNet a VNet suporta a ligação de redes virtuais do Azure. Não suporta ao ligar máquinas virtuais ou serviços que não são implementados para uma rede virtual em nuvem.
* VNet a VNet precisa de gateways de encaminhamento dinâmico. Os gateways de encaminhamento estático do Azure não são suportados.
* A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais. Não há um máximo de 10 túneis VPN para um gateway de VPN de rede virtual ligar para outras redes virtuais, ou sites no local.
* Os espaços de endereços das redes virtuais e sites de rede local no local não se podem sobrepor. Sobreposição de espaços de endereços fará com que a criação de redes virtuais ou os ficheiros de configuração netcfg carregamento falha.
* Os túneis redundantes entre um par de redes virtuais não são suportados.
* Todos os túneis VPN da vnet, incluindo P2S VPNs, partilham a largura de banda disponível para o gateway VPN e o mesmo VPN gateway SLA de disponibilidade no Azure.
* O tráfego VNet a VNet circula em toda a estrutura principal do Azure.

## <a name="next-steps"></a>Passos seguintes
Verifique as suas ligações. Consulte [verificar uma ligação de Gateway de VPN](vpn-gateway-verify-connection-resource-manager.md).
