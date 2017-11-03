---
title: Exportar para o SQL do Azure Application Insights | Microsoft Docs
description: Continuamente exporte dados do Application Insights para o SQL Server utilizando o Stream Analytics.
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
editor: mrbullwinkle
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/06/2015
ms.author: mbullwin
ms.openlocfilehash: e935350fbcdeb7a3192778b3dafb288aac281886
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Instruções: Exportação para o SQL Server do Application Insights com o Stream Analytics
Este artigo mostra como mover os dados de telemetria [Azure Application Insights] [ start] para uma base de dados SQL do Azure utilizando [exportação contínua] [ export] e [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

A exportação contínua move os dados de telemetria para o armazenamento do Azure no formato JSON. Vamos analisar os objetos JSON utilizando o Azure Stream Analytics e criar linhas numa tabela da base de dados.

(Mais geralmente, a exportação contínua é a forma de fazer o seu próprio análise de telemetria a enviam as suas aplicações para o Application Insights. Foi adaptar este exemplo de código para efetuar outras ações com a telemetria exportada, tais como a agregação de dados.)

Iremos começar com pressuposto de que já tenha a aplicação que pretende monitorizar.

Neste exemplo, vamos utilizar os dados da vista de página, mas o mesmo padrão pode facilmente ser expandido para outros tipos de dados, tais como exceções e eventos personalizados. 

## <a name="add-application-insights-to-your-application"></a>Adicionar o Application Insights à sua aplicação
Para começar a utilizar:

1. [Configurar o Application Insights para as suas páginas web](app-insights-javascript.md). 
   
    (Neste exemplo, iremos irá focar-se no processamento de dados de vista de página de browsers do cliente, mas também pode configurar Application Insights no lado do servidor do seu [Java](app-insights-java-get-started.md) ou [ASP.NET](app-insights-asp-net.md) aplicação e processar o pedido, dependência e outra telemetria do servidor.)
2. Publicar a aplicação e ver os dados de telemetria no recurso do Application Insights.

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
A exportação contínua sempre produz dados para uma conta de armazenamento do Azure, por isso terá de criar primeiro o armazenamento.

1. Criar uma conta de armazenamento na sua subscrição no [portal do Azure][portal].
   
    ![No portal do Azure, escolha novo, dados, de armazenamento. Selecione clássico, escolha o criar. Forneça um nome de armazenamento.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione de contentores, clique em mosaico de contentores e, em seguida, adicionar](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)
3. Copie a chave de acesso de armazenamento
   
    Irá precisar das mesmas em breve para configurar a entrada para o serviço de análise de fluxo.
   
    ![No armazenamento, abra as definições, as chaves e efetuar uma cópia da chave de acesso primária](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar a exportação contínua ao armazenamento do Azure
1. No portal do Azure, navegue para o recurso do Application Insights que criou para a sua aplicação.
   
    ![Escolha procurar, Application Insights, a aplicação](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)
2. Crie uma exportação contínua.
   
    ![Escolha as definições, a exportação contínua, adicione](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    Defina os tipos de eventos que pretende ver:

    ![Escolha tipos de evento](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


1. Permitir que alguns dados a acumularem. Manter-se novamente e permitir que as pessoas a utilizar a aplicação de tempo. Telemetria ficará e irá ver gráficos análises [explorer métrica](app-insights-metrics-explorer.md) e eventos individuais no [pesquisa de diagnóstico](app-insights-diagnostic-search.md). 
   
    Além disso, os dados serão de exportação e para o armazenamento. 
2. Inspecione os dados exportados, no portal - escolher **procurar**, selecione a sua conta de armazenamento e, em seguida, **contentores** - ou no Visual Studio. No Visual Studio, selecione **ver / nuvem Explorer**e a abrir Azure / armazenamento. (Se não tiver esta opção de menu, tem de instalar o SDK do Azure: Abra a caixa de diálogo novo projeto e abra o Visual c# / nuvem / obter o Microsoft Azure SDK para .NET.)
   
    ![No Visual Studio, abra o Browser do servidor, o Azure, o armazenamento](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Anote a parte do nome do caminho, o que é derivado da chave de instrumentação e o nome de aplicação comum. 

Os eventos são escritos para o blob ficheiros no formato JSON. Cada ficheiro pode conter um ou mais eventos. Por isso, gostaríamos de ler os dados de eventos e filtrar os campos que queremos. Existem todos os tipos de coisas que pode efetuar com os dados, mas o nosso plano hoje consiste em utilizar o Stream Analytics para mover os dados para uma base de dados do SQL Server. Que facilite a executar muitas das consultas interessantes.

## <a name="create-an-azure-sql-database"></a>Criar uma base de dados SQL do Azure
Novamente a partir da sua subscrição no [portal do Azure][portal], criar a base de dados (e um novo servidor, a menos que já já tem um) que irá escrever os dados.

![Novo, dados, o SQL Server](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)

Certifique-se de que o servidor de base de dados permite o acesso aos serviços do Azure:

![Procurar, servidores, o servidor, definições, Firewall, permitir o acesso ao Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Criar uma tabela na BD SQL do Azure
Ligar à base de dados que criou na secção anterior com a ferramenta de gestão preferenciais. Esta explicação passo a passo, vamos utilizar [ferramentas de gestão do SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Criar uma nova consulta e execute o seguinte T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

Neste exemplo, estamos a utilizar dados a partir de vistas de página. Para ver os dados disponíveis, Inspecione a saída JSON e vê o [exportar o modelo de dados](app-insights-export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância do Azure Stream Analytics
Do [Portal clássico do Azure](https://manage.windowsazure.com/), selecione o serviço Azure Stream Analytics e criar uma nova tarefa de Stream Analytics:

![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)

![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

Quando é criada a nova tarefa, expanda os respectivos detalhes:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### <a name="set-blob-location"></a>Definir localização de blob
Defina-o para tomar entrada a partir do seu blob exportação contínua:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Agora, terá a chave de acesso primária da sua conta de armazenamento, que anotou anteriormente. Defina esta opção como a chave de conta de armazenamento.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### <a name="set-path-prefix-pattern"></a>Padrão de prefixo do caminho de conjunto
![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Lembre-se de que definiu o formato de data como **aaaa-MM-DD** (com **traços**).

O padrão de prefixo do caminho Especifica como o Stream Analytics localiza os ficheiros de entrada no armazenamento. Tem de defini-lo para corresponder à forma como a exportação contínua armazena os dados. Defina-o como esta:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27`é o nome do recurso do Application Insights, **todo em minúsculas**. 
* `1234...`é a chave de instrumentação do recurso Application Insights **com traços removidos**. 
* `PageViews`é o tipo de dados que pretende analisar. Os tipos disponíveis dependem do filtro definido na exportação contínua. Examine os dados exportados, consulte os outros tipos disponíveis e ver o [exportar o modelo de dados](app-insights-export-data-model.md).
* `/{date}/{time}`um padrão é escrito literalmente.

Para obter o nome e a iKey do recurso do Application Insights, abra Essentials na sua página de descrição geral ou abra definições.

#### <a name="finish-initial-setup"></a>Concluir a configuração inicial
Confirme o formato de serialização:

![Confirmar e fechar o Assistente](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Fechar o assistente e aguarde a conclusão da configuração.

> [!TIP]
> Utilize a função de exemplo para verificar que tem de definir o caminho de entrada corretamente. Se não conseguir: Verifique se há dados com o armazenamento para o intervalo de tempo de exemplo que escolheu. Editar a definição de entrada e verificar a definir a conta de armazenamento, o prefixo do caminho e data formato corretamente.
> 
> 

## <a name="set-query"></a>Consulta de conjunto
Abra a secção de consulta:

![Do stream analytics, selecione a consulta](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Substitua a consulta predefinida com:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Tenha em atenção que o primeiro algumas propriedades são específicas para dados da vista de página. Exportações de outros tipos de telemetria tem propriedades diferentes. Consulte o [detalhadas referência de modelo de dados para os tipos de propriedade e valores.](app-insights-export-data-model.md)

## <a name="set-up-output-to-database"></a>Configurar a saída à base de dados
Selecione o SQL Server como o resultado.

![Do stream analytics, selecione saídas](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Especifique a base de dados do SQL Server.

![Preencha os detalhes da base de dados](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Fechar o assistente e aguarde que uma notificação que o resultado foi configurado.

## <a name="start-processing"></a>Iniciar o processamento
Inicie a tarefa na barra de ação:

![Do stream analytics, clique em Iniciar](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

Pode escolher se pretende iniciar o processamento dos dados a partir de agora ou em anteriores para começar com dados. A última opção é útil se tiver tido a exportação contínua já em execução para um pouco.

![Do stream analytics, clique em Iniciar](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Após alguns minutos, volte atrás para ferramentas de gestão do SQL Server e veja os dados que fluem no. Por exemplo, utilize uma consulta como esta:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Artigos relacionados
* [Exportar para o Power BI utilizando o Stream Analytics](app-insights-export-power-bi.md)
* [Referência para os tipos de propriedade e os valores de modelo de dados de detalhado.](app-insights-export-data-model.md)
* [Exportação contínua no Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

