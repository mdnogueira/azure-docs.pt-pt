---
title: "Smart - deteção de anomalias de desempenho | Microsoft Docs"
description: "Application Insights faz uma análise inteligente da sua telemetria de aplicação e avisa-o de potenciais problemas. Esta funcionalidade não necessita de nenhuma configuração."
services: application-insights
documentationcenter: windows
author: antonfrMSFT
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: 6886d1239fa074a6316b243e574833f5554ac298
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="smart-detection---performance-anomalies"></a>Deteção inteligente - anomalias de desempenho

[Application Insights](app-insights-overview.md) automaticamente analisa o desempenho da aplicação web e pode avisá-lo sobre potenciais problemas. Poderá ser ler este artigo porque recebeu um dos nossas notificações de deteção inteligente.

Esta funcionalidade não necessita de nenhuma configuração especial, além de configurar a sua aplicação para o Application Insights (no [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), ou [Node.js](app-insights-nodejs.md)e, em [página web código](app-insights-javascript.md)). Está ativa quando a sua aplicação gerar telemetria suficiente.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quando receber uma notificação de deteção inteligente?

Application Insights detetou que o desempenho da aplicação tem degradado das seguintes formas:

* **Degradação de tempo de resposta** -a aplicação foi iniciada mais lentamente do que utilizou para a responder a pedidos. A alteração poderá ter sido rápida, por exemplo porque não havia uma regressão na sua implementação mais recente. Ou este poderá ter sido gradual, possivelmente causado por uma fuga de memória. 
* **Degradação de duração da dependência** -a aplicação faz com que chamadas a uma API REST, a base de dados ou outras dependências. Está a responder mais lentamente do que utilizou para a dependência.
* **Padrão de desempenho lento** -a aplicação é apresentada ter um problema de desempenho que está a afetar apenas alguns pedidos. Por exemplo, as páginas são carregamento mais lentamente um tipo de browser que outros; ou estão a ser mais lentamente servidos pedidos de um servidor específico. Atualmente, nossos algoritmos observe os tempos de carregamento de página, tempos de resposta do pedido e tempos de resposta de dependência.  

Deteção inteligente requer, pelo menos, oito dias de telemetria a um volume determinem para estabelecer uma linha de base de desempenho normal. Por isso, depois da aplicação tem estado em execução durante esse período, qualquer problema significativo fará uma notificação.


## <a name="does-my-app-definitely-have-a-problem"></a>A minha aplicação sem dúvida tem um problema?

Não, uma notificação não significa que a aplicação sem dúvida tiver um problema. É simplesmente uma sugestão sobre algo que pode querer ver mais detalhadamente.

## <a name="how-do-i-fix-it"></a>Como corrigi-lo?

As notificações incluem informações de diagnóstico. Eis um exemplo:


