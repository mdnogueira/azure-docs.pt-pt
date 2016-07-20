<properties
   pageTitle="Criar, iniciar ou eliminar um gateway de aplicação | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e eliminar um gateway de aplicação do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma"/>

# Criar, iniciar ou eliminar um gateway de aplicação

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local. O Aplicação Gateway tem as seguintes funcionalidades de entrega de aplicações: balanceamento de carga HTTP, afinidade de sessão com base em cookies e descarga de SSL (Secure Sockets Layer).

> [AZURE.SELECTOR]
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Modelo Azure Resource Manager](application-gateway-create-gateway-arm-template.md)


<BR>

Este artigo descreve os passos para criar, configurar, iniciar e eliminar um gateway de aplicação.


## Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Verifique se a rede virtual funciona com uma sub-rede válida. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que irá configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um gateway de aplicação?


Quando utiliza o comando **New-AzureApplicationGateway** para criar o gateway de aplicação, nenhuma configuração é definida nesta fase, pelo que o recurso recém-criado terá de ser configurado com o XML ou um objeto de configuração.


Os valores são:

- **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ter um IP/VIP público.
- **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
- **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
- **Serviço de escuta:** o serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https, sensível às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
- **Regra:** a regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta.


## Criar um novo gateway de aplicação

Para criar um gateway de aplicação:

1. Crie um recurso do gateway de aplicação.
2. Crie um ficheiro XML de configuração ou um objeto de configuração.
3. Confirme a configuração para o recurso de gateway de aplicação criado recentemente.

>[AZURE.NOTE] Se tiver de configurar uma sonda personalizada para o gateway de aplicação, veja [Create an application gateway with custom probes by using PowerShell (Criar um gateway de aplicação com sondas personalizadas com o PowerShell)](application-gateway-create-probe-classic-ps.md). Veja [monitorização das sondas personalizadas e do estado de funcionamento](application-gateway-probe-overview.md) para obter mais informações.


### Criar um recurso do gateway de aplicação

Para criar o gateway, utilize o cmdlet **New-AzureApplicationGateway**, substituindo os valores pelos seus próprios valores. Neste ponto, ainda não é contabilizada a faturação do gateway. A faturação passará a ser contabilizada num passo posterior, quando o gateway tiver sido iniciado com êxito.

O exemplo que se segue cria um novo gateway de aplicação com uma rede virtual denominada “testvnet1” e uma sub-rede denominada “subnet-1”.


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Descrição*, *InstanceCount* e *GatewaySize* são parâmetros opcionais.


Para validar a criação do gateway, pode utilizar o cmdlet **Get-AzureApplicationGateway**.




    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre Pequeno, Médio e Grande.


 *VirtualIPs* e *DnsName* são apresentados em branco, porque o gateway ainda não foi iniciado. Estes valores poderão ser criados assim que o gateway estiver em execução.

## Configurar o gateway de aplicação

Pode configurar o gateway de aplicação com XML ou um objeto de configuração.

## Configurar o gateway de aplicação com XML

No exemplo que se segue, irá utilizar um ficheiro XML para configurar todas as definições do gateway de aplicação e consolidá-las para o recurso do gateway de aplicação.  

### Passo 1  

Copie o seguinte texto para o Bloco de Notas.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Edite os valores entre parêntesis dos itens de configuração. Guarde o ficheiro com a extensão .xml.

>[AZURE.IMPORTANT] O item do protocolo Http ou Https é sensível às maiúsculas e minúsculas.

O exemplo que se segue mostra como utilizar um ficheiro de configuração para configurar o gateway de aplicação para o balanceamento de carga do tráfego HTTP na porta pública 80 e como enviar tráfego de rede para a porta 80 de back-end entre dois endereços IP.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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


### Passo 2

Em seguida, configure o gateway de aplicação. Utilize o cmdlet **Set-AzureApplicationGatewayConfig** com um ficheiro XML de configuração.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Configurar o gateway de aplicação com um objeto de configuração

O exemplo que se segue mostra como configurar o gateway de aplicação com objetos de configuração. Todos os itens de configuração devem ser configurado individualmente e, em seguida, adicionados a um objeto de configuração do gateway de aplicação. Depois de criar o objeto de configuração, utilize o comando **Set-AzureApplicationGateway** para consolidar a configuração para o recurso de gateway de aplicação criado anteriormente.

>[AZURE.NOTE] Antes de atribuir um valor a cada objeto de configuração, tem de indicar qual o tipo de objeto utilizado pelo PowerShell para o armazenamento. A primeira linha para criar os itens individuais define qual Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (nome do objeto) será utilizado.

