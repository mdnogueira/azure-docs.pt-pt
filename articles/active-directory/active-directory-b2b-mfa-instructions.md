---
title: "Acesso condicional para os utilizadores de colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "Colaboração do Azure Active Directory B2B suporta autenticação multifator (MFA) para acesso seletivo às suas aplicações empresariais"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 09/11/2017
ms.author: sasubram
ms.openlocfilehash: d3917754069ad961ffd9233d852aab6fa172eaef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso condicional para os utilizadores de colaboração do B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticação multifator para utilizadores B2B
Com a colaboração B2B do Azure AD, as organizações podem impor políticas de autenticação multifator (MFA) para os utilizadores de B2B. Estas políticas podem ser impostas no inquilino, aplicação ou nível de utilizador individuais, da mesma forma que estão ativados para empregados a tempo inteiro e membros da organização. Políticas MFA são aplicadas a organização de recursos.

Exemplo:
1. Trabalho de administração ou informações da empresa A invites utilizador da empresa B a uma aplicação *Foo* da empresa A.
2. Aplicação *Foo* na empresa, uma está configurada para exigir a MFA no acesso.
3. Quando o utilizador da empresa B tenta aceder à aplicação *Foo* na empresa um inquilino, estes são-lhe pedidos para concluir um desafio MFA.
4. O utilizador pode configurar a respetiva MFA da empresa A e escolhe a sua opção de MFA.
5. Este cenário funciona para qualquer identidade (Azure AD ou MSA, por exemplo, se os utilizadores da empresa B autenticam com o ID de redes social)
6. Empresa A tem licenças suficientes Premium do Azure AD que suportam MFA. O utilizador da empresa B consome esta licença da empresa A.

Os inquilinos convidando sempre é responsável pela MFA para utilizadores da organização de parceiro, mesmo se a organização do parceiro tem capacidades MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configurar a MFA para utilizadores de colaboração do B2B
Para saber como é fácil para configurar a MFA para utilizadores de colaboração do B2B, consulte como no vídeo seguinte:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Os utilizadores B2B MFA experiência para oferecem resgate
Veja a animação seguinte para ver o resgate experiência:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Repor para os utilizadores de colaboração B2B do MFA
Atualmente, o administrador pode exigir a utilizadores de colaboração B2B a prova cópias de segurança novamente apenas ao utilizar os seguintes cmdlets do PowerShell:

1. Ligar ao Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Obter todos os utilizadores com uma prova dos métodos

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Segue-se um exemplo:

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Repor o método MFA para um utilizador específico exigir que o utilizador de colaboração B2B definir métodos de prova novamente. Exemplo:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Por que razão, efetuar MFA em inquilinos do recurso?

Na versão atual, o MFA está sempre no inquilinos recurso, por motivos de previsão. Por exemplo, vamos supor que um utilizador Contoso (Sally) está convidado a Fabrikam e Fabrikam ativou a MFA para utilizadores B2B.

Se Contoso tem política da MFA ativada para App1, mas não App2, em seguida, se a afirmação de MFA de Contoso no token, vamos ver, poderá ver o seguinte problema:

* Dia 1: Um utilizador tiver a MFA na Contoso e está a aceder ao App1, em seguida, nenhum MFA adicional linha é apresentada na Fabrikam.

* Dia 2: O utilizador acedeu 2 de aplicação em Contoso, pelo que agora ao aceder a Fabrikam, tem de se registar para a MFA não existe.

Este processo pode ser confuso e levar para remover o início de sessão conclusões.

Além disso, mesmo que a Contoso tem capacidade MFA, não é sempre que o caso, a Fabrikam seria confiar a política da Contoso MFA.

Por fim, o inquilino de recursos MFA também funciona para MSAs e IDs de redes sociais e para organizações do parceiro que não têm o MFA configurar.

Por conseguinte, as recomendações para a MFA para utilizadores B2B é sempre exigir a MFA no inquilino convidando. Este requisito pode levar à MFA duplo em alguns casos, mas sempre que aceder convidando inquilino, a experiência dos utilizadores finais é previsível: Sally tem de se registar para a MFA com o inquilino convidando.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Acesso condicional baseado no dispositivo, com base na localização e com base em risco para utilizadores B2B

Quando Contoso ativa políticas de acesso condicional baseado no dispositivo para os dados empresariais, o acesso é impedido de dispositivos que não são geridos pelo Contoso e não conformes com as políticas de dispositivo de Contoso.

Se o B2B dispositivo do utilizador não é gerido pelo Contoso, acesso de utilizadores de B2B das organizações de parceiro é bloqueado qualquer contexto estas políticas são impostas. No entanto, a Contoso pode criar listas de exclusão que contém utilizadores parceira específica para os excluir da política de acesso condicional baseado no dispositivo.

#### <a name="location-based-conditional-access-for-b2b"></a>Acesso condicional com base na localização do B2B

Políticas de acesso condicional com base na localização podem ser impostas para utilizadores B2B se a organização convidando é capaz de criar um intervalo de endereços IP fidedigno que define as organizações de parceiros.

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso condicional baseado em risco para B2B

Atualmente, não não possível aplicar políticas baseadas em risco de início de sessão aos utilizadores B2B, porque a avaliação de risco é efetuada na organização da página principal do utilizador B2B.

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Resolução de problemas de colaboração B2B do Azure Active Directory do](active-directory-b2b-troubleshooting.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Colaboração B2B do Active Directory Azure API e personalização](active-directory-b2b-api.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos da gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
