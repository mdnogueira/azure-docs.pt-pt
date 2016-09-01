<properties
   pageTitle="Criar e configurar um gateway de aplicação com um balanceador de carga interno (ILB) com o Azure Resource Manager | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e eliminar um gateway de aplicação do Azure com o balanceador de carga interno (ILB) do Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# Criar um gateway de aplicação com um balanceador de carga interno (ILB) com o Azure Resource Manager

> [AZURE.SELECTOR]
- [Passos do Azure clássico](application-gateway-ilb.md)
- [Passos do Resource Manager PowerShell](application-gateway-ilb-arm.md)

Pode configurar o Application Gateway do Azure com um VIP de acesso à Internet ou com um ponto final interno não exposto à Internet, também conhecido como ponto final do balanceador de carga interno (ILB). Configurar o gateway com um ILB é útil para as aplicações de linha de negócio internas não expostas à Internet. Também é útil para serviços e camadas dentro de uma aplicação de várias camadas que assenta num limite de segurança que, não sendo exposto à Internet, requer a distribuição de carga round-robin, a persistência da sessão ou a terminação SSL (Secure Sockets Layer).

Este artigo descreve os passos para configurar um gateway de aplicação com um ILB.

## Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Vai criar uma rede virtual e uma sub-rede para o Application Gateway. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O Application Gateway tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que vai configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um gateway de aplicação?


- **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à rede virtual, mas numa sub-rede diferente para o gateway de aplicação, ou ser um IP/VIP público.
- **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
- **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
- **Serviço de escuta:** o serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https, sensível às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
- **Regra:** a regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.



## Para criar um gateway de aplicação

A diferença entre a utilização do Azure Clássico e do Azure Resource Manager é a ordem pela qual vai criar o gateway de aplicação e os itens que devem ser configurados.
Com o Resource Manager, todos os itens que constituem um gateway de aplicação são configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicação.


Os passos necessários para criar um gateway de aplicação encontram-se descritos abaixo:

1. Criar um grupo de recursos para o Resource Manager
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicação
3. Criar um objeto de configuração do gateway de aplicação
4. Criar um recurso do gateway de aplicação


## Criar um grupo de recursos para o Resource Manager

Não se esqueça de mudar o modo do PowerShell para utilizar o cmdlets do Azure Resource Manager. Para obter mais informações, veja [Using Windows PowerShell with Resource Manager (Usar o Windows PowerShell com o Resource Manager)](../powershell-azure-resource-manager.md).

### Passo 1

    Login-AzureRmAccount

### Passo 2

Verifique as subscrições da conta.

    Get-AzureRmSubscription

Ser-lhe-á solicitado a autenticação com as suas credenciais.<BR>

### Passo 3

Escolha qual das subscrições do Azure utilizar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passo 4

Crie um grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Verifique se todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

No exemplo acima, criámos um grupo de recursos denominado “appgw-rg” e a localização “EUA Oeste”.

## Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo que se segue mostra como criar uma rede virtual utilizando o Resource Manager:

### Passo 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Esta ação atribui o intervalo de endereços 10.0.0.0/24 a uma variável da sub-rede a utilizar para criar uma rede virtual.

### Passo 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Esta ação cria uma rede virtual com o nome “appgwvnet” no grupo de recursos “appgw-rg” para a região EUA Oeste utilizando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

### Passo 3

    $subnet = $vnet.subnets[0]

Esta ação atribui o objeto de sub-rede à variável $subnet para os passos seguintes.

## Criar um objeto de configuração do gateway de aplicação

### Passo 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Esta ação cria uma configuração de IP do gateway de aplicação com o nome “gatewayIP01”. Ao iniciar, o Application Gateway escolhe um endereço IP na sub-rede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de front-end. Note que cada instância terá um endereço IP.


### Passo 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Esta ação configura o conjunto de endereços IP de front-end com o nome “pool01” com os endereços IP “134.170.185.46, 134.170.188.221,134.170.185.50”. Estes endereços são os endereços IP que irão receber o tráfego de rede do ponto final do IP de front-end. Deverá substituir os endereços IP acima para adicionar os pontos finais do endereço IP da sua própria aplicação.

### Passo 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Esta ação configura a definição do gateway de aplicação “poolsetting01” para o tráfego de rede com balanceamento de carga no conjunto de back-end.

### Passo 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Esta ação configura a porta IP de front-end com o nome “frontendport01” para o ILB.

### Passo 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Esta ação cria a configuração IP de front-end denominada “fipconfig01” e associa-a a um IP privado a partir da atual sub-rede da rede virtual.

### Passo 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Esta ação cria o serviço de escuta com o nome “listener01” e associa a porta de front-end à configuração IP de front-end.

### Passo 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Esta ação cria a regra de encaminhamento do balanceador de carga com o nome “rule01”, que configura o comportamento do balanceador de carga.

### Passo 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Esta ação configura o tamanho da instância do gateway de aplicação.

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## Criar um gateway de aplicação com o New-AzureApplicationGateway

Esta ação cria um gateway de aplicação com todos os itens de configuração indicados nos passos acima. Neste exemplo, o gateway de aplicação é designado “appgwtest”.


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Esta ação cria um gateway de aplicação com todos os itens de configuração indicados nos passos acima. No exemplo, o gateway de aplicação é designado “appgwtest”.


## Eliminar um gateway de aplicação

Para eliminar um gateway de aplicação, terá de seguir estes passos por ordem:

1. Utilize o cmdlet **Stop-AzureRmApplicationGateway** para parar o gateway.
2. Utilize o cmdlet **Remove-AzureRmApplicationGateway** para remover o gateway.
3. Verifique se o gateway foi removido com o cmdlet **Get-AzureApplicationGateway**.


### Passo 1

Obtenha o objeto do gateway de aplicação e associe-o a uma variável “$getgw”.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Passo 2

Utilize **Stop-AzureRmApplicationGateway** para parar o gateway de aplicação. Este exemplo mostra o cmdlet **Stop-AzureRmApplicationGateway** na primeira linha, seguido pela saída.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Assim que o gateway de aplicação estiver no estado parado, utilize o cmdlet **Remove-AzureRmApplicationGateway** para remover o serviço.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] Pode utilizar o comutador **-force** para eliminar a mensagem de confirmação de remoção.


Para verificar se o serviço foi removido, pode utilizar o cmdlet **Get-AzureRmApplicationGateway**. Este passo não é necessário.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## Passos seguintes

Se pretender configurar a descarga de SSL, veja [Configure an application gateway for SSL offload (Configurar um gateway de aplicação para a descarga de SSL)](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um ILB, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Traffic Manager do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=ago16_HO4-->


