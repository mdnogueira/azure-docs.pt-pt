<properties
   pageTitle="Criar, iniciar ou eliminar um gateway de aplicação com o Azure Resource Manager | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e eliminar um gateway de aplicação do Azure com o Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma"/>


# Criar, iniciar ou eliminar um gateway de aplicação com o Azure Resource Manager

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local. O Aplicação Gateway tem as seguintes funcionalidades de entrega de aplicações: balanceamento de carga HTTP, afinidade de sessão com base em cookies e descarga de SSL (Secure Sockets Layer).


> [AZURE.SELECTOR]
- [Passos do Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Modelo Azure Resource Manager ](application-gateway-create-gateway-arm-template.md)


<BR>


Este artigo descreve os passos para criar, configurar, iniciar e eliminar um gateway de aplicação.


>[AZURE.IMPORTANT] Antes de trabalhar com recursos do Azure, é importante compreender que o Azure tem atualmente dois modelos de implementação: Resource Manager e clássico. Certifique-se de que compreende os [modelos e ferramentas de implementação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Pode ver a documentação de diversas ferramentas clicando nos separadores na parte superior deste artigo. Este documento aborda a criação de um gateway de aplicação com o Azure Resource Manager. Para utilizar a versão clássica, aceda a [Create an application gateway classic deployment by using PowerShell (Criar uma implementação clássica do gateway de aplicação com o PowerShell)](application-gateway-create-gateway.md).



## Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Vai criar uma rede virtual e uma sub-rede para o Application Gateway. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que irá configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um gateway de aplicação?


- **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ter um IP/VIP público.
- **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
- **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
- **Serviço de escuta:** o serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https, sensível às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
- **Regra:** a regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. 



## Criar um novo gateway de aplicação

A diferença entre a utilização do Azure Clássico e do Azure Resource Manager é a ordem pela qual vai criar o gateway de aplicação e os itens que devem ser configurados.

Com o Resource Manager, todos os itens que irão constituir um gateway de aplicação serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicação.


Os passos necessários para criar um gateway de aplicação encontram-se descritos abaixo:

1. Crie um grupo de recursos para o Resource Manager.
2. Crie uma rede virtual, uma sub-rede e um IP público para o gateway de aplicação.
3. Crie um objeto de configuração do gateway de aplicação.
4. Crie um recurso do gateway de aplicação.


## Criar um grupo de recursos para o Resource Manager

Verifique se está a utilizar a versão mais recente do Azure PowerShell. Para obter mais informações, veja [Using Windows PowerShell with Resource Manager (Usar o Windows PowerShell com o Resource Manager)](../powershell-azure-resource-manager.md).

### Passo 1
Inicie sessão no Azure Login-AzureRmAccount

Ser-lhe-á solicitado a autenticação com as suas credenciais.<BR>
### Passo 2
Verifique as subscrições da conta.

        Get-AzureRmSubscription

### Passo 3
Escolha qual das subscrições do Azure utilizar. <BR>

        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Passo 4
Crie um grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Verifique se todos os comandos para criar um gateway de aplicação irão utilizar o mesmo grupo de recursos.

No exemplo acima, criámos um grupo de recursos denominado “appgw-RG” e a localização “EUA Oeste”.

>[AZURE.NOTE] Se tiver de configurar uma sonda personalizada para o gateway de aplicação, veja [Create an application gateway with custom probes by using PowerShell (Criar um gateway de aplicação com sondas personalizadas com o PowerShell)](application-gateway-create-probe-ps.md). Veja [monitorização das sondas personalizadas e do estado de funcionamento](application-gateway-probe-overview.md) para obter mais informações.



## Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo que se segue mostra como criar uma rede virtual com o Resource Manager.

### Passo 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável da sub-rede a utilizar para criar uma rede virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Passo 2

Crie uma rede virtual com o nome “appgwvnet” no grupo de recursos “appgw-rg” para a região EUA Oeste utilizando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Passo 3

Atribua uma variável de sub-rede nos passos seguintes, o que permitirá criar um gateway de aplicação.

    $subnet=$vnet.Subnets[0]

## Criar um endereço IP público para a configuração de front-end

Crie um recurso de IP público “publicIP01” no grupo de recursos “appgw-rg” para a região EUA Oeste.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Criar um objeto de configuração do gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### Passo 1

Crie uma configuração de IP do gateway de aplicação com o nome “gatewayIP01”. Ao iniciar, o Application Gateway escolhe um endereço IP na sub-rede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de front-end. Note que cada instância terá um endereço IP.


    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Passo 2

Configure o conjunto de endereços IP de front-end com o nome “pool01” com os endereços IP “134.170.185.46, 134.170.188.221,134.170.185.50”. Estes endereços são os endereços IP que irão receber o tráfego de rede do ponto final do IP de front-end. Deverá substituir os endereços IP acima para adicionar os pontos finais do endereço IP da sua própria aplicação.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Passo 3

Configure a definição do gateway de aplicação “poolsetting01” para o tráfego de rede com balanceamento de carga no conjunto de back-end.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Passo 4

Configure a porta IP de front-end com o nome “frontendport01” para o ponto final do IP público.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Passo 5

Crie a configuração do IP de front-end com o nome “fipconfig01” e associe o endereço IP público à configuração do IP de front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Passo 6

Crie o serviço de escuta com o nome “listener01” e associe a porta de front-end à configuração do IP de front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Passo 7

Crie a regra encaminhamento do balanceador de carga com o nome “rule01”, que configura o comportamento do balanceador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Passo 8

Configure o tamanho da instância do gateway de aplicação.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## Criar um gateway de aplicação com New-AzureRmApplicationGateway

Crie um gateway de aplicação com todos os itens de configuração indicados nos passos acima. Neste exemplo, o gateway de aplicação é designado “appgwtest”.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Eliminar um gateway de aplicação

Siga estes passos para eliminar um gateway de aplicação:

1. Utilize o cmdlet **Stop-AzureRmApplicationGateway** para parar o gateway.
2. Utilize o cmdlet **Remove-AzureRmApplicationGateway** para remover o gateway.
3. Verifique se o gateway foi removido utilizando o cmdlet **Get-AzureRmApplicationGateway**.

### Passo 1

Obtenha o objeto do gateway de aplicação e associe-o a uma variável “$getgw”.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Passo 2

Utilize **Stop-AzureRmApplicationGateway** para parar o gateway de aplicação.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Assim que o gateway de aplicação estiver no estado parado, utilize o cmdlet **Remove-AzureRmApplicationGateway** para remover o serviço.


    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] Pode utilizar o comutador **-force** para eliminar a mensagem de confirmação de remoção.


Para verificar se o serviço foi removido, pode utilizar o cmdlet **Get-AzureRmApplicationGateway**. Este passo não é necessário.


    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Passos seguintes

Se pretender configurar a descarga de SSL, veja [Configure an application gateway for SSL offload (Configurar um gateway de aplicação para a descarga de SSL)](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Traffic Manager do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=Jun16_HO2-->


