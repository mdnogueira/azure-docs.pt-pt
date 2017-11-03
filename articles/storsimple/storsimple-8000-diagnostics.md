---
title: "Ferramenta de diagnóstico para resolver o dispositivo de 8000 do StorSimple | Microsoft Docs"
description: Descreve os modos de dispositivo StorSimple e explica como utilizar o Windows PowerShell para StorSimple para alterar o modo de dispositivo.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 8fae7bb357f8e5e8eff249edfe3a2aaafe04283c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Utilizar a ferramenta de diagnóstico do StorSimple para resolver problemas com dispositivos série 8000

## <a name="overview"></a>Descrição geral

A ferramenta de diagnóstico do StorSimple diagnoses problemas relacionados com o sistema, desempenho, rede e hardware de funcionamento do componente para um dispositivo StorSimple. A ferramenta de diagnóstico pode ser utilizada em vários cenários. Estes cenários incluem a carga de trabalho de planeamento, implementação de um dispositivo StorSimple, avaliar o ambiente de rede e determinar o desempenho de um dispositivo operacional. Este artigo fornece uma descrição geral da ferramenta de diagnóstico e descreve a forma como a ferramenta pode ser utilizada com um dispositivo StorSimple.

A ferramenta de diagnóstico destina-se principalmente a dispositivos de local de série 8000 do StorSimple (8100 e 8600).

## <a name="run-diagnostics-tool"></a>Execute a ferramenta de diagnóstico

Esta ferramenta pode ser executada através da interface do Windows PowerShell do dispositivo StorSimple. Existem duas formas de aceder à interface local do dispositivo:

* [Utilizar o PuTTY para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Aceder remotamente a ferramenta através do Windows PowerShell para StorSimple](storsimple-remote-connect.md).

Neste artigo, partimos do pressuposto que tiver estabelecido ligação à consola de série do dispositivo através do PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Para executar a ferramenta de diagnóstico

