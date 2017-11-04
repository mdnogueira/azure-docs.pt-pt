---
title: "Atribuir funções de administrador no Azure Active Directory | Microsoft Docs"
description: "Uma função de administrador pode criar ou editar utilizadores, atribuir funções administrativas a outras pessoas, repor palavras-passe do utilizador, gerir licenças de utilizador ou gerir os domínios. Um utilizador a quem é atribuído uma função de administrador tem as mesmas permissões em todos os serviços de nuvem para o qual a sua organização tiver subscrito."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: curtand
ms.reviewer: Vince.Smith
ms.custom: it-pro;
ms.openlocfilehash: 260462e57c53d2d41bb2bfd8381a1c184303eea1
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Atribuir funções de administrador no Azure Active Directory

Utilizar o Azure Active Directory (Azure AD), pode designar administradores separados para servir diferentes funções. Os administradores têm acesso a várias funcionalidades no portal do Azure ou portal clássico do Azure e, consoante a respetiva função, podem criar ou editar utilizadores, atribuir funções administrativas a outras pessoas, repor palavras-passe do utilizador, gerir licenças de utilizador e gerir os domínios, entre outros coisas. Um utilizador a quem é atribuído uma função de administrador terá as mesmas permissões em todos os serviços de nuvem que a sua organização tem subscritas, independentemente de atribuir a função no portal do Office 365, ou no portal clássico do Azure ou utilizando o módulo do Azure AD para o Windows PowerShell.

Estão disponíveis as seguintes funções de administrador:

* **Administrador de faturação**: efetua compras, gere subscrições, gere pedidos de suporte e monitoriza o estado de funcionamento do serviço.

* **Administrador de compatibilidade**: os utilizadores com esta função têm permissões de gestão dentro no Office 365 segurança & do Centro de conformidade e do Centro de administração do Exchange. Obter mais informações em "[sobre as funções de administrador do Office 365](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)."

* **Administrador de acesso condicional**: os utilizadores com esta função têm a capacidade para gerir as definições de acesso condicional do Azure Active Directory.
  > [!NOTE]
  > Para implementar a política de acesso condicional do Exchange ActiveSync no Azure, o utilizador também tem de ser Administrador Global.
  
* **Administrador de serviço do CRM**: os utilizadores com esta função têm permissões global no Microsoft CRM Online, quando o serviço está presente, bem como a capacidade de gerir pedidos de suporte e monitorizar estado de funcionamento do serviço. Obter mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administradores de dispositivos**: os utilizadores com esta função tornar-se os administradores do computador local em todos os dispositivos Windows 10 que estão associados ao Azure Active Directory. Não têm a capacidade de gerir objetos de dispositivos no Azure Active Directory.

* **Leitores de diretório**: Esta é uma função de legado que está a ser atribuída a aplicações que não suportam o [consentimento Framework](active-directory-integrating-applications.md). Não deve ser atribuído a qualquer utilizador.

* **Contas de sincronização de diretórios**: não utilize. Esta função é atribuída automaticamente ao serviço do Azure AD Connect e não se destina ou não está suportada para quaisquer outras utilizações.

* **Diretório escritores**: Esta é uma função de legado que está a ser atribuída a aplicações que não suportam o [consentimento Framework](active-directory-integrating-applications.md). Não deve ser atribuído a qualquer utilizador.

