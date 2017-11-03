---
title: "Desempenho de aplicações web no App Service lento | Microsoft Docs"
description: "Este artigo ajuda-o a resolver problemas de desempenho de aplicações lenta web no App Service do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "desempenho de aplicações Web, aplicações lenta, aplicação lenta"
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.openlocfilehash: 1cfe7ec37ad8b24a8bd9ab2bf67e95675a57b675
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Resolver problemas de desempenho de aplicações lenta web no App Service do Azure
Este artigo ajuda-o a resolver problemas de desempenho de aplicações web lenta no [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [do MSDN Azure e os fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode também ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
Quando navega seja lento da aplicação web, o carregamento de páginas e, por vezes, tempo limite.

## <a name="cause"></a>Causa
Este problema é frequentemente causado por problemas de nível de aplicação, tais como:

* pedidos de rede a demorar muito tempo
* consultas de base de dados ou de código de aplicação que está a ser ineficaz
* utilização de CPU/memória elevada de aplicação
* a falhar devido a uma exceção de aplicação

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
Resolução de problemas pode ser dividida em três tarefas diferentes, por ordem sequencial:

1. [Observar e monitorizar o comportamento da aplicação](#observe)
2. [Recolher dados](#collect)
3. [Mitigar o problema](#mitigate)

[Web Apps do App Service](/services/app-service/web/) dá-lhe várias opções de cada passo.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorizar o comportamento da aplicação
#### <a name="track-service-health"></a>Controlar o estado de funcionamento do serviço
Microsoft Azure publicizes sempre que há uma degradação de interrupção ou de desempenho do serviço. Pode controlar o estado de funcionamento do serviço no [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [controlar o estado de funcionamento do serviço](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorizar a aplicação web
Esta opção permite-lhe saber se a aplicação está a ter problemas. No painel da sua aplicação web, clique o **pedidos e erros** mosaico. O **métrica** painel mostra todas as métricas, pode adicionar.

Alguns das métricas que pode querer monitorizar da sua aplicação web

* Memória média conjunto de trabalho
* Tempo de resposta médio
* Tempo de CPU
* Conjunto de trabalho de memória
* Pedidos

![monitorizar o desempenho de aplicações web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitor Web Apps no App Service do Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorizar o estado de ponto final da web
Se estiver a executar a aplicação web **padrão** escalão de preço, as aplicações Web permite-lhe monitorizar dois pontos finais de três localizações geográficas.

Monitorização de pontos finais configura testes web a partir de localizações geo-distribuição que testar um tempo de resposta e a disponibilidade dos URLs da web. O teste efetua uma operação HTTP GET no URL de web para determinar o tempo de resposta e a disponibilidade de cada localização. Cada localização configurada é executado um teste de cada cinco minutos.

Tempo de atividade está a ser monitorizado utilizando códigos de resposta HTTP e o tempo de resposta é medido em milissegundos. Um teste de monitorização falha se o código de resposta HTTP é maior que ou igual a 400 ou se a resposta demora mais do que 30 segundos. Um ponto final é considerado disponível se os testes de monitorização com êxito a partir de localizações especificadas.

Para configurar, consulte [monitorizar aplicações no App Service do Azure](web-sites-monitor.md).

Além disso, consulte [manter Sites do Azure Web a cópia de segurança e monitorização de pontos finais - com Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) para obter um vídeo sobre como monitorizar o ponto final.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorização de desempenho de aplicações utilizando extensões
Também pode monitorizar o desempenho de aplicações utilizando *site extensões*.

Cada aplicação web do app Service fornece um ponto final de gestão extensível que permite-lhe utilizar um conjunto potente das ferramentas de implementação como as extensões de site. As extensões incluem: 

- Editores de código de origem, como [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Ferramentas de gestão de recursos ligados, tais como uma base de dados MySQL ligada a uma aplicação web.

[Azure Application Insights](/services/application-insights/) e [New Relic](/marketplace/partners/newrelic/newrelic/) são dois do desempenho de monitorização as extensões de site que estão disponíveis. Para utilizar New Relic, instale um agente no tempo de execução. Para utilizar o Azure Application Insights, reconstrua o seu código com um SDK e também pode instalar uma extensão que fornece acesso a dados adicionais. O SDK permite-lhe escrever código para monitorizar a utilização e o desempenho da sua aplicação em mais detalhe.

Para utilizar o Application Insights, consulte o artigo [monitorizar o desempenho em aplicações web](../application-insights/app-insights-web-monitor-performance.md).

Para utilizar New Relic, consulte [novo Relic de gestão de desempenho de aplicações no Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Recolher dados
O ambiente de aplicações Web fornece a funcionalidade de diagnóstico para informações de registo do servidor web e a aplicação web. As informações são separadas em diagnóstico do servidor web e o application diagnostics.

#### <a name="enable-web-server-diagnostics"></a>Ativar o diagnóstico do servidor web
Pode ativar ou desativar os seguintes tipos de registos:

* **Detalhadas de registo de erro** -erro informações detalhadas de códigos de estado HTTP que indiquem uma falha (código de estado 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que motivo o servidor devolveu o código de erro.
* **Falha do rastreio de pedido** -informações detalhadas sobre pedidos falhados, incluindo um rastreio de componentes IIS utilizada para processar o pedido e o tempo decorrido em cada componente. Isto pode ser útil se está a tentar melhorar o desempenho de aplicações web ou isolar o que está a causar um erro HTTP específico.
* **Registo do servidor de Web** -informações sobre transações de HTTP utilizando o formato de ficheiro de registo expandido de W3C. Isto é útil quando determinar geral métricas da aplicação web, como o número de pedidos processados ou o número de pedidos está a partir de um endereço IP específico.

#### <a name="enable-application-diagnostics"></a>Ativar o diagnóstico da aplicação
Existem várias opções para recolher dados de desempenho da aplicação de Web Apps, perfil de aplicação em direto a partir do Visual Studio ou modificar o código da aplicação para obter mais informações e rastreios de registo. Pode escolher as opções, com base na quantidade acesso tem da aplicação e observados de monitorização de ferramentas.

##### <a name="use-application-insights-profiler"></a>Utilizar o gerador de perfis do Application Insights
Pode ativar o gerador de perfis do Application Insights iniciar a capturar rastreios de detalhado de desempenho. Pode aceder a rastreios capturados até cinco dias há quando necessárias para investigar problemas aconteceu no passado. Pode escolher esta opção, desde que tem acesso ao recurso do Application Insights da aplicação web no portal do Azure.

O gerador de perfis do Application Insights fornece estatísticas em tempo de resposta para cada chamada web e os rastreios que indica que a linha de código causou as lentas. Por vezes, a aplicação de serviço de aplicações estiver lenta porque determinado código não é escrito num performant forma. Exemplos de código sequencial que pode ser executado em contentions de bloqueio da base de dados paralelas e indesejado. A remoção destes constrangimentos no código aumenta o desempenho da aplicação, mas são difíceis de detetar sem configuração de rastreios elaborados e registos. Os rastreios recolhidos pelo gerador de perfis do Application Insights ajuda a identificar as linhas de código que atrasar a aplicação e ultrapassar este desafio para aplicações do App Service.

 Para obter mais informações, consulte [aplicações web do Azure em direto de criação de perfis com o Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Utilizar a criação de perfis remoto
No App Service do Azure, pode de profiled remotamente as Web Apps, API Apps e WebJobs. Escolha esta opção se tiver acesso ao recurso de aplicação web e sabe como reproduza o problema ou, se souber o intervalo de tempo exato ocorre o problema de desempenho.

Criação de perfis remota é útil se a utilização da CPU do processo é elevada e o processo está em execução inferior que o esperado, ou a latência de pedidos HTTP são superiores ao normal, que pode remotamente o processo de perfil e começar a pilhas de chamada de amostragem da CPU para analisar o processo caminhos atividade e o código de acesso frequente.

Para obter mais informações, consulte [suporte de criação de perfis remotos no App Service do Azure](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Configurar manualmente o rastreios de diagnóstico
Se tiver acesso para o código fonte da aplicação web, o Application diagnostics permite-lhe capturar informações produzidas por uma aplicação web. As aplicações do ASP.NET podem utilizar o `System.Diagnostics.Trace` classe para registar informações no registo de diagnóstico de aplicação. No entanto, terá de alterar o código e volte a implementar a aplicação. Este método é recomendado se a aplicação está em execução no ambiente de teste.

Para obter instruções detalhadas sobre como configurar a sua aplicação para o registo, consulte [ativar o registo de diagnóstico para web apps no App Service do Azure](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Utilizar o portal de suporte do App Service do Azure
As Web Apps fornece-lhe a capacidade para resolver problemas relacionados com a sua aplicação web observando HTTP registos, registos de eventos, informações de estado do processo e muito mais. Pode aceder a todos os estas informações com o nosso portal de suporte em **http://&lt;o nome da aplicação >.scm.azurewebsites.net/Support**

O portal de suporte do App Service do Azure fornece-lhe com três separadores separados para suportar os três passos de um cenário de resolução de problemas comuns:

1. Observar o comportamento atual
2. Analisar a recolher informações de diagnóstico e executando os analisadores incorporadas
3. Mitigar

Se o problema está a acontecer agora, clique em **analisar** > **diagnóstico** > **diagnosticar agora** para criar uma sessão de diagnóstico para si, que recolhe registos de HTTP, os registos do Visualizador de eventos, Estados da memória, os registos de erros PHP e relatório de processo PHP.

Depois dos dados são recolhidos, o portal de suporte executa uma análise nos dados e fornece um relatório HTML.

No caso de pretender transferir os dados, por predefinição, teria de ser armazenado na pasta D:\home\data\DaaS.

Para obter mais informações sobre o portal de suporte do App Service do Azure, consulte [novas atualizações a extensão de Site de suporte para Web sites do Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Utilize a consola de depuração do Kudu
As Web Apps vem com uma consola de depuração que pode utilizar para depuração, explorar, carregar ficheiros, bem como pontos finais JSON para obter informações sobre o ambiente. Esta consola é denominada o *consola Kudu* ou *SCM Dashboard* para a sua aplicação web.

Pode aceder a este dashboard acedendo à ligação **https://&lt;o nome da aplicação >.scm.azurewebsites.net/**.

Algumas das ações que o Kudu fornece são:

* definições de ambiente para a sua aplicação
* fluxo de registo
* informação de diagnóstico
* consola no qual pode executar cmdlets do Powershell e comandos DOS básicos de depuração.

Outra funcionalidade útil do Kudu é que, no caso da aplicação que está a gerar as exceções de primeira hipótese, pode utilizar o Kudu e informações de estado a ferramenta de SysInternals Procdump para criar a memória. Estas capturas de memória são instantâneos do processo e, muitas vezes, podem ajudar a resolver problemas mais complicados com a sua aplicação web.

Para obter mais informações sobre as funcionalidades disponíveis no Kudu, consulte [ferramentas dos serviços de equipa de Web sites do Azure, deve conhecer sobre](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Mitigar o problema
#### <a name="scale-the-web-app"></a>Dimensionar a aplicação web
No App Service do Azure, para um melhor desempenho e débito, pode ajustar a escala a que está a executar a aplicação. Como aumentar verticalmente a uma aplicação web envolve dois ações relacionadas: alterar o seu plano de serviço de aplicações para um escalão de preço superior e a configuração de determinadas definições depois de terem mudado para o escalão de preço superior.

Para obter mais informações sobre como aumentar, consulte [dimensionar uma aplicação web no App Service do Azure](web-sites-scale.md).

Além disso, pode optar por executar a sua aplicação em mais do que uma instância. Aumentar horizontalmente não só fornece-lhe mais capacidade de processamento, mas também lhe dê algum período de tolerância a falhas. Se o processo de ficar inativo numa instância, as outras instâncias continuam para servir pedidos.

Pode definir o dimensionamento para ser Manual ou automático.

#### <a name="use-autoheal"></a>Utilizar AutoHeal
AutoHeal recicla o processo de trabalho para a sua aplicação com base nas definições que escolha (por exemplo, as alterações de configuração, pedidos, limites de memória ou o tempo necessário para executar um pedido). Na maioria das vezes, a Reciclagem do processo é a forma mais rápida para recuperar a partir de um problema. Embora sempre pode reiniciar a aplicação web de diretamente no portal do Azure, AutoHeal faz-la automaticamente por si. Tudo o que precisa de fazer é adicionar alguns acionadores o Web. config de raiz para a sua aplicação web. Estas definições funciona da mesma forma, mesmo se a aplicação não é uma aplicação .net.

Para obter mais informações, consulte [Web Sites Azure Autorrecuperação](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Reinicie a aplicação web
O reinício, muitas vezes, é a forma mais simples para recuperar de problemas isolados. No [portal do Azure](https://portal.azure.com/), no painel da sua aplicação web, tem as opções para interromper ou reiniciar a sua aplicação.

 ![reiniciar a aplicação web para resolver problemas de desempenho](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Também pode gerir a sua aplicação web com o Azure Powershell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).
