---
title: "Criar um gateway de aplicação utilizando regras de encaminhamento de URL | Microsoft Docs"
description: "Esta página fornece instruções para criar e configurar um gateway de aplicação do Azure através de regras de encaminhamento de URL."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: f2797864d7f0bda35d4d84ee78b157879451f889
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Criar um gateway de aplicação utilizando a com base no caminho de encaminhamento

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [CLI 2.0 do Azure](application-gateway-create-url-route-cli.md)

Com base no caminho de encaminhamento associa as rotas com base no caminho de URL de um pedido HTTP. Verifica se existe uma rota para um conjunto de back-end configurado para o URL apresentado no gateway de aplicação e, em seguida, envia o tráfego de rede para o conjunto de back-end definido. É uma utilização comum para encaminhamento baseado no URL para a carga de pedidos de saldo para diferentes tipos de conteúdo em agrupamentos de diferentes servidores de back-end.

Gateway de aplicação do Azure tem dois tipos de regra: encaminhamento básico e com base no caminho de encaminhamento. Basic fornece um serviço round robin para os conjuntos de back-end. Também é baseado no caminho de encaminhamento, além de distribuição de round robin, utiliza o padrão do caminho do URL do pedido para escolher o conjunto de back-end.

## <a name="scenario"></a>Cenário

No exemplo seguinte, o Gateway de aplicação serve tráfego para contoso.com com dois conjuntos de servidores de back-end: um agrupamento de servidores de vídeo e um conjunto de servidores de imagem.

Pedidos para http://contoso.com/image * são encaminhados para o agrupamento de servidores de imagem (**pool1**), e são encaminhados os pedidos para http://contoso.com/video * ao agrupamento de servidores de vídeo (**pool2**). Se nenhum dos padrões de caminho correspondem, um conjunto predefinido de servidor (**pool1**) está selecionado.

![rota de URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Crie uma rede virtual e uma sub-rede para um gateway de aplicação. Certifique-se de que nenhuma máquina virtual ou implementações de nuvem utilizam dessa sub-rede. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
3. Certifique-se de que existem servidores adicionados ao conjunto de back-end para o gateway de aplicação ou que têm os respetivos pontos finais criados na rede virtual ou com um público IP/VIP atribuído.

## <a name="requirements-to-create-an-application-gateway"></a>Requisitos para criar um gateway de aplicação

* **Agrupamento de servidores de back-end**: A lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ter um IP/VIP público.
* **Definições de agrupamento de servidores de back-end**: por exemplo, a afinidade com base em cookies, o protocolo e porta. Estes são associadas a um conjunto e aplicadas a todos os servidores dentro do conjunto.
* **Porta de front-end**: A porta pública aberta no gateway de aplicação. O tráfego chega a esta porta e, em seguida, redireciona para um dos servidores de back-end.
* **Serviço de escuta**: O serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https, que são maiúsculas e minúsculas) e o nome do certificado SSL (se configurar o SSL offload).
* **Regra**: A regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto do tráfego deve ser direcionado ao chegar a um serviço de escuta.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

A diferença entre utilizar o portal clássico do Azure e o Azure Resource Manager é a ordem pela qual criar o gateway de aplicação e os itens que devem ser configurados.

Com o Resource Manager, todos os itens que irão constituir um gateway de aplicação serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicação.

Siga estes passos para criar um gateway de aplicação:

1. Crie um grupo de recursos para o Resource Manager.
2. Crie uma rede virtual, uma sub-rede e um IP público para o gateway de aplicação.
3. Crie um objeto de configuração do gateway de aplicação.
4. Crie um recurso do gateway de aplicação.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

