---
title: "Descrição geral do Azure Application Insights para DevOps | Microsoft Docs"
description: Saiba como utilizar o Application Insights num ambiente de desenvolvimento Ops.
author: mrbullwinkle
services: application-insights
documentationcenter: 
manager: carmonm
ms.assetid: 6ccab5d4-34c4-4303-9d3b-a0f1b11e6651
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.custom: mvc
ms.topic: overview
ms.date: 06/26/2017
ms.author: mbullwin
ms.openlocfilehash: b83d08b9dac4fccc033ad4537afd343a6fbe02c2
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="overview-of-application-insights-for-devops"></a>Descrição geral do Application Insights para DevOps

Com [Application Insights](app-insights-overview.md), pode rapidamente descobrir como a aplicação está a funcionar e a ser utilizado quando se encontra em direto. Se existir um problema, permite-lhe saber sobre, ajuda a avaliar o impacto e ajuda a determinar a causa.

Segue-se uma conta de um agrupamento que desenvolvidas pela organização aplicações web:

* *"Alguns há dias, foi implementada uma correção 'secundária'. Iremos não foram executados uma passagem de teste abrangente, mas Infelizmente alguma alteração inesperada obteve intercalada na payload, fazendo com que incompatibilidade entre o front-end e back-ends. Imediatamente, desencadeou o nosso alerta surged exceções de servidor, e foram efetuadas em consideração a situação. Alguns cliques ausente no portal do Application Insights, iremos obteve informações suficientes da exceção callstacks para restringir o problema. Foi revertida imediatamente e limitada os danos. Application Insights efetuou esta parte do devops ciclo muito fácil e acionáveis."*

Neste artigo, vamos siga uma equipa na Fabrikam Bank que desenvolvidas pela organização do sistema de banca online (OBS) para ver como podem utilizar o Application Insights para responder a clientes e efetuar atualizações rapidamente.  

A equipa funciona num ciclo de DevOps representado na ilustração seguinte:

![Ciclo de DevOps](./media/app-insights-detect-triage-diagnose/00-devcycle.png)

Requisitos do feed no respetivo registo de segurança do desenvolvimento (lista de tarefas). Funcionam em suma sprints, que frequentemente disponibilizar software trabalhar - normalmente, sob a forma de melhoramentos e extensões para a aplicação existente. A aplicação em direto é atualizada frequentemente com novas funcionalidades. Enquanto estiver em direto, a equipa monitoriza-lo para utilização com a ajuda do Application Insights e desempenho. Estes dados APM feeds no respetivo registo de segurança de desenvolvimento.

A equipa utiliza o Application Insights para monitorizar a aplicação web em direto rigorosamente para:

* Desempenho. Pretendem compreender como tempos de resposta variar de acordo com a contagem de pedido quanto CPU, rede, disco e outros recursos estão a ser utilizados; o código da aplicação abrandado desempenho; e onde estão os congestionamentos.
* Falhas. Se existirem exceções ou falha pedidos, ou se um contador de desempenho ficar fora do intervalo familiarizado, a equipa precisa de saber rapidamente, para que o podem tomar medidas.
* Utilização. Sempre que for lançada uma nova funcionalidade, a equipa querem saber até que ponto é utilizado e se os utilizadores têm dificuldades com o mesmo.

Vamos concentrar-se na peça de comentários do ciclo:

![Detetar-triagem-diagnosticar](./media/app-insights-detect-triage-diagnose/01-pipe1.png)

## <a name="detect-poor-availability"></a>Detetar disponibilidade fraca
Marcela Markova é um programador sénior na equipa de OBS e demora as oportunidades potenciais na monitorização do desempenho online. A Joana configura vários [testes de disponibilidade](app-insights-monitor-web-app-availability.md):

* Um teste de URL único para a página de destino principal da aplicação, http://fabrikambank.com/onlinebanking/. A Joana define os critérios de texto e o código de HTTP 200 'Bem-vindo!'. Se este teste falhar, não há um muito a sério problema com a rede ou os servidores ou pode ser um problema de implementação. (Ou alguém mudou o bem-vindo! mensagem na página sem que o respetivo know.)
* Um mais profundo teste com vários passos, que inicia sessão e obtém uma conta atual listagem, alguns detalhes chaves em cada página de verificação. Este teste verifica se a ligação para a base de dados de contas está a funcionar. Ela utiliza um id de cliente fictícios: alguns deles são mantidas para fins de teste.

