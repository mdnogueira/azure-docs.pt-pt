---
title: "Utilizar o Gateway de aplicação do Azure com o Balanceador de carga interno | Microsoft Docs"
description: "Esta página fornece instruções para configurar um Gateway de aplicação do Azure com um ponto final de balanceamento de carga interno"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 7403d28e-909f-46a2-b282-43a8e942f53c
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 7ca9307e8a78f6dade4b231fa3a0d83a68af3f21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Criar um Gateway de Aplicação com um Balanceador de Carga Interno (ILB)

> [!div class="op_single_selector"]
> * [Azure Classic PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager PowerShell](application-gateway-ilb-arm.md)

Gateway de aplicação pode ser configurado com um internet com o virtual IP ou com um ponto de final interno não exposto à internet, também conhecido como ponto final do Balanceador de carga interno (ILB). Configurar o gateway com um ILB é útil para aplicações de linha de negócio internas não expostas à internet. Também é útil para serviços/camadas dentro de uma aplicação multicamada, que se encontre num limite de segurança não exposto à internet, mas ainda necessitam de distribuição de carga round robin, a persistência da sessão ou a terminação de SSL. Este artigo descreve os passos para configurar um gateway de aplicação com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do PowerShell do Azure utilizando o instalador de plataforma Web. Pode transferir e instalar a versão mais recente do **do Windows PowerShell** secção o [página de transferência](https://azure.microsoft.com/downloads/).
2. Certifique-se de que tem uma rede virtual de trabalhar com sub-rede válida.
3. Certifique-se de que possui servidores de back-end na rede virtual ou com um IP/VIP público atribuído.

Para criar um gateway de aplicação, execute os seguintes passos pela ordem indicada. 

1. [Criar um gateway de aplicação](#create-a-new-application-gateway)
2. [Configurar o gateway](#configure-the-gateway)
3. [Definir a configuração do gateway](#set-the-gateway-configuration)
4. [Iniciar o gateway](#start-the-gateway)
5. [Verifique se o gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Crie um gateway de aplicação:

**Para criar o gateway**, utilize o `New-AzureApplicationGateway` cmdlet, substituindo os valores pelos seus. Neste ponto, ainda não é contabilizada a faturação do gateway. A faturação passará a ser contabilizada num passo posterior, quando o gateway tiver sido iniciado com êxito.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

```
VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399
```

**Para validar** que foi criado o gateway, pode utilizar o `Get-AzureApplicationGateway` cmdlet. 

No exemplo, *Descrição*, *InstanceCount*, e *GatewaySize* são parâmetros opcionais. O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pequenas e grandes outros valores disponíveis. *VIP* e *DnsName* são apresentados em branco, porque o gateway ainda não foi iniciado. Estes valores são criados assim que o gateway estiver em execução. 

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 4:39:39 PM - Begin Operation:
Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
Operation: Get-AzureApplicationGateway
Name: AppGwTest    
Description: 
VnetName: testvnet1 
Subnets: {Subnet-1} 
InstanceCount: 2 
GatewaySize: Medium 
State: Stopped 
VirtualIPs: 
DnsName:
```

## <a name="configure-the-gateway"></a>Configurar o gateway
Uma configuração de gateway de aplicação consiste de vários valores. Os valores que podem ser vinculados em conjunto para construir a configuração.

Os valores são:

* **Agrupamento de servidores de back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da VNet, ou devem ser um IP/VIP público. 
* **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** o serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https, estes são maiúsculas e minúsculas) e o nome do certificado SSL (se configurar o SSL offload). 
* **Regra:** a regra vincula o serviço de escuta e o agrupamento de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

Pode criar a configuração através da criação de um objeto de configuração ou através de um ficheiro XML de configuração. Para construir a configuração utilizando um ficheiro XML de configuração, utilize o exemplo abaixo.

Tenha em atenção o seguinte:

* O *FrontendIPConfigurations* elemento descreve os detalhes do ILB relevantes para configurar o Gateway de aplicação com um ILB. 
* O IP de front-end *tipo* deve ser definido como 'Private'
* O *StaticIPAddress* deve ser definido para o IP interno pretendido no qual o gateway recebe o tráfego. Tenha em atenção que o *StaticIPAddress* elemento é opcional. Se não for definido, um IP interno disponível da sub-rede implementado é escolhido. 
* O valor da *nome* elemento especificado na *FrontendIPConfiguration* deve ser utilizada no HTTPListener *FrontendIP* elemento referir-se para o FrontendIPConfiguration.
  
  **Exemplo XML de configuração**
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name> 
            <Type>Private</Type> 
            <StaticIPAddress>10.0.0.10</StaticIPAddress> 
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendIP>fip1</FrontendIP>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```


## <a name="set-the-gateway-configuration"></a>Definir a configuração do gateway
Em seguida, iremos definir o gateway de aplicação. Pode utilizar o `Set-AzureApplicationGatewayConfig` cmdlet com um objeto de configuração ou com um ficheiro XML de configuração. 

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

```
VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d
```

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois de configurar o gateway, utilize o cmdlet `Start-AzureApplicationGateway` para iniciar o gateway. A faturação de um gateway de aplicação é contabilizada depois de o gateway ser iniciado com êxito. 

> [!NOTE]
> O `Start-AzureApplicationGateway` cmdlet poderá demorar até 15 a 20 minutos a concluir. 
> 
> 

```powershell
Start-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b
```

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Utilize o `Get-AzureApplicationGateway` cmdlet para verificar o estado do gateway. Se `Start-AzureApplicationGateway` foi concluída com êxito no passo anterior, o estado deve ser *executar*, e o Vip e DnsName deverão ter entradas válidas. Este exemplo mostra o cmdlet na primeira linha, seguido pela saída. Neste exemplo, o gateway está em execução e está pronto para tirar o tráfego. 

> [!NOTE]
> O gateway de aplicação está configurado para aceitar o tráfego, o ponto final ILB configurado de 10.0.0.10 neste exemplo.

```powershell
Get-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   : 
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {10.0.0.10}
DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net
```

## <a name="next-steps"></a>Passos seguintes
Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

