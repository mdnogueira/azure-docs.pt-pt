---
title: "Criar modelos de implementação para o Azure Logic Apps | Microsoft Docs"
description: "Criar modelos do Azure Resource Manager para aplicações lógicas gestão de implementação e a versão do"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9cfbb294010d48deaf4b4c78c6a6bcd59a387d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-templates-for-logic-apps-deployment-and-release-management"></a>Criar modelos para as logic apps gestão de implementação e a versão do

Depois de criar uma aplicação lógica, pode querer criá-la como um modelo Azure Resource Manager.
Desta forma, facilmente pode implementar a aplicação lógica para qualquer ambiente ou grupo de recursos onde poderá ser necessário.
Para mais informações sobre modelos do Resource Manager, consulte [criação de modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [implementar recursos através de modelos Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modelo de implementação de aplicação lógica

Uma aplicação lógica tem três componentes básicos:

* **Recurso de aplicação lógica**: contém informações sobre coisas como preços plano, localização e a definição de fluxo de trabalho.
* **Definição de fluxo de trabalho**: descreve os passos de fluxo de trabalho da sua aplicação lógica e a forma como o motor de Logic Apps deve executar o fluxo de trabalho.
Pode ver esta definição na sua aplicação lógica **vista de código** janela.
No recurso de aplicação lógica, pode encontrar esta definição no `definition` propriedade.
* **Ligações**: refere-se para separar recursos em segurança armazenam metadados sobre todas as ligações de conector, tais como uma cadeia de ligação e um token de acesso.
No recurso de aplicação lógica, a sua aplicação lógica referencia destes recursos no `parameters` secção.

Pode ver todas as estas duas logic apps existentes utilizando uma ferramenta como o [Explorador de recursos do Azure](http://resources.azure.com).

Para tornar um modelo para uma aplicação lógica para utilizar com implementações do grupo de recursos, tem de definir os recursos e parametrizar conforme necessário.
Por exemplo, se estiver a implementar para desenvolvimento, teste e ambiente de produção, provavelmente pretende utilizar cadeias de ligação diferente para uma base de dados do SQL em cada ambiente.
Em alternativa, pode querer implementar em diferentes subscrições ou grupos de recursos.  

## <a name="create-a-logic-app-deployment-template"></a>Criar um modelo de implementação de aplicação lógica

A forma mais fácil para ter um modelo de implementação de aplicação lógica válido consiste em utilizar o [Visual Studio Tools para aplicações lógicas](logic-apps-deploy-from-vs.md).
As ferramentas do Visual Studio geram um modelo de implementação válida que possa ser utilizado em qualquer subscrição ou localização.

Algumas outras ferramentas podem ajudá-lo como criar um modelo de implementação de aplicação lógica.
Pode criar manualmente, ou seja, através da utilização de recursos já abordados aqui para criar os parâmetros conforme necessário.
Outra opção consiste em utilizar um [criador de modelo de aplicação lógica](https://github.com/jeffhollan/LogicAppTemplateCreator) módulo do PowerShell. Este módulo de open source avalia primeiro a aplicação lógica e quaisquer ligações que está a utilizar e, em seguida, gera recursos de modelo com os parâmetros necessários para a implementação.
Por exemplo, se tiver uma aplicação lógica que recebe uma mensagem a partir de uma fila do Service Bus do Azure e adiciona dados para uma base de dados SQL do Azure, a ferramenta preserva toda a lógica de orquestração e parameterizes a ligação de SQL e barramento de serviço cadeias para que podem ser definidos ao implementar mentar.

> [!NOTE]
> Ligações tem de estar no mesmo grupo de recursos que a aplicação lógica.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Instalar o módulo de PowerShell do modelo de aplicação lógica
A forma mais fácil para instalar o módulo é através de [galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), utilizando o comando `Install-Module -Name LogicAppTemplate`.  

Também pode instalar o módulo do PowerShell manualmente:

1. Transferir a versão mais recente do [criador de modelo de aplicação lógica](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Extrair a pasta na sua pasta de módulo do PowerShell (normalmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para o módulo trabalhar com qualquer acesso inquilino e subscrição do token, recomendamos que utilize-o com o [ARMClient](https://github.com/projectkudu/ARMClient) ferramenta da linha de comandos.  Isto [blogue](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) aborda ARMClient mais detalhadamente.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Gerar um modelo de aplicação lógica com o PowerShell
Depois do PowerShell está instalado, pode gerar um modelo utilizando o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`é o ID de subscrição do Azure. Esta linha primeiro obtém acesso token através de ARMClient, em seguida, encaminha-lo ao script do PowerShell e, em seguida, cria o modelo de um ficheiro JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Adicionar parâmetros a um modelo de aplicação lógica
Depois de criar o modelo de aplicação lógica, pode continuar a adicionar ou modificar os parâmetros que poderá ser necessário. Por exemplo, se a definição incluir um ID de recurso a uma função do Azure ou o fluxo de trabalho aninhado que planeia implementar numa única implementação, pode adicionar mais recursos ao seu modelo e parametrizar IDs conforme necessário. O mesmo se aplica a quaisquer referências a personalizado APIs ou Swagger pontos finais que pretende implementar com cada grupo de recursos.

## <a name="deploy-a-logic-app-template"></a>Implementar um modelo de aplicação lógica

Pode implementar o modelo utilizando ferramentas como o PowerShell, REST API, [Visual Studio Team Services versão gestão](#team-services)e a implementação do modelo através do portal do Azure.
Além disso, para armazenar os valores de parâmetros, recomendamos que crie um [ficheiro de parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Saiba como [implementar recursos com modelos Azure Resource Manager e o PowerShell](../azure-resource-manager/resource-group-template-deploy.md) ou [implementar recursos com modelos Azure Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autorizar ligações do OAuth

Após a implementação, a aplicação lógica funciona ponto a ponto com parâmetros válidos.
No entanto, tem de autorizar ainda OAuth ligações para gerar um token de acesso válido.
Para autorizar ligações de OAuth, abra a aplicação lógica no Designer de aplicações lógicas e autorizar estas ligações. Ou, para a implementação automática, pode utilizar um script para o consentimento para cada ligação de OAuth.
Há um script de exemplo no GitHub sob o [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projeto.

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Gestão de versões do Visual Studio Team Services

Um cenário comum para implementar e gerir um ambiente é utilizar uma ferramenta como a gestão de versões no Visual Studio Team Services, com um modelo de implementação de aplicação lógica. Visual Studio Team Services inclui um [implementar o grupo de recursos do Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) tarefas que pode adicionar a qualquer compilação ou libertar o pipeline. Tem de ter um [principal de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para autorização para implementar e, em seguida, pode gerar a definição de versão.

1. Na gestão de versões, selecione **vazio** para que criar uma definição vazia.

    ![Criar definição vazia][1]

2. Escolha quaisquer recursos de que precisa para tal, provavelmente, incluindo o modelo de aplicação lógica que é gerado manualmente ou como parte do processo de compilação.
3. Adicionar um **implementação do grupo de recursos do Azure** tarefas.
4. Configurar com um [principal de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)e os ficheiros de modelo e os parâmetros do modelo de referência.
5. Continue a criar os passos no processo de lançamento para outro ambiente, teste automatizada ou aprovadores conforme necessário.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