Com estes testes, configurar, Marcela é certeza de que a equipa rapidamente ficará a saber sobre qualquer interrupção.  

Falhas de apareçam como vermelhos pontos no gráfico de teste web:

![Apresentação de testes web que foram executados durante o período anterior](./media/app-insights-detect-triage-diagnose/04-webtests.png)

Mas, mais importante ainda, um alerta sobre qualquer falha é enviado por e-mail à equipa de desenvolvimento. Dessa forma, que já estão habituados acerca do mesmo antes de quase todos os clientes.

## <a name="monitor-performance"></a>Monitorizar o desempenho
Na página de descrição geral do Application Insights, está um gráfico que mostra uma variedade de [chave métricas](app-insights-web-monitor-performance.md).

![Várias métricas](./media/app-insights-detect-triage-diagnose/05-perfMetrics.png)

Tempo de carregamento de página do browser é derivado de telemetria enviada diretamente a partir de páginas web. Tempo de resposta do servidor, a contagem de pedidos do servidor e a contagem de pedidos falhados são todos medidas no servidor web e enviados para o Application Insights a partir daí.

Marcela é ligeiramente preocupado com o gráfico de resposta do servidor. Este gráfico mostra o tempo médio entre quando o servidor recebe um pedido de HTTP a partir do browser de um utilizador e, quando se retomam a resposta. Não se encontra invulgar Consulte uma variação neste gráfico, como varia de carga no sistema. Mas neste caso, parece haver que uma correlação entre pequeno aumenta a contagem de pedidos e grande aumenta o tempo de resposta. Que pode indicar que o sistema está a funcionar apenas na respetivos limites.

Abre os gráficos de servidores:

![Várias métricas](./media/app-insights-detect-triage-diagnose/06.png)

Parece não haver nenhum sinal de limitação de recursos, talvez que bumps nos gráficos de resposta do servidor são apenas um coincidence.

## <a name="set-alerts-to-meet-goals"></a>Alertas de conjunto para atingir os objetivos
Contudo, a Joana gostaria de manter a par os tempos de resposta. Se avançam demasiado elevados, que pretende saber sobre-lo imediatamente.

Para que ela define um [alerta](app-insights-metrics-explorer.md), para tempos de resposta maiores do que um limiar normal. Isto permite que a confiança de que ela vai conhecê-lo se tempos de resposta são lentos.

![Adicionar o painel de alerta](./media/app-insights-detect-triage-diagnose/07-alerts.png)

Alertas podem ser definidos numa ampla variedade de outras métricas. Por exemplo, pode receber mensagens de correio eletrónico se a contagem de exceção torna elevada ou a memória disponível for baixa, ou se houver um horário de pico nos pedidos de cliente.

## <a name="stay-informed-with-smart-detection-alerts"></a>Manter-se informado com alertas de deteção inteligente
Dia seguinte, uma mensagem de e-mail de alerta chegam do Application Insights. Mas, quando abre-lo, ela localiza não se encontra o alerta de tempo de resposta que ela definidas. Em vez disso, se lhe indica tiver ocorrido um aumento súbito repentino no pedidos falhados - ou seja, pedidos de tem devolvido códigos de falha de 500 ou mais.

Pedidos falhados são onde os utilizadores têm visto um erro - normalmente, seguindo uma excepção iniciada no código. Talvez veem uma mensagem a indicar "Lamentamos mas não foi possível atualizar os detalhes da sua neste momento." Em alternativa, na pior embarrassing absoluto, as informações de pilha aparece no ecrã do utilizador, courtesy of o servidor web.

Este alerta é um surprise, porque a última vez que ela consultou, a contagem de pedidos falhados foi encouragingly baixa. É um pequeno número de falhas esperadas num servidor ocupado.

Foi também um bits de um surprise para lhe porque ela não tem de configurar este alerta. Application Insights incluem a deteção inteligente. Automaticamente ajustável à sua aplicação padrão falha habitual e falhas "é utilizado para" a uma página específica ou em carga elevada ou ligadas a outras métricas. Gera o alarme apenas se existir um aumento súbito acima que se trata de esperar.

![o diagnóstico de e-mail](./media/app-insights-detect-triage-diagnose/21.png)

Este é um e-mail muito úteis. Apenas se não elevar um alarme. Faz muito a triagem e diagnóstico work, que é demasiado.

