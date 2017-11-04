---
title: "Desempenho da aplicação de Web monitorização - Azure Application Insights | Microsoft Docs"
description: Como o Application Insights ajusta o ciclo de devOps
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 24e249bb515c509f2fba1f943ac5e23a1ea9965e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diagnósticos avançados de aplicações Web e serviços com o Application Insights
## <a name="why-do-i-need-application-insights"></a>Por que motivo precisa de Application Insights?
Application Insights monitoriza a sua aplicação web em execução. Informa-sobre problemas de desempenho e falhas e ajuda a analisar a forma como os clientes utilizam a sua aplicação. Isto funciona para aplicações em execução em muitas plataformas (ASP.NET, J2EE, Node.js,...) e é alojado ou na nuvem ou no local. 

![Aspetos a complexidade da entrega de aplicações web](./media/app-insights-devops/010.png)

É essencial para monitorizar uma aplicação moderna enquanto estiver em execução. Mais importante ainda, que pretende detetar falhas antes de fazer a maioria dos seus clientes. Também pretende detetar e corrigir problemas de desempenho que, enquanto não catastrófica, talvez retardam as operações ou fazer com que algumas inconveniência aos seus utilizadores. E quando o sistema está a efetuar para sua satisfação, se pretender saber que os utilizadores estão a fazer com o mesmo: são que utilizar a funcionalidade mais recente? São podem ter êxito com o mesmo?

Aplicações modernas web são desenvolvidas num ciclo de entrega contínua: uma nova funcionalidade ou melhoramento; da versão observar o quão bem funciona para os utilizadores; Planear o incremento de desenvolvimento com base nos conhecimentos seguinte. Uma parte de chave deste ciclo é a fase de observação. Application Insights fornece as ferramentas para monitorizar uma aplicação web para o desempenho e utilização.

O aspeto mais importante deste processo é de diagnóstico e de diagnóstico. Se a aplicação falhar, em seguida, negócio está a ser perdido. A função de prime de uma estrutura de monitorização é, por conseguinte, para detetar falhas de forma fiável, notificá-lo imediatamente e apresentar-lhe as informações necessárias para diagnosticar o problema. Isto é exatamente o que faz o Application Insights.

### <a name="where-do-bugs-come-from"></a>Onde erros provenientes?
Falhas nos sistemas de web normalmente resultam de problemas de configuração ou incorretas interações entre os diversos componentes. A primeira tarefa quando tackling um incidente de site em direto é, por conseguinte identificar o locus do problema: o componente ou relação é a causa?

Alguns dos EUA, aqueles com hair cinzento, podem Lembre-se um era mais simples em que um programa de computador foi executada no computador. Os programadores seriam testar cuidadosamente antes de envio-los; e ter fornecidos, seria raramente Consulte ou pensá-lo novamente. Os utilizadores teriam onde colocar a cópia de segurança com os erros residuais durante muitos anos. 

Coisas são, por isso, muito diferentes agora. A aplicação tiver um plethora de diferentes dispositivos a executar, e pode ser difícil garantir o mesmo comportamento exato em cada um. Alojamento de aplicações na nuvem significa erros podem ser corrigidos rápida, mas também significa concorrência contínua e as expectativas das novas funcionalidades em intervalos frequentes. 

Estas condições, a única forma de manter um controlo sólido na contagem de erros é unidade automatizada de teste. Seriam impossível de novamente testar manualmente tudo em cada entrega. Teste de unidade faz agora parte commonplace do processo de compilação. Ajuda das ferramentas, tais como a nuvem de teste do Xamarin fornecendo IU automatizada testes em múltiplas versões do browser. Estes testes regimes permitem-nos hope que a taxa de erros encontrados no interior de uma aplicação pode ser mantida num mínimo.

As aplicações web típico tem muitos componentes em direto. Para além do cliente (uma aplicação de browser ou dispositivo) e o servidor web, é provável que seja o processamento de back-end substanciais. Talvez o back-end é um pipeline de componentes, ou uma coleção looser de colaborar partes. E muitos deles não será no controlo – estiverem serviços externos no qual dependem.

Em configurações de como estas, pode ser difícil e uneconomical testar ou previr, cada modo falha possíveis, exceto a do sistema em direto. 

