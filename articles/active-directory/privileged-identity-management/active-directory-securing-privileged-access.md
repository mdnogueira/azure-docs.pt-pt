---
title: Proteger o acesso privilegiado no Azure AD | Microsoft Docs
description: "Um tópico que explica as abordagens para proteger o acesso privilegiado através do Azure, o Azure Active Directory e o Microsoft Online Services."
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: 278aa67013eb2cabcf5efa7e0de21e9cff0519ba
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="securing-privileged-access-in-azure-ad"></a>Proteger o acesso privilegiado no Azure AD
Proteger o acesso privilegiado é o primeiro passo crítico para ajudar a proteger os ativos de negócio numa organização moderna. Contas com privilégios são contas que administre e gira os sistemas de TI. Os atacantes de informático estas contas para obter acesso aos dados de uma organização e sistemas de destino. Para proteger o acesso privilegiado, deve isolar as contas e os sistemas do risco de que está a ser expostos a um utilizador mal intencionado.

Mais utilizadores estão a começar a obter acesso privilegiado através dos serviços de nuvem. Isto pode incluir os administradores globais do Office 365, os administradores da subscrição do Azure e os utilizadores que têm acesso administrativo nas VMs ou nas aplicações SaaS.

A Microsoft recomenda que siga este plano no [proteger o acesso privilegiado](https://technet.microsoft.com/library/mt631194.aspx).

Para clientes que utilizam o Azure Active Directory, Office 365, ou outros serviços e aplicações Microsoft, estes princípios aplicam-se se as contas de utilizador são geridas e autenticadas pelo Active Directory ou no Azure Active Directory. As secções seguintes fornecem mais informações sobre funcionalidades do Azure AD para suportar a proteger o acesso privilegiado.

## <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure
Para aumentar a segurança de autenticação de administrador, deve solicitar a verificação de dois passos antes de conceder privilégios. Verificação de dois passos é um método de verificar que é que requer a utilização de mais do que apenas um nome de utilizador e palavra-passe. Fornece uma segunda camada de segurança para inícios de sessão de utilizador e de transações.

Azure multi-factor Authentication (MFA) é uma solução de verificação de dois passos da Microsoft, que ajuda a salvaguardar o acesso a dados e aplicações cumprindo utilizador precisar de um processo de início de sessão simple. Fornece autenticação forte através de uma variedade de opções de verificação fácil, incluindo:

- chamadas telefónicas
- Mensagens de texto
- notificações da aplicação móvel
- códigos de verificação da aplicação móvel
- tokens OATH de terceiros

Para obter uma descrição geral de como funciona a multi-factor Authentication do Azure, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

Para obter mais informações, consulte [MFA para Office 365 e o MFA do Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilégios de vínculo de tempo
Algumas organizações, podem constatar que têm demasiados utilizadores em funções com privilégios elevados. Um utilizador pode ter sido adicionado para a função para uma determinada atividade, como inscrever-se para um serviço, mas não utiliza esses privilégios frequentemente posteriormente.

Para reduzir o tempo de exposição de privilégios e aumentar a visibilidade para a sua utilização, limitar os utilizadores apenas colocar nos seus privilégios "just in time" (JIT), ou atribuir estas funções durante um período reduzido com confiança privilégios serão revogados automaticamente. Para o Azure Active Directory, recursos do Azure (pré-visualização) e Serviços Online da Microsoft, pode utilizar [do Azure AD Privileged Identity Management (PIM)](http://aka.ms/AzurePIM).

![Dashboard do PIM][2]

## <a name="attack-detection"></a>Deteção de ataques
[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) fornece uma vista consolidada sobre eventos de risco e potenciais vulnerabilidades que afetam as identidades da organização. Com base em eventos de risco, Identity Protection calcula um nível de risco de utilizador para cada utilizador, permitindo-lhe configurar as políticas baseadas em risco para proteger automaticamente as identidades da sua organização. Estas políticas, juntamente com outros controlos de acesso condicional fornecidas pelo Azure Active Directory e o EMS, podem automaticamente a bloquear o utilizador ou a oferecer sugestões que incluem reposições de palavra-passe e a imposição de autenticação multifator.

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>Acesso condicional
Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas, que escolha quando autenticar um utilizador, antes de permitir o acesso a uma aplicação. Depois destas condições são cumpridas, o utilizador é autenticado e permissão de acesso à aplicação.

## <a name="related-articles"></a>Artigos relacionados
* Ativar [multi-factor Authentication do Azure](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Ativar [do Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* Ativar [do Azure AD Identity Protection](../active-directory-identityprotection.md)
* Ativar [controlos de acesso condicional](../active-directory-conditional-access.md)

Para obter mais informações sobre como criar um plano de segurança completa, consulte a secção "responsabilidades do cliente e plano" o [Microsoft Cloud Security para o Enterprise Arquitetos](http://aka.ms/securecustomer) documento. Para obter mais informações no envolvimento dos serviços da Microsoft para melhorar a qualquer um destes tópicos, contacte o seu representante da Microsoft ou visite a nossa [página de soluções atuais](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
