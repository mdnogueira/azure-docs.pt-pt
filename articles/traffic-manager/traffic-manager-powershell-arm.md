---
title: "Utilizar o PowerShell para gerir o Gestor de tráfego no Azure | Microsoft Docs"
description: "Com o PowerShell para o Gestor de tráfego com o Azure Resource Manager"
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 1cd7bd7e32c96398d72c7cd3b51e2b456d60f01d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Utilizar o PowerShell para gerir o Gestor de tráfego

O Azure Resource Manager é a interface de gestão preferenciais para serviços no Azure. Perfis do Traffic Manager do Azure podem ser geridos através de APIs do Azure Resource Manager e as ferramentas.

## <a name="resource-model"></a>Modelo de recursos

Traffic Manager do Azure está configurado com uma coleção de definições denominado um perfil do Traffic Manager. Este perfil contém as definições DNS, as definições de encaminhamento de tráfego, as definições de monitorização do ponto final e uma lista de pontos finais de serviço ao qual o tráfego é encaminhado.

Cada perfil de Gestor de tráfego é representada por um recurso do tipo 'TrafficManagerProfiles'. Ao nível da REST API, o URI para cada perfil é:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Configurar o Azure PowerShell

Estas instruções utilizam o Microsoft Azure PowerShell. O seguinte artigo explica como instalar e configurar o Azure PowerShell.

* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

Os exemplos deste artigo partem do princípio de que tem um grupo de recursos existente. Pode criar um grupo de recursos com o seguinte comando:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> O Azure Resource Manager requer que todos os grupos de recursos tem uma localização. Esta localização é utilizada como predefinição para os recursos criados nesse grupo de recursos. No entanto, uma vez que os recursos de perfil do Gestor de tráfego são globais, não regionais, a escolha de localização do grupo de recursos não tem impacto no Gestor de tráfego do Azure.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil de Gestor de tráfego

Para criar um perfil do Traffic Manager, utilize o `New-AzureRmTrafficManagerProfile` cmdlet:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A tabela seguinte descreve os parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| Nome |O nome do recurso para o recurso de perfil do Traffic Manager. Perfis no mesmo grupo de recursos tem de ter nomes exclusivos. Este nome está separado do nome DNS utilizado para consultas DNS. |
| resourceGroupName |O nome do grupo de recursos que contém o recurso de perfil. |
| TrafficRoutingMethod |Especifica o método de encaminhamento do tráfego utilizado para determinar o ponto final é devolvido em resposta uma consulta DNS. Os valores possíveis são 'Desempenho', 'Weighted' ou 'Priority'. |
| RelativeDnsName |Especifica a parte do nome de anfitrião de DNS fornecido por este perfil do Traffic Manager. Este valor é combinado com o nome de domínio DNS utilizado pelo Gestor de tráfego do Azure para formar o nome de domínio completamente qualificado (FQDN) do perfil. Por exemplo, definir o valor de "contoso" fica 'contoso.trafficmanager.net'. |
| VALOR DE TTL |Especifica o DNS Time-to-Live (TTL), em segundos. Este valor de TTL informa os clientes DNS quanto tempo as respostas DNS de cache para este perfil do Gestor de tráfego e as resoluções Local DNS. |
| MonitorProtocol |Especifica o protocolo a utilizar para monitorizar o estado de funcionamento do ponto final. Os valores possíveis são 'HTTP' e 'HTTPS'. |
| MonitorPort |Especifica a porta TCP utilizada para monitorizar o estado de funcionamento do ponto final. |
| MonitorPath |Especifica o caminho relativo para o nome de domínio de ponto final utilizado para a sonda de estado de funcionamento do ponto final. |

