---
title: "Criar um gateway de aplicação para alojar vários sites | Microsoft Docs"
description: "Esta página fornece instruções para criar, configurar um gateway de aplicação do Azure para alojar várias aplicações web no mesmo gateway."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Criar um gateway de aplicação para alojar várias aplicações web

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Alojar vários do site permite-lhe implementar mais do que uma aplicação web no mesmo gateway de aplicação. Baseia-se na presença de cabeçalho de anfitrião no pedido HTTP recebido, para determinar qual escuta seria receber tráfego. O serviço de escuta, em seguida, direciona o tráfego para o conjunto back-end apropriado, conforme configurado na definição de regras do gateway. Nas aplicações da web SSL ativado, o gateway de aplicação baseia-se a extensão de indicação de nome de servidor (SNI) para escolher o serviço de escuta correto para o tráfego web. É uma utilização comum para vários sites de alojamento para a carga de pedidos de saldo para domínios web diferente para conjuntos de diferentes servidores de back-end. Da mesma forma vários subdomínios do mesmo domínio de raiz também podem ser alojados num mesmo gateway de aplicação.

## <a name="scenario"></a>Cenário

No exemplo seguinte, gateway de aplicação está a servir o tráfego para contoso.com e fabrikam.com com dois conjuntos de servidores de back-end: contoso agrupamento de servidores e agrupamento de servidores da fabrikam. Configuração semelhante poderia ser utilizado para subdomínios de anfitrião, como app.contoso.com e blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Servidores adicionados ao conjunto de back-end para utilizar o gateway de aplicação tem de existir ou os respetivos pontos finais criou ou na rede virtual numa sub-rede separada ou com um IP/VIP público atribuído.

## <a name="requirements"></a>Requisitos

* **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ter um IP/VIP público. Também pode ser utilizado o FQDN.
* **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** o serviço de escuta tem uma porta de front-end, um protocolo (Http ou Https; estes valores são sensíveis às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL). Para gateways de aplicação ativada multilocal, nome de anfitrião e os indicadores SNI também são adicionados.
* **Regra:** a regra vincula o serviço de escuta, o agrupamento de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. As regras são processadas pela ordem que estão listados e será direcionado tráfego através da primeira regra que corresponde ao independentemente especificidade. Por exemplo, se tiver uma regra com um serviço de escuta básico e uma regra com uma escuta de vários site ambas na mesma porta, a regra com o serviço de escuta de vários site têm de estar listada antes da regra com o serviço de escuta básico por ordem para a regra de vários site a funcionar conforme esperado.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Seguem-se os passos necessários para criar um gateway de aplicação:

1. Crie um grupo de recursos para o Resource Manager.
2. Crie uma rede virtual, sub-redes e um IP público para o gateway de aplicação.
3. Crie um objeto de configuração do gateway de aplicação.
4. Crie um recurso do gateway de aplicação.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

