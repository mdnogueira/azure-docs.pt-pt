---
title: "Resolução de problemas de armazenamento do Azure com o diagnóstico & Message Analyzer | Microsoft Docs"
description: "Um tutorial que demonstra ponto-a-ponto de resolução de problemas com a análise de armazenamento do Azure, o AzCopy e o Microsoft Message Analyzer"
services: storage
documentationcenter: dotnet
author: tamram
manager: timlt
ms.assetid: 643372a3-1c07-4e88-b4ef-042512a43086
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.openlocfilehash: 13d01e63cfecdc826eba19b8eb0dc539019409dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Resolução de problemas de ponto-a-ponto utilizando as métricas do Storage do Azure e o registo, o AzCopy e o Message Analyzer
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Resolução de problemas e diagnosticar são uma chave skill para criar e suportar aplicações de cliente com o armazenamento do Microsoft Azure. Devido à natureza distribuída de uma aplicação do Azure, diagnosticar e resolução de problemas de erros e problemas de desempenho podem ser mais complexos do que em ambientes tradicionais.

Neste tutorial, iremos demonstrar como identificar determinados erros que possam afetar o desempenho e resolver os erros de ponto-a-ponto utilizando as ferramentas fornecidas pelo Microsoft e Storage do Azure, para otimizar a aplicação de cliente.

Este tutorial fornece uma exploração prática de um cenário de resolução de problemas de ponto a ponto. Para obter um Guia conceptual aprofundado resolução de problemas de aplicações de armazenamento do Azure, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para resolução de problemas de aplicações de armazenamento do Azure
Para resolver problemas de aplicações de cliente utilizando o armazenamento do Microsoft Azure, pode utilizar uma combinação de ferramentas para determinar quando ocorreu um problema e o que pode ser a causa do problema. Estas ferramentas incluem:

* **Análise de armazenamento do Azure**. [Análise de armazenamento do Azure](/rest/api/storageservices/Storage-Analytics) fornece métricas e registo para o Storage do Azure.
  
  * **As métricas do Storage** controla as métricas de transação e métricas de capacidade para a sua conta de armazenamento. Utilizar métricas, pode determinar como está a efetuar a sua aplicação, de acordo com uma variedade de medidas diferentes. Consulte [armazenamento esquema da tabela de métricas de análise](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) para obter mais informações sobre os tipos de métricas controladas pela análise de armazenamento.
  * **Registo de armazenamento** os registos de cada pedido para os serviços de armazenamento do Azure para um registo do lado do servidor. O registo controla dados detalhados para cada pedido, incluindo a operação efetuada, o estado da operação e informações de latência. Consulte [formato de registo de análise do armazenamento](/rest/api/storageservices/Storage-Analytics-Log-Format) para obter mais informações sobre os dados de pedido e resposta escrito nos registos de análise de armazenamento.

> [!NOTE]
> Contas de armazenamento com um tipo de replicação de com redundância de zona de armazenamento (ZRS) não têm as métricas ou a capacidade de registo neste momento ativada. 
> 
> 

