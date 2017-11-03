---
title: "Configurar a integração do LinkedIn no Azure AD | Microsoft Docs"
description: "Explica como ativar ou desativar a integração do LinkedIn para aplicações da Microsoft no Azure Active Directory."
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Ativar a integração do LinkedIn no Azure Active Directory
Ativar a integração do LinkedIn permite aos utilizadores aceder a ambos os dados de LinkedIn públicos e, se escolherem, suas redes LinkedIn pessoais a partir aplicações da Microsoft. Cada utilizador pode escolher independentemente ligar a respetiva conta profissional a respetiva conta LinkedIn.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>Integração do LinkedIn da perspetiva dos seus utilizadores finais
Quando os utilizadores finais na sua organização ligar as respetivas contas LinkedIn às suas contas de trabalho, são capazes de identificar mais facilmente as pessoas que funcionam com dentro e fora da organização. Ligar as duas contas permite ligações LinkedIn e dos dados de perfil para ser utilizado em aplicações da Microsoft da sua organização do utilizador, mas estes podem sempre ativamente por não participar ao remover a permissão para LinkedIn partilhar os dados. A integração também utiliza as informações do perfil publicamente disponíveis e os utilizadores possam optar por partilhar as suas informações de perfil e a rede públicas com aplicações da Microsoft através das definições de privacidade do LinkedIn.

>[!NOTE]
> Ativar a integração do LinkedIn no Azure AD permite que aplicações e serviços para aceder a algumas das informações dos seus utilizadores finais. Leia o [declaração de privacidade do Microsoft](https://privacy.microsoft.com/privacystatement/) para saber mais sobre as considerações de privacidade ao ativar a integração do LinkedIn no Azure AD. 

## <a name="enable-linkedin-integration"></a>Ativar a integração do LinkedIn
Integração de LinkedIn para empresas está ativada por predefinição no Azure AD. Por isso, quando esta funcionalidade é disponibilizada para o seu inquilino, podem ver todos os utilizadores na sua organização LinkedIn funcionalidades e perfis. Ativar a integração do LinkedIn permite aos utilizadores na sua organização utilizar funcionalidades de LinkedIn dentro de serviços Microsoft, como perfis de visualização ao receber uma mensagem de e-mail no Outlook. Desativar esta funcionalidade impede o acesso às funcionalidades de LinkedIn e deixa de ligações de conta de utilizador e a partilha de dados entre LinkedIn e a sua organização através de serviços Microsoft.

> [!IMPORTANT]
> Esta funcionalidade não está disponível para aceda local, sovereign e inquilinos government. Além disso, do Azure AD atualizações de serviço, como o LinkedIn capacidades de integração, não são implementadas para todos os inquilinos do Azure ao mesmo tempo. Não é possível ativar a integração de LinkedIn com o Azure AD até que esta capacidade foi revertida para o seu inquilino do Azure.

1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do diretório.
2. Selecione **utilizadores e grupos**.
3. No **utilizadores e grupos** painel, selecione **as definições de utilizador**.
4. Em **LinkedIn integração**, selecione Sim ou não para ativar ou desativar a integração do LinkedIn.
   ![Ativar a integração do LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>Saiba mais 
* [Informações de LinkedIn e funcionalidades nas suas aplicações da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Passos seguintes
Pode utilizar a hiperligação seguinte para ativar ou desativar LinkedIn integração com o Azure AD a partir do portal do Azure:

[Configurar a integração do LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 