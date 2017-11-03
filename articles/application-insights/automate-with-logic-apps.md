---
title: Automatizar processos de Azure Application Insights utilizando as Logic Apps.
description: "Saiba como rapidamente pode automatizar os processos repetíveis, adicionando o conector do Application Insights à sua aplicação lógica."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: 92521bab2082f63fa01e816151e3da9400effe6d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizar processos de Application Insights utilizando as Logic Apps

Der por si a executar repetidamente as mesmas consultas nos seus dados de telemetria para verificar se o serviço está a funcionar corretamente? Está à procura para automatizar estas consultas para localizar tendências e anomalias e, em seguida, criar os seus fluxos de trabalho em torno deles? O conector do Azure Application Insights (pré-visualização) para aplicações lógicas é a ferramenta certa para esta finalidade.

Esta integração, pode automatizar processos várias sem ter de escrever uma única linha de código. Pode criar uma aplicação lógica com o conector do Application Insights para rapidamente automatizar qualquer processo do Application Insights. 

Pode adicionar, bem como as ações adicionais. A funcionalidade de Logic Apps do App Service do Azure disponibiliza a centenas de ações. Por exemplo, utilizando uma aplicação lógica, pode automaticamente enviar uma notificação por e-mail ou criar um erro no Visual Studio Team Services. Também pode utilizar um dos muitos disponíveis [modelos](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) para ajudar a acelerar o processo de criação da sua aplicação lógica. 

## <a name="create-a-logic-app-for-application-insights"></a>Criar uma aplicação lógica para o Application Insights

Neste tutorial, irá aprender a criar uma aplicação lógica que utiliza o algoritmo de autocluster de análise para atributos de grupos de dados para uma aplicação web. O fluxo envia automaticamente os resultados por correio eletrónico, apenas um exemplo de como pode utilizar o Application Insights Analytics e Logic Apps em conjunto. 

### <a name="step-1-create-a-logic-app"></a>Passo 1: Criar uma aplicação lógica
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No **novo** painel, selecione **Web + móvel**e, em seguida, selecione **aplicação lógica**.

    ![Nova janela de aplicação lógica](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Passo 2: Criar um acionador para a sua aplicação lógica
1. No **Designer de aplicação de lógica** janela em **começar com um acionador comuns**, selecione **periodicidade**.

    ![Janela do estruturador de aplicação lógica](./media/automate-with-logic-apps/logicapp2.png)

2. No **frequência** caixa, selecione **dia** e, em seguida, no **intervalo** caixa, escreva **1**.

    ![Designer de aplicação lógica "Recurrence" janela](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação do Application Insights
1. Clique em **novo passo**e, em seguida, clique em **adicionar uma ação**.

2. No **escolher uma ação** caixa de pesquisa, escreva **Azure Application Insights**.

3. Em **ações**, clique em **pré-visualização de consulta do Azure Application Insights – análise visualizar**.

    !["Escolher uma ação" janela do Designer de aplicação lógica](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar a um recurso do Application Insights

Para concluir este passo, terá uma ID da aplicação e uma chave de API para o seu recurso. Possível obtê-las no portal do Azure, conforme mostrado no diagrama seguinte:

![ID da aplicação no portal do Azure](./media/automate-with-logic-apps/appid.png) 

Forneça um nome para a ligação, o ID da aplicação e a chave de API.

![Janela de ligação de fluxo de Designer da aplicação lógica](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especificar o tipo de gráfico de consulta e análise
No exemplo seguinte, a consulta seleciona pedidos falhados no último dia e está correlacionada com exceções que ocorreram como parte da operação. Análise de correlaciona pedidos falhados, com base no identificador de operation_Id. A consulta, em seguida, segmenta os resultados utilizando o algoritmo de autocluster. 

Quando criar as suas próprias consultas, certifique-se de que estão a funcionar corretamente no Analytics antes de adicioná-lo para o fluxo.

1. No **consulta** caixa, adicione a seguinte consulta de análise: 

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

2. No **tipo de gráfico** caixa, selecione **Html tabela**.

    ![Janela de configuração de consulta de análise](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Passo 6: Configurar a aplicação lógica para enviar correio eletrónico

1. Clique em **novo passo**e, em seguida, selecione **adicionar uma ação**.

2. Na caixa de pesquisa, escreva **Outlook do Office 365**.

3. Clique em **Office 365 Outlook – enviar um e-mail**.

    ![Seleção de Outlook do Office 365](./media/automate-with-logic-apps/flow2b.png)

4. No **enviar um e-mail** janela, efetue o seguinte procedimento:

   a. Escreva o endereço de e-mail do destinatário.

   b. Escreva um assunto do e-mail.

   c. Clique em qualquer parte no **corpo** caixa e, em seguida, no menu conteúdo dinâmico que se abre no lado direito, selecione **corpo**.

   d. Clique em **Mostrar opções avançadas**.

      ![Configuração do Outlook do Office 365](./media/automate-with-logic-apps/flow5.png)

5. No menu de conteúdo dinâmico, efetue o seguinte:

    a. Selecione **nome do anexo**.

    b. Selecione **anexo conteúdo**.
    
    c. No **é HTML** caixa, selecione **Sim**.

      ![Ecrã de configuração de e-mail do Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Passo 7: Guardar e testar a sua aplicação lógica
* Clique em **guardar** para guardar as alterações.

Pode aguardar acionar a executar a aplicação lógica, ou pode executar a aplicação lógica imediatamente selecionando **executar**.

![Ecrã de criação de aplicação lógica](./media/automate-with-logic-apps/step7.png)

Quando é executada a sua aplicação lógica, os destinatários especificados na lista de e-mail irão receber um e-mail que o seguinte aspeto:

![Mensagem de correio eletrónico de aplicação lógica](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como criar [as consultas de análises](app-insights-analytics-using.md).
- Saiba mais sobre [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





