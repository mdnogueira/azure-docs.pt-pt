---
title: "Do Azure Active Directory B2C: Políticas incorporadas | Microsoft Docs"
description: "Um tópico sobre a estrutura de política extensível do Azure Active Directory B2C e sobre como criar vários tipos de política"
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mbaldwin
editor: PatAltimore
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.openlocfilehash: daad3af089afdf76b930053728bb11a5cf4c2a92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Do Azure Active Directory B2C: Políticas incorporadas


A estrutura de política extensível do Azure Active Directory (Azure AD) B2C é a força de principal do serviço. Políticas totalmente descrevem experiências de identidade do consumidor, tais como inscrição, início de sessão ou edição de perfil. Por exemplo, uma política de inscrição permite-lhe controlar comportamentos ao configurar as seguintes definições:

* Tipos de conta (contas de redes sociais, como o Facebook) ou contas locais, tais como endereços de correio eletrónico que os consumidores podem utilizar para se inscrever para a aplicação
* Atributos (por exemplo, nome próprio, código postal e tamanho shoe) a serem recolhidos do consumidor durante a inscrição
* Utilização do multi-factor Authentication do Azure
* O aspeto e funcionalidade de todas as páginas de inscrição
* Informações (que manifestos como afirmações num token) que a aplicação recebe quando a política de execução depois de concluída

Pode criar várias políticas de tipos diferentes no seu inquilino e utilizá-los nas suas aplicações, conforme necessário. As políticas podem ser reutilizadas em aplicações. Esta flexibilidade permite aos programadores definir e modificar experiências de identidade do consumidor mínima com ou sem alterações para o seu código.

As políticas estão disponíveis para utilização através de uma interface de programação simples. A aplicação aciona uma política através da utilização de um pedido de autenticação HTTP standard (transmitir um parâmetro de política no pedido) e recebe um token personalizado como resposta. Por exemplo, a única diferença entre os pedidos que invocam a uma política de inscrição e pedidos que invocam a uma política de início de sessão é o nome da política que é utilizado no parâmetro de cadeia de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Para obter mais informações sobre a arquitetura de política, consulte [neste blogue sobre o Azure AD B2C no blogue de segurança de Enterprise Mobility and](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou início de sessão

Esta política processa ambas as experiências de inscrição & início de sessão do consumidor com uma configuração única. Os consumidores são guiados para baixo a solução certa (inscrição ou início de sessão) dependendo no contexto. Também descreve os conteúdos de tokens que a aplicação receberá após ups de início de sessão com êxito ou inícios de sessão.  Um exemplo de código para a política de inscrição ou início de sessão é [aqui disponível](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  É recomendados que utiliza esta política através de uma política de inscrição e a política de início de sessão.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Criar uma política de inscrição

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Criar uma política de início de sessão

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfis

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Criar uma política de reposição de palavra-passe

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Como associar a uma política de inscrição ou iniciar sessão com uma política de reposição de palavra-passe?
Quando criar uma política de inscrição ou início de sessão (com contas locais), verá um **palavra-passe de Forgot?** ligação na primeira página da experiência. Ao clicar nesta hiperligação não automaticamente acionador uma palavra-passe política de reposição. 

Em vez disso, o código de erro  **`AADB2C90118`**  é devolvido para a sua aplicação. A aplicação tem de processar este código de erro ao invocar uma política de reposição de palavra-passe específica. Para obter mais informações, consulte um [amostra que demonstra a abordagem de ligar políticas](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Posso utilizar uma política de inscrição ou início de sessão ou uma política de inscrição e uma política de início de sessão?
Recomendamos que utilize uma política de inscrição ou início de sessão através de uma política de inscrição e uma política de início de sessão.  

A política de inscrição ou início de sessão tem mais capacidades que a política de início de sessão. Também permite-lhe utilizar personalização de IU de página e tem um melhor suporte para a localização. 

A política de início de sessão é recomendada se não precisa de localizar as suas políticas, só precisa de capacidades de personalização secundária para a imagem corporativa e pretender palavra-passe reposição incorporada no mesmo.

## <a name="next-steps"></a>Passos seguintes
* [Token, a sessão e a configuração de início de sessão único](active-directory-b2c-token-session-sso.md)
* [Desativar a verificação de correio eletrónico durante a inscrição de consumidor](active-directory-b2c-reference-disable-ev.md)

