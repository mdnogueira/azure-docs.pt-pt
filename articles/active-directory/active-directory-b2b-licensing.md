---
title: "Colaboração B2B do Active Directory Azure licenciamento orientações | Microsoft Docs"
description: "B2B de diretório Active Directory do Azure não necessita de colaboração paga licenças do Azure AD, mas pode também obter paga funcionalidades para os utilizadores convidados de B2B"
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
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.openlocfilehash: dfef32c05af157ae8d3a5434016f87f488a35051
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Orientação de licenciamento de colaboração do Azure Active Directory B2B

Pode utilizar as capacidades de colaboração B2B do Azure AD para convidar utilizadores convidados no inquilino do Azure AD para lhes permitir aceder a serviços do Azure AD e outros recursos na sua organização. Se pretender fornecer acesso a paga funcionalidades do Azure AD, B2B os utilizadores convidados de colaboração têm de estar licenciados com adequado de licenças do Azure AD. 

Especificamente:
* Capacidades de gratuita do Azure AD estão disponíveis para os utilizadores convidados sem licenças adicionais.
* Se pretender fornecer acesso a paga funcionalidades do Azure AD B2B utilizadores, tem de ter licenças suficientes para suportar os utilizadores de convidado B2B.
* Um inquilino convidando com um Azure AD, uma licença paga tem colaboração B2B utilizar direitos para um adicionais cinco B2B os utilizadores convidados convidado para o inquilino.
* O cliente proprietário convidando inquilino tem de ser um para determinar quantos colaboração B2B os utilizadores precisam de paga capacidades do Azure AD. Consoante o Azure AD pago funcionalidades que pretende que sejam dos utilizadores convidados, tem de ter suficiente do Azure AD paga licenças para os utilizadores de colaboração do B2B o mesmo rácio de 5:1.

Um utilizador de convidado de colaboração B2B é adicionado como um utilizador de uma empresa parceira, não um empregado da sua organização ou um empregado do que uma visita diferentes na sua conglomerate. Um utilizador de convidado B2B pode iniciar sessão com credenciais externas ou credenciais propriedade pela sua organização, conforme descrito neste artigo. 

Por outras palavras, licenciamento B2B está definido não pela forma como o utilizador efetua a autenticação, mas em vez disso, a relação do utilizador para a sua organização. Se estes utilizadores não são parceiros, estes são tratados de forma diferente em termos de licenciamento. Não são consideradas como um utilizador de colaboração B2B para efeitos de licenciamento, mesmo se os seus UserType está marcada como "Convidado". Devem ser licenciadas normalmente, a uma licença por utilizador. Estes utilizadores incluem:
* Seus empregados
* Equipa de iniciar sessão utilizando as identidades externas
* Um empregado do que uma visita diferentes na sua conglomerate


## <a name="licensing-examples"></a>Exemplos de licenciamento
- Um cliente pretende convidar 100 utilizadores de colaboração B2B ao seu inquilino do Azure AD. O cliente atribui aprovisionamento para todos os utilizadores e de gestão de acesso, mas 50 utilizadores também exigem MFA e o acesso condicional. Tem de comprar o Azure AD Basic 10 e 10 do Azure AD Premium P1 licenças para estes utilizadores B2B corretamente este cliente. Se o cliente planeia utilizar funcionalidades de proteção de identidade com B2B utilizadores, tem de ter licenças do Azure AD Premium P2 para cobrir os utilizadores convidados com o mesmo rácio de 5:1.
- Um cliente tem 10 funcionários que são licenciados todos atualmente com o Azure AD Premium P1. Agora que pretendem convidar 60 utilizadores B2B que requerem autenticação multifator (MFA). Sob a regra de licenciamento de 5:1, o cliente tem de ter, pelo menos, 12 licenças do Azure AD Premium P1 abranger todos os utilizadores de colaboração B2B 60. Porque já tem 10 Premium P1 licenças para os 10 funcionários, têm direitos para convidar 50 utilizadores B2B com funcionalidades Premium P1 como MFA. Neste exemplo, em seguida, estes tem de comprar 2 licenças Premium P1 adicionais para abranger os restantes utilizadores de colaboração B2B 10.

> [!NOTE]
> Não é possível ainda para atribuir licenças diretamente para os utilizadores de B2B a ativar estes direitos de utilizador de colaboração B2B.

O cliente proprietário convidando inquilino tem de ser um para determinar quantos colaboração B2B os utilizadores precisam de paga capacidades do Azure AD. Consoante pagas que funcionalidades do Azure AD que pretende para os utilizadores convidados, tem de ter suficiente do Azure AD paga licenças para os utilizadores de colaboração do B2B o rácio de 5:1. 

## <a name="additional-licensing-details"></a>Detalhes de licenciamento adicionais
- Não é necessário para, efetivamente, atribuir licenças às contas de utilizador de B2B. Com base no rácio de 5:1, licenciamento é automaticamente calculado e reportado.
- Se não paga licença do Azure AD existe no inquilino, todos os utilizadores convidados obtém os direitos de edição gratuita do Azure AD oferece.
- Se um utilizador de colaboração já tem um pago do Azure AD de B2B de licença da respetiva organização, não consumirem uma das licenças de colaboração B2B do inquilino convidando.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Avançadas debate: quais são as considerações de licenciamento, iremos adicionar utilizadores a partir de uma organização conglomerate como "membros", utilizando as suas APIs?
Um utilizador de convidado B2B é aquele que está convidado a partir de uma organização de parceiro para trabalhar com a organização de anfitrião. Normalmente, quaisquer outro caso não qualifique como B2B mesmo-utiliza funcionalidades B2B. Vamos ver dois casos em particular:

1. Se um anfitrião invites um empregado a utilizar um endereço de consumidor
  * Este cenário não é compatível com as nossas políticas de licenciamento e não é recomendado.

2. Se uma organização de anfitrião adiciona um utilizador de outra organização conglomerate
  1. Neste caso, o utilizador é convidou com APIs de B2B, mas neste caso, não é tradicionalmente B2B. Idealmente, deve temos estas organizações convidar os outros utilizadores orgs como membros (nossa API permite que). Neste caso, licenças tem de ser atribuída a estes membros para os mesmos para aceder a recursos na organização de convidando.

  2. Algumas organizações poderão querer adicionar utilizadores de outros org para ser adicionado como "Convidado" como uma política. Existem dois cenários aqui:
      * A organização conglomerate já é utilizar o Azure AD e os utilizadores convidados são licenciados na outra organização: neste caso, esperamos que não seja convidados que os utilizadores têm de seguir a fórmula de 1:5, apresentada anteriormente neste documento. 

      * A organização conglomerate não está a utilizar o Azure AD ou não tem as licenças adequadas: neste caso, siga a fórmula de 1:5, apresentada anteriormente neste documento.

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Resolução de problemas de colaboração B2B do Azure Active Directory do](active-directory-b2b-troubleshooting.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Colaboração B2B do Active Directory Azure API e personalização](active-directory-b2b-api.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
