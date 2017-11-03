---
title: Criar conectores personalizados a partir de APIs da Web - Azure Logic Apps | Microsoft Docs
description: Criar conectores personalizados a partir de APIs da Web
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Criar conectores personalizados a partir de APIs da Web

Para criar um conetor personalizado que pode utilizar no Azure Logic Apps, Microsoft Flow ou Microsoft PowerApps, criar uma API Web que podem alojar Web Apps do Azure, autenticar com o Azure Active Directory e registar como um conector com Logic Apps, fluxo, ou PowerApps. Este tutorial mostra como realizar estas tarefas ao criar uma aplicação de API Web do ASP.NET. Para os padrões que mostram pode estruturar o código para o conector acionadores e ações, consulte [criar APIs personalizadas que pode chamar a partir de fluxos de trabalho de aplicação de lógica](../logic-apps/logic-apps-create-api-app.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2013 ou posterior](https://www.visualstudio.com/vs/). Este tutorial utiliza o Visual Studio 2015.

* Código para a API Web. Se não tiver nenhum, tente neste tutorial: [introdução ao ASP.NET Web API 2 (c#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

* Uma subscrição do Azure. Se não tiver uma subscrição, pode começar com uma [conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, inscreva-se um [subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Criar e implementar uma aplicação web ASP.NET para o Azure

Para este tutorial, crie uma aplicação web do Visual c# ASP.NET. 

1. Abra o Visual Studio, em seguida, escolha **ficheiro** > **novo projeto**.

   1. Expanda **instalada**, aceda a **modelos** > **Visual c#** > **Web**e selecione **ASP Aplicação web .NET**.

   2. Forneça um nome de projeto, a localização e o nome da solução para a sua aplicação, em seguida, escolha **OK**.

   Por exemplo:

   ![Criar uma aplicação web do Visual c# ASP.NET](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. No **nova aplicação Web do ASP.NET** caixa, selecione o **Web API** modelo. Se ainda não estiver selecionada, selecione **anfitrião na nuvem**. Escolha **alterar autenticação**.

   ![Selecionar modelo de "Web API", "Anfitrião na nuvem", "Alterar autenticação"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. Selecione **sem autenticação**e escolha **OK**. Pode configurar a autenticação mais tarde.

   ![Selecionar "Sem autenticação"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. Quando o **nova aplicação Web do ASP.NET** caixa reappears, escolha **OK**. 

5. No **criar App Service** caixa, reveja as definições de alojamento descritas na tabela, efetue as alterações que pretende e escolha **criar**. 

   Um [plano do App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) representa uma coleção de recursos físicos utilizados para alojar aplicações na sua subscrição do Azure. Saiba mais sobre [do serviço de aplicações](../app-service/app-service-value-prop-what-is.md).

   ![Criar o serviço de aplicações](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | O Azure ou conta profissional ou a conta Microsoft pessoal | *conta de utilizador do* | Selecione a sua conta de utilizador. | 
   | **Nome da aplicação Web** | *personalizada-web---nome da aplicação api* ou o nome predefinido | Introduza o nome para a sua aplicação Web API, que é utilizado no URL da sua aplicação, por exemplo: http://*web---nome da aplicação api*. | 
   | **Subscrição** | *Nome da subscrição do Azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de Recursos** | *Azure-resource--nome do grupo* | Selecione um grupo de recursos do Azure existente ou, se ainda não o fez, crie um grupo de recursos. <p>**Tenha em atenção**: grupo de recursos do Azure um organiza recursos do Azure na sua subscrição do Azure. | 
   | **Plano do App Service** | *Aplicações--plano-nome do serviço* | Selecione um plano de serviço aplicacional existente ou, se ainda não o fez, crie um plano. | 
   |||| 

   Se criar um plano do App Service, especificar estas definições:

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Localização** | *implementação-região* | Selecione a região para implementar a sua aplicação. | 
   | **Tamanho** | *Plano-tamanho de App-Service* | Selecione o tamanho do seu plano, que determina o custo e a capacidade de recursos informáticos para o seu plano de serviço. | 
   |||| 

   Para configurar quaisquer outros recursos de que a sua aplicação, escolha **explorar serviços Azure adicionais**.

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Tipo de Recurso** | *Tipo de recurso do Azure* | Selecione e configure quaisquer recursos adicionais necessários pela sua aplicação. | 
   |||| 

6. Depois do Visual Studio implementa o projeto, crie o código para a sua aplicação.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Criar um ficheiro de OpenAPI (Swagger) que descrevem a API Web

Para ligar a aplicação de Web API ao Logic Apps, é necessário um [OpenAPI (anteriormente Swagger) ficheiro](http://swagger.io/) que descreve operações da API. Pode escrever a sua própria definição OpenAPI para a sua API com o [Swagger online editor](http://editor.swagger.io/), mas este tutorial utiliza uma ferramenta de código aberto com o nome [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md).

1. Se ainda não o fez, instale o pacote Swashbuckle Nuget no seu projeto de Visual Studio.

   1. No Visual Studio, selecione **ferramentas** > **Gestor de pacotes NuGet** > 
    **consola do Gestor de pacotes**.

   2. No **consola do Gestor de pacotes**, aceda ao diretório de projeto da aplicação se não ainda existe (executar `Set-Location "project-path"`), e execute este commandlet de PowerShell: 
   
      `Install-Package Swashbuckle`

      Por exemplo:

      ![Consola do Gestor de pacotes, instale o Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Se executar a aplicação depois de instalar o Swashbuckle, o Swashbuckle gera um ficheiro de OpenAPI neste URL: 
   >
   > http://*{your-web-api-app-root-URL}*/swagger/docs/v1
   > 
   > Swashbuckle também gera uma interface de utilizador neste URL: 
   > 
   > http://*{your-web-api-app-root-URL}*  /swagger

3. Quando estiver pronto, publique a aplicação de Web API do Azure. Para publicar a partir do Visual Studio, clique no seu projeto web no Explorador de soluções, escolha **publicar...** e siga as instruções.

   > [!IMPORTANT]
   > IDs de operação duplicado tornar um documento de OpenAPI inválido. Se utilizou o c# modelo de exemplo, o modelo *repete este ID de operação duas vezes*:`Values_Get` 
   > 
   > Para corrigir este problema, altere uma instância para `Value_Get` e voltar a publicar.

4. Obter o documento OpenAPI ao navegar para esta localização: 

   http://*{your-web-api-app-root-URL}*/swagger/docs/v1

   Também pode transferir um [documento do exemplo OpenAPI](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json) deste tutorial. 
   Certifique-se de que remove os comentários, que começados por "/ /", antes de utilizar o documento.

5. Guarde o conteúdo como um ficheiro JSON. Com base no seu browser, poderá ter de copiar e colar o texto no ficheiro de texto vazio.

## <a name="next-steps"></a>Passos seguintes

* [Configurar a autenticação para os conectores personalizados](../logic-apps/custom-connector-azure-active-directory-authentication.md)











