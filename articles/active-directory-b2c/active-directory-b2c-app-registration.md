---
title: "Azure Active Directory B2C: Registo de aplicação | Microsoft Docs"
description: "Como registar a aplicação com o Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: PatAltimore
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.openlocfilehash: 6232459326599d244c6d3f055bf890e78ecf631e
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C : Registar a aplicação

Este guia de início rápido ajuda-o a registar uma aplicação num inquilino do Microsoft Azure Active Directory (Azure AD) B2C em alguns minutos. Quando tiver terminado, a aplicação fica registada para utilização no inquilino do Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma aplicação que aceite a inscrição e o início de sessão do consumidor, terá primeiro de registar a aplicação com um inquilino do Azure Active Directory B2C. Obtenha o seu inquilino, utilizando os passos descritos em [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md).

As aplicações criadas a partir do painel Azure AD B2C no portal do Azure têm de ser geridas a partir da mesma localização. Se utilizar o PowerShell ou outro portal para editar as aplicações B2C, estas deixam de ser suportadas e não irão funcionar com o Azure AD B2C. Veja os detalhes na secção [Aplicações com falha](#faulted-apps). 

Este artigo utiliza exemplos que o ajudam a começar com os nossos exemplos. Pode saber mais sobre estes exemplos nos artigos subsequentes.

## <a name="navigate-to-b2c-settings"></a>Navegar para as definições do B2C

Inicie sessão no [Portal do Azure](https://portal.azure.com/) como Administrador Global do inquilino do B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Escolha os passos seguintes com base no tipo da sua aplicação

* [Registar uma aplicação Web](#register-a-web-app)
* [Registar uma API Web](#register-a-web-api)
* [Registar uma aplicação móvel ou nativa](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registar uma aplicação Web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Criar um segredo do cliente da aplicação Web

Se a sua aplicação Web chamar uma API Web protegida pelo Azure AD B2C, efetue estes passos:
   1. Crie um segredo de aplicação ao aceder ao painel **Chaves** e ao clicar no botão **Gerar Chave**. Anote o valor da **Chave da aplicação**. Utilize o valor como o segredo de aplicação no código da aplicação.
   2. Clique em **Acesso à API**, clique em **Adicionar** e selecione a API Web e os âmbitos (permissões).

> [!NOTE]
> Um **Segredo de Aplicação** é uma credencial de segurança importante e deve ser protegida devidamente.
> 

[Ir para os **passos seguintes**](#next-steps)

### <a name="register-a-web-api"></a>Registar uma API Web

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Clique em **Âmbitos publicados** para adicionar mais âmbitos, conforme necessário. Por predefinição, é definido o âmbito "user_impersonation". O âmbito user_impersonation fornece a outras aplicações a capacidade de aceder a esta API em nome do utilizador com sessão iniciada. Se quiser, o âmbito user_impersonation pode ser removido.

[Ir para os **passos seguintes**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Registar uma aplicação móvel ou nativa

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Ir para os **passos seguintes**](#next-steps)

## <a name="limitations"></a>Limitações

### <a name="choosing-a-web-app-or-api-reply-url"></a>Escolher uma aplicação Web ou um URL de resposta da API

Atualmente, as aplicações registadas com o Azure AD B2C estão restritas a um conjunto limitado de valores de URL de resposta. O URL de resposta para aplicações e serviços Web tem de começar com o esquema `https`, e todos os valores de URL de resposta têm de partilhar um único domínio de DNS. Por exemplo, não pode registar uma aplicação Web que tenha um destes URLs de resposta:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

O sistema de registo compara o nome DNS completo do URL de resposta existente ao nome DNS do URL de resposta que está a adicionar. O pedido para adicionar o nome DNS falha se uma das seguintes condições for verdadeira:

* O nome DNS completo do novo URL de resposta não corresponde ao nome DNS do URL de resposta existente.
* O nome DNS completo do novo URL de resposta não é um subdomínio do URL de resposta existente.

Por exemplo, se a aplicação tiver este URL de resposta:

`https://login.contoso.com`

Pode adicioná-lo, da seguinte forma:

`https://login.contoso.com/new`

Neste caso, o nome DNS corresponde exatamente. Ou pode fazer o seguinte:

`https://new.login.contoso.com`

Neste caso, está a referir-se a um subdomínio de DNS de login.contoso.com. Se quiser ter uma aplicação com login-east.contoso.com e login-west.contoso.com como URLs de resposta, tem de adicionar esses URLs de resposta pela seguinte ordem:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Pode adicionar os dois últimos porque são subdomínios do primeiro URL de resposta, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Escolher um URI de redirecionamento da aplicação nativa

Existem duas considerações importantes sobre quando selecionar um URI de redirecionamento para aplicações móveis/nativas:

* **Exclusivo**: o esquema do URI de redirecionamento deve ser exclusivo para cada aplicação. No nosso exemplo (com.onmicrosoft.contoso.appname://redirect/path), utilizamos com.onmicrosoft.contoso.appname como esquema. Recomendamos seguir este padrão. Se duas aplicações partilharem o mesmo esquema, o utilizador vê uma caixa de diálogo "selecionar aplicação". Se o utilizador fizer uma seleção incorreta, o início de sessão falha.
* **Completo**: o URI de redirecionamento tem de ter um esquema e um caminho. O caminho tem de conter pelo menos uma barra depois do domínio (por exemplo, //contoso/ funciona e //contoso falha).

Certifique-se de que não existem carateres especiais, como carateres de sublinhado, no uri de redirecionamento.

### <a name="faulted-apps"></a>Aplicações com falha

As aplicações B2C NÃO devem ser editadas:

* Noutros portais de gestão de aplicações, como o [portal clássico do Azure](https://manage.windowsazure.com/) e no [Portal de Registo da Aplicação](https://apps.dev.microsoft.com/).
* Utilizar o Graph API ou o PowerShell

Se editar a aplicação B2C, conforme descrito acima, e tentar editá-la novamente no painel de funcionalidades do Azure AD B2C no portal do Azure, ela torna-se uma aplicação com falha e a sua aplicação deixa de ser utilizável com o Azure AD B2C. Tem de eliminar a aplicação e criá-la novamente.

Para eliminar a aplicação, aceda ao [Portal de Registo da Aplicação](https://apps.dev.microsoft.com/) e elimine a aplicação. Para que a aplicação fique visível, tem de ser o proprietário da aplicação (e não apenas um administrador do inquilino).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma aplicação registada no Azure AD B2C, pode concluir um dos nossos [guias de introdução](active-directory-b2c-overview.md#get-started) para começar a trabalhar.

> [!div class="nextstepaction"]
> [Criar uma aplicação Web ASP.NET com inscrição, início de sessão e reposição da palavra-passe](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
