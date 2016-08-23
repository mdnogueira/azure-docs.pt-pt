<properties
   pageTitle="Criar uma ligação do VPN Gateway VNet a VNet através do Azure Resource Manager e do PowerShell para VNets | Microsoft Azure"
   description="Este artigo explica como ligar redes virtuais entre si através do Azure Resource Manager e do PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="cherylmc"/>

# Configurar uma ligação VNet a VNet com o Azure Resource Manager e o PowerShell

> [AZURE.SELECTOR]
- [Portal Clássico do Azure](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell – Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Este artigo descreve os passos para criar uma ligação entre redes virtuais com o modelo de implementação Resource Manager e o PowerShell. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes, das mesmas subscrições ou de subscrições diferentes.

[AZURE.INCLUDE [vpn-gateway-peering](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


**Acerca dos modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelos de implementação e ferramentas para VNet a VNet**

Pode configurar uma ligação VNet a VNet em ambos os modelos de implementação e com várias ferramentas. Veja a tabela abaixo para obter mais informações. Atualizamos esta tabela à medida que novos artigos, novos modelos de implementação e ferramentas adicionais ficam disponíveis para esta configuração. Quando estiver disponível um artigo, criamos uma ligação direta na tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## Acerca das ligações VNet a VNet

A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é muito semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN do Azure para fornecer um túnel seguro através de IPsec/IKE. As VNets que liga podem estar em regiões diferentes. Ou em subscrições diferentes. Pode, inclusive, combinar uma comunicação VNet a VNet com configurações multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual, como mostrado no diagrama abaixo. 


![Acerca das ligações](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Por que motivo ligar redes virtuais?

Poderá pretender ligar redes virtuais pelos seguintes motivos:

- **Geopresença e georredundância entre várias regiões**
    - Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
    - Com o Load Balancer e o Traffic Manager do Azure, pode configurar uma elevada carga de trabalho com georredundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.

- **Aplicações multicamadas regionais com isolamento ou limites administrativos**
    - Na mesma região, pode configurar aplicações de várias camadas com várias redes virtuais ligadas em conjunto devido ao isolamento ou a requisitos administrativos.


### FAQ da ligação VNet a VNet

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


## Que conjunto de passos devo utilizar?

Neste artigo, são apresentados diferentes conjuntos de passos que se aplicam a VNets criadas com o modelo de implementação Resource Manager. Os passos de configuração VNet a VNet dependem do facto de as VNets residirem na mesma subscrição ou em diferentes subscrições. 

![Ambas as ligações](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


Nos passos de configuração, a principal diferença entre os dois é se pode criar e configurar todos os recursos de gateway e rede virtual na mesma sessão do PowerShell. Este documento inclui passos para cada um. O gráfico acima mostra uma ligação VNet a VNet da mesma subscrição e uma ligação VNet a VNet entre subscrições. 


- [VNets que residem na mesma subscrição](#samesub)
- [VNets que residem em subscrições diferentes](#difsub)


## <a name="samesub"></a>Como ligar VNets que estão na mesma subscrição

Esta configuração aplica-se a redes virtuais que estão na mesma subscrição, como mostrado no diagrama abaixo:

![VNet2VNet na mesma subscrição](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Antes de começar

- Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Terá de instalar os cmdlets PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets PowerShell.

### <a name="Step1"></a>Passo 1 – Planear os intervalos de endereços IP


É importante decidir os intervalos que irá utilizar para definir a sua configuração de rede. Nota: precisa confirmar que nenhum dos intervalos de VNet ou intervalos de rede local se sobrepõe de modo algum.

Nos passos abaixo, vamos criar duas redes virtuais juntamente com as respetivas sub-redes de gateway e configurações. Em seguida, vamos criar uma ligação de gateway de VPN entre as duas VNets.

Para este exercício, utilize os seguintes valores para as VNets:

**Valores da TestVNet1:**

- Nome da VNet: TestVNet1
- Grupo de Recursos: TestRG1
- Localização: EUA Leste
- TestVNet1: 10.11.0.0/16 e 10.12.0.0/16
- Front-End: 10.11.0.0/24
- Back-End: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet1GW
- IP Público: VNet1GWIP
- VPNType: RouteBased
- Ligação (1 a 4): VNet1toVNet4
- Ligação (1 a 5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Valores da TestVNet4:**

- Nome da VNet: TestVNet4
- TestVNet2: 10.41.0.0/16 e 10.42.0.0/16
- Front-End: 10.41.0.0/24
- Back-End: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0/27
- Grupo de Recursos: TestRG4
- Localização: EUA Oeste
- Servidor DNS: 8.8.8.8
- GatewayName: VNet4GW
- IP Público: VNet4GWIP
- VPNType: RouteBased
- Ligação: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name="Step2"></a>Passo 2 – Criar e configurar a TestVNet1

1. Declarar as variáveis

    Para este exercício, vamos começar por declarar as nossas variáveis. O exemplo abaixo declara as variáveis com os valores deste exercício. Verifique se substitui os valores pelos seus quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modifique as variáveis e, em seguida, copie e cole a consola do PowerShell.

        $Sub1          = "Replace_With_Your_Subcription_Name"
        $RG1           = "TestRG1"
        $Location1     = "East US"
        $VNetName1     = "TestVNet1"
        $FESubName1    = "FrontEnd"
        $BESubName1    = "Backend"
        $GWSubName1    = "GatewaySubnet"
        $VNetPrefix11  = "10.11.0.0/16"
        $VNetPrefix12  = "10.12.0.0/16"
        $FESubPrefix1  = "10.11.0.0/24"
        $BESubPrefix1  = "10.12.0.0/24"
        $GWSubPrefix1  = "10.12.255.0/27"
        $DNS1          = "8.8.8.8"
        $GWName1       = "VNet1GW"
        $GWIPName1     = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14  = "VNet1toVNet4"
        $Connection15  = "VNet1toVNet5"

2. Ligar à subscrição 1

    Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

    Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

        Login-AzureRmAccount

    Verifique as subscrições da conta.

        Get-AzureRmSubscription 

    Especifique a subscrição que pretende utilizar.

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. Criar um novo grupo de recursos

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Criar as configurações de sub-rede da TestVNet1

    O exemplo abaixo cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, Front-end e Back-end. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falhará. 

    No exemplo abaixo, a sub-rede do gateway está a utilizar /27. Embora, tecnicamente, possa criar uma sub-rede do gateway com uma sub-rede tão pequena como /29, não aconselhamos esta ação. Recomendamos que utilize possivelmente algo maior, tal como /27 ou /26, caso pretenda tirar partido das configurações existentes ou futuras que podem necessitar de uma sub-rede do gateway maior. 


        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Criar a TestVNet1

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Solicitar um endereço IP público

    Em seguida, deverá solicitar um endereço IP público para ser atribuído ao gateway que será criado para a VNet. Não pode especificar o endereço IP que pretende utilizar. Está dinamicamente atribuído ao gateway. Utilizará este endereço IP na secção de configuração seguinte. Utilize o exemplo abaixo. O Método de Alocação deste endereço tem de ser Dinâmico. 

        $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Criar a configuração do gateway

    A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo abaixo para criar a configuração do gateway. 

        $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Criar o gateway da TestVNet1

    Neste passo, vai criar o gateway de rede virtual da TestVNet1. As configurações VNet a VNet requerem um VpnType RouteBased. A criação de um gateway pode demorar algum tempo (30 minutos ou mais).

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Passo 3 – Criar e configurar a TestVNet4

Assim que tiver configurado a TestVNet1, deverá repetir os passos para criar a TestVNet4. Siga os passos abaixo, substituindo os valores pelos seus, quando necessário. Este passo pode ser feito na mesma sessão do PowerShell porque se encontra na mesma subscrição.

1. Declarar as variáveis

    Confirme que substitui os valores pelos que pretende utilizar para a configuração.

        $RG4           = "TestRG4"
        $Location4     = "West US"
        $VnetName4     = "TestVNet4"
        $FESubName4    = "FrontEnd"
        $BESubName4    = "Backend"
        $GWSubName4    = "GatewaySubnet"
        $VnetPrefix41  = "10.41.0.0/16"
        $VnetPrefix42  = "10.42.0.0/16"
        $FESubPrefix4  = "10.41.0.0/24"
        $BESubPrefix4  = "10.42.0.0/24"
        $GWSubPrefix4  = "10.42.255.0/27"
        $DNS4          = "8.8.8.8"
        $GWName4       = "VNet4GW"
        $GWIPName4     = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41  = "VNet4toVNet1"

    Antes de continuar, verifique se ainda está ligado à Subscrição 1.

2. Criar um novo grupo de recursos

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Criar as configurações de sub-rede da TestVNet4

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Criar a TestVNet4

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Solicitar um endereço IP público

        $gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Criar a configuração do gateway

        $vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Criar o gateway da TestVNet4

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Passo 4 – Ligar os gateways

1. Obter os gateways da rede virtual

    Neste exemplo, uma vez que ambos os gateways estão na mesma subscrição, este passo pode ser concluído na mesma sessão do PowerShell.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Criar a ligação da TestVNet1 para a TestVNet4

    Neste passo, vai criar a ligação da TestVNet1 para a TestVNet4. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Criar a ligação da TestVNet4 para a TestVNet1

    Este passo é semelhante ao anterior, exceto que está a criar a ligação da TestVNet4 para a TestVNet1. Verifique se as chaves partilhadas correspondem.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    Após alguns minutos, a ligação deverá ser estabelecida.

## <a name="Verify"></a>Como verificar a ligação VNet a VNet

Os exemplos abaixo mostram-lhe como verificar a ligação. Confirme que altera os valores para corresponder ao seu ambiente.

### Para verificar a ligação com o Portal do Azure

Para verificar uma ligação VPN no Portal do Azure, aceda a **Gateways da rede virtual** -> **clique no nome do seu gateway** -> **Definições** -> **Ligações**. Ao selecionar o nome da ligação, pode ver mais informações no painel **Ligação**. 


### Para verificar a ligação com o PowerShell

Também pode verificar se a ligação teve êxito com *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Pode utilizar o seguinte exemplo, alterando os valores para corresponderem aos seus. Quando lhe for pedido, selecione A para executar Todos.

    Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

Após concluir o cmdlet, veja os valores. No exemplo de saída do PowerShell abaixo, o estado da ligação é apresentado como *Ligado* e pode ver bytes de entrada e de saída.



    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    LocalNetworkGateway2       :
    Peer                       :
    ConnectionType             : Vnet2Vnet
    RoutingWeight              : 0
    SharedKey                  : AzureA1b2C3
    ConnectionStatus           : Connected
    EgressBytesTransferred     : 1376
    IngressBytesTransferred    : 1232
    ProvisioningState          : Succeeded
    VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
    VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
    LocalNetworkGateway2Text   :
    PeerText                   :
    ResourceGroupName          : TestRG1
    Location                   : eastus
    ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
    Tag                        : {}
    TagsTable                  :
    Name                       : VNet1toVNet4
    Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name="difsub"></a>Como ligar VNets que estão em subscrições diferentes

Os passos de configuração abaixo adicionam uma ligação VNet a VNet adicional para ligar a TestVNet1 à TestVNet5, que reside numa subscrição diferente. A diferença aqui é que alguns dos passos de configuração têm de ser efetuados numa sessão separada do PowerShell no contexto da segunda subscrição, especialmente quando as duas subscrições pertencem a diferentes organizações. Após concluir os passos abaixo, a configuração resultante é mostrada no diagrama abaixo:

![Subscrições VNet2VNet diferentes.](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

As instruções abaixo são a continuação dos passos anteriores listados acima. Tem de concluir o [Passo 1](#Step1) e o [Passo 2](#Step2) para criar e configurar a TestVNet1 e o VPN Gateway da TestVNet1. Se pretender ligar apenas VNets em diferentes subscrições, não precisa de se preocupar com os passos 3 e 4 do exercício anterior e pode avançar para o passo 5 abaixo. 

### Passo 5 – Verificar os intervalos de endereços IP adicionais

É importante garantir que o espaço de endereços IP da nova rede virtual, TestVNet5, não se sobrepõe a qualquer um dos intervalos de VNets ou intervalos de gateways de rede local. 

Neste exemplo, as redes virtuais poderão pertencer a diferentes organizações. Para este exercício, pode utilizar os seguintes valores para a TestVNet5:

**Valores da TestVNet5:**

- Nome da VNet: TestVNet5
- Grupo de Recursos: TestRG5
- Localização: Leste do Japão
- TestVNet5: 10.51.0.0/16 e 10.52.0.0/16
- Front-End: 10.51.0.0/24
- Back-End: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet5GW
- IP Público: VNet5GWIP
- VPNType: RouteBased
- Ligação: VNet5toVNet1
- ConnectionType: VNet2VNet

**Valores Adicionais da TestVNet1:**

- Ligação: VNet1toVNet5


### Passo 6 – Criar e configurar a TestVNet5

Este passo tem de ser realizado no contexto da nova subscrição. Esta parte pode ser realizada pelo administrador numa organização diferente proprietária da subscrição.

1. Declarar as variáveis

    Confirme que substitui os valores pelos que pretende utilizar para a configuração.

        $Sub5          = "Replace_With_the_New_Subcription_Name"
        $RG5           = "TestRG5"
        $Location5     = "Japan East"
        $VnetName5     = "TestVNet5"
        $FESubName5    = "FrontEnd"
        $BESubName5    = "Backend"
        $GWSubName5    = "GatewaySubnet"
        $VnetPrefix51  = "10.51.0.0/16"
        $VnetPrefix52  = "10.52.0.0/16"
        $FESubPrefix5  = "10.51.0.0/24"
        $BESubPrefix5  = "10.52.0.0/24"
        $GWSubPrefix5  = "10.52.255.0/27"
        $DNS5          = "8.8.8.8"
        $GWName5       = "VNet5GW"
        $GWIPName5     = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51  = "VNet5toVNet1"

2. Estabelecer ligação à subscrição 5

    Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

        Login-AzureRmAccount

    Verifique as subscrições da conta.

        Get-AzureRmSubscription 

    Especifique a subscrição que pretende utilizar.

        Select-AzureRmSubscription -SubscriptionName $Sub5

3. Criar um novo grupo de recursos

        New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Criar as configurações de sub-rede da TestVNet4
    
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Criar a TestVNet5

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Solicitar um endereço IP público

        $gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Criar a configuração do gateway

        $vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Criar o gateway da TestVNet5

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Passo 7 – Ligar os gateways

Neste exemplo, uma vez que os gateways estão em subscrições diferentes, dividimos este passo em duas sessões do PowerShell marcadas como [Subscrição 1] e [Subscrição 5].

1. **[Subscrição 1]** Obter o gateway de rede virtual da Subscrição 1

    Confirme que inicia sessão e se liga à Subscrição 1.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

    Copie a saída dos seguintes elementos e envie-os ao administrador da Subscrição 5 por e-mail ou outro método.

        $vnet1gw.Name
        $vnet1gw.Id

    Estes dois elementos terão valores semelhantes ao seguinte exemplo de saída:

        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Subscrição 5]** Obter o gateway de rede virtual da Subscrição 5

    Confirme que inicia sessão e estabelece ligação à Subscrição 5.

        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

    Copie a saída dos seguintes elementos e envie-os para o administrador da Subscrição 1 por e-mail ou outro método.

        $vnet5gw.Name
        $vnet5gw.Id

    Estes dois elementos terão valores semelhantes ao seguinte exemplo de saída:

        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Subscrição 1]** Criar a ligação da TestVNet1 para a TestVNet5

    Neste passo, vai criar a ligação da TestVNet1 para a TestVNet5. A diferença aqui é que $vnet5gw não pode ser obtido diretamente porque se está numa subscrição diferente. Terá de criar um novo objeto do PowerShell com os valores comunicados da Subscrição 1 nos passos acima. Substitua o Nome, o ID e a chave partilhada pelos seus valores. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

    Verifique se estabelece ligação à Subscrição 1. 
    
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Subscrição 5]** Criar a ligação da TestVNet5 para a TestVNet1

    Este passo é semelhante ao de cima, exceto que está a criar a ligação da TestVNet5 para a TestVNet1. Aplica-se também aqui o mesmo processo de criação de um objeto do PowerShell basedo nos valores obtidos na Subscrição 1. Neste passo, verifique se as chaves partilhadas correspondem.

    Confira se estabelece ligação à Subscrição 5.

        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Verificar a ligação

    Após concluir estes passos, pode verificar a ligação utilizando os métodos em [Como verificar uma ligação VNet a VNet](#Verify).

## Passos seguintes

- Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.
- Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md). 




<!--HONumber=Aug16_HO1-->


