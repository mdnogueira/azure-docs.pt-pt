---
title: "Introdução à conectividade, verifique no observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral da capacidade de conectividade do observador de rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 16ceef9c923b6a933a5caf752991b466346e0ebc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-connectivity-check-in-azure-network-watcher"></a>Introdução à verificação da conectividade no observador de rede do Azure

A funcionalidade de conectividade do observador de rede fornece a capacidade para verificar uma ligação de TCP direta de uma máquina virtual a uma máquina virtual (VM), o nome de domínio completamente qualificado (FQDN), URI, ou o endereço IPv4. Cenários de rede são complexos, estão implementados utilizando grupos de segurança de rede, firewalls, rotas definidas pelo utilizador e de recursos fornecidos pelo Azure. Configurações complexas efetua a resolução de problemas de conectividade um desafio. Observador de rede ajuda a reduzir a quantidade de tempo para localizar e detetar problemas de conectividade. Os resultados devolvidos podem fornecer informações sobre se o problema de conectividade é devido a uma plataforma ou um problema de configuração do utilizador. Conectividade pode ser verificada com [PowerShell](network-watcher-connectivity-powershell.md), [CLI do Azure](network-watcher-connectivity-cli.md), e [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Verificação da conectividade requer uma extensão da máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão numa Windows VM visite [extensão da máquina virtual de agente de observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para, visite VM com Linux [extensão da máquina virtual de agente de observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="response"></a>Resposta

A tabela seguinte mostra as propriedades devolvidas quando uma verificação de conectividade concluiu a execução.

|Propriedade  |Descrição  |
|---------|---------|
|ConnectionStatus     | O estado da verificação de conectividade. São possíveis resultados **Reachable** e **Unreachable**.        |
|AvgLatencyInMs     | Latência média de durante a verificação de conectividade em milissegundos. (Apenas apresentar se verifique o estado é acessível)        |
|MinLatencyInMs     | Latência mínima durante a verificação de conectividade em milissegundos. (Apenas apresentar se verifique o estado é acessível)        |
|MaxLatencyInMs     | Latência máxima durante a verificação de conectividade em milissegundos. (Apenas apresentar se verifique o estado é acessível)        |
|ProbesSent     | Número de sondas enviada durante a verificação. O valor máximo é 100.        |
|ProbesFailed     | Número de sondas que falharam durante a verificação. O valor máximo é 100.        |
|Saltos     | Salto pelo caminho de salto de origem para destino.        |
|Saltos []. Tipo     | Tipo de recurso. Os valores possíveis são **origem**, **VirtualAppliance**, **VnetLocal**, e **Internet**.        |
|Saltos []. ID | Identificador exclusivo do salto.|
|Saltos []. Endereço | Endereço IP do salto.|
|Saltos []. ResourceId | ResourceID do salto se o salto é um recurso do Azure. Se se tratar de um recurso de internet, ResourceID é **Internet**. |
|Saltos []. NextHopIds | O identificador exclusivo do próximo salto executado.|
|Saltos []. Problemas | Uma coleção de problemas encontrados durante a verificação em que hop. Se ocorreram sem problemas, o valor está em branco.|
|Saltos []. Problemas []. Origem | Em salto atual, em que ocorreu o problema. Os valores possíveis são:<br/> **Entrada** -problema está na ligação do salto anterior para o salto atual<br/>**Saída** -problema está na ligação do salto atual para o salto seguinte<br/>**Local** -problema está no salto atual.|
|Saltos []. Problemas []. Gravidade | A gravidade do problema detetado. Os valores possíveis são **erro** e **aviso**. |
|Saltos []. Problemas []. Tipo |O tipo de problema encontrado. Os valores possíveis são: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Saltos []. Problemas []. Contexto |Detalhes sobre o problema encontrado.|
|Saltos []. Problemas []. Contexto [] .key |Chave do par chave-valor devolvido.|
|Saltos []. Problemas []. Contexto [] .value |Valor do par chave-valor devolvido.|

Segue-se um exemplo de um problema encontrado um salto.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Tipos de falhas

A verificação de conectividade devolve tipos de falhas sobre a ligação. A tabela seguinte fornece uma lista dos tipos de falhas atual devolvido.

|Tipo  |Descrição  |
|---------|---------|
|CPU     | Elevada utilização da CPU.       |
|Memória     | Utilização elevada da memória.       |
|GuestFirewall     | O tráfego está bloqueado devido a uma configuração de firewall de máquina virtual.        |
|DNSResolution     | Falha na resolução DNS para o endereço de destino.        |
|NetworkSecurityRule    | O tráfego está bloqueado por uma regra de NSG (regra será devolvida)        |
|UserDefinedRoute|O tráfego é removido devido a um definidas pelo utilizador ou a rota de sistema. |

### <a name="next-steps"></a>Passos seguintes

Saiba como verificar a conectividade a um recurso, visitando: [Verifique a conectividade com o observador de rede de Azure](network-watcher-connectivity-powershell.md).

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png

