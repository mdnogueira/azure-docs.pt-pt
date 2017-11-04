---
title: "Diagnostique conectividade no local através do gateway VPN com o observador de rede do Azure | Microsoft Docs"
description: "Este artigo descreve como diagnosticar conectividade no local através do gateway VPN com a resolução de problemas de recursos do observador de rede do Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 94658dfcf93e821e24cabb1f010f8dce0c014700
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnosticar conectividade no local através de gateways de VPN

Gateway de VPN do Azure permite-lhe criar solução híbrida que endereços a necessidade de uma ligação segura entre a sua rede no local e a sua rede virtual do Azure. Como os requisitos são exclusivos, por isso é a escolha do dispositivo VPN no local. Azure suporta atualmente [vários dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) que constantemente são validadas em parceria com os fornecedores de dispositivos. Reveja as definições de configuração específicos do dispositivo antes de configurar o dispositivo VPN no local. Da mesma forma, o Gateway de VPN do Azure está configurado com um conjunto de [IPsec parâmetros suportados](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) que são utilizadas para estabelecer ligações. Atualmente não é possível que pode especificar ou selecionar uma combinação específica de parâmetros de IPsec do Gateway de VPN do Azure. Para estabelecer uma ligação com êxito entre no local e o Azure, as definições do dispositivo VPN no local tem de estar em conformidade com os parâmetros IPsec prescritos pelo Gateway de VPN do Azure. Se as definições estão no mesmo correto, uma perda de conectividade e até agora estes problemas de resolução de problemas não trivial e normalmente demorou horas para identificar e corrigir o problema.

Com o observador de rede de Azure resolver problemas de funcionalidade, poderá diagnosticar problemas com o seu Gateway e ligações e dentro de minutos tem informações suficientes para tomar uma decisão informada para resolver o problema.

## <a name="scenario"></a>Cenário

Se quiser configurar uma ligação site a site entre o Azure e no local utilizando FortiGate como o Gateway de VPN no local. Para alcançar este cenário, precisaria de que a configuração seguinte:

