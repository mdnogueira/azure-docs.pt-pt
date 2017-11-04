---
title: Exportar a utilizar o Stream Analytics do Azure Application Insights | Microsoft Docs
description: "Do Stream Analytics continuamente pode transformar, filtrar e encaminhar os dados de que exportação do Application Insights."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: 978af1a57a5fc3d9c95d517288a074c636874984
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Utilizar o Stream Analytics para processar dados exportados do Application Insights
[O Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) é a ferramenta ideal para processamento de dados [exportados a partir do Application Insights](app-insights-export-telemetry.md). Do Stream Analytics pode solicitar dados a partir de uma variedade de origens. Pode transformar e filtre os dados e, em seguida, encaminhar para uma variedade de sinks.

Neste exemplo, vamos criar um adaptador que utiliza os dados do Application Insights, muda e processa alguns dos campos e encaminha-lo para o Power BI.

> [!WARNING]
> Existem muito melhor e mais fácil [recomendado formas para apresentar os dados do Application Insights no Power BI](app-insights-export-power-bi.md). O caminho ilustrado aqui é apenas um exemplo para ilustrar como processar os dados exportados.
> 
> 

![Diagrama de blocos para exportação através de SA para PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
A exportação contínua sempre produz dados para uma conta de armazenamento do Azure, por isso terá de criar primeiro o armazenamento.

1. Criar uma conta de armazenamento "clássico" na sua subscrição no [portal do Azure](https://portal.azure.com).
   
   ![No portal do Azure, escolha novo, dados, armazenamento](./media/app-insights-export-stream-analytics/030.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione de contentores, clique em mosaico de contentores e, em seguida, adicionar](./media/app-insights-export-stream-analytics/040.png)
3. Copie a chave de acesso de armazenamento
   
    Irá precisar das mesmas em breve para configurar a entrada para o serviço de análise de fluxo.
   
    ![No armazenamento, abra as definições, as chaves e efetuar uma cópia da chave de acesso primária](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar a exportação contínua ao armazenamento do Azure
[A exportação contínua](app-insights-export-telemetry.md) move os dados do Application Insights para o armazenamento do Azure.

1. No portal do Azure, navegue para o recurso do Application Insights que criou para a sua aplicação.
   
    ![Escolha procurar, Application Insights, a aplicação](./media/app-insights-export-stream-analytics/050.png)
2. Crie uma exportação contínua.
   
    ![Escolha as definições, a exportação contínua, adicione](./media/app-insights-export-stream-analytics/060.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/app-insights-export-stream-analytics/070.png)

    Defina os tipos de eventos que pretende ver:

    ![Escolha tipos de evento](./media/app-insights-export-stream-analytics/080.png)

1. Permitir que alguns dados a acumularem. Manter-se novamente e permitir que as pessoas a utilizar a aplicação de tempo. Telemetria ficará e irá ver gráficos análises [explorer métrica](app-insights-metrics-explorer.md) e eventos individuais no [pesquisa de diagnóstico](app-insights-diagnostic-search.md). 
   
    Além disso, os dados serão de exportação e para o armazenamento. 
2. Inspecione os dados exportados. No Visual Studio, selecione **ver / nuvem Explorer**e a abrir Azure / armazenamento. (Se não tiver esta opção de menu, tem de instalar o SDK do Azure: Abra a caixa de diálogo novo projeto e abra o Visual c# / nuvem / obter o Microsoft Azure SDK para .NET.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Anote a parte do nome do caminho, o que é derivado da chave de instrumentação e o nome de aplicação comum. 

Os eventos são escritos para o blob ficheiros no formato JSON. Cada ficheiro pode conter um ou mais eventos. Por isso, gostaríamos de ler os dados de eventos e filtrar os campos que queremos. Existem todos os tipos de coisas que pode efetuar com os dados, mas o nosso plano hoje consiste em utilizar o Stream Analytics para encaminhar os dados ao Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância do Azure Stream Analytics
Do [Portal clássico do Azure](https://manage.windowsazure.com/), selecione o serviço Azure Stream Analytics e criar uma nova tarefa de Stream Analytics:

![](./media/app-insights-export-stream-analytics/090.png)

![](./media/app-insights-export-stream-analytics/100.png)

Quando é criada a nova tarefa, expanda os respectivos detalhes:

![](./media/app-insights-export-stream-analytics/110.png)

### <a name="set-blob-location"></a>Definir localização de blob
Defina-o para tomar entrada a partir do seu blob exportação contínua:

![](./media/app-insights-export-stream-analytics/120.png)

Agora, terá a chave de acesso primária da sua conta de armazenamento, que anotou anteriormente. Defina esta opção como a chave de conta de armazenamento.

![](./media/app-insights-export-stream-analytics/130.png)

### <a name="set-path-prefix-pattern"></a>Padrão de prefixo do caminho de conjunto
![](./media/app-insights-export-stream-analytics/140.png)

**Lembre-se de que definiu o formato de data como aaaa-MM-DD (com traços).**

O padrão de prefixo do caminho Especifica onde o Stream Analytics localiza os ficheiros de entrada no armazenamento. Tem de defini-lo para corresponder à forma como a exportação contínua armazena os dados. Defina-o como esta:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27`é o nome do recurso Application Insights **todas as minúsculas**.
* `1234...`é a chave de instrumentação do recurso Application Insights, **omitindo traços**. 
* `PageViews`é o tipo de dados que pretende analisar. Os tipos disponíveis dependem do filtro definido na exportação contínua. Examine os dados exportados, consulte os outros tipos disponíveis e ver o [exportar o modelo de dados](app-insights-export-data-model.md).
* `/{date}/{time}`um padrão é escrito literalmente.

> [!NOTE]
> Inspecione o armazenamento para se certificar de que a obter o caminho correto.
> 
> 

### <a name="finish-initial-setup"></a>Concluir a configuração inicial
Confirme o formato de serialização:

![Confirmar e fechar o Assistente](./media/app-insights-export-stream-analytics/150.png)

Fechar o assistente e aguarde a conclusão da configuração.

> [!TIP]
> Utilize o comando de exemplo para transferir alguns dados. Mantenha-o como um exemplo de teste para depurar a sua consulta.
> 
> 

## <a name="set-the-output"></a>Definir o resultado
Agora, selecione a tarefa e definir a saída.

![Selecione o canal novo, clique em saídas, adicionar, Power BI](./media/app-insights-export-stream-analytics/160.png)

Forneça o **conta escolar ou profissional** para autorizar o Stream Analytics para aceder ao seu recurso do Power BI. Em seguida, invent um nome para a saída e para o conjunto de dados do destino Power BI e a tabela.

![Invent três nomes](./media/app-insights-export-stream-analytics/170.png)

## <a name="set-the-query"></a>Definir a consulta
A consulta regulam a tradução de entrada para a saída.

![Selecione a tarefa e clique em consulta. Cole o exemplo abaixo.](./media/app-insights-export-stream-analytics/180.png)

Utilize a função de teste para verificar que obtém o resultado da direita. Atribua os dados de exemplo demorou a partir da página de entradas. 

### <a name="query-to-display-counts-of-events"></a>Consulta para apresentar o número de eventos
Cole este consulta:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* exportação de entrada é o alias que forneceu no fluxo de entrada
* saída do pbi é o alias de saída que definimos
* Utilizamos [externa invocar GetElements aplicar](https://msdn.microsoft.com/library/azure/dn706229.aspx) porque o nome do evento está a ser um aninhada arrray JSON. Em seguida, selecionar escolhe o nome de evento, juntamente com uma contagem do número de instâncias com esse nome no período de tempo. O [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) cláusula agrupa os elementos para períodos de tempo de 1 minuto.

### <a name="query-to-display-metric-values"></a>Consulta para apresentar valores métricos
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Esta consulta vai desagregando para a telemetria de métricas para obter a hora do evento e o valor métrico. Os valores de métricos estão dentro de uma matriz, pelo que utilizamos o padrão de invocar GetElements aplicar externa para extrair as linhas. "myMetric" é o nome da métrica neste caso. 

### <a name="query-to-include-values-of-dimension-properties"></a>Consulta para incluir valores de propriedades de dimensão
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Esta consulta inclui os valores das propriedades de dimensão sem consoante a dimensão específica que está a ser num índice fixo na matriz da dimensão.

## <a name="run-the-job"></a>Executar a tarefa
Pode selecionar uma data no passado para iniciar a tarefa de. 

![Selecione a tarefa e clique em consulta. Cole o exemplo abaixo.](./media/app-insights-export-stream-analytics/190.png)

Aguarde até que a tarefa está em execução.

## <a name="see-results-in-power-bi"></a>Ver os resultados no Power BI
> [!WARNING]
> Existem muito melhor e mais fácil [recomendado formas para apresentar os dados do Application Insights no Power BI](app-insights-export-power-bi.md). O caminho ilustrado aqui é apenas um exemplo para ilustrar como processar os dados exportados.
> 
> 

Abrir o Power BI com o seu trabalho ou escola conta e selecione o conjunto de dados e a tabela que é definida como o resultado da tarefa de Stream Analytics.

![No Power BI, selecione o conjunto de dados e os campos.](./media/app-insights-export-stream-analytics/200.png)

Agora, pode utilizar este conjunto de dados nos relatórios e dashboards no [Power BI](https://powerbi.microsoft.com).

![No Power BI, selecione o conjunto de dados e os campos.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Não existem dados?
* Verifique que [definiu o formato da data](#set-path-prefix-pattern) corretamente para AAAA-MM-DD (com traços).

## <a name="video"></a>Vídeo
Noam Bernardo Zeev mostra como processar os dados exportados através do Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Exportação contínua](app-insights-export-telemetry.md)
* [Referência para os tipos de propriedade e os valores de modelo de dados de detalhado.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

