---
title: Como utilizar PerfInsights no Microsoft Azure | Microsoft Docs
description: Aprende como utilizar PerfInsights para resolver problemas de desempenho da VM do Windows.
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/18/2017
ms.author: genli
ms.openlocfilehash: 8d66bbdf6f7153cf59af60051e54377f6eccdc3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-perfinsights"></a>Como utilizar PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) é um script automatizado que recolhe informações de diagnóstico úteis, executa cargas de esforço de e/s e fornece um relatório de análise para ajudar a resolver problemas de desempenho da VM do Windows no Microsoft Azure. Isto pode ser executado nas máquinas virtuais como um script de autónomo ou diretamente a partir do portal através da instalação [extensão de VM de diagnóstico do Azure desempenho](performance-diagnostics-vm-extension.md).

Recomendamos que execute este script antes de abrir um pedido de suporte com a Microsoft para problemas de desempenho da VM.

## <a name="supported-troubleshooting-scenarios"></a>Suportado cenários de resolução de problemas

PerfInsights pode recolher e analisar vários tipos de informações que são agrupados em cenários exclusivos.

### <a name="collect-disk-configuration"></a>Recolher configuração de disco 

Este cenário recolhe a configuração do disco e outras informações importantes, incluindo os seguintes itens:

-   Registos de eventos

-   Estado da rede para todas as ligações de entrada e saídas

-   Definições de configuração de rede e de firewall

-   Lista de tarefas para todas as aplicações que estão atualmente em execução no sistema

-   Ficheiro de informações criado pela msinfo32 para a máquina virtual (VM)

-   Definições de configuração de base de dados Microsoft SQL Server (se a VM é identificada como um servidor que está a executar o SQL Server)

-   Contadores de fiabilidade de armazenamento

-   Correções importantes do Windows

-   Controladores de filtro instalado

Esta é uma coleção passiva de informações que não devem afetar o sistema. 

>[!Note]
>Este cenário é automaticamente incluído em cada um dos seguintes cenários.

### <a name="benchmarkstorage-performance-test"></a>Teste de desempenho do benchmark/armazenamento

