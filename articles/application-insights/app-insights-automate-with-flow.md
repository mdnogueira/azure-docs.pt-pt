---
title: Automatizar processos de Azure Application Insights com o Microsoft Flow
description: "Saiba como pode utilizar o Microsoft Flow rapidamente automatizar processos repetíveis, utilizando o conector do Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: a1d2787626ed8fa71e3e4e9921ffb8a4680014cb
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos de Azure Application Insights com o conector Microsoft Flow

Der por si a executar repetidamente as mesmas consultas nos seus dados de telemetria para verificar que o serviço está a funcionar corretamente? Está à procura para automatizar estas consultas para localizar tendências e anomalias e, em seguida, criar os seus fluxos de trabalho em torno deles? O conector Azure Application Insights (pré-visualização) para a Microsoft Flow é a ferramenta certa para estes fins.

Esta integração, agora pode automatizar processos várias sem ter de escrever uma única linha de código. Depois de criar um fluxo através de uma ação do Application Insights, o fluxo executa automaticamente a consulta do Application Insights Analytics. 

Pode adicionar, bem como as ações adicionais. Flow Microsoft disponibiliza a centenas de ações. Por exemplo, pode utilizar o Microsoft Flow automaticamente enviar uma notificação por e-mail ou criar um erro no Visual Studio Team Services. Também pode utilizar um dos muitos [modelos](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) que estão disponíveis para o conector Microsoft Flow. Estes modelos acelerar o processo de criação de um fluxo. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para o Application Insights

Neste tutorial, irá aprender a criar um fluxo que utiliza o algoritmo de clusters de automática de análise para atributos de grupo nos dados para uma aplicação web. O fluxo envia automaticamente os resultados por correio eletrónico, apenas um exemplo de como pode utilizar o Microsoft Flow e o Application Insights Analytics em conjunto. 

### <a name="step-1-create-a-flow"></a>Passo 1: Criar um fluxo
1. Inicie sessão no [Microsoft Flow](http://flow.microsoft.com)e, em seguida, selecione **flui My**.
2. Clique em **criar um fluxo de em branco**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passo 2: Criar um acionador para o fluxo
1. Selecione **agenda**e, em seguida, selecione **agenda - periodicidade**.
2. No **frequência** caixa, selecione **dia**e, no **intervalo** box, introduza **1**.

    ![Caixa de diálogo de Acionador de Flow Microsoft](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação do Application Insights
1. Clique em **novo passo**e, em seguida, clique em **adicionar uma ação**.
2. Procurar **Azure Application Insights**.
3. Clique em **pré-visualização de consulta do Azure Application Insights – análise visualizar**.

    ![Execute a janela de consulta de análise](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar a um recurso do Application Insights

Para concluir este passo, terá uma ID da aplicação e uma chave de API para o seu recurso. Possível obtê-las no portal do Azure, conforme mostrado no diagrama seguinte:

![ID da aplicação no portal do Azure](./media/app-insights-automate-with-flow/appid.png) 

- Forneça um nome para a ligação, juntamente com a chave de ID e a API de aplicação.

    ![Janela de ligação do Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especificar o tipo de gráfico de consulta e análise
Esta consulta de exemplo seleciona pedidos falhados no último dia e está correlacionada com exceções que ocorreram como parte da operação. Análise de correlaciona-los com base no identificador de operation_Id. A consulta, em seguida, segmenta os resultados utilizando o algoritmo de autocluster. 

Quando criar as suas próprias consultas, certifique-se de que estão a funcionar corretamente no Analytics antes de adicioná-lo para o fluxo.

- Adicione a seguinte consulta de análise e, em seguida, selecione o tipo de gráfico de tabela HTML. 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Janela de configuração de consulta de análise](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passo 6: Configurar o fluxo para enviar correio eletrónico

1. Clique em **novo passo**e, em seguida, clique em **adicionar uma ação**.
2. Procurar **Outlook do Office 365**.
3. Clique em **Office 365 Outlook – enviar um e-mail**.

    ![Janela de seleção do Outlook do Office 365](./media/app-insights-automate-with-flow/flow2b.png)

4. No **enviar um e-mail** janela, efetue o seguinte procedimento:

   a. Escreva o endereço de e-mail do destinatário.

   b. Escreva um assunto do e-mail.

   c. Clique em qualquer parte no **corpo** caixa e, em seguida, no menu conteúdo dinâmico que se abre no lado direito, selecione **corpo**.

   d. Clique em **Mostrar opções avançadas**.

    ![Configuração do Outlook do Office 365](./media/app-insights-automate-with-flow/flow5.png)

5. No menu de conteúdo dinâmico, efetue o seguinte:

    a. Selecione **nome do anexo**.

    b. Selecione **anexo conteúdo**.
    
    c. No **é HTML** caixa, selecione **Sim**.

    ![Janela de configuração de e-mail do Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Passo 7: Guardar e testar o fluxo
- No **nome de fluxo** caixa, adicionar um nome para o fluxo e, em seguida, clique em **criar fluxo**.

    ![Janela de criação de fluxo](./media/app-insights-automate-with-flow/flow8.png)

Pode aguardar o acionador executar esta ação em alternativa, pode executar o fluxo imediatamente por [com o acionador a pedido](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando o fluxo é executado, os destinatários que especificou na lista de correio eletrónico recebem uma mensagem de e-mail que o seguinte aspeto:

![E-mail de exemplo](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como criar [as consultas de análises](app-insights-analytics-using.md).
- Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





