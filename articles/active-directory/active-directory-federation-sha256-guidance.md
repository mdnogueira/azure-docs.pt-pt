---
title: "Algoritmo de hash de assinatura de alteração para a confiança da entidade confiadora do Office 365 | Microsoft Docs"
description: "Esta página fornece diretrizes para alterar o algoritmo SHA de fidedignidade de federação com o Office 365"
keywords: "SHA1, SHA256, Office 365, Federação aadconnect, adfs, do ad fs, sha de alteração, fidedignidade de Federação, a confiança da entidade confiadora"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: samueld
editor: 
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: c581b1468630a9f28204592c936360b72f42f0d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Altere o algoritmo de hash de assinatura para o Office 365 confiança da entidade confiadora
## <a name="overview"></a>Descrição geral
Serviços de Federação do Active Directory (AD FS) assina os tokens para o Microsoft Azure Active Directory para garantir que estes não podem ser adulterados. Esta assinatura pode basear-se no SHA1 ou SHA256. Azure Active Directory suporta agora tokens assinados com um algoritmo SHA256 e, recomendamos que defina o algoritmo de assinatura de tokens para SHA256 para o nível mais elevado de segurança. Este artigo descreve os passos necessários para definir o algoritmo de assinatura de tokens para o mais seguro SHA256 nível.

>[!NOTE]
>A Microsoft recomenda a utilização de SHA256 como o algoritmo de assinatura de tokens, é mais segura do que SHA1 mas SHA1 permanece uma opção suportada.

## <a name="change-the-token-signing-algorithm"></a>Altere o algoritmo de assinatura de tokens
Depois de ter de definir o algoritmo de assinatura com um dos dois processos abaixo, o AD FS inicia os tokens para o Office 365 com SHA256 confiança de entidade confiadora. Não precisa de efetuar quaisquer alterações de configuração adicional e esta alteração não tem impacto sobre a capacidade de aceder ao Office 365 ou outras aplicações do Azure AD.

### <a name="ad-fs-management-console"></a>Consola de gestão do AD FS
1. Abra a consola de gestão do AD FS no servidor primário do AD FS.
2. Expanda o nó do AD FS e clique em **confianças da entidade Confiadora**.
3. A Office 365/Azure confiança de entidade confiadora com o botão direito e selecione **propriedades**.
4. Selecione o **avançadas** separador e selecionar o algoritmo de hash seguro SHA256.
5. Clique em **OK**.

![Algoritmo de assinatura SHA256 – MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets do AD FS PowerShell
1. Em qualquer servidor do AD FS, abra o PowerShell com privilégios de administrador.
2. Definir o algoritmo hash seguro utilizando o **Set-AdfsRelyingPartyTrust** cmdlet.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Leia também
* [Reparar a confiança do Office 365 com o Azure AD Connect](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

