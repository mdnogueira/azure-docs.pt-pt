---
title: Pedir tokens de acesso - Azure AD B2C | Microsoft Docs
description: "Este artigo irá mostrar como configurar uma aplicação de cliente e adquirir um token de acesso."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: parakhj
ms.openlocfilehash: 7202be4e0e9b8b28b5ec1443d6d248c1738da6fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>O Azure AD B2C: Pedir tokens de acesso

Um token de acesso (como a designação **acesso\_token** nas respostas do Azure AD B2C) é um formulário de token de segurança que um cliente pode utilizar para aceder a recursos que está protegido por um [servidor autorização](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), tais como uma API web. Os tokens de acesso são representados como [JWTs](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) e contêm informações sobre o servidor de recursos pretendidos e as permissões concedidas para o servidor. Quando chamar o servidor de recursos, o token de acesso tem de estar presente no pedido de HTTP.

Este artigo descreve como configurar uma aplicação de cliente e a API web para obter um **acesso\_token**.

> [!NOTE]
> **Web cadeias de API (em-nome-de) não é suportado pelo Azure AD B2C.**
>
> Muitas arquiteturas incluem uma API web que precisa chamar outra API, ambas protegidas pelo Azure AD B2C de web a jusante. Este cenário é comum em clientes nativos que têm um web API de back-end, que por sua vez chama um serviço online da Microsoft, tais como o Azure AD Graph API.
>
> Neste cenário de API web em cadeia pode ser suportado utilizando a concessão de credencial de portador do OAuth 2.0 JWT, caso contrário, conhecida como o fluxo em-nome-de. No entanto, o fluxo em-nome-de atualmente não é implementado no Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registar uma API web e publicar permissões

Antes de pedir um token de acesso, primeiro tem de registar uma API web e publicar permissões (âmbitos) que podem ser concedidas a aplicação de cliente.

### <a name="register-a-web-api"></a>Registar uma API Web

1. No menu de funcionalidades do Azure AD B2C no portal do Azure, clique em **aplicações**.
1. Clique em **+ adicionar** na parte superior do menu.
1. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir "Contoso API".
1. Coloque o comutador **Incluir aplicação Web/API Web** em **Sim**.
1. Introduza um valor arbitrário para o **URLs de resposta**. Por exemplo, introduza `https://localhost:44316/`. O valor não é importante uma vez que um API deve não receber o token diretamente a partir do Azure AD B2C.
1. Introduza um **URI de ID de Aplicação**. Este é o identificador utilizado para a API Web. Por exemplo, introduza 'Notas' na caixa. O **URI de ID de aplicação** , em seguida, seria `https://{tenantName}.onmicrosoft.com/notes`.
1. Clique em **Criar** para registar a aplicação.
1. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.

### <a name="publishing-permissions"></a>Permissões de publicação

Âmbitos, que são semelhante às permissões, são necessários quando a aplicação está a chamar uma API. Alguns exemplos dos âmbitos são "leitura" ou "escrita". Suponha que pretende que a aplicação nativa para "leitura" a partir de uma API ou web. A aplicação iria chamar do Azure AD B2C e pedir um token de acesso que lhe dá acesso ao âmbito "leitura". Por ordem para o Azure AD B2C emitir um token de acesso, a aplicação tem de ser concedida permissão "ler" a partir da API específica. Para tal, a API primeiro tem de publicar o âmbito "ler".

1. No Azure AD B2C **aplicações** menu, abra a API web de aplicação ("API de Contoso").
1. Clique em **Âmbitos publicados**. Esta é a secção onde pode definir as permissões (âmbitos) que podem ser concedidas a outras aplicações.
1. Adicionar **âmbito valores** conforme necessário (por exemplo, "ler"). Por predefinição, será definido o âmbito "user_impersonation". Pode ignorar este se desejar. Introduza uma descrição do âmbito no **nome de âmbito** coluna.
1. Clique em **Guardar**.

> [!IMPORTANT]
> O **nome de âmbito** é a descrição do **valor do âmbito**. Ao utilizar o âmbito, certifique-se de que utiliza o **valor do âmbito**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Conceder um nativo ou web permissões de aplicação a uma API web

