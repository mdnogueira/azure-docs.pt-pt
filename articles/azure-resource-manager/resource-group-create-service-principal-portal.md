---
title: "Criar a identidade da aplicação do Azure no portal | Microsoft Docs"
description: "Descreve como criar uma nova aplicação do Azure Active Directory e um principal de serviço que pode ser utilizada com o controlo de acesso baseado em funções no Gestor de recursos do Azure para gerir o acesso aos recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 3b9c49d4c7d49cc6795fb093f9abc748d55b5b6f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Utilize o portal para criar uma aplicação Azure Active Directory e um principal de serviço que pode aceder a recursos

Quando tiver uma aplicação que necessita para aceder ou modificar recursos, tem de configurar uma aplicação do Azure Active Directory (AD) e atribuir as permissões necessárias para a mesma. Esta abordagem é preferível executar a aplicação com as suas próprias credenciais porque:

* Pode atribuir permissões para a identidade de aplicação que sejam diferentes a suas própria permissões. Normalmente, estas permissões são restritos exatamente o que a aplicação tem de fazer.
* Não é necessário que alterar credenciais da aplicação, se alterar as suas responsabilidades. 
* Pode utilizar um certificado para automatizar a autenticação ao executar um script automático.

Este tópico mostra como efetuar os passos através do portal. Concentra-se uma aplicação do inquilino único onde a aplicação foi concebida para ser executada dentro da organização apenas um. Normalmente utiliza aplicações de inquilino único para aplicações de linha de negócio que são executadas dentro da sua organização.

## <a name="required-permissions"></a>Permissões necessárias

Para concluir este tópico, tem de ter permissões suficientes para registar uma aplicação com o seu inquilino do Azure AD e atribuir a aplicação a uma função na sua subscrição do Azure. Vamos certificar-se de que tem as permissões corretas para efetuar esses passos.

### <a name="check-azure-active-directory-permissions"></a>Verifique as permissões do Azure Active Directory

