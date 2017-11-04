Os passos para esta tarefa utilizam uma VNet com base nos valores de lista de referências de configuração seguintes. Nomes e outras definições também são descritos nesta lista. Iremos não utilize esta lista diretamente em nenhum dos passos, embora adicionamos variáveis com base nos valores nesta lista. Pode copiar a lista para utilizar como uma referência, substituindo os valores pelos seus.

**Lista de referência de configuração**

* Nome da rede virtual = "TestVNet"
* Espaço de endereços de rede virtuais = 192.168.0.0/16
* Grupo de recursos = "TestRG"
* Subnet1 Name = "FrontEnd" 
* Espaço de endereços Subnet1 = "192.168.1.0/24"
* Nome de sub-rede de gateway: "GatewaySubnet" tem de nome sempre uma sub-rede de gateway *GatewaySubnet*.
* Espaço de endereços de sub-rede de gateway = "192.168.200.0/26"
* Região = "EUA Leste"
* O nome do gateway = "GW"
* O nome IP do gateway = "GWIP"
* Configuração de IP do gateway Name = "gwipconf"
* Tipo = "ExpressRoute" deste tipo é necessário para uma configuração do ExpressRoute.
* Nome de IP público do gateway = "gwpip"

## <a name="add-a-gateway"></a>Adicionar um gateway
1. Ligar à sua subscrição do Azure.

  ```powershell 
  Login-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Declare as variáveis para este exercício. É necessário editar o exemplo para refletir as definições que pretende utilizar.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Armazene o objeto de rede virtual como uma variável.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Adicione uma sub-rede de gateway para a rede Virtual. A sub-rede do gateway deve ter o nome "GatewaySubnet". Deve criar uma sub-rede de gateway é/27 ou superior (/ 26, / 25, etc.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Defina a configuração.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Armazene a sub-rede do gateway como uma variável.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Solicitar um endereço IP público. O endereço IP solicitado antes de criar o gateway. Não é possível especificar o endereço IP que pretende utilizar. é dinamicamente atribuído. Utilizará este endereço IP na secção de configuração seguinte. O allocationmethod como tem de ser dinâmico.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Crie a configuração para o seu gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Neste passo, especifica a configuração que será utilizada ao criar o gateway. Este passo não efetivamente a criar o objeto do gateway. Utilize o exemplo abaixo para criar a configuração do gateway.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Crie o gateway. Neste passo, o **- GatewayType** é especialmente importante. Tem de utilizar o valor **ExpressRoute**. Depois de executar estes cmdlets, o gateway pode demorar 45 minutos ou mais para criar.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Certifique-se de que a criação do gateway
Utilize os seguintes comandos para verificar que foi criado o gateway:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Redimensionar um gateway
Há uma série de [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Pode utilizar o seguinte comando para alterar o SKU de Gateway em qualquer altura.

> [!IMPORTANT]
> Este comando não funciona para UltraPerformance gateway. Para alterar o gateway para um gateway UltraPerformance, remova primeiro o gateway do ExpressRoute existente e, em seguida, crie um novo gateway de UltraPerformance. Para mudar o gateway de um gateway UltraPerformance, remova primeiro o gateway de UltraPerformance e, em seguida, crie um novo gateway.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Remover um gateway
Utilize o seguinte comando para remover um gateway:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```