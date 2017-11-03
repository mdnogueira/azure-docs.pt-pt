---
title: "Chamar uma função do Azure a partir de Microsoft Flow | Microsoft Docs"
description: "Criar um conetor personalizado e chamar uma função utilizando esse conector."
services: functions
keywords: "aplicações em nuvem, na nuvem processos de negócio de serviços, Microsoft Flow, aplicação empresarial"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: mblythe
ms.custom: 
ms.openlocfilehash: 120f5d69441c5e01ffafbdb8dccb179bf00bdb0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-function-from-microsoft-flow"></a>Chamar uma função a partir do Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) torna mais fácil automatizar fluxos de trabalho e os processos de negócios entre os seus serviços e aplicações favoritas. Programadores profissionais podem utilizar as funções do Azure para aumentar as capacidades do Microsoft Flow, enquanto efetua a blindagem de construtores de fluxo dos detalhes técnicos.

Criar um fluxo deste tópico com base num cenário de manutenção para turbines vento. Este tópico mostra como chamar a função que definiu no [criar uma definição de OpenAPI para uma função](functions-openapi-definition.md). A função determina se uma reparação de emergência num turbine vento económica. Se for económica, o fluxo envia um e-mail para recomendar a reparação.

Para obter informações sobre a chamar a mesma função a partir do PowerApps, consulte [chamar uma função a partir do PowerApps](functions-powerapps-scenario.md).

Neste tópico, saiba como:

> [!div class="checklist"]
> * Crie uma lista no SharePoint.
> * Exporte uma definição de API.
> * Adicione uma ligação para a API.
> * Crie um fluxo para enviar correio eletrónico se a reparação é económica.
> * Execute o fluxo.

## <a name="prerequisites"></a>Pré-requisitos

