---
title: "Perguntas mais frequentes do desempenho de aplicações para aplicações web do Azure | Microsoft Docs"
description: "Obtenha respostas às perguntas mais frequentes sobre a disponibilidade, desempenho e problemas de aplicações na funcionalidade Web Apps do App Service do Azure."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: d997c291e487e27b7e12c015e34ad839ac020151
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Perguntas mais frequentes do desempenho de aplicações para aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas de desempenho de aplicações para o [funcionalidade Web Apps do App Service do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Por que motivo é a minha aplicação lenta?

Vários fatores pode contribuir para a aplicação desempenho lento. Para obter os passos de resolução de problemas detalhada, consulte [Troubleshoot o desempenho da aplicação web lenta](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Como posso resolver um cenário de consumo da CPU elevado?

Em alguns cenários de elevado consumo de CPU, a aplicação verdadeiramente pode necessitar de mais recursos informáticos. Nesse caso, considere dimensionamento para uma camada de serviço mais elevada para a aplicação obtém todos os recursos que necessita. Noutros casos, elevado consumo da CPU poderá ser causado por um ciclo incorreto ou por uma prática codificação. Obter informações sobre o que é acionar uma maior consumo de CPU é um processo de duas partes. Em primeiro lugar, criar uma captura de processo e, em seguida, analise a captura de processo. Para obter mais informações, consulte [capturar e analisar um ficheiro de informação para elevado consumo de CPU para as Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Como posso resolver um cenário de consumo de memória elevada?

Em alguns cenários de consumo de memória elevada, a aplicação verdadeiramente pode necessitar de mais recursos informáticos. Nesse caso, considere dimensionamento para uma camada de serviço mais elevada para a aplicação obtém todos os recursos que necessita. Noutros casos, um erro no código pode provocar uma fuga de memória. Também uma codificação prática pode aumentar o consumo de memória. Obter informações sobre o que é acionar memória elevada consumo é um processo de duas partes. Em primeiro lugar, criar uma captura de processo e, em seguida, analise a captura de processo. Diagnosticador de falhas na Galeria de extensão de Site do Azure eficientemente pode efetuar ambos estes passos. Para obter mais informações, consulte [capturar e analisar um ficheiro de informação para intermitente elevada da memória para aplicações Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Como automatizar a web apps do App Service utilizando o PowerShell

Pode utilizar os cmdlets do PowerShell para gerir e manter as web apps do App Service. No nosso blogue [automatizar as aplicações web alojadas no App Service do Azure utilizando o PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), iremos descrevem como utilizar cmdlets do PowerShell do Gestor de recursos do Azure para automatizar tarefas comuns. A mensagem de blogue também tem o código de exemplo para várias tarefas de gestão de aplicações web. Para obter descrições e sintaxe para todos os cmdlets de aplicações de serviço de aplicações web, consulte [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Como posso ver registos de eventos da minha aplicação web?

Para ver registos de eventos da aplicação web:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. No menu, selecione **consola de depuração** > **CMD**.
3. Selecione o **LogFiles** pasta.
4. Para ver os registos de eventos, selecione o ícone de lápis junto a **eventlog.xml**.
5. Para transferir os registos, execute o cmdlet do PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Como captura de uma informação de memória do modo de utilizador da minha aplicação web?

Para capturar uma captura de memória do modo de utilizador da sua aplicação web:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o **Explorador de processos** menu.
3. Clique com botão direito do **w3wp.exe** processo ou o processo de trabalho Web.
4. Selecione **transferir informação do Estado da memória** > **completa informação**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Como ver informações de nível de processo a minha aplicação web?

Tem duas opções para ver as informações de nível de processo para a sua aplicação web:

*   No portal do Azure:
    1. Abra o **Explorador de processos** da aplicação web.
    2. Para ver os detalhes, selecione o **w3wp.exe** processo.
*   Na consola do Kudu:
    1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Selecione o **Explorador de processos** menu.
    3. Para o **w3wp.exe** processo, selecione **propriedades**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Quando posso procura para a minha aplicação, vejo "Erro 403 - esta aplicação web está parado." Como resolver isto?

Três condições podem causar este erro:

* A aplicação web foi atingido um limite de faturação e o site foi desativado.
* A aplicação web foi parada no portal.
* A aplicação web foi atingido um limite de quota de recursos que podem ser aplicadas a um gratuito ou partilhado plano de serviço de escala.

Para ver o que está a causar o erro e resolva o problema, siga os passos no [Web Apps: "Erro 403 – esta aplicação web está parado"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Onde posso obter mais informações sobre quotas e limites para vários planos de serviço de aplicações?

Para obter informações sobre quotas e limites, consulte [limites do App Service](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Como diminuir o tempo de resposta para o primeiro pedido após o tempo de inatividade

Por predefinição, as aplicações web são descarregadas que estejam Inativos durante um período de tempo definido. Desta forma, o sistema pode poupar recursos. O downside é que a resposta ao primeiro pedido depois da aplicação web é descarregada é mais longo para permitir que a aplicação web para carregar e iniciar processa as respostas. Nos planos de serviço básico e padrão, pode ativar o **Always On** definição para manter a aplicação sempre carregada. Isto elimina mais tempos de carregamento após a aplicação está inativa. Para alterar o **Always On** definição:

1. No portal do Azure, aceda à sua aplicação web.
2. Selecione **definições da aplicação**.
3. Para **Always On**, selecione **no**.

## <a name="how-do-i-turned-on-failed-request-tracing"></a>Como ativou rastreio de pedidos falhados

Para ativar o rastreio de pedido falhado:

1. No portal do Azure, aceda à sua aplicação web.
3. Selecione **todas as definições** > **registos de diagnóstico**.
4. Para **rastreio de pedidos falhados**, selecione **no**.
5. Selecione **Guardar**.
6. No painel de aplicação web, selecione **ferramentas**.
7. Selecione **Visual Studio Online**.
8. Se a definição não for **no**, selecione **no**.
9. Selecione **aceda**.
10. Selecione **Web. config**.
11. No System. webServer, adicione esta configuração (para capturar um URL específico):

    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Para resolver problemas de desempenho lento, adicione esta configuração (se o pedido de captura está a demorar mais do que 30 segundos):
    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Para transferir os rastreios de pedidos falhados no [portal](https://portal.azure.com), aceda ao seu Web site.
15. Selecione **ferramentas** > **Kudu** > **aceda**.
18. No menu, selecione **consola de depuração** > **CMD**.
19. Selecione o **LogFiles** pasta e, em seguida, selecione a pasta com um nome que começa com **W3SVC**.
20. Para ver o ficheiro XML, selecione o ícone de lápis.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Posso ver a mensagem "O processo de trabalho solicitado Reciclagem devido ao limite de 'Percentagem de memória'". Como posso resolver este problema?

A quantidade de disponível máxima de memória para um processo de 32 bits (mesmo num sistema operativo de 64 bits) é de 2 GB. Por predefinição, o processo de trabalho está definido para 32 bits no App Service (para compatibilidade com aplicações web legado).

Considere mudar para processos de 64 bits, pelo que pode tirar partido da memória adicional disponível da função de trabalho Web. Isto aciona um reinício de aplicação web, por isso, agendar em conformidade.

Note também que um ambiente de 64 bits requer um plano de serviço básico ou padrão. Gratuito e partilhado planos são sempre executadas num ambiente de 32 bits.

Para obter mais informações, consulte [configurar web apps no App Service](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-240-seconds"></a>É por que motivo o meu tempo limite do pedido após segundos 240?

Balanceador de carga do Azure tem uma definição de tempo limite de inatividade predefinida de quatro minutos. Isto é, geralmente, um limite de tempo de resposta razoáveis para um pedido web. Se a sua aplicação web requer processamento em segundo plano, recomendamos a utilização de WebJobs do Azure. A aplicação web do Azure pode chamar WebJobs e ser notificada quando o processamento em segundo plano está concluído. Pode escolher entre vários métodos para utilizar WebJobs, incluindo as filas e é acionado.

WebJobs foi concebida para processamento em segundo plano. Pode fazer o mesmo processamento em segundo plano que pretende incluir um trabalho Web. Para obter mais informações sobre WebJobs, consulte [executar tarefas em segundo plano com WebJobs](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Aplicações de ASP.NET Core que estão alojadas no App Service, por vezes, deixar de responder. Como posso corrigir este problema?

Um problema conhecido com anterior [versão Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) poderá fazer com que uma aplicação ASP.NET Core 1.0 que está alojada no App Service ligados intermitentemente deixar de responder. Também poderá ver esta mensagem: "a aplicação CGI especificada encontrou um erro e o servidor finalizou o processo".

Este problema ser corrigido no Kestrel versão 1.0.2. Esta versão está incluído na atualização do ASP.NET Core 1.0.3. Para resolver este problema, certifique-se de que atualiza as dependências de aplicação para utilizar Kestrel 1.0.2. Em alternativa, pode utilizar uma das duas soluções que estão descritas na mensagem de blogue [problemas de desempenho lento do ASP.NET Core 1.0 nas web apps do App Service](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Não é possível encontrar o meu ficheiros de registo na estrutura de ficheiros da minha aplicação web. Como posso encontrá-los?

Se utilizar a funcionalidade de Local Cache do serviço de aplicações, a estrutura de pastas das pastas LogFiles e os dados para a instância de serviço de aplicações são afetadas. Quando a Local Cache é utilizada, subpastas são criadas no armazenamento LogFiles e pastas de dados. Utilizam as subpastas de nomenclatura padrão "Identificador exclusivo" + o carimbo de data / hora. Cada subpasta corresponde a uma instância VM em que a aplicação web está em execução ou foi executada.

Para determinar se estiver a utilizar a Local Cache, verifique o serviço de aplicações **definições da aplicação** separador. Se a Local Cache está a ser utilizado, a aplicação definir `WEBSITE_LOCAL_CACHE_OPTION` está definido como `Always`. 

Se não estiver a utilizar a Cache Local e estão a ter este problema, submeta um pedido de suporte.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Posso ver a mensagem "foi efetuada uma tentativa para aceder a um socket de forma proibido pelas respetivas permissões de acesso." Como resolver isto?

Este erro normalmente ocorre se as ligações de TCP de saída na instância de VM ficam esgotadas. No App Service, os limites são impostos para o número máximo de ligações de saída que podem ser efetuadas para cada instância VM. Para obter mais informações, consulte [limites numéricos Cross-VM](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Este erro também poderá ocorrer se tentar aceder a um endereço local da sua aplicação. Para obter mais informações, consulte [pedidos de endereço Local](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Para obter mais informações sobre ligações de saída na sua aplicação web, consulte o blogue sobre [ligações para Web sites do Azure de saída](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Como posso utilizar o Visual Studio para remoto depurar a minha aplicação web do app Service?

Para instruções detalhadas que mostram como depurar a aplicação web utilizando o Visual Studio, consulte [remoto depurar o aplicação web do app Service](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
