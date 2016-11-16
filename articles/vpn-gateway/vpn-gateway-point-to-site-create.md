---
title: "Configurar uma ligação de gateway de VPN Ponto a Site para uma Virtual Network do Azure através do portal clássico | Microsoft Docs"
description: "Ligue-se de forma segura à Rede Virtual do Azure criando uma ligação de gateway de VPN Ponto a Site."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 487a006050bdb77f03db19b87a98dd3f4c64a738


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-the-classic-portal"></a>Configurar uma ligação de Ponto a Site a uma VNet através do portal clássico
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Clássica – Portal Clássico](vpn-gateway-point-to-site-create.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma ligação P2S é útil quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual.

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no **modelo de implementação clássica** através do **portal clássico**.

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. Para obter mais informações sobre ligações Ponto a Site, veja [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Planeamento e Conceção](vpn-gateway-plan-design.md).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-point-to-site-create/p2sclassic.png "point-to-site")

Os seguintes passos explicam como criar uma ligação Ponto a Site segura para uma rede virtual. 

A configuração de uma ligação Ponto a Site é apresentada em quatro secções. A ordem na qual configurou cada uma das seguintes secções é importante. Não ignore os passos ou passe à frente.

* **Secção 1** Criar uma rede virtual e gateway de VPN.
* **Secção 2** Criar os certificados utilizados para a autenticação e a carregá-los.
* **Secção 3** Exportar e instalar os certificados de cliente.
* **Secção 4** Configurar o cliente VPN.

## <a name="a-namevnetvpnasection-1-create-a-virtual-network-and-a-vpn-gateway"></a><a name="vnetvpn"></a>Secção 1 - Criar uma rede virtual e um gateway de VPN
### <a name="part-1-create-a-virtual-network"></a>Parte 1: criar uma rede virtual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/). Estes passos utilizam o portal clássico e não o portal do Azure. Atualmente, não é possível criar uma ligação P2S através do portal do Azure.
2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Na página **Detalhes da Rede Virtual**, introduza as seguintes informações e clique na seta seguinte na parte inferior direita.
   
   * **Nome**: atribua um nome à rede virtual. Por exemplo, “VNet1”. Este é o nome que deverá mencionar quando implementar VMs nesta VNet.
   * **Localização**: a localização está diretamente relacionada com a localização física (região) onde pretende que os recursos (VMs) residam. Por exemplo, se pretender que as VMs que implementa nesta rede virtual estejam fisicamente localizadas nos EUA Leste, selecione essa localização. Não é possível alterar a região associada à rede virtual depois de a criar.
4. Na página **Servidores DNS e Conetividade VPN**, introduza as seguintes informações e clique na seta seguinte na parte inferior direita.
   
   * **Servidores DNS**: introduza o endereço IP e o nome do servidor DNS ou selecione um servidor DNS anteriormente registado no menu de atalho. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual. Se pretender utilizar o serviço de resolução de nomes predefinido do Azure, deixe esta secção em branco.
   * **Configurar VPN Ponto a Site**: selecione a caixa de verificação.
5. Na página **Conetividade Ponto a Site**, especifique o intervalo de endereços IP a partir do qual os clientes VPN receberão um endereço IP quando estiverem ligados. Existem algumas regras relativamente aos intervalos de endereços que pode especificar. É importante verificar se o intervalo que especificou não se sobrepõe a qualquer um dos intervalos localizados na sua rede no local.
6. Introduza as seguintes informações e clique na seta seguinte.
   
   * **Espaço de Endereços**: inclua o IP Inicial e o CIDR (Contagem de Endereços).
   * **Adicionar espaço de endereços**: adicione o espaço de endereços apenas se for preciso para o design de redes.
