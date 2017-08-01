---
title: "Criar e gerir um Gateway de Aplicação do Azure – PowerShell | Microsoft Docs"
description: "Esta página fornece instruções para criar, configurar, iniciar e eliminar um gateway de aplicação do Azure com o Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 6d38dd6802a25b147fd014b4d26ca432ca87a07d
ms.contentlocale: pt-pt
ms.lasthandoff: 07/25/2017

---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Criar, iniciar ou eliminar um gateway de aplicação com o Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure](application-gateway-create-gateway-cli.md)

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local.
O Gateway de Aplicação proporciona muitas funcionalidades de Controlador de Entrega de Aplicação (ADC), incluindo o balanceamento de carga HTTP, afinidade por sessões com base em cookies, descarga do SSL (Secure Sockets Layer), sondas de estado de funcionamento personalizadas, suporte multilocal e muitas outras.

Para encontrar uma lista completa das funcionalidades suportadas, visite [Descrição geral do Gateway de Aplicação](application-gateway-introduction.md)

Este artigo descreve os passos para criar, configurar, iniciar e eliminar um gateway de aplicação.

> [!IMPORTANT]
> Antes de trabalhar com recursos do Azure, é importante compreender que o Azure tem atualmente dois modelos de implementação: o Resource Manager e o clássico. Certifique-se de que compreende os [modelos e ferramentas de implementação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Pode ver a documentação de diversas ferramentas clicando nos separadores na parte superior deste artigo. Este documento aborda a criação de um gateway de aplicação com o Azure Resource Manager. Para utilizar a versão clássica, aceda a [Create an application gateway classic deployment by using PowerShell (Criar uma implementação clássica do gateway de aplicação com o PowerShell)](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
1. Se tiver uma rede virtual existente, selecione uma subrede vazia existente ou crie uma subrede na sua rede virtual existente apenas para utilização pelo gateway de aplicação. Não é possível implementar o gateway de aplicação a uma rede virtual diferente dos recursos que pretende implementar por trás do gateway de aplicação.
1. Os servidores que irá configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicação?

* **Agrupamento de servidores de back-end:** a lista de endereços IP, FQDNs ou NICs dos servidores de back-end. Se os endereços IP forem utilizados, devem pertencer à sub-rede da rede virtual ou ser um IP/VIP público.
* **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** o serviço de escuta tem uma porta de front-end, um protocolo (Http ou Https; estes valores são sensíveis às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
* **Regra:** a regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

A diferença entre a utilização do Azure Clássico e do Azure Resource Manager é a ordem pela qual vai criar o gateway de aplicação e os itens que devem ser configurados.

Com o Resource Manager, todos os itens que irão constituir um gateway de aplicação serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicação.

Os passos necessários para criar um gateway de aplicação estão descritos abaixo.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

Verifique se está a utilizar a versão mais recente do Azure PowerShell. Para obter mais informações, veja [Using Windows PowerShell with Resource Manager (Usar o Windows PowerShell com o Resource Manager)](../powershell-azure-resource-manager.md).

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
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Verifique se todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

No exemplo acima, criámos um grupo de recursos denominado **appgw-RG** e a localização **E.U.A. Oeste**.

> [!NOTE]
> Se tiver de configurar uma sonda personalizada para o gateway de aplicação, visite: [Criar um gateway de aplicação com sondas personalizadas através do PowerShell](application-gateway-create-probe-ps.md). Veja [monitorização das sondas personalizadas e do estado de funcionamento](application-gateway-probe-overview.md) para obter mais informações.

## <a name="create-a-virtual-network-and-a-subnet"></a>Crie uma rede virtual e uma sub-rede

O exemplo que se segue mostra como criar uma rede virtual com o Resource Manager. Este exemplo cria uma VNET para o Gateway de Aplicação. O Gateway de Aplicação requer uma sub-rede própria, pelo que, por esta razão, a sub-rede criada para o Gateway de Aplicação é mais pequena do que o espaço de endereços da VNET. Com uma sub-rede mais pequena, são permitidos outros recursos, incluindo, sem limitação, servidores Web a ser configurados na mesma VNET.

### <a name="step-1"></a>Passo 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável da sub-rede a utilizar para criar uma rede virtual. Este passo cria o objeto de configuração da sub-rede para o Gateway de Aplicação, que é utilizado no exemplo seguinte.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Passo 2

Crie uma rede virtual com o nome **appgwvnet** no grupo de recursos **appgw-rg** para a região E.U.A. Oeste utilizando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24. Este passo conclui a configuração da VNET com uma única sub-rede para o Gateway de Aplicação a residir.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Passo 3

Atribua a variável da sub-rede para os passos seguintes. Esta variável é transmitida para o cmdlet `New-AzureRMApplicationGateway` num passo subsequente.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Crie um recurso de IP público **publicIP01** no grupo de recursos **appgw-rg** para a região E.U.A. Oeste. O Gateway de Aplicação pode utilizar um endereço IP público, um endereço IP interno ou ambos para receber pedidos para balanceamento de carga.  Este exemplo apenas utiliza um endereço IP público. No exemplo seguinte, não está configurado nenhum nome DNS para criar o endereço IP público.  O Gateway de Aplicação não suporta nomes DNS personalizados em endereços IP públicos.  Se for necessário um nome personalizado para o ponto final público, deve criar um registo CNAME para apontar para o nome DNS gerado automaticamente para o endereço IP público.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-the-application-gateway-configuration-objects"></a>Criar os objetos de configuração do gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração de IP do gateway de aplicação com o nome **gatewayIP01**. Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Passo 2

Configure o conjunto de endereços IP de back-end com o nome **pool01** com os endereços IP para **pool1**. Estes são os endereços IP dos recursos que alojam a aplicação Web que será protegida pelo gateway de aplicação. Estes membros do conjunto de back-end são todos validados como estando em bom estado de funcionamento por sondas, sejam estas básicas ou personalizadas.  Em seguida, o tráfego é encaminhado para estes membros quando os pedidos entram no gateway de aplicação. Os conjuntos de back-end podem ser utilizados por várias regras dentro do gateway de aplicação, o que significa que um conjunto de back-end pode ser utilizado para várias aplicações Web que residam no mesmo anfitrião.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

Neste exemplo, existem dois conjuntos de back-end para encaminhar o tráfego de rede com base no caminho do URL. Um conjunto recebe tráfego do caminho do URL "/video" e outro conjunto recebe tráfego do caminho "/image". Substitua os endereços IP acima para adicionar os seus próprios pontos finais do endereço IP da aplicação.

### <a name="step-3"></a>Passo 3

Configure a definição do gateway de aplicação **poolsetting** para o tráfego de rede com balanceamento de carga no conjunto de back-end. Cada conjunto de back-end pode ter a sua própria definição de conjunto de back-end.  As definições de HTTP de back-end são utilizadas por regras para encaminhar tráfego para os membros do conjunto de back-end corretos. As definições de HTTP de back-end determinam o protocolo e a porta utilizados ao enviar tráfego para os membros do conjunto de back-end. As sessões com base em cookies também são determinadas pelas definições de HTTP de back-end.  Se estiver ativada, a afinidade de sessão baseada em cookies envia o tráfego para o mesmo back-end de pedidos anteriores para cada pacote.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>Passo 4

Configure a porta de front-end de um gateway de aplicação. O objeto de configuração da porta de front-end é utilizado por um serviço de escuta para definir a porta em que o Gateway de Aplicação escuta tráfego no serviço de escuta.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Passo 5

Configure o IP de front-end com o ponto final de IP público. O objeto de configuração do IP de front-end é utilizado por um serviço de escuta para relacionar o endereço IP com acesso para fora com o serviço de escuta.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Passo 6

Configure o serviço de escuta. Este passo configura o serviço de escuta para o endereço IP público e a porta utilizados para receber o tráfego de rede de entrada. O exemplo seguinte utiliza a configuração anterior do IP de front-end, a configuração da porta de front-end e um protocolo (http ou https) para configurar o serviço de escuta. Neste exemplo, o serviço de escuta tráfego HTTP na porta 80 no endereço IP público criado anteriormente.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Passo 7

Crie a regra encaminhamento do balanceador de carga com o nome **rule01**, que configura o comportamento do balanceador de carga. As definições do conjunto de back-end, o serviço de escuta e o conjunto de back-end criados nos passos anteriores constituem a regra. Com base nos critérios definidos, o tráfego é encaminhado para o back-end correto.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Passo 8

Configure o número de instâncias e o tamanho do gateway de aplicação.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> O valor predefinido para **InstanceCount** é 2, com um valor máximo de 10. O valor predefinido para **GatewaySize** é Médio. Pode escolher entre **Standard_Small**, **Standard_Medium** e **Standard_Large**.

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Crie um gateway de aplicação com todos os itens de configuração indicados nos passos anteriores. Neste exemplo, o gateway de aplicação é designado **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Obter os detalhes de DNS e VIP do gateway da aplicação do recurso de IP público anexado ao gateway de aplicação.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>Eliminar o gateway de aplicação

Siga estes passos para eliminar um gateway de aplicação:

### <a name="step-1"></a>Passo 1

Obtenha o objeto do gateway de aplicação e associe-o a uma variável `$getgw`.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Passo 2

Utilize `Stop-AzureRmApplicationGateway` para parar o gateway de aplicação.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

Assim que o gateway de aplicação estiver no estado parado, utilize o cmdlet `Remove-AzureRmApplicationGateway` para remover o serviço.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> Pode utilizar o comutador **-force** para eliminar a mensagem de confirmação de remoção.

Para verificar se o serviço foi removido, pode utilizar o cmdlet `Get-AzureRmApplicationGateway`. Este passo não é necessário.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Obter nome de DNS de gateway de aplicação

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Ao utilizar um IP público, o gateway de aplicação requer um nome DNS dinamicamente atribuído, que não é amigável. Para garantir que os utilizadores finais alcançam o gateway de aplicação, pode utilizar-se um registo CNAME para apontar para o ponto final público do gateway de aplicação. [Configurar um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para encontrar o nome DNS criado de forma dinâmica, obtenha os detalhes do gateway de aplicação e o respetivo nome IP/DNS associado através do elemento PublicIPAddress ligado ao gateway de aplicação. O nome DNS do gateway de aplicação deve servir para criar um registo CNAME, que aponta as duas aplicações Web para este nome DNS. Não é recomendada a utilização de registos A, uma vez que o VIP pode ser alterado no reinício do gateway de aplicação.

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

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Para eliminar todos os recursos criados neste artigo, conclua os seguintes passos:

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>Passos seguintes

Se quiser configurar a descarga de SSL, visite: [Configurar um gateway de aplicação para descarga de SSL](application-gateway-ssl.md).

Se quiser configurar um gateway de aplicação para utilizar com um balanceador de carga interno, visite: [Criar um gateway de aplicação com um balanceador de carga interno (ILB)](application-gateway-ilb.md).

Se quiser mais informações sobre as opções de balanceamento de carga em geral, visite:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)


