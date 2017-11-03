---
title: "Introdução ao Azure Active Directory Identity Protection e o Microsoft Graph | Microsoft Docs"
description: "Fornece uma introdução ao Microsoft Graph de consulta para obter uma lista de eventos de risco e informações associadas do Azure Active Directory."
services: active-directory
keywords: "eventos de risco, proteção de identidade do Azure Active Directory, a política de segurança, o Microsoft Graph e vulnerabilidade"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 9b01ff86da6a1fd4a439a6ba59ea15ed6480cdad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory Identity Protection e o Microsoft Graph
Microsoft Graph é o Microsoft unified ponto final de API e a base [do Azure Active Directory Identity Protection](active-directory-identityprotection.md) APIs. A primeira API **identityRiskEvents**, permite-lhe consultar Microsoft Graph para uma lista de [eventos de risco](active-directory-identityprotection-risk-events-types.md) e informações de associados. Este artigo obtém a começar a consultar esta API. Para uma introdução aprofundada, documentação completa e acesso para o Explorador do gráfico, consulte o [site Microsoft Graph](https://graph.microsoft.io/).

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo.

Existem três passos para aceder aos dados de proteção de identidade através do Microsoft Graph:

1. Adicione uma aplicação com um segredo do cliente. 
2. Utilize este segredo e alguns outros tipos de informações para autenticar para o Microsoft Graph, onde recebe um token de autenticação. 
3. Utilize este token para fazer pedidos para o ponto final de API e voltar a dados da proteção de identidade.

Antes de começar, terá de:

* Privilégios de administrador para criar a aplicação no Azure AD
* O nome de domínio do inquilino (por exemplo, contoso.onmicrosoft.com)

## <a name="add-an-application-with-a-client-secret"></a>Adicionar uma aplicação com um segredo do cliente
1. [Inicie sessão no](https://manage.windowsazure.com) para o portal clássico do Azure como administrador. 
2. No painel de navegação esquerdo, clique em **do Active Directory**. 
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
4. No menu na parte superior, clique em **aplicações**.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. Clique em **adicionar** na parte inferior da página.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação que a minha organização está a desenvolver**.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. No **conte-na sua aplicação** caixa de diálogo, execute os seguintes passos:
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. No **nome** caixa de texto, escreva um nome para a sua aplicação (por ex.: aplicação de API de eventos de risco AADIP).
   
    b. Como **tipo**, selecione **aplicação Web e / ou Web API**.
   
    c. Clique em **Seguinte**.
8. No **as propriedades da aplicação** caixa de diálogo, execute os seguintes passos:
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. No **URL de início de sessão** caixa de texto, tipo `http://localhost`.
   
    b. No **URI de ID de aplicação** caixa de texto, tipo `http://localhost`.
   
    c. Clique em **Concluído**.

Agora pode configurar a sua aplicação.

![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder permissão para utilizar a API a aplicação
1. Na página da aplicação, no menu na parte superior, clique em **configurar**. 
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. No **permissões para outras aplicações** secção, clique em **Adicionar aplicação**.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. No **permissões para outras aplicações** caixa de diálogo, execute os seguintes passos:
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. Selecione **Microsoft Graph**.
   
    b. Clique em **Concluído**.
4. Clique em **permissões de aplicação: 0**e, em seguida, selecione **ler todas as informações de eventos de risco de identidade**.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. Clique em **Guardar** na parte inferior da página.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>Obter uma chave de acesso
1. Na página da aplicação, no **chaves** secção, selecione de 1 ano como duração.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. Clique em **Guardar** na parte inferior da página.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. na secção de chaves, copie o valor da sua chave recém-criado e, em seguida, cole-o numa localização segura.
   
    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > Se perder a esta chave, terá de voltar a esta secção e crie uma nova chave. Manter esta chave de segredo: qualquer pessoa que tenha pode aceder aos seus dados.
   > 
   > 
4. No **propriedades** secção, copie o **ID de cliente**e, em seguida, cole-o numa localização segura. 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticar para o Microsoft Graph e consultar a API de eventos de risco de identidade
Neste momento, é necessário:

* O ID de cliente que copiou acima
* A chave que copiou acima
* O nome de domínio do inquilino

Para autenticar, enviar um pedido post para `https://login.microsoft.com` com os seguintes parâmetros no corpo do:

* grant_type: "**client_credentials**"
* recurso: "**https://graph.microsoft.com**"
* client_id:<your client ID>
* client_secret:<your key>

> [!NOTE]
> É necessário fornecer valores para o **client_id** e **client_secret** parâmetro.
> 
> 

Se tiver êxito, esta ação devolve um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

    `Authorization`=”<token_type> <access_token>"


Quando a autenticação, pode encontrar o tipo de token e um token de acesso no token devolvido.

Envie este cabeçalho como um pedido para o seguinte URL de API:`https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se tiver êxito, é uma coleção de eventos de risco de identidade e dados associados no formato JSON de OData, que pode ser analisado e processado como julgar.

Eis o código de exemplo para autenticar e chamar a API utilizando o Powershell.  
Basta adicionar o seu ID de cliente, a chave e domínio de inquilino.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Passos seguintes
Parabéns, efetuadas apenas a primeira chamada para o Microsoft Graph!  
Agora pode consultar eventos de risco de identidade e utilizar os dados, no entanto, julgar.

Para obter mais informações sobre como criar aplicações utilizando a Graph API e o Microsoft Graph, veja o [documentação](https://graph.microsoft.io/docs) e muito mais informações sobre o [site Microsoft Graph](https://graph.microsoft.io/). Além disso, certifique-se marcar o [API do Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) página que apresenta uma lista de todas as APIs de proteção de identidade disponíveis no gráfico. À medida que adiciona novas formas de trabalhar com a proteção de identidade através de API, irá vê-los nessa página.

## <a name="additional-resources"></a>Recursos adicionais
* [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)
* [Tipos de eventos de risco detetados pelo Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Descrição geral do Microsoft Graph](https://graph.microsoft.io/docs)
* [Raiz do serviço do Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

