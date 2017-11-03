---
title: "Credenciais de implementação do App Service do Azure | Microsoft Docs"
description: "Saiba como utilizar as credenciais de implementação do App Service do Azure."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 2f8691b0aadbd97e87996c2683849a6db3b74618
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurar as credenciais de implementação para o App Service do Azure
[App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) suportam dois tipos de credenciais para [implementação de Git local](app-service-deploy-local-git.md) e [implementação de FTP/S](app-service-deploy-ftp.md). Estes não são as mesmas que as suas credenciais do Azure Active Directory.

* **Credenciais de utilizador ao nível**: um conjunto de credenciais da conta do Azure completa. Pode ser utilizado para implementar no App Service para qualquer aplicação, em qualquer subscrição, se a conta do Azure tem permissão para aceder. Estes são o conjunto de credenciais predefinido que configurar na **serviços aplicacionais** > **&lt;APP_NAME>.azurewebsites.NET >** > **as credenciais de implementação**. Esta é a predefinição conjunto que é apresentado no portal do GUI (tais como o **descrição geral** e **propriedades** da sua aplicação [painel de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > Quando delegar o acesso aos recursos do Azure através de controlo de acesso baseado em ' (RBAC) da função ou coadministrador de permissões, cada utilizador do Azure que recebe o acesso a uma aplicação pode utilizar sua credenciais de nível de utilizador pessoais até que o acesso é revogado. Estas credenciais de implementação não devem ser partilhados com outros utilizadores do Azure.
    >
    >

* **Credenciais ao nível da aplicação**: um conjunto de credenciais para cada aplicação. Pode ser utilizado para implementar apenas essa aplicação. As credenciais para cada aplicação é gerada automaticamente durante a criação da aplicação e encontra-se na aplicação do perfil de publicação. Não é possível configurar manualmente as credenciais, mas pode repô-los para uma aplicação em qualquer altura.

    > [!NOTE]
    > Para dar alguém aceder a estas credenciais através de função de acesso baseado em controlo (RBAC), tem de torná-los contribuinte ou superior na aplicação Web. Os leitores não são permitidos para publicar e, por conseguinte, não é possível aceder essas credenciais.
    >
    >

## <a name="userscope"></a>Definir e repor as credenciais de nível de utilizador

Pode configurar as suas credenciais de nível de utilizador em qualquer aplicação [painel de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources). Independentemente da aplicação que pode configurar estas credenciais, aplica-se a todas as aplicações e para todas as subscrições da sua conta do Azure. 

Para configurar as suas credenciais de nível de utilizador:

1. No [portal do Azure](https://portal.azure.com), clique em do serviço de aplicações >  **&lt;any_app >** > **as credenciais de implementação**.

    > [!NOTE]
    > No portal, tem de ter pelo menos uma aplicação para poder aceder ao painel de credenciais de implementação. No entanto, com o [CLI do Azure](/cli/azure/webapp/deployment/user#set), pode configurar as credenciais de nível de utilizador sem uma aplicação existente.

2. Configurar o nome de utilizador e palavra-passe e, em seguida, clique em **guardar**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Assim que tiver configurado as suas credenciais de implementação, pode encontrar o *Git* nome de utilizador de implementação na sua aplicação **descrição geral**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

e e *FTP* nome de utilizador de implementação na sua aplicação **propriedades**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure mostra a palavra-passe de implementação de nível de utilizador. Caso se esqueça da palavra-passe, não é possível obtê-lo. No entanto, pode repor as suas credenciais ao seguir os passos nesta secção.
>
>  

## <a name="appscope"></a>Obter e repor as credenciais de nível de aplicação
Para cada aplicação no App Service, as suas credenciais ao nível da aplicação são armazenadas no XML de perfil de publicação.

Para obter as credenciais ao nível da aplicação:

1. No [portal do Azure](https://portal.azure.com), clique em do serviço de aplicações >  **&lt;any_app >** > **descrição geral**.

2. Clique em **... Mais** > **perfil de publicação de Get**, e inicia a transferência para um. Ficheiro PublishSettings.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Abra o. PublishSettings de ficheiros e localize o `<publishProfile>` etiqueta com o atributo `publishMethod="FTP"`. Em seguida, obter o `userName` e `password` atributos.
Estas são as credenciais ao nível da aplicação.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Semelhante às credenciais de nível de utilizador, o nome de utilizador de implementação de FTP tem o formato de `<app_name>\<username>`, sendo apenas o nome de utilizador de implementação de Git `<username>` sem precedente `<app_name>\`.

Para repor as credenciais ao nível da aplicação:

1. No [portal do Azure](https://portal.azure.com), clique em do serviço de aplicações >  **&lt;any_app >** > **descrição geral**.

2. Clique em **... Mais** > **perfil de publicação de reposição**. Clique em **Sim** para confirmar a reposição.

    A ação repor invalida qualquer anteriormente transferidos. Ficheiros PublishSettings.

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar estas credenciais para implementar a aplicação da [local Git](app-service-deploy-local-git.md) ou utilizando [FTP/S](app-service-deploy-ftp.md).
