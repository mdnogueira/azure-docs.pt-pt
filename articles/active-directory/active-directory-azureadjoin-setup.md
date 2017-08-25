---
title: "Configurar a Associação do Azure AD dos utilizadores| Microsoft Docs"
description: "Explica como os administradores podem configurar a Associação do Azure AD para o diretório no local e registo de dispositivos."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: bfc5d415-c918-4d8b-afee-b3f41cc28469
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 0480c4f89b6036897bc8d7eff81b56262ea8806c
ms.openlocfilehash: e4e0fa77552c4df2ea5bb9ddae916e7c661824d1
ms.contentlocale: pt-pt
ms.lasthandoff: 02/23/2017

---
# <a name="setting-up-azure-ad-join-in-your-organization"></a>Configurar a Associação do Azure AD na sua 
Antes de configurar a Associação do Azure Active Directory (Associação do Azure AD), terá da sincronizar o diretório de utilizadores no local para a nuvem ou criar manualmente contas geridas no Azure AD.

Encontrará instruções detalhadas para sincronizar os utilizadores no local com o Azure AD em [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

Para criar e gerir manualmente utilizadores no Azure AD, consulte [Gestão de utilizadores no Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Configurar o registo do dispositivo
1. Inicie sessão no Portal do Azure como administrador.
2. No painel esquerdo, selecione **Active Directory**.
3. No separador **Diretório** selecione o diretório.
4. Selecione o separador **Configurar**.
5. Aceda à secção **Dispositivos**.
6. No separador **Dispositivos**, defina o seguinte:  
   * **NÚMERO MÁXIMO DE DISPOSITIVOS POR UTILIZADOR**: selecione o número máximo de dispositivos que um utilizador pode ter no Azure AD.  Se um utilizador atingir esta quota, este não poderá adicionar dispositivos até remover um ou mais dispositivos existentes.
   * **REQUER MULTI-FACTOR AUTH PARA ASSOCIAR DISPOSITIVOS**: defina se os utilizadores têm de fornecer um segundo fator de autenticação para associar os seus dispositivos ao Azure AD. Para obter mais informações sobre o Multi-Factor Authentication do Azure, consulte [Introdução ao Multi-Factor Authentication do Azure na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).
   * **OS UTILIZADORES PODEM ASSOCIAR DISPOSITIVOS AO AZURE AD**: selecione os utilizadores e grupos que estão autorizados a associar dispositivos ao Azure AD.
   * **ADMINISTRADORES ADICIONAIS EM DISPOSITIVOS ASSOCIADOS DO AZURE AD**: com o Azure AD Premium ou o Enterprise Mobility Suite (EMS), pode escolher quais os utilizadores que terão direitos de administrador no dispositivo. Por predefinição, são concedidos direitos de administrador local as administradores globais e aos proprietários do dispositivo.

<center>![Configurar o registo do dispositivo](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

Depois de configurar a Associação do Azure AD para os utilizadores, estes poderão ligar-se ao Azure AD com os seus dispositivos profissionais ou pessoais.

Apresentamos a seguir os três cenários que pode utilizar para permitir aos seus utilizadores configurar a Associação do Azure AD:

* Os utilizadores associam um dispositivo da empresa diretamente ao Azure AD.
* Os utilizadores associam um domínio de um dispositivo da empresa ao Active Directory no local e, em seguida, expandem o dispositivo ao Azure AD.
* Os utilizadores adicionam contas escolares ou profissionais no Windows, num dispositivo pessoal

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar os dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Aumentar as capacidades de nuvem para dispositivos com Windows 10 através da Associação do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre os cenários de utilização da Associação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar a dispositivos associados a um domínio ao Azure AD para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Associação do Azure AD](active-directory-azureadjoin-setup.md)