Verifique se está a utilizar a versão mais recente do Azure PowerShell. Estão disponíveis mais informações no [utilizando o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passo 1

Iniciar sessão no Azure

```powershell
Login-AzureRmAccount
```
Ser-lhe-á solicitado para autenticar com as suas credenciais.

### <a name="step-2"></a>Passo 2

Verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Passo 3

Escolha qual das subscrições do Azure utilizar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passo 4

Crie um novo grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

Em alternativa também podem criar etiquetas para um grupo de recursos para o gateway de aplicação:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizem o mesmo grupo de recursos.

No exemplo acima, criámos um grupo de recursos denominado **appgw-RG** com uma localização de **EUA oeste**.

> [!NOTE]
> Se tiver de configurar uma sonda personalizada para o gateway de aplicação, veja [Create an application gateway with custom probes by using PowerShell (Criar um gateway de aplicação com sondas personalizadas com o PowerShell)](application-gateway-create-probe-ps.md). Visite [das sondas personalizadas e monitorização de estado de funcionamento](application-gateway-probe-overview.md) para obter mais informações.

## <a name="create-a-virtual-network-and-subnets"></a>Criar uma rede virtual e sub-redes

O exemplo que se segue mostra como criar uma rede virtual com o Resource Manager. Duas sub-redes são criadas neste passo. É a primeira sub-rede para o próprio gateway de aplicação. Gateway de aplicação requer a sua própria sub-rede para armazenar as suas instâncias. Outros gateways de aplicação podem ser implementados nessa sub-rede. A segunda sub-rede é utilizada para conter os servidores de back-end da aplicação.

### <a name="step-1"></a>Passo 1

Atribua o endereço intervalo 10.0.0.0/24 a variável da sub-rede a ser utilizado para conter o gateway de aplicação.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>Passo 2

Atribua o endereço intervalo 10.0.1.0/24 à variável subnet2 a ser utilizada para os conjuntos de back-end.

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Passo 3

Criar uma rede virtual denominada **appgwvnet** no grupo de recursos **appgw-rg** para a região EUA oeste com 10.0.0.0/16 o prefixo de sub-rede 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>Passo 4

Atribua uma variável de sub-rede para conhecer os passos seguintes, que cria um gateway de aplicação.

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso de IP público **publicIP01** no grupo de recursos **appgw-rg** para a região E.U.A. Oeste.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar a configuração do gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração de IP do gateway de aplicação com o nome **gatewayIP01**. Quando é iniciado o gateway de aplicação, escolherá um endereço IP da sub-rede configurado e encaminhar o tráfego de rede para os endereços IP no conjunto de IP back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>Passo 2

Configurar o conjunto de endereços IP back-end com o nome **pool01** e **pool2** com endereços IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50** para **pool1** e **134.170.186.46**, **134.170.189.221**, **134.170.186.50** para **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

Neste exemplo, existem dois conjuntos de back-end para encaminhar o tráfego de rede com base no site de pedido. Um conjunto recebe o tráfego do site "contoso.com" e outro conjunto recebe o tráfego do site "fabrikam.com". Tem de substituir os endereços IP anteriores para adicionar os seus próprios endereços IP de aplicação pontos finais. Em vez de endereços IP internos, pode também utilizar endereços IP públicos, FQDN ou NIC de uma VM para instâncias de back-end. Para especificar o FQDN em vez de IPs na utilização do PowerShell "-BackendFQDNs" parâmetro.

### <a name="step-3"></a>Passo 3

Configurar a definição de gateway de aplicação **poolsetting01** e **poolsetting02** para o tráfego de rede com balanceamento de carga no conjunto de back-end. Neste exemplo, configure as definições de outro conjunto de back-end para os conjuntos de back-end. Cada conjunto de back-end pode ter a sua própria definição de conjunto de back-end.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Passo 4

Configure o IP de front-end com o ponto final de IP público.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Passo 5

Configure a porta de front-end de um gateway de aplicação.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>Passo 6

Configure dois certificados SSL para os dois sites que iremos suportar este exemplo. Um certificado é para o tráfego contoso.com e o outro é para o tráfego de fabrikam.com. Estes certificados devem ter uma autoridade de certificação que emitiu certificados para os Web sites. Certificados autoassinados são suportados mas não recomendados para o tráfego de produção.

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>Passo 7

Configure dois serviços de escuta para os dois web sites neste exemplo. Este passo configura os serviços de escuta para o endereço, porta e anfitrião utilizado para receber o tráfego de entrada IP público. Parâmetro de nome de anfitrião é necessário para o suporte para vários sites e deve ser definido para o site apropriado para o qual o tráfego é recebido. RequireServerNameIndication parâmetro deve ser definido como verdadeiro para Web sites que precisam de suporte para SSL num cenário de vários anfitriões. Se é necessário o suporte SSL, também tem de especificar o certificado SSL que é utilizado para proteger o tráfego para essa aplicação web. A combinação de FrontendIPConfiguration, FrontendPort e nome de anfitrião deve ser exclusiva num serviço de escuta. Cada serviço de escuta pode suportar um certificado.

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>Passo 8

Crie duas de regra de definição para as aplicações web de duas neste exemplo. Uma regra vincula em conjunto, as definições de http, conjuntos back-end e os serviços de escuta. Este passo configura o gateway de aplicação para utilizar a regra de encaminhamento básica, um para cada Web site. O tráfego para cada Web site é recebido pelo respetivo serviço de escuta configurado e, em seguida, é direcionado para o seu conjunto de back-end configurada, utilizando as propriedades especificadas no BackendHttpSettings.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>Passo 9

Configure o número de instâncias e o tamanho do gateway de aplicação.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Criar gateway de aplicação

Crie um gateway de aplicação com todos os objetos de configuração dos passos anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> Aprovisionamento de Gateway de aplicação é uma operação demorada que e pode demorar algum tempo a concluir.
> 
> 

## <a name="get-application-gateway-dns-name"></a>Obter nome de DNS de gateway de aplicação

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Ao utilizar um IP público, o gateway de aplicação requer um nome DNS dinamicamente atribuído, que não é amigável. Para garantir que os utilizadores finais alcançam o gateway de aplicação, pode utilizar-se um registo CNAME para apontar para o ponto final público do gateway de aplicação. [Configurar um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para o fazer, obtenha detalhes sobre o gateway de aplicação e o seu nome DNS/IP associado com o elemento PublicIPAddress ligado ao gateway de aplicação. O nome DNS do gateway de aplicação deve servir para criar um registo CNAME, que aponta as duas aplicações Web para este nome DNS. Não é recomendada a utilização de registos A, uma vez que o VIP pode ser alterado no reinício do gateway de aplicação.

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

Saiba como proteger os seus Web sites com [Gateway de aplicação - Firewall de aplicações Web](application-gateway-webapplicationfirewall-overview.md)

