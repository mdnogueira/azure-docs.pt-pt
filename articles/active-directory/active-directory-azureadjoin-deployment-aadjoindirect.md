---
title: "Cenários de utilização e considerações de implementação para associação do Azure AD | Microsoft Docs"
description: "Explica como os administradores podem configurar associação do Azure AD para os respetivos utilizadores finais (empregados, estudantes, outros utilizadores). Também descreve os cenários no mundo real diferentes para utilizar a associação do Azure AD."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: fd0aab1a14bbd324e734e5efe8fe101e8a8dfefa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Cenários de utilização e considerações de implementação para associação do Azure AD
## <a name="usage-scenarios-for-azure-ad-join"></a>Cenários de utilização de associação do Azure AD
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Cenário 1: Empresas grande parte na nuvem
Azure associação do Active Directory (associação do Azure AD) podem beneficiar-se atualmente operar e gerir identidades para a sua empresa na nuvem ou estiver a mover para a nuvem em breve. Pode utilizar uma conta que tenha criado no Azure AD para iniciar sessão no Windows 10. Através de [processo (FRX) de experiência de primeira execução](active-directory-azureadjoin-user-frx.md), ou por associação do Azure AD de [menu definições](active-directory-azureadjoin-user-upgrade.md), os utilizadores podem associar as respetivas máquinas para o Azure AD.  Os utilizadores também possam desfrutar único início de sessão (SSO) para recursos de nuvem, como o Office 365, nos seus browsers ou nas aplicações do Office.

### <a name="scenario-2-educational-institutions"></a>Cenário 2: Instituições Educational
Normalmente, instituições Educational têm dois tipos de utilizador: corpo docente e alunos. Os membros do corpo docente são considerados duração mais longa membros da organização. A criação de contas no local para os mesmos é desejável. Mas estudantes são shorter-term membros da organização e das respetivas contas podem ser geridas no Azure AD. Isto significa que a escala de diretório pode ser enviada para a nuvem em vez de ser armazenada no local. Também significa que os estudantes que estejam conseguirá iniciar sessão no Windows com as respetivas contas do Azure AD e obter acesso aos recursos do Office 365 em aplicações do Office.

### <a name="scenario-3-retail-businesses"></a>Cenário 3: As empresas de revenda
As empresas de revenda tem trabalhadores sazonais e funcionários de longo prazo. Normalmente, criar contas no local e utilizar máquinas associados a um domínio para empregados a tempo inteiro duração mais longa. Mas trabalhadores sazonais são shorter-term membros da organização e é necessário para gerir as respetivas contas onde licenças de utilizador podem ser mais facilmente movidas à volta. Quando criar as contas de utilizador na nuvem com o Office 365 licenças, estes utilizadores obtêm as vantagens de iniciar sessão no Windows e o Office aplicações com uma conta do Azure AD, enquanto mantém uma maior flexibilidade com as respetivas licenças depois de serem saírem.

### <a name="scenario-4-additional-scenarios"></a>Cenário 4: Cenários adicionais
Juntamente com as vantagens abordadas anteriormente, poderá beneficiar de ter os seus utilizadores a adesão dos respetivos dispositivos para o Azure AD devido a uma experiência associar simplificada, gestão de dispositivos eficiente, inscrição da gestão de dispositivos móveis automática e início de sessão único para o Azure AD e recursos no local.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Considerações de implementação para associação do Azure AD
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Permitir que os utilizadores associar um dispositivo da empresa diretamente ao Azure AD
As empresas podem fornecer contas apenas na nuvem para empresas e organizações. Destes parceiros podem, em seguida, aceder facilmente às aplicações da empresa e de recursos com o início de sessão único. Este cenário é aplicável a utilizadores que acedem a recursos principalmente na nuvem, como aplicações do Office 365 ou SaaS que dependem do Azure AD para autenticação.

### <a name="prerequisites"></a>Pré-requisitos
**A nível empresarial (administrador)**

* Subscrição do Azure com o Azure Active Directory  

**Ao nível do utilizador**

* Windows 10 (edições Professional e Enterprise)

### <a name="administrator-tasks"></a>Tarefas de administrador
* [Configurar o registo do dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tarefas de utilizador
* [Configurar um novo dispositivo de Windows 10 com o Azure AD durante a configuração](active-directory-azureadjoin-user-frx.md)
* [Configurar um dispositivo Windows 10 com o Azure AD no menu de definições](active-directory-azureadjoin-user-upgrade.md)
* [Associar um dispositivo Windows 10 pessoal à sua organização](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Ativar o BYOD na sua organização para o Windows 10
Pode configurar os utilizadores e os funcionários utilizar os respetivos dispositivos Windows pessoais (BYOD) para aceder a aplicações da empresa e recursos. Os utilizadores podem adicionar as contas do Azure AD (contas profissionais ou escolares) para um dispositivo pessoal do Windows para aceder a recursos de forma segura e compatível.

### <a name="prerequisites"></a>Pré-requisitos
**A nível empresarial (administrador)**

* Subscrição do Azure AD

**Ao nível do utilizador**

* Windows 10 (edições Professional e Enterprise)

### <a name="administrator-tasks"></a>Tarefas de administrador
* [Configurar o registo do dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tarefas de utilizador
* [Associar um dispositivo Windows 10 pessoal à sua organização](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar os dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Aumentar as capacidades de nuvem para dispositivos com Windows 10 através da Associação do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticar identidades sem palavras-passe através do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre os cenários de utilização da Associação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar a dispositivos associados a um domínio ao Azure AD para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Associação do Azure AD](active-directory-azureadjoin-setup.md)

