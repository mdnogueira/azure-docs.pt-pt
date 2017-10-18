---
title: "Criar o seu primeiro fluxo de trabalho automatizado entre sistemas e serviços cloud - Azure Logic Apps | Microsoft Docs"
description: "Crie e execute aplicações lógicas para automatizar processos empresariais e fluxos de trabalho para cenários de integração de sistemas e integração de aplicações empresariais (EAI)."
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Criar a sua primeira aplicação de lógica para automatizar fluxos de trabalho e processos através do portal do Azure

Sem escrever código, pode integrar sistemas e serviços ao criar e executar fluxos de trabalho automatizados com o [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). Para mostrar como é fácil automatizar tarefas com um fluxo de trabalho, este tutorial cria uma aplicação lógica básica que verifica feeds RSS relativamente a conteúdos novos num site e envia um e-mail para cada item novo nesses feeds. 

![Descrição geral - primeiro exemplo de aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica em branco.
> * Adicionar um acionador para iniciar a sua aplicação lógica quando é publicado um item de feed RSS.
> * Adicionar uma ação para enviar um e-mail com os detalhes do item de feed RSS.
> * Executar e testar a sua aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, pode [inscrever-se numa subscrição Pay As You Go](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de e-mail de [qualquer fornecedor de e-mail que o Azure Logic Apps suporte](../connectors/apis-list.md), para enviar notificações. Por exemplo, pode utilizar o Outlook do Office 365, o Outlook.com, o Gmail ou outro fornecedor suportado. Este tutorial utiliza o Outlook do Office 365.

  > [!TIP]
  > Se tiver uma [conta Microsoft](https://account.microsoft.com/account) pessoal, também tem uma conta do Outlook.com. Caso contrário, se tiver uma conta escolar ou profissional do Azure, tem uma conta do Outlook do Office 365.

* Uma ligação para o feed RSS do Web site. Este exemplo utiliza o [Feed RSS para as principais histórias do site CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1. Criar uma aplicação lógica em branco 

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu principal do Azure, escolha **Nova** > **Integração Empresarial** > **Aplicação Lógica**.

   ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições especificadas na tabela.

   ![Indicar os detalhes da aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-da-sua-aplicação-lógica* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *a-sua-subscrição-do-azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de recursos** | *o-seu-grupo-de-recursos-do-Azure* | Crie ou selecione um grupo de recursos do Azure, que o ajuda a organizar e gerir recursos do Azure relacionados. | 
   | **Localização** | *a-sua-região-do-Azure* | Selecione a região do datacenter para implementar a sua aplicação lógica. | 
   |||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard** e escolha **Criar**.

   Acabou de criar um recurso do Azure para a aplicação lógica. 
   Quando o Azure implementar a sua aplicação lógica, o Estruturador de Aplicações Lógicas mostra-lhe modelos de padrões comuns, para que possa começar mais depressa.

   > [!NOTE] 
   > Quando seleciona **Afixar ao dashboard**, a aplicação lógica aparece no dashboard do Azure após a implementação e abre-se automaticamente no Estruturador de Aplicações Lógicas. Caso contrário, pode encontrar e abrir a aplicação lógica manualmente.

5. Por agora, em **Modelos**, escolha **Aplicação Lógica em Branco**, para criar a sua aplicação do zero.

   ![Escolher o modelo da aplicação lógica](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   O Estruturador de Aplicações Lógicas mostra-lhe agora os [*conectores*](../connectors/apis-list.md) disponíveis e respetivos [*acionadores*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), que são utilizados para iniciar o fluxo de trabalho da sua aplicação lógica.

   ![Acionadores da aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. Adicionar um acionador para iniciar o fluxo de trabalho

Todas as aplicações lógicas têm de começar com um [*acionador*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). O acionador é acionado quando ocorre um evento específico ou quando os dados novos satisfazem a condição que tiver definido. O motor do Logic Apps cria, depois, uma instância de aplicação lógica para executar o fluxo de trabalho. Sempre que o acionador é acionado, o motor cria outra instância separada que executa o fluxo de trabalho d sua aplicação lógica.

1. Na caixa de pesquisa, escreva "rss" como o filtro. Selecione este acionador: **RSS - quando for publicado um item de feed** 

   ![Selecionar acionador: “RSS - quando for publicado um item de feed”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Forneça a ligação para o feed RSS do Web site que quer controlar, como, por exemplo, `http://rss.cnn.com/rss/cnn_topstories.rss`. Defina o intervalo e a frequência da periodicidade. Neste exemplo, defina estas propriedades para verificar o feed todos os dias. 

   ![Configurar o acionador com o feed RSS, a frequência e o intervalo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. Guarde o seu trabalho por agora. Na barra de ferramentas do estruturador, escolha **Guardar**.
Para fechar e ocultar os detalhes do acionador, escolha a barra de título do mesmo.

   ![Guardar a aplicação lógica](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   A aplicação lógica está agora a funcionar, mas não faz mais nada que não verificar a existência de novos itens no feed RSS enquanto não adicionar ações ao fluxo de trabalho. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. Adicionar uma ação que responde ao acionador

Agora, adicione uma [*ação*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), que é uma tarefa que o fluxo de trabalho da sua aplicação lógica realiza. Neste exemplo, adicione uma ação que envia um e-mail quando aparecer um novo item no feed RSS.

1. No Estruturador das Aplicações Lógicas, no acionador, escolha **+ Novo passo** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-create-a-logic-app/add-new-action.png)

   O estruturador mostra os [conectores disponíveis](../connectors/apis-list.md), para que possa selecionar uma ação a ser realizada quando o acionador é desencadeado.

   ![Selecione a partir da lista de ações](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Na caixa de pesquisa, introduza "enviar e-mail" como o filtro. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar e-mail" para o conector. Por exemplo: 

   * Numa conta escolar ou profissional do Azure, selecione o conector Office 365 Outlook. 
   * Em contas Microsoft pessoais, selecione o conector Outlook.com. 
   * Em contas do Gmail, selecione o conector Gmail. 

   Vamos continuar a utilizar o conector Office 365 Outlook. 
   Se utilizar outro fornecedor, os passos são os mesmos, mas a IU poderá ser diferente. 

   ![Selecione esta ação: "Office 365 Outlook - enviar e-mail"](./media/logic-apps-create-a-logic-app/actions.png)

3. Quando lhe forem pedidas as credenciais, inicie sessão com o nome de utilizador e a palavra-passe da sua conta de e-mail. 

4. Indique os detalhes especificados na tabela e escolha os campos que quer ver incluídos no e-mail.

   | Para | Passos | 
   | -- | ----- | 
   | Selecionar os campos disponíveis no fluxo de trabalho. | Clique no interior de uma caixa de edição, para que seja aberta a lista **Conteúdo dinâmico**, ou escolha **Adicionar conteúdo dinâmico**. | 
   | Ver outros campos disponíveis. | Na lista **Conteúdo dinâmico**, escolha **Ver mais** para cada secção.  | 
   | Adicionar linhas em branco numa caixa de edição. | Prima Shift + Enter. | 
   | Fechar a lista **Conteúdo dinâmico**. | Escolha novamente **Adicionar conteúdo dinâmico**. | 
   ||| 

   ![Selecionar dados a incluir no e-mail](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Para** | *endereço-de-e-mail-do-destinatário* | Introduza o endereço de e-mail do destinatário. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. | 
   | **Assunto** | Nova publicação da CNN: **Título do feed** | Introduza o conteúdo para o assunto do e-mail. <p>Neste tutorial, introduza o texto sugerido e selecione o campo **Título do feed** do acionador, o que mostra o título do item do feed. | 
   | **Corpo** | Título: **Título do feed** <p>Data de publicação: **Ligação principal do feed** <p>Ligação: **Ligação principal do feed** | Introduza o conteúdo para o corpo do e-mail. <p>Neste tutorial, introduza o texto sugerido e selecione estes campos do acionador: <p>- **Título do feed**, que mostra novamente o título do item do feed </br>- **Feed publicado em**, que mostra a data e a hora de publicação do item </br>- **Ligação principal do feed**, que mostra o URL do item do feed | 
   |||| 

   > [!NOTE] 
   > Se selecionar um campo que armazena uma matriz, o estruturador adiciona automaticamente um ciclo “For each” em torno da ação que referencia essa matriz. Desta forma, a sua aplicação lógica realiza essa ação em cada item da matriz.

5. Quando tiver terminado, guarde as alterações. Na barra de ferramentas do estruturador, escolha **Guardar**.

   ![Aplicação lógica concluída](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   Para testar a sua aplicação lógica agora, avance para a secção seguinte.

## <a name="4-run-and-test-your-workflow"></a>4. Executar e testar o fluxo de trabalho

1. Para executar manualmente a sua aplicação lógica para fins de teste, na barra de ferramentas do estruturador, escolha **Executar**. Caso contrário, pode permitir que a aplicação lógica verifique o feed RSS especificado com base numa agenda que configurar.

   ![Executar a aplicação lógica](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   Se a aplicação lógica encontrar itens novos, envia um e-mail que inclui os dados que selecionou, como, por exemplo:

   ![E-mail enviado para novo item de feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   Se a aplicação não encontrar itens novos, ignora a ação que envia o e-mail e aguarda pelo próximo intervalo antes de voltar a verificar. 

2. Para rever o histórico de execuções e acionadores da sua aplicação lógica, no menu da mesma, escolha **Descrição geral**.
Para ver mais detalhes sobre uma execução, selecione a linha relativa à mesma.

   ![Monitorizar e ver o histórico de execuções e acionadores da aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Se não encontrar os dados que esperava, na barra de ferramentas, experimente escolher **Atualizar**.

   Quer a execução tenha passado ou falhado, a vista Detalhes da Execução mostra os passos que passaram ou falharam. 

   ![Ver detalhes da execução de uma aplicação lógica](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   Para saber mais sobre o estado, o histórico de execuções e o histórico de acionadores da sua aplicação lógica ou para a diagnosticar, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Diagnosticar a sua aplicação lógica).

3. Para ver as entradas e saídas de cada passo, expanda o passo que pretende rever. Estas informações podem ajudá-lo a diagnosticar e depurar problemas na sua aplicação lógica. Por exemplo:

   ![Ver detalhes do passo](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   Para obter mais informações, veja [Monitor your logic app](../logic-apps/logic-apps-monitor-your-logic-apps.md) (Monitorizar a sua aplicação lógica).

Parabéns! Acabou de criar e executar a sua primeira aplicação lógica básica. Este exemplo mostra como pode criar facilmente fluxos de trabalho que automatizam processos para integrar sistemas e serviços, tudo sem ter de escrever código.

> [!NOTE]
> A aplicação lógica continua a ser executada até que a desative. Para desativar temporariamente a sua aplicação, avance para a secção seguinte.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utiliza recursos e realiza ações que podem implicar custos na sua subscrição do Azure. Quando concluir o tutorial e os testes, certifique-se de que desativa ou elimina quaisquer recursos nos casos em que não quer incorrer em custos.

Pode parar a execução e o envio de e-mails na aplicação lógica sem ter de eliminá-la. No menu da aplicação lógica, escolha **Descrição geral**. Na barra de ferramentas, escolha **Desativar**.

![Desativar a sua aplicação lógica](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>FAQ

**P:** O que mais posso fazer com a minha aplicação lógica? </br>
**R:** Pode realizar outras tarefas, como, por exemplo, editar, ver a definição de JSON, rever o registo de atividades ou eliminar a aplicação.

Para encontrar outras tarefas de gestão de aplicações lógicas, reveja estes comandos no menu da aplicação lógica:

| Tarefa | Passos | 
| ---- | ----- | 
| Ver o estado, as execuções, o histórico de acionadores e informações gerais da aplicação | Escolha **Descrição geral**. | 
| Editar a sua aplicação | Escolha **Estruturador da Aplicação Lógica**. | 
| Ver a definição de JSON do fluxo de trabalho da aplicação lógica | Escolha **Vista de Código da Aplicação Lógica**. | 
| Ver operações realizadas na aplicação lógica | Escolha **Registo de atividades**. | 
| Ver as versões passadas da aplicação lógica | Escolha **Versões**. | 
| Desativar temporariamente a aplicação | Escolha **Descrição geral** e, na barra de ferramentas, escolha **Desativar**. | 
| Eliminar a aplicação | Escolha **Descrição geral** e, na barra de ferramentas, escolha **Eliminar**. Introduza o nome da sua aplicação lógica e selecione **Eliminar**. | 
||| 

## <a name="get-support"></a>Obter suporte

* Para perguntas sobre o Azure Logic Apps, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Quer ajudar a melhorar o Azure Logic Apps e os conectores? Vote ou submeta ideias no [site Azure Logic Apps User Voice](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Create your logic app with Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md) (Criar a sua aplicação lógica com o Visual Studio)
* [Add conditions and run workflows](../logic-apps/logic-apps-use-logic-app-features.md) (Adicionar condições e executar fluxos de trabalho)
*   [Logic app templates](../logic-apps/logic-apps-use-logic-app-templates.md) (Modelos de aplicações lógicas)
* [Create logic apps from Azure Resource Manager templates](../logic-apps/logic-apps-arm-provision.md) (Criar aplicações lógicas a partir de modelos do Azure Resource Manager)
* [Logic Apps usage metering](../logic-apps/logic-apps-pricing.md) (Medição da utilização de Aplicações Lógicas) 
* [Preços de Aplicações Lógicas](https://azure.microsoft.com/pricing/details/logic-apps)
