---
title: Criar fluxos de trabalho a partir de modelos - Azure Logic Apps | Microsoft Docs
description: "Criar fluxos de trabalho mais rápidos, utilizando modelos de aplicação lógica"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8adda0d9cd6af98c04f2432eeabbc003ad403719
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Criar fluxos de trabalho da aplicação de lógica a partir de modelos prebuilt

Para ajudar a começar a criar fluxos de trabalho mais rapidamente, as Logic Apps fornecem modelos, que são prebuilt logic apps que se seguem padrões frequentemente utilizadas. Utilizar estes modelos fornecia ou editá-los para ajustar o seu cenário.

Seguem-se algumas categorias do modelo:

| Tipo de modelo | Descrição | 
| ------------- | ----------- | 
| Modelos do Enterprise cloud | Para integrar o Blob do Azure, o Dynamics CRM, Salesforce, caixa e inclui outros conectores para a nuvem de enterprise tem. Por exemplo, pode utilizar estes modelos para organizar os clientes de negócio potenciais clientes potenciais ou fazer uma cópia dos seus dados de ficheiros empresariais. | 
| Modelos de produtividade pessoal | Melhorar a produtividade pessoal definindo lembretes diárias, desativar importante itens de trabalho a lista de ações pendentes e automatizar tarefas demoradas para baixo para um passo de aprovação de utilizador único. | 
| Modelos de nuvem de consumidor | Para integrar os serviços de redes sociais como Twitter, Slack e e-mail. Útil para strengthening de redes sociais iniciativas de marketing. Estes modelos também incluem tarefas, tais como nuvem copiar, o que aumenta a produtividade, poupando tempo nas tarefas tradicionalmente repetitivas. | 
| Modelos de pacote de integração do Enterprise | Para configurar VETER (validar, extrair, transformar, enriqueça, encaminhar) pipelines, receber um X12 documento EDI através de AS2 e transformar em XML e processamento X12, EDIFACT e mensagens AS2. | 
| Modelos de padrão de protocolo | Para implementar padrões de protocolo como resposta-pedido através de HTTP e integrações em FTP e SFTP. Utilizar estes modelos como fornecidos ou criar nos mesmos padrões de protocolo complexas. | 
||| 

