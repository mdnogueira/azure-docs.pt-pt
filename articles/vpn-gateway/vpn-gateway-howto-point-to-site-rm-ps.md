---
title: "Ligar um computador a uma rede virtual do Azure através de Ponto a Site: PowerShell | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site para ligar de forma segura um computador à sua Rede Virtual do Azure."
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
ms.date: 04/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d340210d799f995cb10a20cf48a9245bbd3bc8d3
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurar uma ligação de Ponto a Site a uma VNet com o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol). As ligações Ponto a Site são úteis quando quer ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O utilizador estabelece a ligação VPN a partir do computador cliente.

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no modelo de implementação Resource Manager através do PowerShell. Para obter mais informações sobre ligações de Ponto a Site, consulte [Point-to-Site FAQ (FAQ sobre Ponto a Site)](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Ligar um computador a uma VNet do Azure - diagrama da ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

Neste cenário, vai criar uma rede virtual com uma ligação de Ponto a Site. As instruções também o ajudam a gerar certificados, que são necessários para esta configuração. Uma ligação P2S é composta pelos seguintes itens: uma VNet com um gateway de VPN, um ficheiro .cer de certificado de raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente. 

Utilizamos os seguintes valores para esta configuração. Definimos as variáveis na secção [1](#declare) do artigo. Pode utilizar os passos como orientação e utilizar os valore sem os alterar ou alterá-los de modo a refletir o seu ambiente. 

### <a name="example"></a>Valores de exemplo
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
* **Servidor DNS: endereço IP** do servidor DNS que pretende utilizar para a resolução de nomes.
* **Nome do GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
* Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações sobre como instalar os cmdlets PowerShell, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). 

## <a name="declare"></a>Parte 1 - Iniciar sessão e definir as variáveis
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
  $DNS = "8.8.8.8"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>Parte 2 – Configurar uma VNet
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
3. Criar a rede virtual. O servidor DNS especificado deve ser um servidor DNS que possa resolver os nomes dos recursos a que se está a ligar. Para este exemplo, utilizámos um endereço IP público. Certifique-se de que utiliza os seus próprios valores.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
  ```
4. Especifique as variáveis para a rede virtual que criou.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. Solicite um endereço IP público atribuído de forma dinâmica. Este endereço IP é necessário para o gateway funcionar corretamente. Mais tarde, vai ligar o gateway à configuração de IP do gateway.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```


## <a name="Certificates"></a>Parte 3 – Certificados

O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Depois de criar o certificado de raiz, pode exportar os dados do certificado público (não a chave privada) como ficheiro .cer X.509 com codificação Base-64. Em seguida, carregue os dados do certificado público do certificado de raiz para o Azure.

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado de raiz e instalado em cada computador cliente. Se não estiver instalado um certificado de cliente válido e o cliente tentar estabelecer ligação com a VNet, a autenticação falhará.

### <a name="cer"></a>Passo 1: Obter o ficheiro .cer para o certificado de raiz

#### <a name="enterprise-certificate"></a>Certificado da empresa
 
Se estiver a utilizar uma solução empresarial, pode servir-se da sua cadeia de certificados existente. Obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar.

#### <a name="self-signed-root-certificate"></a>Certificado de raiz autoassinado

Se não estiver a utilizar uma solução de certificados de empresa, tem de criar um certificado de raiz autoassinado. Para criar um certificado de raiz autoassinado que contenha os campos necessários para autenticação P2S, pode utilizar o PowerShell. [Criar um certificado de raiz autoassinado para ligações Ponto a Site com o PowerShell](vpn-gateway-certificates-point-to-site.md) descreve os passos para criar um certificado de raiz autoassinado.

> [!NOTE]
> Anteriormente, makecert era o método recomendado para criar certificados de raiz autoassinados e gerar certificados de cliente para ligações Ponto a Site. Pode agora utilizar o PowerShell para criar esses certificados. Uma vantagem de utilizar o PowerShell é a capacidade de criar certificados SHA-2. Veja [Criar um certificado de raiz autoassinado para ligações Ponto a Site com o PowerShell](vpn-gateway-certificates-point-to-site.md) para os valores necessários.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar a chave pública para um certificado de raiz autoassinado

As ligações Ponto a Site requerem o carregamento da chave pública (.cer) para o Azure. Os passos seguintes ajudam-no a exportar o ficheiro .cer para o certificado de raiz autoassinado.

1. Para obter um ficheiro .cer a partir do certificado, abra **Gerir certificados de utilizador**.
2. Localize o certificado de raiz autoassinado “P2SRootCert”, normalmente em ''Certificates - Current User\Personal\Certificates'', e clique com o botão direito do rato. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar** para abrir o **Assistente para Exportar Certificados**.
3. No Assistente, clique em **Seguinte**. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
4. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base 64 (.CER).** e, em seguida, clique em **Seguinte**. 
5. Na página **Ficheiro a Exportar**, navegue para “C:”, crie um subdiretório com o nome “cert” e selecione-o. Dê o nome “P2SRootCert.cer” ao ficheiro de certificado e clique em **Guardar**. 
6. Clique em **Seguinte** e, em seguida, em **Concluir** para exportar o certificado. Aparece a mensagem **A exportação foi concluída com êxito**. Clique em **OK** para fechar o assistente.

### <a name="generate"></a>Passo 2: Gerar um certificado de cliente
Pode optar por gerar um certificado exclusivo para cada cliente ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados para cada cliente individual é a capacidade de revogar um único certificado. Caso contrário, se todos estiverem a utilizar o mesmo certificado de cliente e precisar de o revogar, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado se poderem autenticar.

#### <a name="enterprise-certificate"></a>Certificado da empresa
- Se estiver a utilizar uma solução de certificado da empresa, gere um certificado de cliente com o formato do valor de nome comum “name@yourdomain.com”, em vez do formato “nome de domínio/nome de utilizador”.
- Certifique-se de que o certificado de cliente se baseia no modelo de certificado "Utilizador" que tem a "Autenticação de Cliente" como primeiro item na lista de utilização, em vez do Início de Sessão de Smart Card, etc. Pode verificar o certificado ao clicar duas vezes no certificado de cliente e visualizar **Detalhes > Utilização Avançada da Chaves**.

#### <a name="self-signed-root-certificate"></a>Certificado de raiz autoassinado 
Se estiver a utilizar um certificado de raiz autoassinado, veja [Gerar um certificado de cliente com o PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) para obter os passos para gerar um certificado de cliente compatível com Ligações Ponto a Site.


### <a name="exportclientcert"></a>Passo 3: Exportar o certificado de cliente

Se gerar um certificado de cliente a partir de um certificado de raiz autoassinado com as instruções do [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), este é instalado automaticamente no computador que utilizou para gerá-lo. Se quiser instalar um certificado de cliente noutro computador cliente, tem de exportá-lo.
 
1. Para exportar um certificado de cliente, abra **Gerir Certificados de Utilizador**. Clique com o botão direito do rato no certificado de cliente que quer exportar, clique em **Todas as Tarefas** e, em seguida, em **Exportar** para abrir o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**, selecione **Sim, exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Exportar Formato de Ficheiro**, deixe as predefinições selecionadas. Certifique-se de que a opção **Incluir todos os certificados no caminho de certificação, se possível** está selecionada para também exportar a informação necessária do certificado de raiz . Clique depois em **Seguinte**.
4. Na página **Segurança**, tem de proteger a chave privada. Se optar por utilizar uma palavra-passe, certifique-se de que regista ou memoriza a palavra-passe que define para este certificado. Clique depois em **Seguinte**.
5. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
6. Clique em **Concluir** para exportar o certificado.

### <a name="upload"></a>Passo 4: Carregar o ficheiro .cer do certificado de raiz

Depois de o gateway ter sido criado, pode carregar o ficheiro .cer de um certificado de raiz fidedigno para o Azure. Pode carregar ficheiros de um máximo de 20 certificados de raiz. Não carregue a chave privada do certificado de raiz para o Azure. Após o carregamento do ficheiro .cer, o Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. Declare a variável para o nome de certificado, ao substituir o valor pelo seu próprio:

  ```powershell
  $P2SRootCertName = "Mycertificatename.cer"
  ```
2. Substitua o caminho do ficheiro pelo seu e, em seguida, execute os cmdlets.

  ```powershell
  $filePathForCert = "C:\cert\Mycertificatename.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```


## <a name="creategateway"></a>Parte 4 – Criar o gateway de VPN
Configure e crie o gateway de rede virtual da sua VNet. O *-GatewayType* tem de ser **Vpn** e o *-VpnType* tem de ser **RouteBased**. Um gateway de VPN pode demorar até 45 minutos a concluir.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert
```


## <a name="clientconfig"></a>Parte 5 – Transferir o pacote de configuração do cliente VPN
Para ligar a uma VNet com um VPN de Ponto a Site, cada cliente tem de instalar um pacote de configuração de cliente VPN. O pacote não instala um cliente VPN. Configura o cliente VPN do Windows com as definições necessárias para ligar à rede virtual. Para consultar a lista de sistemas operativos cliente que são suportados, consulte [Point-to-Site connections FAQ (FAQ das ligações de Ponto a Site)](#faq) no final deste artigo.

1. Depois de criado o gateway, pode gerar e transferir o pacote de configuração do cliente. Este exemplo transfere o pacote para clientes de 64 bits. Se pretende transferir o cliente de 32 bits, substitua “Amd64” por “x86”. Também pode transferir o cliente VPN ao utilizar o portal do Azure.

  ```powershell
  Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
  -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
  ```
2. Copie e cole a ligação devolvida num browser, para transferir o pacote, não esquecendo de remover o """ à volta da ligação. 
3. Transfira e instale o pacote no computador cliente. Se vir um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**. Também pode guardar o pacote para instalar noutros computadores cliente.
4. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.



## <a name="clientcertificate"></a>Parte 6 - Instalar um certificado de cliente exportado

Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Localize e copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Deixe a **Localização do Arquivo** como **Utilizador Atual** e, em seguida, clique em **Seguinte**.
2. Na página **Ficheiro a importar**, não efetue nenhuma alteração. Clique em **Seguinte**.
3. Na página **Proteção por chave privada**, introduza a palavra-passe para o certificado ou verifique se o principal de segurança está correto e, em seguida, clique em **Seguinte**.
4. Na página **Arquivo de Certificados**, mantenha a localização predefinida e, em seguida, clique em **Seguinte**.
5. Clique em **Concluir**. Em **Aviso de Segurança** para a instalação do certificado, clique em **Sim**. Pode sentir-se à vontade para clicar em ''Sim'', uma vez que gerou o certificado. O certificado foi agora importado com êxito.

## <a name="connect"></a>Parte 7 – Ligar ao Azure
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![O cliente VPN liga-se ao Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. A ligação é estabelecida.
   
    ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

Se estiver a ter problemas em ligar, verifique os seguintes itens:

- Abra **Gerir certificados de utilizador** e navegue para **Autoridades de Certificação de Raiz Fidedigna/Certificados**. Certifique-se de que o certificado de raiz está listado. O certificado de raiz tem de estar presente para que a autenticação funcione. Quando exporta um certificado de cliente .pfx com o valor predefinido “Incluir todos os certificados no caminho de certificação, se possível”, a informação do certificado de raiz também é exportada. Quando instala o certificado de cliente, o certificado de raiz também é instalado no computador cliente. 

- Se estiver a utilizar um certificado que foi emitido através de uma solução de AC Empresarial e se deparou com problemas de autenticação, verifique a ordem de autenticação do certificado de cliente. Pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado de cliente e aceder a **Detalhes > Utilização Avançada de Chaves**. Certifique-se de que a lista apresenta "Autenticação de Cliente" como o primeiro item. Caso contrário, terá de emitir um certificado de cliente com base no modelo de Utilizador que tenha a Autenticação de Cliente como o primeiro item na lista.  


## <a name="verify"></a>Parte 8 – Verificar a ligação
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


## <a name="connectVM"></a>Conectar a uma máquina virtual

1. Depois de ligar à sua VNet, pode ligar a uma VM através da sua ligação de P2S. Para ligar à VM, precisa do endereço IP privado da máquina virtual. O exemplo seguinte ajuda-o a obter o endereço IP privado com [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). Os resultados devolvem uma lista de VMs e endereços IP privados correspondentes em todos os Grupos de Recursos. 

  ```powershell   
  $vms = get-azurermvm
  $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null #skip Nics with no VM
  
  foreach($nic in $nics)
  {
    $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
    $prv =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
    $alloc =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
    Write-Output "$($vm.Name) : $prv , $alloc"
  }
  ```
2. Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP pelo endereço IP privado associado à VM a que se pretende ligar. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual. 

  ```powershell   
  mstsc /v:192.168.1.4
  ```

Se estiver a ter problemas em ligar a uma máquina virtual através de P2S, utilize “ipconfig” para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual se está a ligar. Se o endereço IP estiver no âmbito do intervalo de endereços da VNet a que se está a ligar, ou no âmbito do intervalo de endereços do seu VPNClientAddressPool, tal trata-se de um espaço de endereços sobreposto. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local. Se os seus espaços de endereços de rede não estiverem sobrepostos, mas continua sem conseguir ligar-se à sua VM, veja o artigo [Troubleshoot Remote Desktop connections to a VM (Resolver problemas de ligação do Ambiente de Trabalho Remoto a uma VM)](../virtual-machines/windows/troubleshoot-rdp-connection.md).

## <a name="addremovecert"></a>Adicionar ou remover um certificado de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ligar-se ao Azure via Ponto a Site. Se quiser que os clientes estabeleçam ligação, terá de instalar um novo certificado de cliente que seja gerado a partir de um certificado considerado fidedigno no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna
Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Os seguintes passos ajudam a adicionar um certificado de raiz:

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


### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna


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

## <a name="revoke"></a>Revogar um certificado de cliente
Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. A revogação de um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua. Isto está declarado como uma variável no próximo passo.
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

### <a name="to-reinstate-a-client-certificate"></a>Para restabelecer um certificado de cliente
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

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).

