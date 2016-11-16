---
title: "Configurar uma ligação de gateway de VPN Ponto a Site para uma rede virtual através do modelo de implementação do Resource Manager e do portal do Azure | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site com o Resource Manager e o portal do Azure para se ligar à sua Rede Virtual do Azure em segurança."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aeebed0c733b9fbac964cdc532ff9d364683609b


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma ligação Ponto a Site a uma VNet com o Portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma ligação P2S é útil quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. 

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. Para obter mais informações sobre ligações Ponto a Site, veja [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Planeamento e Conceção](vpn-gateway-plan-design.md).

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no modelo de implementação do Resource Manager através do portal do Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Valores de exemplo
* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16**<br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede: FrontEnd**
* **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **GatewaySubnet: 192.168.200.0/24**
* **Nome do gateway de rede virtual: VNet1GW**
* **Tipo de Gateway: VPN**
* **Tipo de VPN: baseado na rota**
* **Endereço IP público: VNet1GWpip**
* **Tipo de ligação: Ponto a site**
* **Conjunto de endereços de cliente: 172.16.201.0/24**<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente especificado.

## <a name="before-beginning"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>Parte 1: criar uma rede virtual
Se estiver a criar esta configuração como um exercício, pode ver os [valores de exemplo](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

### <a name="2-add-additional-address-space-and-subnets"></a>2. Adicionar sub-redes e espaços de endereços adicionais
Pode adicionar sub-redes e espaços de endereços adicionais à VNet depois de ter sido criada.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

### <a name="3-create-a-gateway-subnet"></a>3. Criar uma sub-rede do gateway
Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. Se possível, é melhor criar uma sub-rede do gateway com um bloco CIDR de /28 ou /27, de modo a fornecer endereços IP suficientes para contemplar requisitos de configuração futuros adicionais.

As capturas de ecrã nesta secção são fornecidas como um exemplo de referência. Certifique-se de que utiliza o intervalo de endereços GatewaySubnet correspondente aos valores necessários para a sua configuração.

#### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="a-namednsa4-specify-a-dns-server-optional"></a><a name="dns"></a>4. Especificar um servidor DNS (opcional)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namecreategwapart-2-create-a-virtual-network-gateway"></a><a name="creategw"></a>Parte 2: criar um gateway de rede virtual
As ligações ponto a site exigem as seguintes definições:

* Tipo de gateway: VPN
* Tipo de VPN: baseado na rota

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namegeneratecertapart-3-generate-certificates"></a><a name="generatecert"></a>Parte 3: gerar certificados
O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Exporte os dados do certificado público (e não a chave privada) como um ficheiro .cer X.509 codificado com Base-64 de um certificado de raiz gerado por uma solução de certificados de empresa ou um certificado de raiz autoassinado. Em seguida, importe os dados do certificado público a partir do certificado de raiz para o Azure. Além disso, precisa de gerar um certificado de cliente a partir do certificado de raiz para os clientes. Cada cliente que pretenda ligar-se à rede virtual através de uma ligação de P2S tem de ter instalado um certificado de cliente que foi gerado a partir do certificado de raiz.

### <a name="a-namegetcera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. Obter o ficheiro .cer para o certificado de raiz
Se estiver a utilizar uma solução empresarial, pode servir-se da sua cadeia de certificados existente. Se não estiver a utilizar uma solução de AC empresarial, pode criar um certificado de raiz autoassinado. Um método para criar um certificado autoassinado é makecert.

* Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar. 
* Se não estiver a utilizar uma solução de certificados de empresa, tem de gerar um certificado de raiz autoassinado. Para obter os passos para o Windows 10, pode ver [Trabalhar com certificados de raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um ficheiro .cer a partir de um certificado, abra **certmgr.msc** e localize o certificado de raiz. Clique no certificado de raiz autoassinado com o botão direito, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**, selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (*.CER).** Clique depois em **Seguinte**. 
4. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
5. Clique em **Concluir** para exportar o certificado.

### <a name="a-namegenerateclientcerta2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. Gerar um certificado de cliente
Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação.

* Se estiver a utilizar uma solução de certificado de empresa, gere um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com',, em vez do formato “nome de domínio/nome de utilizador”. 
* Se estiver a utilizar um certificado de raiz autoassinado, consulte [Trabalhar com certificados de raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md), para gerar um certificado de cliente.

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. Exportar o certificado de cliente
É necessário um certificado de cliente para a autenticação. Após a gerar o certificado de cliente, exporte-o. O certificado de cliente que exportar será instalado posteriormente em cada computador cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr.msc*. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *.pfx*. Certifique-se de que regista ou memoriza a palavra-passe (chave) que define para este certificado.

## <a name="a-nameaddresspoolapart-4-add-the-client-address-pool"></a><a name="addresspool"></a>Parte 4: adicionar o conjunto de endereços de cliente
1. Quando o gateway de rede virtual tiver sido criado, navegue para a secção **Definições** do painel do gateway de rede virtual. Na secção **Definições**, clique em **Configuração ponto a site** para abrir o painel **Configuração**.
   
    ![painel ponto a site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "point to site blade")
2. **Conjunto de endereços** é o conjunto de endereços IP a partir do qual os clientes que se ligam irão receber um endereço IP. Adicione o conjunto de endereços e, em seguida, clique em **Guardar**.
   
    ![conjunto de endereços de cliente](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "client address pool")

## <a name="a-nameuploadfileapart-5-upload-the-root-certificate-cer-file"></a><a name="uploadfile"></a>Parte 5: carregar o ficheiro .cer do certificado de raiz
Depois de o gateway ter sido criado, pode carregar o ficheiro .cer de um certificado de raiz fidedigno para o Azure. Pode carregar ficheiros de um máximo de 20 certificados de raiz. Não carregue a chave privada do certificado de raiz para o Azure. Após o carregamento do ficheiro .cer, o Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. Navegue para o painel **Configuração ponto a site**. Adicione os ficheiros .cer na secção **Certificado de raiz** deste painel.
   
    ![painel ponto a site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "point to site blade")
2. Certifique-se de que exportou o certificado de raiz como um ficheiro X.509 codificado com Base-64 (.cer). Tem de exportá-lo neste formato para poder abrir o certificado com o editor de texto.
3. Abra o certificado com um editor de texto, como o Bloco de Notas. Copie apenas a secção seguinte:
   
    ![dados de certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "certificate data")
4. Cole os dados de certificado a secção **Dados de Certificado Público** do portal. Coloque o nome do certificado no espaço **Nome** e, em seguida, clique em **Guardar**. Pode adicionar até 20 certificados de raiz fidedigna.
   
    ![carregamento de certificados](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "certificate upload")

## <a name="a-nameclientconfigapart-6-download-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Parte 6: transferir e instalar o pacote de configuração do cliente VPN
Os clientes que se liguem ao Azure com P2S têm de ter um certificado de cliente e um pacote de instalação de cliente de VPN instalados. Os pacotes de configuração do cliente VPN estão disponíveis para clientes Windows. 

O pacote de clientes VPN contém informações para configurar o software de cliente VPN incorporado no Windows. A configuração é específica da VPN à qual se quer ligar. O pacote não instala software adicional. Consulte as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. No painel **Configuração ponto a site**, clique em **Transferir cliente VPN** para abrir o painel **Transferir cliente VPN**.
   
    ![transferência do cliente VPN](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "VPN client download")
2. Selecione o pacote correto para o cliente e, em seguida, clique em **Transferir**. Para clientes de 64 bits, selecione **AMD64**. Para clientes de 32 bits, selecione **x86**.
3. Instale o pacote no computador cliente. Se obtiver um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim** para instalar o pacote.
4. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostrará o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a este exemplo: 
   
    ![cliente de VPN](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "VPN client")

## <a name="a-nameinstallclientcertapart-7-install-the-client-certificate"></a><a name="installclientcert"></a>Parte 7: instalar o certificado de cliente
Cada computador cliente tem de ter um certificado de cliente para se autenticar. Ao instalar o certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Copie o ficheiro .pfx para o computador cliente.
2. Faça duplo clique no ficheiro .pfx para o instalar. Não modifique a localização da instalação.

## <a name="a-nameconnectapart-8-connect-to-azure"></a><a name="connect"></a>Parte 8: ligar ao Azure
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![cliente de VPN 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. A ligação deve ser agora estabelecida.
   
    ![cliente de VPN 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## <a name="a-nameverifyapart-9-verify-your-connection"></a><a name="verify"></a>Parte 9: verificar a ligação
1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do Conjunto de Endereços de Cliente de VPN de Ponto a Site que especificou na configuração. Os resultados deverão ser algo semelhante a isto:
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="a-nameaddato-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Para adicionar ou remover certificados de raiz fidedigna
Pode remover o certificado de raiz fidedigna do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ser ligados ao Azure via Ponto a Site. Se pretender que os clientes estabeleçam ligação, terá de instalar um novo certificado de cliente que seja gerado a partir de um certificado considerado fidedigno no Azure.

Pode gerir a lista de certificados de cliente revogados no painel **Configuração ponto a site**. Este é o painel que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).

## <a name="a-namerevokeclientato-manage-the-list-of-revoked-client-certificates"></a><a name="revokeclient"></a>Para gerir a lista de certificados de cliente revogados
Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Se remover um certificado de raiz .cer do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Se pretender revogar um certificado de cliente específico, e não a raiz, pode fazê-lo. Dessa forma, os outros certificados gerados a partir do certificado de raiz continuarão a ser válidos. 

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

Pode gerir a lista de certificados de cliente revogados no painel **Configuração ponto a site**. Este é o painel que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).

## <a name="next-steps"></a>Passos seguintes
Pode adicionar uma máquina virtual à rede virtual. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.




<!--HONumber=Nov16_HO2-->


