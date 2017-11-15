---
title: Exportar para o Power BI do Application Insights | Microsoft Docs
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
ms.openlocfilehash: ae204b79be228d55b30bb543dd25efdd9c3f0436
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Feed do Power BI do Application Insights
[Power BI](http://www.powerbi.com/) é um conjunto de ferramentas de análise de negócio que ajudam a analisar os dados e partilhar informações. Dashboards avançados estão disponíveis em todos os dispositivos. Pode combinar dados de várias origens, incluindo consultas de análises de [Azure Application Insights](app-insights-overview.md).

Existem três métodos recomendados para exportar dados do Application Insights para o Power BI. Pode utilizá-los em separado ou em conjunto.

* [**Adaptador do Power BI** ](#power-pi-adapter) -configurar um dashboard completo da telemetria da sua aplicação. O conjunto de gráficos está predefinido, mas pode adicionar as suas próprias consultas de quaisquer outras fontes.
* [**Exportar as consultas de análises** ](#export-analytics-queries) -escrever qualquer quiser através da análise de consulta ou a partir de Funnels utilização e exportá-lo para o Power BI. Pode colocar esta consulta num dashboard juntamente com quaisquer outros dados.
* [**A exportação contínua e Stream Analytics** ](app-insights-export-stream-analytics.md) -Isto envolve mais tarefas para configurar. Isto é útil se pretender manter os seus dados por períodos longos. Caso contrário, os outros métodos são recomendados.

## <a name="power-bi-adapter"></a>Adaptador do Power BI
Este método cria um dashboard completo de telemetria para si. O conjunto de dados inicial está predefinido, mas pode adicionar mais dados ao mesmo.

### <a name="get-the-adapter"></a>Obter o adaptador
1. Inicie sessão no [Power BI](https://app.powerbi.com/).
2. Abra **obter dados**, **serviços**, **Application Insights**
   
    ![Obter a partir da origem de dados do Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Forneça os detalhes do recurso do Application Insights.
   
    ![Obter a partir da origem de dados do Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Aguarde um minuto ou dois para que os dados a ser importados.
   
    ![Adaptador do Power BI](./media/app-insights-export-power-bi/010.png)

Pode editar o dashboard, combinar os gráficos do Application Insights com as outras origens e com as consultas de análises. Há uma galeria de visualização, onde pode obter mais gráficos e cada gráfico tem uma parâmetros que pode definir.

Após a importação inicial, o dashboard e os relatórios continuarem a atualizar diariamente. Pode controlar a agenda de atualização no conjunto de dados.

## <a name="export-analytics-queries"></a>Consultas de análises de exportação
Esta rota permite-lhe escrever qualquer consulta de análise, como ou exportar a partir de utilização Funnels e, em seguida, exportar que a um dashboard do Power BI. (Pode adicionar o dashboard criado pela placa).

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar o Power BI Desktop
Para importar a sua consulta do Application Insights, pode utilizar a versão de ambiente de trabalho do Power BI. Mas, em seguida, pode publicá-la na Web ou a sua área de trabalho de nuvem do Power BI. 

Instalar [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta de análise
1. [Abra a análise e escrever a consulta](app-insights-analytics-tour.md).
2. Testar e refine a consulta enquanto estiver satisfeito com os resultados.

   **Certifique-se de que a consulta é executada corretamente no Analytics antes de exportá-lo.**
3. No **exportar** menu, escolha **Power BI (M)**. Guarde o ficheiro de texto.
   
    ![Exportar a consulta do Power BI](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Em select do Power BI Desktop **obter dados, a consulta em branco** e, em seguida, no editor de consulta, em **vista** selecione **Editor de consultas avançada**.

    Cole o script de idioma M exportado num Editor de consultas avançada.

    ![Editor de consultas avançada](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Poderá ter de fornecer credenciais para permitir que o Power BI para aceder ao Azure. Utilizar 'conta institucional' Iniciar sessão com a sua conta Microsoft.
   
    ![Forneça credenciais do Azure para ativar o Power BI executar a consulta do Application Insights](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Se necessário verificar as credenciais, utilize o comando de menu de definições da origem de dados no Editor de consultas. Asseguramos para especificar as credenciais a que utilizar para o Azure, o que pode ser diferente das suas credenciais para o Power BI.)
2. Escolha uma visualização para a sua consulta e selecionar os campos para o eixo x, eixo y e segmentar dimensão.
   
    ![Selecione a visualização](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publica o relatório para a sua área de trabalho de nuvem do Power BI. A partir daí, pode incorporar uma versão sincronizada para outras páginas web.
   
    ![Selecione a visualização](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Atualizar o relatório manualmente intervalos ou configurar uma atualização agendada na página de opções.

### <a name="export-a-funnel"></a>Exportar um funil
1. [Tornar a sua funil](usage-funnels.md)
2. Clique no botão do Power BI 

   ![Botão de PowerBI](./media/app-insights-export-power-bi/button.png)
   
3. Em select do Power BI Desktop **obter dados, a consulta em branco** e, em seguida, no editor de consulta, em **vista** selecione **Editor de consultas avançada**.

   ![Consulta em branco](./media/app-insights-export-power-bi/blankquery.png)

   Cole o script de idioma M exportado num Editor de consultas avançada. 

   ![Editor de consultas avançada](./media/app-insights-export-power-bi/advancedquery.png)

4. Selecionar itens da consulta e escolha a visualização de funil

   ![Selecione a sequência e funil](./media/app-insights-export-power-bi/selectsequence.png)

5. Altere o título torná-lo com significado e publicar o relatório para a sua área de trabalho de nuvem do Power BI. 

   ![Alterar o título](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="401-or-403-unauthorized"></a>não autorizado 401 ou 403 
Isto pode acontecer se não tiver sido atualizado o seu token de atualização. Repita estes passos para garantir que continuam a ter acesso. Se tiver acesso e atualizar as credenciais não funcionar, abra um pedido de suporte.

1. Inicie sessão no Portal do Azure e certifique-se de que pode aceder ao recurso
2. Tente atualizar as credenciais para o Dashboard

### <a name="502-bad-gateway"></a>502 Gateway incorreto
Isto é habitualmente provocado por uma consulta de análise que devolve demasiados dados. Deve tentar utilizar um intervalo de tempo mais pequeno ou utilizando o [há](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#ago) ou [startofweek/startofmonth](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#startofweek) funciona apenas [projeto](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#project-operator) os campos que precisa.

Se reduzir o conjunto de dados provenientes da consulta de análise não cumpre os requisitos da sua deve considerar a utilização de [API](https://dev.applicationinsights.io/documentation/overview) para solicitar a um conjunto de dados maior. Seguem-se instruções sobre como converter a exportação de consulta M para utilizar a API.

1. Criar um [chave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)
2. Atualizar o script de Power BI M que exportou a partir da análise, substituindo o URL de ARM com a API de AI (consulte o exemplo abaixo)
   * Substitua **https://management.azure.com/subscriptions/...**
   * com **https://api.applicationinsights.io/beta/apps/...**
3. Por fim, Atualize as credenciais para básico e utilize a chave de API
  

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre a amostragem
Se a aplicação envia uma grande quantidade de dados, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma percentagem da sua telemetria. O mesmo se aplica se tiver definido manualmente amostragem no SDK ou em ingestão. [Saiba mais sobre a amostragem.](app-insights-sampling.md)


## <a name="next-steps"></a>Passos seguintes
* [Power BI - Saiba mais](http://www.powerbi.com/learning/)
* [Tutorial de análise](app-insights-analytics-tour.md)