Se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter mais informações sobre como criar uma aplicação lógica, consulte [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-logic-apps-from-templates"></a>Criar as logic apps a partir de modelos

1. Se ainda não o fez, inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu principal do Azure, escolha **Nova** > **Integração Empresarial** > **Aplicação Lógica**.

   ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições da tabela abaixo desta imagem:

   ![Indicar os detalhes da aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Nome** | *nome-da-sua-aplicação-lógica* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *o-nome-da-sua-subscrição-do-Azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de recursos** | *o-nome-do-seu-grupo-de-recursos-do-Azure* | Crie ou selecione um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) para esta aplicação de lógica e para organizar a todos os recursos associados a esta aplicação. | 
   | **Localização** | *a-região-do-seu-datacenter-do-Azure* | Selecione a região do datacenter para implementar a sua aplicação lógica, como, por exemplo, E.U.A. Oeste. | 
   | **Log Analytics** | **Desativar** (predefinição) ou **no** | Ativar [registo diagnóstico](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) para a sua aplicação lógica através de [Log Analytics do Azure](../log-analytics/log-analytics-overview.md). Requer que já tenham um [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) área de trabalho. | 
   |||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard**. Desta forma, a aplicação lógica aparece automaticamente no seu dashboard do Azure e abre-se após a implementação. Escolha **Criar**.

   > [!NOTE]
   > Se não quiser afixar a sua aplicação lógica, tem de localizá-la e abri-la manualmente após a implementação, para poder continuar.

   Após o Azure implementar a aplicação lógica, abre-se o Estruturador de Aplicações Lógicas, que mostra uma página com um vídeo de introdução. 
   Abaixo do vídeo, estão disponíveis modelos para padrões de aplicações lógicas comuns. 

5. Desloque-se após os acionadores de vídeo e comuns de introdução para **modelos**. Escolha um modelo prebuilt. Por exemplo:

   ![Escolha um modelo de aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Para criar a sua aplicação lógica a partir do zero, escolha **aplicação lógica em branco**.

   Quando seleciona um modelo prebuilt, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo de prebuilt](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Para continuar com o modelo selecionado, selecione **utilizar este modelo**. 

7. Com base nos conectores no modelo, é-lhe pedido que efetue qualquer uma destes passos:

   * Inicie sessão com as suas credenciais para os sistemas ou serviços que são referenciados pelo modelo.

   * Crie ligações para quaisquer serviços nem sistemas referidos pelo modelo. Para criar uma ligação, forneça um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurou a estas ligações, escolha **continuar**.

   Por exemplo:

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Quando tiver terminado, a aplicação lógica abre-se e é apresentada no Designer de aplicações lógicas.

   > [!TIP]
   > Para voltar para o Visualizador de modelo, escolha **modelos** na barra de ferramentas estruturador. Esta ação elimina as alterações não guardadas, pelo que é apresentada uma mensagem de aviso a confirmar o pedido.

8. Continue a criar uma aplicação lógica.

   > [!NOTE] 
   > Vários modelos incluem conectores que poderão já ter pré-preenchida propriedades necessárias. No entanto, alguns modelos ainda poderão exigir que forneça valores antes de poder implementar a aplicação lógica corretamente. Se tentar implementar sem concluir os campos de propriedade em falta, receberá uma mensagem de erro. 

## <a name="update-logic-apps-with-templates"></a>Atualizar as logic apps com modelos

1. No [portal do Azure](https://portal.azure.com "portal do Azure"), localize e abra a sua aplicação lógica em ésimo Designer de aplicação lógica.

2. Na barra de ferramentas estruturador, escolha **modelos**. Esta ação elimina as alterações não guardadas, pelo que é apresentada uma mensagem de aviso para que possa confirmar que pretende continuar. Para confirmar, escolha **OK**. Por exemplo:

   ![Escolha "Modelos"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Desloque-se após os acionadores de vídeo e comuns de introdução para **modelos**. Escolha um modelo prebuilt. Por exemplo:

   ![Escolha um modelo de aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Quando seleciona um modelo prebuilt, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo de prebuilt](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Para continuar com o modelo selecionado, selecione **utilizar este modelo**. 

5. Com base nos conectores no modelo, é-lhe pedido que efetue qualquer uma destes passos:

   * Inicie sessão com as suas credenciais para os sistemas ou serviços que são referenciados pelo modelo.

   * Crie ligações para quaisquer serviços nem sistemas referidos pelo modelo. Para criar uma ligação, forneça um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurou a estas ligações, escolha **continuar**.

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   A aplicação lógica agora abre-se e é apresentada no Designer de aplicações lógicas.

8. Continue a criar uma aplicação lógica. 

   > [!TIP]
   > Se ainda não guardar as alterações, pode eliminar o seu trabalho e regressar à sua aplicação lógica anterior. Na barra de ferramentas estruturador, escolha **rejeitar**.

> [!NOTE] 
> Vários modelos incluem conectores que poderão ter já pré-preenchido propriedades necessárias. No entanto, alguns modelos ainda poderão exigir que forneça valores antes de poder implementar a aplicação lógica corretamente. Se tentar implementar sem concluir os campos de propriedade em falta, receberá uma mensagem de erro.

## <a name="deploy-logic-apps-built-from-templates"></a>Implementar as logic apps criadas a partir de modelos

Depois de efetuar as alterações ao modelo, pode guardar as alterações. Esta ação publica também automaticamente a sua aplicação lógica.

Na barra de ferramentas do estruturador, escolha **Guardar**.

![Guarde e publicar a sua aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como criar aplicações lógicas através de exemplos, cenários, stories de cliente e instruções.

> [!div class="nextstepaction"]
> [Exemplos de aplicação de lógica de revisão, cenários e instruções](../logic-apps/logic-apps-examples-and-scenarios.md)