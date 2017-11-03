---
title: "Como preencher a campos específicos para uma aplicação desenvolvida personalizado | Microsoft Docs"
description: "Orientações sobre a preencher a campos específicos ao registar uma aplicação personalizada desenvolvida com o Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 04fd35f238e4dd05486f85b0b16c2ab0c5ae9f30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Como preencher a campos específicos para uma aplicação desenvolvida personalizada

Este artigo dão-lhe uma breve descrição de todos os campos disponíveis no formulário de registo de aplicação no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registar uma nova aplicação

-   Para registar uma nova aplicação, navegue para o [portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **do Azure Active Directory.**

-   Escolha **registos de aplicação** e clique em **adicionar**.

-   Este abrir o formulário de registo de aplicação de cópia de segurança.

## <a name="fields-in-the-application-registration-form"></a>Campos no formulário de registo de aplicação


| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome da aplicação. Este deve ter um mínimo de quatro caracteres.                |
| Tipo de aplicação | **Web app/Web API**: uma aplicação que representa uma aplicação web, uma API web ou ambos 
| |**Nativo**: uma aplicação que pode ser instalada num computador ou dispositivo do utilizador           |
| URL de início de sessão      | O URL onde os utilizadores podem iniciar sessão e utilizar a aplicação                                  |

Assim que tiver preencher os campos acima, a aplicação ser registado no portal do Azure e ser redirecionado para a página da aplicação. O **definições** botão no painel aplicação abre a página de definições, que tem mais campos para personalizar a sua aplicação. A tabela abaixo descreve todos os campos na página Definições. tenha em atenção que apenas conseguiria obter um subconjunto destes campos, dependendo se criou uma aplicação web ou uma aplicação nativa.

| Campo           | Descrição                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID da aplicação  | Ao registar uma aplicação, o Azure AD atribui a aplicação um ID de aplicação. A aplicação ID pode ser utilizado para identificar exclusivamente a sua aplicação em pedidos de autenticação para o Azure AD, bem como para aceder aos recursos, como a Graph API.                                                          |
| URI de ID de aplicação      | Isto deve ser um URI exclusivo, normalmente com a forma **https://&lt;inquilino\_nome&gt;/&lt;aplicação\_nome&gt;.** Isto é utilizado durante o fluxo de conceder de autorização, como um identificador exclusivo para especificar o recurso que o token deve ser emitido para. Também torna-se a afirmação 'aud' no token de acesso emitidos. |
| Carregar o logótipo novo | Pode utilizá-lo para carregar um logótipo para a sua aplicação. O logótipo tem de estar no formato *.bmp,. jpg ou PNG e o tamanho do ficheiro deve ser inferior a 100KB. As dimensões para a imagem devem ser 215 x 215 pixels, com dimensões de imagem central de 94 x 94 pixels.                                                       |
| URL de página inicial   | Este é o URL de início de sessão especificado durante o registo de aplicação.                                                                                                                                                                                                                                              |
| URL de fim de sessão      | Este URL de fim de sessão de início de sessão único. Azure AD envia um pedido de fim de sessão para este URL quando o utilizador. limpa a sessão com o Azure AD com qualquer outra aplicação registada.                                                                                                                                       |
| Várias-tenanted  | Este parâmetro especifica se a aplicação pode ser utilizada por vários inquilinos. Normalmente, isto significa que organizações externas podem utilizar a aplicação, registando-o no seu inquilino e conceder acesso a dados da sua organização.                                                                   |
| URLs de resposta      | A resposta URLs são os pontos finais onde do Azure AD devolver qualquer tokens que solicita a sua aplicação.                                                                                                                                                                                                          |
| URI de redirecionamento   | Para aplicações nativas, o que é onde o utilizador ser enviado para o seguinte autorização com êxito. Verificação do AD do Azure que forneça o URI da aplicação de redirecionamento do pedido de OAuth 2.0 corresponde a um dos valores registados no portal.                                                            |
| Chaves            | Pode criar chaves para programaticamente acesso APIs web protegidas pelo Azure AD sem qualquer interação do utilizador. Do \* \*chaves\* \* página, introduza uma descrição de chave e a data de expiração e guardar para gerar a chave. Certifique-se para guardá-lo algures segura, como não conseguirá aceder ao mesmo mais tarde.             |

## <a name="next-steps"></a>Passos seguintes
[Gestão de aplicações com o Azure Active Directory](active-directory-enable-sso-scenario.md)
