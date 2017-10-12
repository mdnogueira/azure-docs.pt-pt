---
title: "Ligar um computador a uma rede virtual através de Ponto a Site e autenticação de certificado: Portal do Azure clássico | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site com o portal do Azure para se ligar à sua Rede Virtual do Azure clássica em segurança."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: cherylmc
ms.openlocfilehash: 7805e7c91c49fe1ef2d92b64c62bbfd15ab492b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-classic-azure-portal"></a>Configurar uma ligação de Ponto a Site a uma VNet com a autenticação de certificado (clássico): Portal do Azure

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra como criar uma VNet com uma ligação Ponto a Site no modelo de implementação clássica através do portal do Azure. Esta configuração utiliza certificados para autenticar o cliente da ligação. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Um gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. As ligações VPN Ponto a Site são úteis quando pretende ligar à VNet a partir de uma localização remota, como em teletrabalho em casa ou numa conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma Rede de VPNs, quando são poucos os clientes que precisam de ligar a uma VNet. 

A P2S utiliza SSTP (Secure Socket Tunneling Protocol), que é um protocolo VPN baseado em SSL. É estabelecida uma ligação VPN P2S ao iniciá-la a partir do computador cliente.


![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)


As ligações de autenticação de certificado de Ponto a Site precisam do seguinte:

* Um gateway de VPN Dinâmico.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Isto é considerado um certificado fidedigno e é utilizado para autenticação.
* Um certificado de cliente gerado a partir do certificado de raiz e instalado em cada computador cliente que irá ligar. Este certificado é utilizado para autenticação de cliente.
* Um pacote de configuração de cliente VPN tem de estar gerado e instalado em todos os computadores cliente que estabelece ligação. O pacote de configuração do cliente configura o cliente VPN nativo que já se encontra no sistema operativo com as informações necessárias para estabelecer uma ligação à VNet.

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público no local. A ligação VPN é criada através de SSTP (Secure Socket Tunneling Protocol). No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição. 

