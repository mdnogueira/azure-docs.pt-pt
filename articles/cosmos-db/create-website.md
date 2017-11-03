---
title: "Implementar uma aplicação web com um modelo - base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como implementar uma conta de base de dados do Azure Cosmos, Web Apps do Azure App Service e um exemplo de aplicação web utilizando um modelo Azure Resource Manager."
services: cosmos-db, app-service\web
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 087d8786-1155-42c7-924b-0eaba5a8b3e0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: 67c11277142d6616fe77179e03b55ed08993b818
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Implementar a BD do Cosmos do Azure e Azure Web Apps do App Service utilizando um modelo do Azure Resource Manager
Este tutorial mostra como utilizar um modelo Azure Resource Manager para implementar e integrar [base de dados do Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/), um [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) aplicação web e um exemplo de aplicação web.

Utilizar modelos Azure Resource Manager, pode facilmente automatizar a implementação e a configuração dos seus recursos Azure.  Este tutorial mostra como implementar uma aplicação web e configurar automaticamente as informações de ligação da conta de base de dados do Azure Cosmos.

Depois de concluir este tutorial, poderá responder às seguintes questões:  

* Como utilizar um modelo Azure Resource Manager para implementar e a integrar uma conta de base de dados do Azure Cosmos e uma aplicação web no App Service do Azure?
* Como utilizar um modelo Azure Resource Manager para implementar e integrar a uma conta de base de dados do Azure Cosmos, uma aplicação web nas Web Apps do App Service e uma aplicação Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos
> [!TIP]
> Enquanto este tutorial assume experiência anterior com modelos Azure Resource Manager ou JSON, deve que pretende modificar os modelos de referenciado ou as opções de implementação, em seguida, dados de conhecimento de cada uma destas áreas será necessário.
> 
> 

Antes de seguir as instruções neste tutorial, certifique-se de que tem o seguinte:

* Uma subscrição do Azure. Azure é uma plataforma baseado na subscrição.  Para obter mais informações sobre a obtenção de uma subscrição, consulte [opções de compra](https://azure.microsoft.com/pricing/purchase-options/), [ofertas de membros](https://azure.microsoft.com/pricing/member-offers/), ou [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Passo 1: Transferir os ficheiros de modelo
Vamos começar por transferir os ficheiros de modelo que iremos utilizar neste tutorial.

1. Transferir o [criar uma conta de base de dados do Azure Cosmos, Web Apps e implementar um exemplo de aplicação de demonstração](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) modelo para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo irá implementar uma conta de base de dados do Azure Cosmos, um aplicação web do app Service e uma aplicação web.  Esta será também automaticamente configurar a aplicação web para ligar à conta de base de dados do Azure Cosmos.
2. Transferir o [criar uma conta de base de dados do Azure Cosmos e exemplos de aplicações Web](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) modelo para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo irá implementar uma conta de base de dados do Azure Cosmos, uma aplicação web do app Service e irá modificar definições da aplicação do site para facilmente descobrir informações de ligação de base de dados do Azure Cosmos, mas não inclui uma aplicação web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Passo 2: Implementar a conta de base de dados do Azure Cosmos, o serviço de aplicações web app e demonstração aplicação exemplo
Agora vamos implementar nosso primeiro modelo.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome de conta de base de dados do Azure Cosmos introduzida abaixo estão a) válido e b) disponíveis.  Recomenda-se vivamente que verifique a disponibilidade dos nomes de que pretende fornecer antes de submeter a implementação.
> 
> 

1. Início de sessão para o [Portal do Azure](https://portal.azure.com), clique em novo e procure "Implementação do modelo".
    ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment1.png)
2. Selecione o item de modelo de implementação e clique em **criar** ![captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment2.png)
3. Clique em **Editar modelo**, cole o conteúdo do ficheiro de modelo DocDBWebsiteTodo.json e, em **guardar**.
   ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment3.png)
4. Clique em **Editar parâmetros**, fornecer valores para cada um dos parâmetros obrigatórios e, em **OK**.  Os parâmetros são os seguintes:
   
   1. SITENAME: Especifica o nome da aplicação do serviço de aplicações web e é utilizado para construir o URL que irá utilizar para aceder à aplicação web (por exemplo, se especificar "mydemodocdbwebapp", em seguida, o URL através do qual será a aceder à aplicação web será mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Especifica o nome do plano de alojamento do serviço de aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização do Azure na qual pretende criar a base de dados do Azure Cosmos e web recursos de aplicação.
   4. DATABASEACCOUNTNAME: Especifica o nome da conta de base de dados do Azure Cosmos criar.   
      
      ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de recursos existente ou forneça um nome para tornar um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment5.png)
6. Clique em **consultar termos legais**, **Compra**e, em seguida, clique em **criar** para iniciar a implementação.  Selecione **afixar ao dashboard** para a implementação resultante é facilmente visível na sua home page do portal do Azure.
   ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment6.png)