Depois de uma API está configurada para publicar âmbitos, a aplicação de cliente tem de ser concedido esses âmbitos através do portal do Azure.

1. Navegue para o **aplicações** menu no menu de funcionalidades do Azure AD B2C.
1. Registar uma aplicação de cliente ([aplicação web](active-directory-b2c-app-registration.md#register-a-web-app) ou [cliente nativo](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) se ainda não tiver um. Se estiver a seguir este guia como ponto de partida, terá de registar uma aplicação de cliente.
1. Clique em **acesso à API**.
1. Clique em **Adicionar**.
1. Selecione a API web e os âmbitos (permissões) que pretende conceder.
1. Clique em **OK**.

> [!NOTE]
> O Azure AD B2C não pedir o cliente utilizadores da aplicação para o seu consentimento. Em vez disso, todos os consentimento é fornecido pelo administrador, com base nas permissões configuradas entre as aplicações descritas acima. Se uma concessão de permissão para uma aplicação é revogado, todos os utilizadores que era anteriormente possível adquirir essa permissão já não será possível fazê-lo.

## <a name="requesting-a-token"></a>Pedir um token

Quando pedir um token de acesso, a aplicação de cliente tem de especificar as permissões pretendidas no **âmbito** parâmetro do pedido. Por exemplo, para especificar o **valor do âmbito** "leitura" para a API que tenha o **URI de ID de aplicação** de `https://contoso.onmicrosoft.com/notes`, o âmbito seria `https://contoso.onmicrosoft.com/notes/read`. Segue-se um exemplo de um pedido de código de autorização para o `/authorize` ponto final.

> [!NOTE]
> Atualmente, os domínios personalizados não são suportados, juntamente com os tokens de acesso. Tem de utilizar o seu domínio tenantName.onmicrosoft.com no URL do pedido.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

A aquisição de várias permissões no mesmo pedido, pode adicionar várias entradas no simples **âmbito** parâmetro, separado por espaços. Por exemplo:

URL de descodificar:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL codificado:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Pode pedir mais âmbitos (permissões) para um recurso que o que é concedido para a sua aplicação de cliente. Quando for este o caso, a chamada terá êxito se pelo menos uma permissão é concedida. Resultante **acesso\_token** terão a afirmação "scp" preenchida com apenas as permissões que foram concedidos com êxito.

> [!NOTE] 
> Não suportamos pedidas permissões contra dois recursos web diferente no mesmo pedido. Este tipo de pedido irá falhar.

### <a name="special-cases"></a>Casos especiais

A norma OpenID Connect especifica vários valores de especiais "âmbito". Os seguintes âmbitos especiais representam a permissão "aceder o perfil do utilizador":

* **openid**: Isto solicita um token de ID
* **offline\_acesso**: Isto solicita um token de atualização (utilizando [código de autenticação flui](active-directory-b2c-reference-oauth-code.md)).

Se o `response_type` parâmetro um `/authorize` pedido inclui `token`, a `scope` parâmetro tem de incluir o âmbito de pelo menos um recurso (diferente de `openid` e `offline_access`) que será atribuído. Caso contrário, o `/authorize` pedido irá terminar com uma falha.

## <a name="the-returned-token"></a>O token devolvido

Com êxito minted **acesso\_token** (partir o `/authorize` ou `/token` endpoint), as seguintes afirmações que estará presentes:

| Nome | Afirmação | Descrição |
| --- | --- | --- |
|Audiência |`aud` |O **ID da aplicação** do que o token concede acesso ao recurso único. |
|Âmbito |`scp` |As permissões concedidas ao recurso. Várias permissões concedidas ficarão separadas por espaço. |
|Terceiros autorizados |`azp` |O **ID da aplicação** da aplicação cliente que iniciou o pedido. |

Quando recebe a sua API o **acesso\_token**, é necessário [validar o token](active-directory-b2c-reference-tokens.md) para provar que o token é autêntico e que tem as afirmações corretas.

Podemos sempre estão abertos para comentários e sugestões! Se tiver dificuldades em causa com este tópico, publique no Stack Overflow utilizando a tag ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Para pedidos de funcionalidades, adicioná-los ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Passos seguintes

* Criar uma API web utilizando [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Criar uma API web utilizando [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