Para obter mais informações sobre ligações de Ponto a Site, consulte [Point-to-Site FAQ (FAQ sobre Ponto a Site)](#faq) no final deste artigo.

### <a name="example-settings"></a>Definições de exemplo

Pode utilizar os valores seguintes para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo:

* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16**<br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede: FrontEnd**
* **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **Tipo de ligação: Ponto a site**
* **Espaço de Endereços do Cliente: 172.16.201.0/24**. Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto especificado.
* **GatewaySubnet: 192.168.200.0/24**. A sub-rede do Gateway tem de utilizar o nome "GatewaySubnet".
* **Tamanho:** selecione o SKU do gateway que pretende utilizar.
* **Tipo de Encaminhamento: Dinâmico**

## <a name="vnetvpn"></a>1. Criar uma rede virtual e um gateway de VPN

Antes de começar, verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="createvnet"></a>Parte 1: Criar uma rede virtual

Se ainda não tiver uma rede virtual, crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para criar uma VNet com o portal do Azure, siga os passos abaixo:

1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Novo**. No campo **Procurar no Marketplace**, escreva "Rede Virtual". Localize a **Rede Virtual** na lista devolvida e clique para abrir a página **Rede Virtual**.

  ![Página Procurar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Junto à parte inferior da página Rede Virtual, na lista **Selecionar um modelo de implementação**, selecione **Clássica** e clique em **Criar**.

  ![Selecionar modelo de implementação](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Na página **Criar rede virtual**, configure as definições da VNet. Nesta página, deve adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais.

  ![Página Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Verifique se a **Subscrição** está correta. Pode utilizar o menu pendente para mudar de subscrição.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo escrevendo um nome para o novo grupo de recursos. Se estiver a criar um novo grupo de recursos, dê-lhe um nome de acordo com os valores de configuração planeados. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em seguida, selecione as definições **Localização** para a sua VNet. A localização determina onde é que os recursos que implementar nesta VNet vão residir.
8. Selecione **Afixar ao dashboard** se pretender conseguir localizar mais facilmente as suas VNet no dashboard e clique em **Criar**.

  ![Afixar ao dashboard](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Depois de clicar em Criar, aparece um mosaico no dashboard que irá refletir o progresso da VNet. O mosaico muda enquanto a VNet está a ser criada.

  ![Mosaico Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Assim que criar a rede virtual, vê **Criado** listado no **Estado** na página de redes do Portal Clássico do Azure.
11. Adicione um servidor DNS (opcional). Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS para a resolução de nomes. O endereço IP do servidor DNS especificado deve ser o endereço de um servidor DNS que possa resolver os nomes dos recursos na sua VNet.<br>Para adicionar um servidor DNS, abra as definições da rede virtual, clique em Servidores DNS e adicione o endereço IP do servidor DNS que pretende utilizar.

### <a name="gateway"></a>Parte 2: Criar a sub-rede de gateway e um gateway de encaminhamento dinâmico

Neste passo, vai criar uma sub-rede de gateway e um gateway de encaminhamento dinâmico. No portal do Azure do modelo de implementação clássica, pode utilizar as mesmas páginas de configuração para criar a sub-rede de gateway e o gateway.

1. No portal, navegue para a rede virtual para a qual pretende criar um gateway.
2. Na página da rede virtual, na página **Descrição Geral**, na secção de ligações VPN, clique em **Gateway**.

  ![Clique para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na página **Nova Ligação VPN**, selecione **Ponto a site**.

  ![Tipo de ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Em **Espaço de Endereços do Cliente**, adicione o intervalo de endereços IP. Este é o intervalo a partir do qual os clientes VPN recebem um endereço IP quando ligam. Utilize um intervalo de endereços IP privados que não se sobrepõe à localização no local onde irá estabelecer ligação ou com a VNet a que pretende ligar. Pode eliminar o intervalo preenchido automaticamente e depois adicionar o intervalo de endereços IP privados que pretende utilizar.

  ![Espaço de endereços do cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selecione a caixa de verificação **Criar gateway de imediato**.

  ![Criar gateway de imediato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Clique em **Configuração do gateway opcional** para abrir a página **Configuração do gateway**.

  ![Clique em configuração do gateway opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Clique em **Definições necessárias da configuração da sub-rede** para adicionar a **sub-rede de gateway**. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando pelo menos /28 ou /27. Desta forma, se quiser ter mais configurações no futuro, haverá endereços suficientes para as acomodar. Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) às mesmas. A associação de um grupo de segurança de rede a esta sub-rede poderá fazer com que o gateway de VPN deixe de funcionar conforme esperado.

  ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Selecione o **Tamanho** do gateway. O tamanho é o SKU de gateway para o gateway de rede virtual. No portal, o SKU Predefinido é **Básico**. Para obter mais informações sobre os SKUs de gateway, veja [About VPN Gateway Settings (Acerca das Definições do Gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Tamanho do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Selecione o **Tipo de Encaminhamento** do gateway. As configurações P2S requerem o tipo de encaminhamento **Dinâmico**. Clique em **OK** quando concluir a configuração desta página.

  ![Configurar o tipo de encaminhamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. Na página **Nova Ligação VPN**, clique em **OK**, na parte inferior da página, para começar a criar o seu gateway de rede virtual. Um gateway de VPN pode demorar até 45 minutos a concluir, consoante a sku do gateway que selecionar.

## <a name="generatecerts"></a>2. Criar certificados

O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública é então considerada "fidedigna". Os certificados de cliente têm de ser gerados a partir do certificado de raiz fidedigna e, em seguida, instalado em cada computador cliente no arquivo de Certificados Atuais do Utilizador/Pessoais. O certificado é utilizado para autenticar o cliente quando é iniciada uma ligação à VNet. 

Se utilizar certificados autoassinados, eles têm de ser criados com parâmetros específicos. Pode criar um certificado autoassinado com as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante que siga os passos nestas instruções ao trabalhar com os certificados de raiz autoassinados e ao gerar certificados de cliente a partir do certificado de raiz autoassinado. Caso contrário, os certificados que criar não serão compatíveis com ligações de P2S e irá receber um erro de ligação.

### <a name="cer"></a>Parte 1: Obter a chave pública (.cer) para o certificado de raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>Parte 2: Gerar um certificado de cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3. Carregar o ficheiro .cer do certificado de raiz

Após o gateway ser criado, pode carregar o ficheiro .cer (que contém as informações da chave pública) para um certificado de raiz fidedigna no Azure. Não carregue a chave privada do certificado de raiz para o Azure. Depois de o ficheiro .cer ser carregado, o Azure pode utilizá-lo para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado de raiz fidedigna. Pode carregar ficheiros de certificado de raiz fidedigna adicionais - até um total de 20 - mais tarde, se necessário.  

1. Na secção **Ligações VPN** da página da VNet, clique na imagem **clientes** para abrir a página **Ligação VPN Ponto a site**.

  ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na página **Ligação ponto a site**, clique em **Gerir certificados** para abrir a página **Certificados**.<br>

  ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na página **Certificados**, clique em **Carregar** para abrir a página **Carregar certificado**.<br>

    ![Página Carregar certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Clique na imagem de pasta para procurar o ficheiro .cer. Selecione o ficheiro e clique em **OK**. Atualize a página para ver o certificado carregado na página **Certificados**.

  ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. Configurar o cliente

Para ligar a uma VNet com um VPN de Ponto a Site, cada cliente tem de instalar um pacote para configurar o cliente de VPN do Windows nativo. O pacote de configuração configura o cliente VPN do Windows nativo com as definições necessárias para ligar à rede virtual.

Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente. Para consultar a lista de sistemas operativos cliente que são suportados, consulte [Point-to-Site connections FAQ (FAQ das ligações de Ponto a Site)](#faq) no final deste artigo.

### <a name="generateconfigpackage"></a>Parte 1: Gerar e instalar o pacote de configuração do cliente VPN

1. No portal do Azure, na página **Descrição Geral** da VNet, em **Ligações VPN**, clique na imagem de cliente para abrir a página **Ligação VPN ponto a site**.
2. Na parte superior da página **Ligação VPN ponto a site**, clique no pacote de transferência que corresponde ao sistema operativo cliente no qual será instalado:

  * Para clientes de 64 bits, selecione **Cliente VPN (64 bit)s**.
  * Para clientes de 32 bits, selecione **Cliente VPN (32 bit)s**.

  ![Transferir o pacote de configuração do cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Assim que o pacote é gerado, transfira e instale-o no seu computador cliente. Se vir um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**. Também pode guardar o pacote para instalar noutros computadores cliente.

### <a name="installclientcert"></a>Parte 2: Instalar o certificado de cliente

Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado. Normalmente, basta fazer duplo clique no certificado e instalá-lo. Para obter mais informações, veja [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install)(Instalar um certificado de cliente exportado).

## <a name="connect"></a>5. Ligar ao Azure

### <a name="connect-to-your-vnet"></a>Ligar à VNet

1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados.
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.

  ![Ligação de cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. A ligação é estabelecida.

  ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>Resolução de problemas com ligações P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="verifyvpnconnect"></a>Verificar a ligação VPN

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do intervalo de endereços de conetividade Ponto a Site que especificou quando criou a VNet. Os resultados deverão ser semelhantes a este exemplo:

  ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Conectar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Adicionar ou remover certificados de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que tenham um certificado gerado a partir dessa raiz não conseguirão autenticar-se e, por conseguinte, não conseguirão estabelecer ligação. Se quiser que um cliente faça a autenticação e estabeleça ligação, terá de instalar um novo certificado de cliente gerado a partir de um certificado de raiz considerado fidedigno (carregado) no Azure.

### <a name="addtrustedroot"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, veja a [Secção 3 - carregar o ficheiro .cer do certificado de raiz](#upload).

### <a name="removetrustedroot"></a>Para remover um certificado de raiz fidedigna

1. Na secção **Ligações VPN** da página da VNet, clique na imagem **clientes** para abrir a página **Ligação VPN Ponto a site**.

  ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na página **Ligação ponto a site**, clique em **Gerir certificados** para abrir a página **Certificados**.<br>

  ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na página **Certificados**, clique nas reticências junto ao certificado que quer remover e, em seguida, clique em **Eliminar**.

  ![Eliminar certificado de raiz](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Revogar um certificado de cliente

Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Ao revogar um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação da ligação Ponto a Site.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="revokeclientcert"></a>Para revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como: Obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua.
3. Navegue para a página **“nome da rede virtual clássica” > Ligação VPN ponto a site > Certificados** e clique em **Lista de revogação** para abrir a página Lista de revogação. 
4. Na página **Lista de revogação**, clique em **+ Adicionar certificado** para abrir a página **Adicionar certificado à lista de revogação**.
5. Na página **Adicionar certificado à lista de revogação**, cole o thumbprint do certificado como uma linha contínua de texto, sem espaços. Clique em **OK** na parte inferior da página.
6. Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que se tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.

## <a name="faq"></a>FAQ Ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).