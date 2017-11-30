---
title: "Guia de resolução de problemas de depurador de instantâneos de Azure Application Insights | Microsoft Docs"
description: "Perguntas mais frequentes sobre o Azure Application Insights instantâneo depurador."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Depurador do instantâneo: Guia de resolução de problemas

Depurador de instantâneo do Application Insights permite-lhe recolher automaticamente um instantâneo de depuração de aplicações web em direto. O instantâneo mostra o estado do código de origem e as variáveis de momento, que é emitida uma exceção. Se estiver a ter dificuldade em obter o depurador de instantâneo do Application Insights cópias de segurança e em execução este artigo explica como funciona o depurador, juntamente com soluções para cenários de resolução de problemas comuns. 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Como funciona o depurador de instantâneo do Application Insights

Application Insights instantâneo depurador faz parte do pipeline de telemetria do Application Insights (uma instância de ITelemetryProcessor), o recoletor de instantâneo monitoriza as exceções no código (AppDomain.FirstChanceException) e as exceções que obter controlados pelo pipeline de telemetria de exceção do Application Insights. Depois de ter adicionado com êxito o recoletor do instantâneo ao seu projeto e detetou uma exceção na telemetria do Application Insights de pipeline, um evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e ' SnapshotCollectorEnabled' nos dados personalizada será enviado. Ao mesmo tempo, este irá iniciar um processo com o nome 'MinidumpUploader.exe', para carregar os ficheiros de dados de instantâneos recolhidos para o Application Insights.  Quando o MinidumpUploader.exe processar é iniciado, será enviado um evento personalizado com o nome 'UploaderStart'. Depois dos passos anteriores, o recoletor de instantâneos irá introduzir o respetivo comportamento normal de monitorização.

Enquanto o recoletor de instantâneo está a monitorizar a telemetria de exceção do Application Insights, utiliza parâmetros (por exemplo, ThresholdForSnapshotting MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) definidos no configuração para determinar quando deve recolher um instantâneo. Quando todas as regras são cumpridas, o recoletor irá solicitar um instantâneo para a seguinte exceção emitida no mesmo local. Em simultâneo, será enviado um evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e 'RequestSnapshots'. Uma vez que o compilador otimizará código 'Versão', variáveis locais não podem ser visíveis nos instantâneos recolhidos. O recoletor de instantâneos irá tentar deoptimize o método que acionou a exceção, quando os pedidos de instantâneos. Durante este período, será enviado um evento personalizado Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e 'ProductionBreakpointsDeOptimizeMethod' nos dados personalizados.  Quando é recolhido o instantâneo da exceção seguinte, as variáveis locais estará disponíveis. Depois do instantâneo é recolhido, este será reoptimize o código para garantir o desempenho. 

> [!NOTE]
> Deoptimization requer a extensão de site do Application Insights para ser instalada.

Quando é pedido um instantâneo de uma exceção específica, o recoletor de instantâneos irá iniciar a monitorização de exceção da sua aplicação, processamento de pipeline (AppDomain.FirstChanceException). Quando a exceção voltar a ocorrer, o recoletor irá iniciar um instantâneo (evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e 'SnapshotStart' nos dados personalizados). Em seguida, é efetuada uma cópia sombra do processo em execução (a tabela de página irá estar duplicada). Isto normalmente, irá demorar 10 a 20 milissegundos. Depois da primeira, serão enviado um evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e 'SnapshotStop' nos dados personalizados. Quando o processo forked for criado, a memória total paginada irá ser aumentada em mesma quantidade da memória paginada da sua aplicação em execução (o conjunto de trabalho será muito mais pequeno). Enquanto o processo de aplicação está a funcionar normalmente, a sombra copiada memória do processo será capturar para o disco e carregada para o Application Insights. Depois do instantâneo é carregado, será enviado um evento personalizado do Application Insights com o nome 'UploadSnapshotFinish'.

## <a name="is-the-snapshot-collector-working-properly"></a>O recoletor de instantâneo está a funcionar corretamente?

### <a name="how-to-find-snapshot-collector-logs"></a>Como localizar registos do Recoletor de instantâneo
Os registos de recoletor de instantâneo são enviados para a sua conta do Application Insight se o [pacote NuGet do Recoletor de instantâneo](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) versão 1.1.0 ou posterior está instalado. Certifique-se de que o *ProvideAnonymousTelemetry* não está definido como FALSO (o valor é verdadeiro por predefinição).

* Navegue para o recurso do Application Insights no portal do Azure.
* Clique em *pesquisa* na secção Descrição geral.
* Introduza a seguinte cadeia na caixa de pesquisa:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Nota: Altere o *intervalo de tempo* se for necessário.

