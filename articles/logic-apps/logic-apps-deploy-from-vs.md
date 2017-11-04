---
title: "Criar, criar e implementar as logic apps no Visual Studio – Azure Logic Apps | Microsoft Docs"
description: Crie projetos do Visual Studio para que possa conceber, criar e implementar o Azure Logic Apps.
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: e7f5cf483d22e4c60dedbe5176ceb0bc8b2b6e66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Conceber, criar e implementar o Azure Logic Apps no Visual Studio

Embora o [portal do Azure](https://portal.azure.com/) oferece uma excelente forma de criar e gerir o Azure Logic Apps, pode utilizar o Visual Studio para conceber, criar e implementar as logic apps. O Visual Studio fornece ferramentas avançadas, como o Designer de aplicação lógica para criar as logic apps, a configurar modelos de implementação e a automatização e a implementar em qualquer ambiente. 

Para começar com Azure Logic Apps, saiba [como criar a sua primeira aplicação de lógica no portal do Azure](logic-apps-create-a-logic-app.md).

## <a name="installation-steps"></a>Passos de instalação

Para instalar e configurar ferramentas do Visual Studio para o Azure Logic Apps, siga estes passos.

### <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) ou Visual Studio 2015
* [Azure SDK mais recente](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Acesso Web ao utilizar o estruturador incorporado

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Instalar as ferramentas do Visual Studio para o Azure Logic Apps

Depois de instalar os pré-requisitos:

1. Abra o Visual Studio. No **ferramentas** menu, selecione **extensões e atualizações**.
2. Expanda o **Online** categoria, pelo que pode procurar online.
3. Navegue ou procure **Logic Apps** até encontrar **Azure Logic Apps Tools para Visual Studio**.
4. Para transferir e instalar a extensão, clique em **transferir**.
5. Reinicie o Visual Studio após a instalação.

> [!NOTE]
> Também pode transferir [ferramentas do Azure Logic Apps para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) e [ferramentas do Azure Logic Apps para o Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio) diretamente a partir do Visual Studio Marketplace.

Depois de concluir a instalação, pode utilizar o projeto do grupo de recursos do Azure com o Designer de aplicação lógica.

## <a name="create-your-project"></a>Criar o projeto

1. No **ficheiro** menu, aceda a **novo**e selecione **projeto**. Ou ao adicionar o seu projeto para uma solução existente, aceda a **adicionar**e selecione **novo projeto**.

    ![Menu Ficheiro](./media/logic-apps-deploy-from-vs/filemenu.png)

2. No **novo projeto** janela, localizar **nuvem**e selecione **grupo de recursos do Azure**. Nomeie o projeto e clique em **OK**.

    ![Adicionar novo projeto](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Selecione o **aplicação lógica** modelo, que cria um modelo de implementação de aplicação lógica em branco para utilizar. Depois de selecionar o modelo, clique em **OK**.

    ![Selecione o modelo de aplicação lógica](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Agora, adicionou o projeto de aplicação lógica à sua solução. 
    No Explorador de soluções, deve aparecer o ficheiro de implementação.

    ![Ficheiro de implementação](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>Criar a sua aplicação lógica com o Designer de aplicação lógica

Quando tiver um projeto do grupo de recursos do Azure que contém uma aplicação lógica, pode abrir o Designer de aplicação lógica no Visual Studio para criar o fluxo de trabalho. 

> [!NOTE]
> O estruturador requer uma ligação à internet para os conectores de consulta para dados e propriedades disponíveis. Por exemplo, se utilizar o conector do Dynamics CRM Online, o estruturador de consulta a instância do CRM para mostrar personalizada disponível e as propriedades predefinidas.

1. Com o botão direito do `<template>.json` do ficheiro e selecione **aberta com o Designer de aplicação lógica**. (`Ctrl+L`)

2. Escolha a sua subscrição do Azure, o grupo de recursos e a localização para o modelo de implementação.

    > [!NOTE]
    > Conceber uma aplicação lógica cria recursos de ligação da API que consulta as propriedades de durante a conceção. Visual Studio utiliza o grupo de recursos selecionado para criar essas ligações durante a concepção. Para ver ou alterar quaisquer ligações de API, aceda ao portal do Azure e procure **API ligações**.

    ![Seleccionador de subscrição](./media/logic-apps-deploy-from-vs/designer_picker.png)

    O estruturador utiliza a definição no `<template>.json` ficheiro para composição.

4. Criar e estruturar a sua aplicação lógica. O modelo de implementação é atualizado com as suas alterações.

    ![Designer de aplicação lógica no Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Adiciona o Visual Studio `Microsoft.Web/connections` recursos para o ficheiro de recursos para todas as ligações a aplicação lógica tem de funcionar. Estas propriedades de ligação podem ser definidas quando implementar e geridas depois de implementar no **API ligações** no portal do Azure.

### <a name="switch-to-json-code-view"></a>Mudar para vista de código JSON

Para mostrar a representação JSON para a sua aplicação lógica, selecione o **vista de código** separador na parte inferior do designer.

Para mudar para o recurso completo JSON, clique com botão direito do `<template>.json` do ficheiro e selecione **abra**.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Adicione referências para os recursos dependentes para modelos de implementação do Visual Studio

Quando pretender que a sua aplicação lógica para referenciar recursos dependentes, pode utilizar [funções de modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) no seu modelo de implementação de aplicação lógica. Por exemplo, poderá pretender que a aplicação lógica para fazer referência a uma conta de função do Azure ou de integração que pretende implementar juntamente com a sua aplicação lógica. Siga estas diretrizes sobre como utilizar parâmetros no seu modelo de implementação para que o Designer de aplicação lógica compõe corretamente. 

Pode utilizar os parâmetros de aplicação de lógica nestes tipos de acionadores e ações:

*   Fluxo de trabalho subordinado
*   Aplicação de função
*   Chamada APIM
*   URL de tempo de execução de ligação de API
*   Caminho de ligação de API

E pode utilizar funções de modelo como parâmetros, variáveis, resourceId, concat, etc. Por exemplo, eis como pode substituir o ID de recurso da função do Azure:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

E, em que pretende utilizar parâmetros:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Outro exemplo pode parametrizar a operação de mensagem de envio do Service Bus:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl é opcional e pode ser removido do seu modelo, se estiver presente.
> 


> [!NOTE] 
> Para o Designer de aplicação lógica funcionar ao utilizar parâmetros, tem de fornecer valores predefinidos, por exemplo:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>Guardar a aplicação lógica

Para guardar a sua aplicação lógica em qualquer altura, aceda a **ficheiro** > **guardar**. (`Ctrl+S`) 

Se a sua aplicação lógica tiver quaisquer erros quando guardar a sua aplicação, aparecem no Visual Studio **saídas** janela.

## <a name="deploy-your-logic-app-from-visual-studio"></a>Implementar a sua aplicação lógica a partir do Visual Studio

Depois de configurar a sua aplicação, pode implementar diretamente a partir do Visual Studio em apenas alguns passos. 

1. No Explorador de soluções, clique no seu projeto e aceda a **implementar** > **nova implementação...**

    ![Nova implementação](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. Quando lhe for solicitado, inicie sessão na sua subscrição do Azure. 

3. Agora tem de selecionar os detalhes para o grupo de recursos onde pretende implementar a sua aplicação lógica. Quando estiver pronto, selecione **implementar**.

    > [!NOTE]
    > Certifique-se de que seleciona o ficheiro de modelo e os parâmetros correto para o grupo de recursos. Por exemplo, se pretender implementar um ambiente de produção, escolha o ficheiro de parâmetros de produção.

    ![Implementar o grupo de recursos](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    O estado de implementação aparece no **saída** janela. 
    Poderá ter de selecionar **Azure aprovisionamento** no **mostram o resultado da** lista.

    ![Resultado do Estado de implementação](./media/logic-apps-deploy-from-vs/output.png)

No futuro, pode editar a sua aplicação lógica no controlo de origem e utilizar o Visual Studio para implementar novas versões.

> [!NOTE]
> Se alterar a definição no portal do Azure diretamente, essas alterações são substituídas quando implementar a partir do Visual Studio próxima vez. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Adicionar a sua aplicação lógica para um projeto do grupo de recursos existente

Se tiver um projeto do grupo de recursos existente, pode adicionar a sua aplicação lógica para esse projeto na janela contorno de JSON. Também pode adicionar outra aplicação de lógica juntamente com a aplicação que criou anteriormente.

1. Abra o ficheiro `<template>.json`.

2. Para abrir a janela contorno de JSON, aceda a **vista** > **outras janelas** > **contorno de JSON**.

3. Para adicionar um recurso para o ficheiro de modelo, clique em **adicionar recursos** na parte superior da janela contorno de JSON. Ou na janela contorno de JSON, clique com botão direito **recursos**e selecione **adicionar novo recurso**.

    ![Janela contorno de JSON](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. No **adicionar recursos** caixa de diálogo, localize e selecione **aplicação lógica**. Nome da sua aplicação lógica e escolha **adicionar**.

    ![Adicionar recurso](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Passos Seguintes

* [Gerir as logic apps com o Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Ver exemplos e cenários comuns](logic-apps-examples-and-scenarios.md)
* [Saiba como automatizar os processos de negócios com Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Saiba como integrar os seus sistemas com Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
