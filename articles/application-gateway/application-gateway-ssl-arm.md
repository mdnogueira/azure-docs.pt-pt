<properties
   pageTitle="Configurar um gateway de aplicação para a descarga de SSL com o Azure Resource Manager | Microsoft Azure"
   description="Esta página fornece instruções para criar um gateway de aplicação com descarga de SSL com o Azure Resource Manager"
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
   ms.date="08/09/2016"
   ms.author="gwallace"/>

# Configurar um gateway de aplicação para a descarga de SSL com o Azure Resource Manager

> [AZURE.SELECTOR]
-[Portal do Azure](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

 Pode configurar o Azure Application Gateway para terminar a sessão SSL (Secure Sockets Layer) no gateway para evitar tarefas dispendiosas de desencriptação de SSL que ocorrem no farm Web. A descarga de SSL simplifica ainda a configuração do servidor de front-end e a gestão da aplicação Web.


## Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Pode criar uma rede virtual e uma subrede para o gateway de aplicação. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O Application Gateway tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um gateway de aplicação?


- **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ter um IP/VIP público.
- **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
- **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
- **Serviço de escuta:** o serviço de escuta possui uma porta de front-end, um protocolo (HTTP ou HTTPS, definições sensíveis a maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
- **Regra:** a regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deverá passar para *Https* (sensível às maiúsculas e minúsculas). O elemento **SslCertificate** é adicionado a **HttpListener** com o valor da variável configurado para o certificado SSL. A porta de front-end deve ser atualizada para 443.

**Para ativar a afinidade com base em cookies**: pode configurar um gateway de aplicação para garantir que um pedido feito a partir de uma sessão de cliente é sempre direcionado para a mesma VM no farm Web. Esta ação é realizada ao injetar um cookie de sessão que permite ao gateway direcionar adequadamente o tráfego. Para ativar a afinidade com base em cookies, defina **CookieBasedAffinity** como *Ativado* no elemento **BackendHttpSettings**.


## Criar um gateway de aplicação

A diferença entre a utilização do modelo de implementação Clássica do Azure e do Azure Resource Manager é a ordem pela qual cria o gateway de aplicação e os itens que devem ser configurados.

Com o Resource Manager, todos os itens que irão constituir um gateway de aplicação serão configurados individualmente e, em seguida, reunidos para criar um recurso do gateway de aplicação.


Os passos necessários para criar um gateway de aplicação encontram-se descritos abaixo:

1. Criar um grupo de recursos para o Resource Manager
2. Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicação
3. Criar um objeto de configuração do gateway de aplicação
4. Criar um recurso do gateway de aplicação


## Criar um grupo de recursos para o Resource Manager

Não se esqueça de mudar o modo do PowerShell para utilizar o cmdlets do Azure Resource Manager. Para obter mais informações, veja [Using Windows PowerShell with Resource Manager (Usar o Windows PowerShell com o Resource Manager)](../powershell-azure-resource-manager.md).

### Passo 1

    Login-AzureRmAccount



### Passo 2

Verifique as subscrições da conta.

    Get-AzureRmSubscription

Ser-lhe-á solicitado para autenticar com as suas credenciais.<BR>

### Passo 3

Escolha qual das subscrições do Azure utilizar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passo 4

Crie um novo grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, Esta definição é utilizada como a localização predefinida para recursos nesse grupo de recursos. Verifique se todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

No exemplo acima, criámos um grupo de recursos denominado “appgw-RG” e a localização “EUA Oeste”.

## Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo que se segue mostra como criar uma rede virtual utilizando o Resource Manager:

### Passo 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Esta ação atribui o intervalo de endereços 10.0.0.0/24 a uma variável da sub-rede a utilizar para criar uma rede virtual.

### Passo 2
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Esta ação cria uma rede virtual com o nome “appgwvnet” no grupo de recursos “appgw-rg” para a região EUA Oeste utilizando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

### Passo 3

    $subnet = $vnet.Subnets[0]

Esta ação atribui o objeto de sub-rede à variável $subnet para os passos seguintes.

## Criar um endereço IP público para a configuração de front-end

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Esta ação cria um recurso IP público “publicIP01” no grupo de recursos “appgw-rg” para a região EUA Oeste.


## Criar um objeto de configuração do gateway de aplicação

### Passo 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Esta ação cria uma configuração de IP do gateway de aplicação com o nome “gatewayIP01”. Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

### Passo 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Esta ação configura o conjunto de endereços IP de front-end com o nome “pool01” com os endereços IP “134.170.185.46, 134.170.188.221,134.170.185.50”. Estes endereços são os endereços IP que irão receber o tráfego de rede do ponto final do IP de front-end. Substitua os endereços IP do exemplo acima pelos endereços IP dos pontos finais da sua aplicação Web.

### Passo 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Esta ação configura a definição do gateway de aplicação “poolsetting01” para o tráfego de rede com balanceamento de carga no conjunto de back-end.

### Passo 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Esta ação configura a porta IP de front-end com o nome “frontendport01” para o ponto final de IP público.

### Passo 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Esta ação configura o certificado utilizado para a ligação SSL. O certificado tem de estar no formato .pfx e a palavra-passe tem de conter entre 4 e 12 carateres.

### Passo 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Esta ação cria a configuração do IP de front-end com o nome “fipconfig01” e associa o endereço IP público à configuração do IP de front-end.

### Passo 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Esta ação cria o nome “listener01” para o serviço de escuta e associa a porta de front-end ao certificado e à configuração IP de front-end.

### Passo 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Esta ação cria a regra de encaminhamento do balanceador de carga com o nome “rule01”, que configura o comportamento do balanceador de carga.

### Passo 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Esta ação configura o tamanho da instância do gateway de aplicação.

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## Criar um gateway de aplicação com o New-AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Esta ação cria um gateway de aplicação com todos os itens de configuração indicados nos passos acima. No exemplo, o gateway de aplicação é designado “appgwtest”.

## Passos seguintes

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno (ILB), veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Traffic Manager do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=ago16_HO4-->