7. Na página **Espaços de Endereços da Rede Virtual**, especifique o intervalo de endereços que pretende utilizar para a rede virtual. Estes são os endereços IP dinâmicos (DIPS) que serão atribuídos às VMs e a outras instâncias de função que implementar nesta rede virtual.<br><br>É especialmente importante selecionar um intervalo que não se sobreponha a nenhum intervalo utilizado para a rede no local. Terá de trabalhar em coordenação com o administrador de rede, que poderá ter de extrair um intervalo de endereços IP do seu espaço de endereços da rede no local para a utilização na sua rede virtual.
8. Introduza as seguintes informações e clique na marca de verificação para começar a criar a rede virtual.
   
   * **Espaço de Endereços**: adicione o intervalo de endereços IP interno que pretende utilizar para esta rede virtual, incluindo o IP Inicial e a Contagem. É importante selecionar um intervalo que não se sobreponha a nenhum intervalo que seja utilizado para a rede no local. 
   * **Adicionar sub-rede**: não são necessárias sub-redes adicionais, mas pode querer criar uma sub-rede separada para as VMs com DIPS estáticos. Em alternativa, pode ter as VMs numa sub-rede separada das outras instâncias de função.
   * **Adicionar sub-rede do gateway**: a sub-rede do gateway é necessária para uma VPN Ponto a Site. Clique para adicionar a sub-rede do gateway. A sub-rede do gateway é utilizada apenas para o gateway de rede virtual.
9. Assim que criar a rede virtual, verá **Criado** listado no **Estado** na página de redes do Portal Clássico do Azure. Após criar a rede virtual, pode criar o gateway de encaminhamento dinâmico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: criar um gateway de encaminhamento dinâmico
O tipo de gateway tem de ser configurado como dinâmico. Os gateways de encaminhamento estático não funcionam com esta funcionalidade.

1. No Portal Clássico do Azure, na página **Redes**, clique na rede virtual que criou e navegue até à página **Dashboard**.
2. Clique em **Criar Gateway**, localizado na parte inferior da página **Dashboard**. É apresentada uma mensagem a perguntar **Pretende criar um gateway para a rede virtual “VNet1”**. Clique em **Sim** para começar a criar o gateway. A criação do gateway pode demorar cerca de 15 minutos.

## <a name="a-namegenerateasection-2-generate-and-upload-certificates"></a><a name="generate"></a>Secção 2 - Gerar e carregar certificados
Os certificados são utilizados para autenticar clientes VPN para as VPNs Ponto a Site. Pode utilizar um certificado de raiz gerado por uma solução de certificados de empresa ou pode utilizar um certificado autoassinado. Pode carregar até 20 certificados de raiz para o Azure. Depois do ficheiro .cer ser carregado, o Azure pode utilizar as informações contidas no mesmo para autenticar clientes que tenham um certificado de cliente instalado. O certificado de cliente tem de ser gerado a partir do mesmo certificado que o ficheiro .cer representa.

Nesta secção irá efetuar o seguinte:

* Obtenha o ficheiro .cer para um certificado de raiz. Pode ser um certificado autoassinado ou pode utilizar o seu sistema de certificado de empresa.
* Carregue o ficheiro .cer para o Azure.
* Gere certificados de cliente.

