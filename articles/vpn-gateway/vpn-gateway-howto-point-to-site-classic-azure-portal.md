---
title: "Configurar uma ligação de gateway de VPN Ponto a Site a uma Rede Virtual do Azure com o portal do Azure | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site com o portal do Azure para se ligar à sua Rede Virtual do Azure em segurança."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: f6fa6511c8d54e191de27fda73aad9feb734191f
ms.openlocfilehash: 11d27b786522d1f780a701229ed0a695224e9eb6


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma ligação Ponto a Site a uma VNet com o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no modelo de implementação clássica através do portal do Azure. Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma ligação P2S é útil quando pretende ligar à VNet a partir de uma localização remota, como, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que têm de se ligar a uma rede virtual.

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. Para obter mais informações sobre as ligações Ponto a Site, veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e o [Acerca do Gateway de VPN](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

As secções seguintes orientam-no ao longo dos passos para criar uma ligação Ponto a Site segura a uma rede virtual. 

1. Criar uma rede virtual e um gateway de VPN
2. Gerar certificados
3. Carregar o ficheiro .cer
4. Gerar o pacote de configuração do cliente VPN
5. Configurar o computador cliente
6. Ligar ao Azure

### <a name="example-settings"></a>Definições de exemplo
Pode utilizar as definições de exemplo seguintes:

* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16**
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

## <a name="a-namevnetvpnasection-1---create-a-virtual-network-and-a-vpn-gateway"></a><a name="vnetvpn"></a>Secção 1 - Criar uma rede virtual e um gateway de VPN
### <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>Parte 1: Criar uma rede virtual
Se ainda não tiver uma rede virtual, crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para criar uma VNet com o portal do Azure, siga os passos abaixo: 

1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Novo**. No campo **Procurar no Marketplace**, escreva “Rede Virtual”. Localize **Rede Virtual** na lista devolvida e clique para abrir o painel **Rede Virtual**.
   
    ![Painel Procurar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Search for virtual network blade")
3. Perto da parte inferior do painel Rede Virtual, na lista **Selecionar um modelo de implementação**, selecione **Clássica** e clique em **Criar**.
   
    ![Selecionar modelo de implementação](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Select deployment model")
4. No painel **Criar rede virtual**, configure as definições da VNet. Neste painel, deverá adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais.
   
    ![Painel Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Create virtual network blade")
5. Verifique se a **Subscrição** está correta. Pode utilizar o menu pendente para mudar de subscrição.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo escrevendo um nome para o novo grupo de recursos. Se estiver a criar um novo grupo, dê-lhe um nome de acordo com os valores de configuração planeados. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em seguida, selecione as definições **Localização** para a sua VNet. A localização irá determinar onde irão residir os recursos que são implementados nesta VNet.
8. Selecione **Afixar ao dashboard** se pretender conseguir localizar mais facilmente as suas VNet no dashboard e clique em **Criar**.
   
    ![Afixar ao dashboard](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Pin to dashboard")
9. Depois de clicar em Criar, verá um mosaico no dashboard que irá refletir o progresso da VNet. O mosaico muda enquanto a VNet está a ser criada.
   
    ![Mosaico Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Creating virtual network tile")
10. Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS, de modo a lidar com a resolução de nomes. Abra as definições da rede virtual, clique em Servidores DNS e adicione o endereço IP do servidor DNS que quer utilizar. Esta definição não cria um servidor DNS novo. Certifique-se de que adiciona um servidor DNS com o qual os seus recursos possam comunicar.

Assim que criar a rede virtual, verá **Criado** listado no **Estado** na página de redes do Portal Clássico do Azure.

### <a name="a-namegatewayapart-2-create-gateway-subnet-and-a-dynamic-routing-gateway"></a><a name="gateway"></a>Parte 2: Criar a sub-rede de gateway e um gateway de encaminhamento dinâmico
Neste passo, vai criar uma sub-rede de gateway e um gateway de encaminhamento dinâmico. No portal do Azure para o modelo de implementação clássica, pode utilizar os mesmos painéis de configuração para criar a sub-rede de gateway e o gateway.

1. No portal, navegue para a rede virtual para a qual pretende criar um gateway.
2. No painel da rede virtual, no painel **Descrição Geral**, na secção de ligações VPN, clique em **Gateway**.
   
    ![Clique aqui para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Click here to create a gateway")
3. No painel **Nova ligação de VPN**, selecione **Ponto a Site**.
   
    ![Tipo de ligação P2S](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "P2S connection type")
4. Em **Espaço de Endereços do Cliente**, adicione o intervalo de endereços IP. Este é o intervalo a partir do qual os clientes VPN receberão um endereço IP quando ligam. Elimine o intervalo preenchido automaticamente e adicione o seu.
   
    ![Espaço de endereços do cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Client address space")
5. Selecione a caixa de verificação **Criar gateway de imediato**.
   
    ![Criar gateway de imediato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Create gateway immediately")
6. Clique em **Configuração do gateway opcional** para abrir o painel **Configuração do gateway**.
   
    ![Clique em configuração do gateway opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Click optional gateway configuration")
7. Clique em **Definições necessárias da configuração da sub-rede** para adicionar a **sub-rede de gateway**. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando pelo menos /28 ou /27. Desta forma, se quiser ter mais configurações no futuro, haverá endereços suficientes para as acomodar.
   
   > [!IMPORTANT]
   > Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) às mesmas. A associação de um grupo de segurança de rede a esta sub-rede poderá fazer com que o gateway de VPN deixe de funcionar conforme esperado. Para obter mais informações sobre os grupos de segurança de rede, veja [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
   > 
   > 
   
    ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Add GatewaySubnet")
8. Selecione o **Tamanho** do gateway. Este é o SKU de gateway que vai utilizar para criar o gateway de rede virtual. No portal, o SKU Predefinido é **Básico**. Para obter mais informações sobre os SKUs de gateway, veja [About VPN Gateway Settings (Acerca das Definições do Gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   
    ![tamanho do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Selecione o **Tipo de Encaminhamento** do gateway. As configurações P2S requerem o tipo de encaminhamento **Dinâmico**. Clique em **OK** depois de concluir a configuração deste painel.
   
    ![Configurar o tipo de encaminhamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configure routing type")
10. No painel **Nova Ligação de VPN**, clique em **OK**, na parte inferior, para começar a criar o gateway de rede virtual. Esta ação pode demorar até 45 minutos a concluir. 

## <a name="a-namegeneratecertsasection-2---generate-certificates"></a><a name="generatecerts"></a>Secção 2 - Gerar certificados
O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Exporte os dados do certificado público (e não a chave privada) como um ficheiro .cer X.509 codificado com Base-64 de um certificado de raiz gerado por uma solução de certificados de empresa ou um certificado de raiz autoassinado. Em seguida, importe os dados do certificado público a partir do certificado de raiz para o Azure. Além disso, precisa de gerar um certificado de cliente a partir do certificado de raiz para os clientes. Cada cliente que pretenda ligar-se à rede virtual através de uma ligação de P2S tem de ter instalado um certificado de cliente que foi gerado a partir do certificado de raiz.

### <a name="a-namecerapart-1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>Parte 1: Obter o ficheiro .cer para o certificado de raiz
Se estiver a utilizar uma solução empresarial, pode servir-se da sua cadeia de certificados existente. Se não estiver a utilizar uma solução de AC empresarial, pode criar um certificado de raiz autoassinado. Um método para criar um certificado autoassinado é makecert.

* Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar. 
* Se não estiver a utilizar uma solução de certificados de empresa, tem de gerar um certificado de raiz autoassinado. Para obter os passos para o Windows 10, pode ver [Trabalhar com certificados de raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um ficheiro .cer a partir de um certificado, abra **certmgr.msc** e localize o certificado de raiz. Clique no certificado de raiz autoassinado com o botão direito, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**, selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (*.CER).** Clique depois em **Seguinte**. 
4. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
5. Clique em **Concluir** para exportar o certificado.

### <a name="a-namegenclientcertapart-2-generate-a-client-certificate"></a><a name="genclientcert"></a>Parte 2: Gerar um certificado de cliente
Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação.

* Se estiver a utilizar uma solução de certificado de empresa, gere um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com',, em vez do formato “nome de domínio/nome de utilizador”. 
* Se estiver a utilizar um certificado de raiz autoassinado, consulte [Trabalhar com certificados de raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md), para gerar um certificado de cliente.

### <a name="a-nameexportclientcertapart-3-export-the-client-certificate"></a><a name="exportclientcert"></a>Parte 3: Exportar o certificado de cliente
Instale um certificado de cliente em cada computador que pretende ligar à rede virtual. É necessário um certificado de cliente para a autenticação. Pode automatizar a instalação do certificado de cliente ou pode instalá-lo manualmente. Os passos seguintes descrevem como exportar e instalar manualmente o certificado de cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr.msc*. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *.pfx*. Certifique-se de que regista ou memoriza a palavra-passe (chave) que define para este certificado.

## <a name="a-nameuploadasection-3---upload-the-root-certificate-cer-file"></a><a name="upload"></a>Secção 3 – Carregar o ficheiro .cer do certificado de raiz
Depois de o gateway ter sido criado, pode carregar o ficheiro .cer de um certificado de raiz fidedigno para o Azure. Pode carregar ficheiros de um máximo de 20 certificados de raiz. Não carregue a chave privada do certificado de raiz para o Azure. Após o carregamento do ficheiro .cer, o Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. Na secção **Ligações VPN** do painel da VNet, clique na imagem **clientes** para abrir o painel **Ligação VPN Ponto a Site**.
   
    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clients")
2. No painel **Ligação Ponto a Site**, clique em **Gerir certificados** para abrir o painel **Certificados**.<br>
   
    ![Painel Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>
3. No painel **Certificados**, clique em **Carregar** para abrir o painel **Carregar certificado**.<br>
   
    ![Painel Carregar certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>
4. Clique na imagem de pasta para procurar o ficheiro .cer. Selecione o ficheiro e clique em **OK**. Atualize a página para ver o certificado carregado no painel **Certificados**.
   
    ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>

## <a name="a-namevpnclientconfigasection-4---generate-the-vpn-client-configuration-package"></a><a name="vpnclientconfig"></a>Secção 4 - Gerar o pacote de configuração do cliente VPN
Para ligar à rede virtual, também tem de configurar um cliente VPN. O computador cliente precisa de um certificado de cliente e do pacote de configuração adequado do cliente VPN para se ligar.

O pacote de clientes VPN contém informações de configuração para configurar o software de cliente VPN incorporado no Windows. O pacote não instala software adicional. As definições são específicas para a rede virtual à qual se quer ligar. Para consultar a lista de sistemas operativos cliente que são suportados, consulte a secção [Ligações de Ponto a Site](vpn-gateway-vpn-faq.md#point-to-site-connections) das FAQ do Gateway de VPN. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Para gerar o pacote de configuração do cliente VPN
1. No portal do Azure, no painel **Descrição Geral** da VNet, em **Ligações VPN**, clique na imagem de cliente para abrir o painel **Ligação VPN Ponto a Site**.
2. Na parte superior do painel **Ligação VPN Ponto a Site**, clique no pacote de transferência que corresponde ao sistema operativo cliente no qual será instalado:
   
   * Para clientes de 64 bits, selecione **Cliente VPN (64 bit)s**.
   * Para clientes de 32 bits, selecione **Cliente VPN (32 bit)s**.
     
     ![Transferir o pacote de configuração do cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>
3. Verá uma mensagem a dizer que o Azure está a gerar o pacote de configuração do cliente VPN da rede virtual. Ao fim de alguns minutos, o pacote é gerado e verá uma mensagem no computador local a dizer que o pacote foi transferido. Guarde o ficheiro do pacote de configuração. Vai instalá-lo em cada computador cliente que se vai ligar à rede virtual através de P2S.

## <a name="a-nameclientconfigurationasection-5---configure-the-client-computer"></a><a name="clientconfiguration"></a>Secção 5 - Configurar o computador cliente
### <a name="part-1-install-the-client-certificate"></a>Parte 1: Instalar o certificado de cliente
Cada computador cliente tem de ter um certificado de cliente para se autenticar. Ao instalar o certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Copie o ficheiro .pfx para o computador cliente.
2. Faça duplo clique no ficheiro .pfx para o instalar. Não modifique a localização da instalação.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: Instalar o pacote de configuração do cliente VPN
Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente.

1. Copie o ficheiro de configuração localmente para o computador que pretende ligar à rede virtual e faça duplo clique no ficheiro .exe. 
2. Depois de instalar o pacote, pode iniciar a ligação VPN. O pacote de configuração não está assinado pela Microsoft. Poderá pretender assinar o pacote utilizando o serviço de assinatura da sua organização ou o pacote pode ser assinado por si através da [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Pode utilizar o pacote sem assinar. No entanto, se o pacote não for assinado, é apresentado um aviso quando instalar o pacote.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostra o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a: 
   
    ![cliente de VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "VNet VPN client")

## <a name="a-nameconnectasection-6---connect-to-azure"></a><a name="connect"></a>Secção 6 – Ligar ao Azure
### <a name="connect-to-your-vnet"></a>Ligar à VNet
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![Ligar](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "VPN client connection")
3. A ligação deve ser agora estabelecida.
   
    ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Connection established")

### <a name="verify-the-vpn-connection"></a>Verificar a ligação VPN
1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do intervalo de endereços de conetividade Ponto a Site que especificou quando criou a VNet. Os resultados deverão ser algo semelhante a isto:

Exemplo:

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

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Nov16_HO3-->


