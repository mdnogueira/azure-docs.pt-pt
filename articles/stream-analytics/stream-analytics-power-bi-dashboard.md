---
title: Dashboard do Power BI no Azure Stream Analytics | Microsoft Docs
description: "Utilize um dashboard do Power BI de transmissão em fluxo em tempo real intelligence de negócio de recolher e analisar dados de elevado volume de uma tarefa de Stream Analytics."
keywords: "dashboard de análise, dashboard em tempo real"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2017
ms.author: samacha
ms.openlocfilehash: b446e2296f2747012849936b994c4a4a2044869e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics e o Power BI: um dashboard de análise em tempo real para transmissão em fluxo de dados
O Azure Stream Analytics permite-lhe tirar partido de um das principais ferramentas de business intelligence, [Microsoft Power BI](https://powerbi.com/). Neste artigo, saiba como criar ferramentas de business intelligence, utilizando o Power BI como uma saída para as tarefas do Azure Stream Analytics. Também irá aprender a criar e utilizar um dashboard em tempo real.

Este artigo continua a partir do Stream Analytics [deteção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) tutorial. Este baseia-se o fluxo de trabalho criado esse tutorial e adiciona um Power BI para que pode visualizar as chamadas telefónicas fraudulentas que são detetadas por uma tarefa de análise de transmissão em fluxo de saída. 

Pode ver [um vídeo](https://www.youtube.com/watch?v=SGUpT-a99MA) que ilustra este cenário.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* Uma conta do Power BI. Pode utilizar uma conta profissional ou de uma conta profissional.
* Uma versão completa do [deteção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) tutorial. O tutorial inclui uma aplicação que gera metadados fictícios de atribuição de chamada telefónica. O tutorial, pode criar um hub de eventos e enviar os dados de chamada telefónica de transmissão em fluxo para o hub de eventos. Escrever uma consulta que Deteta chamadas fraudulentas (chamadas do mesmo número em simultâneo em diferentes localizações). 


## <a name="add-power-bi-output"></a>Adicionar a saída do Power BI
Tutorial de deteção de fraudes em tempo real, o resultado é enviado para o armazenamento de Blobs do Azure. Nesta secção, adicione uma saída que envia informações para o Power BI.

1. No portal do Azure, abra a tarefa de análise de transmissão em fluxo que criou anteriormente. Se utilizou o nome sugerido, a tarefa com o nome `sa_frauddetection_job_demo`.

2. Selecione o **saídas** caixa no meio do dashboard de tarefa e, em seguida, selecione **+ adicionar**.

3. Para **Alias de saída**, introduza `CallStream-PowerBI`. Pode utilizar um nome diferente. Se o fizer, tome nota do mesmo, porque tem o nome de mais tarde. 

4. Em **Sink**, selecione **Power BI**.

   ![Criar um resultado para o Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Clique em **autorizar**.

    Abre uma janela onde pode fornecer as suas credenciais do Azure para uma conta escolar ou profissional. 

    ![Introduza as credenciais de acesso ao Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Introduza as suas credenciais. Tenha em atenção, em seguida, quando introduzir as suas credenciais, também está a dar permissão para a tarefa de análise de transmissão em fluxo para aceder à sua área de Power BI.

7. Quando estiver a devolvido para o **nova saída** painel, introduza as seguintes informações:

    * **Área de trabalho de grupo**: selecione uma área de trabalho no seu inquilino do Power BI onde pretende criar o conjunto de dados.
    * **Nome do conjunto de dados**: introduza `sa-dataset`. Pode utilizar um nome diferente. Se o fizer, anote-lo para utilizar mais tarde.
    * **Nome de tabela**: introduza `fraudulent-calls`. Atualmente, o resultado de Power BI de tarefas do Stream Analytics pode ter apenas uma tabela num conjunto de dados.

    ![Área de trabalho do PBI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Se o Power BI tem um conjunto de dados e a tabela que tem os mesmos nomes que aqueles que especificou na tarefa de Stream Analytics, os existentes serão substituídos.
    > Recomendamos que não explicitamente crie este conjunto de dados e a tabela na sua conta do Power BI. Estes são criados automaticamente quando iniciar a tarefa de Stream Analytics e a tarefa começa a saída de bombagem para o Power BI. Se a consulta da tarefa não devolve quaisquer resultados, o conjunto de dados e a tabela não são criados.
    >

8. Clique em **Criar**.

O conjunto de dados é criado com as seguintes definições:

* **defaultRetentionPolicy: BasicFIFO**: os dados são FIFO, com um máximo de 200 000 linhas.
* **defaultMode: pushStreaming**: O conjunto de dados suporta a transmissão em fluxo de mosaicos e visuais baseados no relatório tradicionais (a.k.a. Push).

Atualmente, não é possível criar conjuntos de dados com outros sinalizadores.

Para mais informações sobre conjuntos de dados do Power BI, consulte o [API de REST do Power BI](https://msdn.microsoft.com/library/mt203562.aspx) referência.


## <a name="write-the-query"></a>A consulta de escrita

1. Fechar o **saídas** painel e voltar ao painel de tarefas.

2. Clique em de **consulta** caixa. 

3. Introduza a seguinte consulta. Esta consulta é semelhante à consulta de associação automática que criou o tutorial de deteção de fraudes. A diferença é que esta consulta envia resultados para a nova saída criou (`CallStream-PowerBI`). 

    >[!NOTE]
    >Se não nome entrada `CallStream` tutorial deteção de fraudes, substitua o nome para `CallStream` no **FROM** e **associar** cláusulas na consulta.

        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))

4. Clique em **Guardar**.


## <a name="test-the-query"></a>A consulta de teste
Esta secção é opcional mas recomendado. 

1. Se a aplicação de TelcoStreaming ainda não atualmente em execução, inicie-o seguindo estes passos:

    * Abra uma janela de comando.
    * Ir para a pasta onde estão os ficheiros de telcodatagen.exe.config modificado e telcogenerator.exe.
    * Execute o seguinte comando:

            telcodatagen.exe 1000 .2 2

2. No **consulta** painel, clique nos pontos junto a `CallStream` de entrada e, em seguida, selecione **dados de entrada de exemplo**.

3. Especifique que pretende que o visão de três minutos de dados e clique em **OK**. Aguarde até que está a notificado de que os dados de amostragem.

4. Clique em **teste** e certifique-se de que está a obter resultados.


## <a name="run-the-job"></a>Executar a tarefa

1. Certifique-se de que a aplicação de TelcoStreaming está em execução.

2. Fechar o **consulta** painel.

3. No painel de tarefas, clique em **iniciar**.

    ![Iniciar a tarefa de Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

A tarefa de análise de transmissão em fluxo inicia à procura de chamadas fraudulentas no fluxo de entrada. A tarefa também cria o conjunto de dados e a tabela no Power BI e começa a enviar dados sobre as chamadas fraudulentas aos mesmos.


## <a name="create-the-dashboard-in-power-bi"></a>Criar o dashboard no Power BI

1. Aceda a [Powerbi.com](https://powerbi.com) e inicie sessão com a sua conta escolar ou profissional. Se a consulta da tarefa de Stream Analytics produz resultados, verá que o conjunto de dados já está a ser criado:

    ![Conjunto de dados de transmissão em fluxo no Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Na área de trabalho, clique em  **+ &nbsp;criar**.

    ![O botão para criar na área de trabalho do Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Criar um novo dashboard e dê-lhe nome `Fraudulent Calls`.

    ![Criar um dashboard e atribua um nome na área de trabalho do Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Na parte superior da janela, clique em **adicionar mosaico**, selecione **dados de transmissão em fluxo personalizados**e, em seguida, clique em **seguinte**.

    ![Personalizada conjunto de dados de transmissão em fluxo](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Em **YOUR DATSETS**, selecione o conjunto de dados e, em seguida, clique em **seguinte**.

    ![O conjunto de dados de transmissão em fluxo](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Em **tipo de visualização**, selecione **cartão**e, em seguida, no **campos** lista, selecione **fraudulentcalls**.

    ![Detalhes de visualização do novo mosaico](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Clique em **Seguinte**.

8. Preencha os detalhes de mosaico, como um título e subtítulo do.

    ![Título e subtítulo do novo mosaico](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Clique em **Aplicar**.

    Tem agora um contador de fraude!

    ![Contador de fraude](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Siga os passos para adicionar um mosaico (começando com o passo 4). Neste momento, efetue o seguinte:

    * Quando chegar a **tipo de visualização**, selecione **gráfico de linhas**. 
    * Adicionar um eixo e selecione **windowend**. 
    * Adicione um valor e selecione **fraudulentcalls**.
    * Para **janela de tempo para apresentar**, selecione os últimos 10 minutos.

    ![Criar o mosaico de gráfico de linha](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Clique em **seguinte**, adicionar um título e subtítulo do e, em **aplicar**.

    O dashboard do Power BI agora dá-lhe duas vistas dos dados sobre chamadas fraudulentas como detetado nos dados de transmissão em fluxo.

    ![Terminou o dashboard do Power BI que mostra dois mosaicos para chamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Mais informações sobre o Power BI

Este tutorial demonstra como criar apenas alguns tipos de visualizações para um conjunto de dados. Power BI pode ajudar a criar outras ferramentas de cliente business intelligence para a sua organização. Para obter mais ideias, consulte os seguintes recursos:

* Para obter outro exemplo de um dashboard do Power BI, veja o [introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) vídeo.
* Para obter mais informações sobre como configurar a análise de transmissão em fluxo de saída para o Power BI da tarefa e utilizar grupos de Power BI, reveja o [Power BI](stream-analytics-define-outputs.md#power-bi) secção o [Stream Analytics produz](stream-analytics-define-outputs.md) artigo. 
* Para obter informações sobre como utilizar o Power BI geralmente, consulte [Dashboards no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Saiba mais sobre as melhores práticas e limitações
Atualmente, Power BI pode ser chamado aproximadamente uma vez por segundo. Os visuais de transmissão em fluxo suportam pacotes de 15 KB. Para além disso, os visuais de transmissão em fluxo falharem (mas push continua a trabalhar). Devido a estas limitações, Power BI presta-se mais naturalmente para casos em que o Azure Stream Analytics does uma redução de carregamento de dados significativos. Recomendamos que utilize uma janela em cascata ou janela Hopping para se certificar de que o push de dados é, no máximo, um push por segundo, e que a sua consulta lands dentro os requisitos de débito.

Pode utilizar a seguinte equação para calcular o valor para dar a janela em segundos:

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Por exemplo:

* Terá de 1000 dispositivos enviar dados em intervalos de um segundo.
* Estão a utilizar o Power BI Pro SKU que suporte 1.000.000 linhas por hora.
* Que pretende publicar a quantidade de dados de média por dispositivo para o Power BI.

Como resultado, torna-se a equação:

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Tendo em conta esta configuração, pode alterar a consulta original para o seguinte:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Renovar autorização
Se a palavra-passe tiver sido alterado desde a tarefa foi criada ou pela última vez autenticada, terá de voltar a sua conta do Power BI. Se o Azure multi-factor Authentication está configurado no seu inquilino do Azure Active Directory (Azure AD), terá também de renovação da autorização do Power BI em duas semanas. Se não renovar, pode ver sintomas como uma falta de saída da tarefa ou um `Authenticate user error` nos registos de operação.

Da mesma forma, se uma tarefa é iniciado depois do token expirou, ocorre um erro e a tarefa falha. Para resolver este problema, parar a tarefa que está a executar e avance para a saída do Power BI. Para evitar a perda de dados, selecione o **renovar autorização** associar e, em seguida, reinicie a tarefa a partir do **hora da última paragem**.

Após ter sido atualizada a autorização com Power BI, um alerta de verde é apresentada na área de autorização para refletir que o problema foi resolvido.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API de REST de gestão do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