### <a name="a-namerootapart-1-obtain-the-cer-file-for-the-root-certificate"></a><a name="root"></a>Parte 1: Obter o ficheiro .cer para o certificado de raiz
Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar. Na [Parte 3](#createclientcert), pode gerar os certificados de cliente a partir do certificado de raiz.

Se não estiver a utilizar uma solução de certificados de raiz, terá de gerar um certificado de raiz autoassinado. Para obter os passos para o Windows 10, pode ver [Trabalhar com certificados de raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md). O artigo vai orientá-lo utilizando o makecert para gerar um certificado autoassinado e, em seguida, exportar o ficheiro .cer.

### <a name="a-nameuploadapart-2-upload-the-root-certificate-cer-file-to-the-azure-classic-portal"></a><a name="upload"></a>Parte 2: Carregar o ficheiro .cer do certificado de raiz para o portal clássico do Azure
Adicionar um certificado fidedigno ao Azure. Quando adiciona um ficheiro com codificação x.509 Base64 (.cer) ao Azure, está a dizer ao Azure para confiar no certificado de raiz que o ficheiro representa.

1. No Portal Clássico do Azure, na página **Certificados** da rede virtual, clique em **Carregar um certificado de raiz**.
2. Na página **Carregar Certificado**, procure o certificado de raiz do ficheiro .cer e clique na marca de verificação.

### <a name="a-namecreateclientcertapart-3-generate-a-client-certificate"></a><a name="createclientcert"></a>Parte 3: Gerar um certificado de cliente
Em seguida, gere certificados de cliente. Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação.

* Se estiver a utilizar uma solução de certificado de empresa, gere um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com',, em vez do formato “DOMÍNIO/nomedeutilizaodor” do NetBIOS. 
* Se estiver a utilizar um certificado de raiz autoassinado, consulte [Trabalhar com certificados de raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md), para gerar um certificado de cliente.

## <a name="a-nameinstallclientcertasection-3-export-and-install-the-client-certificate"></a><a name="installclientcert"></a>Secção 3 - Exportar e instalar o certificado de cliente
Instale um certificado de cliente em cada computador que pretende ligar à rede virtual. É necessário um certificado de cliente para a autenticação. Pode automatizar a instalação do certificado de cliente ou pode instalar manualmente. Os passos seguintes descrevem como exportar e instalar manualmente o certificado de cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr.msc*. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *.pfx*. Certifique-se de que regista ou memoriza a palavra-passe (chave) que define para este certificado.
3. Copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Introduza a palavra-passe, quando solicitado. Não modifique a localização da instalação.

## <a name="a-namevpnclientconfigasection-4-configure-your-vpn-client"></a><a name="vpnclientconfig"></a>Secção 4 - Configurar o cliente VPN
Para ligar à rede virtual, também tem de configurar um cliente VPN. O cliente exige um certificado de cliente e a configuração adequada do cliente VPN para se ligar. Para configurar um cliente VPN, execute os passos seguintes, por ordem.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: criar o pacote de configuração do cliente VPN
1. No portal clássico do Azure, na página do **Dashboard** da sua rede virtual, navegue para o menu de leitura rápida no canto direito. Para consultar a lista de sistemas operativos cliente que são suportados, consulte a secção [Ligações de Ponto a Site](vpn-gateway-vpn-faq.md#point-to-site-connections) das FAQ do Gateway de VPN. O pacote de clientes VPN contém informações de configuração para configurar o software de cliente VPN incorporado no Windows. O pacote não instala software adicional. As definições são específicas para a rede virtual à qual se quer ligar.<br><br>Selecione o pacote de transferência que corresponde ao sistema operativo cliente no qual será instalado:
   
   * Para clientes de 32 bits, selecione **Transferir o Pacote de VPN Cliente de 32 bits**.
   * Para clientes de 64 bits, selecione **Transferir o Pacote de VPN Cliente de 64 bits**.
2. A criação do pacote de clientes demora alguns minutos. Quando o pacote estiver concluído, pode descarregar o ficheiro. O ficheiro *.exe* que transferir pode ser armazenado com segurança no computador local.
3. Depois de gerar e transferir o pacote de clientes VPN do Portal Clássico do Azure, pode instalá-lo no computador cliente a partir do qual pretende ligar-se à rede virtual. Se planear instalar o pacote de clientes VPN em vários computadores cliente, verifique se cada um também tem um certificado de cliente instalado.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Parte 2: instalar o pacote de configuração de VPN no cliente
1. Copie o ficheiro de configuração localmente para o computador que pretende ligar à rede virtual e faça duplo clique no ficheiro .exe. 
2. Depois de instalar o pacote, pode iniciar a ligação VPN. O pacote de configuração não está assinado pela Microsoft. Poderá pretender assinar o pacote utilizando o serviço de assinatura da sua organização ou o pacote pode ser assinado por si através da [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Pode utilizar o pacote sem assinar. No entanto, se o pacote não for assinado, é apresentado um aviso quando instalar o pacote.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostra o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a: 
   
    ![cliente de VPN](./media/vpn-gateway-point-to-site-create/vpn.png "VPN client")

### <a name="part-3-connect-to-azure"></a>Parte 3: ligar ao Azure
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![cliente de VPN 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "VPN client connection")
3. A ligação deve ser agora estabelecida.
   
    ![cliente de VPN 3](./media/vpn-gateway-point-to-site-create/connected.png "VPN client connection 2")

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: verificar a ligação VPN
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
Pode adicionar máquinas virtuais à rede virtual. Veja [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Se pretender obter mais informações sobre Redes Virtuais, veja a página [Documentação das Redes Virtuais](https://azure.microsoft.com/documentation/services/virtual-network/).




<!--HONumber=Nov16_HO2-->


