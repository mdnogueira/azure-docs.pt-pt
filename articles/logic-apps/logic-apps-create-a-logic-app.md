---
title: "Automatizar fluxos de trabalho entre sistemas e serviços cloud - Azure Logic Apps | Microsoft Docs"
description: "Crie aplicações lógicas para automatizar fluxos de trabalho para cenários de integração de sistemas e integração de aplicações empresariais (EAI)."
author: ecfan
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
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>Automatizar o seu primeiro fluxo de trabalho para processar dados com uma aplicação lógica

Para integrar sistemas e serviços mais depressa na sua organização, pode automatizar fluxos de trabalho e processos de negócios com o [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). Este início rápido mostra como pode criar e executar facilmente um fluxo de trabalho automatizado mediante a criação de uma aplicação lógica. A aplicação de exemplo mostra como automatizar um fluxo de trabalho que verifica o feed RSS de um site relativamente a novos itens e envia um e-mail para cada um dos mesmos.

Esta aplicação lógica de exemplo envia um e-mail como o deste exemplo:

![E-mail enviado para novo item de feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

E este é o fluxo de trabalho da aplicação lógica de nível elevado que criou:

![Descrição geral - exemplo de aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

Neste início rápido, vai aprender a:

> [!div class="checklist"]
> * Criar uma aplicação lógica em branco.
> * Adicionar um acionador para iniciar o fluxo de trabalho quando um item novo aparece no feed RSS.
> * Adicionar uma ação para enviar um e-mail com detalhes sobre o item do feed RSS.
> * Executar o fluxo de trabalho da aplicação lógica.

Se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de qualquer fornecedor de e-mail que o Azure Logic Apps suporte, para enviar notificações. Por exemplo, pode utilizar o Outlook do Office 365, o Outlook.com, ou o Gmail. Para outros conectores de e-mail suportados, [reveja a lista de conectores](https://docs.microsoft.com/connectors/). Este guia de introdução utiliza o Outlook do Office 365.

  > [!TIP]
  > Se tiver uma [conta Microsoft](https://account.microsoft.com/account) pessoal, também tem uma conta do Outlook.com. Caso contrário, se tiver uma conta escolar ou profissional do Azure, tem uma conta do Outlook do Office 365.

* Uma ligação para o feed RSS do Web site. Este exemplo utiliza o [feed RSS para as principais notícias do site da Reuters](http://feeds.reuters.com/reuters/topNews): `http://feeds.reuters.com/reuters/topNews`

Este início rápido não requer a escrita de código, mas o Logic Apps suporta outros cenários que utilizam código, como, por exemplo, cenários em que executa o seu próprio código a partir de uma aplicação lógica com as [Funções do Azure](../azure-functions/functions-overview.md).

## <a name="create-a-blank-logic-app"></a>Criar uma aplicação lógica em branco 

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2. No menu principal do Azure, escolha **Nova** > **Integração Empresarial** > **Aplicação Lógica**.

   ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições da tabela abaixo desta imagem:

   ![Indicar os detalhes da aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-da-sua-aplicação-lógica* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *o-nome-da-sua-subscrição-do-Azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de recursos** | *o-nome-do-seu-grupo-de-recursos-do-Azure* | Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) para esta aplicação lógica e para organizar todos os recursos associados a esta aplicação. | 
   | **Localização** | *a-região-do-seu-datacenter-do-Azure* | Selecione a região do datacenter para implementar a sua aplicação lógica, como, por exemplo, E.U.A. Oeste. | 
   | **Log Analytics** | Desativado | Ative o registo de diagnósticos na sua aplicação lógica, mas mantenha a definição **Desativado** neste início rápido. | 
   |||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard**. Desta forma, a aplicação lógica aparece automaticamente no seu dashboard do Azure e abre-se após a implementação. Escolha **Criar**.

   > [!NOTE]
   > Se não quiser afixar a sua aplicação lógica, tem de localizá-la e abri-la manualmente após a implementação, para poder continuar.

   Após o Azure implementar a aplicação lógica, abre-se o Estruturador de Aplicações Lógicas, que mostra uma página com um vídeo de introdução. 
   Abaixo do vídeo, estão disponíveis modelos para padrões de aplicações lógicas comuns. 
   Este guia de introdução cria a sua aplicação lógica do zero. 

5. Desloque-se para lá do vídeo de introdução e dos acionadores comuns. Em **Modelos**, escolha **Aplicação Lógica em Branco**.

   ![Escolher o modelo de aplicação lógica em branco](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   O Estruturador de Aplicações Lógicas mostra-lhe os conectores disponíveis e os respetivos acionadores, que são utilizados para iniciar os fluxos de trabalho das aplicações lógicas.

   ![Acionadores da aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>Adicionar um acionador para detetar itens novos

Todos os fluxos de trabalho das aplicações lógicas começam com um [acionador](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). O acionador é acionado quando ocorre um evento específico ou quando os dados novos satisfazem a condição que tiver definido. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho.

1. Na caixa de pesquisa, escreva "rss" como o filtro. Selecione este acionador: **RSS - quando for publicado um item de feed** 

   ![Selecionar acionador: “RSS - quando for publicado um item de feed”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Forneça a ligação para o feed RSS que quer monitorizar, como, por exemplo, `http://feeds.reuters.com/reuters/topNews`. Defina o intervalo e a frequência da periodicidade. Este exemplo verifica o feed de cinco em cinco minutos.

   ![Configurar o acionador com o feed RSS, a frequência e o intervalo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   O Logic Apps cria uma ligação para o feed RSS.

   > [!TIP]
   > Para simplificar a vista no estruturador, pode fechar e ocultar os detalhes de uma forma - basta clicar no interior da barra de título da forma.

3. Guarde o seu trabalho. Na barra de ferramentas do estruturador, escolha **Guardar**. 

   ![Guardar a aplicação lógica](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar o feed RSS. Por isso, vamos adicionar uma ação que responde quando o acionador é acionado.

## <a name="add-an-action-to-send-email"></a>Adicionar uma ação para enviar e-mail

Agora que já tem um acionador, adicione uma [ação](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) que envia um e-mail quando aparecer um item novo no feed RSS. O fluxo de trabalho executa esta ação depois de o acionador ser acionado.

1. No Estruturador das Aplicações Lógicas, no acionador, escolha **+ Novo passo** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-create-a-logic-app/add-new-action.png)

   O estruturador mostra as ações que a sua aplicação lógica pode executar quando o acionador é acionado.

   ![Selecione a partir da lista de ações](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Na caixa de pesquisa, introduza "enviar e-mail" como o filtro. Encontre e selecione o conector de e-mail que quer utilizar. Em seguida, selecione a ação "enviar e-mail" para esse conector. Por exemplo: 

   * Relativamente a contas escolares ou profissionais do Azure, selecione Office 365 Outlook (Outlook do Office 365). 
   * Quanto a contas Microsoft pessoais, selecione Outlook.com. 
   * Para contas do Gmail, selecione Gmail. 

   Este guia de introdução utiliza o Outlook do Office 365. 
   Se utilizar outro fornecedor de e-mail, os passos são os mesmos, mas a IU poderá ser diferente. 

   ![Selecione esta ação: "Office 365 Outlook - enviar e-mail"](./media/logic-apps-create-a-logic-app/actions.png)

3. Quando lhe forem pedidas as credenciais, inicie sessão com o nome de utilizador e a palavra-passe da sua conta de e-mail. 

   O Logic Apps cria uma ligação para a sua conta de e-mail.

4. Agora, especifique os dados que pretende incluir no e-mail. 

   1. Na caixa **Para**, introduza o endereço de e-mail do destinatário. 
   Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

   2. Na caixa **Assunto**, introduza o assunto do e-mail. 
   Neste exemplo, introduza “Item de RSS novo:", conforme mostrado:

      ![Introduza o assunto do e-mail](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      Quando clica dentro da caixa de edição, abre-se a **lista Adicionar conteúdo dinâmico**, para que possa selecionar os campos de dados disponíveis para inclusão na sua ação. 
      Se a lista de conteúdo dinâmico não se abrir, na respetiva caixa de edição, escolha **Adicionar conteúdo dinâmico**.

   3. Na lista **Adicionar conteúdo dinâmico**, selecione **Título do feed**, o que inclui o título do item no e-mail.

      ![Introduza o assunto do e-mail](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      Quando estiver pronto, o assunto do e-mail terá o aspeto deste exemplo:

      ![Título do feed adicionado](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > Se selecionar um campo que contém uma matriz, como **categories-item**, o estruturador adiciona automaticamente um ciclo “For each” em torno da ação que referencia esse campo. Desta forma, a sua aplicação lógica pode realizar essa ação em cada item da matriz. 
      > 
      > Para remover o ciclo, escolha as reticências (**...** ) na barra de título do ciclo e escolha **Eliminar**.

   4. Na caixa **Corpo**, introduza o conteúdo do corpo do e-mail. 
   Neste exemplo, introduza este texto e selecione estes campos:

      ![Adicione conteúdo para o corpo do e-mail](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | Campo | Descrição | 
      | ----- | ----------- | 
      | **Título do feed** | Mostra o título do item. | 
      | **Feed publicado em** | Mostra a data e a hora de publicação do item | 
      | **Ligação principal do feed** | Mostra o URL do item. | 
      ||| 

      > [!TIP]
      > Para adicionar linhas em branco a uma caixa de edição, prima Shift + Enter. 
      
5. Guarde o seu trabalho. Na barra de ferramentas do estruturador, escolha **Guardar**.

   ![Aplicação lógica concluída](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>Executar o fluxo de trabalho da sua aplicação lógica

Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas do estruturador, escolha **Executar**. Caso contrário, pode aguardar que seja executada na agenda que tiver configurado.

![Executar a aplicação lógica](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

Se o feed RSS tiver itens novos, a sua aplicação lógica envia um e-mail para cada item novo. Por exemplo, eis um e-mail de exemplo do Outlook enviado por esta aplicação lógica:

![E-mail enviado para novo item de feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Se o feed não tiver itens novos, a aplicação lógica ignora o passo que envia o e-mail e aguarda pelo próximo intervalo antes de voltar a verificar. 

> [!TIP]
> Se não receber nenhum e-mail, veja a pasta de e-mail de lixo. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns! Acabou de criar e executar a sua primeira aplicação lógica. Este início rápido mostrou-lhe como é fácil e rápido criar fluxos de trabalho automatizados para integrar sistemas e serviços.

## <a name="clean-up-resources"></a>Limpar recursos

Enquanto não a desativar ou eliminar, a sua aplicação lógica permanece em execução e, possivelmente, a incorrer em custos na sua subscrição do Azure. Além disso, quando cria ligações para a sua aplicação lógica, essas ligações permanecem, mesmo depois de eliminar a aplicação lógica. 

Quando tiver terminado, certifique-se de que desativa ou elimina quaisquer recursos nos casos em que não quer incorrer em custos ou que não quer manter. Para eliminar todos os recursos que criou para este início rápido, elimine o grupo de recursos do Azure que criou para esta aplicação lógica. 

### <a name="delete-resource-group"></a>Eliminar grupo de recursos

Se não quiser manter nada que esteja relacionado com a sua aplicação lógica, elimine o grupo de recursos que criou para este início rápido e todos os recursos associados. Saiba mais sobre [como gerir grupos de recursos do Azure](../azure-resource-manager/resource-group-portal.md#manage-resources).

1. No menu do Azure, escolha **Grupos de recursos**.

2. Escolha o grupo de recursos que pretende eliminar. No menu do grupo de recursos, escolha **Descrição geral**, se ainda não estiver selecionada. 

3. Reveja todos os recursos no grupo que quer eliminar. Quando estiver pronto, escolha **Eliminar grupo de recursos**, na barra de ferramentas do grupo de recursos.

### <a name="turn-off-logic-app"></a>Desativar a aplicação lógica

Para parar de executar a sua aplicação lógica sem eliminar o seu trabalho, desative-a. 

No menu da aplicação lógica, escolha **Descrição geral**. Na barra de ferramentas, escolha **Desativar**.

  ![Desativar a sua aplicação lógica](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra a aplicação lógica.

### <a name="delete-logic-app"></a>Eliminar a aplicação lógica

Pode eliminar apenas a aplicação lógica e manter todos os outros recursos associados, como, por exemplo, as ligações que criou.

1. No menu da aplicação lógica, escolha **Descrição geral**. Na barra de ferramentas, escolha **Eliminar**. 

   ![Eliminar a aplicação lógica](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra a aplicação lógica.

2. Confirme que pretende eliminar a sua aplicação lógica e escolha **Eliminar**.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Create logic app workflows from prebuilt templates](../logic-apps/logic-apps-create-logic-apps-from-templates.md) (Criar fluxos de trabalho de aplicações lógicas a partir de modelos pré-criados)