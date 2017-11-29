---
title: Exportar para o Power BI do Azure Application Insights | Microsoft Docs
description: "As consultas de análises podem ser apresentadas no Power BI."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: 19595983ba49a88d9139c85afbf38d3106d4a81d
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Feed do Power BI do Application Insights
[Power BI](http://www.powerbi.com/) é um conjunto de ferramentas de business, que o ajuda a analisar os dados e partilhar informações. Dashboards avançados estão disponíveis em todos os dispositivos. Pode combinar dados de várias origens, incluindo consultas de análises de [Azure Application Insights](app-insights-overview.md).

Existem três métodos recomendados para exportar dados do Application Insights para o Power BI. Pode utilizá-los em separado ou em conjunto.

* [**Adaptador do Power BI**](#power-pi-adapter). Configure um dashboard completo da telemetria da sua aplicação. O conjunto de gráficos está predefinido, mas pode adicionar as suas próprias consultas de quaisquer outras fontes.
* [**Exportar as consultas de análises**](#export-analytics-queries). Escrever qualquer consulta que pretende e exportá-lo para o Power BI. Pode escrever a consulta utilizando a análise de ou escrevê-los a partir de Funnels de utilização. Pode colocar esta consulta num dashboard, juntamente com quaisquer outros dados.
* [**A exportação contínua e Azure Stream Analytics**](app-insights-export-stream-analytics.md). Este método é útil se pretender manter os seus dados por períodos longos. Caso contrário, utilize um dos outros métodos, porque este envolve mais tarefas para configurar.

## <a name="power-bi-adapter"></a>Adaptador do Power BI
Este método cria um dashboard completo de telemetria para si. O conjunto de dados inicial está predefinido, mas pode adicionar mais dados ao mesmo.

### <a name="get-the-adapter"></a>Obter o adaptador
1. Inicie sessão no [Power BI](https://app.powerbi.com/).
2. Abra **obter dados**, **serviços**e, em seguida, **Application Insights**.
   
    ![Capturas de ecrã da introdução da origem de dados do Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Forneça os detalhes do recurso do Application Insights.
   
    ![Captura de ecrã da introdução da origem de dados do Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Aguarde um minuto ou dois para que os dados a ser importados.
   
    ![Adaptador de captura de ecrã do Power BI](./media/app-insights-export-power-bi/010.png)

Pode editar o dashboard, combinar os gráficos do Application Insights com as outras origens e com as consultas de análises. Pode obter mais gráficos na Galeria de visualização e cada gráfico tem os parâmetros que pode definir.

Após a importação inicial, o dashboard e os relatórios continuarem a atualizar diariamente. Pode controlar a agenda de atualização no conjunto de dados.

## <a name="export-analytics-queries"></a>Consultas de análises de exportação
Esta rota permite-lhe escrever qualquer consulta de análise, como ou, exportar a partir de utilização Funnels e, em seguida, exportar que a um dashboard do Power BI. (Pode adicionar o dashboard criado pela placa).

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar o Power BI Desktop
Para importar a sua consulta do Application Insights, pode utilizar a versão de ambiente de trabalho do Power BI. Em seguida, pode publicá-lo na Web ou a sua área de trabalho de nuvem do Power BI. 

Instalar [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta de análise
1. [Abra a análise e escrever a consulta](app-insights-analytics-tour.md).
2. Testar e refine a consulta enquanto estiver satisfeito com os resultados. Certifique-se de que a consulta é executada corretamente no Analytics antes de exportá-lo.
3. No **exportar** menu, escolha **Power BI (M)**. Guarde o ficheiro de texto.
   
    ![Captura de ecrã da análise, com o menu de exportação realçado](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. No Power BI Desktop, selecione **obter dados** > **consulta em branco**. Em seguida, no editor de consultas em **vista**, selecione **avançadas Editor**.

    Cole o script de idioma M exportado para o Editor de avançadas.

    ![Captura de ecrã do Power BI Desktop, com o Editor avançadas realçado](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Para permitir que o Power BI para aceder ao Azure, poderá ter de fornecer credenciais. Utilize **conta institucional** para iniciar sessão com a sua conta Microsoft.
   
    ![Caixa de diálogo de captura de ecrã do Power BI as definições da consulta](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Se precisar de verificar as credenciais, utilize o **definições da origem de dados** comandos de menu no editor de consultas. Lembre-se de que especifique as credenciais a que utilizar para o Azure, o que pode ser diferente das suas credenciais para o Power BI.
2. Escolha uma visualização para a sua consulta e selecionar os campos para o eixo x, eixo y e segmentar dimensão.
   
    ![Opções de vizualization de captura de ecrã do Power BI Desktop](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publica o relatório para a sua área de trabalho de nuvem do Power BI. A partir daí, pode incorporar uma versão sincronizada para outras páginas web.
   
    ![Captura de ecrã do Power BI Desktop, com o botão publicar realçado](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Atualizar o relatório manualmente intervalos ou configurar uma atualização agendada na página de opções.

### <a name="export-a-funnel"></a>Exportar um funil
1. [Tornar a sua funil](usage-funnels.md).
2. Selecione **Power BI**. 

   ![Botão de captura de ecrã do Power BI](./media/app-insights-export-power-bi/button.png)
   
3. No Power BI Desktop, selecione **obter dados** > **consulta em branco**. Em seguida, no editor de consultas em **vista**, selecione **avançadas Editor**.

   ![Captura de ecrã do Power BI Desktop, com o botão de consulta em branco realçado](./media/app-insights-export-power-bi/blankquery.png)

   Cole o script de idioma M exportado para o Editor de avançadas. 

   ![Captura de ecrã do Power BI Desktop, com o Editor avançadas realçado](./media/app-insights-export-power-bi/advancedquery.png)

4. Selecionar itens da consulta e escolha uma visualização de funil.

   ![Opções de vizualization de captura de ecrã do Power BI Desktop](./media/app-insights-export-power-bi/selectsequence.png)

5. Alterar o título para torná-lo com significado e publicar o relatório para a sua área de trabalho de nuvem do Power BI. 

   ![Captura de ecrã do Power BI Desktop, com alteração de título realçada](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Resolução de problemas

Poderá encontrar erros relacionados com as credenciais ou o tamanho do conjunto de dados. Eis algumas informações sobre o que fazer sobre estes erros.

### <a name="unauthorized-401-or-403"></a>Não autorizado (401 ou 403)
Isto pode acontecer se não tiver sido atualizado o seu token de atualização. Repita estes passos para garantir que continuam a ter acesso:

1. Inicie sessão no portal do Azure e certifique-se de que pode aceder ao recurso.
2. Tente atualizar as credenciais para o dashboard.

 Se tiver acesso e atualizar as credenciais não funcionar, abra um pedido de suporte.

### <a name="bad-gateway-502"></a>Gateway incorreto (502)
Isto é habitualmente provocado por uma consulta de análise que devolve demasiados dados. Tente utilizar um intervalo de tempo mais pequeno para a consulta. 

Se reduzir o conjunto de dados provenientes da consulta de análise não cumpre os requisitos, considere utilizar o [API](https://dev.applicationinsights.io/documentation/overview) para solicitar a um conjunto de dados maior. Eis como converter a exportação de consulta M para utilizar a API.

1. Criar um [chave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Atualize o script de Power BI M que exportou a partir da análise, substituindo o URL do Gestor de recursos do Azure com a API do Application Insights.
   * Substitua **https://management.azure.com/subscriptions/...**
   * com **https://api.applicationinsights.io/beta/apps/...**
3. Por fim, Atualize as credenciais para básico e utilizar a sua chave de API.
  

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre a amostragem
Se a sua aplicação enviar uma grande quantidade de dados, pode querer utilizar a funcionalidade de amostragem adaptável, o que envia apenas uma percentagem da sua telemetria. O mesmo se aplica se tiver definido manualmente amostragem no SDK ou em ingestão. [Saiba mais sobre amostragem](app-insights-sampling.md).


## <a name="next-steps"></a>Passos seguintes
* [Power BI - Saiba mais](http://www.powerbi.com/learning/)
* [Tutorial de análise](app-insights-analytics-tour.md)