Mostra o número de clientes serem afetado e que as páginas web ou operações. Marcela pode decidir se ela precisa de obter a equipa de toda a funcionar neste como um exercício fire, ou se pode ser ignorado até próximas semanas.

A mensagem de e-mail também mostra que um determinado exceção ocorreu e - mesmo mais interessante - que a falha está associada a chamadas de falha para uma determinada base de dados. Isto explica por que motivo o índice de falhas apareceu subitamente, apesar de equipa do Marcela não implementada quaisquer atualizações recentemente.

Marcella faz o ping leader da equipa de base de dados com base neste e-mail. A Joana aprende que eles lançada uma correção frequente na meia hora anterior; e UPS, talvez poderá tiver ocorrido uma alteração de esquema secundárias...

Por isso, se o problema de forma a ser corrigido, antes dos registos a investigar e, em 15 minutos-resultantes. No entanto, Marcela clica na ligação para abrir o Application Insights. Abre-se diretamente para um pedido falhado e ela consegue ver a base de dados falhada chamar na lista associada de chamadas de dependência.

![pedidos falhados](./media/app-insights-detect-triage-diagnose/23.png)

## <a name="detect-exceptions"></a>Detetar exceções
Com um pouco de configuração, [exceções](app-insights-asp-net-exceptions.md) são comunicados ao Application Insights automaticamente. Estes podem também ser capturadas explicitamente através da inserção de chamadas para [trackexception ()](app-insights-api-custom-events-metrics.md#trackexception) no código:  

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }


A equipa de Fabrikam banco evoluiu e deu lugar tem a prática sempre que envia a telemetria uma exceção, a menos que exista uma recuperação óbvias.  

Na verdade, a respetiva estratégia é mesmo mais abrangente do que: enviam telemetria em cada caso em que o cliente é frustrado no que pretendiam fazer, se corresponde a uma exceção no código ou não. Por exemplo, se o sistema externo banco entre transferência devolve uma mensagem "não é possível concluir esta transação", por algum motivo operacional (sem falhas do cliente), em seguida, eles monitorizam esse evento.

    var successCode = AttemptTransfer(transferAmount, ...);
    if (successCode < 0)
    {
       var properties = new Dictionary <string, string>
            {{ "Code", returnCode, ... }};
       var measurements = new Dictionary <string, double>
         {{"Value", transferAmount}};
       telemetry.TrackEvent("transfer failed", properties, measurements);
    }

TrackException é utilizada para comunicar exceções porque envia uma cópia da pilha. TrackEvent é utilizada para outros eventos de relatório. Pode anexar quaisquer propriedades que poderão ser úteis para diagnósticos adicionais.

Exceções e eventos de apareçam no [pesquisa de diagnóstico](app-insights-diagnostic-search.md) painel. Pode explorá-los para ver as propriedades adicionais e rastreio da pilha.

![Na pesquisa de diagnóstico, utilize os filtros para mostrar a tipos específicos de dados](./media/app-insights-detect-triage-diagnose/appinsights-333facets.png)


## <a name="monitor-proactively"></a>Monitorizar proativamente
Marcela não manter-se apenas à volta a aguardar para alertas. Logo após cada reimplementação, ela demora uma vista de olhos [tempos de resposta](app-insights-web-monitor-performance.md) -a figura geral e a tabela de pedidos mais lentos, bem como contagens de exceção.  

![Gráfico de tempo de resposta e grelha dos tempos de resposta do servidor.](./media/app-insights-detect-triage-diagnose/09-dependencies.png)

Ela pode avaliar o efeito de desempenho de todas as implementações, normalmente comparar cada semana com a última. Se existir um worsening repentino, ela gera que com os programadores relevantes.

## <a name="triage-issues"></a>Problemas de triagem
Triagem - avaliar a gravidade e a extensão de um problema - é o primeiro passo após deteção. Deve chamamos a saída da equipa de à meia-noite? Ou pode ser deixado até que o intervalo da conveniente seguinte no registo de segurança? Existem algumas questões-chave no triagem.

Frequência acontece? Os gráficos no painel de descrição geral dê algum perspetiva a um problema. Por exemplo, a aplicação da Fabrikam gerados alertas de teste web quatro um noite. Observar o gráfico da manhã, a equipa foi Consulte que ocorreram, de facto, alguns pontos vermelhos, embora ainda maior parte dos testes foram verde. Desagregação no gráfico de disponibilidade, estava claro que todos estes problemas intermitentes foram da localização de um teste. Obviamente foi um problema de rede que afetam apenas uma rota e provavelmente pretende limpar próprio.  