### <a name="questions-"></a>Perguntas...
Algumas questões Pedimos-lhe quando iremos estiver a desenvolver um sistema web:

* A minha aplicação falhar 
* O que aconteceu exatamente? --Lo se não tiver um pedido, pretender saber como obteve não existe. É necessário efetuar um rastreio de eventos...
* É suficientemente rápida a minha aplicação? Quanto tempo demora para responder a pedidos típicos?
* Pode servidor face à carga? Quando aumenta a taxa de pedidos, o tempo de resposta manter gradual?
* Grau são os meus dependências - as APIs REST, as bases de dados e outros componentes que chama a minha aplicação. Em especial, se o sistema estiver lento, é meu componente ou estou obter respostas lentas do alguém?
* É a minha aplicação ou reduzir verticalmente? Pode, ser visto a partir de todo o mundo? Informar-me se deixa...
* O que é a causa raiz? Foi a falha na minha componente ou uma dependência? É um problema de comunicação?
* Número de utilizadores que é afetado? Se tiver mais do que um problema ao resolver o problema, que é mais importante?

## <a name="what-is-application-insights"></a>O que é o Application Insights?
![Fluxo de trabalho básico do Application Insights](./media/app-insights-devops/020.png)

1. Application Insights instruments a sua aplicação e envia a telemetria sobre o assunto, enquanto a aplicação está em execução. Pode criar o Application Insights SDK na aplicação, ou pode aplicar instrumentação no tempo de execução. O método anteriores é mais flexível e como pode adicionar a sua própria telemetria para os módulos normais.
2. A telemetria é enviada para o portal do Application Insights, onde são armazenado e processado. (Embora o Application Insights está alojado no Microsoft Azure, este pode monitorizar todas as aplicações web - apps não apenas do Azure.)
3. A telemetria é apresentada no formato de gráficos e tabelas de eventos.

Existem dois principais tipos de telemetria: as instâncias de agregados e não processadas. 

* Dados de instância incluem, por exemplo, um relatório de um pedido que foi recebido pela sua aplicação web. Pode encontrar para e Inspecione os detalhes de um pedido com a ferramenta de pesquisa no portal do Application Insights. A instância inclui dados, tais como a sua aplicação de quanto tempo demorou a responder a pedido, bem como o URL pedido, aproximada da localização do cliente e outros dados.
* Dados agregados incluem contagens de eventos por hora de unidade, para que pode comparar a taxa de pedidos com tempos de resposta. Também inclui médias de métricas, tais como os tempos de resposta do pedido.

As principais categorias de dados são:

* Pedidos para a sua aplicação (normalmente, os pedidos HTTP), com dados no URL, tempo de resposta e êxito ou falha.
* Dependências - REST e SQL chamadas efetuadas pela sua aplicação, também com URI, tempos de resposta e o sucesso
* Exceções, incluindo os rastreios de pilha.
* Página Ver dados, que vêm da browsers dos utilizadores.
* Métricas, tais como os contadores de desempenho, bem como as métricas de escrita por si. 
* Eventos personalizados que pode utilizar para controlar eventos empresariais
* Rastreios de registo utilizados para depuração.

