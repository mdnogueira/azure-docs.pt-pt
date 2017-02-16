---
title: "Configurar uma ligação de gateway de VPN Ponto a Site para uma rede virtual do Azure através do modelo de implementação do Resource Manager | Microsoft Docs"
description: "Ligue-se de forma segura à Rede Virtual do Azure criando uma ligação de gateway de VPN Ponto a Site."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: 41bba6660c52d4aa7d10d846ad65e1f6aa5e582c


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurar uma ligação de Ponto a Site a uma VNet com o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma ligação P2S é útil quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. 

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. Para obter mais informações sobre ligações Ponto a Site, veja [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Planeamento e Conceção](vpn-gateway-plan-design.md). 

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no modelo de implementação Resource Manager através do PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

Neste cenário, irá criar uma rede virtual com uma ligação Ponto a Site. As instruções também o vão ajudar a gerar certificados, que são necessários para esta configuração. Uma ligação P2S é composta pelos seguintes itens: uma VNet com um gateway de VPN, um ficheiro .cer de certificado de raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente. 

Utilizamos os seguintes valores para esta configuração. Definimos as variáveis na secção [1](#declare) do artigo. Pode utilizar os passos como orientação e utilizar os valore sem os alterar ou alterá-los de modo a refletir o seu ambiente. 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Valores de exemplo
* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16** e **10.254.0.0/16**<br>Para este exemplo, utilizamos mais do que um espaço de endereço para ilustrar que esta configuração irá funcionar com vários espaços de endereço. No entanto, vários espaços de endereços não são precisos para esta configuração.
* **Nome da sub-rede: FrontEnd**
  * **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Nome da sub-rede: BackEnd**
  * **Intervalo de endereços da sub-rede: 10.254.1.0/24**
* **Nome da sub-rede: GatewaySubnet**<br>O nome da Sub-rede *GatewaySubnet* é obrigatório para o gateway de VPN funcionar.
  * **Intervalo de endereços da sub-rede: 192.168.200.0/24** 
* **Conjunto de endereços de cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente VPN especificado.
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **Servidor DNS: endereço IP** do servidor DNS que pretende utilizar para a resolução de nomes.
* **Nome do GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
* Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações sobre como instalar os cmdlets PowerShell. Ao trabalhar com o PowerShell para esta configuração, certifique-se de que está a executar como administrador. 

## <a name="a-namedeclareapart-1---log-in-and-set-variables"></a><a name="declare"></a>Parte 1 - Iniciar sessão e definir as variáveis
Nesta secção, vai iniciar sessão e declarar os valores utilizados para esta configuração. Os valores declarados são utilizados nos scripts de exemplo. Altere os valores para refletir o seu ambiente. Em alternativa, pode utilizar os valores declarados e percorrer os passos como um exercício.

1. Na consola do PowerShell, inicie sessão na sua conta do Azure. Este cmdlet pede-lhe as credenciais de início de sessão da sua Conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell.
   
        Login-AzureRmAccount 
2. Obtenha uma lista das suas subscrições do Azure.
   
        Get-AzureRmSubscription
3. Especifique a subscrição que pretende utilizar. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. Declare as variáveis que quer utilizar. Utilize o exemplo seguinte, substituindo os valores pelos seus próprios, quando necessário.
   
        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="a-nameconfigurevnetapart-2---configure-a-vnet"></a><a name="ConfigureVNet"></a>Parte 2 – Configurar uma VNet
1. Crie um grupo de recursos.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. Crie as configurações de sub-rede para a rede virtual, nomeando-as *FrontEnd*, *BackEnd* e *GatewaySubnet*. Estes prefixos têm de fazer parte do espaço de endereços da VNet que declarou.
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. Criar a rede virtual. O servidor DNS especificado deve ser um servidor DNS que possa resolver os nomes dos recursos a que se está a ligar. Para este exemplo, utilizámos um endereço IP público. Certifique-se de que utiliza os seus próprios valores.
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. Especifique as variáveis para a rede virtual que criou.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. Solicite um endereço IP público atribuído de forma dinâmica. Este endereço IP é necessário para o gateway funcionar corretamente. Mais tarde, vai ligar o gateway à configuração de IP do gateway.
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="a-namecertificatesapart-3---certificates"></a><a name="Certificates"></a>Parte 3 – Certificados
O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Exporte os dados do certificado público (e não a chave privada) como um ficheiro .cer X.509 codificado com Base-64 de um certificado de raiz gerado por uma solução de certificados de empresa ou um certificado de raiz autoassinado. Em seguida, importe os dados do certificado público a partir do certificado de raiz para o Azure. Além disso, precisa de gerar um certificado de cliente a partir do certificado de raiz para os clientes. Cada cliente que pretenda ligar-se à rede virtual através de uma ligação de P2S tem de ter instalado um certificado de cliente que foi gerado a partir do certificado de raiz.

### <a name="a-namecera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. Obter o ficheiro .cer para o certificado de raiz
Terá de obter os dados do certificado público para o certificado de raiz que pretende utilizar.

* Se estiver a utilizar um sistema de certificado de empresa, obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar. 
* Se não estiver a utilizar uma solução de certificados de empresa, tem de gerar um certificado de raiz autoassinado. Para obter os passos para o Windows 10, pode ver [Trabalhar com certificados de raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um ficheiro .cer a partir de um certificado, abra **certmgr.msc** e localize o certificado de raiz. Clique no certificado de raiz autoassinado com o botão direito, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**, selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (*.CER).** Clique depois em **Seguinte**. 
4. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
5. Clique em **Concluir** para exportar o certificado.

### <a name="a-namegeneratea2-generate-the-client-certificate"></a><a name="generate"></a>2. Gerar o certificado de cliente
Em seguida, gere certificados de cliente. Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação. Os certificados de cliente são instalados em cada computador cliente posteriormente neste exercício.

* Se estiver a utilizar uma solução de certificado de empresa, gere um certificado de cliente com o formato do valor de nome comum 'name@yourdomain.com',, em vez do formato “DOMÍNIO/nomedeutilizaodor” do NetBIOS. 
* Se estiver a utilizar um certificado de raiz autoassinado, consulte [Trabalhar com certificados de raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md), para gerar um certificado de cliente.

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. Exportar o certificado de cliente
É necessário um certificado de cliente para a autenticação. Após a gerar o certificado de cliente, exporte-o. O certificado de cliente que exportar será instalado posteriormente em cada computador cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr.msc*. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *.pfx*. Certifique-se de que regista ou memoriza a palavra-passe (chave) que define para este certificado.

### <a name="a-nameuploada4-upload-the-root-certificate-cer-file"></a><a name="upload"></a>4. Carregar o ficheiro .cer do certificado de raiz
Declare a variável para o nome de certificado, ao substituir o valor pelo seu próprio:

        $P2SRootCertName = "Mycertificatename.cer"

Adicione os dados do certificado público para o certificado de raiz ao Azure. Pode carregar ficheiros de um máximo de 20 certificados de raiz. Não carregue a chave privada do certificado de raiz para o Azure. Após o carregamento do ficheiro .cer, o Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual. 

Substitua o caminho do ficheiro pelo seu e, em seguida, execute os cmdlets.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4---create-the-vpn-gateway"></a><a name="creategateway"></a>Parte 4 – Criar o gateway de VPN
Configure e crie o gateway de rede virtual da sua VNet. O *-GatewayType* tem de ser **Vpn** e o *-VpnType* tem de ser **RouteBased**. Esta ação pode demorar até 45 minutos a concluir.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5---download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Parte 5 – Transferir o pacote de configuração do cliente VPN
Os clientes que se liguem ao Azure com P2S têm de ter um certificado de cliente e um pacote de instalação de cliente de VPN instalados. Os pacotes de configuração do cliente VPN estão disponíveis para clientes Windows. O pacote de cliente de VPN contém as informações para configurar o software de cliente de VPN que está incluído no Windows e é específico da VPN à qual se quer ligar. O pacote não instala software adicional. Consulte as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. Depois de criado o gateway, pode transferir o pacote de configuração do cliente. Este exemplo transfere o pacote para clientes de 64 bits. Se pretende transferir o cliente de 32 bits, substitua “Amd64” por “x86”. Também pode transferir o cliente VPN ao utilizar o portal do Azure.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. O cmdlet do PowerShell devolve uma hiperligação do URL. Este é um exemplo do aspeto do URL devolvido:
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Copie e cole a ligação devolvida num browser, para transferir o pacote. Em seguida, instale o pacote no computador cliente. Se obtiver um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim** para instalar o pacote.
4. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostrará o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a este exemplo: 
   
    ![cliente de VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="a-nameclientcertificateapart-6---install-the-client-certificate"></a><a name="clientcertificate"></a>Parte 6 – Instalar o certificado de cliente
Cada computador cliente tem de ter um certificado de cliente para se autenticar. Ao instalar o certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Copie o ficheiro .pfx para o computador cliente.
2. Faça duplo clique no ficheiro .pfx para o instalar. Não modifique a localização da instalação.

## <a name="a-nameconnectapart-7---connect-to-azure"></a><a name="connect"></a>Parte 7 – Ligar ao Azure
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![Ligação de cliente VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. A ligação deve ser agora estabelecida.
   
    ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connection established")

## <a name="a-nameverifyapart-8---verify-your-connection"></a><a name="verify"></a>Parte 8 – Verificar a ligação
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

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>Para adicionar ou remover um certificado de raiz fidedigna
Os certificados são utilizados para autenticar clientes VPN para as VPNs Ponto a Site. Os seguintes passos orientam-no para adicionar e remover certificados de raiz. Quando adiciona um ficheiro com codificação x.509 Base64 (.cer) ao Azure, está a dizer ao Azure para confiar no certificado de raiz que o ficheiro representa. 

Pode adicionar ou remover os certificados de raiz fidedigna com o PowerShell ou através do portal do Azure. Se quiser fazê-lo através do portal do Azure, aceda ao **gateway de rede virtual > definições > Configuração de Ponto a Site > Certificados de raiz**. Os passos abaixo descrevem estas tarefas com o PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Adicionar um certificado de raiz fidedigna
Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Siga os passos abaixo para adicionar um certificado de raiz.

1. Crie e prepare o novo certificado de raiz que será adicionado ao Azure. Exporte a chave pública como um X.509 codificado em Base64 (.CER). e abra-o num editor de texto. Em seguida, copie apenas a secção mostrada abaixo. 
   
    Copie os valores, conforme mostrado no exemplo seguinte:
   
    ![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificate")
2. Especifique o nome do certificado e as informações da chave como uma variável. Substitua as informações pelas suas próprias informações, conforme mostrado no exemplo seguinte:
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Adicione o novo certificado de raiz. Apenas pode adicionar um certificado de cada vez.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. Pode verificar se o novo certificado foi adicionado corretamente ao utilizar o cmdlet seguinte.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna
Pode remover o certificado de raiz fidedigna do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ser ligados ao Azure via Ponto a Site. Se pretender que os clientes estabeleçam ligação, terá de instalar um novo certificado de cliente que seja gerado a partir de um certificado considerado fidedigno no Azure.

1. Para remover um certificado de raiz fidedigna, modifique o exemplo seguinte:
   
    Declare as variáveis.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Remova o certificado.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Utilize o cmdlet seguinte para verificar se o certificado foi removido com êxito. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>Para gerir a lista de certificados de cliente revogados
Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Se remover um certificado de raiz .cer do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Se pretender revogar um certificado de cliente específico, e não a raiz, pode fazê-lo. Dessa forma, os outros certificados gerados a partir do certificado de raiz continuarão a ser válidos.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente
1. Obtenha o thumbprint do certificado de cliente a revogar.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "?ef2af033d0686820f5a3c74804d167b88b69982f"
2. Adicione o thumbprint à lista de thumbprint revogado.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Certifique-se de que o thumbprint foi adicionado à lista de revogação de certificados. Tem de adicionar um thumbprint de cada vez.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Restabelecer um certificado de cliente
Pode restabelecer um certificado de cliente, removendo o thumbprint da lista de certificados de cliente revogados.

1. Remova o thumbprint da lista de thumbprint de certificado de cliente revogado.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Verifique se o thumbprint é removido da lista revogada.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Dec16_HO1-->