Este cenário é executado o [diskspd](https://github.com/Microsoft/diskspd) teste benchmark (IOPS e MBPS) para todas as unidades que estão ligados à VM. 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado num sistema de produção em direto. Se necessário, execute este cenário numa janela de manutenção dedicada para evitar quaisquer problemas. Maior carga de trabalho que é causada por um teste de rastreio ou benchmark pode afetar negativamente o desempenho da sua VM.
>

### <a name="general-vm-slow-analysis"></a>Análise de geral VM lenta 

Este cenário é executado um [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) rastreio, utilizando os contadores que são especificados no ficheiro Generalcounters.txt. Se a VM é identificada como um servidor que está a executar o SQL Server, é executado um rastreio de contador de desempenho utilizando os contadores que se encontram no ficheiro Sqlcounters.txt. Também inclui dados de diagnóstico de desempenho.

### <a name="vm-slow-analysis-and-benchmark"></a>Análise de VM lenta e benchmark

Este cenário é executado um [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) rastreio que é seguido um [diskspd](https://github.com/Microsoft/diskspd) teste benchmark. 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado num sistema de produção em direto. Se necessário, execute este cenário numa janela de manutenção dedicada para evitar quaisquer problemas. Maior carga de trabalho que é causada por um teste de rastreio ou benchmark pode afetar negativamente o desempenho da sua VM.
>

### <a name="azure-files-analysis"></a>Análise de ficheiros do Azure 

Este cenário é executada uma captura de contador de desempenho especial, juntamente com um rastreio de rede. A captura inclui todos os contadores de "Partilhas de cliente SMB". Seguem-se alguns chaves SMB cliente partilha contadores de desempenho que fazem parte de captura de:

| **Tipo**     | **Contador de partilhas de cliente do SMB** |
|--------------|-------------------------------|
| IOPS         | Pedidos de dados/seg             |
|              | Pedidos de leitura/seg             |
|              | Escrever pedidos/seg            |
| Latência      | Média de pedidos de dados/seg         |
|              | Média de seg/leitura                 |
|              | Média de seg/escrita                |
| Tamanho de e/s      | Média Pedido de bytes/dados       |
|              | Média Bytes/leitura               |
|              | Média Bytes/escrita              |
| Débito   | Bytes de dados/seg                |
|              | Bytes lidos/seg                |
|              | Escrever Bytes/seg               |
| Comprimento da fila | Média Comprimento da fila de leitura        |
|              | Média Comprimento da fila de escrita       |
|              | Média Comprimento da fila de dados        |

### <a name="custom-configuration"></a>Configuração personalizada 

Quando executa uma configuração personalizada, estiver a executar todos os rastreios (diagnóstico de desempenho, contador de desempenho, xperf, rede, storport) em paralelo, dependendo de quantos rastreios diferentes estão selecionados. O rastreio esteja concluída, a ferramenta é executada diskspd benchmark, se estiver selecionada. 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado num sistema de produção em direto. Se necessário, execute este cenário numa janela de manutenção dedicada para evitar quaisquer problemas. Maior carga de trabalho que é causada por um teste de rastreio ou benchmark pode afetar negativamente o desempenho da sua VM.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Que tipo de informações recolhido pelo script?

Regista informações sobre a VM do Windows, discos ou configuração de agrupamentos de armazenamento, contadores de desempenho, e vários rastreios são recolhidos dependendo do cenário de desempenho utilizado:

|Dados recolhidos                              |  |  | Cenários de desempenho |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Recolher configuração do disco | Teste de desempenho do benchmark/armazenamento | Análise de geral VM lenta | Análise de VM lenta e benchmark | Análise de ficheiros do Azure | Configuração personalizada |
| Informações de registos de eventos      | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Informações do sistema               | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Mapa de volume                       | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Mapa de disco                         | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Tarefas em execução                    | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Contadores de fiabilidade de armazenamento     | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Informações de armazenamento              | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Saída de fsutil                    | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Informações do controlador de filtro               | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Netstat saída                   | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Configuração da rede            | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Configuração da firewall           | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Configuração do SQL Server         | Sim                        | Sim                                | Sim                      | Sim                            | Sim                  | Sim                  |
| Os rastreios de diagnóstico de desempenho * |                            |                                    | Sim                      |                                |                      | Sim                  |
| O contador de desempenho rastreio * *     |                            |                                    |                          |                                |                      | Sim                  |
| O contador SMB rastreio * *             |                            |                                    |                          |                                | Sim                  |                      |
| Contador do SQL Server rastreio * *      |                            |                                    |                          |                                |                      | Sim                  |
| Rastreio XPerf                      |                            |                                    |                          |                                |                      | Sim                  |
| Rastreio StorPort                   |                            |                                    |                          |                                |                      | Sim                  |
| Rastreio de rede                    |                            |                                    |                          |                                | Sim                  | Sim                  |
| Rastreio de Diskspd Benchmark ***      |                            | Sim                                |                          | Sim                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Rastreio de diagnóstico de desempenho (*)

Executa um motor baseado em regras em segundo plano para recolher dados e diagnosticar problemas de desempenho em curso. Atualmente são suportadas as seguintes regras:

- Regra de HighCpuUsage: Deteta períodos de utilização elevados da CPU e mostra os consumidores de utilização da CPU superiores durante os períodos.
- Regra de HighDiskUsage: Deteta períodos de utilização elevada de disco em discos físicos e mostra os consumidores de utilização de disco superior durante os períodos.
- Regra de HighResolutionDiskMetric: mostra métricas de latência IOPS, débito e/s por 50 milissegundos para cada disco físico. Ajuda a identificar rapidamente os períodos de limitação de disco.
- Regra de HighMemoryUsage: Deteta períodos de utilização elevada da memória e mostra a memória superior consumidores de utilização durante os períodos.

> [!NOTE] 
> Atualmente, são suportadas versões do Windows que incluem o .NET Framework 3.5 ou versões posteriores.

### <a name="performance-counter-trace-"></a>Rastreio de contador de desempenho (*)

Recolhe os seguintes contadores de desempenho:

- \Process \Processor, \Memory, \Thread, \PhysicalDisk, \LogicalDisk
- \Cache\Dirty páginas, \Cache\Lazy escrita esvaziamentos/seg, \Server\Pool na memória não paginável, falhas, falhas de \Server\Pool bloco paginado
- Selecionar contadores em \Network Interface, \IPv4\Datagrams \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network adaptador, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ QoS Policy\Packets, \Per processador rede Interface Card atividade, \Microsoft Winsock BSP de rede

#### <a name="for-sql-server-instances"></a>Para instâncias do SQL Server
- Gestor de servidor: memória intermédia \SQL, estatísticas de agrupamento \SQLServer:Resource, \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, estatísticas
- Métodos de \SQLServer:Access

#### <a name="for-azure-files"></a>Para ficheiros do Azure
Partilhas de cliente \SMB

### <a name="diskspd-benchmark-trace-"></a>Rastreio de Diskspd Benchmark (*)
Testes de carga de trabalho de e/s Diskspd [disco do SO (escrita) e unidades de agrupamento (leitura/escrita)]

## <a name="run-the-perfinsights-on-your-vm"></a>Execute o PerfInsights na VM

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>O que é necessário saber antes de posso executar o script? 

**Requisitos de script**

1.  Este script tem de ser executado na VM que tenha o problema de desempenho. 

2.  Os sos seguintes são suportados: Windows Server 2008 R2, 2012, 2012 R2, 2016; Windows 8.1 e Windows 10.

**Possíveis problemas ao executar o script em produção VMs:**

1.  O script poderá afetar negativamente o desempenho da VM quando é utilizado em conjunto com o cenário "Benchmark" ou "Personalizada" que é configurado utilizando XPerf ou DiskSpd. Seja cuidadoso ao executar o script num ambiente de produção.

2.  Quando utiliza o script, juntamente com o cenário "Benchmark" ou "Personalizada" que é configurado utilizando DiskSpd, certifique-se de que nenhuma outra atividade em segundo plano interfere com a carga de trabalho de e/s nos discos testadas.

3.  Por predefinição, o script utiliza a unidade de armazenamento temporário para recolher dados. Se o rastreio permanece ativada durante mais tempo, a quantidade de dados que são recolhidos pode ser relevante. Isto pode reduzir a disponibilidade de espaço em disco temporário, por conseguinte que afetam a qualquer aplicação que se baseie nesta unidade.

### <a name="how-do-i-run-perfinsights"></a>Como posso executar PerfInsights? 

Pode executar PerfInsights numa máquina virtual através da instalação [extensão de VM de diagnóstico do Azure desempenho](performance-diagnostics-vm-extension.md) ou executá-lo como um script de autónomo. 

**Instalar e executar PerfInsights do portal do Azure**

PerfInsights agora podem ser executados com uma extensão VM chamada extensão de diagnóstico de desempenho do Azure. Para obter mais informações, consulte [extensão de diagnóstico de desempenho de Azure instalar](performance-diagnostics-vm-extension.md#install-the-extension).  

**Execute o script de PerfInsights no modo autónomo**

Para executar o script de PerfInsights, siga estes passos:


1. Transferir [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. O ficheiro PerfInsights.zip de desbloqueio. Para tal, clique no ficheiro PerfInsights.zip, selecione **propriedades**. No **geral** separador, selecione **desbloqueio** e, em seguida, selecione **OK**. Esta ação irá garantir que o script é executado sem avisos adicionais de segurança.  

    ![Desbloquear o ficheiro zip](media/how-to-use-perfInsights/unlock-file.png)

3.  Expanda o ficheiro PerfInsights.zip comprimido para a unidade temporária (por predefinição, normalmente, a unidade D). O ficheiro comprimido deve conter os seguintes ficheiros e pastas:

    ![ficheiros na pasta zip](media/how-to-use-perfInsights/file-folder.png)

4.  Abra o Windows PowerShell como administrador e, em seguida, execute o script de PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Poderá ter de introduzir "y" para se lhe for pedido que confirme que pretende alterar a política de execução.

    Na caixa de diálogo de exclusão de responsabilidade, é-lhe dada a opção partilhar informações de diagnóstico com Support da Microsoft. Também terá de consentir para o contrato de licença para continuar. Faça as suas seleções e, em seguida, clique em **executar Script**.

    ![Caixa de exclusão de responsabilidade](media/how-to-use-perfInsights/disclaimer.png)

5.  Submeta o número de cenário, se estiver disponível, ao executar o script (trata-se para a nossa estatísticas). Em seguida, clique em **OK**.
    
    ![Introduza o ID de suporte](media/how-to-use-perfInsights/enter-support-number.png)

6.  Selecione a unidade de armazenamento temporário. O Script pode deteção automática a letra de unidade da unidade. Se ocorrerem problemas nesta fase, poderá ser-lhe pedido para selecionar a unidade (unidade predefinida é D). Registos gerados são armazenados aqui no registo de\_pasta de coleção. Depois de introduzir ou aceitar a letra de unidade, clique em **OK**.

    ![Introduza a unidade](media/how-to-use-perfInsights/enter-drive.png)

7.  Selecione um cenário de resolução de problemas da lista fornecida.

       ![Selecione os cenários de suporte](media/how-to-use-perfInsights/select-scenarios.png)

8.  Também pode executar PerfInsights sem a IU.

    O seguinte comando executa o "geral VM lenta Analysis Services" cenário sem uma linha de comandos de IU de resolução de problemas ou a captura de dados para 30 segundos. Pede-lhe consentimento para a mesma exclusão de responsabilidade e EULA mencionados no passo 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    Se pretender PerfInsights para ser executado no modo silencioso, utilize o **- AcceptDisclaimerAndShareDiagnostics** parâmetro. Por exemplo, utilize o seguinte comando:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Como posso resolver problemas ao executar o script?

Se o script termina anormalmente, é possível limpar estado inconsistente ao executar o script em conjunto com o - comutador de limpeza, da seguinte forma:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Se ocorrerem problemas durante a deteção automática de unidade temporária, poderá ser-lhe pedido para selecionar a unidade (unidade predefinida é D).

![unidade introduza](media/how-to-use-perfInsights/enter-drive.png)

O script desinstala as ferramentas e remove pastas temporárias.

### <a name="troubleshooting-other-script-issues"></a>Resolução de problemas de outros problemas de script 

Se ocorrerem problemas ao executar o script, prima Ctrl + C para interromper a execução do script. Para remover objetos temporários, consulte a secção "Limpar após a terminação anormal".

Se continuar a ocorrer a falha de script, mesmo após várias tentativas, recomendamos que execute o script no "modo de depuração" utilizando o "-Debug" opção de parâmetro no arranque.

Após a falha ocorre, copie a saída completa da consola do PowerShell e enviá-lo para o agente de Support da Microsoft que está a prestar assistência para ajudar a resolver o problema.

### <a name="how-do-i-run-the-script-in-custom-configuration-mode"></a>Como se executar o script no modo de configuração personalizado?

Ao selecionar o **personalizada** configuração, pode ativar várias rastreios em paralelo (utilize Shift para selecionar vários):

![Selecione cenários](media/how-to-use-perfInsights/select-scenario.png)

Quando seleciona o diagnóstico de desempenho, rastreio de contador de desempenho, XPerf rastreio, rastreio da rede ou Storport rastreio cenários, siga as instruções nas caixas de diálogo e tentam reproduzir o problema de desempenho lento depois de iniciar os rastreios.

A seguinte caixa de diálogo permite-lhe iniciar um rastreio:

![rastreio de início](media/how-to-use-perfInsights/start-trace-message.png)

Para parar os rastreios, tem de confirmar o comando numa segunda caixa de diálogo.

![Stop-rastreio](media/how-to-use-perfInsights/stop-trace-message.png)
![stop-rastreio](media/how-to-use-perfInsights/ok-trace-message.png)

Quando os rastreios nem de operações estiverem concluídas, um novo ficheiro é gerado de d:\\registo\_coleção (ou unidade temporária) com o nome **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip.** Pode enviar este ficheiro para o agente de suporte para análise.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>Reveja o relatório de diagnóstico criado pelo PerfInsights

Dentro do **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip ficheiro,** que é gerado pelo PerfInsights, pode encontrar um relatório HTML que explica em detalhe findings de PerfInsights. Para rever o relatório, expanda o **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip** de ficheiros e, em seguida, abra o **PerfInsights Report.html** ficheiro.

Selecione o **Findings** separador.

![localizar separador](media/how-to-use-perfInsights/findingtab.png)

**Notas**

-   Mensagens vermelho são conhecidas problemas de configuração que podem causar problemas de desempenho.

-   As mensagens no amarelo são avisos que representam não ideal configurações que não necessariamente a causar problemas de desempenho.

-   As mensagens azul são apenas as instruções informativos.

Reveja as ligações HTTP para todas as mensagens de erro vermelho obter informações mais detalhadas sobre os findings e como pode afetar o desempenho ou melhores práticas para as configurações com otimização de desempenho.

### <a name="disk-configuration-tab"></a>Separador de configuração do disco

O **descrição geral** secção apresenta vistas diferentes da configuração do armazenamento, incluindo informações de Diskpart e espaços de armazenamento

O **DiskMap** e **VolumeMap** secções descrevem numa perspetiva dupla lógica como volumes e discos físicos estão relacionados uns aos outros.

Na perspetiva PhysicalDisk (DiskMap), a tabela mostra todos os volumes lógicos que estão em execução no disco. No exemplo seguinte, PhysicalDrive2 executa dois Volumes lógico criado em várias partições (J e H):

![separador de dados](media/how-to-use-perfInsights/disktab.png)

Na perspetiva Volume (*VolumeMap*), as tabelas apresentam todos os discos físicos em cada volume lógico. Tenha em atenção que para discos RAID/dinâmico, poderá executar a um volume lógico após vários discos físicos. No seguinte exemplo *c:\\montar* é uma pontodemontagem configurada como *SpannedDisk* no PhysicalDisks \#2 e \#3:

![separador de volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-server-tab"></a>Separador do SQL Server

Se o destino da VM aloja quaisquer instâncias do SQL Server, consulte um separador adicional no relatório com o nome **do SQL Server**:

![separador de SQL](media/how-to-use-perfInsights/sqltab.png)

Esta secção contém uma descrição "geral" e separadores sub adicionais para cada uma das instâncias do SQL Server alojadas na VM.

A secção "Descrição geral" contém uma tabela útil que resume a todos os discos físicos (discos de dados e do sistema) que estão em execução e que contenham uma mistura de ficheiros de dados e ficheiros de registo de transações.

No exemplo seguinte, *PhysicalDrive0* (em execução a unidade C) é apresentado porque tanto o *modeldev* e *modellog* ficheiros estão localizados na unidade C e são de diferentes tipos (por exemplo, o ficheiro de dados e registo de transações, respetivamente):

![LogInfo](media/how-to-use-perfInsights/loginfo.png)

Os separadores de específico da instância do SQL Server contenham uma secção geral que apresenta informações básicas sobre a instância selecionada e as secções adicionais para as informações avançadas, incluindo opções de utilizador, as configurações e definições.

## <a name="references-to-the-external-tools-used"></a>Referências para as ferramentas externas utilizadas

### <a name="diskspd"></a>Diskspd

O DISKSPD é um armazenamento de carga e o desempenho do gerador de ferramenta de teste das equipas de engenharia do Windows e Windows Server e a infraestrutura de servidor de nuvem. Para obter mais informações, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf é uma ferramenta da linha de comandos para capturar rastreios do Kit de ferramentas de desempenho do Windows.

Para obter mais informações, consulte [Toolkit de desempenho do Windows – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Passos Seguintes

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>Carregar registos de diagnóstico e de relatórios ao Microsoft Support para uma revisão mais aprofundada

Quando trabalha com a equipa de Support da Microsoft, podem ser solicitadas para transmitir o resultado que é gerado pelo PerfInsights para ajudar o processo de resolução de problemas.

O agente de suporte irá criar uma área de trabalho DTM para si e receberá uma mensagem de e-mail que inclui uma ligação para o [portal DTM (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) e um ID de utilizador exclusivo e uma palavra-passe.

Esta mensagem será enviada **CTS automatizada diagnóstico serviços** (ctsadiag@microsoft.com).

![Exemplo da mensagem](media/how-to-use-perfInsights/supportemail.png)

Para segurança adicional, será necessário para alterar a palavra-passe na primeira utilização.

Depois de iniciar sessão DTM, vai encontrar uma caixa de diálogo para carregar o **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip** ficheiro que foi recolhido por PerfInsights.