* **Portal do Azure**. Pode configurar as métricas e registo para a sua conta de armazenamento na [portal do Azure](https://portal.azure.com). Também pode ver gráficos e gráficos que mostram a forma como a aplicação está a efetuar ao longo do tempo e configurar alertas para notificá-lo se a aplicação executa de forma diferente do que o previsto para uma métrica especificada.
  
    Consulte [monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md) para obter informações sobre como configurar a monitorização no portal do Azure.
* **AzCopy**. Registos do servidor para o Storage do Azure são armazenados como blobs, pelo que pode utilizar o AzCopy para copiar os blobs de registo para um diretório local para a análise com o Microsoft Message Analyzer. Consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md) para obter mais informações sobre o AzCopy.
* **Microsoft Message Analyzer**. Analisador de mensagens é uma ferramenta que consome os ficheiros de registo e apresenta dados de registo num formato visual que torna mais fácil para filtrar, pesquisa e dados de registo de grupo no útil define que pode utilizar para analisar os erros e problemas de desempenho. Consulte [Microsoft mensagem analisador operativo guia](http://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o Message Analyzer.

## <a name="about-the-sample-scenario"></a>Sobre o cenário de exemplo
Para este tutorial, vamos examinar um cenário onde as métricas do Storage do Azure indica uma taxa de êxito de percentagem baixa para uma aplicação que chama o armazenamento do Azure. A métrica de taxa de êxito de percentagem baixa (apresentada como **PercentSuccess** no [portal do Azure](https://portal.azure.com) e nas tabelas métricas) controla as operações que tenha êxito, mas que devolver um código de estado HTTP é superior ao 299. Nos ficheiros de registo do armazenamento do lado do servidor, estas operações são registadas com um Estado de transação de **ClientOtherErrors**. Para obter mais detalhes sobre a métrica de percentagem de êxito baixa, consulte [métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operações de armazenamento do Azure podem devolver os códigos de estado HTTP maior 299 como parte da respetiva funcionalidade normal. Mas estes erros em alguns casos indicam que poderá otimizar a aplicação de cliente para um melhor desempenho.

Neste cenário, iremos irá considerar uma taxa de êxito de percentagem baixa para ser qualquer coisa inferior a 100%. No entanto, pode escolher um nível de métrico diferentes, consoante as suas necessidades. Recomendamos que durante os testes da aplicação, estabeleça uma tolerância de linha de base para as métricas chave de desempenho. Por exemplo, poderá determinar, com base em testes, que a aplicação deve ter uma taxa de êxito de percentagem consistente de 90% ou 85%. Se os dados de métricas mostram que a aplicação é deviating desse número, em seguida, pode investigar o que poderá estar a causar o aumento.

Para o nosso cenário de exemplo, uma vez que estabeleceu a que a métrica de taxa de êxito por cento é inferior a 100%, iremos irá examine os registos para localizar os erros que está correlacionado com as métricas e utilizá-los para descobrir o que está a causar a taxa de êxito por cento inferior. Vamos analisar especificamente erros no intervalo entre 400. Em seguida, iremos irá investigar mais detalhadamente 404 erros (não for encontrado).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas dos erros de intervalo de 400
Os exemplos abaixo apresenta uma amostra de alguns erros de intervalo de 400 para pedidos contra o Blob Storage do Azure e das suas causas possíveis. Qualquer um destes erros, bem como erros no intervalo de 300 e 500 intervalo, este pode contribuir para uma taxa de êxito de percentagem baixa.

Tenha em atenção que as listas abaixo longe concluída. Consulte [estado e códigos de erro](http://msdn.microsoft.com/library/azure/dd179382.aspx) no MSDN para obter mais informações sobre erros gerais de armazenamento do Azure e sobre os erros específicos de cada um dos serviços de armazenamento.

**Exemplos de código 404 (não for encontrado) de estado**

Ocorre quando uma operação de leitura em relação a um contentor ou um blob falha porque o blob ou o contentor não foi encontrado.

* Ocorre se um contentor ou um blob foi eliminado por outro cliente antes deste pedido.
* Ocorre se estiver a utilizar uma chamada de API que cria o contentor ou um blob após verificar se existe. As APIs de CreateIfNotExists efetuar uma chamada de HEAD primeiro para verificar a existência do contentor ou blob; Se não existir, é devolvido um erro 404 e, em seguida, é efetuada uma chamada PUT segundo ao escrever o contentor ou um blob.

**409 (conflito) Exemplos de código de estado**

* Ocorre se utilizar uma API de criar para criar um novo contentor ou um blob, sem a verificar a existência de pela primeira vez, e já existe um contentor ou um blob com esse nome.
* Ocorre se está a ser eliminado um contentor e tentar criar um novo contentor com o mesmo nome antes da conclusão da operação de eliminação.
* Ocorre se especificar uma concessão no contentor ou blob e já houver uma concessão presente.

**Código de estado 412 (falhada de pré-condição) Exemplos**

* Ocorre quando a condição especificada por um cabeçalho condicional não foram cumprida.
* Ocorre quando o ID de concessão especificado não corresponde ao ID de concessão no contentor ou blob.

## <a name="generate-log-files-for-analysis"></a>Gerar ficheiros de registo de Analysis Services
Neste tutorial, iremos utilizar o Message Analyzer para trabalhar com três tipos diferentes de ficheiros de registo, embora pode optar por trabalhar com qualquer um dos seguintes:

* O **registo server**, que é criado quando ativar o registo de armazenamento do Azure. O registo de servidor contém dados sobre cada operação chamado em relação a um dos serviços de armazenamento do Azure - ficheiro, tabela, fila e blob. O registo do servidor indica que a operação foi chamada e o código de estado foi devolvidos, bem como outros detalhes sobre o pedido e resposta.
* O **registo de cliente .NET**, que é criado quando ativar o registo do lado do cliente de dentro da aplicação .NET. O registo de cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.
* O **registo de rastreio de rede HTTP**, que recolhe dados de HTTP/HTTPS pedido e resposta de dados, incluindo para operações de armazenamento do Azure. Neste tutorial, iremos irá gerar o rastreio de rede através do analisador de mensagens.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar registo do lado do servidor e métricas
Em primeiro lugar, vamos precisar de configurar o registo de armazenamento do Azure e métricas de, pelo que temos dados da aplicação cliente para analisar. Pode configurar o registo e as métricas numa variedade de formas - através de [portal do Azure](https://portal.azure.com), utilizando o PowerShell, ou através de programação. Consulte [ativar as métricas do Storage e visualizar dados de métricas](http://msdn.microsoft.com/library/azure/dn782843.aspx) e [aceder aos dados de registo e ativar o registo de armazenamento](http://msdn.microsoft.com/library/azure/dn782840.aspx) no MSDN para obter detalhes sobre como configurar o registo e as métricas.

**Através do portal do Azure**

Para configurar o registo e de métricas para o armazenamento de contas utilizando o [portal do Azure](https://portal.azure.com), siga as instruções apresentadas em [monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md).

> [!NOTE]
> Não é possível definir o minutos métricas no portal do Azure. No entanto, recomendamos que defina-las para efeitos deste tutorial e para investigar problemas de desempenho com a sua aplicação. Pode definir métricas minutos com o PowerShell, conforme mostrado abaixo ou através de programação utilizando a biblioteca de clientes de armazenamento.
> 
> Tenha em atenção que o portal do Azure não é possível a apresentação das métricas minutos, apenas as métricas por hora.
> 
> 

**Através do PowerShell**

Para começar a utilizar com o PowerShell para o Azure, consulte o artigo [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Utilize o [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0) cmdlet para adicionar a sua conta de utilizador do Azure para a janela do PowerShell:
   
    ```powershell
    Add-AzureAccount
    ```

2. No **iniciar sessão no Microsoft Azure** janela, escreva o endereço de correio eletrónico e a palavra-passe associada à sua conta. O Azure autentica e guarda as informações das credenciais e, em seguida, fecha a janela.
3. Defina a conta de armazenamento predefinido para a conta de armazenamento que está a utilizar para o tutorial executando estes comandos numa janela do PowerShell:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Ative o registo de armazenamento para o serviço Blob:
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. Ativar as métricas do storage para o serviço Blob, certificar-se de que definir **- MetricsType** para `Minute`:
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Configurar o registo do .NET do lado do cliente
Para configurar o registo do lado do cliente para uma aplicação .NET, ative os diagnósticos de .NET no ficheiro de configuração da aplicação (Web. config ou App. config). Consulte [do lado do cliente registo com a biblioteca de clientes de armazenamento de .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) e [do lado do cliente registo com o SDK de armazenamento do Microsoft Azure para Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) no MSDN para obter mais detalhes.

O registo do lado do cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.

A biblioteca de clientes de armazenamento armazena dados de registo do lado do cliente na localização especificada no ficheiro de configuração da aplicação (Web. config ou App. config).

### <a name="collect-a-network-trace"></a>Recolher um rastreio de rede
Pode utilizar o Message Analyzer para recolher um rastreio de rede HTTP/HTTPS enquanto a aplicação de cliente está em execução. Utiliza analisador de mensagens [Fiddler](http://www.telerik.com/fiddler) no back-end. Antes de recolher o rastreio de rede, recomendamos que configure o Fiddler para registar o tráfego HTTPS sem encriptação:

1. Instalar [Fiddler](http://www.telerik.com/download/fiddler).
2. Inicie o Fiddler.
3. Selecione **ferramentas | Opções de fiddler**.
4. Na caixa de diálogo Opções, certifique-se de que **capturar liga-se de HTTPS** e **desencriptar o tráfego HTTPS** estão ambos selecionadas, conforme mostrado abaixo.

![Configurar opções de Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

O tutorial, recolher e guardar um rastreio de rede pela primeira vez em Message Analyzer, em seguida, criar uma sessão de análise para analisar os registos e o rastreio. Para recolher um rastreio de rede Message Analyzer:

1. No Message Analyzer, selecione **ficheiro | Rastreio rápido | Não encriptada HTTPS**.
2. O rastreio será imediatamente iniciada. Selecione **parar** para parar o rastreio para que podemos configurá-lo para apenas o tráfego de armazenamento rastreio.
3. Selecione **editar** para editar a sessão de rastreio.
4. Selecione o **configurar** ligação à direita do **Microsoft-Pef-WebProxy** fornecedor ETW.
5. No **definições avançadas** caixa de diálogo, clique em de **fornecedor** separador.
6. No **Hostname filtro** campo, especifique os pontos finais de armazenamento, separados por espaços. Por exemplo, pode especificar pontos finais da sua forma; alterar `storagesample` para o nome da sua conta de armazenamento:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Sair da caixa de diálogo e clique em **reiniciar** para começar a recolher o rastreio com o filtro de nome de anfitrião no local, para que o tráfego de rede de armazenamento do Azure apenas está incluído no rastreio.

> [!NOTE]
> Depois de concluída a recolha de rastreio de rede, recomendamos vivamente que reverter as definições que possa ter alterado no Fiddler para desencriptar o tráfego HTTPS. Na caixa de diálogo Opções de Fiddler, desmarque a **capturar liga-se de HTTPS** e **desencriptar o tráfego HTTPS** caixas de verificação.
> 
> 

Consulte [utilizando as funcionalidades de rastreio de rede](http://technet.microsoft.com/library/jj674819.aspx) na Technet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-portal"></a>Reveja os dados de métricas no portal do Azure
Assim que a aplicação tem estado em execução durante um período de tempo, pode rever os gráficos de métricas que são apresentadas as [portal do Azure](https://portal.azure.com) para observar como o seu serviço tem sido executar.

Em primeiro lugar, navegue até à sua conta do storage no portal do Azure. Por predefinição, uma monitorização de gráfico com o **percentagem de êxito** métrica é apresentada no painel de conta. Se anteriormente tiver modificado o gráfico de apresentação das métricas diferentes, adicione o **percentagem de êxito** métrica.

Agora verá **percentagem de êxito** no gráfico de monitorização, juntamente com quaisquer outras métricas que possa ter adicionado. No cenário que vai investigar junto ao analisar os registos no Message Analyzer, a taxa de êxito por cento é um pouco inferior a 100%.

Para obter mais detalhes sobre a adição e personalizar os gráficos de métricas, consulte [personalizar gráficos de métricas](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Pode demorar algum tempo para os seus dados de métricas a aparecer no portal do Azure, depois de ativar as métricas do storage. Isto acontece porque as métricas de hora a hora para a hora anterior não são apresentadas no portal do Azure, até que a hora atual tiver decorrido. Além disso, métricas minutos atualmente não são apresentadas no portal do Azure. Por isso, consoante o quando ativar métricas, pode demorar até duas horas para ver dados de métricas.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilizar o AzCopy para copiar os registos do servidor para um diretório local
Armazenamento do Azure escreve dados de registo do servidor para blobs, enquanto as métricas são escritas tabelas. Os blobs de registo estão disponíveis no bem conhecidos `$logs` contentor para a sua conta de armazenamento. Registo blobs são denominados hierárquica por ano, mês, dia e hora, para que possa localizar facilmente o intervalo de tempo que pretende investigar. Por exemplo, no `storagesample` conta, o contentor de blobs de registo para 01/02/2015 de 9 de 8 am, é `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Os blobs neste contentor individuais são denominados sequencialmente, começando com `000000.log`.

Pode utilizar a ferramenta de linha de comandos do AzCopy para transferir estes ficheiros de registo do lado do servidor para uma localização da sua preferência no seu computador local. Por exemplo, pode utilizar o seguinte comando para transferir os ficheiros de registo para operações de BLOBs que demorou local em 2 de Janeiro de 2015 para a pasta `C:\Temp\Logs\Server`; substituir `<storageaccountname>` com o nome da sua conta do storage e `<storageaccountkey>` com a chave de acesso da conta:

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy está disponível para transferência no [Azure transfere](https://azure.microsoft.com/downloads/) página. Para obter mais informações sobre como utilizar o AzCopy, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).

Para obter informações adicionais sobre a transferir os registos do lado do servidor, consulte [registo do armazenamento de transferir dados de registo](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilizar o Microsoft Message Analyzer para analisar dados de registo
Microsoft Message Analyzer é uma ferramenta para capturar, visualizar e analisar o tráfego, eventos e outras mensagens de sistema ou aplicação em cenários de resolução de problemas e diagnóstico de mensagens de protocolo. Analisador de mensagens também permite-lhe carregar, Agregar e analisar dados de registo e guardar ficheiros de rastreio. Para obter mais informações sobre o Message Analyzer, consulte [Microsoft mensagem analisador operativo guia](http://technet.microsoft.com/library/jj649776.aspx).

Analisador de mensagens inclui recursos para o armazenamento do Azure que ajudam a analisar registos de rede, cliente e servidor. Nesta secção, iremos falar sobre como utilizar essas ferramentas para resolver o problema de sucesso percentagem baixo nos registos de armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Transfira e instale o Message Analyzer e dos recursos de armazenamento do Azure
1. Transferir [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) do Microsoft Centro de transferências e execute o instalador.
2. Inicie o analisador de mensagens.
3. Do **ferramentas** menu, selecione **Gestor do Asset Intelligence**. No **Gestor do Asset Intelligence** caixa de diálogo, selecione **transfere**, em seguida, filtre **Storage do Azure**. Irá ver os recursos de armazenamento do Azure, conforme mostrado na imagem abaixo.
4. Clique em **sincronização todos os itens apresentados** para instalar os recursos de armazenamento do Azure. Os recursos disponíveis incluem:
   * **Regras de cor de armazenamento do Azure:** as regras de cor de armazenamento do Azure permitem-lhe definir os filtros de especiais que utilizam os estilos de cor, o texto e o tipo de letra para realçar mensagens que contêm informações específicas num rastreio.
   * **Gráficos de armazenamento do Azure:** gráficos de armazenamento do Azure são gráficos predefinidos que graph dados de registo do servidor. Tenha em atenção que para utilizar gráficos de armazenamento do Azure neste momento, pode apenas carregar o registo de servidor para a grelha de análise.
   * **Parsers de armazenamento do Azure:** parsers de armazenamento do Azure analisar o cliente do Storage do Azure, o servidor e a registos de HTTP para as pode apresentar na grelha Analysis Services.
   * **Filtros de armazenamento do Azure:** filtros de armazenamento do Azure são critérios predefinidos que pode utilizar para consultar os dados na grelha Analysis Services.
   * **Esquemas de vista de armazenamento do Azure:** esquemas de vista de armazenamento do Azure são esquemas de coluna predefinida e agrupamentos na grelha Analysis Services.
5. Reinicie o Message Analyzer depois de instalar os ativos.

![Gestor do Asset Intelligence de analisador de mensagens](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Instale todos os recursos de armazenamento do Azure mostrados para efeitos deste tutorial.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>Importar os ficheiros de registo para o Message Analyzer
Pode importar todos os seus ficheiros de registo guardado (do lado do servidor, do lado do cliente e rede) para uma única sessão no Microsoft Message Analyzer para análise.

1. No **ficheiro** menu no Microsoft Message Analyzer, clique em **nova sessão**e, em seguida, clique em **sessão em branco**. No **nova sessão** caixa de diálogo, introduza um nome para a sua sessão do Analysis Services. No **detalhes da sessão** painel, clique em de **ficheiros** botão.
2. Para carregar os dados de rastreio de rede gerados pelo Message Analyzer, clique em **adicionar ficheiros**, navegue para a localização onde guardou o ficheiro .matp da sessão de rastreio web, selecione o ficheiro .matp e clique em **abra**.
3. Para carregar os dados de registo do lado do servidor, clique em **adicionar ficheiros**, navegue para a localização onde transferiu os registos do lado do servidor, selecione os ficheiros de registo para o intervalo de tempo que pretende analisar e clique em **abra**. Em seguida, no **detalhes da sessão** painel, defina o **configuração de registo de texto** pendente para cada ficheiro de registo do lado do servidor para **AzureStorageLog** para se certificar de que Microsoft Message Analyzer pode analisar o ficheiro de registo corretamente.
4. Para carregar os dados de registo do lado do cliente, clique em **adicionar ficheiros**, navegue para a localização onde guardou os registos do lado do cliente, selecione os ficheiros de registo que pretende analisar e clique em **abra**. Em seguida, no **detalhes da sessão** painel, defina o **configuração de registo de texto** pendente para cada ficheiro de registo do lado do cliente para **AzureStorageClientDotNetV4** para se certificar de que Microsoft Message Analyzer pode analisar o ficheiro de registo corretamente.
5. Clique em **iniciar** no **nova sessão** caixa de diálogo para carregar e analisar os dados de registo. Apresenta os dados de registo na grelha de análise do analisador de mensagens.

A imagem abaixo mostra uma sessão de exemplo configurada com o servidor, cliente e os ficheiros de registo de rastreio de rede.

![Configurar sessão do analisador de mensagens](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Tenha em atenção que o Message Analyzer carrega os ficheiros de registo na memória. Se tiver um grande conjunto de dados de registo, irá querer filtrá-la para obter o melhor desempenho do analisador de mensagens.

Em primeiro lugar, determine o intervalo de tempo que está interessado em rever e manter este período de tempo tão reduzida quanto possível. Em muitos casos irá querer rever durante um período de minutos ou horas no máximo. Importe o mais pequeno conjunto de registos que pode satisfazer as suas necessidades.

Se ainda tiver uma grande quantidade de dados de registo, em seguida, pode pretender especificar uma sessão de filtro para filtrar os dados de registo antes de carregá-lo. No **sessão filtro** caixa, selecione o **biblioteca** botão para escolher um filtro predefinido; por exemplo, escolha **Global tempo filtro I** o Storage do Azure filtros a filtrar um intervalo de tempo. Em seguida, pode editar os critérios de filtro para especificar a iniciar e terminar timestamp durante o intervalo de que pretende ver. Pode também filtrar por um código de estado específico; Por exemplo, pode optar por carregar apenas entradas de registo em que o código de estado é 404.

Para obter mais informações sobre como importar dados de registo para o Microsoft Message Analyzer, consulte [obter dados de mensagens](http://technet.microsoft.com/library/dn772437.aspx) no TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilize o ID do pedido de cliente para correlacionar dados de ficheiro de registo
Biblioteca de cliente do Storage do Azure gera automaticamente um ID de pedido de cliente exclusivos para cada pedido. Este valor é escrito no registo de cliente, o registo de servidor e o rastreio de rede, pelo que pode utilizá-lo para correlacionar dados em todos os registos de três dentro do analisador de mensagens. Consulte [ID do pedido de cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter informações adicionais sobre o cliente pedir ID.

As secções abaixo descrevem como utilizar vistas de esquema previamente configurada e personalizadas para correlacionar e dados de grupo com base no ID do pedido de cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecionar um esquema de vista para apresentar na grelha de análise
Os recursos de armazenamento para Message Analyzer incluem esquemas de vista de armazenamento de Azure, que são vistas pré-configuradas que pode utilizar para apresentar os dados com agrupamentos útil e colunas para diferentes cenários. Também pode criar esquemas de vista personalizada e guardá-las para serem reutilizadas.

A imagem abaixo mostra o **esquema de vista** menu, disponível selecionando **esquema de vista** a partir do Friso barra de ferramentas. As esquemas de vista de armazenamento do Azure são agrupadas sob o **Storage do Azure** nó no menu. Pode procurar `Azure Storage` na caixa de pesquisa para filtrar no armazenamento do Azure ver apenas os esquemas. Também pode selecionar a estrela junto a um esquema de vista para tornar um favorito e apresente-a na parte superior do menu.

![Visualizar o menu de esquema](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Selecione a lugar com **agrupados por ClientRequestID e módulo**. Grupos de esquema esta vista de registo dados a partir de todos os registos de três primeiro por ID de pedido de cliente, em seguida, ao ficheiro de registo de origem (ou **módulo** no Message Analyzer). Com esta vista, pode desagregar um ID de pedido de cliente específico e ver os dados todos os três ficheiros de registo para esse pedido de cliente ID.

A imagem abaixo mostra esta vista de esquema aplicada para os dados de registo de exemplo, com um subconjunto de colunas apresentadas. Pode ver que, para um ID de pedido de cliente específico, a grelha de análise apresenta dados do registo de cliente, o registo de servidor e o rastreio de rede.

![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Ficheiros de registo diferentes têm colunas diferentes, pelo que, quando os dados de vários ficheiros de registo são apresentados na grelha de análise, algumas colunas não podem conter quaisquer dados para uma linha fornecida. Por exemplo, na imagem acima, as linhas de registo de cliente não mostram todos os dados para o **Timestamp**, **TimeElapsed**, **origem**, e **destino** colunas, porque estas colunas não existe no registo de cliente, mas existe no rastreio de rede. Da mesma forma, o **Timestamp** coluna apresenta dados de carimbo do registo de servidor, mas não serem apresentados dados para o **TimeElapsed**, **origem**, e **destino** colunas, o que não façam parte do registo do servidor.
> 
> 

Além de utilizar as esquemas de vista de armazenamento do Azure, também pode definir e guarde as seus próprios esquemas de vista. Pode selecionar outros campos para agrupar dados pretendidos e guardar o agrupamento como parte do seu esquema de personalizado.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar as regras de cor à grelha de análise
Os recursos de armazenamento também incluir regras de cor, o que oferecem que um visual significa identificar os diferentes tipos de erros na grelha Analysis Services. As regras de cor predefinida aplicam-se para erros de HTTP, para que serem apresentados apenas para o rastreio de rede e de registo do servidor.

Para aplicar as regras de cor, selecione **as regras de cor** a partir do Friso barra de ferramentas. Verá as regras de cor de armazenamento do Azure no menu. Para o tutorial, selecione **erros de cliente (StatusCode compreendido entre 400 e 499)**, conforme mostrado na imagem abaixo.

![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Além de utilizar as regras de cor de armazenamento do Azure, também pode definir e guardar as suas próprias regras de cor.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Dados de registo de grupo e filtrar para localizar o intervalo de 400 erros
Em seguida, vamos grupo e filtre os dados de registo para localizar todos os erros no intervalo entre 400.

1. Localize o **StatusCode** coluna na grelha de análise, com a coluna cabeçalho e selecione o botão direito **grupo**.
2. Em seguida, grupo no **ClientRequestId** coluna. Verá que os dados na grelha Analysis agora estão organizados por código de estado e por ID de pedido do cliente.
3. Visualizar a janela de ferramenta de filtro de vista se já não for apresentado. No Friso barra de ferramentas, selecione **ferramenta Windows**, em seguida, **vista filtro**.
4. Para filtrar os dados de registo para apresentar apenas os erros de 400-range, adicione os seguintes critérios de filtro para o **vista filtro** janela e clique em **aplicar**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

A imagem abaixo mostra os resultados deste agrupamento e o filtro. Expandir o **ClientRequestID** campo abaixo o agrupamento de código de estado 409, por exemplo, mostra uma operação que resultaram nesse código de estado.

![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Depois de aplicar este filtro, verá que as linhas a partir do registo do cliente são excluídas, como o cliente do registo não inclui um **StatusCode** coluna. A lugar com, iremos deverá consultar o servidor e os registos de rastreio de rede para localizar 404 erros e, em seguida, iremos devolver o registo de cliente para examinar as operações de cliente que conduziram aos mesmos.

> [!NOTE]
> Pode filtrar o **StatusCode** coluna ainda apresentar dados e de todos os registos de três, incluindo o registo de cliente, se adicionar uma expressão para o filtro que inclui entradas de registo em que o código de estado é nulo. Para construir esta expressão de filtro, utilize:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Este filtro devolve todas as linhas do cliente registo e apenas as linhas do registo de servidor e do registo HTTP onde o código de estado é superior ao 400. Se aplicá-la para o esquema de vista agrupado por ID de pedido de cliente e o módulo, pode procurar ou desloque-se através de entradas de registo para localizar os onde todos os registos de três são representados.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Filtre os dados de registo para localizar 404 erros
Os recursos de armazenamento incluem filtros predefinidos que pode utilizar para restringir dados de registo para localizar os erros ou tendências que procura. Em seguida, iremos irá aplicar dois filtros predefinidos: que filtra o servidor e os registos de rastreio de rede 404 erros e, que filtra os dados de um intervalo de tempo especificado.

1. Visualizar a janela de ferramenta de filtro de vista se já não for apresentado. No Friso barra de ferramentas, selecione **ferramenta Windows**, em seguida, **vista filtro**.
2. Na janela Filtro de vista, selecione **biblioteca**e uma procura `Azure Storage` para localizar o Storage do Azure filtros. Selecione o filtro para **404 (não for encontrado) de mensagens em todos os registos**.
3. Apresentar o **biblioteca** menu novo e localize e selecione o **filtro de tempo Global**.
4. Edite os carimbos mostrados no filtro para o intervalo de que pretende ver. Isto irá ajudar a reduzir o intervalo de dados para analisar.
5. O filtro deve ser semelhante ao exemplo abaixo. Clique em **aplicar** para aplicar o filtro à grelha de análise.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analisar os dados de registo
Agora que tem agrupados e filtrar os dados, pode examinar os detalhes dos pedidos individuais que geraram 404 erros. O esquema de vista atual, os dados são agrupados por ID de pedido de cliente, em seguida, pela origem de registo. Uma vez que vamos está a filtrar nos pedidos em que o campo de StatusCode contém 404, iremos ver apenas o servidor e os dados de rastreio de rede, não os dados de registo do cliente.

A imagem abaixo mostra um pedido específico em que uma operação de Blob obter geraram um 404, porque o blob não existe. Tenha em atenção que algumas colunas foram removidas da vista de padrão para apresentar os dados relevantes.

![Servidor filtrado e registos de rastreio de rede](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Em seguida, iremos irá correlacionar este ID de pedido de cliente com os dados de registo de cliente para ver as ações que o cliente estava a demorar quando ocorreu o erro. Pode visualizar uma nova vista de grelha de análise para esta sessão ver os dados de registo do cliente, que abre num separador segundo:

1. Em primeiro lugar, copie o valor do **ClientRequestId** campo para a área de transferência. Pode fazê-selecionar qualquer uma das linhas, localizar o **ClientRequestId** campo, clicar com o valor de dados e escolher **cópia 'ClientRequestId'**.
2. No Friso barra de ferramentas, selecione **novo Visualizador**, em seguida, selecione **Analysis grelha** para abrir um novo separador. O novo separador mostra todos os dados nos seus ficheiros de registo, sem agrupamento, filtragem ou as regras de cor.
3. No Friso barra de ferramentas, selecione **esquema de vista**, em seguida, selecione **todas as colunas de cliente .NET** sob o **Storage do Azure** secção. Este esquema de vista mostra os dados do cliente registo, bem como os registos de rastreio do servidor e da rede. Por predefinição é ordenada no **MessageNumber** coluna.
4. Em seguida, procure o registo de cliente para o ID de pedido de cliente. No Friso barra de ferramentas, selecione **localizar mensagens**, em seguida, especifique um filtro personalizado no ID de pedido de cliente no **localizar** campo. Utilize esta sintaxe de filtro, especificando o seu próprio ID do pedido de cliente:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Analisador de mensagens localiza e seleciona a primeira entrada de registo em que os critérios de pesquisa corresponde ao ID do pedido de cliente. No registo de cliente, existem várias entradas para cada ID de pedido de cliente, pelo que poderá querer agrupá-los no **ClientRequestId** campo para tornar mais fácil para vê-los a todos os em conjunto. A imagem abaixo mostra todas as mensagens no registo de cliente para o cliente especificado ID do pedido.

![Erros de 404 de apresentação do registo de cliente](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Utilizar os dados apresentados as esquemas de ver estes dois separadores, pode analisar os dados de pedido para determinar o que poderá ter causado o erro. Também pode ver pedidos que precedido este um para ver se um evento anterior poderá resultaram o erro 404. Por exemplo, pode rever as entradas de registo do cliente anterior a este ID de pedido de cliente para determinar se o blob pode ter sido eliminado ou se o erro se a aplicação de cliente chamar uma API CreateIfNotExists num contentor ou blob devia. No registo de cliente, pode encontrar o endereço do blob no **Descrição** campo; no servidor e os registos de rastreio de rede, estas informações aparecem no **resumo** campo.

Quando souber o endereço do blob que geraram o erro 404, pode investigar mais. Se procurar as entradas de registo para outras mensagens associadas com operações no mesmo blob, pode verificar se o cliente eliminado anteriormente a entidade.

## <a name="analyze-other-types-of-storage-errors"></a>Analisar os outros tipos de erros de armazenamento
Agora que já está familiarizado com a utilização Message Analyzer para analisar os dados de registo, pode analisar outros tipos de erros de utilizar a vista de esquemas, as regras de cor e pesquisar/filtragem. As tabelas a seguir lista alguns problemas que pode encontrar e os critérios de filtro pode utilizar a localizá-las. Para obter mais informações sobre a construção de filtros e o Message Analyzer filtragem de idioma, consulte [dados de mensagens de filtragem](http://technet.microsoft.com/library/jj819365.aspx).

| Para investigar... | Utilize a expressão de filtro... | Expressão aplica-se no registo (cliente, de servidor, de rede, todos os) |
| --- | --- | --- |
| Atrasos inesperados na entrega de mensagens numa fila |AzureStorageClientDotNetV4.Description contém "Repetir falha na operação." |Cliente |
| Aumento de HTTP no PercentThrottlingError |PROTOCOLO HTTP. Response.StatusCode = = 500 &#124; &#124; PROTOCOLO HTTP. Response.StatusCode = = 503 |Rede |
| Aumentam PercentTimeoutError |PROTOCOLO HTTP. Response.StatusCode = = 500 |Rede |
| Aumentam PercentTimeoutError (todos) |* StatusCode = = 500 |Todos |
| Aumentam PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level < 2 |Cliente |
| Mensagens HTTP 403 (proibido) |PROTOCOLO HTTP. Response.StatusCode = = 403 |Rede |
| HTTP 404 (não for encontrado) mensagens |PROTOCOLO HTTP. Response.StatusCode = = 404 |Rede |
| 404 (todos) |* StatusCode = = 404 |Todos |
| Partilhado problema de autorização de assinatura de acesso (SAS) |AzureStorageLog.RequestStatus = = "SASAuthorizationError" |Rede |
| HTTP 409 (conflito) mensagens |PROTOCOLO HTTP. Response.StatusCode = = 409 |Rede |
| 409 (todos) |* StatusCode = = 409 |Todos |
| Entradas de registo PercentSuccess baixa ou análise tem operações com o estado de transação de ClientOtherErrors |AzureStorageLog.RequestStatus = = "ClientOtherError" |Servidor |
| Aviso de Nagle |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) e (AzureStorageLog.RequestPacketSize < 1460) e (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Servidor |
| Intervalo de tempo nos registos do servidor e da rede |#Timestamp > = 2014-10-20T16:36:38 e #Timestamp < = 2014-10-20T16:36:39 |Servidor de rede |
| Intervalo de tempo em registos do servidor |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 e AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Servidor |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os cenários de ponto a ponto de resolução de problemas no armazenamento do Azure, consulte estes recursos:

* [Monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
* [Análise de Armazenamento](http://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)
* [Guia de funcionamento de analisador de mensagens da Microsoft](http://technet.microsoft.com/library/jj649776.aspx)
