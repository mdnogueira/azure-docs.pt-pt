---
title: "Criar uma aplicação sem servidor no Visual Studio | Microsoft Docs"
description: "Introdução à sua primeira aplicação sem servidor com este guia sobre como criar, implementar e gerir a aplicação no Visual Studio."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Criar uma aplicação sem servidor no Visual Studio com Logic Apps e funções

Sem servidor ferramentas e capacidades do Azure permitem um rápido desenvolvimento e implementação das aplicações em nuvem.  Este documento centra-se sobre como começar a utilizar no Visual Studio para criar uma aplicação sem servidor.  Uma descrição geral sem servidor no Azure [podem ser encontrados neste artigo](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>A obter tudo pronto

Seguem-se os pré-requisitos necessários para criar uma aplicação sem servidor a partir do Visual Studio:

* [Visual Studio 2017](https://www.visualstudio.com/vs/) ou Visual Studio 2015 - Comunidade, Professional ou Enterprise
* [Logic Apps Tools para Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [Azure SDK mais recente](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Ferramentas de núcleos de funções do Azure](https://www.npmjs.com/package/azure-functions-core-tools) depurar funciona localmente
* Acesso Web ao utilizar o designer de aplicação lógica incorporado

## <a name="getting-started-with-a-deployment-template"></a>Começar a utilizar um modelo de implementação

Gerir recursos no Azure terminar dentro de um grupo de recursos.  Um grupo de recursos é um agrupamento lógico de recursos.  Grupos de recursos permitem a implementação e gestão de uma coleção de recursos.  Para uma aplicação sem servidor no Azure, o nosso grupo de recursos contém Azure Logic Apps tanto as funções do Azure.  Ao utilizar o projeto do grupo de recursos no Visual Studio, mas é possível desenvolver, gerir e implementar a aplicação completa como um único recurso.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Criar um projeto do grupo de recursos no Visual Studio

1. No Visual Studio, clique para adicionar um **novo projeto**
1. No **nuvem** categoria, selecione para criar um Azure **grupo de recursos** projeto  
 * Se não vir a categoria ou projeto listados, não se esqueça de que ter o Azure SDK instalado para o Visual Studio
1. O projeto de dar um nome e localização e selecione **Ok** criar o Visual Studio solicita para seleccionar um modelo.  Pode selecionar para iniciar a partir de em branco, começar com uma aplicação lógica ou outros recursos.  No entanto, neste caso, utilize um modelo de início rápido do Azure para obter-na começar a utilizar uma aplicação sem servidor.
1. Selecione para mostrar os modelos da **início rápido do Azure** ![modelos de início rápido do Azure de seleção][1]
1. Selecione o modelo de início rápido sem servidor: **101-logic-app-and-function-app** e clique em **Ok**

O modelo de guia de introdução cria um modelo de implementação no seu projeto de grupo de recursos.  O modelo contém uma aplicação lógica simples que chama das funções do Azure e devolve o resultado.  Se abrir o `azuredeploy.json` ficheiro no Explorador de soluções, pode ver os recursos da aplicação sem servidor.

## <a name="deploying-the-serverless-application"></a>Implementar a aplicação sem servidor

Antes de pode abrir o estruturador visual de aplicação lógica no Visual Studio, tem de ser um grupo de recursos do Azure previamente implementado.  Isto permite que o estruturador para criar e utilizar as ligações a serviços e recursos na aplicação lógica.  Para começar, precisamos simplesmente de implementar a solução criada.

1. Clique com o botão direito no projeto no Visual Studio, selecione **implementar**e criar um **novo** implementação ![selecionando a nova implementação de recursos][2]
1. Selecione uma subscrição do Azure válida e o grupo de recursos
1. Selecione **implementar** a solução
1. Introduza o nome para a aplicação lógica e a aplicação de função do Azure.  O nome de função do Azure tem de ser globalmente exclusivo

Implementa a solução sem servidor para o grupo de recursos especificado.  Se observar o **saída** no Visual Studio, pode ver o estado da implementação.

## <a name="editing-the-logic-app-in-visual-studio"></a>Editar a aplicação lógica no Visual Studio

Assim que tiver sido implementada a solução em qualquer grupo de recursos, o estruturador visual pode ser utilizado para editar e fazer alterações para a aplicação lógica.

1. Clique com botão direito a `azuredeploy.json` ficheiro no Explorador de soluções e selecione **aberto com Logic Apps Designer**
1. Selecione o **grupo de recursos** e **localização** a solução foi implementado para e selecione **OK**

O estruturador visual de aplicação lógica agora deve estar visível com o Visual Studio.  Pode continuar a adicionar os passos, modificar o fluxo de trabalho e guardar as alterações.  Também pode criar as logic apps a partir do Visual Studio.  Se, clique com botão direito do **recursos** no navegador modelo, pode optar por adicionar um **aplicação lógica** ao projeto.  Carregar aplicações lógicas vazio no designer visual sem uma pré-implementar num grupo de recursos.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Gerir e ver o histórico de execução de uma aplicação lógica implementado

Também pode gerir e ver o histórico de execução de aplicações lógicas implementado no Azure.  Se abrir o **Cloud Explorer** ferramenta no Visual Studio, qualquer aplicação lógica com o botão direito e selecione editar, desativar, ver as propriedades ou ver o histórico de execução.  Ao clicar em Editar também permite-lhe transferir uma aplicação lógica publicados para um projeto do grupo de recursos do Visual Studio.  Isto significa que mesmo que se começou a criar uma aplicação lógica no portal do Azure, pode ainda importá-los e geri-lo a partir do Visual Studio.

## <a name="developing-an-azure-function-in-visual-studio"></a>Desenvolver uma função do Azure no Visual Studio

O modelo de implementação implementa quaisquer funções do Azure que estão incluídas na solução para o repositório de git especificado no `azuredeploy.json` variáveis.  Se criar um projeto de função na solução de, verifique no controlo de origem (GitHub, Visual Studio Team Services, etc.) e atualize o `repo` variável, o modelo irá implementar a função do Azure.

### <a name="creating-an-azure-function-project"></a>Criar um projeto de função do Azure

Se utilizar o JavaScript, Python, F #, Bash, Batch ou PowerShell, siga o [os passos na CLI de funções](../azure-functions/functions-run-local.md) para criar um projeto.  Se desenvolver uma função em c#, pode utilizar um [biblioteca de classes do c#](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) na solução atual para a função do Azure.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como criar um dashboard de redes social sem servidor](logic-apps-scenario-social-serverless.md)
* [Gerir uma aplicação lógica a partir do Explorador de nuvem do Visual Studio](logic-apps-manage-from-vs.md)
* [Linguagem de definição de fluxo de trabalho de aplicação lógica](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
