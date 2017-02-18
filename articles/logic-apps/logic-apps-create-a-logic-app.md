---
title: "Criar fluxos de trabalho com a sua Aplicação Lógica do Azure | Microsoft Docs"
description: "Introdução à ligação de aplicações e serviços SaaS com a sua primeira Aplicação Lógica"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: ce1e0cfe7c7f52d521ef3318376af75331323083
ms.openlocfilehash: 178a57624345a4b3e5d73e64ce4ccf81b8b90a88


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Criar uma nova aplicação lógica de ligação aos serviços SaaS
Este tópico demonstra, em apenas alguns minutos, como pode começar a utilizar [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Vamos explicar através de um fluxo de trabalho simples que lhe permite enviar tweets interessantes para o seu e-mail.

Para utilizar este cenário, precisa de:

* Uma subscrição do Azure.
* Uma conta do Twitter
* Uma caixa de correio do Outlook.com ou do Office 365 alojado

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Criar uma nova aplicação lógica para enviar os tweets por correio eletrónico
1. No [dashboard do portal do Azure](https://portal.azure.com), selecione **Novo**. 
2. Na barra de pesquisa, procure «aplicação lógica» e, em seguida, selecione **Aplicação Lógica**. Também pode selecionar **Novo**, **Web + Móvel** e selecione **Aplicação Lógica**. 
3. Introduza um nome para a sua aplicação lógica, selecione um local, grupo de recursos e selecione **Criar**.  Se selecionar **Afixar ao Dashboard**, a aplicação de lógica será aberta automaticamente depois de ser implementada.  
4. Depois de abrir a aplicação lógica pela primeira vez pode selecionar a partir de um modelo para começar.  Por enquanto, clique em **Aplicação Lógica em Branco** para criá-la do zero. 
5. O primeiro item que tem de criar é o acionador.  Este é o evento que irá iniciar a sua aplicação de lógica.  Procure por **twitter** na caixa de pesquisa do acionador e selecione-o.
6. Agora escreva um termo de pesquisa para acionar.  A **Frequência** e o **Intervalo** irão determinar a frequência com que a aplicação de lógica irá verificar novos tweets (e devolver todos as tweets durante esse intervalo de tempo).
    ![Pesquisa do Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)
7. Selecione o botão **Novo passo** e, em seguida, escolha **Adicionar uma ação** ou **Adicionar uma condição**
8. Quando seleciona **Adicionar uma Ação**, pode procurar a partir dos [conectores disponíveis](../connectors/apis-list.md) para selecionar uma ação. Por exemplo, pode selecionar **Outlook.com - Enviar e-mail** para enviar e-mail a partir de um endereço do outlook.com:  
    ![Ações](media/logic-apps-create-a-logic-app/actions.png)
9. Agora tem de preencher os parâmetros para o correio eletrónico que pretende:  ![Parâmetros](media/logic-apps-create-a-logic-app/parameters.png)
10. Por fim, pode selecionar **Guardar** para colocar a sua aplicação lógica em direto.

## <a name="manage-your-logic-app-after-creation"></a>Gerir a sua aplicação lógica após a criação
A aplicação lógica está agora instalada e em execução. Irá verificar periodicamente tweets com o termo de pesquisa introduzido. Quando encontra um tweet correspondente, irá enviar-lhe um e-mail. Por fim, irá saber como desativar a aplicação ou ver o respetivo estado.

1. Aceda ao [Portal do Azure](https://portal.azure.com)
2. Clique em **Procurar** no lado esquerdo do ecrã e selecione **Logic Apps**.
3. Clique na nova aplicação lógica que acabou de criar para ver o estado atual e informações gerais.
4. Para editar a sua nova aplicação lógica, clique em **Editar**.
5. Para desativar a aplicação, clique em **Desativar** na barra de comandos.
6. Veja os históricos de execução e acionamento para monitorizar quando a aplicação de lógica está em execução.  Pode clicar em **Atualizar** para ver os dados mais recentes.

Em menos de 5 minutos conseguiu configurar uma aplicação lógica simples em execução na nuvem. Para saber mais sobre como utilizar funcionalidades das Logic Apps, consulte o artigo [Utilizar funcionalidades da aplicação lógica]. Para saber mais sobre as próprias definições da Aplicação Lógica, consulte o artigo [Criar definições da Aplicação Lógica](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Utilizar funcionalidades da aplicação lógica]: logic-apps-create-a-logic-app.md



<!--HONumber=Jan17_HO4-->