Certifique-se de que utiliza a versão mais recente do Azure PowerShell. Localizar mais informações em [utilizando o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passo 1

Inicie sessão no Azure.

```powershell
Login-AzureRmAccount
```

É-lhe pedido que autenticar com as suas credenciais.<BR>

### <a name="step-2"></a>Passo 2

Verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Passo 3

Escolha qual das subscrições do Azure utilizar. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passo 4

Crie um grupo de recursos. (Ignore este passo se estiver a utilizar um grupo de recursos existente.)

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

Em alternativa, pode criar etiquetas para um grupo de recursos para um gateway de aplicação:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

O Azure Resource Manager requer que os grupos de recursos especifiquem uma localização predefinida, que é utilizada para todos os recursos nesse grupo. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizem o mesmo grupo de recursos.

No exemplo anterior, é criado um grupo de recursos denominado "appgw-RG" e utilizou a localização "EUA Oeste".

> [!NOTE]
> Se precisar de configurar uma sonda personalizada para o gateway de aplicação, aceda a [criar um gateway de aplicação com sondas personalizadas com o PowerShell](application-gateway-create-probe-ps.md). Consulte [ estado de funcionamento do Gateway de aplicação descrição geral da monitorização](application-gateway-probe-overview.md) para obter mais informações.
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo que se segue mostra como criar uma rede virtual com o Resource Manager. Este exemplo cria uma rede virtual para o gateway de aplicação. Gateway de aplicação requer a sua própria sub-rede. Por este motivo, a sub-rede criada para o gateway de aplicação é menor do que o espaço de endereços de rede virtual. Isto permite que outros recursos, incluindo, mas não se limitando a servidores web, ser configurado na mesma rede virtual.

### <a name="step-1"></a>Passo 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável da sub-rede a utilizar para criar uma rede virtual.  Esta ação cria o objeto de configuração de sub-rede para o gateway de aplicação, o que é utilizado no exemplo seguinte.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Passo 2

Criar uma rede virtual denominada **appgwvnet** no grupo de recursos **appgw-rg** para a região EUA Oeste utilizando o prefixo 10.0.0.0/16 com 10.0.0.0/24 sub-rede. Isto conclui a configuração da rede virtual com uma única sub-rede para o gateway de aplicação residam.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Passo 3

Atribua a variável de sub-rede para conhecer os passos seguintes. Isto é passado para o `New-AzureRMApplicationGateway` cmdlet num passo futuro.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso de IP público **publicIP01** no grupo de recursos **appgw-rg** para a região E.U.A. Oeste. Gateway de aplicação pode utilizar um endereço IP público, um endereço IP ou ambos para receber pedidos para o balanceamento de carga.  Este exemplo utiliza apenas um endereço IP público. No exemplo seguinte, nenhum nome DNS está configurado para criar o endereço IP público, porque o Gateway de aplicação não suporta nomes DNS personalizados em endereços IP públicos.  Se um nome personalizado é necessário para o ponto final público, crie um registo CNAME para apontar para o nome de DNS automaticamente gerado para o endereço IP público.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-the-application-gateway-configuration"></a>Criar a configuração do gateway de aplicação

Todos os itens de configuração tem de ser configurados antes de criar o gateway de aplicação. Os seguintes passos criar itens de configuração necessários para um recurso de gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração de IP do gateway de aplicação com o nome **gatewayIP01**. Quando é iniciado o Gateway de aplicação, um endereço IP da sub-rede configurado, escolherá e rotas de rede de tráfego para os endereços IP no conjunto de IP back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Passo 2

Configurar o conjunto de endereços IP back-end com o nome **pool1** e **pool2** com endereços IP para **pool1** e **pool2**. Estes são os endereços IP dos recursos que alojam a aplicação web para ser protegido pelo gateway de aplicação. Estes membros do conjunto back-end são todos validados ser bom estado de funcionamento pelo sondas básicas ou personalizadas. Em seguida, o tráfego é encaminhado para estes membros quando os pedidos entram no gateway de aplicação. Conjuntos de back-end podem ser utilizados por várias regras dentro do gateway de aplicação. Isto significa que um conjunto de back-end pode ser utilizado para várias aplicações web que residem no mesmo anfitrião.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

Neste exemplo, dois conjuntos de back-end encaminhar o tráfego de rede com base no caminho de URL. Um conjunto recebe o tráfego do caminho de URL "/ vídeo," e o outro conjunto recebe o tráfego do caminho de "/ imagem." Substitua os endereços IP acima para adicionar os seus próprios pontos finais do endereço IP da aplicação. 

### <a name="step-3"></a>Passo 3

Configurar definições do gateway de aplicação **poolsetting01** e **poolsetting02** para o tráfego de rede com balanceamento de carga no conjunto de back-end. Neste exemplo, configure as definições de outro conjunto de back-end para os conjuntos de back-end. Cada conjunto de back-end pode ter as suas próprias definições.  As regras utilizam as definições de HTTP de back-end para encaminhar o tráfego para os membros do conjunto de back-end correto. Isto determina o protocolo e porta que é utilizada para enviar o tráfego para os membros do conjunto back-end. Sessões com base no cookie também são determinadas pelas definições de HTTP de back-end. Se estiver ativada, afinidade com base no cookie de sessão envia o tráfego para o mesmo back-end como anteriores pedidos para cada pacote.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Passo 4

Configure o IP de front-end com pontos finais IP públicos. Um serviço de escuta utiliza o objeto de configuração de IP Front-end para relacionar o endereço IP destinado ao outward com o serviço de escuta.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Passo 5

Configure a porta de front-end de um gateway de aplicação. O serviço de escuta utiliza o objeto de configuração de porta de front-end para definir a que o gateway de aplicação escuta tráfego pelo serviço de escuta de porta.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Passo 6

Configure o serviço de escuta para o endereço IP público e a porta utilizada para receber o tráfego de rede recebido. O exemplo seguinte executa a configuração de IP Front-end configurada anteriormente, a configuração de porta de front-end e um protocolo (Http ou Https, que são maiúsculas e minúsculas) e configura o serviço de escuta. Neste exemplo, o serviço de escuta tráfego HTTP na porta 80 no endereço IP público criado anteriormente.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Passo 7

Configure caminhos de regra de URL para os conjuntos de back-end. Este passo configura o caminho relativo utilizado pelo Gateway de aplicação e define o mapeamento entre o caminho de URL e o conjunto de back-end que tem atribuídos ao processar o tráfego de entrada.

> [!IMPORTANT]
> Cada caminho tem de começar com um "/" e um asterisco só é permitido no fim. Exemplos válidos são /xyz, /xyz*, ou /xyz/*. A cadeia que sejam fornecida ao matcher caminho não inclui qualquer texto após o primeiro "?" ou "#" e os carateres não são permitidas. 

O exemplo seguinte cria duas regras: um para um "/ imagem /" caminho encaminhamento tráfego para o back-end **pool1**e outra para um "/ vídeo /" caminho encaminhamento tráfego para o back-end **pool2**. Estas regras Certifique-se de que o tráfego para cada conjunto de URLs é encaminhado para o back-end. Por exemplo, http://contoso.com/image/figure1.jpg vai para **pool1** e http://contoso.com/video/example.mp4 entra em **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

Se o caminho não corresponde a qualquer uma das regras de caminho predefinido, a configuração de mapa de caminho de regra configura também um conjunto predefinido de endereços de back-end. Por exemplo, http://contoso.com/shoppingcart/test.html vai para **pool1** porque se encontra definida como o agrupamento predefinido para o tráfego sem correspondência.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Passo 8

Crie uma definição de regra. Este passo configura o gateway de aplicação para utilizar o URL com base no caminho de encaminhamento. O `$urlPathMap` variável definida no passo anterior agora é utilizado para criar a regra de caminho. Neste passo, vamos associar a regra a um serviço de escuta e o mapeamento de caminho de URL criada anteriormente.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Passo 9

Configure o número de instâncias e o tamanho do gateway de aplicação.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Crie um gateway de aplicação com todos os objetos de configuração dos passos anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Obter um nome de DNS do gateway de aplicação

Depois de criar o gateway, irá configurar o front-end para comunicação. Quando utilizar um IP público, o Gateway de aplicação requer um nome DNS dinamicamente atribuído, o que não é amigável. Para garantir que os clientes podem atingiu o gateway de aplicação, pode utilizar um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado para um serviço em nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Para configurar o registo CNAME de IP Front-end, obter detalhes sobre o gateway de aplicação e o seu nome IP/DNS associado utilizando o elemento de PublicIPAddress ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME. Não recomendamos a utilização de registos porque o VIP pode ser alterado de reinício do Gateway de aplicação.

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

Se pretender saber mais sobre a descarga de Secure Sockets Layer (SSL), consulte [configurar um gateway de aplicação para a descarga SSL de utilizando o Azure Resource Manager](application-gateway-ssl-arm.md).

