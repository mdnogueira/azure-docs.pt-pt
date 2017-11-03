---
title: Ligar ao Dynamics 365 (online) do Azure Logic Apps | Microsoft Docs
description: "Criar lógica fluxos de trabalho de aplicações que gerem Dynamics 365 entidades (online) através da API fornecida pelo conector do Dynamics 365"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Ligar ao Dynamics 365 a partir de fluxos de trabalho de aplicação de lógica

Com as Logic Apps, que pode ligar ao Dynamics 365 (online) e criar fluxos de negócio útil que criar registos de itens ou devolvem uma lista de registos. Com o conector de Dynamics 365, pode:

* Crie o fluxo de negócio com base nos dados que aproveitar Dynamics 365 (online).
* Utilize as ações que obter uma resposta e, em seguida, disponibilizá-a saída para outras ações. Por exemplo, quando um item é atualizado no Dynamics 365 (online), pode enviar uma mensagem de e-mail utilizando o Office 365.

Este tópico mostra como criar uma aplicação lógica que cria uma tarefa no Dynamics 365 sempre que é criado um novo fabrico no Dynamics 365.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure.
* Uma conta de Dynamics 365 (online).

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Criar uma tarefa quando é criado um novo fabrico no Dynamics 365

1.  [Inicie sessão no Azure](https://portal.azure.com).

2.  Na caixa de pesquisa do Azure, escreva `Logic apps`, e prima ENTER.

      ![Localizar as Logic Apps](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  Em **as Logic apps**, clique em **adicionar**.

      ![Adicionar LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Para criar a aplicação lógica, conclua o **nome**, **subscrição**, **grupo de recursos**, e **localização** campos e, em seguida, clique em **Criar**.

5.  Selecione a nova aplicação lógica. Quando receber o **implementação concluída com êxito** notificação, clique em **atualizar**.

6.  Em **ferramentas de desenvolvimento**, clique em **Designer de aplicação lógica**. Na lista de modelo, clique em **aplicação lógica em branco**.

7.  Na caixa de pesquisa, escreva `Dynamics 365`. Na lista de acionadores Dynamics 365, selecione **Dynamics 365 – quando é criado um registo**.

8.  Se lhe for pedido que inicie sessão no Dynamics 365, faça-o agora.

9.  Nos detalhes de Acionador, introduza as seguintes informações:

  * **Nome da organização**. Selecione a instância de Dynamics 365 que pretende que a aplicação lógica para escutar.

  * **Nome da entidade**. Selecione a entidade que pretende para escutar. Este evento atua como um acionador para iniciar a aplicação lógica. 
  Nestas instruções, **leva** está selecionada.

  * **Frequência que pretende procurar itens?** Estes valores definidos como muitas vezes, a aplicação lógica verifica a existência de atualizações relacionados com o acionador. A predefinição é para procurar atualizações a cada três minutos.

    * **Frequência**. Selecione segundos, minutos, horas ou dias.

    * **Intervalo**. Introduza o número de segundos, minutos, horas ou dias que pretende passar antes da verificação seguinte.

      ![Detalhes de Acionador de aplicação lógica](./media/connectors-create-api-crmonline/trigger-details.png)

10. Clique em **novo passo**e, em seguida, clique em **adicionar uma ação**.

11. Na caixa de pesquisa, escreva `Dynamics 365`. Na lista de ações, selecione **Dynamics 365 – criar um novo registo**.

12. Introduza as seguintes informações:

    * **Nome da organização**. Selecione a instância de Dynamics 365 onde pretende que o fluxo para criar o registo. 
    Tenha em atenção que esta instância não tem de ser a mesma instância em que o evento é acionado a partir do.

    * **Nome da entidade**. Selecione a entidade que pretende criar um registo quando o evento é activado. 
    Nestas instruções, **tarefas** está selecionada.

13. Clique no **requerente** caixa que aparece. Na lista conteúda dinâmica que aparece, pode selecionar qualquer um destes campos:

    * **Último nome**. Selecionar este campo Insere o último nome para as oportunidades potenciais no campo do requerente para a tarefa, quando o registo de tarefas é criado.
    * **Tópico**. Selecionar este campo Insere o campo de tópico para as oportunidades potenciais no campo do requerente para a tarefa, quando o registo de tarefas é criado. 
    Clique em **tópico** que para adicionar o **requerente** caixa.

      ![Lógica aplicação criar novos detalhes do registo](./media/connectors-create-api-crmonline/create-record-details.png)

14. Na barra de ferramentas Designer de aplicação lógica, clique em **guardar**.

    ![Barra de ferramentas de Designer da aplicação de lógica de gravação](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Para iniciar a aplicação lógica, clique em **executar**.

    ![Barra de ferramentas de Designer da aplicação de lógica de gravação](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Agora, crie um registo de oportunidades potenciais no Dynamics 365 de vendas e ver o fluxo em ação!

## <a name="set-advanced-options-for-a-logic-app-step"></a>Definir as opções avançadas de um passo de aplicação lógica

Para especificar como filtrar dados num passo aplicação lógica, clique em **Mostrar opções avançadas** esse passo, em seguida, adicione um filtro ou a ordem pela consulta.

Por exemplo, pode utilizar uma consulta de filtro para obter apenas a contas do Active Directory e a ordem, o nome da conta. Para efetuar esta tarefa, introduza a consulta de filtro de OData `statuscode eq 1`e selecione **nome da conta** na lista de conteúdo dinâmico. Obter mais informações: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) e [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Aplicação de lógica opções avançadas](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Melhores práticas quando utilizar opções avançadas

Quando adiciona um valor para um campo, têm de corresponder o tipo de campo se escreva um valor ou selecione um valor na lista de conteúdo dinâmica.

Tipo de campo  |Como utilizar  |Onde encontrar  |Nome  |Tipo de dados  
---------|---------|---------|---------|---------
Campos de texto|Campos de texto necessitam de uma única linha de texto ou de conteúdo dinâmico, que é um campo de tipo de texto. Os exemplos incluem os campos de categoria e subcategoria.|Definições > personalizações > Personalizar o sistema > entidades > tarefas > campos |categoria |Linha única de texto        
Campos de número inteiro | Alguns campos requerem um número inteiro ou conteúdo dinâmico, que é um campo de tipo número inteiro. Os exemplos incluem a percentagem de conclusão e duração. |Definições > personalizações > Personalizar o sistema > entidades > tarefas > campos |PercentComplete |Número inteiro         
Campos de data | Alguns campos necessitam de uma data introduzida no formato dd/mm/aaaa ou conteúdo dinâmico, que é um campo de tipo de data. Os exemplos incluem criado, data de início, iniciar real, pela última vez em tempo de espera, fim real e data devida. | Definições > personalizações > Personalizar o sistema > entidades > tarefas > campos |createdon |Data e hora
Tipo de campos que necessitam de um ID de registo e o pesquisa |Alguns campos que façam referência a outro registo entidade requerem o ID do registo e o tipo de pesquisa. |Definições > personalizações > Personalizar o sistema > entidades > conta > campos  | accountid  | Chave primária

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Obter mais exemplos de campos que requerem uma ID de registo e a pesquisa de tipo
Expansão na tabela anterior, eis mais exemplos de campos que não funcionam com valores selecionados na lista de conteúdo dinâmica. Em vez disso, estes campos requerem ambos um ID e a pesquisa de tipo de registo introduzido nos campos da PowerApps.  
* Proprietário e o tipo de proprietário. O campo de proprietário deve ter um ID válido. registo de utilizador ou equipa O tipo de proprietário deve ser **systemusers** ou **equipas**.
* Cliente e o tipo de cliente. O campo de cliente tem de ser uma conta válida ou contacte o ID de registo. O tipo de proprietário deve ser **contas** ou **contactos**.
* Relativos à e sobre o tipo. O campo de Regarding tem de ser um ID de registo válida, tal como uma conta ou contacte o ID de registo. O tipo de relativos à deve ser o tipo de pesquisa para o registo, tais como **contas** ou **contactos**.

O seguinte exemplo de ação de criação de tarefas adiciona um registo de conta que corresponde ao ID de registo adicionando-a no campo sobre da tarefa.

![Fluxo recordId e o tipo de conta](./media/connectors-create-api-crmonline/recordid-type-account.png)

Este exemplo também atribui a tarefa a um utilizador específico com base no ID de registo. do utilizador

![Fluxo recordId e o tipo de conta](./media/connectors-create-api-crmonline/recordid-type-user.png)

Para localizar o ID de um registo, consulte a secção seguinte: *localizar o ID do registo*

## <a name="find-the-record-id"></a>Localizar o ID do registo

1. Abra um registo, como um registo de conta.

2. Na barra de ferramentas ações, clique em **destacar** ![popout registo](./media/connectors-create-api-crmonline/popout-record.png).
Em alternativa, na barra de ferramentas ações, para copiar o URL completo para o programa de e-mail predefinido, clique em **ligação de um E-Mail**.

   O ID do registo é apresentado entre o % 7b e %7 d codificação de carateres do URL.

   ![Fluxo recordId e o tipo de conta](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Resolução de problemas
Para resolver uma falha de um passo numa aplicação lógica, ver os detalhes do Estado do evento.

1. Em **Logic Apps**, selecione a sua aplicação lógica e, em seguida, clique em **descrição geral**. 

   A área de resumo é apresentada e fornece o estado de execução para a aplicação lógica. 

   ![Aplicação de lógica executar estado](./media/connectors-create-api-crmonline/tshoot1.png)

2. Para ver mais informações sobre qualquer falha é executado, clique no evento de falha. Para expandir um passo falhado, clique nesse passo.

   ![Expanda o passo falhado](./media/connectors-create-api-crmonline/tshoot2.png)

   Os detalhes do passo são apresentados e podem ajudá-lo a resolver problemas relacionados com a causa da falha.

   ![Falha ao passo detalhes](./media/connectors-create-api-crmonline/tshoot3.png)

Para obter mais informações sobre resolução de problemas das logic apps, consulte [diagnosticar falhas de aplicação lógica](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/crm/). 

## <a name="next-steps"></a>Passos seguintes
Explorar os outros conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).