Por outro lado, um aumento súbito enormes e estável no gráfico de vezes que contagens ou resposta de exceção obviamente é algo para panic sobre.

Tática uma triagem útil é tente-lo por si. Caso se depare com o mesmo problema, sabe é real.

Que fração de utilizadores são afetados? Para obter uma resposta aproximada, divida a taxa de falhas por número de sessões.

![Gráficos de pedidos falhados e sessões](./media/app-insights-detect-triage-diagnose/10-failureRate.png)

Quando existem lentas, compare a tabela de pedidos mais lentos responder com a frequência de utilização de cada página.

Como importante é o cenário de bloqueados? Se este for um problema funcional bloquear uma história de utilizador em particular,-importa muito? Se os clientes não é possível pagar os respetivos faturas, esta é grave; Se não podem alterar as respetivas preferências de cor do ecrã, talvez-lo pode aguardar. Detalhe de exceção ou o evento ou a identidade de páginas lento, indica onde os clientes com dificuldades.

## <a name="diagnose-issues"></a>Diagnosticar problemas
Diagnóstico não é bastante igual a depuração. Antes de iniciar o rastreio através de código, deve ter uma ideia aproximada do motivo, onde e quando o problema está a ocorrer.

**Quando se acontecer?** A vista histórico fornecida pelos gráficos de eventos e a métrica torna mais fácil correlacionar os efeitos com as causas possíveis. Se existirem picos intermitentes taxas de exceção ou tempo de resposta, observe a contagem de pedido: se este picos ao mesmo tempo, em seguida, se parece com um problema de recurso. Necessita de atribuir mais CPU ou memória? Ou é uma dependência que não é possível gerir a carga?

**É-nos?**  Se tiver uma redução repentino no desempenho de um determinado tipo de pedido - por exemplo quando o cliente pretende uma instrução de conta -, em seguida, é possível pode ser um subsistema externo em vez da aplicação web. No Explorador de métricas, selecione a taxa de falhas de dependência e taxas de duração da dependência e comparar os respetivos histories através da passado algumas horas ou dias com o problema que tiver detetado. Se é correlacionando que são as alterações, poderá ser um subsistema externo de blame.  


![Gráficos de falha de dependência e duração das chamadas para dependências](./media/app-insights-detect-triage-diagnose/11-dependencies.png)

Alguns problemas de dependência lenta são problemas de geolocalização. Fabrikam banco utiliza máquinas virtuais do Azure e detetados que que tinham inadvertidamente localizados os seus servidor web e o servidor de conta em diferentes países. Uma melhoria enormes foi colocada ao migrar um deles.

**O que podemos fazer?** Se o problema não for apresentada uma dependência de ser e, se não sempre existe, é provavelmente provocado por uma alteração recente. Perspetiva histórica fornecida pelos gráficos de métricas e eventos torna mais fácil correlacionar quaisquer alterações repentino com as implementações. Que restringe-se para baixo a pesquisa para o problema. Para identificar quais as linhas no código da aplicação abrandado o desempenho, ative o gerador de perfis do Application Insights. Consulte [aplicações web do Azure em direto de criação de perfis com o Application Insights](./app-insights-profiler.md). Depois de ativar o gerador de perfis, verá um rastreio semelhante ao seguinte. Neste exemplo, é fácil considerável que o método *GetStorageTableData* causou o problema.  

![Rastreio de gerador de perfis do App Insights](./media/app-insights-detect-triage-diagnose/AppInsightsProfiler.png)

**Que está a suceder?** Alguns problemas ocorrem raramente e podem ser difíceis de identificar testando offline. Todos os podemos fazer está a tentar capturar os erros quando ocorrer em direto. Pode inspecionar as capturas de pilha nos relatórios de exceção. Além disso, pode escrever chamadas de rastreio, com a arquitetura de registo favorita ou com tracktrace () ou trackevent ().  

Fabrikam teve um problema intermitente faça com conta entre transferências, mas apenas com determinados tipos de conta. Para compreender melhor que estava a acontecer, estes inseridos chamadas tracktrace () na chaves pontos no código, para cada chamada a expor o tipo de conta como uma propriedade. Que efetuada, fácil de filtrar apenas esses rastreios na pesquisa de diagnóstico. Ligados também os valores dos parâmetros como propriedades e as medidas para as chamadas de rastreio.

