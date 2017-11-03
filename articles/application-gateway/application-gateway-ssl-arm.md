---
title: "Configurar a descarga de SSL - Gateway de aplicação do Azure - PowerShell | Microsoft Docs"
description: "Este artigo fornece instruções para criar um gateway de aplicação com SSL offload utilizando o Gestor de recursos do Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: ee48ca45ae0d337b5b919dbbb28341caf8af0d45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurar um gateway de aplicação para a descarga de SSL com o Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [PowerShell clássico do Azure](application-gateway-ssl.md)
> * [CLI 2.0 do Azure](application-gateway-ssl-cli.md)

Pode configurar o Azure Application Gateway para terminar a sessão SSL (Secure Sockets Layer) no gateway para evitar tarefas dispendiosas de desencriptação de SSL que ocorrem no farm Web. A descarga de SSL simplifica ainda a configuração do servidor de front-end e a gestão da aplicação Web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Crie uma rede virtual e uma sub-rede para o gateway de aplicação. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que configurar para utilizar o gateway de aplicação devem existir ou tem os respetivos pontos finais criados na rede virtual ou com um endereço IP público ou endereço IP virtual (VIP) atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicação?

* **Agrupamento de servidores de back-end**: A lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
* **Definições de agrupamento de servidores de back-end**: cada conjunto tem definições como a porta, protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end**: esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta**: O serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https; estas definições são maiúsculas e minúsculas) e o nome do certificado SSL (se configurar o SSL offload).
* **Regra**: A regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end para direcionar o tráfego para quando chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deverá passar para **Https** (sensível às maiúsculas e minúsculas). Adicionar o **SslCertificate** elemento **HttpListener** com o valor da variável configurado para o certificado SSL. A porta de front-end deve ser atualizada para **443**.

**Para ativar a afinidade com base em cookies**: pode configurar um gateway de aplicação para se certificar de que um pedido de uma sessão de cliente é sempre direcionado para a mesma VM no web farm. Para tal, insira um cookie de sessão que permite ao gateway direcionar adequadamente o tráfego. Para ativar a afinidade com base em cookies, defina **CookieBasedAffinity** como **Ativado** no elemento **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

A diferença entre utilizar o modelo de implementação clássico do Azure e com o Azure Resource Manager é a ordem pela qual criar um gateway de aplicação e os itens que devem ser configurados.

Com o Resource Manager, todos os componentes de um gateway de aplicação são configurados individualmente e, em seguida, colocar em conjunto para criar um recurso de gateway de aplicação.

Os passos necessários para criar um gateway de aplicação encontram-se descritos abaixo:

