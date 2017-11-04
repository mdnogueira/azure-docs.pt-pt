---
title: "Perfil de aplicações web em direto no Azure com o gerador de perfis do Application Insights | Microsoft Docs"
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
ms.openlocfilehash: e66dc2af18785c6c8e83815129c8bca5b877d25b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Perfil live aplicações web do Azure com o Application Insights

*Esta funcionalidade do Application Insights está normalmente disponível para o App Service do Azure e está em pré-visualização para os recursos de computação do Azure.*

Saber quanto tempo é gasto a cada método na sua aplicação web em direto utilizando [gerador de perfis do Application Insights](app-insights-overview.md). A ferramenta de criação de perfis do Application Insights mostra perfis de detalhado de pedidos em direto que foram processados pela sua aplicação e realça o *caminho frequente* que utiliza mais tempo. O gerador de perfis seleciona automaticamente os exemplos que tenham tempos de resposta diferentes e, em seguida, utiliza várias técnicas para minimizar os custos gerais.

Atualmente, o gerador de perfis funciona para aplicações web ASP.NET em execução no App Service do Azure,, pelo menos, a camada de serviço básico.

## <a id="installation"></a>Ativar o gerador de perfis

[Instale o Application Insights](app-insights-asp-net.md) no seu código. Se já estiver instalado, certifique-se de que dispõe da versão mais recente. Para verificar a versão mais recente, no Explorador de soluções, clique no seu projeto e, em seguida, selecione **pacotes NuGet gerir** > **atualizações** > **Atualize todos os pacotes**. Em seguida, volte a implementar a aplicação.

