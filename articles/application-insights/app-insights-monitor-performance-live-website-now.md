---
title: Diagnosticar problemas de desempenho num site IIS em execução | Microsoft Docs
description: Monitorize o desempenho de um site sem o reimplementar. Utilizar SDK autónomo ou do Application Insights para obter telemetria de dependência.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2016
ms.author: awills

---
# Instrumentar aplicações Web no tempo de execução com o Application Insights
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
| [Diagnóstico de dependência](app-insights-asp-net-dependencies.md) |No .NET 4.6+ |Sim |
| [Contadores de desempenho do sistema](app-insights-web-monitor-performance.md#system-performance-counters) | |Serviço na nuvem IIS ou do Azure, não é aplicação Web do Azure |
| [API para telemetria personalizada][api] |Sim | |
| [Integração de registo de rastreio](app-insights-asp-net-trace-logs.md) |Sim | |
| [Dados de utilizador e vista de página](app-insights-javascript.md) |Sim | |
| Não é necessário reconstruir código |Não | |

## Instrumente a sua aplicação Web em tempo de execução
Precisará de uma subscrição do [Microsoft Azure](http://azure.com).

### Se a sua aplicação estiver alojada no seu servidor IIS
1. No seu servidor Web de IIS, inicie sessão com credenciais de administrador.
2. Transfira e execute o [instalador do Monitor de Estado](http://go.microsoft.com/fwlink/?LinkId=506648).
3. No assistente de instalação, inicie sessão no Microsoft Azure.
   
    ![Inicie sessão no Azure com as credenciais da sua conta Microsoft](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)
   
    *Erros de ligação? Veja [Resolução de Problemas](#troubleshooting).*
4. Selecione a aplicação Web instalada ou o site que pretende monitorizar e, em seguida, configure o recurso no qual pretende ver os resultados do portal do Application Insights.
   
    ![Escolha uma aplicação e um recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)
   
    Normalmente, pode optar por configurar um novo recurso e [grupo de recursos][funções].
   
    Caso contrário, utilize um recurso existente se tiver configurado [testes Web][disponibilidade] para o seu site ou a [monitorização de cliente Web][cliente].
5. Reinicie o IIS.
   
    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)
   
    O serviço Web será interrompido durante um curto período.
6. Repare que ApplicationInsights.config foi inserido nas aplicações Web que pretende monitorizar.
   
    ![Localize o ficheiro .config, juntamente com os ficheiros de código da aplicação Web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)
   
   Existem também algumas alterações no web.config.

#### Pretende (re)configurar mais tarde?
Depois de concluir o assistente, pode reconfigurar o agente sempre que pretender. Também pode utilizá-lo se instalar o agente, mas tiverem surgido alguns problemas na configuração inicial.

![Clique no ícone do Application Insights na barra de tarefas](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

### Se a aplicação for executada como uma aplicação Web do Azure
1. No [portal do Azure](https://portal.azure.com), crie um recurso do Application Insights com o tipo ASP.NET. Este é o local onde a sua telemetria de aplicações será armazenada, analisada e apresentada.
   
    ![Adicione o Application Insights. Selecione o tipo ASP.NET.](./media/app-insights-monitor-performance-live-website-now/01-new.png)
2. Em seguida, abra o painel de controlo da sua aplicação Web do Azure, abra **Ferramentas > Monitorização do Desempenho** e adicione a extensão do Application Insights.
   
    ![Na aplicação Web, Ferramentas, Extensões, Adicionar, Application Insights](./media/app-insights-monitor-performance-live-website-now/05-extend.png)
   
    Selecione o recurso do Application Insights que acabou de criar.

### Se for um projeto dos serviços em nuvem do Azure
[Adicione scripts às funções Web e de trabalho](app-insights-cloudservices.md).

## Ver a telemetria de desempenho
Inicie sessão no [portal do Azure](https://portal.azure.com), procure o Application Insights e abra o recurso que criou.

![Selecione Procurar, Application Insights e selecione a aplicação](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Abra o painel Desempenho para ver o pedido, o tempo de resposta, a dependência e outros dados.

![Desempenho](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Clique em qualquer gráfico para abrir uma vista com mais detalhes.

Pode [editar, reorganizar, guardar](app-insights-metrics-explorer.md) e afixar gráficos ou todo o painel num [dashboard](app-insights-dashboards.md).

## Dependências
O gráfico Duração da Dependência mostra o tempo das chamadas a partir da sua aplicação para componentes externos, como bases de dados, APIs ou o armazenamento de blobs do Azure.

Para segmentar o gráfico por chamadas para diferentes dependências: edite o gráfico, ative Agrupamento e, em seguida, agrupe por Dependência, Tipo de Dependência ou Desempenho de Dependência.

![Dependência](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## Contadores de desempenho
(Não para aplicações Web do Azure.) Clique em Servidores no painel de descrição geral para ver gráficos dos contadores de desempenho do servidor, como a ocupação da CPU e a utilização de memória.

Se tiver várias instâncias de servidor, pode ser necessário editar os gráficos para agrupar por Instância de função.

![Servidores](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

Também pode [alterar o conjunto de contadores de desempenho que são enviados pelo SDK](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3). 

## Exceções
![Clique no gráfico de exceções do servidor](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Pode desagregar exceções específicas (dos últimos sete dias) e obter rastreios de pilha e dados de contexto.

## Amostragem
Se a aplicação enviar uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para o ASP.NET versão 2.0.0-beta3 ou posterior, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma percentagem da sua telemetria. [Saiba mais sobre amostragem.](app-insights-sampling.md)

## Resolução de problemas
### Erros de ligação
Tem de abrir [algumas portas de envio](app-insights-ip-addresses.md#outgoing-ports) na firewall do seu servidor para permitir que o Monitor de Estado funcione.

### Sem telemetria?
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
* Veja [Resolução de Problemas][FAQ].

## Requisitos de Sistema
Suporte de SO para o Monitor de Estado do Application Insights no Servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

com SP e .NET Framework 4.0 e 4.5 mais recentes

No lado do cliente, Windows 7, 8 e 8.1, novamente com o .NET Framework 4.0 e 4.5

O Suporte do IIS é: 7, IIS 7.5, 8, 8.5 (o IIS é necessário)

## Automatização com o PowerShell
Pode iniciar e parar a monitorização utilizando o PowerShell.

Primeiro, importe o módulo do Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Descubra que aplicações estão a ser monitorizadas:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Opcional) O nome de uma aplicação Web.
* Apresenta o Estado de Monitorização do Application Insights de cada aplicação Web (ou a aplicação com nome) neste servidor IIS.
* Devolve `ApplicationInsightsApplication` para cada aplicação:
  
  * `SdkState==EnabledAfterDeployment`: a aplicação está a ser monitorizada e foi instrumentada no momento de execução pela ferramenta Monitor de Estado ou por `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: a aplicação não foi instrumentada para o Application Insights. Ou nunca foi instrumentada ou a monitorização no momento de execução foi desativada com a ferramenta Monitor de Estado ou com o `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: a aplicação foi instrumentada adicionando o SDK ao código de origem. O SDK não pode ser atualizado ou parado.
  * `SdkVersion` mostra a versão utilizada para monitorizar esta aplicação.
  * `LatestAvailableSdkVersion`apresenta a versão atualmente disponível na galeria NuGet. Para atualizar a aplicação para esta versão, utilize `Update-ApplicationInsightsMonitoring`.

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
* `-All` Interrompe a monitorização de todas as aplicações neste servidor IIS para o qual `SdkState==EnabledAfterDeployment`
* Interrompe a monitorização de aplicações especificadas e remove a instrumentação. Só funciona para aplicações que foram instrumentadas durante a execução com a ferramenta Monitorização de Estado ou Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Devolve ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: O nome de uma aplicação Web no IIS.
* `-InstrumentationKey` (Opcional.) Utilize esta opção para alterar o recurso para o qual a telemetria da aplicação é enviada.
* Este cmdlet:
  * Atualiza a aplicação nomeada para a versão do SDK mais recentemente transferida para esta máquina. (Só funciona se `SdkState==EnabledAfterDeployment`)
  * Se fornecer uma chave de instrumentação, a aplicação nomeada é reconfigurada para enviar a telemetria para o recurso com essa chave. (Funciona se `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Transfere o SDK mais recente do Application Insights para o servidor.

## Modelo do Azure
Se a aplicação Web estiver no Azure e criar os seus recursos através de um modelo do Azure Resource Manager, pode configurar o Application Insights, adicionando o seguinte ao nó de recursos:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` - um nome para o recurso do Application Insights
* `myWebAppName` - o id da aplicação Web

## <a name="next"></a>Passos seguintes
* [Criar testes Web][disponibilidade] para certificar-se de que mantém o seu site em direto.
* [Pesquise eventos e registos][diagnóstico] para ajudar a diagnosticar problemas.
* [Adicionar telemetria de cliente Web][utilização] para ver as exceções de código de página Web e permitir a inserção de chamadas de rastreio.
* [Adicionar o Application Insights SDK ao código do serviço Web][greenbrown] para que possa inserir chamadas de rastreio e de registo no código do servidor.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[disponibilidade]: app-insights-monitor-web-app-availability.md
[cliente]: app-insights-javascript.md
[diagnóstico]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[FAQ]: app-insights-troubleshoot-faq.md
[funções]: app-insights-resources-roles-access-control.md
[utilização]: app-insights-web-track-usage.md



<!--HONumber=Sep16_HO3-->