1. [Criar um grupo de recursos para o Resource Manager](#create-a-resource-group-for-resource-manager)
2. [Criar rede virtual, uma sub-rede e um IP público para o gateway de aplicação](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [Criar um objeto de configuração do gateway de aplicação](#create-an-application-gateway-configuration-object)
4. [Crie um recurso de gateway de aplicação](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

Não se esqueça de mudar o modo do PowerShell para utilizar o cmdlets do Azure Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

   1. Introduza o seguinte comando:

   ```powershell
   Login-AzureRmAccount
   ```

   2. Para verificar as subscrições para a conta, introduza os seguintes comandos:

   ```powershell
   Get-AzureRmSubscription
   ```

   Ser-lhe-á solicitado para autenticar com as suas credenciais.

   3. A escolha qual das suas subscrições do Azure a utilizar, introduza o seguinte comando:

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Para criar um grupo de recursos, introduza o seguinte comando. (Ignore este passo se estiver a utilizar um grupo de recursos existente.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, Esta definição é utilizada como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizem o mesmo grupo de recursos.

No exemplo anterior, criámos um grupo de recursos denominado **appgw-RG** e a localização é **EUA oeste**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo que se segue mostra como criar uma rede virtual utilizando o Resource Manager:

   1. Introduza o seguinte comando:

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   Este exemplo atribui o intervalo de endereços **10.0.0.0/24** a uma variável de sub-rede a utilizar para criar uma rede virtual.

   2. Introduza o seguinte comando:

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   Este exemplo cria uma rede virtual denominada **appgwvnet** no grupo de recursos **appgw-rg** para o **EUA oeste** região utilizando o prefixo **10.0.0.0/16** com sub-rede **10.0.0.0/24**.

   3. Introduza o seguinte comando:

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   Este exemplo atribui o objeto de sub-rede à variável **$subnet** para conhecer os passos seguintes.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Para criar um endereço IP público para a configuração de front-end, introduza o seguinte comando:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Este exemplo cria um recurso IP público **publicIP01** no grupo de recursos **appgw-rg** para o **EUA oeste** região.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

   1. Para criar um objeto de configuração do gateway de aplicação, introduza o seguinte comando:

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   Este exemplo cria uma configuração de IP do gateway de aplicação com o nome **gatewayIP01**. Quando é iniciado o Gateway de aplicação, escolherá um endereço IP da sub-rede configurado e encaminha o tráfego de rede para os endereços IP no conjunto de IP back-end. Note que cada instância terá um endereço IP.

   2. Introduza o seguinte comando:

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   Este exemplo configura o conjunto de endereços IP back-end com o nome **pool01** com endereços IP **134.170.185.46**, **134.170.188.221**, e **134.170.185.50** . Estes valores são os endereços IP que irão receber o tráfego de rede proveniente do ponto final do IP de front-end. Substitua os endereços IP do exemplo acima pelos endereços IP dos pontos finais da sua aplicação Web.

   3. Introduza o seguinte comando:

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   Este exemplo configura a definição de gateway de aplicação **poolsetting01** para tráfego de rede de balanceamento de carga no conjunto de back-end.

   4. Introduza o seguinte comando:

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   Este exemplo configura a porta IP de front-end com o nome **frontendport01** para o ponto final IP público.

   5. Introduza o seguinte comando:

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   Este exemplo configura o certificado utilizado para a ligação SSL. O certificado tem de estar no formato PFX e a palavra-passe tem de ter entre 4 e 12 carateres.

   6. Introduza o seguinte comando:

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   Este exemplo cria a configuração de IP Front-end com o nome **fipconfig01** e associa o endereço IP público com a configuração de IP Front-end.

   7. Introduza o seguinte comando:

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   Este exemplo cria o serviço de escuta com o nome **listener01** e associa a porta de front-end para a configuração de IP Front-end e um certificado.

   8. Introduza o seguinte comando:

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   Este exemplo cria a regra de encaminhamento do Balanceador de carga com o nome **rule01** que configura o comportamento do Balanceador de carga.

   9. Introduza o seguinte comando:

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   Este exemplo configura o tamanho da instância do gateway de aplicação.

   > [!NOTE]
   > O valor predefinido para **InstanceCount** é **2**, com um valor máximo de 10. O valor predefinido para **GatewaySize** é **média**. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

   10. Introduza o seguinte comando:

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   Este passo define a política SSL a utilizar no gateway de aplicação. Para obter mais informações, consulte [versões de política de configurar o SSL e conjuntos de cifras no Gateway de aplicação](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicação com o New-AzureApplicationGateway

Introduza o seguinte comando:

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

Este exemplo cria um gateway de aplicação com todos os itens de configuração dos passos anteriores. No exemplo, o gateway de aplicação é designado **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Obter o nome DNS do gateway de aplicação

Depois de criado o gateway, o próximo passo é configurar o front-end para comunicação. Gateway de aplicação requer um nome DNS dinamicamente atribuído ao utilizar um IP público, que não é amigável. Para garantir que os utilizadores finais podem aceder o gateway de aplicação, pode utilizar um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para obter o nome DNS do gateway de aplicação, obter os detalhes do gateway de aplicação e o seu nome IP/DNS associado ao utilizar o **PublicIPAddress** elemento ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME, que aponta de aplicações dois web para este nome DNS. Iremos não recomendamos a utilização de registos de um, porque o VIP pode ser alterado de reiniciar o gateway de aplicação.


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

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, consulte [criar um gateway de aplicação com um balanceador de carga interno](application-gateway-ilb.md).

Para obter mais informações sobre balanceamento de carga em geral, consulte opções:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
