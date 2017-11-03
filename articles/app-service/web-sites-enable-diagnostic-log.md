---
title: "Ativar o registo de diagnóstico para web apps no App Service do Azure"
description: "Saiba como ativar o registo de diagnóstico e adicionar instrumentação à sua aplicação, bem como aceder as informações registadas pelo Azure."
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.openlocfilehash: 42644bd97443e827b523503989b0b25d3d48028c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Ativar o registo de diagnóstico para web apps no App Service do Azure
## <a name="overview"></a>Descrição geral
O Azure oferece incorporado diagnóstico para ajudar a depurar uma [aplicação web do app Service](http://go.microsoft.com/fwlink/?LinkId=529714). Este artigo irá aprender como ativar o registo de diagnóstico e adicionar instrumentação à sua aplicação, bem como aceder as informações registadas pelo Azure.

Este artigo utiliza o [Portal do Azure](https://portal.azure.com), Azure PowerShell e a Interface de linha de comandos do Azure (CLI do Azure) para trabalhar com registos de diagnóstico. Para obter informações sobre como trabalhar com registos de diagnóstico com o Visual Studio, consulte [Azure de resolução de problemas no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnóstico do servidor Web e o application diagnostics
Web apps do App Service fornecem a funcionalidade de diagnóstico para informações de registo do servidor web e a aplicação web. Estes logicamente estão separadas em **diagnóstico do servidor de web** e **diagnóstico de aplicação**.

### <a name="web-server-diagnostics"></a>Diagnóstico do servidor Web
Pode ativar ou desativar os seguintes tipos de registos:

* **Detalhadas de registo de erro** -erro informações detalhadas de códigos de estado HTTP que indiquem uma falha (código de estado 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que motivo o servidor devolveu o código de erro.
* **Falha do rastreio de pedido** -informações detalhadas sobre pedidos falhados, incluindo um rastreio de componentes IIS utilizada para processar o pedido e o tempo decorrido em cada componente. Isto pode ser útil se está a tentar melhorar o desempenho do site ou isolar o que está a causar um erro HTTP específico a ser devolvido.
* **Registo do servidor de Web** -informações sobre transações de HTTP utilizando o [formato de ficheiro de registo expandido de W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Isto é útil ao determinar as métricas de site geral, tais como o número de pedidos processados ou o número de pedidos está a partir de um endereço IP específico.

### <a name="application-diagnostics"></a>Diagnóstico de aplicação
Diagnóstico de aplicação permite-lhe capturar informações produzidas por uma aplicação web. As aplicações do ASP.NET podem utilizar o [Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) classe para registar informações no registo de diagnóstico de aplicação. Por exemplo:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

No tempo de execução pode obter estes registos para ajudar a resolver problemas. Para obter mais informações, consulte [web apps do Azure de resolução de problemas no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Web apps do App Service também registar informações de implementação quando publicar conteúdo para uma aplicação web. Isto ocorre automaticamente e não existem sem definições de configuração para o registo de implementação. Registo de implementação permite-lhe determinar por que motivo uma implementação falhou. Por exemplo, se estiver a utilizar um script de implementação personalizada, poderá utilizar o registo de implementação para determinar por que razão o script está a falhar.

## <a name="enablediag"></a>Como ativar o diagnóstico
Para ativar o diagnóstico no [Portal do Azure](https://portal.azure.com), aceda ao painel da sua aplicação web e clique em **definições > registos de diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de registos](./media/web-sites-enable-diagnostic-log/logspart.png)

Quando ativa **diagnóstico de aplicação** também optar pelo **nível**. Esta definição permite-lhe filtrar as informações capturadas para **informativa**, **aviso** ou **erro** informações. Definir este como **verboso** regista todas as informações produzidas pela aplicação.

> [!NOTE]
> Ao contrário de alterar o ficheiro Web. config, ativar o diagnóstico de aplicação ou alterar os níveis de registo de diagnóstico não recicle o domínio de aplicação que a aplicação é executada no.
>
>

No [portal clássico](https://manage.windowsazure.com) aplicação Web **configurar** separador, pode selecionar **armazenamento** ou **sistema de ficheiros** para **registo de servidor web** . Selecionar **armazenamento** permite-lhe selecionar uma conta de armazenamento e, em seguida, um contentor do blob que os registos serão escritos. Todos os outros registos para **site diagnóstico** são escritos para o sistema de ficheiros apenas.

O [portal clássico](https://manage.windowsazure.com) aplicação Web **configurar** separador também tem definições adicionais para o application diagnostics:

* **Sistema de ficheiros** -armazena as informações de diagnóstico de aplicação para o sistema de ficheiros de aplicação web. Estes ficheiros podem ser acedidos por FTP ou transferiu como um arquivo Zip com o Azure PowerShell ou a Interface de linha de comandos do Azure (CLI do Azure).
* **Tabela de armazenamento** -armazena as informações de diagnóstico de aplicação no nome de conta de armazenamento do Azure e a tabela especificado.
* **Armazenamento de BLOBs** -armazena as informações de diagnóstico de aplicação no contentor de conta de armazenamento do Azure e o blob especificado.
* **Período de retenção** -por predefinição, os registos não são automaticamente eliminados do **armazenamento de BLOBs**. Selecione **definir a retenção** e introduza o número de dias a manter os registos, se pretender eliminar automaticamente registos.

> [!NOTE]
> Se lhe [voltar a gerar chaves de acesso da sua conta de armazenamento](../storage/common/storage-create-storage-account.md), tem de repor a configuração do respetivo registo para utilizar as chaves atualizadas. Para efetuar este procedimento:
>
> 1. No **configurar** separador, defina a funcionalidade de registo correspondentes **desativar**. Guarde a definição.
> 2. Ative o registo para o blob de conta de armazenamento ou tabela novamente. Guarde a definição.
>
>

Qualquer combinação de sistema de ficheiros, o armazenamento de tabela ou o armazenamento de blob pode ser ativada em simultâneo e ter configurações de nível de registo individuais. Por exemplo, pode pretender registar erros e avisos para armazenamento de BLOBs como uma solução de registo de longa duração, ao ativar o registo do sistema de ficheiros com um nível de verboso.

Enquanto todos os três localizações de armazenamento fornecem as mesmas informações básicas para eventos registados, **tabela armazenamento** e **armazenamento de BLOBs** registar informações adicionais, tais como o ID de instância, ID de thread e um mais timestamp granular (formato de marcas de escala) que o registo para **sistema de ficheiros**.

> [!NOTE]
> As informações armazenadas num **tabela armazenamento** ou **armazenamento de BLOBs** só pode ser acedido através de um cliente de armazenamento ou uma aplicação que pode trabalhar diretamente com estes sistemas de armazenamento. Por exemplo, o Visual Studio 2013 contém um Explorador de armazenamento que podem ser utilizadas para explorar a tabela ou o blob storage e HDInsight pode aceder a dados armazenados no blob storage. Também pode escrever uma aplicação que acede ao Storage do Azure, utilizando um do [Azure SDKs](/downloads/#).
>
> [!NOTE]
> Diagnóstico também pode ser ativado a partir do PowerShell do Azure utilizando o **conjunto AzureWebsite** cmdlet. Se não tiver instalado o Azure PowerShell ou não tiver configurado para utilizar a sua subscrição do Azure, consulte [como utilizar o Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

## <a name="download"></a>Como: transferir os registos
Informações de diagnóstico armazenadas para o sistema de ficheiros de aplicação web podem ser acedidas diretamente a utilizar FTP. Também pode ser transferido como um arquivo Zip com o Azure PowerShell ou a Interface de linha de comandos do Azure.

A estrutura de diretórios que os registos são armazenados no é o seguinte:

* **Os registos de aplicações** -/LogFiles/aplicação /. Esta pasta contém um ou mais ficheiros de texto que contém informações produzidas pelo registo de aplicações.
* **Falha de rastreios de pedido** -/ LogFiles/W3SVC # # # /. Esta pasta contém um ficheiro XSL e um ou mais ficheiros XML. Certifique-se de que transfira o ficheiro XSL no mesmo diretório como o XML ficheiros porque o ficheiro XSL fornece a funcionalidade para formatação e filtrar o conteúdo os ficheiros XML, quando são visualizados no Internet Explorer.
* **Os registos de erros de detalhado** -/LogFiles/DetailedErrors /. Esta pasta contém um ou mais ficheiros. htm que fornecem informações extensas eventuais erros de HTTP que ocorreram.
* **Os registos do servidor de Web** -/LogFiles/http/RawLogs. Esta pasta contém uma ou mais ficheiros de texto formatado utilizando o [formato de ficheiro de registo expandido de W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Registos de implementação** -LogFiles/Git. Esta pasta contém registos gerados pelos processos de implementação interno utilizados por aplicações web do Azure, bem como os registos para implementações do Git.

### <a name="ftp"></a>FTP
Para aceder a informações de diagnóstico a utilizar FTP, visite o **Dashboard** da sua aplicação web no [portal clássico](https://manage.windowsazure.com). No **leitura rápida** secção, utilize o **registos de diagnóstico de FTP** ligação para aceder aos ficheiros de registo a utilizar FTP. O **utilizador de FTP/implementação** entrada lista o nome de utilizador que deve ser utilizado para aceder ao site FTP.

> [!NOTE]
> Se o **utilizador de FTP/implementação** entrada não estiver definida, ou tenha esquecido a palavra-passe para este utilizador, pode criar um novo utilizador e palavra-passe utilizando o **repor as credenciais de implementação** ligação no  **leitura rápida** secção o **Dashboard**.
>
>

### <a name="download-with-azure-powershell"></a>Transferir com o Azure PowerShell
Para transferir os ficheiros de registo, inicie uma nova instância do Azure PowerShell e utilize o seguinte comando:

    Save-AzureWebSiteLog -Name webappname

Isto irá guardar os registos da aplicação web especificada pelo **-nome** parâmetro para um ficheiro denominado **logs.zip** no diretório atual.

> [!NOTE]
> Se não tiver instalado o Azure PowerShell ou não tiver configurado para utilizar a sua subscrição do Azure, consulte [como utilizar o Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="download-with-azure-command-line-interface"></a>Transferir com a Interface de linha de comandos do Azure
Para transferir os ficheiros de registo utilizando a Interface de linha de comandos do Azure, abra uma nova linha de comandos, PowerShell, Bash ou sessão de Terminal e introduza o seguinte comando:

    azure site log download webappname

Isto irá guardar os registos da aplicação web com o nome 'webappname' para um ficheiro denominado **diagnostics.zip** no diretório atual.

> [!NOTE]
> Se não tiver instalado a Interface de linha de comandos do Azure (CLI do Azure) ou não tiver configurado para utilizar a sua subscrição do Azure, consulte [como utilizar o Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Como: ver os registos no Application Insights
Visual Studio Application Insights fornece ferramentas para filtrar e procurar os registos e para correlacionar os registos com pedidos e outros eventos.

1. Adicione o Application Insights SDK ao projeto no Visual Studio.
   * No Explorador de soluções, clique com o botão direito do rato em projeto e escolha adicionar o Application Insights. Será guiado através de passos que incluem a criação de um recurso do Application Insights. [Saiba mais](../application-insights/app-insights-asp-net.md)
2. Adicione o pacote de serviço de escuta de rastreio ao seu projeto.
   * Clique com o botão direito do rato em projeto e selecione gerir pacotes NuGet. Selecione `Microsoft.ApplicationInsights.TraceListener` [Saiba mais](../application-insights/app-insights-asp-net-trace-logs.md)
3. Carregar o projeto e execute-o para gerar dados de registo.
4. No [Portal do Azure](https://portal.azure.com/), navegue para o novo recurso do Application Insights e abra **pesquisa**. Verá os dados de registo, juntamente com o pedido, utilização e outra telemetria. Alguma telemetria poderá demorar alguns minutos a chegada: clique em Atualizar. [Saiba mais](../application-insights/app-insights-diagnostic-search.md)

[Saiba mais sobre o desempenho de controlo com o Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a>Como: registos de fluxo
Ao desenvolver uma aplicação, muitas vezes, é útil ver informações de registo em tempo quase real. Isto pode ser conseguido através de transmissão em fluxo de informações de registo para o ambiente de desenvolvimento utilizando o Azure PowerShell ou a Interface de linha de comandos do Azure.

> [!NOTE]
> Alguns tipos de memória intermédia de registo de escrita para o ficheiro de registo, o que pode resultar em eventos fora de ordem na sequência. Por exemplo, uma entrada de registo de aplicação que ocorre quando um utilizador visita uma página poderão ser apresentada no fluxo antes da entrada de registo HTTP correspondente para o pedido de página.
>
> [!NOTE]
> Registo de transmissão em fluxo também irá transmitir informações escritas para qualquer ficheiro de texto armazenado no **D:\\raiz\\LogFiles\\**  pasta.
>
>

### <a name="streaming-with-azure-powershell"></a>Transmissão em fluxo com o Azure PowerShell
Para transmitir informações de registo, inicie uma nova instância do Azure PowerShell e utilize o seguinte comando:

    Get-AzureWebSiteLog -Name webappname -Tail

Isto irá ligar para a aplicação web especificada pelo **-nome** parâmetro e começar a transmissão em fluxo de informações para a janela do PowerShell como de registo de eventos ocorre na aplicação web. Todas as informações de escrita em ficheiros que termina em. txt,. log ou. htm que estão armazenados no diretório /LogFiles (home/d: / logfiles) serão transmitidas para a consola local.

Para filtrar eventos específicos, como erros, utilize o **-mensagem** parâmetro. Por exemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Para filtrar os tipos de registo específicos, tal como HTTP, utilize o **-caminho** parâmetro. Por exemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Para ver uma lista de caminhos disponíveis, utilize o parâmetro - ListPath.

> [!NOTE]
> Se não tiver instalado o Azure PowerShell ou não tiver configurado para utilizar a sua subscrição do Azure, consulte [como utilizar o Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Transmissão em fluxo com a Interface de linha de comandos do Azure
Para transmitir informações de registo, abra uma nova linha de comandos, PowerShell, Bash ou sessão de Terminal e introduza o seguinte comando:

    azure site log tail webappname

Isto irá ligar a aplicação web com o nome 'webappname' e começar a transmissão em fluxo de informações para a janela tal como eventos de registo na aplicação web. Todas as informações de escrita em ficheiros que termina em. txt,. log ou. htm que estão armazenados no diretório /LogFiles (home/d: / logfiles) serão transmitidas para a consola local.

Para filtrar eventos específicos, como erros, utilize o **– filtro** parâmetro. Por exemplo:

    azure site log tail webappname --filter Error

Para filtrar os tipos de registo específicos, tal como HTTP, utilize o **– caminho** parâmetro. Por exemplo:

    azure site log tail webappname --path http

> [!NOTE]
> Se não tiver instalado a Interface de linha de comandos do Azure, ou se não tiver configurado para utilizar a sua subscrição do Azure, consulte [como ao utilizar o Azure da linha de comandos Interface](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Como: compreender os registos de diagnóstico
### <a name="application-diagnostics-logs"></a>Registos de diagnóstico de aplicações
Diagnóstico de aplicação armazena informações num formato específico para aplicações de .NET, dependendo se armazenar os registos para o sistema de ficheiros, o armazenamento de tabelas ou o armazenamento de Blobs. O conjunto de base de dados armazenados é igual em todos os três tipos de armazenamento - a data e hora que do evento ocorreu, o ID do processo que produziu o evento, o tipo de evento (informação, aviso, erro) e a mensagem de evento.

**Sistema de Ficheiros**

Cada linha com sessão iniciada para o sistema de ficheiros ou recebidas utilizando a transmissão em fluxo estará no seguinte formato:

    {Date}  PID[{process id}] {event type/level} {message}

Por exemplo, um evento de erro, aparece semelhante ao seguinte:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

O registo para o sistema de ficheiros fornece as informações mais básicas dos três métodos disponíveis, fornecendo apenas o tempo, id do processo, ao nível do evento e a mensagem.

**Armazenamento de tabelas**

Quando o registo para o table storage, propriedades adicionais são utilizadas para facilitar a procurar os dados armazenados na tabela, bem como informações mais granulares sobre o evento. As seguintes propriedades (colunas) são utilizadas para cada entidade (linhas) armazenada na tabela.

| Nome da propriedade | Formato de valores / |
| --- | --- |
| PartitionKey |Data/hora do evento no formato yyyyMMddHH |
| RowKey |Um valor GUID que identifica exclusivamente esta entidade |
| Timestamp |A data e hora em que ocorreu o evento |
| EventTickCount |A data e hora em que o evento ocorreu, no formato de marcas de escala (maior precisão) |
| ApplicationName |O nome da aplicação web |
| Nível |Nível do evento (por exemplo, erro, aviso, informações) |
| EventId |O ID de evento deste evento<p><p>Por predefinição, 0 se nenhum especificado |
| instanceId |Instância da aplicação web que ocorreu o mesmo |
| PID |ID do processo |
| TID |O ID de thread do thread que produziu o evento |
| Mensagem |Mensagem de detalhes do evento |

**Armazenamento de blobs**

Ao iniciar sessão para o armazenamento de BLOBs, os dados são armazenados no formato de valores separados por vírgulas (CSV). Semelhante ao armazenamento de tabelas, campos adicionais são registados para fornecer informações mais granulares sobre o evento. As seguintes propriedades são utilizadas para cada linha no CSV:

| Nome da propriedade | Formato de valores / |
| --- | --- |
| Data |A data e hora em que ocorreu o evento |
| Nível |Nível do evento (por exemplo, erro, aviso, informações) |
| ApplicationName |O nome da aplicação web |
| instanceId |Instância da aplicação web que o evento ocorreu |
| EventTickCount |A data e hora em que o evento ocorreu, no formato de marcas de escala (maior precisão) |
| EventId |O ID de evento deste evento<p><p>Por predefinição, 0 se nenhum especificado |
| PID |ID do processo |
| TID |O ID de thread do thread que produziu o evento |
| Mensagem |Mensagem de detalhes do evento |

Os dados armazenados num blob seriam ter um aspeto semelhantes ao seguinte:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> A primeira linha do registo irá conter os cabeçalhos de coluna, conforme representado neste exemplo.
>
>

### <a name="failed-request-traces"></a>Falha de rastreios de pedido
Os rastreios de pedidos falhados são armazenados nos ficheiros XML com o nome **. XML de # # # fr**. Para tornar mais fácil ver as informações com sessão iniciada, uma folha de estilos XSL chamada **freb.xsl** é fornecido no mesmo diretório que os ficheiros XML. Abrir um dos ficheiros XML no Internet Explorer irá utilizar para fornecer uma apresentação formatada as informações de rastreio de folha de estilos XSL. Isto irá aparecer semelhante ao seguinte:

![pedidos falhados visualizados no browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Registos de erros detalhados
Registos de erros detalhados são documentos HTML que fornecem informações mais detalhadas sobre os erros HTTP que ocorreram. Uma vez que são simplesmente os documentos HTML, podem ser vistos utilizando um browser.

### <a name="web-server-logs"></a>Registos do servidor Web
Os registos do servidor web são formatados com o [formato de ficheiro de registo expandido de W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Estas informações podem ser lidos com um editor de texto ou analisar a utilizar utilitários como [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Os registos produzidos pelas aplicações web do Azure não suportam o **s-computername**, **s-ip**, ou **cs-version** campos.
>
>

## <a name="nextsteps"></a> Passos seguintes
* [Como monitorizar aplicações Web](web-sites-monitor.md)
* [Resolução de problemas de aplicações web do Azure no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Analisar registos de aplicações web no HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
>
>

## <a name="whats-changed"></a>O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte: [App Service do Azure e o Respetivo Impacto nos Serviços do Azure Existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia da alteração do portal antigo para o novo portal, consulte: [referência para navegação do portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