### Passo 1

Crie todos os itens de configuração individuais.

Crie o IP de front-end, conforme mostrado no exemplo seguinte.

    PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    PS C:\> $fip.Name = "fip1"
    PS C:\> $fip.Type = "Private"
    PS C:\> $fip.StaticIPAddress = "10.0.0.5"

Crie a porta de front-end, conforme mostrado no exemplo seguinte.

    PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    PS C:\> $fep.Name = "fep1"
    PS C:\> $fep.Port = 80

Crie o conjunto de servidores de back-end.

 Defina os endereços IP que serão adicionados ao conjunto de servidores de back-end, conforme mostrado no exemplo seguinte.


    PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    PS C:\> $servers.Add("10.0.0.1")
    PS C:\> $servers.Add("10.0.0.2")

 Utilize o objeto $server para adicionar os valores ao objeto do conjunto de back-end ($pool).

    PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    PS C:\> $pool.BackendServers = $servers
    PS C:\> $pool.Name = "pool1"

Crie a definição do conjunto de servidores de back-end.

    PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    PS C:\> $setting.Name = "setting1"
    PS C:\> $setting.CookieBasedAffinity = "enabled"
    PS C:\> $setting.Port = 80
    PS C:\> $setting.Protocol = "http"

Crie o serviço de escuta.

    PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    PS C:\> $listener.Name = "listener1"
    PS C:\> $listener.FrontendPort = "fep1"
    PS C:\> $listener.FrontendIP = "fip1"
    PS C:\> $listener.Protocol = "http"
    PS C:\> $listener.SslCert = ""

Crie a regra.

    PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    PS C:\> $rule.Name = "rule1"
    PS C:\> $rule.Type = "basic"
    PS C:\> $rule.BackendHttpSettings = "setting1"
    PS C:\> $rule.Listener = "listener1"
    PS C:\> $rule.BackendAddressPool = "pool1"

### Passo 2

Atribua todos os itens individuais de configuração a um objeto de configuração do gateway de aplicação ($appgwconfig).

Adicione o IP de front-end à configuração.

    PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

Adicione a porta de front-end à configuração.

    PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Adicione o conjunto de servidores de front-end à configuração.

    PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Adicione a definição do conjunto de front-end à configuração.

    PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

Adicione o serviço de escuta à configuração.

    PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    PS C:\> $appgwconfig.HttpListeners.Add($listener)

Adicione a regra à configuração.

    PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### Passo 3

Consolide o objeto de configuração para o recurso de gateway de aplicação com **Set-AzureApplicationGatewayConfig**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Iniciar o gateway

Depois de configurar o gateway, utilize o cmdlet **Start-AzureApplicationGateway** para iniciar o gateway. A faturação de um gateway de aplicação é contabilizada depois de o gateway ser iniciado com êxito.


> [AZURE.NOTE] O cmdlet **Start-AzureApplicationGateway** poderá demorar entre 15 a 20 minutos.



    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verificar o estado do gateway

Utilize o cmdlet **Get-AzureApplicationGateway** para verificar o estado do gateway. Se o comando **Start-AzureApplicationGateway** tiver sido concluído com êxito no passo anterior, o *Estado* deve ser Em Execução e os valores *Vip* e *DnsName* deverão ter entradas válidas.

O exemplo que se segue mostra um gateway de aplicação ativo, em execução e pronto a passar o tráfego para `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Eliminar um gateway de aplicação

Para eliminar um gateway de aplicação:

1. Utilize o cmdlet **Stop-AzureApplicationGateway** para parar o gateway.
2. Utilize o cmdlet **Remove-AzureApplicationGateway** para remover o gateway.
3. Verifique se o gateway foi removido com o cmdlet **Get-AzureApplicationGateway**.

O exemplo que se segue mostra o cmdlet **Stop-AzureApplicationGateway** na primeira linha, seguido pela saída.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Assim que o gateway de aplicação estiver no estado parado, utilize o cmdlet **Remove-AzureApplicationGateway** para remover o serviço.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Para verificar se o serviço foi removido, pode utilizar o cmdlet **Get-AzureApplicationGateway**. Este passo não é necessário.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## Passos seguintes

Se pretender configurar a descarga de SSL, veja [Configure an application gateway for SSL offload (Configurar um gateway de aplicação para a descarga de SSL)](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Traffic Manager do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=Jun16_HO2-->


