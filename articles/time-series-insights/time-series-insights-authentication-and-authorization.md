---
title: "Configurar a autenticação e autorização para uma aplicação personalizada que chama a API de informações de série de tempo do Azure | Microsoft Docs"
description: "Este tutorial explica como configurar a autenticação e autorização para uma aplicação personalizada que chama a API de informações de série de tempo do Azure"
keywords: 
services: time-series-insights
documentationcenter: 
author: dmdenmsft
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/24/2017
ms.author: dmden
ms.openlocfilehash: abc3b5400e6961a798e1e6bf9e4986304046e845
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API de Insights de série de tempo do Azure

Este artigo explica como configurar uma aplicação personalizada que chama a API de informações de série de tempo do Azure.

## <a name="service-principal"></a>Principal de serviço

Esta secção explica como configurar uma aplicação para aceder a API de informações de séries de tempo em nome da aplicação. A aplicação pode consultar dados ou publicar dados de referência no ambiente de informações de séries de tempo com credenciais de aplicação e não as credenciais do utilizador.

Quando tiver uma aplicação que necessita de informações de série de tempo de acesso, tem de configurar uma aplicação do Azure Active Directory e atribuir as políticas de acesso de dados no ambiente de informações de séries de tempo. Esta abordagem é preferível executar a aplicação com as suas próprias credenciais porque:

* Pode atribuir permissões para a identidade de aplicação que são diferentes das suas permissões. Normalmente, estas permissões são restritos exatamente o que a aplicação tem de fazer. Por exemplo, pode permitir que a aplicação para só de leitura de dados num ambiente Insights de séries de tempo específico.
* Não tem de alterar as credenciais da aplicação, se alterar as suas responsabilidades.
* Pode utilizar um certificado ou uma chave de aplicação para automatizar a autenticação quando estiver a executar um script automático.

Este artigo mostra como efetuar os passos através do portal do Azure. Concentra-se uma aplicação do inquilino único onde a aplicação foi concebida para ser executada na organização apenas um. Normalmente utiliza aplicações de inquilino único para aplicações de linha de negócio que são executadas na sua organização.

O fluxo de configuração consiste em três passos de alto nível:

1. Crie uma aplicação no Azure Active Directory.
2. Autorize a aplicação a aceder ao ambiente de informações de séries de tempo.
3. Utilize o ID da aplicação e a chave para adquirir um token para o `"https://api.timeseries.azure.com/"` audiência ou recurso. O token, em seguida, pode ser utilizado para chamar a API de informações de séries de tempo.

Eis os passos detalhados:

1. No portal do Azure, selecione **do Azure Active Directory** > **registos de aplicação** > **novo registo de aplicação**.

   ![Novo registo de aplicação no Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Dê um nome de aplicação, selecione o tipo a ser **aplicação Web / API**, selecione qualquer URI válido para **URL de início de sessão**e clique em **criar**.

   ![Criar a aplicação no Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Selecione a aplicação criada recentemente e copie o ID da aplicação no seu editor de texto favorito.

   ![Copie o ID da aplicação](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Selecione **chaves**, introduza o nome da chave, selecione a expiração e clique em **guardar**.

   ![Selecione as chaves de aplicação](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Introduza o nome da chave e a expiração e clique em Guardar](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Copie a chave para o editor de texto favorito.

   ![Copie a chave de aplicação](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Para o ambiente de informações de séries de tempo, selecione **políticas de acesso de dados** e clique em **adicionar**.

   ![Adicionar nova política de acesso de dados no ambiente de informações de séries de tempo](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. No **selecionar utilizador** ou ID da aplicação (a partir do passo 3) a caixa de diálogo, cole o nome da aplicação (a partir do passo 2).

   ![Localizar uma aplicação na caixa de diálogo Selecionar utilizador](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Selecione a função (**leitor** para consultar os dados, **contribuinte** para consultar os dados e a alteração de dados de referência) e clique em **Ok**.

   ![Escolher o leitor ou contribuinte na caixa de diálogo Selecionar função](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Guardar a política clicando **Ok**.

10. Utilize o ID da aplicação (a partir do passo 3) e a chave da aplicação (a partir do passo 5) para obter o token em nome da aplicação. O token, em seguida, pode ser transmitido a `Authorization` cabeçalho quando a aplicação chama a API de informações de séries de tempo.

    Se estiver a utilizar c#, pode utilizar o seguinte código para obter o token em nome da aplicação. Para um exemplo completo, consulte [consultar dados com c#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

## <a name="next-steps"></a>Passos seguintes

Utilize o ID da aplicação e a chave na sua aplicação. Para o código de exemplo chama a API de informações de séries de tempo, consulte [consultar dados com c#](time-series-insights-query-data-csharp.md).

## <a name="see-also"></a>Consultar também

* [API de consulta](/rest/api/time-series-insights/time-series-insights-reference-queryapi) para a referência da API de consulta completa
* [Criar um principal de serviço no portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)
