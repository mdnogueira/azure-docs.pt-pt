---
title: "Introdução ao Azure observador de rede de resolução de problemas de recursos | Microsoft Docs"
description: "Esta página fornece uma descrição geral das capacidades de resolução de problemas de recursos de observador de rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: a37c92e1aa58184ed29185742ec727c120fe593f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introdução ao recurso observador de rede do Azure de resolução de problemas

Gateways da virtual Network fornecer conectividade entre recursos no local e a outras redes virtuais no Azure. Estes gateways e as suas ligações de monitorização é essencial para garantir que a comunicação não é interrompido. Observador de rede fornece a capacidade de resolução de problemas de Gateways da Virtual Network e ligações. Isto pode ser chamado através do portal, o PowerShell, a CLI ou a REST API. Quando chamado, o observador de rede diagnoses o estado de funcionamento do gateway de rede virtual ou da ligação e devolvem os resultados adequados. Este pedido é uma transação de execução longa, os resultados são devolvidos quando o diagnóstico estiver concluído.

![portal][2]

## <a name="results"></a>Resultados

Os resultados preliminares devolvidos dar uma visão geral do Estado do Estado de funcionamento do recurso. Informações mais aprofundadas podem ser fornecidas para os recursos conforme mostrado na secção seguinte:

A lista seguinte se os valores devolvidos com a API de resolução de problemas:

* **startTime** -este valor é a hora de início de chamada de API de resolução de problemas.
* **endTime** -este valor é o tempo quando terminou a resolução de problemas.
* **código** -este valor é mau estado de funcionamento, se ocorrer uma falha de diagnóstico único.
* **resultados** -resultados é uma coleção de resultados devolvidos da ligação ou o gateway de rede virtual.
    * **ID** -este valor é o tipo de falha.
    * **resumo** -este valor é um resumo da falha.
    * **detalhadas** -este valor fornece uma descrição detalhada da falha.
    * **recommendedActions** -esta propriedade é uma coleção de ações recomendadas.
      * **actionText** -este valor contém o texto que descreve a ação a tomar.
      * **actionUri** -este valor fornece o URI a documentação sobre a funcionar.
      * **actionUriText** -este valor é uma breve descrição do texto de ação.

As tabelas seguintes mostram os tipos de falhas diferente (id em resultados na lista anterior) que estão disponíveis e se a falha cria registos.

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
| ConnectionsNotConnected | As ligações não estão ligadas. Esta é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | A utilização de CPU do Gateway atual é > 95%. | Sim |

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

## <a name="supported-gateway-types"></a>Tipos de Gateway suportados

A lista seguinte mostra o suporte mostra os gateways e ligações são suportadas na resolução de problemas do observador de rede.
|  |  |
|---------|---------|
|**Tipos de gateway**   |         |
|VPN      | Suportado        |
|ExpressRoute | Não suportado |
|Hypernet | Não suportado|
|**Tipos de VPN** | |
|Rota baseada | Suportado|
|Baseado na política | Não suportado|
|**Tipos de ligação**||
|IPSec| Suportado|
|VNet2Vnet| Suportado|
|ExpressRoute| Não suportado|
|Hypernet| Não suportado|
|VPNClient| Não suportado|

## <a name="log-files"></a>Ficheiros de registo

Os ficheiros de registo de resolução de problemas de recursos são armazenados numa conta do storage depois de concluída a resolução de problemas de recursos. A imagem seguinte mostra os conteúdos de exemplo de uma chamada para o que resultou num erro.

![ficheiro zip][1]

> [!NOTE]
> Em alguns casos, apenas um subconjunto dos ficheiros de registo é escrito para o armazenamento.

Para obter instruções sobre como transferir ficheiros entre contas de armazenamento do azure, consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser utilizada é Explorador de armazenamento. Obter mais informações sobre o Explorador de armazenamento podem ser encontradas aqui na seguinte hiperligação: [Explorador de armazenamento](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

O **ConnectionStats.txt** ficheiro contém estatísticas gerais da ligação, incluindo os bytes de entrada e saída, o estado de ligação e a hora a ligação foi estabelecida.

> [!NOTE]
> Se a chamada para a API de resolução de problemas devolve bom estado de funcionamento, a única coisa que devolveu no ficheiro zip é um **ConnectionStats.txt** ficheiro.

O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

O **CPUStats.txt** ficheiro contém a utilização da CPU e memória disponível no momento de teste.  O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

O **IKEErrors.txt** ficheiro contém quaisquer erros de IKE que foram encontrados durante a monitorização.

O exemplo seguinte mostra os conteúdos de um ficheiro de IKEErrors.txt. Os erros podem ser diferentes consoante o problema.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Wfpdiag.txt limpa

O **Scrubbed wfpdiag.txt** ficheiro de registo contém o registo de wfp. Este registo contém o registo de falhas de IKE/Authenticated e remoção de pacotes.

O exemplo seguinte mostra os conteúdos do ficheiro Scrubbed wfpdiag.txt. Neste exemplo, a chave partilhada de uma ligação não estava correta, podem ser vistos a partir da linha de 3rd na parte inferior. O exemplo seguinte é apenas um fragmento de todo o registo, como o registo pode ser demorado, consoante o problema.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.SUM

O **wfpdiag.txt.sum** ficheiro é um registo que mostra as memórias intermédias e eventos processados.

O exemplo a seguir está o conteúdo de um ficheiro de wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Passos seguintes

Saiba como diagnosticar ligações e Gateways de VPN através do portal, visitando [resolução de problemas de Gateway - portal do Azure](network-watcher-troubleshoot-manage-portal.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
