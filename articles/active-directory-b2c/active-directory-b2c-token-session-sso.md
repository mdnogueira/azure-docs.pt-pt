---
title: "Token, sessão e início de sessão configuração única - Azure AD B2C | Microsoft Docs"
description: "Token, a sessão e a configuração do início de sessão único no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: e78e6344-0089-49bf-8c7b-5f634326f58c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: 256c93e5c343cba022599f8e13c5b7616bfa8b58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Token, sessão e a configuração de início de sessão único

Esta funcionalidade fornece um controlo detalhado, num [base por política](active-directory-b2c-reference-policies.md), de:

1. Durações de tokens de segurança emitidos pelo Azure Active Directory (Azure AD) B2C.
2. Durações de sessões de aplicação web geridas pelo Azure AD B2C.
3. Formatos de afirmações importantes em tokens de segurança emitidos pelo Azure AD B2C.
4. Comportamento início de sessão único (SSO) em várias aplicações e políticas no seu inquilino do B2C.

Para as políticas incorporadas, pode utilizar esta funcionalidade no diretório do Azure AD B2C da seguinte forma:

1. Siga estes passos para [navegue até ao menu de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Clique em **políticas de inscrição ou início de sessão**. *Nota: Pode utilizar esta funcionalidade em qualquer tipo de política, não apenas no **políticas de inscrição ou início de sessão***.
3. Abra uma política clicando nela. Por exemplo, clique em **B2C_1_SiUpIn**.
4. Clique em **editar** na parte superior do menu.
5. Clique em **Token, a sessão e a configuração de início de sessão único**.
6. Efetue as alterações pretendidas. Saiba mais sobre as propriedades disponíveis nas secções subsequentes.
7. Clique em **OK**.
8. Clique em **guardar** no topo do menu.

## <a name="token-lifetimes-configuration"></a>Configuração de durações de token

Azure AD B2C suporta o [protocolo de autorização do OAuth 2.0](active-directory-b2c-reference-protocols.md) para ativar o acesso seguro a recursos protegidos. Para implementar este suporte, Azure AD B2C emite várias [tokens de segurança](active-directory-b2c-reference-tokens.md). Estas são as propriedades que pode utilizar para gerir durações de tokens de segurança emitidos pelo Azure AD B2C:

* **Durações (minutos) de token de acesso & ID**: A duração do token de portador OAuth 2.0 utilizados para obter acesso a um recurso protegido.
  * Predefinição = 60 minutos.
  * Mínimo (inclusive) = 5 minutos.
  * Máximo (inclusive) = 1440 minutos.
* **Atualize a duração do token (dias)**: O período de tempo máximo antes do qual um token de atualização pode ser utilizado para adquirir um token de ID de acesso novo ou (e, opcionalmente, um novo token de atualização, se a aplicação tivesse sido concedida a `offline_access` âmbito).
  * Predefinição = 14 dias.
  * Mínimo (inclusive) = 1 dia.
  * Máximo (inclusive) = 90 dias.
* **Atualizar o token duração de janela deslizante (dias)**: após este período de tempo decorrido o utilizador é forçado a autenticar novamente, independentemente do período de validade mais recente de atualizar o token adquirido pela aplicação. Só pode ser fornecido se o comutador está definido como **Bounded**. Tem de ser maior que ou igual a **duração do token de atualização (dias)** valor. Se o comutador está definido como **Unbounded**, não é possível fornecer um valor específico.
  * Predefinição = 90 dias.
  * Mínimo (inclusive) = 1 dia.
  * Máximo (inclusive) = 365 dias.

Estes são alguns casos de utilização que pode ativar a utilizar estas propriedades:

* Permitir que um utilizador manter indefinidamente, assinado numa aplicação móvel, desde que ele for continuamente Active Directory na aplicação. Pode fazê-lo definindo a **atualização deslizante janela duração do token (dias)** mudar para **Unbounded** na sua política de início de sessão.
* Cumprir os requisitos de conformidade e de segurança da indústria definindo as durações de token de acesso adequados.

    > [!NOTE]
    > Estas definições não estão disponíveis para as políticas de reposição de palavra-passe.
    > 
    > 

## <a name="token-compatibility-settings"></a>Definições de compatibilidade de token

Iremos efetuadas alterações formatação afirmações importantes em tokens de segurança emitidos pelo Azure AD B2C. Isto foi feito para melhorar o nosso suporte de protocolos padrão e interoperabilidade melhor com bibliotecas de identidade de terceiros. No entanto, para evitar interrompendo as aplicações existentes, criámos as seguintes propriedades para permitir que os clientes optar ativamente pelo conforme necessário:

* **O emissor (iss) afirmação**: esta identifica o inquilino do Azure AD B2C que emitiu o token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: Este é o valor predefinido.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Este valor inclui os IDs para o inquilino do B2C e a política utilizada no pedido de token. Se a aplicação ou a biblioteca necessita do Azure AD B2C para estar em conformidade com a [OpenID Connect 1.0 deteção spec](http://openid.net/specs/openid-connect-discovery-1_0.html), utilize este valor.
* **O requerente (sub) afirmação**: esta identifica a entidade, ou seja, o utilizador, para o qual o token de asserções informações.
  * **ObjectID**: Este é o valor predefinido. Preenche o ID de objeto do utilizador no diretório para o `sub` no token de afirmação.
  * **Não suportado**: Isto é fornecido apenas para compatibilidade com versões anteriores e recomendamos que mude para **ObjectID** assim conseguir.
* **Que representa o ID de política de afirmações**: esta identifica o tipo de afirmação para o qual o ID de política utilizado no pedido de token é preenchido.
  * **tfp**: Este é o valor predefinido.
  * **Acr**: Isto é fornecido apenas para compatibilidade com versões anteriores e recomendamos que mude para `tfp` assim conseguir.

## <a name="session-behavior"></a>Comportamento de sessão

Azure AD B2C suporta o [protocolo de autenticação OpenID Connect](active-directory-b2c-reference-oidc.md) para ativar a sessão segura a aplicações web. Estas são as propriedades que pode utilizar para gerir sessões de aplicação web:

* **Aplicação Web duração de sessão (minutos)**: A duração do cookie de sessão do Azure AD B2C armazenado no browser do utilizador após a autenticação com êxito.
  * Predefinição = 1440 minutos.
  * Mínimo (inclusive) = 15 minutos.
  * Máximo (inclusive) = 1440 minutos.
* **Tempo limite de sessão de aplicação Web**: se este parâmetro estiver definido como **absoluto**, o utilizador é forçado a autenticar novamente após o período de tempo especificado pelo **aplicação Web duração de sessão (minutos)** decorrida. Se este parâmetro estiver definido como **Rolling** (a predefinição), o utilizador permanecer com sessão iniciada, desde que o utilizador é continuamente Active Directory na sua aplicação web.

Estes são alguns casos de utilização que pode ativar a utilizar estas propriedades:

* Cumpre os requisitos de segurança e conformidade da indústria, definindo a sessão de aplicação web adequado durações.
* Força nova autenticação após um período de tempo definido durante a interação de um utilizador com uma parte de alta segurança da sua aplicação web. 

    > [!NOTE]
    > Estas definições não estão disponíveis para as políticas de reposição de palavra-passe.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Configuração início de sessão único (SSO)
Se tiver várias aplicações e políticas no seu inquilino do B2C, pode gerir interações do utilizador através da-los utilizando o **configuração de início de sessão único** propriedade. Pode definir a propriedade para uma das seguintes definições:

* **Inquilino**: Esta é a predefinição. Utilizar esta definição permite que várias aplicações e políticas no seu inquilino do B2C para partilhar a mesma sessão do utilizador. Por exemplo, depois de um utilizador inicia sessão numa aplicação, compras de Contoso, ele ou ela pode também perfeitamente inicie sessão no outro Contoso Pharmacy um, após a aceder ao mesmo.
* **Aplicação**: Isto permite-lhe manter uma sessão de utilizador exclusivamente para uma aplicação, independente de outras aplicações. Por exemplo, se pretender que o utilizador iniciar sessão na Contoso Pharmacy (com as mesmas credenciais), mesmo que ele ou já esteja assinada para compras de Contoso, outra aplicação no mesmo B2C de inquilino. 
* **Política**: Isto permite-lhe manter uma sessão de utilizador exclusivamente para uma política, independentemente das aplicações a utilizá-la. Por exemplo, se o utilizador já tem sessão iniciada e concluída um passo de authentication (MFA) do fator de várias, seja pode ser dado acesso para partes de uma maior segurança de várias aplicações enquanto não expira a sessão associada à política.
* **Desativado**: Isto força o utilizador a executar o journey de utilizador completo cada após a execução da política. Por exemplo, isto permite que vários utilizadores inscrever-se à sua aplicação (num cenário de ambiente de trabalho partilhado), permanece mesmo durante um único utilizador com sessão iniciada durante o período de tempo de todo.

    > [!NOTE]
    > Estas definições não estão disponíveis para as políticas de reposição de palavra-passe.
    > 
    > 

