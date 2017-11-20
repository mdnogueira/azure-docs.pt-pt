---
title: "Azure AD SSPR no ecrã de início de sessão do Windows 10 | Microsoft Docs"
description: "Configurar a reposição de palavras-passe do Azure AD no ecrã de início de sessão do Windows 10 e esqueci-me do meu PIN"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 9f7fdb97fd121eecf9e7b2f4edc1b568c8114869
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Reposição de palavras-passe do Azure AD no ecrã de início de sessão

Já implementou a reposição de palavras-passe self-service (SSPR) do Azure AD, mas se se esquecerem das palavras-passe, os seus utilizadores podem continuar a entrar em contacto com o suporte técnico. Entram em contacto com o suporte técnico porque não conseguem abrir um browser para aceder à SSPR.

Com a nova Fall Creators Update do Windows 10, os utilizadores que tenham dispositivos associados ao Azure AD podem ver a ligação “Repor palavra-passe” no ecrã de início de sessão. Quando clicam nesta ligação, são encaminhados para a experiência de reposição de palavras-passe self-service (SSPR) que já conhecem. 

Para que os utilizadores possam repor as palavras-passe do Azure AD a partir do ecrã de início de sessão do Windows 10, têm de ser satisfeitos os requisitos seguintes:

* Windows 10, versão 1709 ou cliente mais recente que esteja associado ao Domínio do Azure AD.
* A reposição de palavras-passe self-service do Azure AD tem de estar ativada.
* Configurar e implementar a definição para ativar a ligação Repor palavra-passe através de um dos métodos abaixo:
   * [Perfil de configuração de dispositivo no Intune](active-directory-passwords-login.md#configure-reset-password-link-using-intune)
   * [Chave de registo](active-directory-passwords-login.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Configurar a ligação Repor palavra-passe com o Intune

### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração de dispositivos no Intune

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Aceda a **Configuração de dispositivos** > **Perfis** > **Criar Perfil** para criar um perfil de configuração de dispositivos novo
   * Dê um nome relevante ao perfil
   * Opcionalmente, indique uma descrição relevante do perfil
   * Plataforma **Windows 10 e posterior**
   * Tipo de perfil **Personalizado**

   ![CreateProfile][CreateProfile]

3. Configure as **Definições**
   * **Adicione** a Definição de OMA-URI seguinte para ativar a ligação Repor palavra-passe
      * Indique um nome relevante para explicar o que a definição faz
      * Opcionalmente, indique uma descrição relevante da definição
      * **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **Tipo de dados** definido como **Inteiro**
      * **Valor** definido como **1**
      * Clique em **OK**
   * Clique em **OK**
4. Clique em **Criar**.

### <a name="assign-a-device-configuration-policy-in-intune"></a>Atribuir uma política de configuração de dispositivos no Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Criar um grupo ao qual aplicar a política de configuração de dispositivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Azure Active Directory**
2. Navegue até **Utilizadores e grupos** > **Todos os grupos** > **Novo grupo**
3. Indique um nome para o grupo e, em **tipo de associação**, escolha **Atribuído** 
   * Em **Membros**, escolha os dispositivos Windows 10 associados ao Azure AD aos quais pretende aplicar a política
   * Clique em **Selecionar**
4. Clique em **Criar**.

Estão disponíveis mais informações sobre a criação de grupos no artigo [Manage access to resources with Azure Active Directory groups](active-directory-manage-groups.md) (Gerir o acesso a recursos com grupos do Azure Active Directory).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Atribuir a política de configuração de dispositivos ao grupo de dispositivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Aceda a **Configuração de dispositivos** > **Perfis** > clique no perfil criado anteriormente para localizar o perfil de configuração de dispositivos que criámos acima.
3. Atribuir o perfil a um grupo de dispositivos 
   * Clique em **Atribuições** > em **Incluir** > **Selecionar grupos a incluir**
   * Selecione o grupo criado anteriormente e clique em **Selecionar**
   * Clique em **Guardar**

   ![Atribuição][Assignment]

Criou e atribuiu agora uma política de configuração de dispositivos para ativar a ligação Repor palavra-passe no ecrã de início de sessão com o Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configurar a ligação Repor palavra-passe com o registo

Recomendamos que utilize este método apenas para testar a alteração da definição.

1. Utilize credenciais administrativas para iniciar sessão no dispositivo associado ao Domínio do Azure AD
2. Execute **regedit** como administrador
3. Defina a chave de registo seguinte
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>O que os utilizadores veem

Agora que a política está configurada e atribuída, que alterações há para o utilizador? Como é que podem saber que é permitido repor a palavra-passe no ecrã de início de sessão?

![LoginScreen][LoginScreen]

Quando os utilizadores tentam iniciar sessão, veem agora a ligação Repor palavra-passe, a qual abre a experiência de reposição de palavra-passe self-service no ecrã de início de sessão. Com esta funcionalidade, os utilizadores podem repor as palavras-passe sem terem de utilizar outro dispositivo para aceder a um browser.

Os seus utilizadores podem obter orientações sobre como utilizar esta funcionalidade em [Reset your work or school password](active-directory-passwords-update-your-own-password.md#reset-password-at-login) (Repor a palavra-passe da conta escolar ou profissional)

## <a name="common-issues"></a>Problemas comuns

Ao testar esta funcionalidade com o Hyper-V, a ligação "Repor palavra-passe" não aparece.

* Aceda à VM com a qual está a testar, clique em **Ver** e desmarque **Sessão avançada**.

Ao testar esta funcionalidade com o Ambiente de Trabalho Remoto, a ligação "Repor palavra-passe" não aparece.

* A reposição de palavras-passe não é suportada em Ambientes de Trabalho Remotos atualmente.

## <a name="next-steps"></a>Passos seguintes
As ligações seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe com o Azure AD

* [Como implemento a SSPR?](active-directory-passwords-best-practices.md)
* [Como ativo a reposição do PIN a partir do ecrã de início de sessão?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [More information about MDM authentication policies](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication) (Mais informações sobre as políticas de autenticação de MDM)

[CreateProfile]: ./media/active-directory-passwords-login/create-profile.png "Criar perfil de configuração de dispositivos do Intune para ativar a ligação Repor palavra-passe no ecrã de início de sessão do Windows 10"
[Assignment]: ./media/active-directory-passwords-login/profile-assignment.png "Atribuir a política de configuração de dispositivos do Intune a um grupo de dispositivos Windows 10"
[LoginScreen]: ./media/active-directory-passwords-login/logon-reset-password.png "Ligação de Repor palavra-passe no ecrã de início de sessão do Windows 10"
