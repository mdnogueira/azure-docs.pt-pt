---
title: "Como utilizar a API Management do Azure na rede Virtual com Gateway de aplicação | Microsoft Docs"
description: "Saiba como instalar e configurar a API Management do Azure na rede Virtual interna com o aplicação Gateway (WAF) como front-end"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: e138241139329b8bb956157ab55b7d22dc2a9b67
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrar a gestão de API numa VNET interna com Gateway de aplicação 

##<a name="overview"></a> Descrição geral
 
O serviço de API Management pode ser configurado numa rede Virtual no modo interno que torna acessível apenas a partir de dentro da rede Virtual. Gateway de aplicação do Azure é um serviço de PAAS que fornece um balanceador de carga de 7 camadas. É também age como um serviço de proxy inverso e fornece entre a oferta de uma Firewall de aplicação Web (WAF).

Combinação de API Management aprovisionados numa VNET com o front-end do Gateway de aplicação interna permite que os seguintes cenários:

* Utilize o mesmo recurso de API Management para consumo por consumidores internos e externos consumidores.
* Utilizar um único recurso de gestão de API e um subconjunto de APIs definidas na API Management disponível para consumidores externos.
* Fornece uma forma de chave ative para mudar o acesso à gestão de API na Internet pública e desativar. 

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de API Management do Azure](get-started-create-service-instance.md).

##<a name="scenario"></a> Cenário
Este artigo abrange como utilizar um único serviço de API Management para consumidores internos e externos e torná-lo a atuar como um único front-end para ambos no local e APIs da nuvem. Também verá como expõem apenas um subconjunto das suas APIs (no exemplo que são realçados verde) para consumo externo utilizando a funcionalidade de PathBasedRouting disponível no Gateway de aplicação.

No exemplo de configuração primeiro as suas APIs são geridos apenas a partir de dentro da sua rede Virtual. Consumidores internos (cor de laranja realçado em) podem aceder a todas as suas APIs internos e externos. Tráfego nunca vai para a Internet é entregue um elevado desempenho através de circuitos do Expressroute.

