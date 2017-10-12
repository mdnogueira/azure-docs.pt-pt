---
title: "Federar vários Azure AD com AD FS único | Microsoft Docs"
description: "Com este documento irá aprender a federar vários Azure AD com um único do AD FS."
keywords: "federar, ADFS, AD FS, vários inquilinos, AD FS único, um ADFS, federação multi- inquilino, adfs multi-florestas, ligação aad, federação, federação entre inquilinos"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.openlocfilehash: 436bf5905d2b203dc4cceea97f4fb90593df7111
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#<a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Federar várias instâncias do Azure AD com uma instância única do AD FS

Um único farm do AD FS altamente disponível pode federar várias florestas se tiverem duas vias confiança entre eles. Estas várias florestas podem, ou não, corresponder ao mesmo Azure Active Directory. Este artigo fornece instruções sobre como configurar a federação entre uma implementação do AD FS única e mais do que uma floresta que sincroniza com Azure AD diferentes.

![Federação multi-inquilino com o AD FS único](media/active-directory-aadconnectfed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> A repetição de escrita do dispositivo e a associação automática do dispositivo não são suportadas neste cenário.

> [!NOTE]
> O Azure AD Connect não pode ser utilizado para configurar a federação neste cenário, uma vez que o Azure AD Connect pode configurar a federação para domínios num único Azure AD.

##<a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Passos para a federação do AD FS com vários Azure AD

Considere que um domínio contoso.com no contoso.onmicrosoft.com do Azure Active Directory já está federado com o AD FS no local instalado no ambiente do Active Directory no local contoso.com. Fabrikam.com é um domínio no Azure Active Directory do .fabrikam.onmicrosoft.com.

##<a name="step-1-establish-a-two-way-trust"></a>Passo 1: Estabelecer uma confiança bidirecional
 
Para o AD FS no contoso.com conseguir autenticar utilizadores no fabrikam.com, é necessária uma confiança bidirecional entre o contoso.com e o fabrikam.com. Siga a documentação de orientação neste [artigo](https://technet.microsoft.com/library/cc816590.aspx) para criar a confiança bidirecional.
 
##<a name="step-2-modify-contosocom-federation-settings"></a>Passo 2: Modificar as definições de federação do contoso.com 
 
O emissor predefinido num único domínio federado para o AD FS é "http://ADFSServiceFQDN/adfs/services/trust", por exemplo, "http://fs.contoso.com/adfs/services/trust". O Azure Active Directory requer um emissor exclusivo para cada domínio federado. Uma vez que o mesmo AD FS vai federar dois domínios, o valor do emissor tem de ser modificado para que seja exclusivo para cada domínio que o AD FS federa com o Azure Active Directory. 
 
No servidor do AD FS, abra o Azure AD PowerShell e execute os seguintes passos:
 
Ligue ao Azure Active Directory que contém o domínio contoso.com Connect-MsolService. Atualize as definições de federação para contoso.com Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain
 
O emissor na definição de federação do domínio será alterado para "http://contoso.com/adfs/services/trust" e uma regra de afirmação de emissão será adicionada à Entidade Confiadora do Azure AD, para emitir o valor correto do issuerId baseado no sufixo de UPN.
 
##<a name="step-3-federate-fabrikamcom-with-ad-fs"></a>Passo 3: Federar o fabrikam.com com o AD FS
 
No sessão de powershell do Azure AD execute os seguintes passos: ligar ao Azure Active Directory que contém o domínio fabrikam.com

    Connect-MsolService
Converta o domínio gerido fabrikam.com para federado:

    Convert-MsolDomainToFederated -DomainName anandmsft.com -Verbose -SupportMultipleDomain
 
A operação acima irá federar o domínio fabrikam.com com o mesmo AD FS. Pode verificar as definições de domínio com o Get-MsolDomainFederationSettings para ambos os domínios.

## <a name="next-steps"></a>Passos seguintes
[Ligar o Active Directory ao Azure Active Directory](active-directory-aadconnect.md)
