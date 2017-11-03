---
title: "Configurar o modo de distribuição do Balanceador de carga do Azure | Microsoft Docs"
description: "Como configurar o modo de distribuição de Balanceador de carga do Azure suportar a afinidade do IP de origem."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d04a469c04553b7d6a14df7054ad5ef795baa500
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurar o modo de distribuição para o Balanceador de carga do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição com base em hash

O modo de distribuição predefinido para o Balanceador de carga do Azure é um hash de 5 cadeias de identificação. A cadeia de identificação é composta pelo IP de origem, porta de origem, IP de destino, porta de destino e tipo de protocolo. O hash é utilizado para mapear o tráfego para os servidores disponíveis e o algoritmo fornece retenções apenas dentro de uma sessão de transporte. Pacotes que estão na mesma sessão são direcionados para a mesma instância de IP (DIP) do Centro de dados por trás o ponto final com balanceamento de carga. Quando o cliente inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterado e faz com que o tráfego Ir para um endpoint diferente do DIP.

![modo de distribuição com base em hash de 5 cadeias de identificação](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade do IP de origem

Balanceador de carga também pode ser configurado utilizando o modo de distribuição de afinidade IP de origem. Este modo de distribuição também é conhecido como afinidade de sessão ou afinidade do IP de cliente. O modo utiliza uma 2 cadeias de identificação (IP de origem e destino IP) ou 3 cadeias de identificação (IP de origem, IP de destino e protocolo tipo) de hash para mapear o tráfego para os servidores disponíveis. Através da utilização de afinidade do IP de origem, ligações que são iniciadas no mesmo computador cliente, vá para o mesmo ponto final do DIP.

A figura seguinte ilustra uma configuração de 2 cadeias de identificação. Repare como de 2 cadeias de identificação é executada através do Balanceador de carga para a máquina virtual 1 (VM1). VM1, em seguida, é feita por VM2 e VM3.

![modo de distribuição de afinidade de sessão de 2 cadeias de identificação](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Modo de afinidade do IP de origem resolve uma incompatibilidade entre o Balanceador de carga do Azure e o Gateway de ambiente de trabalho remoto (Gateway de RD). Ao utilizar este modo, pode criar um farm de Gateway de RD num serviço em nuvem único.

Outro cenário de utilização é carregamento de suporte de dados. O carregamento de dados ocorre através do UDP, mas o plane de controlo é conseguido através de TCP:

* Um cliente inicia uma sessão TCP para o endereço público com balanceamento de carga e é direcionado para um DIP específico. O canal for deixado Active Directory para monitorizar o estado de funcionamento de ligação.
* Uma nova sessão UDP no mesmo computador cliente é iniciada para o mesmo com balanceamento de carga ponto final público. A ligação é direcionada para o mesmo ponto final do DIP como a ligação de TCP anterior. O carregamento de suporte de dados pode ser executado no débito elevado, mantendo em simultâneo um canal de controlo através de TCP.

> [!NOTE]
> Quando um conjunto com balanceamento de carga alterações feitas por remover ou adicionar uma máquina virtual, a distribuição de pedidos de cliente é recomputed. Não é possível dependem novas ligações de clientes existentes para terminar a cópia de segurança no mesmo servidor. Além disso, IP de origem a utilizar o modo de distribuição de afinidade pode causar uma distribuição unequal de tráfego. Clientes que executam o atrás proxies poderão ser vistos como uma aplicação de cliente exclusivos.

## <a name="configure-source-ip-affinity-settings"></a>Configurar definições de afinidade do IP de origem

Para máquinas virtuais, utilize o Azure PowerShell para alterar as definições de tempo limite. Adicionar um ponto final do Azure para uma máquina virtual e configurar o modo de distribuição do Balanceador de carga:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Defina o valor da `LoadBalancerDistribution` elemento para a quantidade de balanceamento de carga pretendida. Especifique sourceIP para balanceamento de carga de 2 cadeias de identificação (IP de origem e destino IP). Especifique sourceIPProtocol para 3 cadeias de identificação (IP de origem, IP de destino e protocolo tipo) balanceamento de carga. Especifique nenhum para o comportamento predefinido de balanceamento de carga de 5 cadeias de identificação.

Obter uma configuração de modo de distribuição do Balanceador de carga do ponto final utilizando estas definições:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Quando o `LoadBalancerDistribution` elemento não está presente, o Balanceador de carga do Azure utiliza o algoritmo de 5 cadeias de identificação predefinida.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurar o modo de distribuição no conjunto de ponto final com balanceamento de carga

Quando os pontos finais fazem parte de um conjunto de ponto final com balanceamento de carga, o modo de distribuição tem de ser configurado no conjunto de ponto final com balanceamento de carga:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configurar o modo de distribuição para pontos finais de serviços Cloud

Utilize o Azure SDK para .NET 2.5 para atualizar o seu serviço em nuvem. As definições de endpoint para serviços em nuvem que são efetuadas no ficheiro. csdef. Para atualizar o modo de distribuição do Balanceador de carga para uma implementação de serviços em nuvem, é necessária uma atualização da implementação.

Eis um exemplo de. csdef alterações para definições de ponto final:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Exemplo de API

O exemplo seguinte mostra como reconfigurar o modo de distribuição do Balanceador de carga para um conjunto com balanceamento de carga especificado numa implementação. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Alterar o modo de distribuição para o conjunto com balanceamento de carga implementado

Utilize o modelo de implementação clássico do Azure para alterar uma configuração de implementação existente. Adicionar o `x-ms-version` cabeçalho e defina o valor para a versão 2014-09-01 ou posterior.

#### <a name="request"></a>Pedir

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Como anteriormente descrito, defina o `LoadBalancerDistribution` elemento sourceIP afinidade de 2 cadeias de identificação, sourceIPProtocol para a afinidade de 3 cadeias de identificação ou none para sem afinidade de (5 cadeias de identificação afinidade).

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Balanceador de carga interno do Azure](load-balancer-internal-overview.md)
* [Introdução ao configurar um balanceador de carga para a internet](load-balancer-get-started-internet-arm-ps.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
