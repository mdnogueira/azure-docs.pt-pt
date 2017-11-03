---
title: "Criação de perfis de aplicações web em direto no Azure com o Application Insights | Microsoft Docs"
description: "Identifica o caminho de acesso frequente no seu código de servidor web com um gerador de perfis de requisitos de espaço insuficiente."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: f6669d90878398dcd4592df97180dcd59b146350
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="profiling-live-azure-web-apps-with-application-insights"></a>Criação de perfis de aplicações web do Azure em direto com o Application Insights

*Esta funcionalidade do Application Insights está GA para serviços de aplicações e na pré-visualização da computação.*

Saber quanto tempo é gasto a cada método na sua aplicação web em direto utilizando a ferramenta de criação de perfis de [Azure Application Insights](app-insights-overview.md). Mostra-lhe perfis de detalhado de pedidos em direto que foram processados pela sua aplicação e realça o "caminho frequente' que está a utilizar mais tempo. Seleciona automaticamente os exemplos que tenham tempos de resposta diferentes. O gerador de perfis utiliza várias técnicas para minimizar os custos gerais.

Atualmente, o gerador de perfis funciona para aplicações web ASP.NET em execução nos serviços de aplicações do Azure, no mínimo Basic escalão de preço.

<a id="installation"></a>
## <a name="enable-the-profiler"></a>Ativar o gerador de perfis

[Instale o Application Insights](app-insights-asp-net.md) no seu código. Se já estiver instalado, certifique-se de que dispõe da versão mais recente. (Para tal, clique no projeto no Explorador de soluções e escolha pacotes NuGet gerir. Selecione as atualizações e todos os pacotes de atualização.) Implemente novamente a sua aplicação.

