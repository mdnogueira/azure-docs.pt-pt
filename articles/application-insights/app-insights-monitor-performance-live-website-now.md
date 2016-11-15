---
title: "Diagnosticar problemas de desempenho num site IIS em execução | Microsoft Docs"
description: "Monitorize o desempenho de um site sem o reimplementar. Utilizar SDK autónomo ou do Application Insights para obter telemetria de dependência."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: b70c8baab03703bc00b75c2c611f69e3b71d6cd7
ms.openlocfilehash: 5159e7fc47d320d52eb7b94b5775158a3f09c769


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentar aplicações Web no tempo de execução com o Application Insights
*O Application Insights está em pré-visualização.*

Pode instrumentar uma aplicação Web em direto com o Visual Studio Application Insights, sem ter de modificar ou voltar a implementar o seu código. Nas suas aplicações, alojadas por um servidor IIS no local, pode instalar o Monitor de Estado. Se serem aplicações Web do Azure ou forem executadas numa VM do Azure, pode instalar a extensão do Application Insights. (Existem artigos separados sobre instrumentação de [aplicações Web J2EE em direto](app-insights-java-live.md) e [Serviços Cloud do Azure](app-insights-cloudservices.md).)

![gráficos de exemplo](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Pode escolher entre três formas de aplicar o Application Insights às suas aplicações Web .NET:

* **Tempo de compilação:** [adicione o Application Insights SDK][greenbrown] ao código de aplicação Web.
* **Tempo de execução:** instrumente a sua aplicação Web no servidor, conforme descrito abaixo, sem a reconstruir e implementar novamente o código.
* **Ambas:** crie o SDK para o seu código de aplicação Web e aplique também as extensões de tempo de execução. Tire o máximo proveito de ambas as opções.

Segue-se um resumo do que pode usufruir:

|  | Hora da compilação | Tempo de execução |
| --- | --- | --- |
| Pedidos e exceções |Sim |Sim |
| [Exceções mais detalhadas](app-insights-asp-net-exceptions.md) | |Sim |
| [Diagnóstico de dependências](app-insights-asp-net-dependencies.md) |No .NET 4.6+ |Sim |
| [Contadores de desempenho do sistema](app-insights-performance-counters.md) | |Serviço na nuvem IIS ou do Azure, não é aplicação Web do Azure |
| [API para telemetria personalizada][api] |Sim | |
| [Integração de registos de rastreio](app-insights-asp-net-trace-logs.md) |Sim | |
| [Dados de utilizador e vista de página](app-insights-javascript.md) |Sim | |
| Não é necessário reconstruir código |Não | |

## <a name="instrument-your-web-app-at-run-time"></a>Instrumente a sua aplicação Web em tempo de execução
Precisará de uma subscrição do [Microsoft Azure](http://azure.com).

### <a name="if-your-app-is-an-azure-web-app-or-cloud-service"></a>Se a aplicação for um Serviço em Nuvem ou uma aplicação Web do Azure
* Selecione o Application Insights no painel de controlo da aplicação no Azure.

    [Saiba mais](app-insights-azure.md).

### <a name="if-your-app-is-hosted-on-your-iis-server"></a>Se a sua aplicação estiver alojada no seu servidor IIS
1. No seu servidor Web de IIS, inicie sessão com credenciais de administrador.
2. Transfira e execute o [instalador do Monitor de Estado](http://go.microsoft.com/fwlink/?LinkId=506648).
3. No assistente de instalação, inicie sessão no Microsoft Azure.

    ![Inicie sessão no Azure com as credenciais da sua conta Microsoft](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Erros de ligação? Veja [Resolução de problemas](#troubleshooting).*
4. Selecione a aplicação Web instalada ou o site que pretende monitorizar e, em seguida, configure o recurso no qual pretende ver os resultados do portal do Application Insights.

    ![Escolha uma aplicação e um recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalmente, pode optar por configurar um novo recurso e [grupo de recursos][roles].

    Caso contrário, utilize um recurso existente se tiver configurado [testes Web][availability] para o seu site ou a [monitorização de cliente Web][client].
5. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web será interrompido durante um curto período.
6. Repare que ApplicationInsights.config foi inserido nas aplicações Web que pretende monitorizar.

    ![Localize o ficheiro .config, juntamente com os ficheiros de código da aplicação Web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Existem também algumas alterações no web.config.

#### <a name="want-to-reconfigure-later"></a>Pretende (re)configurar mais tarde?
Depois de concluir o assistente, pode reconfigurar o agente sempre que pretender. Também pode utilizá-lo se instalar o agente, mas tiverem surgido alguns problemas na configuração inicial.

![Clique no ícone do Application Insights na barra de tarefas](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## <a name="view-performance-telemetry"></a>Ver a telemetria de desempenho
Inicie sessão no [portal do Azure](https://portal.azure.com), procure o Application Insights e abra o recurso que criou.

![Selecione Procurar, Application Insights e selecione a aplicação](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Abra o painel Desempenho para ver o pedido, o tempo de resposta, a dependência e outros dados.

![Desempenho](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Clique em qualquer gráfico para abrir uma vista com mais detalhes.

Pode [editar, reorganizar, guardar](app-insights-metrics-explorer.md) e afixar gráficos ou todo o painel num [dashboard](app-insights-dashboards.md).

## <a name="dependencies"></a>Dependências
O gráfico Duração da Dependência mostra o tempo das chamadas a partir da sua aplicação para componentes externos, como bases de dados, APIs ou o armazenamento de blobs do Azure.

Para segmentar o gráfico por chamadas para diferentes dependências: edite o gráfico, ative Agrupamento e, em seguida, agrupe por Dependência, Tipo de Dependência ou Desempenho de Dependência.

![Dependência](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## <a name="performance-counters"></a>Contadores de desempenho
(Não para aplicações Web do Azure.) Clique em Servidores no painel de descrição geral para ver gráficos dos contadores de desempenho do servidor, como a ocupação da CPU e a utilização de memória.

Se tiver várias instâncias de servidor, pode ser necessário editar os gráficos para agrupar por Instância de função.

![Servidores](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

Também pode alterar o conjunto de contadores de desempenho que são enviados pelo SDK. 

## <a name="exceptions"></a>Exceções
![Clique no gráfico de exceções do servidor](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Pode desagregar exceções específicas (dos últimos sete dias) e obter rastreios de pilha e dados de contexto.

## <a name="sampling"></a>Amostragem
Se a aplicação enviar uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para o ASP.NET versão 2.0.0-beta3 ou posterior, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma percentagem da sua telemetria. [Saiba mais sobre a amostragem.](app-insights-sampling.md)

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="connection-errors"></a>Erros de ligação
Tem de abrir [algumas portas de envio](app-insights-ip-addresses.md#outgoing-ports) na firewall do seu servidor para permitir que o Monitor de Estado funcione.

### <a name="no-telemetry"></a>Sem telemetria?
* Utilize o seu site para gerar alguns dados.
* Aguarde alguns minutos para permitir que os dados cheguem e clique em **Atualizar**.
* Abra Pesquisa de Diagnóstico (o mosaico de Pesquisa) para ver eventos individuais. Muitas vezes, os eventos estão visíveis na Pesquisa de Diagnóstico antes de dados agregados aparecerem nos gráficos.
* Abra o Monitor de Estado e selecione a aplicação no painel esquerdo. Verifique se existem quaisquer mensagens de diagnóstico para esta aplicação na secção "Notificações de configuração":

  ![Abra o painel Desempenho para ver o pedido, o tempo de resposta, a dependência e outros dados](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Certifique-se de que a firewall do servidor permite tráfego de saída nas portas indicadas acima.
* No servidor, se vir uma mensagem sobre "permissões insuficientes", experimente o seguinte:
  * No Gestor de IIS, selecione o conjunto de aplicações, abra **Definições Avançadas**, e, em **Modelo de Processos** tenha em atenção a identidade.
  * No painel de controlo de gestão do Computador, adicione esta identidade ao grupo de Utilizadores do Monitor de Desempenho.
* Se tiver MMA/SCOM instalado no servidor, algumas versões podem entrar em conflito. Desinstale o SCOM e o Monitor de Estado, e reinstale as versões mais recentes.
* Veja [Resolução de Problemas][qna].

## <a name="system-requirements"></a>Requisitos de Sistema
Suporte de SO para o Monitor de Estado do Application Insights no Servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

com SP e .NET Framework 4.0 e 4.5 mais recentes

No lado do cliente, Windows 7, 8 e 8.1, novamente com o .NET Framework 4.0 e 4.5

O Suporte do IIS é: 7, IIS 7.5, 8, 8.5 (o IIS é necessário)

## <a name="automation-with-powershell"></a>Automatização com o PowerShell
Pode iniciar e parar a monitorização com o PowerShell no servidor de IIS.

Primeiro, importe o módulo do Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Descubra que aplicações estão a ser monitorizadas:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Opcional) O nome de uma aplicação Web.
* Apresenta o Estado de Monitorização do Application Insights de cada aplicação Web (ou a aplicação com nome) neste servidor IIS.
* Devolve `ApplicationInsightsApplication` para cada aplicação:

  * `SdkState==EnabledAfterDeployment`: a aplicação está a ser monitorizada e foi instrumentada no momento de execução pela ferramenta Monitor de Estado ou por `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: a aplicação não foi instrumentada para o Application Insights. Ou nunca foi instrumentada ou a monitorização no momento de execução foi desativada com a ferramenta Monitor de Estado ou com o `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: a aplicação foi instrumentada ao adicionar o SDK ao código de origem. O SDK não pode ser atualizado ou parado.
  * `SdkVersion` mostra a versão utilizada para monitorizar esta aplicação.
  * `LatestAvailableSdkVersion` mostra a versão atualmente disponível na galeria NuGet. Para atualizar a aplicação para esta versão, utilize `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` O nome da aplicação no IIS
* `-InstrumentationKey` A ikey do recurso Application Insights onde pretende que os resultados sejam apresentados.
* Este cmdlet afeta apenas as aplicações que ainda não foram instrumentadas - ou seja, SdkState==NotInstrumented.

    O cmdlet não afeta uma aplicação que já está instrumentada, quer no momento da compilação, adicionando o SDK ao código, ou no momento da execução, através de uma utilização anterior deste cmdlet.

    A versão SDK utilizada para instrumentar a aplicação é a versão mais recentemente transferida para este servidor.

    Para transferir a versão mais recente, utilize Update-ApplicationInsightsVersion.
* Devolve `ApplicationInsightsApplication` com êxito. Se falhar, regista um rastreio em stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` O nome de uma aplicação no IIS
* `-All` Para a monitorização de todas as aplicações neste servidor IIS para o qual `SdkState==EnabledAfterDeployment`
* Interrompe a monitorização de aplicações especificadas e remove a instrumentação. Só funciona para aplicações que foram instrumentadas durante a execução com a ferramenta Monitorização de Estado ou Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Devolve ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: o nome de uma aplicação Web no IIS.
* `-InstrumentationKey` (Opcional.) Utilize esta opção para alterar o recurso para o qual a telemetria da aplicação é enviada.
* Este cmdlet:
  * Atualiza a aplicação nomeada para a versão do SDK mais recentemente transferida para esta máquina. (Só funciona se `SdkState==EnabledAfterDeployment`)
  * Se fornecer uma chave de instrumentação, a aplicação nomeada é reconfigurada para enviar a telemetria para o recurso com essa chave. (Funciona se `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Transfere o SDK mais recente do Application Insights para o servidor.

## <a name="a-namenextanext-steps"></a><a name="next"></a>Passos seguintes
* [Criar testes Web][availability] para certificar-se de que mantém o seu site em direto.
* [Pesquise eventos e registos][diagnostic] para ajudar a diagnosticar problemas.
* [Adicionar telemetria de cliente Web][usage] para ver as exceções de código de página Web e permitir a inserção de chamadas de rastreio.
* [Adicionar o Application Insights SDK ao código do serviço Web][greenbrown] para que possa inserir chamadas de rastreio e de registo no código do servidor.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=Nov16_HO2-->


