---
title: "Configurar uma firewall de aplicação web: Gateway de aplicação do Azure | Microsoft Docs"
description: "Este artigo fornece orientação sobre como começar a utilizar uma firewall de aplicação web num gateway de aplicação nova ou existente."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurar uma firewall de aplicação web num gateway de aplicação nova ou existente

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI do Azure](application-gateway-web-application-firewall-cli.md)

Saiba como criar uma firewall de aplicação web (WAF)-ativado o gateway de aplicação. Além disso, saiba como adicionar uma WAF a um gateway de aplicação existente.

WAF no Gateway de aplicação do Azure protege as aplicações web de ataques baseados na web comuns, como a injeção de SQL, ataques de scripts entre sites e hijacks de sessão.

 Gateway de aplicação é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diferentes servidores, se estiverem na nuvem ou no local. Gateway de aplicação oferece muitas funcionalidades de controlador (ADC) de entrega de aplicações:

 * Balanceamento de carga HTTP
 * Afinidade com base no cookie de sessão
 * Proteger a descarga de Sockets Layer (SSL)
 * Sondas de estado de funcionamento personalizado
 * Suporte para a funcionalidade de múltiplos sites
 
 Para obter uma lista completa das funcionalidades suportadas, consulte [descrição geral do Gateway de aplicação](application-gateway-introduction.md).

Este artigo mostra como [adicionar uma WAF para um gateway de aplicação existente](#add-web-application-firewall-to-an-existing-application-gateway). Também mostra como [criar um gateway de aplicação que utiliza uma WAF](#create-an-application-gateway-with-web-application-firewall).

![imagem do cenário][scenario]

## <a name="waf-configuration-differences"></a>Diferenças de configuração WAF

Se já leu [criar um gateway de aplicação com o PowerShell](application-gateway-create-gateway-arm.md), compreender as definições de SKU para configurar quando criar um gateway de aplicação. O WAF fornece definições adicionais para definir quando configura um SKU num gateway de aplicação. Não foram efetuadas alterações adicionais que efetuar no gateway de aplicação em si.

| **Definição** | **Detalhes**
|---|---|
|**SKU** |Um gateway de aplicação normal sem uma WAF suporta **padrão\_pequeno**, **padrão\_média**, e **padrão\_grande**tamanhos. Com a introdução de uma WAF, existem dois SKUs adicionais, **WAF\_média** e **WAF\_grande**. Uma WAF não é suportada nos gateways de aplicação pequeno.|
|**Camada** | Os valores disponíveis são **padrão** ou **WAF**. Quando utiliza uma WAF, tem de escolher **WAF**.|
|**Modo** | Esta definição é o modo do WAF. valores permitidos são **deteção** e **prevenção**. Quando o WAF está configurado **deteção** modo, todas as ameaças são armazenadas num ficheiro de registo. No **prevenção** modo, os eventos são registados ainda mas o atacante recebe 403 não autorizado resposta do gateway de aplicação.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar uma firewall de aplicação web para um gateway de aplicação existente

Certifique-se de que utiliza a versão mais recente do Azure PowerShell. Para obter mais informações, consulte [utilize o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

1. Inicie sessão sua conta do Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Selecione a subscrição a utilizar para este cenário.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Obter o gateway em que pretende adicionar uma WAF.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Configure o SKU de WAF. Os tamanhos disponíveis são **WAF\_grande** e **WAF\_média**. Quando utiliza uma WAF, a camada tem de ser **WAF**. Confirme a capacidade quando definir o SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. Configure as definições de WAF conforme definido no exemplo seguinte. Para **FirewallMode**, os valores disponíveis são **prevenção** e **deteção**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Atualize o gateway de aplicação com as definições especificadas no passo anterior.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Este comando atualiza o gateway de aplicação com uma WAF. Para compreender como ver os registos para o gateway de aplicação, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md). Devido à natureza segurança de uma WAF, consulte os registos regularmente para compreender a postura de segurança das suas aplicações web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Criar um gateway de aplicação com uma firewall de aplicação web

Os seguintes passos orienta-o por todo o processo de criação de um gateway de aplicação com uma WAF.

Certifique-se de que utiliza a versão mais recente do Azure PowerShell. Para obter mais informações, consulte [utilize o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

1. Inicie sessão no Azure através da execução `Login-AzureRmAccount`. É-lhe pedido que autenticar com as suas credenciais.

2. Verifique as subscrições para a conta através da execução `Get-AzureRmSubscription`.

3. Selecione a subscrição do Azure a utilizar.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para o gateway de aplicação.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizem o mesmo grupo de recursos.

No exemplo anterior, criámos um grupo de recursos denominado "appgw-RG" com a localização "EUA Oeste".

> [!NOTE]
> Se tiver de configurar uma sonda personalizada para o gateway de aplicação, veja [Create an application gateway with custom probes by using PowerShell (Criar um gateway de aplicação com sondas personalizadas com o PowerShell)](application-gateway-create-probe-ps.md). Para obter mais informações, consulte [sondas personalizadas e monitorização de estado de funcionamento](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Um gateway de aplicação precisa de uma sub-rede do seu próprio. Neste passo, vai criar uma rede virtual com um espaço de endereços de 10.0.0.0/16 e duas sub-redes, um para o gateway de aplicação e outro para os membros do conjunto de back-end.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>Configurar o endereço IP público

Para processar pedidos externos, o gateway de aplicação requer um endereço IP público. Este endereço IP público não pode ter um `DomainNameLabel` definido para ser utilizado pelo gateway de aplicação.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Configurar o gateway de aplicação

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Gateways de aplicação criados com a configuração básica do WAF estão configurados com CR 3.0 para proteção.

## <a name="get-an-application-gateway-dns-name"></a>Obter um nome de DNS do gateway de aplicação

Depois de criado o gateway, o próximo passo é configurar o front-end para comunicação. Quando utilizar um IP público, o gateway de aplicação requer um nome DNS dinamicamente atribuído, o que não é amigável. Para garantir que os utilizadores podem atingir o gateway de aplicação, utilize um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado para um serviço em nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um alias, obter detalhes sobre o gateway de aplicação e o seu nome IP/DNS associado utilizando o elemento de PublicIPAddress ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME, que aponta de aplicações dois web para este nome DNS. Não recomendamos que utilize um registos, porque o VIP pode mudar quando reinicia o gateway de aplicação.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Passos seguintes

Para saber como configurar o registo de diagnóstico para registar os eventos que são detetados ou impedidos com uma WAF, consulte o artigo [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
