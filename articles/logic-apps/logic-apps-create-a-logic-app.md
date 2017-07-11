---
title: "Criar o seu primeiro fluxo de trabalho entre aplicações da cloud e os serviços cloud - Azure Logic Apps | Microsoft Docs"
description: "Crie e execute fluxos de trabalho no Azure Logic Apps para automatizar processos empresariais para cenários de integração de sistemas e integração de aplicações empresariais (EAI)."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: "fluxo de trabalho, aplicações na cloud, serviços cloud, processos empresariais, integração de sistemas, integração de aplicações empresariais, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: LADocs; jehollan; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 204bf123509729b60b55c306050cef54aa7fecc5
ms.contentlocale: pt-pt
ms.lasthandoff: 05/17/2017

---

<a id="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services" class="xliff"></a>

# Criar o seu primeiro fluxo de trabalho de aplicação lógica para automatizar processos entre aplicações na cloud e serviços cloud.

Sem que seja necessário escrever qualquer código, pode automatizar processos empresariais mais fácil e rapidamente se criar e executar fluxos de trabalho com o [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Este primeiro exemplo mostra-lhe como criar um fluxo de trabalho de aplicação lógica básico, que analisa um feed RSS para conteúdos novos num Web site. Quando surgem itens novos no feed do Web site, a aplicação lógica envia um e-mail de uma conta do Outlook ou do Gmail.

Para criar e executar uma aplicação lógica, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, pode [inscrever-se numa subscrição Pay As You Go](https://azure.microsoft.com/pricing/purchase-options/).

  A subscrição do Azure é utilizada para faturar a utilização da aplicação lógica. Saiba como funciona a [medição da utilização](../logic-apps/logic-apps-pricing.md) e os [preços](https://azure.microsoft.com/pricing/details/logic-apps) no Azure Logic Apps.

Além disso, este exemplo requer os itens seguintes

* Uma conta do Outlook.com, do Office 365 Outlook ou do Gmail

    > [!TIP]
    > Se tiver uma [conta Microsoft](https://account.microsoft.com/account) pessoal, também tem uma conta do Outlook.com. Caso contrário, se tiver uma conta escolar ou profissional do Azure, tem uma conta do **Office 365 Outlook**.

* Uma ligação para o feed RSS do Web site. Este exemplo utiliza o [Feed RSS para as principais histórias do site CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

<a id="add-a-trigger-that-starts-your-workflow" class="xliff"></a>

## Adicionar um acionador que inicia o fluxo de trabalho

Um [*acionador*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho da aplicação lógica e é o primeiro item de que esta precisa.

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu à esquerda, escolha **Novo** > **Integração Empresarial** > **Aplicação Lógica**, conforme mostrado aqui:

     ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > Também pode escolher **Novo** e, em seguida, na caixa de pesquisa, escreva `logic app`, e prima Enter. Em seguida, escolha **Aplicação Lógica** > **Criar**.

3. Dê um nome à aplicação lógica e selecione a sua subscrição do Azure. Agora, crie ou selecione um grupo de recursos do Azure, que o ajuda a organizar e gerir recursos do Azure relacionados. Por fim, selecione a localização do datacenter para alojar a aplicação lógica. Quando estiver pronto, escolha **Afixar ao dashboard** e **Criar**.

     ![Detalhes da aplicação lógica](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > Quando seleciona **Afixar ao dashboard**, a aplicação lógica aparece no dashboard do Azure após a implementação e abre-se automaticamente. Se não aparecer no dashboard, no mosaico **Todos os recursos**, escolha **Ver Mais** e selecione a sua aplicação lógica. No menu à esquerda, escolha **Mais serviços**. Em **Integração Empresarial**, escolha **Aplicações Lógicas** e selecione a sua aplicação lógica.

4. Quando abrir a aplicação lógica pela primeira vez, o Estruturador da Aplicação Lógica mostra modelos que pode utilizar para começar. Por agora, escolha **Aplicação Lógica Vazia**, para que possa criá-la do zero.

    O Estruturador da Aplicação Lógica abre-se e mostra os serviços e os *acionadores* disponíveis que pode utilizar na sua aplicação.

5. Na caixa de pesquisa, escreva `RSS` e selecione este acionador: **RSS - quando um item de feed for publicado** 

    ![Acionador RSS](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. Introduza uma ligação para o feed RSS do Web site que pretende controlar. 

     Também pode alterar a **Frequência** e o **Intervalo**. 
     Estas definições determinam a frequência com que a aplicação lógica verifica novos itens e devolve todos os itens encontrados durante esse intervalo de tempo.

     Neste exemplo, vamos escolher todos os dias as melhores histórias publicadas no site CNN.

     ![Configurar o acionador com o feed RSS, a frequência e o intervalo](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. Guarde o seu trabalho por agora. (Na barra de comandos do estruturador, escolha **Guardar**.)

   ![Guardar a aplicação lógica](media/logic-apps-create-a-logic-app/save-logic-app.png)

   Quando guarda, a sua aplicação lógica é publicada, mas, por agora, a única coisa que faz é verificar itens novos no feed RSS especificado. 
   Para tornar este exemplo mais útil, vamos adicionar uma ação que a sua aplicação lógica realiza após o acionador ser desencadeado.

<a id="add-an-action-that-responds-to-your-trigger" class="xliff"></a>

## Adicionar uma ação que responde ao acionador

Uma [*ação*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) é uma tarefa realizada pelo fluxo de trabalho da sua aplicação lógica. Depois de adicionar um acionador à aplicação lógica, pode adicionar uma ação para realizar operações com dados gerados por esse acionador. No nosso exemplo, vamos adicionar agora uma ação que envia um e-mail quando aparecem itens novos no feed RSS do Web site.

1. No estruturador, no seu acionador, escolha **Novo passo** > **Adicionar uma ação**, conforme mostrado aqui:

   ![Adicionar uma ação](media/logic-apps-create-a-logic-app/add-new-action.png)

   O estruturador mostra os [conectores disponíveis](../connectors/apis-list.md), para que possa selecionar uma ação a ser realizada quando o acionador é desencadeado.

2. Com base na sua conta de e-mail, siga os passos para o Outlook ou o Gmail.

   * Para enviar um e-mail da conta do Outlook, na caixa de pesquisa, introduza `outlook`. Em **Serviços**, escolha **Outlook.com**, para contas Microsoft pessoais, ou **Office 365 Outlook**, para contas escolares ou profissionais do Azure. 
   Em **Ações**, selecione **Enviar e-mail**.

       ![Selecionar a ação "Enviar e-mail" do Outlook](media/logic-apps-create-a-logic-app/actions.png)

   * Para enviar um e-mail da conta do Gmail, na caixa de pesquisa, introduza `gmail`. 
   Em **Ações**, selecione **Enviar e-mail**.

       ![Escolher "Gmail - Enviar e-mail”](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. Quando lhe forem pedidas as credenciais, inicie sessão com o nome de utilizador e a palavra-passe da sua conta de e-mail. 

4. Indique os detalhes desta ação, como o endereço de e-mail de destino, e escolha os parâmetros dos dados a incluir no e-mail, como, por exemplo:

   ![Selecionar dados a incluir no e-mail](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Por isso, se optou pelo Outlook, a sua aplicação lógica poderá ser semelhante a este exemplo:

    ![Aplicação lógica concluída](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    Guarde as alterações. (Na barra de comandos do estruturador, escolha **Guardar**.)

6. Pode agora executar manualmente a sua aplicação lógica para testes. Na barra de comandos do estruturador, escolha **Executar**. Caso contrário, pode permitir que a aplicação lógica verifique o feed RSS especificado com base numa agenda que configurar.

   Se a aplicação lógica encontrar itens novos, envia um e-mail que inclui os dados que selecionou. 
   Se não forem encontrados itens novos, a aplicação ignora a ação que envia o e-mail.

7. Para monitorizar e verificar o histórico de execuções e acionadores da sua aplicação lógica, no menu da mesma, escolha **Descrição geral**.

   ![Monitorizar e ver o histórico de execuções e acionadores da aplicação lógica](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Se não encontrar os dados que esperava, na barra de comandos, experimente escolher **Atualizar**.

   Para saber mais sobre o estado ou o histórico de execuções e acionadores da sua aplicação lógica ou para a diagnosticar, veja [Troubleshoot your logic app](logic-apps-diagnosing-failures.md) (Diagnosticar a sua aplicação lógica).

      > [!NOTE]
      > A aplicação lógica continua a ser executada até que a desative. Para desativar a sua aplicação por agora, no menu da aplicação lógica, selecione **Descrição geral**. Na barra de comandos, escolha **Desativar**.

Parabéns! Acabou de configurar e executar a sua primeira aplicação lógica básica. Também ficou a saber como pode criar facilmente fluxos de trabalho que automatizam processos e integram aplicações na cloud e serviços cloud, tudo sem ter de escrever código.

<a id="manage-your-logic-app" class="xliff"></a>

## Gerir a aplicação lógica

Para gerir a sua aplicação, pode realizar tarefas como verificar o estado, editar, ver o histórico, desativar ou eliminar a sua aplicação lógica.

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu à esquerda, escolha **Mais serviços**. Em **Integração Empresarial**, escolha **Aplicações Lógicas**. Selecione a sua aplicação lógica. 

   No menu da aplicação lógica, pode encontrar as tarefas de gestão de aplicações lógicas seguintes:

   |Tarefa|Passos| 
   |:---|:---| 
   | Ver o estado da aplicação, o histórico de execuções e informações gerais| Escolha **Descrição geral**.| 
   | Editar a sua aplicação | Escolha **Estruturador da Aplicação Lógica**. | 
   | Ver a definição de JSON do fluxo de trabalho da aplicação lógica | Escolha **Vista de Código da Aplicação Lógica**. | 
   | Ver operações realizadas na aplicação lógica | Escolha **Registo de atividades**. | 
   | Ver as versões passadas da aplicação lógica | Escolha **Versões**. | 
   | Desativar temporariamente a aplicação | Escolha **Descrição geral** e, na barra de comandos, escolha **Desativar**. | 
   | Eliminar a aplicação | Escolha **Descrição geral** e, na barra de comandos, escolha **Eliminar**. Introduza o nome da sua aplicação lógica e selecione **Eliminar**. | 

<a id="get-help" class="xliff"></a>

## Obter ajuda

Para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar o Azure Logic Apps e os conectores, vote ou submeta ideais no [site de comentários dos utilizadores do Azure Logic Apps](http://aka.ms/logicapps-wish).

<a id="next-steps" class="xliff"></a>

## Passos seguintes

*  [Adicionar condições e executar fluxos de trabalho](../logic-apps/logic-apps-use-logic-app-features.md)
*     [Modelos de aplicações lógicas](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Create logic apps from Azure Resource Manager templates](../logic-apps/logic-apps-arm-provision.md) (Criar aplicações lógicas a partir de modelos do Azure Resource Manager)

