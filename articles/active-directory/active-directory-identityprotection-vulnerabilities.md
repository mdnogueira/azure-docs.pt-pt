---
title: Vulnerabilidades detetadas pelo Azure Active Directory Identity Protection | Microsoft Docs
description: "Descrição geral de vulnerabilidades detetadas pelo Azure Active Directory Identity Protection."
services: active-directory
keywords: "proteção de identidade do Azure Active Directory, o cloud app discovery, gestão de aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: dfb75c91d66d61a63e3f6c3e431a9a45465cbf5c
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detetadas pelo Azure Active Directory Identity Protection
Vulnerabilidades são fragilidades no seu ambiente que podem ser forem exploradas por um atacante. Recomendamos que resolver estas vulnerabilidades para melhorar a postura de segurança da sua organização e impedir que os atacantes a explorá-los.


![vulnerabilidades](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilidades")



As secções seguintes fornecem-lhe uma descrição geral de vulnerabilidades comunicado pelo Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registo de autenticação multifator não configurado
Este vulnerabilidade ajuda-o a controlar a implementação de multi-factor Authentication do Azure na sua organização. 

Azure multi-factor authentication fornece uma segunda camada de segurança para autenticação de utilizador. -Ajuda a salvaguardar o acesso a dados e aplicações cumprindo o pedido do utilizador para um processo de início de sessão simple. Fornece autenticação forte através de uma gama de opções de verificação fácil — chamada telefónica, mensagem de texto ou aplicação móvel notificação ou da verificação de código e 3rd terceiros tokens OATH.

Recomendamos que necessitam de Azure multi-factor Authentication para inícios de sessão do utilizador. Autenticação multifator desempenha uma função de chave nas políticas de acesso condicional baseado em risco disponíveis através da proteção de identidade.

Para obter mais detalhes, consulte [que é o Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Aplicações na nuvem não gerido
Este vulnerabilidade ajuda-o a identificar aplicações na nuvem não geridos na sua organização.

As empresas modernas, departamentos de TI são frequentemente não tem conhecimento de todas as aplicações em nuvem que os utilizadores na sua organização estiver a utilizar para trabalhar. É fácil ver a razão pela qual os administradores teria preocupações acesso não autorizado a dados empresariais, fugas de dados e outros riscos de segurança. 

Recomendamos que a sua organização implementar Cloud App Discovery para detetar aplicações na nuvem não geridos e para gerir estas aplicações com o Azure Active Directory.

Para obter mais detalhes, consulte [localizar aplicações da nuvem não geridos com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de segurança de Privileged Identity Management
Este vulnerabilidade ajuda a detetar e resolver alertas sobre as identidades privilegiadas na sua organização.  

Para permitir aos utilizadores realizar operações privilegiadas, as organizações precisam de conceder acesso privilegiado utilizadores permanentes ou temporárias no Azure AD, os recursos do Azure ou do Office 365 ou outras aplicações SaaS. Cada um destes utilizadores privilegiados aumenta a superfície de ataque da sua organização. Esta vulnerabilidade ajuda a identificar os utilizadores com acesso privilegiado desnecessário e tome as medidas necessárias para reduzir ou eliminar o risco que apresentam. 

Recomendamos que a organização utilizar o Azure AD Privileged Identity Management para gerir, controlar e monitorizar privilegiado identidades e o respetivo acesso a recursos no Azure AD, bem como outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Para obter mais detalhes, consulte [do Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="see-also"></a>Consultar também
* [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)