+ Um Active Directory [conta Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) com o início de sessão mesmo as credenciais de início como a sua conta do Azure. 
+ SharePoint, que utilizar como origem de dados deste fluxo. Inscrever-se [uma versão de avaliação do Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) se ainda não tiver o SharePoint.
+ Concluir o tutorial [criar uma definição de OpenAPI para uma função](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Criar uma lista do SharePoint
Começar através da criação de uma lista para utilizar como origem de dados para o fluxo. A lista tem as seguintes colunas.

| Coluna de lista     | Tipo de dados           | Notas                                    |
|-----------------|---------------------|------------------------------------------|
| **Título**           | Linha única de texto | Nome do turbine                      |
| **LastServiceDate** | Data                |                                          |
| **MaxOutput**       | Número              | Saída de turbine, em KwH            |
| **ServiceRequired** | Sim/Não              |                                          |
| **EstimatedEffort** | Número              | Tempo estimado para a reparação, em horas |

1. No seu site do SharePoint, clique ou toque em **novo**, em seguida, **lista**.

    ![Criar nova lista do SharePoint](./media/functions-flow-scenario/new-list.png)

2. Introduza o nome `Turbines`, em seguida, clique ou toque em **criar**.

    ![Especifique o nome para a nova lista](./media/functions-flow-scenario/create-list.png)

    O **Turbines** lista é criada, com a predefinição **título** campo.

    ![Lista de turbines](./media/functions-flow-scenario/initial-list.png)

3. Clique ou toque em ![novo ícone do item](./media/functions-flow-scenario/icon-new.png) , em seguida, **data**.

    ![Adicione uma linha única de campo de texto](./media/functions-flow-scenario/add-column.png)

4. Introduza o nome `LastServiceDate`, em seguida, clique ou toque em **criar**.

    ![Criar a coluna de LastServiceDate](./media/functions-flow-scenario/date-column.png)

5. Repita os dois últimos passos para outras três colunas na lista:

    1. **Número** > "MaxOutput"

    2. **Sim/não** > "ServiceRequired"

    3. **Número** > "EstimatedEffort"

Já está por agora - devem ter uma lista vazia que se assemelha a imagem seguinte. Adicionar dados à lista depois de criar o fluxo.

![Lista vazia](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Adicionar uma ligação para a API
A API personalizada (também conhecido como um conetor personalizado) está disponível no Microsoft Flow, mas tem de estabelecer uma ligação para a API antes de poder utilizar um fluxo.

1. No [flow.microsoft.com](https://flow.microsoft.com), clique no ícone de engrenagem (no canto superior direito), em seguida, clique em **ligações**.

    ![Ligações de fluxo](media/functions-flow-scenario/flow-connections.png)

1. Clique em **criar ligação**, desloque para baixo até o **Turbine reparação** conector e clique no mesmo.

    ![Criar a ligação](media/functions-flow-scenario/create-connection.png)

1. Introduza a chave de API e clique em **criar ligação**.

    ![Introduza a chave de API e criar](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Se partilhar o seu fluxo com outros utilizadores, cada pessoa que funciona na ou utiliza o fluxo também tem de introduzir a chave de API para ligar à API. Este comportamento pode ser alterado no futuro e, neste tópico para refletir que iremos atualizar.


## <a name="create-a-flow"></a>Criar um fluxo

Agora está pronto para criar um fluxo que utiliza o conector personalizado e lista do SharePoint que criou.

### <a name="add-a-trigger-and-specify-a-condition"></a>Adicionar um acionador e especificar uma condição

Primeiro criar um fluxo de em branco (sem um modelo) e adicione um *acionador* que é acionado quando é criado um item na lista do SharePoint. Em seguida, adicione um *condição* para determinar o que acontece em seguida.

1. No [flow.microsoft.com](https://flow.microsoft.com), clique em **flui My**, em seguida, **criar em branco**.

    ![criar a partir de em branco](media/functions-flow-scenario/create-from-blank.png)

2. Clique no acionador de SharePoint **quando é criado um item**.

    ![Escolha um acionador](media/functions-flow-scenario/choose-trigger.png)

    Se ainda não tenha sido iniciada no SharePoint, será solicitado a fazê-lo.

3. Para **endereço do Site**, introduza o nome de site do SharePoint e para **lista nome**, introduza a lista que contém os dados de turbine.

    ![Escolha um acionador](media/functions-flow-scenario/site-list.png)

4. Clique em **novo passo**, em seguida, **adicionar uma condição**.

    ![Adicionar uma condição](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow adiciona dois ramos o fluxo: **em caso afirmativo** e **se não**. Adicione passos para um ou ambos os ramos depois de definir a condição que pretende corresponder.

    ![Ramos de condição](media/functions-flow-scenario/condition-branches.png)

5. No **condição** cartão, clique na primeira caixa, em seguida, selecione **ServiceRequired** do **conteúdo dinâmico** caixa de diálogo.

    ![Selecione o campo para a condição](media/functions-flow-scenario/condition1-field.png)

6. Introduza um valor de `True` para a condição.

    ![Introduza verdadeiro para a condição](media/functions-flow-scenario/condition1-yes.png)

    O valor é apresentado como `Yes` ou `No` no SharePoint lista, mas é armazenado como uma *booleano*, `True` ou `False`. 

7. Clique em **criar fluxo** na parte superior da página. Certifique-se clicar em **atualização fluxo** periodicamente.

Para quaisquer itens de criar na lista, o fluxo verifica se o **ServiceRequired** campo está definido como `Yes`, em seguida, vai para a **se Sim** ramo ou o **se não** sucursal como conforme apropriado. Para poupar tempo, neste tópico apenas especificar ações para o **em caso afirmativo** ramo.

### <a name="add-the-custom-connector"></a>Adicionar o conetor personalizado

Agora adicione o conector personalizado que chama a função no Azure. Adicione o conector personalizado para o fluxo, tal como um conector padrão. 

1. No **em caso afirmativo** sucursal, clique em **adicionar uma ação**.

    ![Adicionar uma ação](media/functions-flow-scenario/condition1-yes-add-action.png)

2. No **escolher uma ação** caixa de diálogo, procure `Turbine Repair`, em seguida, selecione a ação **Turbine reparar - calcula os custos**.

    ![Escolher uma ação](media/functions-flow-scenario/choose-turbine-repair.png)

    A imagem seguinte mostra o cartão é adicionado o fluxo. Os campos e descrições vêm da definição de OpenAPI para o conector.

    ![Calcula as predefinições de custos](media/functions-flow-scenario/calculates-costs-default.png)

3. No **calcula os custos** cartão, utilize o **conteúdo dinâmico** caixa de diálogo Selecionar entradas para a função. Microsoft Flow mostra os campos numéricos, mas não o campo de data, porque a definição de OpenAPI Especifica que **horas** e **capacidade** são numéricos.

    Para **horas**, selecione **EstimatedEffort**e para **capacidade**, selecione **MaxOutput**.

    ![Escolher uma ação](media/functions-flow-scenario/calculates-costs-fields.png)

     Agora adicione outra condição com base na saída da função.

4. Na parte inferior do **em caso afirmativo** sucursal, clique em **mais**, em seguida, **adicionar uma condição**.

    ![Adicionar uma condição](media/functions-flow-scenario/condition2-add.png)

5. No **condição 2** cartão, clique na primeira caixa, em seguida, selecione **mensagem** do **conteúdo dinâmico** caixa de diálogo.

    ![Selecione o campo para a condição](media/functions-flow-scenario/condition2-field.png)

6. Introduza um valor de `Yes`. O fluxo vai para a próxima **em caso afirmativo** ramo ou **se não** ramo com base na mensagem devolvida pela função yes (marca a reparação) ou (não efetuar a reparação). 

    ![Introduza yes condição](media/functions-flow-scenario/condition2-yes.png)

O fluxo deverá agora parecer-como a imagem seguinte.

![Introduza yes condição](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Enviar correio eletrónico com base nos resultados de função

Neste momento no fluxo, a função devolveu um **mensagem** valor `Yes` ou `No` a função, bem como outras informações de receitas de custos e potenciais. No **em caso afirmativo** ramo da condição do segundo, enviará um e-mail, mas, pode efetuar qualquer número de ações, como escrever novamente para a lista do SharePoint ou inicie uma [processo de aprovação](https://flow.microsoft.com/documentation/modern-approvals/).

1. No **em caso afirmativo** ramo da segunda condição, clique em **adicionar uma ação**.

    ![Adicionar uma ação](media/functions-flow-scenario/condition2-yes-add-action.png)

2. No **escolher uma ação** caixa de diálogo, procure `email`, em seguida, selecione uma ação de e-mail de envio com base no sistema de e-mail que utilize (este cenário Outlook).

    ![Envio de Outlook uma mensagem de e-mail](media/functions-flow-scenario/outlook-send-email.png)

3. No **enviar um e-mail** cartão, compõem uma mensagem de e-mail. Introduza um nome válido na sua organização para o **para** campo. Na imagem abaixo, pode ver os outros campos são uma combinação de texto e tokens a partir de **conteúdo dinâmico** caixa de diálogo. 

    ![Campos de e-mail](media/functions-flow-scenario/email-fields.png)

    O **título** token for proveniente da lista do SharePoint, e **CostToFix** e **RevenueOpportunity** são devolvidos pela função.

    O fluxo concluído deverá ser semelhante a imagem seguinte (Iremos deixado primeiro **se não** ramo para guardar espaço).

    ![Fluxo completo](media/functions-flow-scenario/complete-flow.png)

4. Clique em **atualização fluxo** na parte superior da página, em seguida, clique em **feito**.

## <a name="run-the-flow"></a>Executar o fluxo

Agora que o fluxo está concluído, pode adicionar uma linha à lista do SharePoint e ver como o fluxo responde.

1. Regressar à lista do SharePoint e clique em **editar rápida**.

    ![Edição rápida](media/functions-flow-scenario/quick-edit.png)

2. Introduza os seguintes valores na grelha de edição.

    | Coluna de lista     | Valor           |
    |-----------------|---------------------|
    | **Título**           | Turbine 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Sim |
    | **EstimatedEffort** | 10 |

3. Clique em **Concluído**.

    ![Edição rápida concluída](media/functions-flow-scenario/quick-edit-done.png)

    Quando adiciona o item, aciona o fluxo, o que lhe observe seguinte.

4. No [flow.microsoft.com](https://flow.microsoft.com), clique em **flui My**, em seguida, clique em fluxo que criou.

    ![A minha fluxos](media/functions-flow-scenario/my-flows.png)

5. Em **histórico de EXECUÇÕES**, clique em fluxo executar.

    ![histórico de execução](media/functions-flow-scenario/run-history.png)

    Se a execução foi bem sucedida, pode rever as operações de fluxo, na página seguinte. Se a execução falhou por qualquer motivo, a página seguinte fornece informações de resolução de problemas.

6. Expanda os cartões para ver o que ocorreu durante o fluxo. Por exemplo, expanda o **calcula os custos** cartão para ver as entradas para e saídas da função. 

    ![Calcula os custos entradas e saídas](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Verificar a conta de e-mail para a pessoa que especificou no **para** campo a **enviar um e-mail** cartão. O e-mail enviado a partir do fluxo deverá ser semelhante a imagem seguinte.

    ![Fluxo de e-mail](media/functions-flow-scenario/flow-email.png)

    Pode ver a forma como os tokens foram substituídos por valores corretos a partir da lista do SharePoint e a função.

## <a name="next-steps"></a>Passos seguintes
Neste tópico, aprendeu como:

> [!div class="checklist"]
> * Crie uma lista no SharePoint.
> * Exporte uma definição de API.
> * Adicione uma ligação para a API.
> * Crie um fluxo para enviar correio eletrónico se a reparação é económica.
> * Execute o fluxo.

Para saber mais sobre o Microsoft Flow, consulte [introdução ao Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Para mais informações sobre outros cenários interessantes que utilizam as funções do Azure, consulte o artigo [chamar uma função a partir do PowerApps](functions-powerapps-scenario.md) e [criar uma função que se integra com Azure Logic Apps](functions-twitter-email.md).