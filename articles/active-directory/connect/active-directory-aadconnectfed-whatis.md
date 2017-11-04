---
title: "Azure AD Connect e a Federação | Microsoft Docs"
description: "Esta página é a localização central para toda a documentação relativas à operações do AD FS que utilizam o Azure AD Connect."
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: anandy
ms.openlocfilehash: 6dc5ef81704423c5a1bc136fb2d51d79a9265e20
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e a federação
Azure Connect do Active Directory (Azure AD) permite ao configurar a Federação com o local Serviços de Federação do Active Directory (AD FS) e o Azure AD. Com Federação início de sessão, pode permitir aos utilizadores iniciar sessão para serviços do Azure baseada no AD com as palavras-passe no local – e, enquanto na rede empresarial, sem ter de introduzir as palavras-passe novamente. Ao utilizar a opção de federação com o AD FS, pode implementar uma nova instalação do AD FS ou pode especificar uma instalação existente num farm do Windows Server 2012 R2.

Este tópico é a base para obter informações sobre as funcionalidades relacionadas com a Federação para o Azure AD Connect. Lista ligações para todos os tópicos relacionados. Para ligações ao Azure AD Connect, consulte [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Do Azure AD Connect: tópicos de Federação
| Tópico | O que abrange e quando lê-lo |
|:--- |:--- |
| **Opções do Azure AD Connect utilizador iniciar sessão** | |
| [Compreender as opções de início de sessão do utilizador](active-directory-aadconnect-user-signin.md) |Saiba mais sobre as várias opções de início de sessão do utilizador e como estas afetam a experiência de utilizador de início de sessão do Azure. |
| **Instalar o AD FS utilizando o Azure AD Connect** | |
| [Pré-requisitos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Consulte os pré-requisitos para uma instalação do AD FS com êxito através do Azure AD Connect. |
| [Configurar um farm do AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Instale um novo farm do AD FS utilizando o Azure AD Connect. |
| [Federar com o Azure AD utilizando o ID de início de sessão alternativo](active-directory-aadconnect-federation-management.md#alternateid) | Configurar a Federação com o ID de início de sessão alternativo  |
| **Modificar a configuração do AD FS** | |
| [Reparar a confiança](active-directory-aadconnect-federation-management.md#repairthetrust) |Reparar a confiança atual no local do AD FS e o Office 365/Azure. |
| [Adicionar um novo servidor do AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Expanda um farm do AD FS com um servidor do AD FS adicional após a instalação inicial. |
| [Adicionar um novo servidor do WAP do AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Expanda um farm do AD FS com um servidor de Proxy de aplicações Web (WAP) adicionais após a instalação inicial. |
| [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#addfeddomain) |Adicione outro domínio Federado com o Azure AD. |
| [Atualizar o certificado SSL](active-directory-aadconnectfed-ssl-update.md)| Atualize o certificado SSL para um farm do AD FS. |
| [Renovar os certificados de Federação para o Office 365 e o Azure AD](active-directory-aadconnect-o365-certs.md)|Renove o certificado do O365 com o Azure AD.|
| **Outra configuração de Federação** | |
| [Federar várias instâncias do Azure AD com uma instância única do AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Federar vários do Azure AD com única farm do AD FS| 
| [Adicionar uma ilustração/logótipo de empresa personalizado](active-directory-aadconnect-federation-management.md#customlogo) |Modificar a experiência de início de sessão, especificando o logótipo personalizado que é apresentado na página de início de sessão do AD FS. |
| [Adicione uma descrição de início de sessão](active-directory-aadconnect-federation-management.md#addsignindescription) |Altere a descrição do início de sessão na página de início de sessão do AD FS. |
| [Modificar as regras de afirmações do AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modificar ou adicione regras de afirmação no AD FS que correspondem à configuração da sincronização do Azure AD Connect. |


## <a name="additional-resources"></a>Recursos adicionais
* [Federação duas do Azure AD com o único do AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Implementação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Implementação do elevada disponibilidade em vários locais geográfica AD FS no Azure com o Gestor de tráfego do Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
