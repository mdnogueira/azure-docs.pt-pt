---
title: "Resolução de problemas de diagnóstico do Azure | Microsoft Docs"
description: "Resolva problemas quando utiliza o diagnóstico do Azure em Virtual Machines do Azure, Service Fabric ou serviços em nuvem."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.openlocfilehash: b03265b52886b30e4b9de0b0293e5dadd6d2413a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-troubleshooting"></a>Resolução de problemas de diagnóstico do Azure
Este artigo descreve as informações de resolução de problemas que são relevantes para a utilização de diagnóstico do Azure. Para mais informações sobre o diagnóstico do Azure, consulte [descrição geral de diagnóstico do Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Componentes lógicos
**O iniciador de plug-in de diagnóstico (DiagnosticsPluginLauncher.exe)**: inicia a extensão de diagnóstico do Azure. Funciona como a entrada do ponto de processo.

**Plug-in de diagnóstico (DiagnosticsPlugin.exe)**: Configura, inicia e gere a duração do agente de monitorização. É o processo principal que é iniciado pelo iniciador.

**Agente de monitorização (MonAgent\*.exe processos)**: monitores, recolhe e transfere os dados de diagnóstico.  

## <a name="logartifact-paths"></a>Caminhos de registo/artefactos
Seguem-se os caminhos para alguns registos importantes e os artefactos. Vamos referir-se a estas informações ao longo do resto do documento.

### <a name="azure-cloud-services"></a>Cloud Services do Azure
| Artefactos | Caminho |
| --- | --- |
| **Ficheiro de configuração de diagnósticos do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão > \Config.txt |
| **Ficheiros de registo** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão > \ |
| **Arquivo local para dados de diagnóstico** | C:\Resources\Directory\<CloudServiceDeploymentID >.\< RoleName >. DiagnosticStore\WAD0107\Tables |
| **Ficheiro de configuração do agente de monitorização** | C:\Resources\Directory\<CloudServiceDeploymentID >.\< RoleName >. DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacote de extensão de diagnóstico do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão > |
| **Caminho do utilitário de recolha de registo** | %SystemDrive%\Packages\GuestAgent\ |
| **Ficheiro de registo MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas virtuais
| Artefactos | Caminho |
| --- | --- |
| **Ficheiro de configuração de diagnósticos do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versão > \RuntimeSettings |
| **Ficheiros de registo** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versão > \Logs\ |
| **Arquivo local para dados de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Tables |
| **Ficheiro de configuração do agente de monitorização** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Configuration\MaConfig.xml |
| **Ficheiro de estado** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versão > \Status |
| **Pacote de extensão de diagnóstico do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion >|
| **Caminho do utilitário de recolha de registo** | C:\WindowsAzure\Packages |
| **Ficheiro de registo MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Configuration\MonAgentHost. < seq_num >. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Dados métricos não aparecem no portal do Azure
Diagnóstico do Azure fornece dados métricos que podem ser apresentados no portal do Azure. Se tiver problemas a ver os dados no portal, verifique o WADMetrics\* tabela na conta de armazenamento de diagnóstico do Azure para ver se os registos de métricos correspondentes existem. 

Aqui, o **PartitionKey** da tabela é um conjunto de dimensionamento de ID, a máquina virtual ou a máquina virtual de recursos. **RowKey** é o nome da métrica (também conhecido como o nome de contador de desempenho).

Verifique se o ID de recurso está incorreto, **diagnóstico** **configuração** > **métricas** > **ResourceId**para ver se o ID de recurso está definido corretamente.

Verifique se não houver nenhum dado para a métrica específica, **configuração de diagnósticos** > **PerformanceCounter** para ver se a métrica (contador de desempenho) está incluída. Iremos ativar os seguintes contadores por predefinição:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET aplicações (__Total__) \Requests/Sec
- \ASP.NET aplicações (__Total__) \Errors totais/seg
- \ASP.NET\Requests colocados em fila
- \ASP.NET\Requests rejeitados
- \Processor(w3wp)\% de tempo do processador
- Bytes de \Private \Process (w3wp)
- \Process(WaIISHost)\% de tempo do processador
- Bytes de \Private \Process (WaIISHost)
- \Process(WaWorkerHost)\% de tempo do processador
- Bytes de \Private \Process (WaWorkerHost)
- \Memory\Page falhas por segundo
- \.Memória NET CLR (_Global_)\% tempo na GC
- \LogicalDisk (c) \Disk escrita Bytes/seg
- \Disk \LogicalDisk (c) Bytes lidos/seg
- \LogicalDisk (d:) \Disk escrita Bytes/seg
- \Disk \LogicalDisk (d:) Bytes lidos/seg

Se a configuração está corretamente definida, mas não pode ver os dados métricos, utilize as diretrizes seguintes para ajudar a resolver.


## <a name="azure-diagnostics-isnt-starting"></a>Diagnóstico do Azure não está a iniciar
Para obter informações sobre o motivo pelo qual diagnósticos do Azure não conseguiu iniciar, consulte o **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** ficheiros na localização de ficheiros de registo que foi fornecido anteriormente. 

Se estes registos indicam `Monitoring Agent not reporting success after launch`, significa que ocorreu uma falha ao iniciar MonAgentHost.exe. Consulte os registos na localização indicado para `MonAgentHost log file` na secção anterior.

A última linha dos ficheiros de registo contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se encontrar um **negativo** código de saída, consulte o [tabela de código de saída](#azure-diagnostics-plugin-exit-codes) no [referencia secção](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dados de diagnóstico não são registados ao Storage do Azure
Determine se nenhum dos dados é a apresentação ou alguns dos dados é a apresentação.

### <a name="diagnostics-infrastructure-logs"></a>Registos de infraestrutura de diagnóstico
Diagnóstico regista todos os erros nos registos de infraestrutura do diagnóstico. Certifique-se de que ativou o [captura da infraestrutura de diagnóstico de registos na sua configuração](#how-to-check-diagnostics-extension-configuration). Em seguida, pode procurar qualquer erros relevantes que são apresentados no rapidamente a `DiagnosticInfrastructureLogsTable` tabela na sua conta de armazenamento configurados.

### <a name="no-data-is-appearing"></a>Não existem dados é de apresentação
A razão mais comum que os dados do evento não aparecem em todos os é que as informações de conta de armazenamento está incorretamente definidas.

Solução: Corrija a configuração de diagnósticos e reinstalar o diagnóstico.

Se a conta de armazenamento está corretamente configurado e remoto acesso para a máquina e certifique-se de que o DiagnosticsPlugin.exe e MonAgentCore.exe estão em execução. Se não estiverem em execução, siga os passos no [diagnósticos do Azure não está a iniciar](#azure-diagnostics-is-not-starting). 

Se estiver a executar os processos, aceda a [dados obter capturadas localmente?](#is-data-getting-captured-locally) e siga as instruções incluídas.

### <a name="part-of-the-data-is-missing"></a>Parte dos dados está em falta
Se está a obter alguns dados, mas não todas, significa que o pipeline de coleção/transferência de dados está definido corretamente. Siga as subsecções aqui para restringir o problema.

#### <a name="is-the-collection-configured"></a>A coleção é configurada? 
A configuração de diagnósticos contém instruções para um determinado tipo de dados a serem recolhidos. [Reveja a configuração](#how-to-check-diagnostics-extension-configuration) para verificar se está a visualizar apenas para dados que configurou para a coleção.

#### <a name="is-the-host-generating-data"></a>O anfitrião está a gerar dados?
- **Contadores de desempenho**: Abra perfmon e verifique o contador.

- **Registos de rastreio**: remoto acesso para a VM e adicionar um TextWriterTraceListener ao ficheiro de configuração da aplicação.  Consulte http://msdn.microsoft.com/library/sk36c28t.aspx para configurar o serviço de escuta de texto.  Certifique-se de que o `<trace>` elemento tem `<trace autoflush="true">`.<br />
Se não vir a ser gerados de registos de rastreio, consulte [mais sobre os registos de rastreio em falta](#more-about-trace-logs-missing).

- **Rastreios ETW**: acesso remoto para a VM e instalar PerfView.  Na PerfView, execute **ficheiro** > **utilizador comando** > **escutar etwprovder1** > **etwprovider2**, e assim sucessivamente. O **escutar** comando maiúsculas e minúsculas e não pode ser espaços entre a lista de valores separados por vírgulas dos fornecedores ETW. Se não for possível executar o comando, pode selecionar o **registo** botão na parte inferior direita da ferramenta Perfview para ver o que tentou executar e que o resultado era.  Uma nova janela do pressuposto de que a entrada é correta, aparece. Em alguns segundos, começar a ver os rastreios ETW.

- **Registos de eventos**: acesso remoto para a VM. Abra `Event Viewer`e, em seguida, certifique-se de que existem os eventos.

#### <a name="is-data-getting-captured-locally"></a>Dados obter capturados localmente?
Em seguida, certifique-se que os dados obter capturados localmente.
Os dados são armazenados localmente no `*.tsf` ficheiros em [arquivo local para dados de diagnóstico](#log-artifacts-path). Diferentes tipos de registos obterem recolhidos em diferentes `.tsf` ficheiros. Os nomes são semelhantes aos nomes de tabela no armazenamento do Azure. 

Por exemplo, `Performance Counters` obter recolhido em `PerformanceCountersTable.tsf`. Registos de eventos obter recolhidos no `WindowsEventLogsTable.tsf`. Utilize as instruções no [extração registo Local](#local-log-extraction) secção para abrir os ficheiros de recolha local e certifique-se de que vê-los, obtendo recolhidos no disco.

Se não vê os registos obter recolhidos localmente e já tiver verificado que o anfitrião está a gerar dados, terá provavelmente um problema de configuração. Reveja cuidadosamente a sua configuração. 

Além disso, reveja a configuração que foi gerada para MonitoringAgent [MaConfig.xml](#log-artifacts-path). Verifique se existe uma secção que descreve a origem de registo relevantes. Em seguida, certifique-se de que não há perda na tradução entre a configuração de diagnósticos e a configuração do agente de monitorização.

#### <a name="is-data-getting-transferred"></a>Obter transferidos dados?
Se tiver verificado que os dados obter capturados localmente, mas ainda não o vir na sua conta do storage, siga os passos seguintes: 

- Certifique-se de que forneceu uma conta de armazenamento correta e que ainda não revertida através de chaves para a conta de armazenamento. Para os serviços de nuvem do Azure, por vezes, Vemos que pessoas não atualizar `useDevelopmentStorage=true`.

- Certifique-se de que a conta de armazenamento fornecido está correta. Certifique-se de que não tem restrições de rede que impedem que os componentes de chegar aos pontos finais públicos storage. Uma forma de fazê-lo é para acesso remoto no computador e, em seguida, tente escrever algo a mesma conta de armazenamento por si.

- Por fim, pode observar as falhas que estão a ser reportadas pelo agente de monitorização. O agente de monitorização escreve os seus registos `maeventtable.tsf`, que está localizado na [arquivo local para dados de diagnóstico](#log-artifacts-path). Siga as instruções no [extração registo Local](#local-log-extraction) secção para abrir este ficheiro. Em seguida, tente determinar se existem `errors` que indica falhas ao ler a escrever para o armazenamento de ficheiros locais.

### <a name="capturing-and-archiving-logs"></a>Capturar e o arquivamento registos
Se estiver a pensar sobre como contactar o suporte, a primeira coisa que poderão pedir que é recolher registos a partir do seu computador. Pode poupar tempo ao fazer esse por si. Execute o `CollectGuestLogs.exe` utilitário em [caminho do utilitário de registo coleção](#log-artifacts-path). Gera um. zip ficheiro com o Azure relevante todos os registos na mesma pasta.

## <a name="diagnostics-data-tables-not-found"></a>Não foram encontradas tabelas de dados de diagnóstico
As tabelas no armazenamento do Azure que contêm eventos ETW são denominadas utilizando o seguinte código:

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Segue-se um exemplo:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Este código gera as quatro tabelas:

| Evento | Nome da tabela |
| --- | --- |
| fornecedor = "prov1" &lt;id de evento = "1" /&gt; |WADEvent + MD5("prov1") + "1" |
| fornecedor = "prov1" &lt;id de evento = "2" eventDestination = "dest1" /&gt; |WADdest1 |
| fornecedor = "prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| fornecedor = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt; |WADdest2 |

## <a name="references"></a>Referências

### <a name="how-to-check-diagnostics-extension-configuration"></a>Como verificar a configuração de extensão de diagnóstico
É a forma mais fácil para verificar a configuração de extensão para ir para [Explorador de recursos do Azure](http://resources.azure.com), e, em seguida, vá para a máquina virtual ou a nuvem de serviço onde a extensão de diagnóstico do Azure (IaaSDiagnostics / PaaDiagnostics) é.

Em alternativa, o ambiente de trabalho remoto para o computador e o ficheiro de configuração de diagnósticos do Azure descrita na vista de olhos a [secção do caminho de artefactos de registo](#log-artifacts-path).

Em ambos os casos, procure **Microsoft.Azure.Diagnostics**e, em seguida, para o **xmlCfg** ou **WadCfg** campo. 

Se estiver a procurar numa máquina virtual e o **WadCfg** campo está presente, significa que a configuração está no formato JSON. Se o **xmlCfg** campo está presente, significa que a configuração em XML e é codificado em base64. Terá de [descodificar-](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo diagnóstico.

Para a função de serviço em nuvem, se escolher a configuração do disco, os dados são com codificação base64, por isso terá [descodificar-](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo diagnóstico.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de saída de plug-in de diagnóstico do Azure
O plug-in devolve os seguintes códigos de saída:

| Código de saída | Descrição |
| --- | --- |
| 0 |Êxito. |
| -1 |Erro genérico. |
| -2 |Não é possível carregar o ficheiro rcf.<p>Este erro interno só deverá acontecer se o iniciador de plug-in de agente do convidado é invocado manualmente incorretamente na VM. |
| -3 |Não é possível carregar o ficheiro de configuração do diagnóstico.<p><p>Solução: Causado por um ficheiro de configuração não passar na validação de esquema. A solução consiste em fornecer um ficheiro de configuração que está em conformidade com o esquema. |
| -4 |Outra instância do diagnóstico do agente de monitorização já está a utilizar o diretório de recursos locais.<p><p>Solução: Especifique um valor diferente para **LocalResourceDirectory**. |
| -6 |O iniciador de plug-in do agente convidado tentou iniciar diagnóstico com uma linha de comandos inválidos.<p><p>Este erro interno só deverá acontecer se o iniciador de plug-in de agente do convidado é invocado manualmente incorretamente na VM. |
| -10 |O plug-in de diagnóstico terminado com uma exceção não processada. |
| -11 |O agente convidado não conseguiu criar o processo responsável por iniciar e monitorizar o agente de monitorização.<p><p>Solução: Certifique-se de que estão disponíveis para iniciar novos processos de recursos de sistema suficientes.<p> |
| -101 |Argumentos inválidos ao chamar o plug-in de diagnóstico.<p><p>Este erro interno só deverá acontecer se o iniciador de plug-in de agente do convidado é invocado manualmente incorretamente na VM. |
| -102 |O processo de plug-in não é possível inicializar si próprio.<p><p>Solução: Certifique-se de que estão disponíveis para iniciar novos processos de recursos de sistema suficientes. |
| -103 |O processo de plug-in não é possível inicializar si próprio. Especificamente, é possível criar o objeto de registo.<p><p>Solução: Certifique-se de que estão disponíveis para iniciar novos processos de recursos de sistema suficientes. |
| -104 |Não é possível carregar o ficheiro de rcf fornecido pelo agente de convidados.<p><p>Este erro interno só deverá acontecer se o iniciador de plug-in de agente do convidado é invocado manualmente incorretamente na VM. |
| -105 |O plug-in do diagnóstico não é possível abrir o ficheiro de configuração do diagnóstico.<p><p>Este erro interno só deverá acontecer se o plug-in de diagnóstico é invocado manualmente incorretamente na VM. |
| -106 |Não é possível ler o ficheiro de configuração do diagnóstico.<p><p>Causado por um ficheiro de configuração não passar na validação de esquema. <br><br>Solução: Forneça um ficheiro de configuração que está em conformidade com o esquema. Para obter mais informações, consulte [como verificar a configuração de extensão de diagnóstico](#how-to-check-diagnostics-extension-configuration). |
| -107 |A passagem de diretório de recursos para o agente de monitorização é inválida.<p><p>Este erro interno só deverá acontecer se o agente de monitorização é invocado manualmente incorretamente na VM.</p> |
| -108 |Não é possível converter o ficheiro de configuração de diagnóstico num ficheiro de configuração monitorização do agente.<p><p>Este erro interno só deverá acontecer se o plug-in de diagnóstico manualmente é invocado com um ficheiro de configuração inválida. |
| -110 |Erro de configuração de diagnósticos de geral.<p><p>Este erro interno só deverá acontecer se o plug-in de diagnóstico manualmente é invocado com um ficheiro de configuração inválida. |
| -111 |Não é possível iniciar o agente de monitorização.<p><p>Solução: Certifique-se de que estão disponíveis recursos de sistema suficientes. |
| -112 |Erro geral |

### <a name="local-log-extraction"></a>Extração de registo de local
O agente de monitorização recolhe registos e artefactos como `.tsf` ficheiros. O `.tsf` ficheiro não é legível, mas pode converter para um `.csv` da seguinte forma: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Um novo ficheiro chamado `<relevantLogFile>.csv` é criado no mesmo caminho como correspondente `.tsf` ficheiro.

>[!NOTE] 
> Apenas terá de executar este utilitário contra o ficheiro de .tsf principal (por exemplo, PerformanceCountersTable.tsf). Os ficheiros associados (por exemplo, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf e assim sucessivamente) são processados automaticamente.

### <a name="more-about-missing-trace-logs"></a>Mais informações sobre a falta de registos de rastreio 

>[!NOTE]
> As seguintes informações aplicam-se principalmente a Cloud Services do Azure, exceto se tiver configurado o DiagnosticsMonitorTraceListener uma aplicação que está em execução na sua VM do IaaS. 

- Certifique-se de que o **DiagnosticMonitorTraceListener** está configurado no App. config ou Web. config.  Este é configurado por predefinição no projetos do serviço em nuvem. No entanto, alguns clientes comente-out, o que faz com que as declarações de rastreio para não ser recolhidas pelo diagnóstico. 

- Se os registos não são obter escritos do **OnStart** ou **executar** método, certifique-se de que o **DiagnosticMonitorTraceListener** está a ser o App. config.  Por predefinição está na Web. config, mas que só se aplica ao código em execução dentro de w3wp.exe. Por isso, terá de-lo no App. config para capturar rastreios que estão em execução no WaIISHost.exe.

- Certifique-se de que está a utilizar **Diagnostics.Trace.TraceXXX** em vez de **Diagnostics.Debug.WriteXXX.** As declarações de depuração são removidas por uma versão de lançamento.

- Certifique-se o código compilado, na verdade, tem de **Diagnostics.Trace linhas** (utilize Refletor, ildasm ou ILSpy para verificar). **Diagnostics.Trace** comandos são removidos do binário compilado a menos que utilize o símbolo de compilação condicional de rastreio. Este é um problema comum que ocorre quando estiver a utilizar msbuild para criar um projeto.   

## <a name="known-issues-and-mitigations"></a>Problemas conhecidos e atenuações
Aqui está uma lista dos problemas conhecidos com mitigações conhecidos:

**1. dependência de o .NET 4.5**

Extensão de diagnóstico do Windows Azure tem uma dependência de tempo de execução no framework .NET 4.5 ou posterior. No momento da escrita, todas as máquinas que sejam aprovisionadas para Cloud Services do Azure, bem como todas as imagens oficiais que se baseiam em máquinas virtuais do Azure, ter o .NET 4.5 ou posterior instalado. 

É possível encontrar uma situação em que tentar executar a extensão de diagnóstico do Windows Azure num computador que não tem o .NET 4.5 ou posterior. Isto acontece quando criar a máquina a partir de uma imagem antiga ou o instantâneo, ou quando traga o seu próprio disco personalizado.

Isto geralmente manifestos como um código de saída **255** quando em execução **DiagnosticsPluginLauncher.exe.** Falha ocorre devido à seguinte exceção não processada: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Atenuação:** instalar o .NET 4.5 ou posterior na sua máquina.

**2. Dados de contadores de desempenho estão disponíveis no armazenamento, mas não é possível mostrar no portal**

A experiência de portal nas máquinas virtuais apresenta alguns contadores de desempenho por predefinição. Se não vir os contadores de desempenho e saber o que é gerados os dados porque está disponível no armazenamento, verifique o seguinte:

- Indica se os dados no armazenamento têm nomes de contador em inglês. Se os nomes de contador não estão em inglês, o gráfico de métrico portal não conseguir reconhecê-lo.

- Se estiver a utilizar carateres universais (\*) na sua nomes de contador de desempenho, o portal não conseguir correlacionar o contador recolhido e configurado.

**Mitigação**: Alterar idioma da máquina para inglês para contas de sistema. Para tal, selecione **painel de controlo** > **região** > **administração** > **as definições de cópia**. Em seguida, desmarque **contas de sistema e de ecrã de boas-vindas** para que o idioma personalizado não foram aplicado para a conta do sistema. Certifique-se também que não utilizar carateres universais, se pretender que o portal para ser a sua experiência de consumo primário.