* **Administrador de serviço do Exchange**: os utilizadores com esta função permissões global dentro do Microsoft Exchange Online, quando o serviço está presente. Obter mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador global / administrador da empresa**: os utilizadores com esta função tem acesso a todas as funcionalidades administrativas no Azure Active Directory, bem como serviços que federar ao Azure Active Directory como o Exchange Online, SharePoint Online e Skype para empresas Online. A pessoa que se inscreve para o inquilino do Azure Active Directory torna-se um administrador global. Apenas os administradores globais podem atribuir outras funções de administrador. Só pode existir mais do que um administrador global da sua empresa. Administradores globais podem repor a palavra-passe para qualquer utilizador e de todos os outros administradores.

  > [!NOTE]
  > Na Microsoft Graph API, AD Graph API do Azure e Azure AD PowerShell, esta função é identificada como "Administrador da empresa". Faz o "Administrador Global" do [portal do Azure](https://portal.azure.com).
  >
  >

* **Convidados Inviter**: os utilizadores nesta função podem gerir convites para de utilizador do Azure Active Directory B2B convidado quando a definição de utilizador "Membros podem convidar" está definida como não. Mais informações sobre a colaboração B2B em [pré-visualização de colaboração sobre o Azure AD B2B](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Não inclui quaisquer outras permissões.

* **Administrador de serviço do Intune**: os utilizadores com esta função permissões global no Microsoft Intune Online, quando o serviço está presente. Além disso, esta função contém a capacidade de gerir utilizadores e dispositivos para associar a política, bem como criar e gerir grupos.

* **Caixa de correio administrador**: esta função é utilizada apenas como parte do suporte de correio eletrónico Exchange Online para dispositivos RIM Blackberry. Se a sua organização não utilizar o e-mail do Exchange Online em dispositivos RIM Blackberry, não utilize esta função.

* **Suporte de 1 de camada de parceiros**: não utilize. Esta função foi despromovida e será removida do Azure AD no futuro. Esta função destina-se por um pequeno número de parceiros de revenda Microsoft e não se destina a utilização geral.

* **Suporte de 2 de nível de parceiros**: não utilize. Esta função foi despromovida e será removida do Azure AD no futuro. Esta função destina-se por um pequeno número de parceiros de revenda Microsoft e não se destina a utilização geral.

* **Palavra-passe de administrador / suporte técnico administrador**: os utilizadores com esta função podem repor palavras-passe, gerir pedidos de serviço e monitorizar estado de funcionamento do serviço. Os administradores de palavras-passe podem repor palavras-passe apenas para os utilizadores e de outros administradores de palavras-passe.

  > [!NOTE]
  > Na Microsoft Graph API, AD Graph API do Azure e Azure AD PowerShell, esta função é identificada como "Administrador de suporte técnico". É "Palavra-passe de administrador" no [portal do Azure](https://portal.azure.com/).
  >
  >
  
* **Administrador de serviço do Power BI**: os utilizadores com esta função têm permissões global dentro do Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerir pedidos de suporte e monitorizar estado de funcionamento do serviço. Obter mais informações em [funções de administrador sobre o Office 365](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Com privilégios de administrador com função**: os utilizadores com esta função podem gerir atribuições de funções no Azure Active Directory, bem como no Azure AD Privileged Identity Management. Além disso, esta função permite a gestão de todos os aspetos de Privileged Identity Management.

* **Administrador de segurança**: os utilizadores com esta função têm todas as permissões só de leitura da função de leitor de segurança com a capacidade de gerir a configuração de serviços relacionados com segurança: Azure Active Directory Identity Protection, Privileged Identity Management, Office 365 segurança & e Centro de conformidade. Estão disponíveis mais informações sobre as permissões do Office 365 no [permissões Centro de conformidade de segurança do Office 365 &](https://support.office.com/en-us/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Leitor de segurança**: os utilizadores com esta função têm acesso só de leitura global, incluindo todas as informações no Azure Active Directory, Identity Protection, Privileged Identity Management, bem como a capacidade para ler relatórios de início de sessão no Azure Active Directory e registos de auditoria. A função também concede a permissão só de leitura no Office 365 segurança & Centro de conformidade. Estão disponíveis mais informações sobre as permissões do Office 365 no [permissões Centro de conformidade de segurança do Office 365 &](https://support.office.com/en-us/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Administrador de suporte do serviço**: os utilizadores com esta função podem abrir os pedidos de suporte com a Microsoft para os serviços do Azure e o Office 365 e vistas de dashboard de serviço e mensagem center no portal do Azure e o portal de administração do Office 365. Obter mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador de serviço do SharePoint**: os utilizadores com esta função têm permissões global no Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de gerir pedidos de suporte e monitorizar estado de funcionamento do serviço. Obter mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Skype para empresas / Lync serviço administrador**: os utilizadores com esta função permissões global no Microsoft Skype para empresas, quando o serviço está presente, bem como gerir os atributos de utilizador do Skype específico no Azure Active Directory. Além disso, esta função concede a capacidade de gerir pedidos de suporte e monitorizar estado de funcionamento do serviço. Obter mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > Na Microsoft Graph API, AD Graph API do Azure e Azure AD PowerShell, esta função é identificada como "Administrador do serviço Lync". É "Skype para empresas administrador de serviço" no [portal do Azure](https://portal.azure.com/).
  >
  >

* **Administrador de conta de utilizador**: os utilizadores com esta função podem criar e gerir todos os aspetos de utilizadores e grupos. Além disso, esta função inclui a capacidade de gerir pedidos de suporte e monitores de estado de funcionamento do serviço. Algumas restrições aplicam-se. Por exemplo, esta função não permite a eliminação de um administrador global e ao permitir a alteração de palavras-passe para não administradores, não permite a alteração de palavras-passe para os administradores globais ou outros administradores com privilégios.

## <a name="administrator-permissions"></a>Permissões de administrador

### <a name="billing-administrator"></a>Administrador de faturação

| Pode fazê-lo | Não é possível efetuar |
| --- | --- |
|<p>Ver informações de utilizador e da empresa</p><p>Gerir pedidos de suporte do Office</p><p>Efetuar operações de faturação e compras para produtos do Office</p> |<p>Repor palavras-passe do utilizador</p><p>Criar e gerir vistas de utilizador</p><p>Criar, editar, eliminar utilizadores e grupos e gerir licenças de utilizador</p><p>Gerir domínios</p><p>Gerir as informações da empresa</p><p>Delegar funções administrativas a outras pessoas</p><p>Utilizar a sincronização de diretórios</p><p>Ver registos de auditoria</p>|

### <a name="conditional-access-administrator"></a>Administrador de acesso condicional

| Pode fazê-lo | Não é possível efetuar |
| --- | --- |
|<p>Ver informações de utilizador e da empresa</p><p>Gerir definições de acesso condicional</p> |<p>Repor palavras-passe do utilizador</p><p>Criar e gerir vistas de utilizador</p><p>Criar, editar, eliminar utilizadores e grupos e gerir licenças de utilizador</p><p>Gerir domínios</p><p>Gerir as informações da empresa</p><p>Delegar funções administrativas a outras pessoas</p><p>Utilizar a sincronização de diretórios</p><p>Ver registos de auditoria</p>|

### <a name="global-administrator"></a>Administrador global
| Pode fazê-lo | Não é possível efetuar |
| --- | --- |
|<p>Ver informações de utilizador e da empresa</p><p>Gerir pedidos de suporte do Office</p><p>Efetuar operações de faturação e compras para produtos do Office</p><p>Repor palavras-passe do utilizador</p><p>Repor palavras-passe de outro administrador</p> <p>Criar e gerir vistas de utilizador</p><p>Criar, editar, eliminar utilizadores e grupos e gerir licenças de utilizador</p><p>Gerir domínios</p><p>Gerir as informações da empresa</p><p>Delegar funções administrativas a outras pessoas</p><p>Utilizar a sincronização de diretórios</p><p>Ativar ou desativar a autenticação multifator</p><p>Ver registos de auditoria</p> |N/D |

### <a name="password-administrator"></a>Administrador de palavras-passe
| Pode fazê-lo | Não é possível efetuar |
| --- | --- |
| <p>Ver informações de utilizador e da empresa</p><p>Gerir pedidos de suporte do Office</p><p>Repor palavras-passe do utilizador</p> <p>Repor palavras-passe de outro administrador</p>|<p>Efetuar operações de faturação e compras para produtos do Office</p><p>Criar e gerir vistas de utilizador</p><p>Criar, editar, eliminar utilizadores e grupos e gerir licenças de utilizador</p><p>Gerir domínios</p><p>Gerir as informações da empresa</p><p>Delegar funções administrativas a outras pessoas</p><p>Utilizar a sincronização de diretórios</p><p>Ver relatórios</p>|

### <a name="service-administrator"></a>Administrador de serviço
| Pode fazê-lo | Não é possível efetuar |
| --- | --- |
| <p>Ver informações de utilizador e da empresa</p><p>Gerir pedidos de suporte do Office</p> |<p>Repor palavras-passe do utilizador</p><p>Efetuar operações de faturação e compras para produtos do Office</p><p>Criar e gerir vistas de utilizador</p><p>Criar, editar, eliminar utilizadores e grupos e gerir licenças de utilizador</p><p>Gerir domínios</p><p>Gerir as informações da empresa</p><p>Delegar funções administrativas a outras pessoas</p><p>Utilizar a sincronização de diretórios</p><p>Ver registos de auditoria</p> |

### <a name="user-administrator"></a>Administrador de utilizador
| Pode fazê-lo | Não é possível efetuar |
| --- | --- |
| <p>Ver informações de utilizador e da empresa</p><p>Gerir pedidos de suporte do Office</p><p>Repor palavras-passe de utilizador, com limitações.</p><p>Repor palavras-passe de outro administrador</p><p>Repor palavras-passe de outros utilizadores</p><p>Criar e gerir vistas de utilizador</p><p>Criar, editar, eliminar utilizadores e grupos e gerir licenças de utilizador, com limitações. Seja não pode eliminar um administrador global nem criar outros administradores.</p> |<p>Efetuar operações de faturação e compras para produtos do Office</p><p>Gerir domínios</p><p>Gerir as informações da empresa</p><p>Delegar funções administrativas a outras pessoas</p><p>Utilizar a sincronização de diretórios</p><p>Ativar ou desativar a autenticação multifator</p><p>Ver registos de auditoria</p> |

### <a name="security-reader"></a>Leitor de segurança
| No | Pode fazê-lo |
| --- | --- |
| Centro de proteção de identidade |Ler todos os relatórios de segurança e informações de definições de funcionalidades de segurança<ul><li>Anti spam<li>Encriptação<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Antiphishing<li>Regras de Mailflow |
| Privileged Identity Management |<p>Tem acesso só de leitura para todas as informações anexados no Azure AD PIM: as políticas e relatórios para atribuições de funções do Azure AD, segurança analisa e acesso aos dados de política e relatórios para cenários para além de atribuição de função do Azure AD de leitura no futuro.<p>**Não é possível** inscrever-se no Azure AD PIM ou efetue as alterações. No portal do PIM ou através do PowerShell, alguém que esta função pode ativar funções adicionais (por exemplo, Administrador Global ou com privilégios de função de administrador), se o utilizador é uma candidata para os mesmos. |
| <p>O estado de funcionamento do monitor do Office 365 serviço</p><p>Segurança do Office 365 e o Centro de conformidade</p> |<ul><li>Ler e gerir alertas<li>Políticas de segurança de leitura<li>Ler informações sobre ameaças, o Cloud App Discovery e colocam em quarentena na procura e investigar<li>Ler todos os relatórios |

### <a name="security-administrator"></a>Administrador de segurança
| No | Pode fazê-lo |
| --- | --- |
| Centro de proteção de identidade |<ul><li>Todas as permissões da função de leitor de segurança.<li>Além disso, a capacidade de efetuar todas as operações de IPC exceto repor palavras-passe. |
| Privileged Identity Management |<ul><li>Todas as permissões da função de leitor de segurança.<li>**Não é possível** gerir as subscrições de funções do Azure AD ou as definições. |
| <p>O estado de funcionamento do monitor do Office 365 serviço</p><p>Segurança do Office 365 e o Centro de conformidade |<ul><li>Todas as permissões da função de leitor de segurança.<li>Pode configurar todas as definições na funcionalidade Advanced Threat Protection (proteção de software maligno e vírus, maliciosa URL configuração, o rastreio de URL, etc.). |

## <a name="details-about-the-global-administrator-role"></a>Detalhes sobre a função de administrador global
O administrador global tem acesso a todas as funcionalidades administrativas. Por predefinição, a pessoa que se inscreve para uma subscrição do Azure é atribuída a função de administrador global para o diretório. Apenas os administradores globais podem atribuir outras funções de administrador.

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Para adicionar um colega como um administrador global

1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja um administrador global do diretório do inquilino.

   ![Abrir o Centro de administração do azure AD](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Selecione **utilizadores e grupos &gt; todos os utilizadores**

3. Localize o utilizador que pretende designar como administrador global e abrir o painel para esse utilizador.

4. No painel de utilizador, selecione **função de diretório**.
 
5. No painel de função de diretório, selecione o **Administrador Global** função e guarde.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador
Para saber como atribuir funções administrativas a um utilizador no Azure Active Directory, consulte [atribuir um utilizador a funções de administrador na pré-visualização do Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser utilizadas. Estes foi despromovida e será removida do Azure AD no futuro.

* Administrador de licença de ad hoc
* Correio eletrónico verificado criador de utilizador
* Associação de dispositivos
* Gestores de dispositivos
* Utilizadores de dispositivos
* Associação à área de trabalho de dispositivo

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como alterar os administradores para uma subscrição do Azure, consulte [Como adicionar ou alterar funções de administrador do Azure](../billing-add-change-azure-subscription-administrator.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](active-directory-understanding-resource-access.md)
* Para obter mais informações sobre como o Azure Active Directory relacionada com a sua subscrição do Azure, consulte [subscrições do Azure como estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir utilizadores](active-directory-create-users.md)
* [Gerir palavras-passe](active-directory-manage-passwords.md)
* [Gerir grupos](active-directory-manage-groups.md)