O cmdlet cria um perfil do Traffic Manager no Azure e devolve um objeto de perfil correspondente para o PowerShell. Neste momento, o perfil não contém quaisquer pontos finais. Para obter mais informações sobre como adicionar pontos finais para um perfil do Traffic Manager, consulte [adicionar pontos finais de Gestor de tráfego](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Obter um perfil do Traffic Manager

Para obter um objeto de perfil do Traffic Manager existente, utilize o `Get-AzureRmTrafficManagerProfle` cmdlet:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Este cmdlet devolve um objeto de perfil do Traffic Manager.

## <a name="update-a-traffic-manager-profile"></a>Atualizar um perfil do Traffic Manager

Modificar perfis do Traffic Manager segue um processo do passo 3:

1. Obter o perfil utilizando `Get-AzureRmTrafficManagerProfile` ou utilizar o perfil devolvido pelo `New-AzureRmTrafficManagerProfile`.
2. Modificar o perfil. Pode adicionar e remover pontos finais ou altere os parâmetros do ponto final ou o perfil. Estas alterações são operações offline. Só está a alterar o objeto na memória que representa o perfil local.
3. Consolidar as alterações utilizando o `Set-AzureRmTrafficManagerProfile` cmdlet.

Todas as propriedades de perfil podem ser alteradas, exceto RelativeDnsName o perfil. Para alterar o RelativeDnsName, tem de eliminar o perfil e um novo perfil com um novo nome.

O exemplo seguinte demonstra como pode alterar TTL o perfil:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Existem três tipos de pontos finais do Gestor de tráfego:

1. **Pontos finais Azure** são serviços alojados no Azure
2. **Pontos finais externos** são serviços alojados fora do Azure
3. **Pontos finais de aninhada** são utilizados para construir aninhadas hierarquias de perfis do Traffic Manager. Pontos finais aninhados ativar o encaminhamento de tráfego configurações avançadas para aplicações complexas.

Em três casos, é possível adicionar pontos finais de duas formas:

1. Através de um processo de passo 3 descrito anteriormente. A vantagem deste método é que várias alterações de ponto final podem ser efetuadas numa única atualização.
2. Utilizando o cmdlet New-AzureRmTrafficManagerEndpoint. Este cmdlet adiciona um ponto final para um perfil do Traffic Manager existente numa única operação.

## <a name="adding-azure-endpoints"></a>Adicionar pontos finais Azure

Pontos finais Azure referenciam serviços alojados no Azure. São suportados dois tipos de pontos finais do Azure:

1. Aplicações Web do Azure
2. Recursos PublicIpAddress do Azure (que podem ser anexados a um balanceador de carga ou uma máquina virtual NIC). O PublicIpAddress tem de ter um nome DNS atribuído a ser utilizado no Traffic Manager.

Em cada caso:

* O serviço for especificado utilizando o parâmetro 'targetResourceId' `Add-AzureRmTrafficManagerEndpointConfig` ou `New-AzureRmTrafficManagerEndpoint`.
* O 'Target' e 'EndpointLocation' são implícito pelo TargetResourceId.
* Especificação de 'Peso' é opcional. Ponderações só são utilizadas se o perfil estiver configurado para utilizar o método de encaminhamento de tráfego de 'Weighted'. Caso contrário, são ignorados. Se for especificado, o valor tem de ser um número entre 1 e 1000. O valor predefinido é '1'.
* Especificação de 'Priority' é opcional. Prioridades só são utilizadas se o perfil estiver configurado para utilizar o método de encaminhamento de tráfego de 'Priority'. Caso contrário, são ignorados. Os valores válidos são entre 1 a 1000 com valores mais baixos que indica uma prioridade mais alta. Se for especificado para um ponto final, tem de ser especificados para todos os pontos finais. Se for omitido, os valores predefinidos a partir de '1' são aplicados pela ordem que estão listados os pontos finais.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exemplo 1: Adicionar pontos finais de aplicação Web utilizando`Add-AzureRmTrafficManagerEndpointConfig`

Neste exemplo, vamos criar um perfil de Gestor de tráfego e adicionar dois pontos finais de aplicação Web utilizando o `Add-AzureRmTrafficManagerEndpointConfig` cmdlet.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar um publicIpAddress utilizando o ponto final`New-AzureRmTrafficManagerEndpoint`

Neste exemplo, um recurso de endereço IP público é adicionado ao perfil do Traffic Manager. O endereço IP público tem de ter um nome DNS configurado e pode ser vinculado a NIC de uma VM ou a um balanceador de carga.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Adicionar pontos finais externos

Gestor de tráfego utiliza pontos finais externos para direcionar o tráfego para serviços alojados fora do Azure. Como com pontos finais do Azure, pontos finais externos podem ser adicionados a utilizar `Add-AzureRmTrafficManagerEndpointConfig` seguido `Set-AzureRmTrafficManagerProfile`, ou `New-AzureRMTrafficManagerEndpoint`.

Quando especificar pontos finais externos:

* O nome de domínio de ponto final tem de ser especificado utilizando o parâmetro de 'Target'
* Se for utilizado o método de encaminhamento de tráfego de 'Desempenho', 'EndpointLocation' não é necessária. Caso contrário, é opcional. O valor tem de ser um [nome da região do Azure válido](https://azure.microsoft.com/regions/).
* O 'Peso' e 'Priority' são opcionais.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Adicionar pontos finais externos utilizando `Add-AzureRmTrafficManagerEndpointConfig` e`Set-AzureRmTrafficManagerProfile`

Neste exemplo, vamos criar um perfil de Gestor de tráfego, adicionar dois pontos finais externos e confirmar as alterações.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar pontos finais externos utilizando`New-AzureRmTrafficManagerEndpoint`

Neste exemplo, vamos adicionar um ponto final externo a um perfil existente. O perfil é especificado, utilizando os nomes de grupo de recursos e perfil.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Adicionar pontos finais de 'Nested'

Cada perfil do Traffic Manager Especifica um único método de encaminhamento de tráfego. No entanto, existem cenários que necessitam que o encaminhamento fornecido por um único perfil do Traffic Manager o encaminhamento de tráfego mais sofisticadas. Podem aninhar perfis do Traffic Manager combinar as vantagens de mais do que um método de encaminhamento de tráfego. Perfis aninhados permitem-lhe substituir o comportamento predefinido do Gestor de tráfego para suporte maior e mais complexas implementações de aplicações. Para obter mais exemplos, consulte [perfis do Gestor de tráfego aninhada](traffic-manager-nested-profiles.md).

Pontos finais aninhados estão configurados no perfil de principal, com um tipo de ponto final específico, 'NestedEndpoints'. Quando especificar pontos finais aninhados:

* O ponto final tem de ser especificado utilizando o parâmetro 'targetResourceId'
* Se for utilizado o método de encaminhamento de tráfego de 'Desempenho', 'EndpointLocation' não é necessária. Caso contrário, é opcional. O valor tem de ser um [nome da região do Azure válido](http://azure.microsoft.com/regions/).
* O 'Peso' e 'Priority' são opcionais, como pontos finais do Azure.
* O parâmetro 'MinChildEndpoints' é opcional. O valor predefinido é '1'. Se o número de pontos finais disponíveis está abaixo deste limiar, o perfil de principal considera o perfil subordinado 'degradado' e diverts tráfego para os pontos finais no perfil do principal.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Adicionar pontos finais aninhados utilizando `Add-AzureRmTrafficManagerEndpointConfig` e`Set-AzureRmTrafficManagerProfile`

Neste exemplo, vamos criar novos principais e subordinados de Gestor de tráfego perfis, adicionar o subordinado como um ponto de final aninhado para o elemento principal e confirmar as alterações.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

De uma forma abreviada neste exemplo, vamos adicione quaisquer pontos finais para os perfis subordinado ou principal.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar pontos finais aninhados utilizando`New-AzureRmTrafficManagerEndpoint`

Neste exemplo, iremos adicionar um perfil subordinado existente como um ponto de final aninhado para um perfil de principal existente. O perfil é especificado, utilizando os nomes de grupo de recursos e perfil.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="update-a-traffic-manager-endpoint"></a>Atualizar um ponto final do Gestor de tráfego

Existem duas formas de atualizar um ponto final do Traffic Manager existente:

1. Obter o perfil do Gestor de tráfego utilizando `Get-AzureRmTrafficManagerProfile`, atualizar as propriedades do ponto final no perfil e confirmar as alterações utilizando `Set-AzureRmTrafficManagerProfile`. Este método tem a vantagem de conseguir atualizar mais do que um ponto final numa única operação.
2. Obter o ponto final do Gestor de tráfego utilizando `Get-AzureRmTrafficManagerEndpoint`, atualizar as propriedades do ponto final e confirmar as alterações utilizando `Set-AzureRmTrafficManagerEndpoint`. Este método é mais simples, uma vez que não requer a indexação para a matriz de pontos finais no perfil.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Atualizar pontos finais utilizando `Get-AzureRmTrafficManagerProfile` e`Set-AzureRmTrafficManagerProfile`

Neste exemplo, vamos modificar a prioridade em dois pontos finais dentro de um perfil existente.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exemplo 2: Atualizar um ponto final utilizando `Get-AzureRmTrafficManagerEndpoint` e`Set-AzureRmTrafficManagerEndpoint`

Neste exemplo, vamos modificar o peso de um único ponto final num perfil existente.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Ativar e desativar pontos finais e perfis

Gestor de tráfego permite que os pontos finais individuais ser ativados e desativados, bem como permitir que a ativação e a desativação dos perfis de todos.
Estas alterações podem ser efetuadas por obter/atualizar/definição o ponto final ou o perfil de recursos. Para simplificar estas operações comuns, também são suportados através de cmdlets dedicados.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemplo 1: Ativar e desativar um perfil do Traffic Manager

Para ativar um perfil do Traffic Manager, utilize `Enable-AzureRmTrafficManagerProfile`. O perfil pode ser especificado utilizando um objeto de perfil. O objeto de perfil pode ser transmitido através do pipeline ou utilizando o '-TrafficManagerProfile' parâmetro. Neste exemplo, vamos especificar o perfil pelo nome do grupo de recursos e perfil.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Para desativar um perfil de Gestor de tráfego:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

O cmdlet Disable-AzureRmTrafficManagerProfile solicita a confirmação. Esta linha de comandos pode ser suprimida utilizando o '-Force' parâmetro.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemplo 2: Ativar e desativar um ponto final do Gestor de tráfego

Para ativar um ponto final do Gestor de tráfego, utilize `Enable-AzureRmTrafficManagerEndpoint`. Existem duas formas para especificar o ponto final

1. Utilizando um objeto de TrafficManagerEndpoint transmitido através do pipeline ou utilizando o '-TrafficManagerEndpoint' parâmetro
2. Utilizar o nome do ponto final, o tipo de ponto final, o nome do perfil e o nome do grupo de recursos:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Da mesma forma, para desativar um ponto final do Gestor de tráfego:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Tal como com `Disable-AzureRmTrafficManagerProfile`, a `Disable-AzureRmTrafficManagerEndpoint` cmdlet solicita a confirmação. Esta linha de comandos pode ser suprimida utilizando o '-Force' parâmetro.

## <a name="delete-a-traffic-manager-endpoint"></a>Eliminar um ponto final do Gestor de tráfego

Para remover pontos finais individuais, utilize o `Remove-AzureRmTrafficManagerEndpoint` cmdlet:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Este cmdlet solicita a confirmação. Esta linha de comandos pode ser suprimida utilizando o '-Force' parâmetro.

## <a name="delete-a-traffic-manager-profile"></a>Eliminar um perfil do Gestor de tráfego

Para eliminar um perfil do Traffic Manager, utilize o `Remove-AzureRmTrafficManagerProfile` cmdlet, especificando os nomes de grupo de recursos e de perfil:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Este cmdlet solicita a confirmação. Esta linha de comandos pode ser suprimida utilizando o '-Force' parâmetro.

O perfil a ser eliminados também pode ser especificado utilizando um objeto de perfil:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Também pode ser ligada esta sequência:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Passos seguintes

[Gestor de tráfego de monitorização](traffic-manager-monitoring.md)

[Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
