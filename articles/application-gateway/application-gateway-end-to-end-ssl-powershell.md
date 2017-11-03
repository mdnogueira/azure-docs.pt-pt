---
title: "Configurar o SSL de ponto a ponto com o Gateway de aplicação do Azure | Microsoft Docs"
description: "Este artigo descreve como configurar o SSL de ponto a ponto com Gateway de aplicação do Azure utilizando o PowerShell"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: e6d80a33-4047-4538-8c83-e88876c8834e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: df14d5c4572a250f9f8951ee3b86e87e6f652782
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Configurar o SSL de ponto a ponto ao utilizar o Gateway de aplicação com o PowerShell

## <a name="overview"></a>Descrição geral

Gateway de aplicação do Azure suporta a encriptação de ponto a ponto do tráfego. Gateway de aplicação termina a ligação SSL no gateway de aplicação. O gateway, em seguida, aplica as regras de encaminhamento para o tráfego, reencripta o pacote e reencaminha o pacote para o servidor de back-end adequado com base nas regras de encaminhamento definidas. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final.

Gateway de aplicação suporta definir opções personalizadas de SSL. Também suporta desativar as seguintes versões de protocolo: **TLSv1.0**, **TLSv1.1**, e **TLSv1.2**, bem a definir os conjuntos de cifras a utilizar e a ordem de preferência . Para saber mais acerca das opções configuráveis SSL, consulte o [descrição geral da política SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 e o SSL 3.0 estão desativados por predefinição e não podem ser ativada. Estes são considerados não seguros e não podem ser utilizados com o Gateway de aplicação.

![imagem do cenário][scenario]

## <a name="scenario"></a>Cenário

Neste cenário, irá aprender a criar um gateway de aplicação, utilizando SSL de ponto a ponto com o PowerShell.

Neste cenário irão:

* Crie um grupo de recursos denominado **appgw-rg**.
* Criar uma rede virtual denominada **appgwvnet** com um espaço de endereços de **10.0.0.0/16**.
* Criar duas sub-redes chamadas **appgwsubnet** e **appsubnet**.
* Crie uma encriptação de SSL de ponto-a-ponto suporte para aplicações pequeno gateway que as versões de protocolo SSL de limites e os conjuntos de cifras.

## <a name="before-you-begin"></a>Antes de começar

Para configurar o SSL de ponto a ponto com um gateway de aplicação, um certificado é necessário para o gateway e os certificados são necessários para os servidores de back-end. O certificado de gateway é utilizado para encriptar e desencriptar o tráfego enviado para o mesmo através de SSL. O certificado do gateway tem de estar no formato de Personal Information (Exchange PFX). Este formato de ficheiro permite-lhe exportar a chave privada que é necessário para o gateway de aplicação para efetuar a encriptação e desencriptação de tráfego.

Para a encriptação de SSL de ponto a ponto, o back-end tem de ser na lista de permissões com o gateway de aplicação. Terá de carregar o certificado público de servidores de back-end para o gateway de aplicação. Adicionar o certificado assegura que o gateway de aplicação comunica apenas com instâncias de back-end conhecidas. Isto ainda mais além de proteger a comunicação de ponto a ponto.

O processo de configuração é descrito nas secções seguintes.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta secção explica como criar um grupo de recursos que contém o gateway de aplicação.


   1. Inicie sessão sua conta do Azure.

   ```powershell
   Login-AzureRmAccount
   ```


   2. Selecione a subscrição a utilizar para este cenário.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Crie um grupo de recursos. (Ignore este passo se estiver a utilizar um grupo de recursos existente.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte cria uma rede virtual e duas sub-redes. Uma sub-rede é utilizada para conter o gateway de aplicação. A outra sub-rede é utilizada para os back-ends que alojam a aplicação web.


   1. Atribua um intervalo de endereços da sub-rede a utilizar para o gateway de aplicação.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Sub-redes configuradas para um gateway de aplicação devem ser corretamente dimensionados. Um gateway de aplicação pode ser configurado para até 10 instâncias. Cada instância tem um endereço IP da sub-rede. Demasiado pequeno de uma sub-rede pode afetar negativamente ampliar um gateway de aplicação.
   > 
   > 

   2. Atribua um intervalo de endereços a ser utilizada para o conjunto de endereços de back-end.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Crie uma rede virtual com as sub-redes definidas nos passos anteriores.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Obter o recurso de rede virtual e recursos de sub-rede para ser utilizada nos passos que se seguem.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público a utilizar para o gateway de aplicação. Este endereço IP público é utilizado em um dos passos que se seguem.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Gateway de aplicação não suporta a utilização de um endereço IP público criado com uma etiqueta de domínio definido. Apenas um endereço IP público com uma etiqueta de domínio criada dinamicamente é suportado. Se necessitar de um nome amigável de DNS para o gateway de aplicação, recomendamos que utilize um registo CNAME com um alias.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

Todos os itens de configuração estão definidos antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

   1. Crie uma configuração de IP do gateway de aplicação. Esta definição configura que as sub-redes que se utiliza o gateway de aplicação. Quando é iniciado o gateway de aplicação, um endereço IP da sub-rede configurado, escolherá e rotas de rede de tráfego para os endereços IP no conjunto de IP back-end. Note que cada instância terá um endereço IP.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Crie uma configuração de IP Front-end. Esta definição mapeia um endereço IP privado ou público para o front-end do gateway de aplicação. O passo seguinte associa o endereço IP público no passo anterior com a configuração de IP Front-end.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Configure o conjunto de endereços IP back-end com os endereços IP dos servidores web de back-end. Estes endereços são os endereços IP que recebem o tráfego de rede que vem do ponto final do IP do front-end. Substitua os endereços IP na amostra os seus pontos finais endereço de IP de aplicação.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Um nome de domínio completamente qualificado (FQDN) também é um valor válido para utilizar em vez de um endereço IP para os servidores de back-end. Ativá-la utilizando o **- BackendFqdns** mudar. 


   4. Configure a porta IP de front-end para o ponto final IP público. Esta porta é a porta que os utilizadores finais à.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. Configure o certificado para o gateway de aplicação. Este certificado é utilizado para desencriptar e reencrypt o tráfego no gateway de aplicação.

   ```powershell
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
   ```

   > [!NOTE]
   > Este exemplo configura o certificado utilizado para a ligação SSL. O certificado tem de estar no formato. pfx e a palavra-passe tem de ser 4 e 12 carateres.

   6. Crie o serviço de escuta HTTP para o gateway de aplicação. Atribua a configuração de IP Front-end, a porta e o certificado SSL a utilizar.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Carregue o certificado a ser utilizada nos recursos SSL ativado o conjunto de back-end.

   > [!NOTE]
   > A sonda predefinida obtém a chave pública a partir de *predefinido* enlace de SSL no endereço IP e compara o valor da chave público receber para o valor da chave público fornecer aqui o back-end. 
   
   > Se estiver a utilizar cabeçalhos de anfitrião e a indicação de nome de servidor (SNI) no back-end, a chave pública obtida poderá não ser site pretendido para os fluxos de tráfego. Se estiver em dúvida, visite https://127.0.0.1/ nos servidores de back-end para confirmar o certificado é utilizado para o *predefinido* enlace SSL. Utilize a chave pública esse pedido nesta secção. Se estiver a utilizar cabeçalhos de anfitrião e SNI nos enlaces de HTTPS e não receber uma resposta e o certificado de um pedido de manual browser para https://127.0.0.1/ nos servidores de back-end, tem de configurar um enlace de SSL predefinido nos mesmos. Se o fizer, sondas falharem e o back-end não está na lista de permissões.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > O certificado fornecido neste passo, deve ser a chave pública do certificado. pfx presente no back-end. Exportar o certificado (não o certificado de raiz) instalado no servidor de back-end no formato de afirmação, provas e Reasoning (CER) e utilizá-lo neste passo. Este passo whitelists back-end com o gateway de aplicação.

   8. Configure as definições de HTTP para o application gateway back-end. Atribua o certificado carregado no passo anterior para as definições de HTTP.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Crie uma regra de encaminhamento do Balanceador de carga que configura o comportamento do Balanceador de carga. Neste exemplo, é criada uma regra round-robin básica.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Configure o tamanho da instância do gateway de aplicação. Os tamanhos disponíveis são **padrão\_pequeno**, **padrão\_média**, e **padrão\_grande**.  A capacidade, os valores disponíveis são **1** através de **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > Uma contagem de instâncias de 1 pode ser escolhida para fins de teste. É importante saber que quaisquer instâncias em duas instâncias não é abrangida pelo SLA e, por conseguinte, não é recomendada. Gateways pequenos estão a ser utilizado para teste de desenvolvimento e não para fins de produção.

   11. Configure a política SSL para ser utilizado no gateway de aplicação. Gateway de aplicação suporta a capacidade de definir a versão mínima para as versões de protocolo SSL.

   Os valores seguintes são uma lista das versões de protocolo que pode ser definida:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   O exemplo seguinte define a versão do protocolo mínimo para **TLSv1_2** e permite **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** apenas.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Utilize os passos anteriores para criar o gateway de aplicação. A criação do gateway é um processo que demora muito tempo a executar.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Limitar as versões de protocolo SSL num gateway de aplicação existente

Os passos anteriores demorou criar uma aplicação com ponto-a-ponto SSL e desativar determinadas versões de protocolo SSL. O exemplo a seguir desativa determinadas políticas SSL num gateway de aplicação existente.

   1. Obter o gateway de aplicação para atualizar.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Defina uma política SSL. No exemplo seguinte, **TLSv1.0** e **TLSv1.1** são desativadas e os conjuntos de cifras **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** são os únicos aqueles permitido.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Por fim, atualize o gateway. Tenha em atenção que neste último passo é uma tarefa de longa execução. Quando terminar, ponto-a-ponto SSL está configurado no gateway de aplicação.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Obter um nome de DNS do gateway de aplicação

Depois de criado o gateway, o próximo passo é configurar o front-end para comunicação. Gateway de aplicação requer um nome DNS dinamicamente atribuído ao utilizar um IP público, que não é amigável. Para garantir que os utilizadores finais podem aceder o gateway de aplicação, pode utilizar um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado para no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um alias, obter detalhes sobre o gateway de aplicação e o seu nome IP/DNS associado utilizando o **PublicIPAddress** elemento ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME que aponta de aplicações dois web para este nome DNS. Iremos não recomendamos a utilização de registos de um, porque o VIP pode ser alterado de reiniciar o gateway de aplicação.

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

Para obter mais informações sobre a segurança das suas aplicações web com Firewall de aplicações Web através do Gateway de aplicação de proteção, consulte o [descrição geral de firewall de aplicações Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