*Com o ASP.NET Core? Obter [obter mais informações](#aspnetcore).*

No [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para a sua aplicação web. Selecione **desempenho** > **ativar o gerador de perfis do Application Insights**.

![Selecione a faixa de gerador de perfis de ativação][enable-profiler-banner]

Em alternativa, pode selecionar **configurar** para ver o estado e ativar ou desativar o gerador de perfis.

![Em desempenho, configurar, selecione][performance-blade]

Aplicações Web que estão configuradas com o Application Insights estão listadas na **configurar**. Siga as instruções para instalar o agente do gerador de perfis, se necessário. Se nenhuma aplicação web foram configuradas com o Application Insights, selecione **adicionar aplicações ligadas**.

Para controlar o gerador de perfis em todas as suas aplicações web ligado, do **configurar** painel, selecione **ativar o gerador de perfis** ou **desativar o gerador de perfis**.

![Configurar opções do painel][linked app services]

Ao contrário das aplicações web alojadas através de planos de serviço de aplicações, as aplicações alojadas nos recursos de computação do Azure (por exemplo, Virtual Machines do Azure, conjuntos de dimensionamento de máquina virtual, do Azure Service Fabric ou Cloud Services do Azure) não são geridas diretamente pelo Azure. Neste caso, não há nenhuma aplicação web para ligar a. Em vez de ligar a uma aplicação, selecione o **ativar o gerador de perfis** botão.

## <a name="disable-the-profiler"></a>Desativar o gerador de perfis
Para interromper ou reiniciar o gerador de perfis para uma instância de serviço de aplicações individuais, em **trabalhos Web**, vá para o recurso de serviço de aplicações. Para eliminar o gerador de perfis, aceda a **extensões**.

![Desativar o gerador de perfis para das tarefas da web][disable-profiler-webjob]

Recomendamos que tenha o gerador de perfis ativado todas as suas aplicações web para detetar como antecipadamente quaisquer problemas de desempenho possível.

Se utilizar o WebDeploy para implementar as alterações à sua aplicação web, certifique-se de que excluir pasta App_Data seja eliminado durante a implementação. Caso contrário, a extensão de gerador de perfis são eliminados ficheiros da próxima vez que implementar a aplicação web no Azure.

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>Utilizar o gerador de perfis com as VMs do Azure e recursos de computação do Azure (pré-visualização)

Quando lhe [ativar Application Insights para o App Service do Azure no tempo de execução](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), o gerador de perfis do Application Insights está automaticamente disponível. Se já tiver ativado o Application Insights para o recurso, poderá ter de atualizar para a versão mais recente, utilizando o Assistente de configuração.

Obter informações um [versão de pré-visualização do gerador de perfis para os recursos de computação do Azure](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Limitações

A retenção de dados predefinido é de cinco dias. Os dados máximos ingeridos diário são de 10 GB.

Não existem sem custos de utilização do serviço de gerador de perfis. Para utilizar o serviço do gerador de perfis, a aplicação web tem de ser alojados em, pelo menos, o escalão básico do serviço de aplicações.

## <a name="overhead-and-sampling-algorithm"></a>Sobrecarga e o algoritmo de amostragem

O gerador de perfis aleatoriamente executa dois minutos a cada hora em cada máquina virtual que aloja a aplicação que tenha ativado para capturar rastreios de gerador de perfis. Quando estiver a executar o gerador de perfis, adiciona entre 5% e 15% sobrecarga da CPU no servidor.
Os mais servidores que estão disponíveis para alojar a aplicação, tem do gerador de perfis o menor impacto no desempenho da aplicação global. Isto acontece porque o algoritmo de amostragem resulta no gerador de perfis em execução em apenas 5% dos servidores em qualquer altura. Estão disponíveis para servir pedidos web e a sobrecarga de servidor causada executando o gerador de perfis de deslocamento mais servidores.


## <a name="view-profiler-data"></a>Ver dados do gerador de perfis

Vá para o **desempenho** painel e, em seguida, desloque-se para baixo para a lista de operações.

![Coluna de exemplos de painel de informações de desempenho de aplicações][performance-blade-examples]

A tabela de operações tem estas colunas:

* **CONTAGEM**: O número destes pedidos no intervalo de tempo a **CONTAGEM** painel.
* **MEDIANA**: O típico tempo demora a aplicação para responder a um pedido. Metade de todas as respostas foram mais rápida do que este valor.
* **PERCENTIL 95TH**: 95% das respostas foram mais rápida do que este valor. Se este valor for substancialmente diferente da mediana, poderá existir um problema intermitente faça com a sua aplicação. (Em alternativa, pode ser explicado por uma funcionalidade de design, como a colocação em cache.)
* **Os RASTREIOS de gerador de perfis**: um ícone indica que o gerador de perfis tiver capturado os rastreios de pilha para esta operação.

Selecione **vista** para abrir o Explorador de rastreio. O Explorador de mostra alguns exemplos que foi capturada o gerador de perfis, classificados por tempo de resposta.

Se estiver a utilizar o **desempenho de pré-visualização** painel, vá para o **tomar ações** secção da página para ver os rastreios de gerador de perfis. Selecione o **rastreios de gerador de perfis** botão.

![Rastreios de gerador de perfis de pré-visualização de painel de informações de desempenho de aplicações][performance-blade-v2-examples]

Selecione uma amostra para mostrar uma divisão de nível de código de tempo que despende a executar o pedido.

![Explorador de rastreio do Application Insights][trace-explorer]

O Explorador de rastreio mostra as seguintes informações:

* **Mostrar caminho frequente**: abre o biggest folha nó ou, pelo menos, algo fechar. Na maioria dos casos, este nó está adjacente a um estrangulamento do desempenho.
* **Etiqueta**: O nome de função ou de eventos. A árvore mostra uma combinação de código e os eventos que ocorreram (como eventos do SQL Server e HTTP). O evento superior representa a duração de pedido geral.
* **Decorrido**: O intervalo de tempo entre o início da operação e de fim da operação.
* **Quando**: quando a função ou o evento estava a ser executado em relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

Gerador de perfis de serviço da Microsoft utiliza uma combinação de métodos de amostragem e instrumentação para analisar o desempenho da sua aplicação. Quando a coleção de detalhado está em curso, o gerador de perfis de serviço amostras o ponteiro de instrução de cada de CPU do computador em cada milissegundo. Cada amostra captura a pilha de chamadas completa do thread que atualmente está em execução. Fornece informações úteis e detalhadas sobre que nesse thread estava a fazer, um nível elevado e um nível de abstração baixa. O gerador de perfis de serviço também recolhe outros eventos para controlar a correlação de atividade e causality, incluindo o contexto de mudança de eventos, eventos de tarefas paralelas biblioteca (TPL) e eventos de conjunto de threads.

A pilha de chamadas que é apresentada na vista de linha cronológica é o resultado da amostragem e instrumentação. Porque cada amostra captura a pilha de chamadas completa do thread, inclui código do Microsoft .NET Framework e a partir de outras estruturas que referenciar.

### <a id="jitnewobj"></a>Alocação de objeto CLR (Common! JIT\_novo ou clr! JIT\_Newarr1)
**CLR! JIT\_novo** e **clr! JIT\_Newarr1** são funções de programa auxiliar no .NET Framework que alocar memória a partir de uma pilha gerida. **CLR! JIT\_novo** é invocada quando existe um objeto é alocado. **CLR! JIT\_Newarr1** é invocada quando é atribuída uma matriz de objetos. Normalmente, estas duas funções são muito rápidas em tomar relativamente pequenas quantidades de tempo. Se vir **clr! JIT\_novo** ou **clr! JIT\_Newarr1** demorar uma quantidade substancial de tempo na sua linha cronológica, é uma indicação de que o código pode alocar muitos objetos e consumir quantidades significativas de memória.

### <a id="theprestub"></a>Código de carregamento de CLR (Common! ThePreStub)
**CLR! ThePreStub** é uma função de programa auxiliar no .NET Framework que prepara o código para executar pela primeira vez. Isto normalmente inclui, mas não está limitado a compilação de just-in-time (JIT). Para cada método c#, **clr! ThePreStub** deve ser invocado no máximo uma vez durante a duração de um processo.

Se **clr! ThePreStub** demora uma quantidade substancial de tempo de um pedido, isto indica que o pedido é o primeiro que executa este método. O tempo de execução .NET Framework para carregar este método é significativo. Poderá considerar a utilização de um processo de aquecimento que executa a que parte do código antes dos utilizadores aceder ao mesmo ou considere executar o gerador de imagens nativas (ngen.exe) com as assemblagens.

### <a id="lockcontention"></a>Contenção CLR (Common! JITutil\_MonContention ou clr! JITutil\_MonEnterWorker)
**CLR! JITutil\_MonContention** ou **clr! JITutil\_MonEnterWorker** indica que o thread atual está a aguardar um bloqueio ser libertado. Isto normalmente aparece quando executar um c# **bloqueio** declaração, ao invocar o **Monitor.Enter** método, ou ao invocar um método com o **Methodimploptions**atributo. Contenção normalmente ocorre quando o thread A adquire um bloqueio, e thread B tenta adquirir o bloqueio mesmo antes de thread A versões-lo.

### <a id="ngencold"></a>Carregar código ([frio])
Se o nome do método contém **[amovíveis]**, tais como **mscorlib.ni! [ COLD]System.Reflection.CustomAttribute.IsDefined**, o tempo de execução de .NET Framework está a executar o código para a primeira vez que não está otimizada por <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">orientado por perfil otimização</a>. Para cada método, deve agora mostrar cópias de segurança no máximo uma vez durante a duração do processo.

Se carregar código demora uma quantidade substancial de tempo de um pedido, isto indica que o pedido é o primeiro para executar a parte do método não otimizada. Considere a utilização de um processo de aquecimento que executa a que parte do código antes dos utilizadores aceder ao mesmo.

### <a id="httpclientsend"></a>Enviar o pedido HTTP
Métodos como **HttpClient.Send** indicar que o código está à espera que um pedido HTTP para ser concluída.

### <a id="sqlcommand"></a>Operação de base de dados
Métodos como **SqlCommand.Execute** indicar que o código está à espera que uma operação de base de dados concluir.

### <a id="await"></a>A aguardar (AWAIT\_tempo)
**AWAIT\_tempo** indica que o código está a aguardar outra tarefa concluir. Esta situação ocorre normalmente com a c# **AWAIT** instrução. Quando o código um c# **AWAIT**, o thread unwinds e devolve o controlo ao conjunto de threads e não há nenhum thread que está bloqueado a aguardar o **AWAIT** para concluir. No entanto, logicamente, o thread que foi o **AWAIT** é "está bloqueado" e está a aguardar a conclusão da operação. O **AWAIT\_tempo** instrução indica o tempo de bloqueados a aguardar a conclusão da tarefa.

### <a id="block"></a>Tempo bloqueado
**BLOCKED_TIME** indica que o código está a aguardar outro recurso estejam disponíveis. Por exemplo, poderá esperar por um objeto de sincronização, com um thread para estar disponível ou para concluir um pedido.

### <a id="cpu"></a>Tempo de CPU
A CPU está ocupada executar as instruções.

### <a id="disk"></a>Tempo de disco
A aplicação está a efetuar operações de disco.

### <a id="network"></a>Tempo da rede
A aplicação está a efetuar operações de rede.

### <a id="when"></a>Quando coluna
O **quando** coluna é uma visualização de como os exemplos INCLUSIVE recolhidos para um nó variam ao longo do tempo. O intervalo total do pedido está dividido em 32 registos de tempo. Os exemplos inclusive para esse nó são acumulados nesses registos 32. Cada registo é representado como uma barra. A altura da barra representa um valor expandido. Para nós marcado **CPU_TIME** ou **BLOCKED_TIME**, ou onde existe uma relação óbvias de consumir um recurso (CPU, disco, thread), a barra representa consumir um destes recursos para o período de hora desse registo. Para estas métricas, é possível obter um valor superior a 100% de consumir a vários recursos. Por exemplo, se utilizar em média duas CPUs durante um intervalo, obter 200%.


## <a id="troubleshooting"></a>Resolução de Problemas

### <a name="too-many-active-profiling-sessions"></a>Existem demasiadas sessões ativas de criação de perfis

Atualmente, pode ativar o gerador de perfis num máximo de quatro aplicações web do Azure e as ranhuras de implementação que estão em execução no mesmo plano de serviço. Se a tarefa do gerador de perfis web está a comunicar demasiadas sessões ativas de criação de perfis, mova algumas aplicações web para um plano de serviço diferente.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como determinar se o gerador de perfis do Application Insights está em execução?

O gerador de perfis é executado como um trabalho web contínua na aplicação web. Pode abrir o recurso de aplicação web no [portal do Azure](https://portal.azure.com). No **WebJobs** painel, verifique o estado do **ApplicationInsightsProfiler**. Se não está em execução, abra **registos** para obter mais informações.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Por que motivo não consigo encontrá qualquer exemplos de pilha, apesar do gerador de perfis está em execução?

Eis algumas coisas que pode verificar:

* Certifique-se de que o seu plano de serviço de aplicações web é o escalão básico ou superior.
* Certifique-se de que a aplicação web tem Application Insights SDK 2.2 Beta ou posterior ativado.
* Certifique-se de que a aplicação web tem o **APPINSIGHTS_INSTRUMENTATIONKEY** definição configurada com a mesma chave de instrumentação que é utilizada pelo Application Insights SDK.
* Certifique-se de que a aplicação web está em execução no .NET Framework 4.6.
* Verifique se a sua aplicação web é uma aplicação ASP.NET Core, [dependências necessárias](#aspnetcore).

Depois do gerador de perfis é iniciado, não há um período de aquecimento curto durante os quais o gerador de perfis recolhe ativamente várias rastreios de desempenho. Depois disso, o gerador de perfis recolhe rastreios de desempenho de dois minutos em cada hora.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Estava a utilizar o gerador de perfis de serviço de Azure. O que aconteceu ao mesmo?  

Quando ativa o gerador de perfis do Application Insights, o agente do gerador de perfis de serviço de Azure está desativado.

### <a id="double-counting"></a>Valor de duplo contando em threads paralelas

Em alguns casos, a métrica do tempo total no Visualizador de pilha é maior do que a duração do pedido.

Esta situação pode ocorrer quando existem duas ou mais threads associadas a um pedido e que estão a funcionar em paralelo. Nesse caso, o tempo total de threads é maior do que o tempo decorrido. Poderá ser a aguardar um thread no outro seja concluída. O Visualizador tenta detetar esta e omite a espera desinteressantes, mas errs ao lado dos mostrar demasiada em vez de omitindo que poderá ser informações críticas.  

Quando vir threads paralelas nos seus rastreios, determine que threads estão a aguardar para que possa determinar o caminho de crítico para o pedido. Na maioria dos casos, o thread que rapidamente entra no estado de espera é simplesmente aguardar a resposta de outros threads. Nos podermos concentrar nos outros threads e ignorar o tempo nos threads de espera.

### <a id="issue-loading-trace-in-viewer"></a>Não existem dados de criação de perfis

Eis algumas coisas que pode verificar:

* Se os dados que está a tentar ver com mais de duas semanas, experimente a limitar o filtro de tempo e tente novamente.
* Verifique se uma firewall ou proxies ter não bloqueou o acesso à https://gateway.azureserviceprofiler.net.
* Verifique que a chave de instrumentação do Application Insights que está a utilizar na sua aplicação é o mesmo que o recurso do Application Insights que utilizou para a criação de perfis ativado. A chave é normalmente no Applicationinsights, mas também pode ser localizada nos ficheiros Web. config ou o App. config.

### <a name="error-report-in-the-profiling-viewer"></a>Relatório de erros no Visualizador de criação de perfis

Submeta um pedido de suporte no portal. Lembre-se de que inclui o ID de correlação da mensagem de erro.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erro de implementação: diretório não vazio ' D:\\raiz\\site\\wwwroot\\App_Data\\das tarefas

Se estiver a reimplementação a aplicação web a um recurso de serviço de aplicações com o gerador de perfis ativado, poderá ver uma mensagem que se pareça com o seguinte:

Diretório não vazio ' D:\\raiz\\site\\wwwroot\\App_Data\\das tarefas

Este erro ocorre se executar o Web Deploy de scripts ou do Visual Studio Team Services implementação Pipeline. A solução é adicionar os seguintes parâmetros de implementação adicional para a tarefa de implementação Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Estes parâmetros elimine a pasta que é utilizada pelo gerador de perfis do Application Insights e desbloquear o processo da implementar. Estes não afetam a instância do gerador de perfis que está atualmente em execução.


## <a name="manual-installation"></a>Instalação manual

Quando configura o gerador de perfis, as atualizações são efetuadas às definições da aplicação web. Pode aplicar as atualizações manualmente se requer o seu ambiente-lo. Por exemplo, se a sua aplicação é executada num ambiente de serviço de aplicações para PowerApps.

1. No painel de controlo de aplicação web, abra **definições**.
2. Definir **.Net Framework versão** para **v4.6**.
3. Definir **Always On** para **no**.
4. Adicionar o __APPINSIGHTS_INSTRUMENTATIONKEY__ aplicação definição e defina o valor para a mesma chave de instrumentação que é utilizada pelo SDK.
5. Abra **avançadas ferramentas**.
6. Selecione **aceda** para abrir o Web site do Kudu.
7. No site do Kudu, selecione **Site extensões**.
8. Instalar __Application Insights__ na Galeria de aplicações Web do Azure.
9. Reinicie a aplicação web.

## <a id="aspnetcore"></a>Suporte de ASP.NET Core

Uma aplicação ASP.NET Core tem de instalar o 2.1.0-beta6 de pacote Microsoft.ApplicationInsights.AspNetCore NuGet ou posterior para trabalhar com o gerador de perfis. A partir 27 de Junho de 2017, não suportamos versões anteriores.

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
