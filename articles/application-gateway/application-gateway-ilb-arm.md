---
title: "Utilizar o Gateway de aplicação do Azure com o Balanceador de carga interno - PowerShell | Microsoft Docs"
description: "Esta página fornece instruções para criar, configurar, iniciar e eliminar um gateway de aplicação do Azure com o balanceador de carga interno (ILB) do Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 8d96af009055a5c0349f0ac17054bebee4e54d36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Criar um gateway de aplicação com um balanceador de carga interno (ILB) com o Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Classic PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager PowerShell](application-gateway-ilb-arm.md)

Pode configurar o Application Gateway do Azure com um VIP de acesso à Internet ou com um ponto final interno não exposto à Internet, também conhecido como ponto final do balanceador de carga interno (ILB). Configurar o gateway com um ILB é útil para as aplicações de linha de negócio internas não expostas à Internet. Também é útil para serviços e camadas dentro de uma aplicação de várias camadas que assenta num limite de segurança que, não sendo exposto à Internet, requer a distribuição de carga round-robin, a persistência da sessão ou a terminação SSL (Secure Sockets Layer).

Este artigo descreve os passos para configurar um gateway de aplicação com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Vai criar uma rede virtual e uma sub-rede para o Application Gateway. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O Application Gateway tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que vai configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicação?

* **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à rede virtual, mas numa sub-rede diferente para o gateway de aplicação, ou ser um IP/VIP público.
* **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** o serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https, sensível às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
* **Regra:** a regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

## <a name="create-an-application-gateway"></a>Para criar um gateway de aplicação

A diferença entre a utilização do Azure Clássico e do Azure Resource Manager é a ordem pela qual vai criar o gateway de aplicação e os itens que devem ser configurados.
Com o Resource Manager, todos os itens que constituem um gateway de aplicação são configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicação.

Os passos necessários para criar um gateway de aplicação encontram-se descritos abaixo:

1. Criar um grupo de recursos para o Resource Manager
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicação
3. Criar um objeto de configuração do gateway de aplicação
4. Criar um recurso do gateway de aplicação

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

Não se esqueça de mudar o modo do PowerShell para utilizar o cmdlets do Azure Resource Manager. Para obter mais informações, veja [Using Windows PowerShell with Resource Manager (Usar o Windows PowerShell com o Resource Manager)](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passo 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Passo 2

Verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

Ser-lhe-á solicitado a autenticação com as suas credenciais.

### <a name="step-3"></a>Passo 3

Escolha qual das subscrições do Azure utilizar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passo 4

Crie um grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Verifique se todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

No exemplo anterior, criámos um grupo de recursos denominado "appgw-rg" e a localização "EUA Oeste".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo que se segue mostra como criar uma rede virtual utilizando o Resource Manager:

### <a name="step-1"></a>Passo 1

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Este passo atribui o endereço intervalo 10.0.0.0/24 a uma variável de sub-rede a utilizar para criar uma rede virtual.

### <a name="step-2"></a>Passo 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Este passo cria uma rede virtual com o nome "appgwvnet" no grupo de recursos "appgw-rg" para a região EUA Oeste utilizando o prefixo 10.0.0.0/16 com 10.0.0.0/24 sub-rede.

### <a name="step-3"></a>Passo 3

```powershell
$subnet = $vnet.subnets[0]
```

Este passo atribui o objeto de sub-rede à variável $subnet para conhecer os passos seguintes.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

### <a name="step-1"></a>Passo 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Este passo cria uma configuração de IP do gateway de aplicação com o nome "gatewayIP01". Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

### <a name="step-2"></a>Passo 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Este passo configura o conjunto de endereços IP back-end com o nome "pool01" com o IP endereços "10.1.1.8 10.1.1.9, 10.1.1.10". Estes endereços são os endereços IP que irão receber o tráfego de rede do ponto final do IP de front-end. Deve substituir os endereços IP acima para adicionar os seus próprios pontos finais do endereço IP da aplicação.

### <a name="step-3"></a>Passo 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Este passo configura o tráfego de rede do gateway definição "poolsetting01" para o tipo de carga com balanceamento de aplicação no conjunto de back-end.

### <a name="step-4"></a>Passo 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Este passo configura a porta IP de front-end com o nome "frontendport01" para o ILB.

### <a name="step-5"></a>Passo 5

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Este passo cria a configuração de IP Front-end denominada "fipconfig01" e associa-o com um IP privado a partir da atual sub-rede de rede virtual.

### <a name="step-6"></a>Passo 6

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Este passo cria o serviço de escuta chamado "listener01" e associa a porta de front-end à configuração de IP Front-end.

### <a name="step-7"></a>Passo 7

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Este passo cria a regra encaminhamento de Balanceador de carga chamada "rule01", que configura o comportamento do Balanceador de carga.

### <a name="step-8"></a>Passo 8

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Este passo configura o tamanho da instância do gateway de aplicação.

> [!NOTE]
> O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicação com o New-AzureApplicationGateway

Cria um gateway de aplicação com todos os itens de configuração dos passos anteriores. Neste exemplo, o gateway de aplicação é designado “appgwtest”.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Este passo cria um gateway de aplicação com todos os itens de configuração dos passos anteriores. No exemplo, o gateway de aplicação é designado “appgwtest”.

## <a name="delete-an-application-gateway"></a>Eliminar um gateway de aplicação

Para eliminar um gateway de aplicação, terá de realizar os seguintes passos por ordem:

1. Utilize o cmdlet `Stop-AzureRmApplicationGateway` para parar o gateway.
2. Utilize o cmdlet `Remove-AzureRmApplicationGateway` para remover o gateway.
3. Verifique se o gateway foi removido com o cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Passo 1

Obtenha o objeto do gateway de aplicação e associe-o a uma variável “$getgw”.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Passo 2

Utilize `Stop-AzureRmApplicationGateway` para parar o gateway de aplicação. Este exemplo mostra o `Stop-AzureRmApplicationGateway` cmdlet na primeira linha, seguido pela saída.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Assim que o gateway de aplicação estiver no estado parado, utilize o cmdlet `Remove-AzureRmApplicationGateway` para remover o serviço.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Pode utilizar o comutador **-force** para eliminar a mensagem de confirmação de remoção.

Para verificar se o serviço foi removido, pode utilizar o cmdlet `Get-AzureRmApplicationGateway`. Este passo não é necessário.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Passos seguintes

Se pretender configurar a descarga de SSL, veja [Configure an application gateway for SSL offload (Configurar um gateway de aplicação para a descarga de SSL)](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um ILB, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