Assim que tiver estabelecido ligação à interface do dispositivo do Windows PowerShell, execute os seguintes passos para executar o cmdlet.
1. Inicie sessão da consola de série do dispositivo ao seguir os passos no [utilizar o PuTTY para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Escreva o seguinte comando:

    `Invoke-HcsDiagnostics`

    Se não for especificado o parâmetro de âmbito, o cmdlet executa todos os testes de diagnóstico. Estes testes incluem system, funcionamento do componente de hardware, rede e o desempenho. 
    
    Para executar apenas um teste específico, especifique o parâmetro de âmbito. Por exemplo, para executar o teste de rede, escreva

    `Invoke-HcsDiagnostics -Scope Network`

3. Selecione e copie a saída da janela do PuTTY para um ficheiro de texto para análise adicional.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Cenários para utilizar a ferramenta de diagnóstico

Utilize a ferramenta de diagnóstico para resolver o estado de funcionamento de rede, o desempenho, o sistema e o hardware do sistema. Seguem-se alguns cenários possíveis:

* **Dispositivo offline** -8000 do StorSimple da série dispositivo estiver offline. No entanto, da interface do Windows PowerShell, parece que ambos os controladores estão em execução.
    * Pode utilizar esta ferramenta para, em seguida, determinar o estado de rede.
         
         > [!NOTE]
         > Não utilize esta ferramenta para avaliar definições de desempenho e da rede num dispositivo antes do registo (ou configurar através do Assistente de configuração). Um IP válido é atribuído ao dispositivo durante o Assistente de configuração e registo. Pode executar este cmdlet, num dispositivo que não está registado para o estado de funcionamento do hardware e sistema. Utilize o parâmetro de âmbito, por exemplo:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemas com dispositivos persistente** -ocorrerem problemas com dispositivos que parecem manter. Por exemplo, registo está a falhar. Pode também ter problemas com dispositivos depois do dispositivo está operacional e registado com êxito para o tempo.
    * Neste caso, utilize esta ferramenta para resolução de problemas preliminar antes de iniciar um pedido de serviço com Support da Microsoft. Recomendamos que execute esta ferramenta e capturar o resultado desta ferramenta. Em seguida, pode fornecer este resultado para suportar a fim de acelerar a resolução de problemas.
    * Se existirem quaisquer falhas de cluster ou o componente de hardware, deve iniciar sessão num pedido de suporte.

* **Falta o desempenho de dispositivo** -dispositivo Your StorSimple é lento.
    * Neste caso, execute este cmdlet com o parâmetro de âmbito definido para o desempenho. Analise o resultado. Obtenha a nuvem latências de leitura e escrita. Utilizar as latências reportadas como destino alcançável máximo, pesar alguma sobrecarga para o processamento de dados interna e, em seguida, implementar as cargas de trabalho no sistema. Para obter mais informações, aceda a [utilize o teste de rede para resolver problemas de desempenho de dispositivo](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Saídas de teste e exemplo de diagnóstico

### <a name="hardware-test"></a>Teste de hardware

Este teste determina o estado dos componentes de hardware, USM firmware e o firmware do disco em execução no seu sistema.

* Os componentes de hardware reportados são os componentes que falhou o teste ou não estão presentes no sistema.
* As versões de firmware USM firmware e do disco são reportadas para o controlador 0, 1 de controlador e partilharam componentes no sistema. Para obter uma lista completa dos componentes de hardware, aceda a:

    * [Componentes no bastidor primário](storsimple-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Componentes no bastidor EBOD](storsimple-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Se o teste de hardware comunica componentes falhados, [iniciar um pedido de serviço com o Microsoft Support](storsimple-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Saída de exemplo de teste de hardware executada num dispositivo 8100

Eis uma saída de exemplo de um dispositivo StorSimple 8100. No dispositivo 8100 modelo, o bastidor EBOD não está presente. Por conseguinte, os componentes de controlador EBOD não são reportados.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Teste de sistema

Este teste relatórios as informações do sistema, as atualizações disponíveis, as informações de cluster e as informações de serviço para o seu dispositivo.

* As informações do sistema incluem o modelo, o número de série do dispositivo, o fuso horário, o estado de controlador e a versão do software de detalhado em execução no sistema. Para compreender os vários parâmetros de sistema comunicados como resultado, aceda a [interpretar as informações do sistema](#appendix-interpreting-system-information).

* A disponibilidade de atualização se os modos normais e de manutenção estão disponíveis os relatórios e os respetivos nomes de pacote associados. Se `RegularUpdates` e `MaintenanceModeUpdates` são `false`, isto indica que as atualizações não estão disponíveis. O dispositivo está atualizado.
* As informações de cluster contém as informações em vários componentes lógicos de todos os grupos de clusters HCS e os seus respetivos Estados. Se vir um grupo de offline cluster nesta secção do relatório, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md).
* As informações de serviço incluem os nomes e Estados de todos os serviços HCS e CiS em execução no seu dispositivo. Estas informações são úteis para o Support da Microsoft na resolução do problema do dispositivo.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Saída de exemplo de teste de sistema executada num dispositivo 8100

Eis uma saída de exemplo do teste sistema executada num dispositivo 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_servic         Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Teste de rede

Este teste valida o estado das interfaces de rede, portas, DNS e NTP conetividade do servidor, SSL certificados, as credenciais da conta de armazenamento, conectividade com os servidores de atualização e conectividade de proxy web no dispositivo StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Saída de exemplo da rede de teste quando apenas DATA0 está ativado

Eis uma saída de exemplo do dispositivo 8100. Pode ver no resultado do que:
* Apenas os dados 0 e interfaces de rede de 1 de dados estiverem ativadas e configuradas.
* DADOS 2 a 5 não estão ativados no portal.
* A configuração do servidor DNS válida e o dispositivo se possa ligar através do servidor DNS.
* A conectividade de servidor NTP também é adequada.
* As portas 80 e 443 estão abertas. No entanto, a porta 9354 está bloqueada. Com base no [requisitos de sistema de rede](storsimple-system-requirements.md), é necessário abrir esta porta para a comunicação de barramento de serviço.
* Certificação de SSL é válida.
* O dispositivo se possa ligar à conta de armazenamento: _myss8000storageacct_.
* A conectividade aos servidores de atualização é válida.
* O proxy web não está configurado neste dispositivo.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Saída de exemplo de teste de rede quando são ativados DATA0 e DATA1

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Teste de desempenho

Este teste comunica o desempenho de nuvem através das latências de leitura-escrita de nuvem para o seu dispositivo. Esta ferramenta pode ser utilizada para estabelecer uma linha de base do desempenho em nuvem que pode alcançar com StorSimple. A ferramenta de relatórios o desempenho máximo (melhor cenário para latências de leitura e escrita) que pode obter para a ligação.

Como a ferramenta de anunciar o máximo desempenho alcançável, podemos utilizar as latências reportadas de leitura e escrita como destinos ao implementar as cargas de trabalho.

O teste simula os tamanhos de blob associados com os tipos de volume diferente no dispositivo. Camadas de regular e as cópias de segurança de volumes localmente afixados utilizam um tamanho de blob de 64 KB. Volumes em camadas com opção de arquivo selecionado utilizam tamanho de dados de BLOBs de 512 KB. Se o dispositivo tem volumes em camadas e localmente afixados configurado, apenas o teste correspondente para o blob de 64 KB é executado o tamanho dos dados.

Para utilizar esta ferramenta, execute os seguintes passos:

1.  Em primeiro lugar, crie uma combinação de volumes em camadas e volumes em camadas com opção arquivada marcada. Esta ação garante que a ferramenta executa os testes de 64 KB e 512 KB tamanhos de blob.

2. Execute o cmdlet depois de ter criado e configurado os volumes. Escreva:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anote as latências de leitura e escrita comunicado pela ferramenta. Este teste pode demorar vários minutos a executar antes que reporta os resultados.

4. Se as latências de ligação estão todos em do intervalo esperado, em seguida, as latências comunicadas pela ferramenta podem ser utilizadas como destino alcançável máximo ao implementar as cargas de trabalho. Pesar alguma sobrecarga de processamento de dados interna.

    Se as latências de leitura e escrita comunicado pela ferramenta de diagnóstico são elevada:

    1. Configure a análise de armazenamento para os serviços de blob e analisar a saída para compreender as latências da conta do storage do Azure. Para obter instruções detalhadas, aceda a [ativar e configurar a análise de armazenamento](../storage/common/storage-enable-and-view-metrics.md). Se as latências também estão alta e comparável aos números que recebeu da ferramenta de diagnóstico do StorSimple, terá de iniciar um pedido de serviço com o storage do Azure.

    2. Se as latências da conta de armazenamento são baixos, contacte o administrador de rede para investigar problemas de latência na sua rede.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Saída de exemplo de teste de desempenho executada num dispositivo 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Apêndice: interpretar as informações do sistema

Eis uma tabela que descreve o que os parâmetros do Windows PowerShell vários no mapa de informações de sistema para. 

| Parâmetro do PowerShell    | Descrição  |
|-------------------------|------------------|
| ID da Instância             | Cada controlador tem um identificador exclusivo ou um GUID associado.|
| Nome                    | O nome amigável do dispositivo como configurado através do portal do Azure durante a implementação do dispositivo. O nome amigável predefinido é o número de série do dispositivo. |
| Modelo                   | O modelo do seu dispositivo de série 8000 do StorSimple. O modelo pode ser 8100 ou 8600.|
| SerialNumber            | O número de série do dispositivo é atribuído na fábrica de e 15 carateres de comprimento. Por exemplo, 8600 SHX0991003G44HT indica:<br> 8600 – é o modelo do dispositivo.<br>SHX – é o site de fabrico.<br> 0991003 - é um produto específico. <br> G44HT-os últimos 5 dígitos são aumentados para criar os números de série exclusivos. Não pode ser um conjunto sequencial.|
| Fuso horário                | O dispositivo fuso horário conforme configurado no portal do Azure durante a implementação do dispositivo.|
| CurrentController       | O controlador que estejam ligados à através da interface do Windows PowerShell do dispositivo StorSimple.|
| ActiveController        | O controlador de que está ativo no seu dispositivo e está a controlar todas as operações de disco e rede. Isto pode ser controlador 0 ou 1 de controlador.  |
| Controller0Status       | O estado do controlador 0 no seu dispositivo. O estado do controlador pode ser normal, no modo de recuperação ou inacessível.|
| Controller1Status       | O estado do controlador 1 no seu dispositivo.  O estado do controlador pode ser normal, no modo de recuperação ou inacessível.|
| SystemMode              | O estado global do dispositivo StorSimple. O estado do dispositivo pode ser normal, manutenção automática, ou desativado (corresponde à desativada no portal do Azure).|
| FriendlySoftwareVersion | A cadeia amigável que corresponde à versão do software de dispositivo. Para um sistema que executa a atualização 4, a versão do software amigável seria StorSimple 8000 série atualização 4.0.|
| HcsSoftwareVersion      | A versão do software HCS em execução no seu dispositivo. Por exemplo, a versão do software HCS correspondente ao StorSimple 8000 série atualização 4.0 é 6.3.9600.17820. |
| ApiVersion              | A versão do software da API do Windows PowerShell do dispositivo HCS.|
| VhdVersion              | A versão do software da imagem de fábrica do dispositivo foi enviado com. Se redefinir o seu dispositivo para as predefinições de fábrica, em seguida, executar esta versão do software.|
| OSVersion               | A versão do software do sistema operativo Windows Server em execução no dispositivo. O dispositivo StorSimple baseia-se desativar o Windows Server 2012 R2, que corresponde ao 6.3.9600.|
| CisAgentVersion         | A versão para o agente de Cis em execução no dispositivo StorSimple. Este agente ajuda a comunicar com o serviço StorSimple Manager em execução no Azure.|
| MdsAgentVersion         | A versão correspondente para o agente de Mds em execução no dispositivo StorSimple. Este agente move dados para a monitorização e diagnóstico de serviço (MDS).|
| Lsisas2Version          | A versão correspondente para os controladores de LSI no dispositivo StorSimple.|
| Capacidade                | A capacidade total do dispositivo em bytes.|
| RemoteManagementMode    | Indica se o dispositivo pode ser gerido remotamente através do seu interface do Windows PowerShell. |
| FipsMode                | Indica se o modo de Estados Unidos Federal Information processamento Standard (FIPS) está ativado no seu dispositivo. A norma FIPS 140 define os algoritmos criptográficos aprovados para utilização pelos sistemas de computador government Federal dos EUA para a proteção de dados confidenciais. Para dispositivos com a atualização 4 ou posterior, o modo FIPS está ativado por predefinição. |

## <a name="next-steps"></a>Passos seguintes

* Saiba o [sintaxe do cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Saiba mais sobre como [resolver problemas de implementação](storsimple-troubleshoot-deployment.md) no dispositivo StorSimple.