*Com o ASP.NET Core? [Verifique aqui](#aspnetcore).*

No [https://portal.azure.com](https://portal.azure.com), abra o recurso do Application Insights para a sua aplicação web. Abra **desempenho** e clique em **ativar o gerador de perfis do Application Insights...** .

![Clique na faixa do gerador de perfis de ativação][enable-profiler-banner]

Em alternativa, pode sempre clicar **configurar** para ver o estado, ativar ou desativar o gerador de perfis.

![No painel de desempenho, clique em configurar][performance-blade]

Aplicações Web que estão configuradas com o Application Insights estão listadas no painel de configurar. Siga as instruções para instalar o agente do gerador de perfis, se necessário. Se não existe nenhuma aplicação web ainda está configurada com o Application Insights, clique em *adicionar aplicações ligadas*.

Utilize o *ativar o gerador de perfis* ou *desativar o gerador de perfis* botões no painel Configurar para controlar o gerador de perfis em todas as suas aplicações web ligado.

![Configurar o painel][linked app services]

Ao contrário de aplicações web alojadas através de planos do App Service aplicações alojadas no *computação do Azure* recursos (por ex.: Máquina Virtual, o conjunto de dimensionamento de Máquina Virtual, Service Fabric, os serviços de nuvem) não são geridos diretamente pelo Azure. Neste caso, não há nenhuma aplicação web para ligar a uma aqui e apenas terá de clicar para ativar o gerador de perfis no ecrã.

## <a name="disable-the-profiler"></a>Desativar o gerador de perfis
Para interromper ou reiniciar o gerador de perfis para uma instância de serviço de aplicações individuais, pode encontrá-la **no recurso de serviço de aplicações**, na **trabalhos Web**. Eliminá-lo, procure em **extensões**.

![Desativar o gerador de perfis para das tarefas da web][disable-profiler-webjob]

Recomendamos que tenha o gerador de perfis ativado todas as suas aplicações web para detetar problemas de desempenho logo que possível.

Se utilizar o WebDeploy para implementar as alterações à sua aplicação web, certifique-se de que exclua o **App_Data** pasta seja eliminado durante a implementação. Caso contrário, serão eliminados os ficheiros da extensão de gerador de perfis quando junto a implementar a aplicação web no Azure.

### <a name="using-profiler-with-azure-vms-and-compute-resources-preview"></a>Utilizar o gerador de perfis com as VMs do Azure e recursos de computação (pré-visualização)

Quando lhe [ativar Application Insights para serviços de aplicações do Azure em tempo de execução](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), o gerador de perfis está automaticamente disponível. (Se já ativou o Application Insights para o recurso, poderá ter de atualizar para a versão mais recente através de **configurar** assistente.)

Não existe um [versão de pré-visualização do gerador de perfis para os recursos de computação do Azure](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Limitações

A retenção de dados predefinido é de 5 dias. Máximo de 10 GB ingerido por dia.

Não há sem encargos durante o serviço do gerador de perfis. A aplicação web tem de ser alojados em, pelo menos, o escalão básico dos serviços de aplicações.

## <a name="overhead-and-sampling-algorithm"></a>Sobrecarga e o algoritmo de amostragem

O gerador de perfis executa aleatoriamente 2 minutos a cada hora em cada máquina Virtual que aloja a aplicação com ativado para capturar rastreios de gerador de perfis. Quando estiver a executar o gerador de perfis, adiciona entre 5 e 15% a sobrecarga da CPU no servidor.
Os mais servidores disponíveis para alojar a aplicação, o menor impacto tem do gerador de perfis no desempenho da aplicação global. Isto é porque o algoritmo de amostragem resulta no gerador de perfis em execução no apenas executado em % 5 de servidores em qualquer momento e mais servidores estará disponíveis para servir pedidos web e os servidores de sobrecarga do gerador de perfis de deslocamento.


## <a name="viewing-profiler-data"></a>Visualizar dados do gerador de perfis

Abra o painel de desempenho e vá para a lista de operações.




![Painel de informações de desempenho de aplicações coluna de exemplos][performance-blade-examples]

As colunas na tabela são:

* **Contagem** -o número destes pedidos no intervalo de tempo do painel.
* **Mediana** - tempo normal a aplicação demora a responder a um pedido. Metade de todas as respostas foram mais rápida do que isto.
* **percentil 95th** 95% das respostas foram mais rápida do que isto. Se desta figura é muito diferente da mediana, poderá existir um problema intermitente faça com a sua aplicação. (Ou pode ser explicado por uma funcionalidade de estrutura como colocação em cache)
* **Os rastreios de gerador de perfis** -um ícone indica que o gerador de perfis tiver capturado os rastreios de pilha para esta operação.

Clique no botão de vista para abrir o Explorador de rastreio. O Explorador de mostra alguns exemplos que foi capturada o gerador de perfis, classificados por tempo de resposta.

Se estiver a utilizar o painel de desempenho de pré-visualização, aceda a **tomar ações** secção no canto inferior direito para ver os rastreios de gerador de perfis. Clique no botão de rastreios de gerador de perfis.

![Application Insights desempenho painel pré-visualização rastreios de gerador de perfis][performance-blade-v2-examples]

Selecione uma amostra para mostrar uma divisão de nível de código de tempo que despende a executar o pedido.

![Explorador de rastreio do Application Insights][trace-explorer]

**Mostrar caminho frequente** abre o biggest folha nó ou, pelo menos, algo fechar. Na maioria dos casos, este nó será adjacente a um estrangulamento do desempenho.



* **Etiqueta**: O nome de função ou de eventos. A árvore mostra uma combinação de código e os eventos que ocorreram (por exemplo, eventos SQL e http). O evento superior representa a duração de pedido geral.
* **Decorrido**: O intervalo de tempo entre o início da operação e de fim.
* **Quando**: mostra quando o evento/função estava a ser executado em relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

Gerador de perfis de serviço de Microsoft utiliza uma combinação de método de amostragem e instrumentação para analisar o desempenho da sua aplicação.
Quando a coleção de detalhado está em curso, o gerador de perfis de serviço amostras o ponteiro de instrução de cada de CPU do computador em cada milissegundo.
Cada amostra captura a pilha de chamadas completa do thread actualmente em execução, fornecer informações úteis e detalhadas sobre o que se thread estava a fazer em ambos alta e baixa níveis de abstração. Gerador de perfis de serviço também recolhe outros eventos, tais como eventos de mudança de contexto, TPL eventos e eventos de conjunto de threads para controlar a correlação de atividade e causality.

A pilha de chamadas apresentada na vista de linha cronológica é o resultado da amostragem e instrumentação acima. Porque cada amostra captura a pilha de chamadas completa do thread, inclui código a partir do .NET framework, bem como outras estruturas que referenciar.

### <a id="jitnewobj"></a>Alocação de objeto (`clr!JIT\_New or clr!JIT\_Newarr1`)
`clr!JIT\_New and clr!JIT\_Newarr1`são funções de programa auxiliar no interior do .NET framework que aloca a memória a partir da área dinâmica para dados geridos. `clr!JIT\_New`é invocada quando existe um objeto é alocado. `clr!JIT\_Newarr1`é invocada quando é atribuída uma matriz de objetos. Estas duas funções são, normalmente, muito rápidas em devem tomar relativamente pequeno período de tempo. Se vir `clr!JIT\_New` ou `clr!JIT\_Newarr1` demorar uma quantidade substancial de tempo na sua linha cronológica, é uma indicação de que o código pode alocar muitos objetos e consumir quantidade significativa de memória.

### <a id="theprestub"></a>Carregar código (`clr!ThePreStub`)
`clr!ThePreStub`é uma função de programa auxiliar no interior do .NET framework que prepara o código para executar pela primeira vez. Isto inclui normalmente, mas não limitado a, compilação JIT (apenas no tempo). Para cada método c#, `clr!ThePreStub` deve ser invocado no máximo uma vez durante a duração de um processo.

Se vir `clr!ThePreStub` demora quantidade significativa de tempo de um pedido, indica que é o primeiro pedido que executa este método e a hora de tempo de execução do .NET framework carregar este método é significativa. Pode considerar um processo de warm-up que executa a que parte do código antes dos utilizadores aceder ao mesmo ou considere executar NGen no seu assemblagens.

### <a id="lockcontention"></a>Bloquear contenção (`clr!JITutil\_MonContention` ou `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention`ou `clr!JITutil\_MonEnterWorker` indicar o thread atual está a aguardar um bloqueio ser libertado. Isto normalmente aparece quando executar uma c# bloqueio instrução, invocar o método Monitor.Enter ou invocar um método com o atributo Methodimploptions. Contenção ocorre normalmente quando o thread A adquire um bloqueio e thread B tenta adquirir o bloqueio mesmo antes de thread A versões-lo.

### <a id="ngencold"></a>Carregar código (`[COLD]`)
Se o nome do método contém `[COLD]`, tais como `mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined`, significa que o tempo de execução do .NET framework está a executar o código que não está otimizado por <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">orientado por perfil otimização</a> pela primeira vez. Para cada método, deve agora mostrar cópias de segurança no máximo uma vez durante a duração do processo.

Se carregar código demora bastante tempo para um pedido, ele indica esse pedido é o primeiro para executar a parte do método não otimizada. Pode considerar um transfira segurança processo que executa a que parte do código antes dos utilizadores aceder ao mesmo.

### <a id="httpclientsend"></a>Enviar o pedido HTTP
Métodos, tais como `HttpClient.Send` indicar o código está à espera de um pedido HTTP concluir.

### <a id="sqlcommand"></a>Operação de base de dados
Método como SqlCommand.Execute indica o código está à espera que uma operação de base de dados seja concluída.

### <a id="await"></a>A aguardar (`AWAIT\_TIME`)
`AWAIT\_TIME`indica que o código está a aguardar a conclusão de outra tarefa. Esta situação ocorre normalmente com c# 'await' da instrução. Quando executa o código de um c# 'await', o thread unwinds e devolve o controlo para o conjunto de threads e não há nenhum thread que está bloqueado a aguardar que o 'await' Concluir. No entanto, logicamente o thread que criou o await é 'Bloqueado' a aguardar a conclusão da operação. O `AWAIT\_TIME` indica o tempo de bloqueados a aguardar a conclusão da tarefa.

### <a id="block"></a>Tempo bloqueado
`BLOCKED_TIME`indica que o código está a aguardar outro recurso estejam disponíveis, tais como a aguardar que um objeto de sincronização, aguardar um thread para estar disponível ou aguardar concluir um pedido.

### <a id="cpu"></a>Tempo de CPU
A CPU está ocupada executar as instruções.

### <a id="disk"></a>Tempo de disco
A aplicação está a efetuar operações de disco.

### <a id="network"></a>Tempo da rede
A aplicação está a efetuar operações de rede.

### <a id="when"></a>Quando coluna
Esta é uma visualização de como os exemplos INCLUSIVE recolhidos para um nó variam ao longo do tempo. O intervalo total do pedido está dividido em 32 registos de tempo e exemplos, inclusive para esse nó são acumulados para esses registos 32. Cada registo, em seguida, é representado como uma barra cuja altura representa um valor expandido. Para nós marcado `CPU_TIME` ou `BLOCKED_TIME`, ou onde existe uma relação óbvias de consumir um recurso (cpu, disco, thread), a barra representa consumir um destes recursos para o período de tempo do que o registo. Para estas métricas, pode obter superior a 100% consumir recursos de vários. Por exemplo, se em média utilizar duas CPUs, ao longo de um intervalo, em seguida, pode obter 200%.


## <a id="troubleshooting"></a>Resolução de Problemas

### <a name="too-many-active-profiling-sessions"></a>Existem demasiadas sessões ativas de criação de perfis

Atualmente, pode ativar o gerador de perfis no máximo, 4 Web Apps do Azure e as ranhuras de implementação em execução no mesmo plano de serviço. Se vir a tarefa de web de gerador de perfis demasiadas sessões ativas de criação de perfis de relatórios, terá de mover algumas aplicações Web para um plano de serviço diferente.

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>Como saber se o gerador de perfis do Application Insights está em execução?

O gerador de perfis é executado como um trabalho web contínua na aplicação Web. Pode abrir o recurso de aplicação Web no https://portal.azure.com e verificar o estado de "ApplicationInsightsProfiler" no painel de WebJobs. Se não está em execução, abra **registos** para saber mais.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Por que motivo não consigo encontrá qualquer exemplos de pilha, apesar do gerador de perfis está em execução?

Seguem-se algumas coisas, que pode verificar.

1. Certifique-se do plano do serviço Web App escalão básico e superior.
2. Certifique-se a aplicação Web com o Application Insights SDK 2.2 Beta e acima ativada.
3. Certifique-se a que sua aplicação Web tem a definição de APPINSIGHTS_INSTRUMENTATIONKEY com a mesma chave de instrumentação utilizada pelo Application Insights SDK.
4. Certifique-se a sua aplicação Web está em execução no .net Framework 4.6.
5. Se se tratar de uma aplicação ASP.NET Core, também verifique [dependências necessárias](#aspnetcore).

Depois do gerador de perfis é iniciado, não há um período curto warm-up quando o gerador de perfis recolhe ativamente várias rastreios de desempenho. Depois disso, o gerador de perfis recolhe rastreios de desempenho de dois minutos em cada hora.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Estava a utilizar o gerador de perfis de serviço de Azure. O que aconteceu ao mesmo?  

Quando ativa o gerador de perfis do Application Insights, o agente do gerador de perfis de serviço de Azure está desativada.

### <a id="double-counting"></a>Valor de duplo contando em threads paralelas

Em alguns casos a métrica do tempo total no Visualizador de pilha é maior do que a duração real do pedido.

Isto pode acontecer quando existem duas ou mais threads associadas a um pedido, a funcionar em paralelo. O tempo total de threads, em seguida, é maior do que o tempo decorrido. Em muitos casos, um thread pode aguardar no outro para concluir. O Visualizador tenta detetá-lo e omitir a espera desinteressantes, mas errs ao lado dos mostrar demasiada em vez de omitindo que podem ser informações críticas.  

Quando vir threads paralelas nos seus rastreios, terá de determinar os threads estão a aguardar para que possa determinar o caminho de crítico para o pedido. Na maioria dos casos, o thread que rapidamente entra no estado de espera é simplesmente aguardar a resposta de outros threads. Concentrar-se nos outros e ignorar o tempo nos threads de espera.

### <a id="issue-loading-trace-in-viewer"></a>Não existem dados de criação de perfis

1. Se os dados que está a tentar ver com mais de duas semanas, experimente a limitar o filtro de tempo e tente novamente.

2. Verifique se uma firewall ou proxies ter não bloqueou o acesso à https://gateway.azureserviceprofiler.net.

3. Verificar que a chave de instrumentação do Application Insights está a utilizar na sua aplicação é o mesmo que o recurso do Application Insights que ativou a criação de perfis de com. A chave está normalmente nas Applicationinsights, mas também pode ser encontrada na Web. config ou o App. config.

### <a name="error-report-in-the-profiling-viewer"></a>Relatório de erros no Visualizador de criação de perfis

Um pedido de suporte do portal de ficheiros. Inclua o ID de correlação da mensagem de erro.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erro de implementação do não vazio ' D:\\raiz\\site\\wwwroot\\App_Data\\das tarefas

Se estiver a reimplementação a aplicação web a um recurso de serviços aplicacionais com o gerador de perfis ativada, poderá encontrar o erro semelhante ao seguinte: diretório não vazio ' D:\\raiz\\site\\wwwroot\\App_Data\\das tarefas este erro irá acontecer, se executar o Web Deploy de scripts ou no VSTS implementação Pipeline.
A solução para este problema é adicionar os seguintes parâmetros de implementação adicional para a tarefa de implementação Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Isto irá eliminar a pasta utilizada pelo gerador de perfis de informações da aplicação e desbloquear o processo da implementar. Não afetará a instância do gerador de perfis em execução.


## <a name="manual-installation"></a>Instalação manual

Quando configura o gerador de perfis, as seguintes atualizações são efetuadas às definições da aplicação Web. Pode fazê-las pessoalmente manualmente se o seu ambiente necessitar, por exemplo, se a aplicação for executada no ambiente de serviço de aplicações do Azure (ASE):

1. No painel de controlo de aplicação web, abra as definições.
2. Definir ".Net Framework versão" para v4.6.
3. Como "Always On" no.
4. Adicionar definição de aplicação "__APPINSIGHTS_INSTRUMENTATIONKEY__" e defina o valor para a mesma chave de instrumentação utilizada pelo SDK.
5. Abrir as ferramentas avançadas.
6. Clique em "Ir" para abrir o Web site do Kudu.
7. No site do Kudu, selecione "As extensões de Site".
8. Instalar "__Application Insights__" da galeria.
9. Reinicie a aplicação web.

## <a id="aspnetcore"></a>Suporte de núcleo de ASP.NET

Aplicação de ASP.NET Core tem de instalar 2.1.0-beta6 de pacote Microsoft.ApplicationInsights.AspNetCore Nuget ou superior para trabalhar com o gerador de perfis. Já não suportamos as versões inferiores após 27/6/2017.

## <a name="next-steps"></a>Passos seguintes

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
