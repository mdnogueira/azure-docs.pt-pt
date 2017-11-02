---
title: "Monitorizar uma aplicação Web ASP.NET com o Application Insights do Azure | Microsoft Docs"
description: "Monitorize o desempenho de um site sem o reimplementar. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: 869ea96072b1492db929c16cfb1e22b0c96bca7d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentar aplicações Web no tempo de execução com o Application Insights


Pode instrumentar uma aplicação Web em direto com o Azure Application Insights, sem ter de modificar ou voltar a implementar o seu código. Se as suas aplicações são alojadas por um servidor IIS no local, instale o Monitor de Estado. Se forem aplicações Web do Azure ou forem executadas numa VM do Azure, pode ativar a monitorização do Application Insights a partir do painel de controlo do Azure. (Existem artigos separados sobre instrumentação de [aplicações Web J2EE em direto](app-insights-java-live.md) e [Serviços Cloud do Azure](app-insights-cloudservices.md).) Precisará de uma subscrição do [Microsoft Azure](http://azure.com).

![gráficos de exemplo](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Pode escolher entre três formas de aplicar o Application Insights às suas aplicações Web .NET:

* **Tempo de compilação:** [adicione o Application Insights SDK][greenbrown] ao código da sua aplicação Web.
* **Tempo de execução:** instrumente a sua aplicação Web no servidor, conforme descrito abaixo, sem a reconstruir e implementar novamente o código.
* **Ambas:** crie o SDK para o seu código de aplicação Web e aplique também as extensões de tempo de execução. Tire o máximo proveito de ambas as opções.

Segue-se um resumo do que pode usufruir:

|  | Hora da compilação | Tempo de execução |
| --- | --- | --- |
| Pedidos e exceções |Sim |Sim |
| [Exceções mais detalhadas](app-insights-asp-net-exceptions.md) | |Sim |
| [Diagnóstico de dependências](app-insights-asp-net-dependencies.md) |Em .NET 4.6+, mas com menos detalhe |Sim, detalhe completo: códigos de resultado, texto do comando do SQL, verbo HTTP|
| [Contadores de desempenho do sistema](app-insights-performance-counters.md) |Sim |Sim |
| [API para telemetria personalizada][api] |Sim |Não |
| [Integração de registos de rastreio](app-insights-asp-net-trace-logs.md) |Sim |Não |
| [Dados de utilizador e vista de página](app-insights-javascript.md) |Sim |Não |
| É necessário reprogramar o código |Sim | Não |


## <a name="monitor-a-live-azure-web-app"></a>Monitorizar uma aplicação Web do Azure em direto

Se a aplicação estiver a ser executada como um serviço Web do Azure, eis como mudar de monitorização:

* Selecione o Application Insights no painel de controlo da aplicação no Azure.

    ![Configurar o Application Insights para uma aplicação Web do Azure](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Ao abrir a página de resumo do Application Insights, clique na ligação na parte inferior para abrir o recurso completo do Application Insights.

    ![Clique no Application Insights](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Monitorizar aplicações da VM e da Cloud](app-insights-azure.md).

### <a name="enable-client-side-monitoring-in-azure"></a>Ativar a monitorização do lado do cliente no Azure

Se tiver ativado o Application Insights no Azure, pode adicionar visualizações de páginas e telemetria dos utilizadores.

1. Selecione Definições > Definições da Aplicação
2.  Em Definições da Aplicação, adicione um par de chaves-valores novo: 
   
    Chave: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Valor:`true`
3. **Guarde** as definições e **reinicie** a aplicação.

O Application Insights JavaScript SDK está agora injetado em cada página Web.

## <a name="monitor-a-live-iis-web-app"></a>Monitorizar uma aplicação Web IIS em direto

Se a aplicação estiver alojada num servidor de IIS, ative o Application Insights com o Monitor de Estado.

1. No seu servidor Web de IIS, inicie sessão com credenciais de administrador.
2. Se o Monitor de Estado do Application Insights ainda não estiver instalado, transfira e execute o [Instalador do Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648) (ou execute o [Instalador de Plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx) e pesquise no Monitor de Estado do Application Insights).
3. No Monitor de Estado, selecione a aplicação Web instalada ou o Web site que pretende monitorizar. Inicie sessão com as credenciais do Azure.

    Configure o recurso onde pretende ver os resultados no portal do Application Insights. (Normalmente, é melhor criar um novo recurso. Selecione um recurso existente se já tiver [testes Web][availability] ou [monitorização de cliente][client] para esta aplicação.) 

    ![Escolha uma aplicação e um recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web é interrompido durante um curto período.

## <a name="customize-monitoring-options"></a>Personalizar opções de monitorização

Ativar o Application Insights adiciona DLLs e o Applicationinsights.config à sua aplicação Web. Pode [editar o ficheiro .config](app-insights-configuration-with-applicationinsights-config.md) para alterar algumas das opções.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Quando voltar a publicar a aplicação, volte a ativar o Application Insights

Antes de voltar a publicar a aplicação, considere [adicionar o Application Insights ao código no Visual Studio][greenbrown]. Irá obter telemetria mais detalhada e a capacidade para escrever telemetria personalizada.

Se pretender voltar a publicar sem adicionar o Application Insights ao código, tenha em atenção de que o processo de implementação poderá eliminar os DLLs e o ApplicationInsights.config do site publicado. Desta forma:

1. Se editou o ApplicationInsights.config, efetue uma cópia do mesmo antes de voltar a publicar a aplicação.
2. Voltar a publicar a aplicação.
3. Voltar a ativar a monitorização do Application Insights. (Utilize o método adequado: o painel de controlo da aplicação Web do Azure ou o Monitor de Estado num anfitrião IIS.)
4. Restabeleça as alterações efetuadas no ficheiro .config.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Resolução de problemas de configuração do runtime do Application Insights

### <a name="cant-connect-no-telemetry"></a>Não consegue ligar? Sem telemetria?

* Abra [as portas de envio necessárias](app-insights-ip-addresses.md#outgoing-ports) na firewall do seu servidor para permitir que o Monitor de Estado funcione.

* Abra o Monitor de Estado e selecione a aplicação no painel esquerdo. Verifique se existem quaisquer mensagens de diagnóstico para esta aplicação na secção "Notificações de configuração":

  ![Abra o painel Desempenho para ver o pedido, o tempo de resposta, a dependência e outros dados](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* No servidor, se vir uma mensagem sobre "permissões insuficientes", experimente o seguinte:
  * No Gestor de IIS, selecione o conjunto de aplicações, abra **Definições Avançadas**, e, em **Modelo de Processos** tenha em atenção a identidade.
  * No painel de controlo de gestão do Computador, adicione esta identidade ao grupo de Utilizadores do Monitor de Desempenho.
* Se tiver MMA/SCOM (Systems Center Operations Manager) instalado no servidor, algumas versões podem entrar em conflito. Desinstale o SCOM e o Monitor de Estado, e reinstale as versões mais recentes.
* Veja a [Resolução de Problemas][qna].

## <a name="system-requirements"></a>Requisitos de Sistema
Suporte de SO para o Monitor de Estado do Application Insights no Servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

com o .NET Framework 4.5 e o SP mais recente

No lado do cliente: Windows 7, 8, 8.1 e 10, novamente com o .NET Framework 4.5

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

    O cmdlet não afeta uma aplicação que já esteja instrumentada. É indiferente se a aplicação foi instrumentada no momento da compilação, adicionando o SDK ao código, ou no momento da execução, através de uma utilização anterior deste cmdlet.

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

## <a name="questions"></a>Perguntas sobre o Monitor de Estado

### <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Uma aplicação de ambiente de trabalho que instala no seu servidor Web do IIS. Ajuda-o a instrumentar e a configurar as aplicações Web. 

### <a name="when-do-i-use-status-monitor"></a>Quando utilizo o Monitor de Estado?

* Para instrumentar qualquer aplicação Web que esteja em execução no seu servidor IIS - mesmo se já estiver em execução.
* Para ativar telemetria adicional para aplicações Web que tenham sido [criadas com o SDK do Application Insights](app-insights-asp-net.md) no momento da compilação. 

### <a name="can-i-close-it-after-it-runs"></a>Pode fechá-lo depois de a tarefa ser executada?

Sim. Depois de ter instrumentado os sites que selecionou, pode fechá-lo.

Não recolhe telemetria por si só. Só configura as aplicações Web e define algumas permissões.

### <a name="what-does-status-monitor-do"></a>O que faz o Monitor de Estado?

Quando seleciona uma aplicação Web para o Monitor de Estado instrumentar:

* Transfere e coloca os assemblagens do Application Insights e o ficheiro .config na pasta de binários da aplicação Web.
* Modifica `web.config` para adicionar o módulo de controlo HTTP do Application Insights.
* Permite a criação de perfis CLR para recolher chamadas de dependência.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Preciso de executar o Monitor de Estado sempre que atualizar a aplicação?

Não, se voltar a implementar incrementalmente. 

Se selecionar a opção "eliminar ficheiros existentes" no processo de publicação, terá de voltar a executar o Monitor de Estado para configurar o Application Insights.

### <a name="what-telemetry-is-collected"></a>Que telemetria é recolhida?

Para aplicações que instrumenta apenas no runtime com o Monitor de Estado:

* Pedidos HTTP
* Chamadas para dependências
* Exceções
* Contadores de desempenho

Para aplicações já instrumentadas no momento da compilação:

 * Contadores de processamento.
 * Chamadas de dependência (.NET 4.5); valores de devolução em chamadas de dependência (.NET 4.6).
 * Valores de rastreio de pilha de exceção.

[Saiba mais](http://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next"></a>Passos seguintes

Ver a telemetria:

* [Explore as métricas](app-insights-metrics-explorer.md) para monitorizar o desempenho e a utilização
* [Pesquise eventos e registos][diagnostic] para diagnosticar problemas
* [Análise](app-insights-analytics.md) para obter mais informações avançadas consultas
* [Criar dashboards](app-insights-dashboards.md)

Adicionar mais telemetria:

* [Criar testes Web][availability] para se certificar de que mantém o seu site em direto.
* [Adicionar telemetria de cliente Web][usage] para ver as exceções de código da página Web e permitir a inserção de chamadas de rastreio.
* [Adicionar o Application Insights SDK ao código][greenbrown] para que possa inserir chamadas de rastreio e de registo

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-javascript.md