![rota de URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Criar uma rede Virtual e crie sub-redes separadas para a API Management e o Gateway de aplicação. 
3. Se pretender criar um servidor DNS personalizado para a rede Virtual, tal antes de iniciar a implementação. Verifique novamente funciona, assegurando que uma máquina virtual criada numa nova sub-rede na rede Virtual pode resolver e aceder a todos os pontos finais de serviço do Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>O que é necessário para criar uma integração entre a API Management e o Gateway de aplicação?

* **Agrupamento de servidores de back-end:** este é o endereço IP virtual interno do serviço de API Management.
* **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições são aplicadas a todos os servidores dentro do conjunto.
* **Porta de front-end:** é a porta pública aberta no gateway de aplicação. Tráfego de alcance obtém redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** o serviço de escuta tem uma porta de front-end, um protocolo (Http ou Https; estes valores são sensíveis às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
* **Regra:** a regra está vinculado a um serviço de escuta para um agrupamento de servidores de back-end.
* **Sonda de estado de funcionamento personalizada:** Gateway de aplicação, por predefinição, utiliza as pesquisas de endereço baseada em IP para saber quais os servidores no BackendAddressPool estão ativos. A API Management service apenas responde a pedidos que tem o cabeçalho de anfitrião correto, por conseguinte, as pesquisas de predefinição falharem. Uma sonda do Estado de funcionamento personalizado tem de ser definido para ajudar a determinar se o serviço está ativo e deve reencaminhar pedidos de gateway de aplicação.
* **Certificado de domínio personalizado:** para aceder à API Management a partir da internet tem de criar um mapeamento de CNAME do respetivo nome de anfitrião para o nome DNS de front-end do Gateway de aplicação. Isto garante que o cabeçalho de nome de anfitrião e o certificado enviado para o Gateway de aplicação que seja reencaminhado para a API Management é um APIM pode reconhecer como válido.

## <a name="overview-steps"></a> Passos necessários para integrar o API Management e o Gateway de aplicação 

1. Crie um grupo de recursos para o Resource Manager.
2. Crie uma rede Virtual, uma sub-rede e um IP público para o Gateway de aplicação. Crie outra sub-rede para a API Management.
3. Criar um serviço de API Management dentro da sub-rede da VNET criada acima e certifique-se de que utilizar o modo interno.
4. Configure o nome de domínio personalizado no serviço de API Management.
5. Crie um objeto de configuração do Gateway de aplicação.
6. Crie um recurso de Gateway de aplicação.
7. Crie um CNAME do nome DNS público do Gateway de aplicação para o nome de anfitrião do proxy de gestão de API.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

Verifique se está a utilizar a versão mais recente do Azure PowerShell. Para obter mais informações, veja [Using Windows PowerShell with Resource Manager (Usar o Windows PowerShell com o Resource Manager)](https://docs.microsoft.com/en-us/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>Passo 1

Iniciar sessão no Azure

```powershell
Login-AzureRmAccount
```

Autenticar com as suas credenciais.<BR>

### <a name="step-2"></a>Passo 2

Verifique as subscrições para a conta e selecione-o.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Passo 3

Crie um novo grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizem o mesmo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede Virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte mostra como criar uma rede Virtual com o resource manager.

### <a name="step-1"></a>Passo 1

Atribua o endereço intervalo 10.0.0.0/24 à variável da sub-rede a utilizar para o Gateway de aplicação ao criar uma rede Virtual.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Passo 2

Atribua o endereço intervalo 10.0.1.0/24 a variável da sub-rede a ser utilizado para gestão de API ao criar uma rede Virtual.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Passo 3

Criar uma rede Virtual denominada **appgwvnet** no grupo de recursos **apim-appGw-RG** para a região EUA Oeste utilizando o prefixo 10.0.0.0/16 com 10.0.0.0/24 sub-redes e 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passo 4

Atribua uma variável de sub-rede para conhecer os passos seguintes

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Criar um serviço de API Management dentro de uma VNET configurado no modo interno

O exemplo seguinte mostra como criar um serviço de API Management numa VNET configurado para terem acesso interno apenas.

### <a name="step-1"></a>Passo 1
Crie um objeto de rede Virtual de gestão de API com a sub-rede $apimsubnetdata criado acima.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Passo 2
Crie um serviço de API Management dentro da rede Virtual.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Depois do comando acima é concluída com êxito, consulte a [necessária configuração de DNS para aceder ao serviço de gestão de API de VNET interno](api-management-using-with-internal-vnet.md#apim-dns-configuration) para aceder ao mesmo.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurar um nome de domínio personalizado na API Management

### <a name="step-1"></a>Passo 1
Carregue o certificado com chave privada para o domínio. Neste exemplo será `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Passo 2
Depois do certificado é carregado, criar um objeto de configuração do nome de anfitrião para o proxy com um nome de anfitrião do `api.contoso.net`, como o certificado de exemplo fornece autoridade para o `*.contoso.net` domínio. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Criar um recurso IP público **publicIP01** no grupo de recursos **apim-appGw-RG** para a região EUA oeste.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar a configuração do gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração de IP do gateway de aplicação com o nome **gatewayIP01**. Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passo 2

Configure a porta IP de front-end para o ponto final IP público. Esta porta é a porta que os utilizadores finais à.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Passo 3

Configure o IP de front-end com o ponto final de IP público.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passo 4

Configure o certificado para o Gateway de aplicação, utilizada para desencriptar e reencriptar o tráfego de passar pela.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Passo 5

Crie o serviço de escuta HTTP para o Gateway de aplicação. Atribua o certificado de configuração, a porta e o ssl IP Front-end ao mesmo.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Passo 6

Criar uma sonda personalizada para o serviço de API Management `ContosoApi` ponto final de domínio do proxy. O caminho `/status-0123456789abcdef` é um ponto final de estado de funcionamento predefinido alojado em todos os serviços de gestão de API. Definir `api.contoso.net` como um nome de anfitrião de sonda personalizada para protegê-lo com o certificado SSL.

> [!NOTE]
> O nome de anfitrião `contosoapi.azure-api.net` é o nome do anfitrião predefinido proxy configurado quando um serviço com o nome `contosoapi` é criada no Azure público. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passo 7

Carregue o certificado a ser utilizada nos recursos de conjunto back-end SSL ativado. Este é o mesmo certificado que é fornecido no passo 4 acima.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Passo 8

Configure definições de back-end HTTP para o Gateway de aplicação. Isto inclui a definição de um limite de tempo limite para o pedido de back-end após o qual são canceladas. Este valor é diferente do que o limite de tempo de pesquisa.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passo 9

Configurar um conjunto de endereços IP back-end com o nome **apimbackend** com o IP virtual interno endereço do serviço de API Management criado acima.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Passo 10

Crie as definições para um back-end de (inexistente) fictício. Pedidos para os caminhos de API que não queremos expor da API de gestão através do Gateway de aplicação serão acessos este back-end e devolver 404.

Configure definições de HTTP para o back-end fictício.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Configurar um back-end fictício **dummyBackendPool**, que aponta para um endereço FQDN **dummybackend.com**. Este endereço FQDN não existe na rede virtual.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Criar uma definição de regra que irá utilizar o Gateway de aplicação por predefinição que aponta para o back-end de inexistente **dummybackend.com** na rede Virtual.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Passo 11

Configure caminhos de regra de URL para os conjuntos de back-end. Isto permite selecionar apenas algumas das APIs de gestão de API para a ser expostos ao público. Por exemplo, se existirem `Echo API` (eco /), `Calculator API` (/calc/), etc. disponibilizar apenas `Echo API` acessível a partir da Internet). 

O exemplo seguinte cria uma regra simples para o "eco /" caminho encaminhamento tráfego para o back-end "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Se o caminho não coincidir com as regras de caminho queremos para ativar a partir da API Management, a configuração de mapa de caminho de regra configura também um conjunto predefinido de endereços de back-end com o nome **dummyBackendPool**. Por exemplo, http://api.contoso.net/calc/ * vai para **dummyBackendPool** conforme está definido como o agrupamento predefinido para o tráfego não correspondente.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

O passo anterior garante que apenas os pedidos para o caminho "/ eco" são permitidos através do Gateway de aplicação. Pedidos para outras APIs configurados na API Management irão gerar 404 erros do Gateway de aplicação quando acede a partir da Internet. 

### <a name="step-12"></a>Passo 12

Crie uma definição de regra para o Gateway de aplicação utilizar o URL com base no caminho de encaminhamento.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Passo 13

Configure o número de instâncias e o tamanho para o Gateway de aplicação. Aqui vamos utilizar o [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) para maior segurança do recurso de API Management.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Passo 14

Configure WAF estar no modo de "Prevenção".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Criar Gateway de aplicação

Crie um Gateway de aplicação com todos os objetos de configuração dos passos anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>O nome de anfitrião de proxy de gestão de API para o nome DNS público do recurso de Gateway de aplicação de CNAME

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Quando utilizar um IP público, o Gateway de aplicação requer um nome DNS dinamicamente atribuído, que pode não ser fáceis de utilizar. 

Nome DNS do Gateway de aplicação deve ser utilizado para criar um registo CNAME que aponta o nome do anfitrião proxy APIM (por exemplo, `api.contoso.net` nos exemplos acima) para este nome DNS. Para configurar o registo CNAME de IP de front-end, obter os detalhes do Gateway de aplicação e o seu nome IP/DNS associado utilizando o elemento de PublicIPAddress. A utilização de registos não é recomendada, uma vez que pode alterar o VIP no momento do reinício do gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"></a> Resumo
Gestão de API do Azure configurada numa VNET fornece uma interface de gateway único para todos os APIs configuradas, se estiverem alojado no local ou na nuvem. Integrar o Gateway de aplicação com a API Management fornece a flexibilidade de seletivamente ativar APIs determinadas para ser acessíveis na Internet, bem como fornecer uma Firewall de aplicação Web como um front-end à sua instância da API Management.

##<a name="next-steps"></a> Próximos passos
* Saiba mais sobre o Gateway de aplicação do Azure
  * [Descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md)
  * [Firewall de aplicação de Web de Gateway de aplicação](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Gateway de aplicação com o encaminhamento com base no caminho](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Saiba mais sobre a gestão de API e as VNETs
  * [Utilizar a API Management disponível apenas dentro da VNET](api-management-using-with-internal-vnet.md)
  * [Utilizar a API Management na VNET](api-management-using-with-vnet.md)
