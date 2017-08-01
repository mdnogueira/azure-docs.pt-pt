---
title: "Ligar um computador a uma rede virtual do Azure através de Ponto a Site: Portal | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site com o Resource Manager e o portal do Azure para se ligar à sua Rede Virtual do Azure em segurança."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 4045dd501ba70b04cb8ea06901c76072fc009018
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma ligação Ponto a Site a uma VNet com o portal do Azure

Este artigo mostra como criar uma VNet com uma ligação Ponto a Site no modelo de implementação Resource Manager com o portal do Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. As ligações Ponto a Site são úteis quando quer ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. A ligação VPN de P2S é iniciada no computador cliente com o cliente VPN do Windows nativo. Os clientes de ligação utilizam certificados para autenticar. 


![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol). No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição. Para obter mais informações sobre ligações de Ponto a Site, consulte [Point-to-Site FAQ (FAQ sobre Ponto a Site)](#faq) no final deste artigo.

As ligações de P2S requerem o seguinte:

* Um gateway de VPN RouteBased.
* A chave pública (ficheiro .cer) de um certificado de raiz carregado para o Azure. Isto é considerado um certificado fidedigno e é utilizado para autenticação.
* Um certificado de cliente gerado a partir do certificado de raiz e instalado em cada computador cliente que irá ligar. Este certificado é utilizado para autenticação de cliente.
* Um pacote de configuração de cliente VPN tem de estar gerado e instalado em todos os computadores cliente que estabelece ligação. O pacote de configuração do cliente configura o cliente VPN nativo que já se encontra no sistema operativo com as informações necessárias para estabelecer uma ligação à VNet.

### <a name="example"></a>Valores de exemplo

Pode utilizar os valores seguintes para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo:

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

## <a name="createvnet"></a>1 - Criar uma rede virtual

Antes de começar, verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial). Se estiver a criar esta configuração como um exercício, pode ver os [valores de exemplo](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>2 - Especificar espaços de endereços e sub-redes

Pode adicionar sub-redes e espaços de endereços adicionais à VNet depois de ter sido criada.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3 - Adicionar uma sub-rede do gateway

Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. Os serviços do gateway utilizam os endereços IP especificados na sub-rede de gateway. Se possível, crie uma sub-rede do gateway com um bloco CIDR de /28 ou /27, para fornecer endereços IP suficientes para contemplar requisitos de configuração futuros adicionais.

As capturas de ecrã nesta secção são fornecidas como um exemplo de referência. Certifique-se de que utiliza o intervalo de endereços GatewaySubnet correspondente aos valores necessários para a sua configuração.

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4 - Especificar um servidor DNS (opcional)

Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS, para lidar com a resolução de nomes. O servidor DNS especificado deve ser um servidor DNS que possa resolver os nomes dos recursos a que se está a ligar. O pacote de configuração de cliente VPN que irá gerar num passo mais à frente irá conter os endereços IP dos servidores DNS que especificar nesta definição. Se precisar de atualizar a lista de servidores DNS no futuro, pode gerar e instalar novos pacotes de configuração de cliente VPN que reflitam a nova lista.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>5 - Criar um gateway de rede virtual

As ligações ponto a site exigem as seguintes definições:

* Tipo de gateway: VPN
* Tipo de VPN: baseado na rota

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[!INCLUDE [create a vnet gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>6 - Gerar certificados

O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública é então considerada "fidedigna". Os certificados de cliente têm de ser gerados a partir do certificado de raiz fidedigna e, em seguida, instalado em cada computador cliente no arquivo de Certificados Atuais do Utilizador/Pessoais. O certificado é utilizado para autenticar o cliente quando é iniciada uma ligação à VNet. 

Se utilizar certificados autoassinados, eles têm de ser criados com parâmetros específicos. Pode criar um certificado autoassinado com as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante que siga os passos nestas instruções ao trabalhar com os certificados de raiz autoassinados e ao gerar certificados de cliente a partir do certificado de raiz autoassinado. Caso contrário, os certificados que criar não serão compatíveis com ligações de P2S e irá receber um erro de ligação.

### <a name="getcer"></a>Passo 1: Obter o ficheiro .cer para o certificado de raiz

[!INCLUDE [obtain root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>Passo 2: Gerar um certificado de cliente

[!INCLUDE [generate client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>7 - Adicionar o conjunto de endereços de cliente

O conjunto de endereços de cliente é um conjunto de endereços IP privados que especificar. Os clientes que ligam através do P2S recebem um endereço IP a partir deste intervalo. Utilize um intervalo de endereços IP privados que não se sobrepõe à localização no local onde irá estabelecer ligação ou a VNet a que pretende ligar.

1. Quando o gateway de rede virtual tiver sido criado, navegue para a secção **Definições** do painel do gateway de rede virtual. Na secção **Definições**, clique em **Configuração ponto a site** para abrir o painel **Configuração**.

  ![Painel Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. Pode eliminar o intervalo preenchido automaticamente e depois adicionar o intervalo de endereços IP privados que pretende utilizar. Clique em **Guardar** para validar e guardar a definição.

  ![Conjunto de endereços de cliente](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>8 - Carregar o ficheiro .cer do certificado de raiz

Após o gateway ser criado, pode carregar o ficheiro .cer (que contém as informações da chave pública) para um certificado de raiz fidedigna no Azure. Depois de o ficheiro .cer ser carregado, o Azure pode utilizá-lo para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado de raiz fidedigna. Pode carregar ficheiros de certificado de raiz fidedigna adicionais - até um total de 20 - mais tarde, se necessário. 

1. Os certificados são adicionados no painel **Configuração ponto a site** na secção **Certificado de raiz**.  
2. Certifique-se de que exportou o certificado de raiz como um ficheiro X.509 codificado com Base-64 (.cer). Tem de exportar o certificado neste formato para poder abrir o certificado com o editor de texto.
3. Abra o certificado com um editor de texto, como o Bloco de Notas. Quando copiar os dados de certificado, certifique-se de que copia o texto como uma linha contínua sem símbolos de retorno ou avanços de linha. Poderá ter de modificar a sua vista no editor de texto para "Mostrar Símbolo/Mostrar todos os carateres" para ver os símbolos de retorno ou avanços de linha. Copie apenas a secção seguinte como uma linha contínua:

  ![Dados de certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. Cole os dados de certificado no campo **Dados de Certificado Público**. **Nomeie** o certificado e, em seguida, clique em **Guardar**. Pode adicionar até 20 certificados de raiz fidedigna.

  ![Carregamento de certificados](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>9 - Instalar o pacote de configuração do cliente VPN

Para ligar a uma VNet com um VPN de Ponto a Site, cada cliente tem de instalar um pacote para configurar o cliente de VPN do Windows nativo. O pacote de configuração configura o cliente VPN do Windows nativo com as definições necessárias para estabelecer ligação à rede virtual e, se especificou um servidor DNS para a sua VNet, contém o endereço IP do servidor DNS que o cliente irá utilizar para a resolução de nomes. Se alterar o servidor DNS especificado mais tarde, depois de gerar o pacote de configuração de cliente, certifique-se de que gera um novo pacote de configuração de cliente para instalar nos seus computadores cliente.

Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente. Para consultar a lista de sistemas operativos cliente que são suportados, consulte [Point-to-Site connections FAQ (FAQ das ligações de Ponto a Site)](#faq) no final deste artigo.

### <a name="step-1---download-the-client-configuration-package"></a>Passo 1 - transferir o pacote de configuração do cliente

1. No painel **Configuração ponto a site**, clique em **Transferir cliente VPN** para abrir o painel **Transferir cliente VPN**. Demora um minuto ou dois para o pacote ser gerado.

  ![transferência do cliente VPN 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Selecione o pacote correto para o cliente e, em seguida, clique em **Transferir**. Guarde o ficheiro do pacote de configuração. Instale o pacote de configuração do cliente VPN em cada computador cliente que se liga à rede virtual.

  ![transferência do cliente VPN 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>Passo 2 - instalar o pacote de configuração do cliente

1. Copie o ficheiro de configuração localmente para o computador que pretende ligar à rede virtual. 
2. Faça duplo clique no ficheiro .exe para instalar o pacote no computador cliente. Como o pacote de configuração foi criado por si, o mesmo não está assinado, pelo que poderá ver um aviso. Se obtiver um pop-up SmartScreen do Windows, clique em **Mais informações** (no lado esquerdo) e, em seguida, em **Executar mesmo assim** para instalar o pacote.
3. Instale o pacote no computador cliente. Se obtiver um pop-up SmartScreen do Windows, clique em **Mais informações** (no lado esquerdo) e, em seguida, em **Executar mesmo assim** para instalar o pacote.
4. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.

## <a name="installclientcert"></a>10 - Instalar o certificado de cliente

Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado. Normalmente, basta fazer duplo clique no certificado e instalá-lo. Para obter mais informações, veja [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install)(Instalar um certificado de cliente exportado).

## <a name="connect"></a>11 - Ligar ao Azure

1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Clique em **Continuar** para utilizar privilégios elevados.

2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.

  ![O cliente VPN liga-se ao Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. A ligação é estabelecida.

  ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

Se estiver a ter problemas em ligar, verifique os seguintes itens:

- Abra **Gerir certificados de utilizador** e navegue para **Autoridades de Certificação de Raiz Fidedigna/Certificados**. Certifique-se de que o certificado de raiz está listado. O certificado de raiz tem de estar presente para que a autenticação funcione. Quando exporta um certificado de cliente .pfx com o valor predefinido “Incluir todos os certificados no caminho de certificação, se possível”, a informação do certificado de raiz também é exportada. Quando instala o certificado de cliente, o certificado de raiz também é instalado no computador cliente. 

- Se estiver a utilizar um certificado que foi emitido através de uma solução de AC Empresarial e se deparou com problemas de autenticação, verifique a ordem de autenticação do certificado de cliente. Pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado de cliente e aceder a **Detalhes > Utilização Avançada de Chaves**. Certifique-se de que a lista apresenta "Autenticação de Cliente" como o primeiro item. Caso contrário, terá de emitir um certificado de cliente com base no modelo de Utilizador que tenha a Autenticação de Cliente como o primeiro item na lista.


## <a name="verify"></a>12 - Verificar a ligação

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do Conjunto de Endereços de Cliente de VPN de Ponto a Site que especificou na configuração. Os resultados são semelhantes a este exemplo:
   
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


Se estiver a ter problemas em ligar a uma máquina virtual através de P2S, utilize “ipconfig” para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual se está a ligar. Se o endereço IP estiver no âmbito do intervalo de endereços da VNet a que se está a ligar, ou no âmbito do intervalo de endereços do seu VPNClientAddressPool, tal trata-se de um espaço de endereços sobreposto. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local. Se os seus espaços de endereços de rede não estiverem sobrepostos, mas continua sem conseguir ligar-se à sua VM, veja o artigo [Troubleshoot Remote Desktop connections to a VM (Resolver problemas de ligação do Ambiente de Trabalho Remoto a uma VM)](../virtual-machines/windows/troubleshoot-rdp-connection.md).

## <a name="connectVM"></a>Conectar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>Adicionar ou remover certificados de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que tenham um certificado gerado a partir dessa raiz não conseguirão autenticar-se e, por conseguinte, não conseguirão estabelecer ligação. Se quiser que um cliente faça a autenticação e estabeleça ligação, terá de instalar um novo certificado de cliente gerado a partir de um certificado de raiz considerado fidedigno (carregado) no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, veja a secção [Carregar um certificado de raiz fidedigna](#uploadfile) neste artigo.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna

1. Para remover um certificado de raiz fidedigna, navegue para o painel **Configuração Ponto a Site** do gateway de rede virtual.
2. Na secção **Certificado de raiz** do painel, localize o certificado que pretende remover.
3. Clique nas reticências junto ao certificado e, em seguida, clique em “Remover”.

## <a name="revokeclient"></a>Revogar um certificado de cliente

Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. A revogação de um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua.
3. Navegue para o painel **Configuração Ponto a Site** do gateway de rede virtual. Este é o mesmo painel que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).
4. Na secção **Certificados revogados**, introduza um nome amigável para o certificado (não tem de ser o CN do certificado).
5. Copie e cole a cadeia de thumbprint para o campo **Thumbprint**.
6. O thumbprint será validado e adicionado automaticamente à lista de revogação. Verá uma mensagem no ecrã a indicar que a lista está a atualizar. 
7. Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que se tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.

## <a name="faq"></a>FAQ Ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).

