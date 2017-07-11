---
title: "Azure Active Directory B2C: Registo de aplicação | Microsoft Docs"
description: "Como registar a aplicação com o Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: pt-pt
ms.lasthandoff: 07/04/2017



---
<a id="azure-active-directory-b2c-register-your-application" class="xliff"></a>

# Azure Active Directory B2C : Registar a aplicação

> [!IMPORTANT]
> As aplicações criadas a partir do painel Azure AD B2C no portal do Azure têm de ser geridas a partir da mesma localização. Se utilizar o PowerShell ou outro portal para editar as aplicações B2C, estas deixam de ser suportadas e não irão funcionar com o Azure AD B2C. Saiba mais [abaixo](#faulted-apps).
>

<a id="prerequisite" class="xliff"></a>

## Pré-requisito

Para criar uma aplicação que aceite a inscrição e o início de sessão do consumidor, terá primeiro de registar a aplicação com um inquilino do Azure Active Directory B2C. Obtenha o seu inquilino, utilizando os passos descritos em [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todos os passos do respetivo artigo, terá o painel de funcionalidades do B2C afixado no seu Startboard.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

<a id="navigate-to-the-b2c-features-blade" class="xliff"></a>

## Navegar para o painel de funcionalidades do B2C

Se tiver o painel de funcionalidades do B2C afixado no seu Startboard, irá vê-lo assim que iniciar sessão no [Portal do Azure](https://portal.azure.com/) como Administrador Global do inquilino do B2C.

Também pode aceder ao painel ao clicar em **Mais serviços** e depois pesquisar **Azure AD B2C** no painel de navegação à esquerda no [Portal do Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Precisa de ser um Administrador Global de inquilino do B2C para poder aceder ao painel de funcionalidades do B2C. Um Administrador Global de qualquer outro inquilino ou um utilizador de qualquer outro inquilino não poderá aceder.  Pode mudar para o seu inquilino B2C ao utilizar o comutador do inquilino no canto superior direito do portal do Azure.
>
>

<a id="register-a-web-application" class="xliff"></a>

## Registar uma aplicação Web

1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
1. Clique em **+ Adicionar** na parte superior do painel.
1. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “Contoso B2C app”.
1. Coloque o comutador **Incluir aplicação Web/API Web** em **Sim**.
1. Introduza um valor [adequado](#limitations) para os **URLs de resposta**, que são pontos finais para onde o Azure AD B2C devolverá quaisquer tokens que a aplicação solicite. Por exemplo, introduza `https://localhost:44316/`.
1. Clique em **Criar** para registar a aplicação.
1. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.
1. Se a aplicação Web também chamar uma API Web protegida pelo Azure AD B2C, recomendamos que:
    1. Crie um **Segredo de Aplicação**, acedendo ao painel **Chaves** e clicando no botão **Gerar Chave**.
    1. Clique em **Acesso à API**, clique em **Adicionar** e selecione a API Web e os âmbitos (permissões).

> [!NOTE]
> Um **Segredo de Aplicação** é uma credencial de segurança importante e deve ser protegida devidamente.
>

<a id="register-a-web-api" class="xliff"></a>

## Registar uma API Web

1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
1. Clique em **+ Adicionar** na parte superior do painel.
1. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “API Contoso B2C”.
1. Coloque o comutador **Incluir aplicação Web/API Web** em **Sim**.
1. Introduza um valor [adequado](#choosing-a-web-app/api-reply-url) para os **URLs de resposta**, que são pontos finais para onde o Azure AD B2C devolverá quaisquer tokens que a aplicação solicite. Por exemplo, introduza `https://localhost:44316/`.
1. Introduza um **URI de ID de Aplicação**. Este é o identificador utilizado para a API Web. Por exemplo, introduza "notas". Irá gerar o URI do identificador completo abaixo.
1. Clique em **Criar** para registar a aplicação.
1. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.
1. Clique em **Âmbitos publicados**. Esta é a secção onde pode definir as permissões (âmbitos) que podem ser concedidas a outras aplicações.
1. Adicione mais âmbitos conforme necessário. Por predefinição, será definido o âmbito "user_impersonation". Esta ação fornece a outras aplicações a capacidade de aceder a esta api em nome do utilizador com sessão iniciada. Isto pode ser removido se desejar.
1. Clique em **Guardar**.

<a id="register-a-mobilenative-application" class="xliff"></a>

## Registar uma aplicação móvel/nativa

1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
1. Clique em **+ Adicionar** na parte superior do painel.
1. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “Contoso B2C app”.
1. Coloque o comutador **Incluir cliente nativo** em **Sim**.
1. Introduza um **URI de redirecionamento** com um esquema personalizado. Por exemplo, com.onmicrosoft.contoso.appname://redirect/path. Certifique-se de que escolhe um [bom URI de redirecionamento](#choosing-a-native-application-redirect-uri) e não inclui caracteres especiais, como carateres de sublinhado.
1. Clique em **Guardar** para registar a aplicação.
1. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.
1. Se a aplicação nativa também chamar uma API Web protegida pelo Azure AD B2C, recomendamos que:
    1. Crie um **Segredo de Aplicação**, acedendo ao painel **Chaves** e clicando no botão **Gerar Chave**.
    1. Clique em **Acesso à API**, clique em **Adicionar** e selecione a API Web e os âmbitos (permissões).

> [!NOTE]
> Um **Segredo de Aplicação** é uma credencial de segurança importante e deve ser protegida devidamente.
>

<a id="limitations" class="xliff"></a>

## Limitações

<a id="choosing-a-web-appapi-reply-url" class="xliff"></a>

### Escolher um URL de respostas de aplicação Web/api

Atualmente, as aplicações registadas com o Azure AD B2C estão restritas a um conjunto limitado de valores de URL de resposta. O URL de resposta para aplicações e serviços Web tem de começar com o esquema `https`, e todos os valores de URL de resposta têm de partilhar um único domínio de DNS. Por exemplo, não pode registar uma aplicação Web que tenha um destes URLs de resposta:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

O sistema de registo compara o nome DNS completo do URL de resposta existente ao nome DNS do URL de resposta que está a adicionar. O pedido para adicionar o nome DNS irá falhar, se uma das seguintes condições for verdadeira:

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

<a id="choosing-a-native-application-redirect-uri" class="xliff"></a>

### Escolher um URI de redirecionamento da aplicação nativa

Existem duas considerações importantes sobre quando selecionar um URI de redirecionamento para aplicações móveis/nativas:

* **Exclusivo**: o esquema do URI de redirecionamento deve ser exclusivo para cada aplicação. No nosso exemplo (com.onmicrosoft.contoso.appname://redirect/path), utilizamos com.onmicrosoft.contoso.appname como esquema. Recomendamos seguir este padrão. Se duas aplicações partilharem o mesmo esquema, o utilizador verá uma caixa de diálogo "selecionar aplicação". Se o utilizador fizer uma seleção incorreta, o início de sessão falhará.
* **Completo**: o URI de redirecionamento tem de ter um esquema e um caminho. O caminho tem de conter pelo menos uma barra depois do domínio (por exemplo, //contoso/ irá funcionar e //contoso irá falhar).

Certifique-se de que não existem carateres especiais, como carateres de sublinhado, no uri de redirecionamento.

<a id="faulted-apps" class="xliff"></a>

### Aplicações com falha

As aplicações B2C NÃO devem ser editadas:

* Noutros portais de gestão de aplicações, como o [portal clássico do Azure](https://manage.windowsazure.com/) e no [Portal de Registo da Aplicação](https://apps.dev.microsoft.com/).
* Utilizar o Graph API ou o PowerShell

Se editar a aplicação B2C, conforme descrito acima, e tentar editá-la novamente no painel de funcionalidades do Azure AD B2C no portal do Azure, ela tornar-se-á numa aplicação com falhas e a sua aplicação deixará de ser utilizável com o Azure AD B2C. Terá de eliminar a aplicação e criá-la novamente.

Para eliminar a aplicação, aceda ao [Portal de Registo da Aplicação](https://apps.dev.microsoft.com/) e elimine a aplicação. Para que a aplicação fique visível, tem de ser o proprietário da aplicação (e não apenas um administrador do inquilino).

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Agora que tem uma aplicação registada no Azure AD B2C, pode concluir um dos nossos [guias de introdução](active-directory-b2c-overview.md#get-started) para começar a trabalhar.

