---
title: "Ligar um computador a uma rede virtual do Azure através de Ponto a Site e da autenticação de certificados nativa do Azure: PowerShell | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site para ligar de forma segura um computador à sua rede virtual através da autenticação de certificados nativa do Azure do gateway de VPN. Este artigo aplica-se ao modelo de implementação do Resource Manager e utiliza o PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 8c4b2d578a8a586fc63c972ab5da694b2dd9d571
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Configurar uma ligação Ponto a Site a uma VNet com a autenticação de certificados nativa do Azure: PowerShell

Este artigo mostra como criar uma VNet com uma ligação Ponto a Site no modelo de implementação Resource Manager com o PowerShell. Esta configuração utiliza certificados para autenticação. Nesta configuração, o gateway de VPN do Azure realiza a validação de certificados, em vez de um servidor RADIUS. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Um gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. As ligações VPN Ponto a Site são úteis quando pretende ligar à VNet a partir de uma localização remota, como em teletrabalho em casa ou numa conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma Rede de VPNs, quando são poucos os clientes que precisam de ligar a uma VNet. Uma ligação VPN P2S é iniciada a partir dos dispositivos Windows e Mac. 

Os clientes que se ligam podem utilizar os seguintes métodos de autenticação:

* Servidor RADIUS - atualmente em Pré-visualização
* Autenticação de certificados nativa do Azure do Gateway de VPN

Este artigo ajuda-o a configurar uma configuração P2S com autenticação através da autenticação de certificados nativa do Azure. Se quiser utilizar RADIUS para autenticar os utilizadores que se ligam, veja [P2S através da autenticação RADIUS](point-to-site-how-to-radius-ps.md).

![Ligar um computador a uma VNet do Azure - diagrama da ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2snativeps.png)

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol) ou de IKEv2.

* SSTP é um túnel VPN baseado em SSL que é suportado apenas nas plataformas de cliente Windows. Consegue penetrar firewalls, pelo que é uma opção ideal para ligar ao Azure a partir de qualquer lugar. No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição.

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores). O IKEv2 encontra-se em Pré-visualização.

>[!NOTE]
>O IKEv2 para P2S encontra-se em Pré-visualização.
>

As ligações de autenticação de certificados nativa do Azure Ponto a Site precisam do seguinte:

* Um gateway de VPN RouteBased.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Assim que o certificado estiver carregado, é considerado um certificado fidedigno e é utilizado para autenticação.
* Um certificado de cliente que é gerado a partir do certificado de raiz e instalado em cada computador de cliente que se irá ligar à VNet. Este certificado é utilizado para autenticação de cliente.
* Uma configuração de cliente VPN. Os ficheiros de configuração de cliente VPN contêm as informações necessárias para o cliente se ligar à VNet. Os ficheiros configuram o cliente VPN existente que é nativo ao sistema operativo. Cada cliente que estabelece ligação tem de ser configurado com as definições dos ficheiros de configuração.

Para obter mais informações sobre ligações Ponto a Site, veja [Acerca das ligações Ponto a Site](point-to-site-about.md).

## <a name="before-you-begin"></a>Antes de começar

* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
* Instale a versão mais recente dos cmdlets do PowerShell do Resource Manager. Para obter mais informações sobre como instalar os cmdlets PowerShell, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

### <a name="example"></a>Valores de exemplo

