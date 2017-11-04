---
title: "Gerir as logic apps no Visual Studio – Azure Logic Apps | Microsoft Docs"
description: Gerir as logic apps e outros recursos do Azure com o Visual Studio Cloud Explorer
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Gerir as logic apps com o Visual Studio Cloud Explorer

Embora o [portal do Azure](https://portal.azure.com/) oferece uma excelente forma de conceber e gerir o Azure Logic Apps, pode utilizar o Visual Studio Cloud Explorer para gerir muitos recursos do Azure, incluindo as logic apps. Visual Studio Cloud Explorer permite-lhe procurar, gerir, editar e transferência publicados as logic apps. Tarefas de gestão incluem os ativar, desativar e ver executar histórico. 

Antes de poder aceder e gerir as logic apps no Visual Studio, instalar e configurar estas ferramentas do Visual Studio para o Azure Logic Apps. 

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2015 ou Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Azure SDK mais recente](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Acesso Web ao utilizar o estruturador incorporado

## <a name="install-visual-studio-tools-for-logic-apps"></a>Instalar as ferramentas do Visual Studio para aplicações lógicas

Depois de instalar os pré-requisitos, transfira e instale as ferramentas do Azure Logic Apps para o Visual Studio.

1. Abra o Visual Studio. No **ferramentas** menu, selecione **extensões e atualizações**.
2. Expanda o **Online** categoria, pelo que pode procurar online na galeria do Visual Studio.
3. Navegue ou procure **Logic Apps** até encontrar **Azure Logic Apps Tools para Visual Studio**.
4. Para transferir e instalar a extensão, clique em **transferir**.
5. Reinicie o Visual Studio após a instalação.

> [!NOTE]
> Para transferir diretamente as ferramentas do Azure Logic Apps para o Visual Studio, visite o [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Procurar logic apps no Cloud Explorer

1.  Para abrir o Explorador de nuvem, no **vista** menu, escolha **Cloud Explorer**.
2.  Navegue para a sua aplicação lógica, através do grupo de recursos ou tipo de recurso. 

    * Se procurar por tipo de recurso, selecione a sua subscrição do Azure, expanda o **Logic Apps** secção e selecione a sua aplicação lógica. 
    * Se procurar por grupo de recursos, expanda o grupo de recursos que tenha a sua aplicação lógica e selecione a sua aplicação lógica.

    Para ver os comandos para a sua aplicação lógica, clique com o botão direito a aplicação lógica ou na parte inferior do Cloud Explorer, escolha o **ações** menu.

    ![Navegue para a sua aplicação lógica](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Editar a sua aplicação lógica com o Designer de aplicações lógicas

A partir do Explorador de nuvem, pode abrir uma aplicação lógica implementado no designer mesmo que utiliza no portal do Azure. 

* Para editar a sua aplicação lógica, no Explorador de nuvem, a aplicação lógica com o botão direito e selecione **aberta com o Editor de aplicação lógica**. 

* Para publicar as atualizações para a nuvem, escolha **publicar**. 

* Para iniciar uma nova execução, escolha **executar acionador**.

![Designer de aplicações lógicas](./media/logic-apps-manage-from-vs/designer.png)

A partir do designer, pode também **transferir** uma aplicação lógica. Esta ação automaticamente parameterizes a definição da aplicação lógica e guarda a definição de como um modelo de implementação Azure Resource Manager. Pode adicionar este modelo de implementação para o projeto do grupo de recursos do Azure.

## <a name="browse-your-logic-app-run-history"></a>Procure a aplicação de lógica histórico de execução

Para ver o histórico de execução para a sua aplicação lógica, a aplicação lógica com o botão direito e selecione **histórico de execução Open**. Para reordenar o histórico de execução com base em qualquer uma das propriedades mostradas, selecione o cabeçalho de coluna.

![histórico de execução](media/logic-apps-manage-from-vs/runs.png)

Para mostrar o histórico de execução de uma instância, pelo que pode rever os resultados de execução, incluindo as entradas e saídas de cada passo, faça duplo clique uma das instâncias em execução.

![Resultados de histórico de execução, entradas e saídas dos passos](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Passos seguintes

* [Criar a sua primeira aplicação lógica](logic-apps-create-a-logic-app.md)
* [Conceber, criar e implementar as logic apps no Visual Studio](logic-apps-deploy-from-vs.md)
* [Ver exemplos e cenários comuns](logic-apps-examples-and-scenarios.md)
* [Vídeo: Automatizar os processos de negócios com Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Vídeo: Integrar os seus sistemas com Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
