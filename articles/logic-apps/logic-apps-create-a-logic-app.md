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
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Criar uma nova aplicação lógica de ligação aos serviços SaaS
Este tópico demonstra, em apenas alguns minutos, como pode começar a utilizar [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Vamos explicar através de um fluxo de trabalho simples que lhe permite enviar tweets interessantes para o seu e-mail.

Para utilizar este cenário, precisa de:

* Uma subscrição do Azure.
* Uma conta do Twitter
* Uma conta do Outlook.com ou do Office 365 Outlook

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Criar uma nova aplicação lógica para enviar os tweets por correio eletrónico

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. No menu à esquerda, escolha **Novo** > **Integração Empresarial** > **Aplicação Lógica**.

    Também pode escolher **Novo** e, em seguida, na caixa de pesquisa, escreva `logic app`, e prima Enter. Escolha **Aplicação Lógica** > **Criar**.

3. Introduza um nome para a sua aplicação lógica, selecione a subscrição do Azure, crie ou selecione um grupo de recursos do Azure, selecione uma localização e escolha **Criar**.

    Se selecionar **Afixar ao Dashboard**, a aplicação lógica abre automaticamente depois da implementação.

4. Ao abrir a aplicação lógica pela primeira vez, pode selecionar a partir de um modelo para começar.
Por enquanto, clique em **Aplicação Lógica em Branco** para criá-la do zero. 

5. O primeiro item que tem de criar é o acionador. Este é o evento que inicia a sua aplicação lógica. Na caixa de pesquisa, procure **twitter** e selecione **Quando é publicado um novo tweet**. Inicie sessão com o nome de utilizador e palavra-passe da sua conta do Twitter.

6. Agora escreva um termo de pesquisa para acionar a sua aplicação lógica.

   ![Pesquisa do twitter](media/logic-apps-create-a-logic-app/twittersearch.png)

    A **Frequência** e o **Intervalo** determinam a frequência com que a aplicação de lógica verifica novos tweets, e devolve todos os tweets durante esse intervalo de tempo.

7. Selecione **Novo passo** e escolha **Adicionar uma ação** ou **Adicionar uma condição**.

    Quando seleciona **Adicionar uma Ação**, pode procurar os [conectores disponíveis](../connectors/apis-list.md) para selecionar uma ação. 

8. Na caixa de pesquisa, procure **outlook**e selecione **Enviar um e-mail**, para enviar e-mail a partir de uma conta do Outlook para qualquer endereço de e-mail especificado.

   ![Ações](media/logic-apps-create-a-logic-app/actions.png)

9. Agora tem de preencher os parâmetros para o correio eletrónico que pretende:

   ![Parâmetros](media/logic-apps-create-a-logic-app/parameters.png)

10. Por fim, pode selecionar **Guardar** para colocar a sua aplicação lógica em direto.

## <a name="manage-your-logic-app-after-creation"></a>Gerir a sua aplicação lógica após a criação

A aplicação lógica está agora instalada e em execução. Irá verificar periodicamente tweets com o termo de pesquisa introduzido. Quando encontra um tweet correspondente, irá enviar-lhe um e-mail. Por fim, irá saber como desativar a aplicação ou ver o respetivo estado.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. No menu à esquerda, clique em **Mais serviços**. Em **Integração Empresarial**, escolha **Aplicações Lógicas**. Selecione a sua aplicação lógica.

    *    Para ver o estado da sua aplicação, o histórico de execução e informações gerais, no menu da aplicação de lógica, selecione **Descrição geral**. Se não encontrar os dados que pretende, na barra de comando, escolha **Atualizar**.

    *    Para editar a sua aplicação, no menu da aplicação lógica, selecione **Estruturador da Aplicação Lógica**.

    *    Para desativar temporariamente a sua aplicação no menu de aplicação lógica, selecione **Descrição geral**. Na barra de comando, escolha **Desativar**.

    *    Para eliminar a sua aplicação, no menu da aplicação lógica, escolha **Descrição geral**. 
    Na barra de comando, escolha **Eliminar**. Introduza o nome da sua aplicação lógica e selecione **Eliminar**.

Em menos de 5 minutos conseguiu configurar uma aplicação lógica simples em execução na nuvem. Para saber mais sobre como utilizar funcionalidades das Logic Apps, consulte o artigo [Utilizar funcionalidades da aplicação lógica]. Para saber mais sobre as próprias definições da Aplicação Lógica, consulte o artigo [Criar definições da Aplicação Lógica](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Utilizar funcionalidades da aplicação lógica]: logic-apps-create-a-logic-app.md