Pode utilizar os valores de exemplo para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo. Definimos as variáveis na secção [1](#declare) do artigo. Pode utilizar os passos como orientação e utilizar os valore sem os alterar ou alterá-los de modo a refletir o seu ambiente.

* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16** e **10.254.0.0/16**<br>Para este exemplo, utilizamos mais do que um espaço de endereço para ilustrar que esta configuração funciona com vários espaços de endereço. No entanto, vários espaços de endereços não são precisos para esta configuração.
* **Nome da sub-rede: FrontEnd**
  * **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Nome da sub-rede: BackEnd**
  * **Intervalo de endereços da sub-rede: 10.254.1.0/24**
* **Nome da sub-rede: GatewaySubnet**<br>O nome da Sub-rede *GatewaySubnet* é obrigatório para o gateway de VPN funcionar.
  * **Intervalo de endereços do GatewaySubnet: 192.168.200.0/24** 
* **Conjunto de endereços de cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente VPN especificado.
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **Servidor DNS: endereço IP** do servidor DNS que pretende utilizar para a resolução de nomes. (opcional)
* **Nome do GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased** 

## <a name="declare"></a>1. Iniciar sessão e definir as variáveis

Nesta secção, vai iniciar sessão e declarar os valores utilizados para esta configuração. Os valores declarados são utilizados nos scripts de exemplo. Altere os valores para refletir o seu ambiente. Em alternativa, pode utilizar os valores declarados e percorrer os passos como um exercício.

1. Abra a consola do PowerShell com privilégios elevados e inicie sessão na sua conta do Azure. Este cmdlet pede-lhe as credenciais de início de sessão. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ```
2. Obtenha uma lista das suas subscrições do Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Especifique a subscrição que pretende utilizar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. Declare as variáveis que quer utilizar. Utilize o exemplo seguinte, substituindo os valores pelos seus próprios, quando necessário.

  ```powershell
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
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>2. Configurar uma VNet

1. Crie um grupo de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. Crie as configurações de sub-rede para a rede virtual, nomeando-as *FrontEnd*, *BackEnd* e *GatewaySubnet*. Estes prefixos têm de fazer parte do espaço de endereços da VNet que declarou.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. Criar a rede virtual.

  Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que consiga resolver os nomes dos recursos a que se está a ligar a partir da sua VNet. Para este exemplo, foi utilizado um endereço IP privado, mas é provável que este não seja o endereço IP do servidor DNS. Certifique-se de que utiliza os seus próprios valores. O valor que especificar será utilizado pelos recursos que implementa na VNet, não pela ligação P2S ou o cliente VPN.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Especifique as variáveis para a rede virtual que criou.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. Um gateway de VPN deve ter um endereço IP público. Primeira, requeira o recurso de endereço IP e, em seguida, faça referência ao mesmo ao criar o gateway de rede virtual. O endereço IP é dinamicamente atribuído ao recurso quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. Não é possível pedir uma atribuição de endereço IP Público Estático. No entanto, isto não significa que o endereço IP é alterado depois de ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

  Solicite um endereço IP público atribuído de forma dinâmica.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```

## <a name="creategateway"></a>3. Criar o gateway de VPN

Configure e crie o gateway de rede virtual da sua VNet.

* O *-GatewayType* tem de ser **Vpn** e o *-VpnType* tem de ser **RouteBased**.
* -VpnClientProtocols é utilizado para especificar os tipos de túnel que quer ativar. As duas opções de túnel são **SSTP** e **IKEv2**. Pode optar por ativar uma ou ambas as opções. Se quiser ativar ambas, especifique ambos os nomes separados por vírgula. O cliente Strongswan no Android e Linux e o cliente VPN IKEv2 nativo em dispositivos iOS e OSX utilizarão apenas o túnel IKEv2 para estabelecer a ligação. Os clientes Windows, primeiro, experimentam o IKEv2 e, se não conseguirem estabelecer a ligação, voltam ao SSTP.
* Um gateway de VPN pode demorar até 45 minutos a concluir, consoante a [sku do gateway](vpn-gateway-about-vpn-gateway-settings.md) que selecionar. Neste exemplo, utilizamos o IKEv2, que está atualmente disponível em Pré-visualização.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocols "IKEv2"
```

## <a name="addresspool"></a>4. Adicione um conjunto de endereços do cliente VPN

Depois do gateway VPN acabar de criar, pode adicionar o conjunto de endereços de cliente VPN. O conjunto de endereços do cliente VPN é o intervalo a partir do qual os clientes VPN recebem um endereço IP quando ligam. Utilize um intervalo de endereços IP privados que não se sobreponha à localização no local onde irá estabelecer ligação ou com a VNet a que pretende ligar. Neste exemplo, o conjunto de endereços de cliente VPN está declarado como uma [variável](#declare) no Passo 1.

```powershell
$Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="Certificates"></a>5. Gerar certificados

O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública é então considerada "fidedigna". Os certificados de cliente têm de ser gerados a partir do certificado de raiz fidedigna e, em seguida, instalado em cada computador cliente no arquivo de Certificados Atuais do Utilizador/Pessoais. O certificado é utilizado para autenticar o cliente quando é iniciada uma ligação à VNet. 

Se utilizar certificados autoassinados, eles têm de ser criados com parâmetros específicos. Pode criar um certificado autoassinado com as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) ou, se não tiver o Windows 10, pode utilizar o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante que siga as etapas nas instruções ao gerar os certificados de raiz autoassinados e os certificados de cliente. Caso contrário, os certificados que gerar não serão compatíveis com ligações P2S e irá receber um erro de ligação.

### <a name="cer"></a>1. Obter o ficheiro .cer para o certificado de raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="generate"></a>2. Gerar um certificado de cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>6. Carregar as informações da chave pública do certificado de raiz

Verifique se o gateway de VPN acabou de criar. Assim que estiver pronto, pode carregar o ficheiro .cer (que contém as informações da chave pública) para um certificado de raiz fidedigna no Azure. Depois de o ficheiro .cer ser carregado, o Azure pode utilizá-lo para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado de raiz fidedigna. Pode carregar ficheiros de certificado de raiz fidedigna adicionais - até um total de 20 - mais tarde, se necessário.

1. Declare a variável para o nome de certificado, substituindo o valor pelo seu próprio valor.

  ```powershell
  $P2SRootCertName = "P2SRootCert.cer"
  ```
2. Substitua o caminho do ficheiro pelo seu e, em seguida, execute os cmdlets.

  ```powershell
  $filePathForCert = "C:\cert\P2SRootCert.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```
3. Carregar informações de chaves públicas para o Azure. Depois das informações do certificado serem carregadas, o Azure considera-o como um certificado de raiz fidedigna.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
  ```

## <a name="clientcertificate"></a>7. Instalar um certificado de cliente exportado

Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

Certifique-se de que o certificado de cliente foi exportado como um ficheiro. pfx, juntamente com a cadeia de certificados inteira (que é a predefinição). Caso contrário, as informações do certificado de raiz não estão presentes no computador do cliente e o cliente não conseguirá autenticar corretamente. 

Para obter os passos de instalação, veja [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="clientconfig"></a>8. Configurar o cliente VPN nativo

Os ficheiros de configuração de cliente VPN contêm as definições para configurar dispositivos para ligar a uma VNet através de uma ligação P2S. Para obter instruções sobre como gerar e instalar ficheiros de configuração de cliente VPN, veja [Criar e instalar ficheiros de configuração de cliente VPN para configurações P2S da autenticação de certificados nativa do Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>9. Ligar ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para ligar a partir de um cliente VPN do Windows

1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.

  ![O cliente VPN liga-se ao Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. A ligação é estabelecida.

  ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Resolução de problemas com ligações P2S do cliente Windows

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Para ligar a partir de um cliente VPN do Mac

Na caixa de diálogo Rede, localize o perfil de cliente que quer utilizar e, em seguida, clique em **Ligar**.

  ![Ligação do Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Para verificar a ligação

Estas instruções aplicam-se aos clientes Windows.

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do Conjunto de Endereços de Cliente de VPN de Ponto a Site que especificou na configuração. Os resultados são semelhantes a este exemplo:

  ```
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
  ```

## <a name="connectVM"></a>Ligar a uma máquina virtual

Estas instruções aplicam-se aos clientes Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="addremovecert"></a>Para adicionar ou remover um certificado de raiz

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que têm um certificado gerado a partir do certificado de raiz não podem autenticar e não serão capazes de ligar. Se quiser que um cliente faça a autenticação e estabeleça ligação, terá de instalar um novo certificado de cliente gerado a partir de um certificado de raiz considerado fidedigno (carregado) no Azure.

### <a name="addtrustedroot"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz ao Azure. Os seguintes passos ajudam a adicionar um certificado de raiz:

#### <a name="certmethod1"></a>Método 1

Este é o método mais eficaz para carregar um certificado de raiz.

1. Prepare o ficheiro. cer para carregar:

  ```powershell
  $filePathForCert = "C:\cert\P2SRootCert3.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
  ```
2. Carregue o ficheiro. Só pode carregar um ficheiro de cada vez.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
  ```

3. Para verificar se o ficheiro de certificado foi carregado:

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

#### <a name="certmethod2"></a>Método 2

Este método tem mais passos que o Método 1, mas tem o mesmo resultado. Está incluído no caso de ser necessário ver os dados do certificado.

1. Crie e prepare o novo certificado de raiz a adicionar ao Azure. Exporte a chave pública como um X.509 codificado em Base64 (.CER). e abra-o num editor de texto. Copie os valores, conforme mostrado no exemplo seguinte:

  ![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

  > [!NOTE]
  > Quando copiar os dados de certificado, certifique-se de que copia o texto como uma linha contínua sem símbolos de retorno ou avanços de linha. Poderá ter de modificar a sua vista no editor de texto para "Mostrar Símbolo/Mostrar todos os carateres" para ver os símbolos de retorno ou avanços de linha.
  >
  >

2. Especifique o nome do certificado e as informações da chave como uma variável. Substitua as informações pelas suas próprias informações, conforme mostrado no exemplo seguinte:

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. Adicione o novo certificado de raiz. Apenas pode adicionar um certificado de cada vez.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. Pode verificar se o novo certificado foi adicionado corretamente ao utilizar o seguinte exemplo:

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

### <a name="removerootcert"></a>Para remover um certificado de raiz

1. Declare as variáveis.

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. Remova o certificado.

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. Utilize o exemplo seguinte para verificar se o certificado foi removido com êxito.

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>Para revogar um certificado de cliente

Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. A revogação de um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="revokeclientcert"></a>Revogar um certificado de cliente

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua. Esta cadeia está declarada como uma variável no próximo passo.
3. Declare as variáveis. Certifique-se de que declara o thumbprint que obteve no passo anterior.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. Adicione o thumbprint à lista de certificados revogados. Vê "Êxito" quando o thumbprint for adicionado.

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. Certifique-se de que o thumbprint foi adicionado à lista de revogação de certificados.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. Depois de o thumbprint ser adicionado, o certificado já não pode ser utilizado para ligar. Os clientes que se tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.

### <a name="reinstateclientcert"></a>Para restabelecer um certificado de cliente

Pode restabelecer um certificado de cliente, removendo o thumbprint da lista de certificados de cliente revogados.

1. Declare as variáveis. Certifique-se de que declara o thumbprint correto para o certificado que pretende restabelecer.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. Remova o thumbprint do certificado da lista de revogação de certificados.

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. Verifique se o thumbprint é removido da lista revogada.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>FAQ Ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).