![Eis um exemplo de deteção de degradação de tempo de resposta do servidor](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **Triagem**. A notificação mostra quantos utilizadores ou quantas operações são afetadas. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito**. É o problema que afetam apenas algumas páginas ou todo o tráfego? Está restringida para browsers específicos ou localizações? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar**. Muitas vezes, as informações de diagnóstico na notificação serão sugerimos a natureza do problema. Por exemplo, se o tempo de resposta atrasar quando a taxa de pedidos é elevada, que sugere que o servidor ou dependências são sobrecarregadas. 

    Caso contrário, abra o painel desempenho no Application Insights. Aqui, encontrará [gerador de perfis](app-insights-profiler.md) dados. Se as exceções forem emitidas, também pode experimentar o [depurador de instantâneo](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Configurar notificações por E-Mail

Inteligentes notificações de deteção são ativadas por predefinição e enviadas a quem tenham [proprietários, contribuintes e leitores de acesso para o recurso do Application Insights](app-insights-resources-roles-access-control.md). Para alterar esta definição, clique em **configurar** na notificação por e-mail ou abra as definições de deteção inteligente no Application Insights. 
  
  ![Definições de deteção inteligente](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * Pode utilizar o **anular a subscrição** ligação no e-mail de deteção inteligente para deixar de receber as notificações por e-mail.

Mensagens de correio eletrónico sobre anomalias de desempenho de deteções inteligente estão limitadas a um e-mail diário por recurso do Application Insights. Mensagem de correio eletrónico será enviada apenas se existir, pelo menos, um novo problema foi detetado nesse dia. Não irá obter repete-se de qualquer mensagem. 

## <a name="faq"></a>FAQ

* *Por isso, os funcionários da Microsoft observar os meus dados?*
  * Não. O serviço está totalmente automático. Apenas obter as notificações. Os seus dados são [privada](app-insights-data-retention-privacy.md).
* *Analisa todos os dados recolhidos pelo Application Insights?*
  * Não neste momento. Atualmente, vamos analisar o pedido de tempo de carregamento de página, tempo de resposta de dependência e tempo de resposta. Análise de métricas adicionais está no nosso registo de segurança está à procura de reencaminhar.

* Que tipos de aplicação Isto funciona?
  * Estes degradations são detetadas em qualquer aplicação que gera a telemetria adequada. Se instalou o Application Insights na aplicação web, em seguida, pedidos e dependências são automaticamente registadas. Mas nos serviços de back-end ou outras aplicações, se inserir chamadas para [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) ou [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), em seguida, deteção inteligente irá funcionar da mesma forma.

* *Pode criar regras de deteção a minha própria anomalias ou personalizar as regras existentes?*

  * Ainda não, mas pode:
    * [Configure alertas](app-insights-alerts.md) que indicam quando uma métrica atravesse um limiar.
    * [Exportar a telemetria](app-insights-export-telemetry.md) para um [base de dados](app-insights-code-sample-export-sql-stream-analytics.md) ou [ao Power BI](app-insights-export-power-bi.md), onde pode analisá-lo por si.
* *Com que frequência é efetuada a análise?*

  * Executamos a análise diariamente na telemetria do dia anterior (dia completo no fuso horário UTC).
* *Por isso, este substituir [alertas métricas](app-insights-alerts.md)?*
  * Não.  Iremos não consolidar para detetar cada comportamento que pode considerar anormal.


* *Se não efetuar nenhuma ação na resposta para uma notificação, irá receber um lembrete?*
  * Não, receberá uma mensagem sobre cada problema apenas uma vez. Se o problema persistir será atualizado na deteção inteligente feed painel.
* *Perdeu a mensagem de correio eletrónico. Onde posso encontrar as notificações no portal?*
  * Na descrição geral Application Insights da sua aplicação, clique em de **deteção inteligente** mosaico. Não existe, poderá encontrar todas as notificações de segurança anterior de 90 dias.

## <a name="how-can-i-improve-performance"></a>Como melhorar o desempenho?
Respostas lentas e falhadas são um os frustrations maiores para os utilizadores do web site, sabe da sua própria experiência. Por isso, é importante resolver os problemas.

### <a name="triage"></a>Triagem
Em primeiro lugar,-importa? Se uma página é sempre lenta carregar, mas apenas 1% de utilizadores do seu site alguma vez tiver parecê-la, talvez tiver aspetos mais importantes a pensar. Por outro lado, se apenas 1 de % de utilizadores abri-lo, mas emite sempre de exceções, que poderá ser vale a investigar.

Utilize a instrução de impacto (utilizadores afectados ou % de tráfego) como um guia Geral, mas tenha em atenção de que não se encontra o bloco de todo. Recolha outras provas para confirmar.

Considere os parâmetros do problema. Se for geografia dependentes, configurar [testes de disponibilidade](app-insights-monitor-web-app-availability.md) incluindo nessa região: pode simplesmente ter problemas de rede dessa área.

### <a name="diagnose-slow-page-loads"></a>Diagnosticar carregamentos lentos de página
Onde está o problema? É o servidor lentas a responder, é a página muito ou o browser precisa de executar uma grande quantidade de trabalho para apresentá-la?

Abra o painel de métrico de Browsers. A apresentação segmentada mostra tempo browser página carga onde vai a hora. 

* Se **tempo do pedido de envio** é elevado, o servidor está a responder lentamente, ou o pedido é uma mensagem com uma grande quantidade de dados. Observe o [métricas de desempenho](app-insights-web-monitor-performance.md#metrics) para investigar tempos de resposta.
* Configurar [controlo de dependência](app-insights-asp-net-dependencies.md) para ver se o slowness é devido a sua base de dados ou serviços externos.
* Se **receber resposta** é predominant, a página e o respetivas peças dependentes - JavaScript, CSS, imagens e assim sucessivamente (mas não no modo assíncrono carregadas dados) são longos. Configurar um [teste de disponibilidade](app-insights-monitor-web-app-availability.md)e não se esqueça de definir a opção de carregar peças dependentes. Quando obtiver alguns resultados, abra os detalhes de resultado e expandi-lo para ver os tempos de carregamento de ficheiros diferentes.
* Elevada **tempo de processamento de cliente** sugere scripts estão a ser executadas lentamente. Se o motivo não óbvios, considere adicionar algum código de temporização precisa e enviar os tempos de chamadas para trackMetric.

### <a name="improve-slow-pages"></a>Melhorar as páginas lentas
Não há uma web completa dos conselhos em melhorar a respostas do servidor e os tempos de carregamento de página, pelo que não tentaremos repeti-lo de todos os aqui. Eis algumas sugestões que provavelmente já conhecer sobre, mas apenas para ajudá-lo a pensar:

* Lento ao carregar devido à grande de ficheiros: carregar os scripts e outras partes de forma assíncrona. Utilize o agrupamento de script. Quebra a página principal widgets carregar os dados em separado. Não enviar HTML antigo simples para tabelas muito: utilizar um script para os dados de pedido como JSON ou outro formato compacto, em seguida, preencha a tabela no local. Existem estruturas excelente para o ajudar com tudo isto. (Estes também entail scripts grandes, obviamente.)
* Lenta dependências do servidor: considere as localizações geográficas dos componentes da. Por exemplo, se estiver a utilizar o Azure, certifique-se o servidor web e a base de dados na mesma região. Consultas obter mais informações que precisam de? Seria a colocação em cache ou criação de batches de ajuda?
* Problemas de capacidade: contagens de pedido e observe as métricas de servidor dos tempos de resposta. Se os tempos de resposta das horas de ponta disproportionately com picos em contagens de pedidos, é provável que os servidores são transferidos.


## <a name="server-response-time-degradation"></a>Degradação de tempo de resposta do servidor

A notificação de degradação de tempo de resposta indica:

* O tempo de resposta em comparação comparado tempo de resposta normal para esta operação.
* Número de utilizadores que é afetado.
* Tempo de resposta médio e 90th tempo de resposta de percentil para esta operação no dia da deteção e sete dias antes. 
* Contagem de pedidos esta operação no dia a deteção e sete dias antes de.
* Correlação entre degradação nesta operação e degradations no dependências relacionadas. 
* Ligações para o ajudar a diagnosticar o problema.
  * Os rastreios de gerador de perfis para o ajudar a ver em que é despendida a hora da operação (a ligação está disponível se exemplos de rastreio do gerador de perfis foram recolhidos para esta operação durante o período de deteção). 
  * Relatórios de desempenho no Explorador de métrica, onde pode segmentação e decomposição de tempo/filtros de intervalo para esta operação.
  * Procure este chamadas ver as propriedades de chamadas específico.
  * Falha de relatórios - se a contagem de > 1 significa que ocorreram falhas nesta operação que poderá têm contribuído para degradação do desempenho.

## <a name="dependency-duration-degradation"></a>Degradação de duração da dependência

Aplicações modernas mais adotar a abordagem de conceção de serviços micro, que, em muitos casos conduz a fiabilidade pesada nos serviços externos. Por exemplo, se a aplicação depende alguns plataforma de dados ou mesmo quando criar os seus próprios bot que provavelmente irá reencaminhar alguns serviços cognitivos fornecedor em para ativar o bots a interação das formas mais humanos e algumas serviço de arquivo de dados para bot solicitar as respostas fro m.  

Notificação de degradação de dependência de exemplo:

![Eis um exemplo de deteção de degradação de duração da dependência](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

Tenha em atenção que o informa sobre:

* A duração em comparação comparada tempo de resposta normal para esta operação
* Número de utilizadores que é afetado
* Duração média e 90th duração de percentil para esta dependência no dia a deteção e sete dias antes da
* Chama o número de dependência no dia a deteção e sete dias antes da
* Ligações para o ajudar a diagnosticar o problema
  * Relatórios de desempenho no Explorador de métrica para esta dependência
  * Procure esta dependência de chamadas para ver as propriedades de chamadas
  * Falha de relatórios - se a contagem de > 1 esta média que ocorreram falhou chamadas de dependência durante o período de deteção que poderão têm contribuído para degradação de duração. 
  * Abra a análise com consultas que calcular esta duração da dependência e contagem  

## <a name="smart-detection-of-slow-performing-patterns"></a>Deteção inteligente de padrões de execução lentas 

Application Insights localiza os problemas de desempenho que podem afetar apenas uma parte dos utilizadores ou afetam apenas os utilizadores em alguns casos. Por exemplo, a notificação sobre o carregamento de páginas é slowler um tipo de browser que nos outros tipos de browsers, ou se os pedidos são servidos mais lentamente a partir de um servidor específico. Também pode detetar problemas relacionados com combinações de propriedades, tais como páginas lento carrega uma área geográfica para clientes que utilizam o sistema operativo específico.  

Anomalias como estas são muito difícil detetar apenas ao inspecionar os dados, mas são mais comuns que se pensa. Muitas vezes, estes apenas superfície quando queixarem os seus clientes. Por esse tempo, é demasiado enlace tardio: os utilizadores afetados já estão mudar para seus concorrentes!

Atualmente, nossos algoritmos observe os tempos de carregamento de página, tempos de resposta do pedido no servidor e tempos de resposta de dependência.  

Não tem de definir os limiares ou configurar as regras. Machine learning e algoritmos de extração de dados são utilizados para detetar padrões anormais.

![De alerta por e-mail, clique na ligação para abrir o relatório de diagnóstico no Azure](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Quando** mostra o tempo foi detetado o problema.
* **O que** descreve:

  * O problema foi detetado;
  * As características do conjunto de eventos que encontrámos apresentado o comportamento de problema.
* A tabela compara o conjunto poorly efetuar com o comportamento de média de todos os outros eventos.

Clique nas hiperligações para abrir o Explorador de métrica e pesquisa em relatórios relevantes, filtrados no tempo e as propriedades do conjunto de desempenho lento.

Modificar o intervalo de tempo e os filtros para explorar a telemetria.

## <a name="next-steps"></a>Passos seguintes
Estas ferramentas de diagnóstico de ajudam a inspecionar a telemetria da sua aplicação:

* [Gerador de perfis](app-insights-profiler.md) 
* [Depurador de instantâneo](app-insights-snapshot-debugger.md)
* [Análise](app-insights-analytics-tour.md)
* [Diagnóstico de smart de análise](app-insights-analytics-diagnostics.md)

Deteções inteligentes são totalmente automáticas. Mas talvez pretenda configurar alguns alertas mais?

* [Alertas de métricos configurados manualmente](app-insights-alerts.md)
* [Testes web de disponibilidade](app-insights-monitor-web-app-availability.md)
