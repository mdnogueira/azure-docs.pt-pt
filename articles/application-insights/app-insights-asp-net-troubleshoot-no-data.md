---
title: "Resolução de problemas sem dados - Application Insights para .NET"
description: "Não existem dados no Azure Application Insights? Tente aqui."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 843ffdf6c39cbdf00bfde969eaf93701db6ed536
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Resolução de problemas sem dados - Application Insights para .NET
## <a name="some-of-my-telemetry-is-missing"></a>Algumas das minha telemetria está em falta
*No Application Insights, vejo apenas uma fração dos eventos que estão a ser gerados pela minha aplicação.*

* Se vir consistentemente a fração do mesma que, se provavelmente a adaptável [amostragem](app-insights-sampling.md). Para confirmar isto, abra pesquisa (a partir do painel de descrição geral) e observe uma instância de um pedido ou outros eventos. Na parte inferior da secção de propriedades, clique em "…" para obter detalhes de propriedade completa. Se solicitar contagem > 1, em seguida, é amostragem na operação. 
* Caso contrário, é possível que está a atingir um [o limite de velocidade de dados](app-insights-pricing.md#limits-summary) para o seu plano de preços. Estes limites são aplicadas por minuto.

## <a name="no-data-from-my-server"></a>Não existem dados a partir do meu servidor
*Posso instalar a minha aplicação meu servidor web e agora não vejo qualquer telemetria a partir do mesmo. Correu OK na minha máquina de desenvolvimento.*

* Provavelmente um problema de firewall. [Definir as exceções de firewall para o Application Insights enviar dados](app-insights-ip-addresses.md).

*Posso [instalado o Monitor de estado](app-insights-monitor-performance-live-website-now.md) no meu servidor web para monitorizar as aplicações existentes. Não consigo ver quaisquer resultados.*

* Consulte [Monitor de estado de resolução de problemas](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights). 

## <a name="q01"></a>Nenhuma opção 'A adicionar o Application Insights' no Visual Studio
*Quando posso com o botão direito um projeto existente no Explorador de soluções, não vejo qualquer opções do Application Insights.*

* Nem todos os tipos de projeto de .NET são suportados pelas ferramentas de. Projetos Web e WCF são suportados. Para outros tipos de projeto, tais como aplicações de ambiente de trabalho ou serviços, pode ainda [adicionar manualmente um Application Insights SDK ao projeto](app-insights-windows-desktop.md).
* Certifique-se de que tem [Visual Studio 2013 atualização 3 ou posterior](http://go.microsoft.com/fwlink/?LinkId=397827). Inclui pré-instaladas com as ferramentas de análise de programador, que fornecem o Application Insights SDK.
* Selecione **ferramentas**, **extensões e atualizações** e verifique se **Developer Analytics Tools** está instalado e ativado. Se assim for, clique em **atualizações** para ver se existe uma atualização disponível.
* Abra a caixa de diálogo novo projeto e escolha a aplicação ASP.NET Web. Se vir a opção do Application Insights existe, as ferramentas são instaladas. Caso contrário, tente desinstalar e reinstalar o Application Insights Tools.

## <a name="q02"></a>Falha ao adicionar o Application Insights
*Quando tentar adicionar o Application Insights para um projeto existente, vejo uma mensagem de erro.*

Causas prováveis:

* Falha de comunicação com o portal do Application Insights; ou
* Há um problema com a sua conta do Azure;
* Tem apenas [acesso de leitura para a subscrição ou grupo em que estava a tentar criar o novo recurso](app-insights-resources-roles-access-control.md).

Corrija:

* Certifique-se de que forneceu as credenciais de início de sessão para a conta à direita do Azure. 
* No seu browser, verifique se tem acesso para o [portal do Azure](https://portal.azure.com). Abra as definições e ver se existe qualquer restrição.
* [Adicionar o Application Insights ao seu projeto existente](app-insights-asp-net.md): no Explorador de soluções, clique com o botão direito do rato em projeto e escolha "Adicionar Application Insights".
* Se esta ainda não está a funcionar, siga o [procedimento manual](app-insights-windows-services.md) para adicionar um recurso no portal e, em seguida, adicione o SDK ao projeto. 

## <a name="emptykey"></a>Posso obter um erro "chave de instrumentação não pode estar vazio"
Parece que ocorreu um erro enquanto foram a instalar o Application Insights ou talvez um adaptador de registo.

No Explorador de soluções, clique no seu projeto e escolha **Application Insights > configurar o Application Insights**. Obterá uma caixa de diálogo invites que inicie sessão no Azure e cria um recurso do Application Insights, ou reutilize um existente.

## <a name="NuGetBuild"></a>"Pacotes de NuGet estão em falta" no meu servidor de compilação
*Tudo baseia-se OK quando estou a depuração no meu computador de desenvolvimento, mas como posso obter um erro de NuGet no servidor de compilação.*

Consulte [restauro do pacote NuGet](http://docs.nuget.org/Consume/Package-Restore) e [restauro automático do pacote](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Comandos de menu em falta para abrir o Application Insights no Visual Studio
*Quando posso com o botão direito meu projeto Explorador de soluções, não consigo ver quaisquer comandos do Application Insights ou não vejo um comando de abrir Application Insights.*

Causas prováveis:

* Se tiver criado manualmente o recurso do Application Insights, ou se o projeto é um tipo que não é suportado pelas ferramentas de Application Insights.
* As ferramentas de programador análise estão desativadas no seu Visual Studio. 
* O Visual Studio é anterior ao 2013 atualização 3.

Corrija:

* Certifique-se a sua versão do Visual Studio 2013 atualização 3 ou posterior.
* Selecione **ferramentas**, **extensões e atualizações** e verifique se **Developer Analytics tools** está instalado e ativado. Se assim for, clique em **atualizações** para ver se existe uma atualização disponível.
* Clique no projeto no Explorador de soluções. Se vir o comando **Application Insights > configurar o Application Insights**, utilizá-la para ligar o seu projeto para o recurso no serviço do Application Insights.

Caso contrário, o tipo de projeto diretamente não é suportado pelas ferramentas de Application Insights. Para ver a telemetria, inicie sessão no [portal do Azure](https://portal.azure.com), escolha Application Insights na barra de navegação esquerdo e selecione a aplicação.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Acesso negado" abrir o Application Insights do Visual Studio
*O comando de menu 'Abra Application Insights' assume-me para o portal do Azure, mas receber um erro "acesso negado".*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

O Microsoft início de sessão que é utilizado pela última vez no seu browser predefinido não tem acesso às [o recurso que foi criado quando o Application Insights foi adicionado a esta aplicação](app-insights-asp-net.md). Existem duas razões prováveis: 

* Tiver mais do que uma conta Microsoft - talvez um trabalho e uma conta Microsoft pessoal? O início de sessão que é utilizado pela última vez no seu browser predefinido foi para uma conta diferente daquela que tem acesso ao [adicionar o Application Insights ao projeto](app-insights-asp-net.md). 
  
  * Corrija: Clique no seu nome no lado direito da janela do browser de cima e terminar sessão. Em seguida, inicie sessão com a conta que tenha acesso. Em seguida, na barra de navegação esquerdo, clique em Application Insights e selecione a sua aplicação.
* Alguém adicionar Application Insights ao projeto e se esqueceu de dão-lhe [acesso ao grupo de recursos](app-insights-resources-roles-access-control.md) no qual foi criado. 
  
  * Corrija: Se utilizaram uma conta institucional, estes podem adicioná-para a equipa; ou, podem conceder acesso individual para o grupo de recursos.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Asset não foi encontrado' abrir o Application Insights do Visual Studio
*O comando de menu 'Abra Application Insights' assume-me para o portal do Azure, mas receber um erro 'ativo não encontrado'.*

Causas prováveis:

* O recurso do Application Insights para a sua aplicação tiver sido eliminado; ou
* A chave de instrumentação foi definida ou alterada no Applicationinsights editando-lo diretamente, sem atualizar o ficheiro de projeto. 

A chave de instrumentação no Applicationinsights os controlos onde a telemetria é enviada. Uma linha no ficheiro de projeto controla qual o recurso é aberto quando utilizar o comando no Visual Studio. 

Corrija:

* No Explorador de soluções, clique com o botão direito no projeto e escolha Application Insights, configurar o Application Insights. Na caixa de diálogo, pode optar por enviar a telemetria para um recurso existente ou criar um novo. Ou:
* Abra o recurso diretamente. Inicie sessão no [portal do Azure](https://portal.azure.com), clique o Application Insights na barra de navegação esquerdo e, em seguida, selecione a sua aplicação.

## <a name="where-do-i-find-my-telemetry"></a>Onde posso encontrar o meu telemetria?
*Posso com sessão iniciada para o [portal do Microsoft Azure](https://portal.azure.com), e que procuro no dashboard principal do Azure. Por isso, onde encontrar os meus dados do Application Insights?*

* Na barra de navegação esquerdo, clique em Application Insights, em seguida, o nome da aplicação. Se não tiver quaisquer projetos existe, terá de [adicionar ou configurar o Application Insights no seu projeto web](app-insights-asp-net.md).
  
    Não existe, verá algumas gráficos de resumo. Pode clicar sucessivamente para ver mais detalhes.
* No Visual Studio, enquanto está a depurar a aplicação, clique no botão do Application Insights.

## <a name="q03"></a>Não existem dados de servidor (ou não existem dados de todo)
*Posso executou a minha aplicação e, em seguida, abrir o serviço do Application Insights no Microsoft Azure, mas todos os gráficos mostram 'Aprenda a recolher...' ou 'Não configurada'.* Ou, *apenas dados de vista de página e utilizador, mas não existem dados de servidor.*

* Execute a sua aplicação no modo de depuração no Visual Studio (F5). Utilize a aplicação, de modo a gerar alguma telemetria. Certifique-se de que consegue ver os eventos registados na janela de saída do Visual Studio. 
  
    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)
* No portal do Application Insights, abra [pesquisa de diagnóstico](app-insights-diagnostic-search.md). Dados normalmente, são apresentados aqui primeiro.
* Clique no botão de atualização. O painel atualiza-se periodicamente, mas pode também fazê-lo manualmente. Ultrapassa o intervalo de atualização para intervalos de tempo maior.
* Verifique as chaves de instrumentação correspondem. No painel principal para a sua aplicação no portal do Application Insights, no **Essentials** pendente, observe **chave de instrumentação**. Em seguida, no projeto no Visual Studio, abra Applicationinsights e localize o `<instrumentationkey>`. Verifique que as duas chaves são iguais. Se não for:
  
  * No portal, clique em Application Insights e procure o recurso de aplicação com a chave correta; ou
  * No Explorador de soluções do Visual Studio, clique com o botão direito no projeto e escolha Application Insights, configurar. Reponha a aplicação para enviar telemetria para o recurso à direita.
  * Se não é possível localizar as correspondentes chaves, verifique que está a utilizar as mesmas credenciais de início de sessão no Visual Studio como no portal.
    
    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
* No [dashboard home page do Microsoft Azure](https://portal.azure.com), observe o mapa de estado de funcionamento do serviço. Se existirem algumas indicações de alerta, aguarde até que tenham devolvido para OK e, em seguida, feche e volte a abrir o painel de aplicações do Application Insights.
* Verifique também [nosso blogue de estado](http://blogs.msdn.com/b/applicationinsights-status/).
* Foi escrever qualquer código para o [SDK do lado do servidor](app-insights-api-custom-events-metrics.md) que poderá alterar a chave de instrumentação na `TelemetryClient` instâncias ou na `TelemetryContext`? Ou foi escrita um [configuração filtro ou amostragem](app-insights-api-filtering-sampling.md) que poderá ser filtragem saída demasiada?
* Se editasse Applicationinsights, verifique a configuração de [TelemetryInitializers e TelemetryProcessors](app-insights-api-filtering-sampling.md). Um tipo com nome incorretamente ou um parâmetro pode fazer com que o SDK para enviar sem dados.

## <a name="q04"></a>Não existem dados sobre a utilização de vistas de página Browsers,
*Posso ver os dados nos gráficos de tempo de resposta do servidor e os pedidos de servidor, mas não existem dados em tempo de carregamento da vista de página ou em painéis de Browser ou utilização.*

Os dados provém de scripts nas páginas web. 

* Se tiver adicionado o Application Insights para um projeto web existente, [tem de adicionar manualmente os scripts](app-insights-javascript.md).
* Certifique-se de que o Internet Explorer não apresentar o site no modo de compatibilidade.
* Utilizar a funcionalidade de depuração no browser (F12 em alguns browsers, em seguida, escolha rede) para verificar que os dados estão a ser enviados para `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Não existem dados de exceção ou dependência
Consulte [telemetria de dependência](app-insights-asp-net-dependencies.md) e [telemetria de exceção](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Não existem dados de desempenho
Dados de desempenho (CPU, velocidade de e/s e assim sucessivamente) está disponível para [serviços web de Java](app-insights-java-collectd.md), [aplicações de ambiente de trabalho do Windows](app-insights-windows-desktop.md), [web IIS aplicações e serviços se instalar o monitor de estado](app-insights-monitor-performance-live-website-now.md), e [Cloud Services do Azure](app-insights-azure.md). Pode encontrá-la em definições, os servidores.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Não existem dados (servidor), uma vez que posso publicado a aplicação ao meu servidor
* Certifique-se de que realmente copiou todos os da Microsoft. DLLs de ApplicationInsights para o servidor, juntamente com Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Na sua firewall poderá ter de [abrir algumas portas TCP](app-insights-ip-addresses.md).
* Se tiver de utilizar um proxy para enviar fora da sua rede empresarial, defina [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) em Web. config
* Windows Server 2008: Certifique-se de que instalou as seguintes atualizações: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Posso utilizado para ver os dados, mas foi parado
* Verifique o [Estado blogue](http://blogs.msdn.com/b/applicationinsights-status/).
* Atingiu a quota mensal de pontos de dados? Abra o definições/Quota e preços para descobrir. Se Sim, pode atualizar o plano ou paga capacidade adicional. Consulte o [preços esquema](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que estou a esperar
Se a aplicação envia uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para ASP.NET versão 2.0.0-Beta3 ou posterior, o [amostragem adaptável](app-insights-sampling.md) funcionalidade pode operar e enviar apenas uma percentagem da sua telemetria. 

Pode desativá-lo, mas isto não é recomendado. A amostragem foi concebida para que telemetrias relacionadas corretamente é transmitida, para fins de diagnóstico. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dados geográficas incorreto na telemetria do utilizador
A cidade, região e dimensões de país são derivadas de endereços IP e não sempre são precisos.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção “método não encontrado” ao executar nos Serviços Cloud do Azure
Compilou para .NET 4.6? O 4.6 não é suportado automaticamente nas funções dos Serviços Cloud do Azure. [Instale o 4.6 em cada função](../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a aplicação.

## <a name="still-not-working"></a>Ainda não está a funcionar...
* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