## <a name="respond-to-discovered-issues"></a>Responder a problemas detetados
Assim que tiver diagnosticado o problema, pode efetuar um plano para o corrigir. Talvez terá de reverter uma alteração recente ou, talvez pode apenas avançar e corrigir. Depois de corrigir é concluído, o Application Insights indica se é concluída com êxito.  

Equipa de desenvolvimento da Fabrikam banco adotar uma abordagem mais estruturada para medição de desempenho que são utilizaram para antes de poderem utilizaram o Application Insights.

* Configuram metas de desempenho em termos de medidas específicas na página de descrição geral do Application Insights.
* Estas medidas de desempenho de design para a aplicação a partir do início, tais como as métricas que medem o progresso do utilizador através de 'funnels'.  


## <a name="monitor-user-activity"></a>Monitorizar a atividade do utilizador
Quando o tempo de resposta é boa consistentemente e existem algumas exceções, a equipa de desenvolvimento pode passar para facilidade de utilização. Estes podem pensar sobre como melhorar a experiência dos utilizadores e como encorajar mais utilizadores para atingir os objetivos pretendidos.

Também podem ser utilizados o Application Insights para saber o que fazem os utilizadores com uma aplicação. Assim que estiver a executar facilmente, a equipa gostaria de saber as funcionalidades que são mais populares, que os utilizadores, como ou tem dificuldade em com e a frequência voltar atrás. Que irá ajudá-los a dar prioridade ao seu trabalho futuros. E se podem planear medir o sucesso de cada funcionalidade como parte do ciclo de desenvolvimento.

Por exemplo, um journey típica de utilizador através do web site tem um limpar "funil." Ver as taxas de diferentes tipos de loan muitos clientes. Um número mais pequeno avance para preencher o formulário de aspas. De quem obter uma aspas, algumas avançar em remova o loan.

![Contagens de vista de página](./media/app-insights-detect-triage-diagnose/12-funnel.png)

Ao considerar onde o número maior de clientes drop, as empresas e podem trabalhar como chegar a mais utilizadores para o fim do funil. Em alguns casos, poderá haver uma falha de experiência (UX) do utilizador - por exemplo, o botão 'Seguinte' é difícil localizar ou as instruções não são evidentes. Mais provavelmente, existem mais significativas razões comerciais para largar outs: talvez as taxas de loan são demasiado elevadas.

Seja qual for os motivos, os dados ajuda-o a equipa de descobrir que os utilizadores estão a fazer. Mais controlo chamadas podem ser inseridas para descobrir mais detalhes. Trackevent () podem ser utilizados para a contagem de ações de utilizador, de detalhe de cliques de botão individuais, para feitos significativos, tal como pagar desativar um loan.

A equipa é obter utilizada para ter informações sobre a atividade do utilizador. Nowadays, sempre que o se estruturar uma nova funcionalidade, funcionam enviados como que irão obter comentários sobre a sua utilização. Estruture a chamadas de registo para a funcionalidade de início. Podem utilizar os comentários para melhorar a funcionalidade em cada ciclo de desenvolvimento.

[Saiba mais sobre o controlo da utilização](app-insights-usage-overview.md).

## <a name="apply-the-devops-cycle"></a>Aplicar o ciclo de DevOps
Por isso que é como uma equipa utilização Application Insights, não apenas para corrigir problemas individuais, mas para melhorar o respetivo ciclo de vida de desenvolvimento. Posso hope-lhe forneceu algumas ideias sobre como Application Insights pode ajudá-lo com a gestão de desempenho de aplicações nas suas próprias aplicações.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
Pode começar a utilizar de várias formas, consoante as características da sua aplicação. Escolha o adequado para si:

* [Aplicação web ASP.NET](app-insights-asp-net.md)
* [Aplicação web em Java](app-insights-java-get-started.md)
* [Aplicação web node.js](app-insights-nodejs.md)
* Já implementada aplicações, alojadas no [IIS](app-insights-monitor-web-app-availability.md), [J2EE](app-insights-java-live.md), ou [Azure](app-insights-azure.md).
* [Páginas Web](app-insights-javascript.md) -única página aplicação ou página comum de web - utilizá-lo no seu próprio ou além disso, para qualquer uma das opções de servidor.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) para testar a aplicação da internet pública.