## <a name="case-study-real-madrid-fc"></a>Caso prático: Real Madrid F.C.
O serviço web de [Real Club de futebol Madrid](http://www.realmadrid.com/) serve ventoinhas de cerca de 450 milhões em todo o mundo. Ventoinhas aceder através de browsers e aplicações móveis do Club. Ventoinhas podem não apenas permissões de livro, mas também aceder às aplicações de clips de vídeo e informações nos resultados, jogadores e jogos futuros. Pode procurar com filtros como classificadas de números de objetivos. Também existem ligações para de redes sociais. A experiência de utilizador é altamente personalizada e foi concebida como uma comunicação bidirecional para o envolvimento ventoinhas.

A solução [é um sistema de serviços e aplicações no Microsoft Azure](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx). A escalabilidade é um requisito-chave: o tráfego é a variável e pode alcançar volumes muito elevadas durante e à volta de correspondências.

Madrid para Real, é vital para monitorizar o desempenho do sistema. As informações de aplicação do Azure fornece uma vista abrangente entre o sistema, garantir um nível elevado e fiável de serviço. 

O Club também obtém aprofundada compreensão das respetivas ventoinhas: onde estiverem (apenas 3% estiverem em Espanha), que interesse têm no jogadores, históricos resultados e jogos futuros, e como estes respondem para fazer corresponder os resultados.

A maioria destes dados de telemetria são recolhidos automaticamente com nenhum código adicionado, o qual simplificada a solução e diminui a complexidade operacional.  Madrid para Real, Application Insights lida com pontos de telemetria de mil milhões de 3.8 cada mês.

Real Madrid utiliza o módulo de Power BI para ver os respetivos telemetria.

![O Power view de BI de telemetria do Application Insights](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Deteção inteligente
[O diagnóstico](app-insights-proactive-diagnostics.md) é uma funcionalidade recente. Sem qualquer configuração especial por si, o Application Insights automaticamente Deteta e alerta-o sobre aumenta invulgar na taxas de falha na sua aplicação. É inteligente ignorar um fundo de falhas ocasionais e também aumenta que são simplesmente proporcional a um aumento súbito do pedidos. Por isso, por exemplo, se ocorrer uma falha dos serviços que dependam ou se criar a nova que acabou de ser implementada não está a funcionar, por isso, também, em seguida, irá saber acerca do mesmo observar o seu e-mail assim que. (E não existirem webhooks, de modo a que pode acionar outras aplicações.)

Outro aspeto desta funcionalidade efetua uma análise aprofundada diária da sua telemetria, à procura de atividade invulgares padrões de desempenho que são difíceis de detetar. Por exemplo, pode encontrar um desempenho lento associado uma área geográfica específica ou com uma versão de browser específico.

Em ambos os casos, o alerta não só indica os sintomas é detetado, mas também oferece a dados que precisa para ajudar a diagnosticar o problema, tais como os relatórios de exceção relevante.

![E-mail a partir de diagnóstico](./media/app-insights-devops/030.png)

Samtec cliente consiga aceder tal: "durante uma funcionalidade recente cutover, encontrámos uma base de dados em ampliada atingir os limites de recursos e fazendo com que os tempos limite. Alertas de deteção proativa veio literalmente como anunciados iremos foram a triagem o problema, muito quase em tempo real. Este alerta conjugada com os alertas de plataforma do Azure ajudou-na quase instantaneamente corrigir o problema. Total de tempo de inatividade < 10 minutos."

## <a name="live-metrics-stream"></a>Transmissão de métricas em direto
Implementação de compilação mais recente pode ser uma experiência anxious. Se existirem quaisquer problemas, pretender saber sobre os mesmos imediato, para que pode criar uma, se necessário. Transmissão de métricas em direto dá-lhe as métricas-chave com uma latência de cerca de um segundo.

![Métricas em direto](./media/app-insights-devops/040.png)

E permite-lhe verificar imediatamente uma amostra de falhas ou as exceções.

![Eventos de falha em direto](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Mapeamento de Aplicações
O mapeamento de aplicações Deteta automaticamente a topologia de aplicação, disposição das informações de desempenho por cima do mesmo, a que possa identificar facilmente estrangulamentos de desempenho e fluxos problemáticos em todo o ambiente distribuído. Permite-lhe detetar dependências de aplicações nos serviços do Azure. Pode triagem o problema por compreender se relacionados com o código ou dependência relacionadas e de um único local Consulte diagnostics relacionados experiência. Por exemplo, a sua aplicação poderá falhar devido a degradação do desempenho da camada SQL. Com o mapeamento de aplicações, pode vê-lo imediatamente e explorar o Assistente do índice de SQL ou a experiência de informações acerca das consultas.

![Mapeamento de Aplicações](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
Com [análise](app-insights-analytics.md), pode escrever consultas arbitrários num idioma poderoso como o SQL Server.  Diagnosticar em toda a pilha de aplicação completa torna-se mais fácil como várias perspetivas entrar em contato e pode colocar as perguntas certas correlacionar o desempenho do serviço com métricas de negócio e experiência do cliente. 

Pode consultar todas as suas instâncias de telemetria e métricos dados não processados armazenados no portal. O idioma inclui filtro, associação, agregação e outras operações. Pode calcular campos e efetuar a análise estatística. Existem visualizações de tabela e gráficas.

![Gráfico de consulta e os resultados da análise](./media/app-insights-devops/025.png)

Por exemplo, é fácil:

* Segmento de dados de desempenho de pedido da aplicação por camadas de cliente para compreender a sua experiência.
* Pesquisa de códigos de erro específico ou nomes de evento personalizado durante as investigações de site em direto.
* Desagregar a utilização de aplicações de clientes específicos para compreender como as funcionalidades são adquiridas e adotado uma larga maioria.
* Controlar as sessões e tempos de resposta para utilizadores específicos ativar as equipas de suporte e operações fornecer o suporte ao cliente instantânea.
* Determine as funcionalidades de aplicações frequentemente utilizados para responder a perguntas de atribuição de prioridades de funcionalidade.

Cliente DNN consiga aceder tal: "Application Insights forneceu-nos com a peça em falta de equação de que está a ser capaz de combinação, ordenação, consulta e os dados de filtro conforme necessário. Permitir que a nossa equipa de utilizar os seus próprios ingenuity e experiência para encontrar dados com um idioma de consulta poderosa concedeu-nos localizar informações e resolver problemas não mesmo sabemos tivemos. Muitos respostas interessantes provenientes as perguntas começadas *' posso wonder se... '.*"

## <a name="development-tools-integration"></a>Integração de ferramentas de desenvolvimento
### <a name="configuring-application-insights"></a>Configurar o Application Insights
Visual Studio e Eclipse têm ferramentas para configurar os pacotes SDK corretos para o projeto que estiver a desenvolver. Não há um comando de menu para adicionar o Application Insights.

Se utilizar um estrutura como Log4N, NLog ou Trace o registo de rastreio, em seguida, depara-se a opção para enviar os registos ao Application Insights, juntamente com a telemetria, para que pode facilmente correlacionar os rastreios com pedidos, chamadas de dependência e exceções.

### <a name="search-telemetry-in-visual-studio"></a>Telemetria de pesquisa no Visual Studio
Desenvolvimento e depuração uma funcionalidade, pode visualizar e pesquisar a telemetria diretamente no Visual Studio, utilizando as mesmo instalações de pesquisa como o portal web.

E ao Application Insights regista uma exceção, pode ver o ponto de dados no Visual Studio e ir diretamente para o código relevante.

![Pesquisa do Visual Studio](./media/app-insights-devops/060.png)

Durante a depuração, terá a opção para manter a telemetria no computador de desenvolvimento, visualizá-lo no Visual Studio, mas sem enviar para o portal. Esta opção local evita a mistura de depuração com telemetria de produção.

### <a name="build-annotations"></a>Criar anotações
Se utilizar o Visual Studio Team Services para criar e implementar a sua aplicação, anotações de implementação ser mostrada no gráficos no portal. Se a versão mais recente tinha qualquer efeito nas métricas, torna-se óbvias.

![Criar anotações](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Itens de trabalho
Quando for gerado um alerta, o Application Insights pode criar automaticamente um item de trabalho no seu sistema de controlo de trabalho.

## <a name="but-what-about"></a>Mas e sobre …?
* [Privacidade e de armazenamento](app-insights-data-retention-privacy.md) -a telemetria é mantida na proteger os servidores do Azure.
* O impacto no desempenho - é muito baixo. Telemetria é em lotes.
* [Preços](app-insights-pricing.md) - pode começar a utilizar gratuitamente e que continua enquanto estiver no volume baixa.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
Introdução ao Application Insights é fácil. As opções principais são:

* Instrumente uma aplicação de web já em execução. Isto dá-lhe a toda a telemetria desempenho incorporados. Está disponível para [Java](app-insights-java-live.md) e [servidores IIS](app-insights-monitor-performance-live-website-now.md)e também para [aplicações web do Azure](app-insights-azure.md).
* Instrumente projecto durante o desenvolvimento. Pode fazê-lo para [ASP.NET](app-insights-asp-net.md) ou [Java](app-insights-java-get-started.md) aplicações, bem como [Node.js](app-insights-nodejs.md) e um anfitrião de [outros tipos de](app-insights-platforms.md). 
* Instrumento [qualquer página web](app-insights-javascript.md) adicionando um fragmento de código curto.

