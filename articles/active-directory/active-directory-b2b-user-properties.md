---
title: "Propriedades de um utilizador de colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "Propriedades de utilizador de colaboração B2B do Active Directory do Azure são configuráveis"
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
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 6e49cb202ed03bf50fb9ca34d34924cda434829c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propriedades de um utilizador de colaboração do Azure Active Directory B2B

Um utilizador de colaboração do Azure Active Directory (Azure AD) empresa-empresa (B2B) é um utilizador com UserType = convidado. Este utilizador convidado normalmente provém de uma organização de parceiro e limitou privilégios no diretório convidando, por predefinição.

Consoante as necessidades da organização convidando, um utilizador de colaboração B2B do Azure AD pode ser um dos seguintes Estados de conta:

- Estado 1: Duplamente, numa instância externa do Azure AD e representado como um utilizador convidado na organização convidando. Neste caso, o utilizador de B2B inicia sessão utilizando uma conta do Azure AD que pertence ao inquilino convidado. Se a organização do parceiro não utilizar o Azure AD, o utilizador convidado no Azure AD ainda é criado. Os requisitos são que resgatar a sua convite e AD do Azure verifica o respetivo endereço de e-mail. Esta disposição também é denominada a arquitetura um just-in-time (JIT) ou um inquilinos "viral".

- Estado de 2: Duplamente, numa conta Microsoft e representado como um utilizador convidado na organização do anfitrião. Neste caso, o utilizador convidado inicia sessão com uma conta Microsoft. Identidade do utilizador convidados de redes sociais (google.com ou semelhante), que não é uma conta Microsoft, é criado como uma conta Microsoft durante resgate de oferta.

- Estado 3: Duplamente, no Active Directory no local da organização de anfitrião e sincronizadas com Azure a organização de anfitrião AD. Durante esta versão, tem de utilizar o PowerShell para alterar manualmente o UserType desses utilizadores na nuvem.

- Estado 4: Duplamente, na Azure do organização de anfitrião AD com UserType = convidado e as credenciais que gere a organização de anfitrião.

  ![Apresentar iniciais do inviter](media/active-directory-b2b-user-properties/redemption-diagram.png)


Agora, vamos ver o aspeto de um utilizador de colaboração B2B do Azure AD no estado 1 no Azure AD.

### <a name="before-invitation-redemption"></a>Antes de resgate convite

![Antes de oferta resgate](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Depois de resgate convite

![Depois de oferta resgate](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Propriedades chave do utilizador de colaboração B2B do Azure AD
### <a name="usertype"></a>UserType
Esta propriedade indica a relação do utilizador para os inquilinos do anfitrião. Esta propriedade pode ter dois valores:
- Membro: Este valor indica um empregado da organização de anfitrião e um utilizador no salarial da organização. Por exemplo, este utilizador espera que tenham acesso aos sites meramente internos. Este utilizador não possam ser considerado um colaborador externo.

- Convidado: Este valor indica que um utilizador que não é considerado interno para a empresa, como um colaborador externo, parceiros, cliente ou utilizador semelhante. Essa um utilizador não deverá receber memorando interno um CEO ou receber os benefícios da empresa, por exemplo.

  > [!NOTE]
  > O UserType não tem nenhuma relação à forma como o utilizador inicia sessão, a função de diretório do utilizador e assim sucessivamente. Basta esta propriedade indica a relação do utilizador na organização de anfitrião e permite que a organização impor políticas que dependem desta propriedade.

### <a name="source"></a>Origem
Esta propriedade indica como o utilizador inicia sessão.

- Convidou utilizador: Este utilizador tem foi convidado, mas ainda não tem resgatadas um convite.

- Externo do Active Directory: Este utilizador é duplamente, numa organização externa e autentica utilizando uma conta do Azure AD que pertence a outra organização. Este tipo de início de sessão corresponde a estado 1.

- Conta Microsoft: este utilizador é duplamente, o numa conta Microsoft e autentica utilizando uma conta Microsoft. Este tipo de início de sessão corresponde a estado 2.

- Windows Server Active Directory: Este utilizador tem sessão iniciado partir do Active Directory no local e que pertença a esta organização. Este tipo de início de sessão corresponde a Estado 3.

- Azure Active Directory: Este utilizador é autenticado utilizando uma conta do Azure AD que pertence a esta organização. Este tipo de início de sessão corresponde a 4 de estado.
  > [!NOTE]
  > Origem e UserType são propriedades independentes. Um valor de origem não implica um valor específico para UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Os utilizadores do Azure AD B2B podem ser adicionados como membros em vez dos convidados?
Normalmente, um utilizador do Azure AD B2B e o utilizador convidado são synonymous. Por conseguinte, um utilizador de colaboração B2B do Azure AD é adicionado como um utilizador com UserType = convidado por predefinição. No entanto, em alguns casos, a organização do parceiro é um membro de uma maior organização a que a organização de anfitrião também pertence. Se assim for, a organização de anfitrião poderá querer tratar os utilizadores na organização de parceiro como membros em vez de convidados. Utilize as APIs do Azure AD B2B convite Manager para adicionar ou convidar um utilizador da organização de parceiro na organização de anfitrião, como um membro.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtro de utilizadores convidados no diretório

![Filtrar utilizadores convidados](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Converter UserType
Atualmente, é possível que os utilizadores converter UserType de membro para o convidado e vice-versa utilizando o PowerShell. No entanto, a propriedade UserType deveria para representar a relação do utilizador na organização. Por conseguinte, o valor desta propriedade deve alterar apenas se a relação do utilizador para a organização for alterada. Se alterar a relação do utilizador, devem problemas, como se deve alterar o nome principal de utilizador (UPN), ser resolvidos? O utilizador deve continuar a ter acesso aos mesmos recursos? Deve ser atribuída uma caixa de correio? Por conseguinte, recomendamos que altere a UserType utilizando o PowerShell como uma atividade atomic. Além disso, no caso desta propriedade fica imutável utilizando o PowerShell, não recomendamos tendo uma dependência neste valor.

## <a name="remove-guest-user-limitations"></a>Remova as limitações de utilizador convidado
Pode haver casos em que pretende conceder aos seus utilizadores de convidado privilégios mais elevados. Pode adicionar um utilizador convidado para qualquer função e até remover as restrições de utilizador convidado predefinição no diretório para dar os privilégios mesmos como membros de um utilizador.

É possível desativar as limitações de utilizador convidado predefinido para que um utilizador convidado no diretório da empresa é dado as mesmas permissões como utilizador do membro.

![Remova as limitações de utilizador convidado](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [A adição de um utilizador de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites para colaboração do B2B](active-directory-b2b-delegate-invitations.md)
* [Utilizador de colaboração do B2B auditoria e de relatórios](active-directory-b2b-auditing-and-reporting.md)
* [Grupos dinâmicos e de colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração do B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicações SaaS colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de utilizador de colaboração B2B](active-directory-b2b-user-token.md)
* [Afirmações de utilizador de colaboração B2B mapeamento](active-directory-b2b-claims-mapping.md)
* [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais de colaboração B2B](active-directory-b2b-current-limitations.md)