1. Gateway de rede virtual - o Gateway VPN no Azure
1. Gateway de rede local - o [no local (FortiGate) VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) representação na nuvem do Azure
1. Ligação site a site (rota baseada) - [ligação entre o Gateway de VPN e o router no local](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Configurar FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Pode encontrar orientação passo a passo detalhada para configurar uma configuração Site a Site, visitando: [criar uma VNet com uma ligação Site a Site utilizando o portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Um dos passos de configuração críticas está a configurar os parâmetros de comunicação do IPsec, qualquer configuração incorreta que leva a perda de conectividade entre a rede no local e o Azure. Gateways de VPN do Azure estão configurados para suportar os seguintes parâmetros IPsec para a fase 1. Tenha em atenção, tal como mencionado anteriormente, que estas definições não podem ser modificadas.  Como pode ver na tabela abaixo, algoritmos de encriptação suportados pelo Gateway de VPN do Azure são AES256, 3DES e AES-128.

### <a name="ike-phase-1-setup"></a>Configuração de fase 1 do IKE

| **Propriedade** | **PolicyBased** | **Gateway RouteBased e Standard ou VPN de elevado desempenho** |
| --- | --- | --- |
| Versão do IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de Autenticação |Chave Pré-partilhada |Chave Pré-partilhada |
| Algoritmos de Encriptação |AES256 AES128 3DES |AES256 3DES |
| Algoritmo Hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 1 |28 800 segundos |10 800 segundos |

Como um utilizador, seriam necessários para configurar o seu FortiGate, uma configuração de exemplo pode ser encontrada no [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Inadvertidamente se configurou a sua FortiGate a utilizar SHA-512 como o algoritmo de hashing. Como este algoritmo não é um algoritmo suportado para ligações baseadas em políticas, funciona a ligação VPN.

Estes problemas são difíceis de resolver problemas e causas raiz são, muitas vezes, não intuitivos. Neste caso, pode abrir um pedido de suporte para obter ajuda sobre como resolver o problema. Mas com o observador de rede de Azure API de resolução de problemas, pode identificar estes problemas no seu próprio.

## <a name="troubleshooting-using-azure-network-watcher"></a>Resolução de problemas com o observador de rede do Azure

Para diagnosticar a ligação, ligue para o Azure PowerShell e iniciar o `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet. Pode encontrar os detalhes sobre como utilizar este cmdlet em [resolver problemas de Gateway de rede Virtual e ligações - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Este cmdlet pode demorar alguns minutos a concluir.

Depois de concluir o cmdlet, pode navegar para a localização de armazenamento especificada no cmdlet para obter informações detalhadas sobre sobre o problema e os registos. Observador de rede do Azure cria uma pasta de zip que contém os ficheiros de registo seguinte:

![1][1]

Abra o ficheiro chamado IKEErrors.txt e apresenta o seguinte erro que indica um problema no local com a configuração incorreta de definição do IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Pode obter informações detalhadas a partir de wfpdiag.txt Scrubbed sobre o erro, como neste caso menciona que foi `ERROR_IPSEC_IKE_POLICY_MATCH` que levar a ligação não está a funcionar corretamente.

Configuração incorreta comum outra é as especificação chaves partilhadas incorretas. Se no exemplo anterior tivesse especificado diferentes chaves partilhadas, o IKEErrors.txt mostra o seguinte erro: `Error: Authentication failed. Check shared key`.

Resolver problemas de observador de rede do Azure funcionalidade permite-lhe diagnosticar e resolver o seu Gateway de VPN e a ligação com o facilitar a um cmdlet do PowerShell simple. Atualmente, suportam a diagnosticar as seguintes condições e estiver a trabalhar para adicionar mais condição.

### <a name="gateway"></a>Gateway

| Tipo de índice de falhas | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar o Gateway ou Gateway não está aprovisionada. |Não|
| PlannedMaintenance |  Uma instância de gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização de utilizador está em curso. Isto pode ser uma operação de redimensionamento. | Não |
| VipUnResponsive | Não é possível contactar a instância principal do Gateway. Isto acontece quando ocorre uma falha da pesquisa de estado de funcionamento. | Não |
| PlatformInActive | Não há um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está em execução. | Não|
| NoConnectionsFoundForGateway | Não existem ligações existe no gateway. Esta é apenas um aviso.| Não|
| ConnectionsNotConnected | Nenhuma das ligações estão ligadas. Esta é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | A utilização do Gateway atual da utilização da CPU é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de índice de falhas | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar o Gateway ou Gateway não está aprovisionada. |Não|
| PlannedMaintenance | Uma instância de gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização de utilizador está em curso. Isto pode ser uma operação de redimensionamento.  | Não |
| VipUnResponsive | Não é possível contactar a instância principal do Gateway. Ocorre quando ocorre uma falha da pesquisa de estado de funcionamento. | Não |
| ConnectionEntityNotFound | Configuração da ligação está em falta. | Não |
| ConnectionIsMarkedDisconnected | A ligação está marcada como "desligada". |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem a ligação configurada. | Sim |
| ConnectionMarkedStandy | O serviço subjacente está marcado como modo de espera.| Sim|
| Autenticação | Erro de correspondência de chave pré-partilhada. | Sim|
| PeerReachability | O gateway de elemento de rede não está acessível. | Sim|
| IkePolicyMismatch | O gateway de ponto a ponto tem políticas IKE que não são suportadas pelo Azure. | Sim|
| Erro de WfpParse | Ocorreu um erro ao analisar o registo WFP. |Sim|

## <a name="next-steps"></a>Passos seguintes

Saiba verificar a conectividade de Gateway de VPN com o PowerShell e automatização do Azure, visitando [gateways de VPN do Monitor com a resolução de problemas do observador de rede do Azure](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