7. Quando a conclusão da implementação, irá abrir o painel do grupo de recursos.
   ![Captura de ecrã do painel do grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Para utilizar a aplicação, basta navegar para o URL da aplicação web (no exemplo acima, o URL seria http://mydemodocdbwebapp.azurewebsites.net).  Verá a seguinte aplicação web:
   
   ![Exemplo de aplicação Todo](./media/create-website/image2.png)
9. Avançar e criar algumas das tarefas na aplicação web e, em seguida, regressar ao painel do grupo de recursos no portal do Azure. Clique no recurso de conta de base de dados do Azure Cosmos na lista de recursos e, em seguida, clique em **Explorador de consulta**.
    ![Captura de ecrã do resumo lente com a aplicação web realçada](./media/create-website/TemplateDeployment8.png)  
10. Execute a consulta predefinida, "SELECIONAR * do c" e Inspecione os resultados.  Tenha em atenção que a consulta tem de obter a representação JSON de itens todo que criou no passo 7 acima.  À vontade experimentar consultas; Por exemplo, tentar executar SELECIONAR * de c WHERE c.isComplete = true para devolver todos os itens todo que foram marcados como concluídos.
    
    ![Captura de ecrã dos painéis de Explorador de consulta e os resultados que mostra os resultados da consulta](./media/create-website/image5.png)
11. Pode explorar a experiência do portal do Azure Cosmos DB ou modificar o exemplo de aplicação de Todo.  Quando estiver pronto, vamos implementar outro modelo.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Passo 3: Implementar o exemplo de aplicação de conta e web documento
Agora vamos implementar o nosso modelo segundo.  Este modelo é útil para mostrar a forma como pode inserir informações de ligação de base de dados do Azure Cosmos como ponto final de conta e chave mestra numa aplicação web como definições da aplicação ou como uma cadeia de ligação personalizada. Por exemplo, talvez ter sua própria aplicação web que gostaria de implementar com uma conta de base de dados do Azure Cosmos e tem as informações de ligação preenchidas automaticamente durante a implementação.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome de conta de base de dados do Azure Cosmos introduzida abaixo estão a) válido e b) disponíveis.  Recomenda-se vivamente que verifique a disponibilidade dos nomes de que pretende fornecer antes de submeter a implementação.
> 
> 

1. No [Portal do Azure](https://portal.azure.com), clique em novo e procure "Implementação do modelo".
    ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment1.png)
2. Selecione o item de modelo de implementação e clique em **criar** ![captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment2.png)
3. Clique em **Editar modelo**, cole o conteúdo do ficheiro de modelo DocDBWebSite.json e, em **guardar**.
   ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment3.png)
4. Clique em **Editar parâmetros**, fornecer valores para cada um dos parâmetros obrigatórios e, em **OK**.  Os parâmetros são os seguintes:
   
   1. SITENAME: Especifica o nome da aplicação do serviço de aplicações web e é utilizado para construir o URL que irá utilizar para aceder à aplicação web (por exemplo, se especificar "mydemodocdbwebapp", em seguida, o URL através do qual será a aceder à aplicação web será mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Especifica o nome do plano de alojamento do serviço de aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização do Azure na qual pretende criar a base de dados do Azure Cosmos e web recursos de aplicação.
   4. DATABASEACCOUNTNAME: Especifica o nome da conta de base de dados do Azure Cosmos criar.   
      
      ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de recursos existente ou forneça um nome para tornar um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment5.png)
6. Clique em **consultar termos legais**, **Compra**e, em seguida, clique em **criar** para iniciar a implementação.  Selecione **afixar ao dashboard** para a implementação resultante é facilmente visível na sua home page do portal do Azure.
   ![Captura de ecrã da implementação do modelo da IU](./media/create-website/TemplateDeployment6.png)
7. Quando a conclusão da implementação, irá abrir o painel do grupo de recursos.
   ![Captura de ecrã do painel do grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Clique no recurso de aplicação Web na lista de recursos e, em seguida, clique em **definições da aplicação** ![captura de ecrã do grupo de recursos](./media/create-website/TemplateDeployment9.png)  
9. Tenha em atenção a como se existem definições de aplicação para o ponto final de base de dados do Azure Cosmos e cada uma das chaves do Azure Cosmos DB mestre.

    ![Captura de ecrã das definições de aplicação](./media/create-website/TemplateDeployment10.png)  
10. À vontade continuar a explorar o Portal do Azure ou, siga um dos nossos BD do Cosmos Azure [amostras](http://go.microsoft.com/fwlink/?LinkID=402386) para criar a sua própria aplicação Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Passos seguintes
Parabéns! Transferiu o Azure Cosmos DB, aplicação web do app Service e um exemplo de aplicação web utilizando os modelos Azure Resource Manager.

* Para saber mais sobre a base de dados do Azure Cosmos, clique em [aqui](http://azure.com/docdb).
* Para saber mais sobre as aplicações Web de serviço de aplicações do Azure, clique em [aqui](http://go.microsoft.com/fwlink/?LinkId=325362).
* Para saber mais sobre modelos Azure Resource Manager, clique em [aqui](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte: [App Service do Azure e o Respetivo Impacto nos Serviços do Azure Existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

