---
title: Configurar uma ligação de gateway de VPN Ponto a Site para uma rede virtual do Azure através do modelo de implementação do Resource Manager | Microsoft Docs
description: Ligue-se de forma segura à Rede Virtual do Azure criando uma ligação de gateway de VPN Ponto a Site.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: cherylmc

---
# Configurar uma ligação Ponto a Site a uma VNet com o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal Clássico](vpn-gateway-point-to-site-create.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma ligação P2S é útil quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. 

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no **modelo de implementação Resource Manager**. Os passos requerem o PowerShell. Atualmente, não pode criar esta solução do princípio ao fim no portal do Azure.

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. Para obter mais informações sobre ligações Ponto a Site, veja [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Planeamento e Conceção](vpn-gateway-plan-design.md).

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modelos de implementação e ferramentas para ligações Ponto a Site**

[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

## Sobre esta configuração
Neste cenário, vai criar uma rede virtual com uma ligação Ponto a Site. As instruções também o vão ajudar a gerar certificados, que são necessários para esta configuração. Uma ligação P2S é composta pelos seguintes itens: uma VNet com um gateway de VPN, um ficheiro .cer de certificado de raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente. 

Utilizamos os seguintes valores para esta configuração. Definimos as variáveis na secção [1](#declare) do artigo. Pode utilizar os passos como orientação e utilizar os valore sem os alterar ou alterá-los de modo a refletir o seu ambiente. 

* Nome: **VNet1**, com os espaços de endereços **192.168.0.0/16** e **10.254.0.0/16**. Tenha em atenção que pode utilizar mais do que um espaço de endereços para uma VNet.
* Nome da sub-rede: **FrontEnd**, utilizando **192.168.1.0/24**
* Nome da sub-rede: **BackEnd**, utilizando **10.254.1.0/24**
* Nome da sub-rede: **GatewaySubnet**, utilizando **192.168.200.0/24**. O nome da Sub-rede *GatewaySubnet* é obrigatório para o gateway funcionar. 
* Conjunto de endereços de cliente VPN: **172.16.201.0/24**. Os clientes VPN que se ligam à VNet através desta ligação de Ponto a Site recebem um endereço IP a partir deste conjunto.
* Subscrição: certifique-se de que tem a subscrição correta caso possua mais do que uma.
* Grupo de Recursos: **TestRG**
* Localização: **E.U.A. Leste**
* Servidor DNS: **endereço IP** do servidor DNS que pretende utilizar para a resolução de nomes.
* Nome de GW: **GW**
* Nome do IP público: **GWIP**
* VpnType: **RouteBased**

## Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets PowerShell do Azure Resource Manager (1.0.2 ou posterior). Veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets PowerShell. Ao trabalhar com o PowerShell para esta configuração, certifique-se de que está a executar como administrador. 

## <a name="declare"></a>Parte 1 - Iniciar sessão e definir as variáveis
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
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $P2SRootCertName = "ARMP2SRootCert.cer"

## Parte 2 - Configurar uma VNet
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

## Parte 3 - Adicionar certificados fidedignos
O Azure autentica os clientes que pretendem ligar através de P2S com certificados. O ficheiro. cer (a chave pública) de um certificado de raiz é importado para o Azure. Quando adiciona um ficheiro com codificação x.509 Base64 (.cer) ao Azure, está a dizer ao Azure para confiar no certificado de raiz que o ficheiro representa.

Se estiver a utilizar uma solução empresarial, pode servir-se da sua cadeia de certificados existente. Se não estiver a utilizar uma solução de AC empresarial, pode criar um certificado de raiz autoassinado. Um método para criar um certificado autoassinado é makecert. Para obter instruções sobre como criar um certificado de raiz autoassinado utilizando *makecert*, consulte [Trabalhar com certificados de raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md).

Independentemente de como obteve o certificado, o ficheiro .cer do certificado é carregado para o Azure e são também gerados certificados de cliente para instalar nos clientes que se querem ligar. O certificado autoassinado não é instalado diretamente no cliente. Pode gerar certificados de cliente mais à frente, na secção [Configuração do cliente](#cc) deste artigo.

Pode adicionar até 20 ficheiros de certificados fidedignos ao Azure. Para obter a chave pública, exporte o certificado como um ficheiro X.509 codificado em Base64 (.cer). Uma forma de exportar para cer. é abrir **certmger.msc** e localizar o certificado em Personal/Certificates. Clique com o botão direito do rato e exporte sem a chave privada como "X.509 codificado em Base-64 (.CER)". Anote o caminho do ficheiro para onde exportou o ficheiro .cer. Este é um exemplo de obtenção da representação de cadeia Base64 do seu certificado. 

Adicione o certificado fidedigno ao Azure. Tem de utilizar o seu próprio caminho do ficheiro .cer neste passo. Preste particular atenção à variável $P2SRootCertName = "ARMP2SRootCert.cer" que definiu na parte 1 deste artigo. Se o nome do seu certificado for diferente, ajuste a variável em conformidade.

        $filePathForCert = "pasteYourCerFilePathHere"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## Parte 4 - Criar o gateway de VPN
Configure e crie o gateway de rede virtual da sua VNet. O *-GatewayType* tem de ser **Vpn** e o *-VpnType* tem de ser **RouteBased**. Esta ação pode demorar até 45 minutos a concluir.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Parte 5 - Transferir o pacote de configuração do cliente VPN
Os clientes que se liguem ao Azure com P2S têm de ter um certificado de cliente e um pacote de instalação de cliente de VPN instalados. Os pacotes de configuração do cliente VPN estão disponíveis para clientes Windows. O pacote de cliente de VPN contém as informações para configurar o software de cliente de VPN que está incluído no Windows e é específico da VPN à qual se quer ligar. O pacote não instala software adicional. Consulte as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. Depois de criado o gateway, utilize o exemplo seguinte para transferir o pacote de configuração do cliente:
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. O cmdlet do PowerShell devolve uma hiperligação do URL. Este é um exemplo do aspeto do URL devolvido:
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Copie e cole a ligação devolvida num browser, para transferir o pacote. Em seguida, instale o pacote no computador cliente.
4. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostrará o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a este exemplo: 
   
    ![cliente de VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="cc"></a>Parte 6 - Gerar o certificado de cliente
Em seguida, gere certificados de cliente. Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação.

* Se estiver a utilizar uma solução de certificado de empresa, gere um certificado de cliente com o formato do valor de nome comum “nome@oseudomínio.com”, em vez do formato “DOMÍNIO/nomedeutilizaodor” do NetBIOS. 
* Se estiver a utilizar um certificado de raiz autoassinado, consulte [Trabalhar com certificados de raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md), para gerar um certificado de cliente.

## Parte 7 - Instalar o certificado de cliente
Instale um certificado de cliente em cada computador que pretende ligar à rede virtual. É necessário um certificado de cliente para a autenticação. Pode automatizar a instalação do certificado de cliente ou pode instalar manualmente. Os passos seguintes descrevem como exportar e instalar manualmente o certificado de cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr.msc*. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *.pfx*. Certifique-se de que regista ou memoriza a palavra-passe (chave) que define para este certificado.
3. Copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Introduza a palavra-passe, quando solicitado. Não modifique a localização da instalação.

## Parte 8 - Ligar ao Azure
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![cliente de VPN 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. A ligação deve ser agora estabelecida.
   
    ![cliente de VPN 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## Parte 9 - Verificar a ligação
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

## Para adicionar ou remover um certificado de raiz fidedigna
Os certificados são utilizados para autenticar clientes VPN para as VPNs Ponto a Site. Os seguintes passos orientam-no para adicionar e remover certificados de raiz. Quando adiciona um ficheiro com codificação x.509 Base64 (.cer) ao Azure, está a dizer ao Azure para confiar no certificado de raiz que o ficheiro representa. 

Pode adicionar ou remover os certificados de raiz fidedigna com o PowerShell ou através do portal do Azure. Se quiser fazê-lo através do portal do Azure, aceda ao **gateway de rede virtual > definições > Configuração de Ponto a Site > Certificados de raiz**. Os passos abaixo descrevem estas tarefas com o PowerShell. 

### Adicionar um certificado de raiz fidedigna
Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Siga os passos abaixo para adicionar um certificado de raiz.

1. Crie e prepare o novo certificado de raiz que será adicionado ao Azure. Exporte a chave pública como um X.509 codificado em Base64 (.CER). e abra-o num editor de texto. Em seguida, copie apenas a secção mostrada abaixo. 
   
    Copie os valores, conforme mostrado no exemplo seguinte:
   
    ![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificate")
2. Especifique o nome do certificado e as informações da chave como uma variável. Substitua as informações pelas suas próprias informações, conforme mostrado no exemplo seguinte:
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Adicione o novo certificado de raiz. Apenas pode adicionar um certificado de cada vez.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. Pode verificar se o novo certificado foi adicionado corretamente ao utilizar o cmdlet seguinte.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "GW"

### Para remover um certificado de raiz fidedigna
Pode remover o certificado de raiz fidedigna do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ser ligados ao Azure via Ponto a Site. Se pretender que os clientes estabeleçam ligação, terá de instalar um novo certificado de cliente que seja gerado a partir de um certificado considerado fidedigno no Azure.

1. Para remover um certificado de raiz fidedigna, modifique o exemplo seguinte:
   
    Declare as variáveis.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Remover o certificado
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Utilize o cmdlet seguinte para verificar se o certificado foi removido com êxito. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "GW"

## Para gerir a lista de certificados de cliente revogados
Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Se remover um certificado de raiz .cer do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Se pretender revogar um certificado de cliente específico, e não a raiz, pode fazê-lo. Dessa forma, os outros certificados gerados a partir do certificado de raiz continuarão a ser válidos. A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### Revogar um certificado de cliente
1. Obtenha o thumbprint do certificado de cliente a revogar.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"
2. Adicione o thumbprint à lista de thumbprint revogado.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Certifique-se de que o thumbprint foi adicionado à lista de revogação de certificados. Tem de adicionar um thumbprint de cada vez.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Restabelecer um certificado de cliente
Pode restabelecer um certificado de cliente, removendo o thumbprint da lista de certificados de cliente revogados.

1. Remova o thumbprint da lista de thumbprint de certificado de cliente revogado.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Verifique se o thumbprint é removido da lista revogada.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Passos seguintes
Pode adicionar uma máquina virtual à rede virtual. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.

<!--HONumber=Sep16_HO3-->