![Captura de ecrã da pesquisa instantâneo Recoletor de registos](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Examine os registos de recoletor de instantâneo
Quando procurar os registos de Recoletor de instantâneo, deverá existir 'UploadSnapshotFinish' eventos no intervalo de tempo de destino. Se ainda não vir o botão 'Abrir instantâneo de depuração' para abrir o instantâneo, envie um e-mail para snapshothelp@microsoft.com com a chave de instrumentação dos Application Insights.

![Captura de ecrã de examinar registos de recoletor de instantâneo](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Não é possível localizar o instantâneo para abrir
Se os seguintes passos não ajudam a resolver o problema, envie um e-mail para snapshothelp@microsoft.com com a chave de instrumentação dos Application Insights.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Passo 1: Certificar-se da que aplicação está a enviar dados de telemetria e os dados da exceção para o Application Insights
Navegue para o recurso do Application Insights, verifique que não há dados enviados a partir da sua aplicação.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Passo 2: Certifique-se de que o recoletor de instantâneo é adicionada corretamente ao pipeline de telemetria do Application Insights da aplicação
Se as conseguir localizar os registos no passo 'Como localizar registos do Recoletor de instantâneo', o recoletor de instantâneo corretamente é adicionado ao seu projeto, pode ignorar este passo.

Se não existirem registos recoletor instantâneo, verifique o seguinte:
* Para aplicações ASP.NET clássicas, verifique a existência desta linha  *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">*  no *Applicationinsights* ficheiro.

* Para aplicações ASP.NET Core, certifique-se de que o *ITelemetryProcessorFactory* com *SnapshotCollectorTelemetryProcessor* é adicionado à *IServiceCollection* serviços .

* Verifique também se estiver a utilizar a chave de instrumentação correto na sua aplicação publicada.

* O recoletor de instantâneo não suporta várias chaves de instrumentação dentro de uma aplicação, irá enviar instantâneos para a chave de instrumentação da primeira exceção que situa-lo.

* Se definir o *InstrmentationKey* manualmente no seu código, atualize o *InstrumentationKey* elemento a partir do *Applicationinsights*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Passo 3: Certifique-se que a /carregador de mini-cópia de segurança é iniciado
Nos registos de recoletor de instantâneo, procure *UploaderStart* (tipo UploaderStart na caixa de texto de pesquisa). Deve ser um evento quando o recoletor de instantâneo monitorizados a primeira exceção. Se este evento não existir, verifique outros registos para obter mais detalhes. Uma forma de possíveis para resolver este problema está a reiniciar a aplicação.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Passo 4: Certificar-se de que recoletor de instantâneo expresso respetivo objetivo para recolher os instantâneos
Nos registos de recoletor de instantâneo, procure *RequestSnapshots* (tipo ```RequestSnapshots``` na caixa de texto de pesquisa).  Se não existir qualquer, verifique a configuração, por exemplo, *ThresholdForSnapshotting* que indica o número de um exceções específicas que podem ocorrer antes de começar a recolher instantâneos.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Passo 5: Certificar-se de que o instantâneo não está desativado devido a proteção de memória
Para proteger o desempenho da aplicação, um instantâneo só irá ser capturado quando há uma boa memória intermédia. Procure nos registos de recoletor de instantâneo, 'CannotSnapshotDueToMemoryUsage'. Dados personalizados do evento, terá um motivo de detalhado. Se estiver a executar a aplicação numa aplicação Web do Azure, a restrição poderá rigorosa. Uma vez que a aplicação Web do Azure irá reiniciar a aplicação quando forem cumpridas determinadas regras de memória. Pode tentar dimensionar o seu plano de serviço para máquinas com mais memória para resolver este problema.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Passo 6: Certifique-se tiverem sido capturados instantâneos
Nos registos de recoletor de instantâneo, procure ```RequestSnapshots```.  Se nenhum estiverem presentes, verifique a configuração, por exemplo, ```ThresholdForSnapshotting``` indica o número de um exceções específicas antes de recolher um instantâneo.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Passo 7: Certifique-se os instantâneos são carregados corretamente
Nos registos de recoletor de instantâneo, procure ```UploadSnapshotFinish```.  Se não estiver presente, envie um e-mail para snapshothelp@microsoft.com com a chave de instrumentação dos Application Insights. Se o evento existir, abra dos registos e copie o valor de 'SnapshotId' nos dados personalizados. Em seguida, procure o valor. Isto irá encontrar a exceção correspondente para o instantâneo. Clique em de exceção e depuração Abrir instantâneo. (Se não houver nenhuma exceção correspondente, a telemetria de exceção pode ser objeto de amostragem, devido a um volume elevado, tente outro snapshotId.)

![Captura de ecrã do SnapshotId localizar](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Captura de ecrã da exceção aberta](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Captura de ecrã de abrir instantâneo de depuração](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Variáveis de locais de vista de instantâneos não estão concluídas

Algumas das variáveis locais estão em falta. Se a aplicação estiver a executar o código de versão, o compilador otimizará algumas variáveis ausente. Por exemplo:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Se as maiúsculas e minúsculas diferentes, isto pode dever um erro. Envie um e-mail para snapshothelp@microsoft.com com a chave de instrumentação dos Application Insights, juntamente com o fragmento de código.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Vista de instantâneo: Não é possível obter valor do argumento ou variável local
Certifique-se a [extensão do Application Insights site](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) está instalado. Se o problema persistir, envie um e-mail para snapshothelp@microsoft.com com a chave de instrumentação dos Application Insights.