1. Inicie sessão na sua conta do Azure através de [portal do Azure](https://portal.azure.com).

1. Selecione **do Azure Active Directory**.

   ![Selecione do azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. No Azure Active Directory, selecione **as definições de utilizador**.

   ![Selecione as definições de utilizador](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Verifique o **registos de aplicação** definição. Se definido como **Sim**, os utilizadores que não podem registar aplicações AD. Esta definição significa que qualquer utilizador no inquilino do Azure AD pode registar uma aplicação. Para poder continuar para [permissões de subscrição do Azure verifique](#check-azure-subscription-permissions).

   ![registos de aplicação de vista](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Se os registos de aplicação definição estiver definida como **não**, apenas os utilizadores administradores podem registar aplicações. Verifique se a sua conta é um administrador de inquilino do Azure AD. Selecione **descrição geral** e **localizar um utilizador** de tarefas rápidas.

   ![Localizar utilizador](./media/resource-group-create-service-principal-portal/find-user.png)

1. Pesquisa para a sua conta e selecioná-lo quando a encontrá-lo.

   ![utilizador de pesquisa](./media/resource-group-create-service-principal-portal/show-user.png)

1. Para a sua conta, selecione **função de diretório**.

   ![função de diretório](./media/resource-group-create-service-principal-portal/select-directory-role.png)

1. Ver a sua função de diretório atribuído no Azure AD. Se a sua conta está atribuída à função de utilizador, mas a definição de registo de aplicação (a partir dos passos anteriores) está limitada a utilizadores de administração, peça ao seu administrador para qualquer lhe atribuir a uma função de administrador ou permitir que os utilizadores registar as aplicações.

   ![função de vista](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Verifique as permissões de subscrição do Azure

Na sua subscrição do Azure, a conta tem de ter `Microsoft.Authorization/*/Write` acesso para atribuir uma aplicação AD a uma função. Esta ação é concedida através de [proprietário](../active-directory/role-based-access-built-in-roles.md#owner) função ou [administrador de acesso de utilizador](../active-directory/role-based-access-built-in-roles.md#user-access-administrator) função. Se a sua conta está atribuída a **contribuinte** função, não tem permissão suficiente. Recebe um erro ao tentar atribuir o principal de serviço a uma função.

Para verificar as permissões de subscrição:

1. Se não já pretender na sua conta do Azure AD dos passos anteriores, selecione **do Azure Active Directory** no painel esquerdo.

1. Localize a conta do Azure AD. Selecione **descrição geral** e **localizar um utilizador** de tarefas rápidas.

   ![Localizar utilizador](./media/resource-group-create-service-principal-portal/find-user.png)

1. Pesquisa para a sua conta e selecioná-lo quando a encontrá-lo.

   ![utilizador de pesquisa](./media/resource-group-create-service-principal-portal/show-user.png)

1. Selecione **recursos do Azure**.

   ![Selecione recursos](./media/resource-group-create-service-principal-portal/select-azure-resources.png)

1. Ver as funções atribuídas e determinar se tem permissões adequadas para atribuir uma aplicação AD a uma função. Caso contrário, peça ao seu administrador de subscrição para adicioná-lo à função de administrador de acesso de utilizador. Na imagem seguinte, o utilizador está atribuído à função de proprietário para duas subscrições, que significa que o utilizador tem permissões adequadas.

   ![Mostrar permissões](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

1. Inicie sessão na sua conta do Azure através de [portal do Azure](https://portal.azure.com).
1. Selecione **do Azure Active Directory**.

   ![Selecione do azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Selecione **registos de aplicação**.

   ![Selecione os registos de aplicação](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Selecione **novo registo de aplicação**.

   ![Adicionar aplicação](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Forneça um nome e o URL para a aplicação. Selecione **aplicação Web / API** ou **nativo** para o tipo de aplicação que pretende criar. Depois de definir os valores, selecione **criar**.

   ![aplicação de nome](./media/resource-group-create-service-principal-portal/create-app.png)

Foi criada com a aplicação.

## <a name="get-application-id-and-authentication-key"></a>Obter a chave de autenticação e ID de aplicação

Quando programaticamente iniciar sessão, terá do ID para a sua aplicação e uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. De **registos de aplicação** no Azure Active Directory, selecione a aplicação.

   ![Selecionar aplicação](./media/resource-group-create-service-principal-portal/select-app.png)

1. Copiar o **ID da aplicação** e armazená-las no código da aplicação. As aplicações no [aplicações de exemplo](#sample-applications) secção fazer referência a este valor como o ID de cliente.

   ![ID de cliente](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Para gerar uma chave de autenticação, selecione **chaves**.

   ![Selecione as chaves](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Forneça uma descrição da chave e um período de tempo para a chave. Quando terminar, selecione **guardar**.

   ![Guardar chave](./media/resource-group-create-service-principal-portal/save-key.png)

   Depois de guardar a chave, é apresentado o valor da chave. Copie este valor, porque não é possível obter a chave mais tarde. Forneça o valor de chave com o ID de aplicação para iniciar sessão como a aplicação. Armazene o valor da chave em que a aplicação o pode obtê-lo.

   ![guardar a chave](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Obter ID de inquilino

Quando programaticamente iniciar sessão, tem de passar o ID do inquilino com o seu pedido de autenticação.

1. Selecione **do Azure Active Directory**.

   ![Selecione do azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Para obter o ID de inquilino, selecione **propriedades** para o seu inquilino do Azure AD.

   ![selecionar propriedades do Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Copiar o **ID de diretório**. Este valor é o ID do inquilino.

   ![ID do inquilino](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Atribuir a aplicação a função

Para aceder a recursos na sua subscrição, tem de atribuir a aplicação a uma função. Decida qual a função representa as permissões corretas para a aplicação. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md).

Pode definir o âmbito ao nível da subscrição, do grupo de recursos ou do recurso. As permissões são herdadas a níveis inferiores de âmbito. Por exemplo, adicionar uma aplicação para a função de leitor para um grupo de recursos significa podem ler o grupo de recursos e todos os recursos que nele contidos.

1. Navegue para o nível de âmbito que pretende atribuir a aplicação. Por exemplo, para atribuir uma função no âmbito de subscrição, selecione **subscrições**. Em vez disso, pode selecionar um grupo de recursos ou um recurso.

   ![Selecione a subscrição](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Selecione a subscrição específica (grupo de recursos ou recursos) para atribuir a aplicação.

   ![Selecione a subscrição de atribuição](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Selecione **(IAM) do controlo de acesso**.

   ![Selecione o acesso](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Selecione **Adicionar**.

   ![Selecionar adicionar](./media/resource-group-create-service-principal-portal/select-add.png)

1. Selecione a função que pretende atribuir à aplicação. A imagem seguinte mostra o **leitor** função.

   ![Selecione a função](./media/resource-group-create-service-principal-portal/select-role.png)

1. Procure a aplicação e selecione-o.

   ![Procure a aplicação](./media/resource-group-create-service-principal-portal/search-app.png)

1. Selecione **guardar** para concluir a atribuição de função. Verá a aplicação na lista de utilizadores atribuídos a uma função para esse âmbito.

## <a name="log-in-as-the-application"></a>Inicie sessão como a aplicação

A aplicação está agora definida no Azure Active Directory. Tem um ID e a chave a utilizar para iniciar sessão como a aplicação. A aplicação é atribuída a uma função que concede-lhe determinadas ações que pode realizar. Para informações sobre como iniciar sessão como a aplicação através de plataformas diferentes, consulte:

* [PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell)
* [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
* [REST](/rest/api/#create-the-request)
* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Passos seguintes
* Para configurar uma aplicação multi-inquilino, consulte [guia para programadores para autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para saber mais sobre como especificar políticas de segurança, consulte [controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md).  
* Para obter uma lista de ações disponíveis que pode ser concedeu ou negou aos utilizadores, consulte [operações de fornecedor de recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).
