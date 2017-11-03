---
title: "Descrição geral do Roaming de estado empresarial | Microsoft Docs"
description: "Fornece informações sobre as definições de Roaming de estado empresarial em dispositivos Windows. Roaming de estado empresarial fornece aos utilizadores uma experiência unificada entre os respetivos dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo."
services: active-directory
keywords: "o que é o Roaming de estado empresarial, a sincronização de enterprise, windows na nuvem"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: b3c01f8d332d26e92dc3052681a4b2c95142d440
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enterprise-state-roaming-overview"></a>Descrição geral do Roaming de Estado Empresarial
Com o Windows 10, [Azure Active Directory (Azure AD)](active-directory-whatis.md) os utilizadores obtêm a capacidade de forma segura sincronizar os seus dados de definições de aplicação para a nuvem e de definições do utilizador. Roaming de estado empresarial fornece aos utilizadores uma experiência unificada entre os respetivos dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo. Roaming de estado empresarial funciona semelhante para a norma [sincronização de definições de consumidor](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) que foi introduzida pela primeira vez no Windows 8. Além disso, proporciona Roaming de estado empresarial:

* **Separação de empresa e dados de consumidor** – organizações são no controlo dos seus dados e não há nenhum mistura de dados da empresa numa conta de nuvem de consumidor ou dados de consumidor numa conta empresarial na nuvem.
* **Uma segurança reforçada** – automaticamente os dados são encriptados antes de deixarem a dispositivo do Windows 10 do utilizador utilizando o Azure Rights Management (Azure RMS) e dados permanecem encriptados Inativos na nuvem. Todo o conteúdo permanece encriptado Inativos na nuvem, exceto para espaços de nomes, como nomes de definições e os nomes das aplicações do Windows.  
* **Melhor monitorização e gestão** – fornece controlo e visibilidade sobre quem sincroniza-se as definições na sua organização e em que dispositivos através da integração de portal do Azure AD. 

Roaming de estado empresarial está disponível em várias regiões do Azure. Pode encontrar a lista atualizada de regiões disponíveis no [serviços do Azure por regiões](https://azure.microsoft.com/regions/#services) página no Azure Active Directory.

| Artigo | Descrição |
| --- | --- |
| [Ativar estado da empresa em Roaming no Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Roaming de estado empresarial está disponível para qualquer organização com uma subscrição Premium do Azure Active Directory (Azure AD). Para obter mais detalhes sobre como obter uma subscrição do Azure AD, consulte o [produto do Azure AD](https://azure.microsoft.com/services/active-directory) página. |
| [As definições e roaming FAQ de dados](active-directory-windows-enterprise-state-roaming-faqs.md) |Este tópico responde a algumas perguntas os administradores de TI podem ter sobre as definições e sincronização de dados de aplicação. |
| [Política de grupo e definições de MDM para sincronização de definições](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 fornece política de grupo e gestão de dispositivos móveis (MDM) definições de política para limitar a sincronização de definições. |
| [Referência de definições de roaming Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Segue-se uma lista completa de todas as definições que irá ser movidos e/ou cópia de segurança no Windows 10. |
| [Resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Este tópico passa através de alguns passos básicos para resolução de problemas e contém uma lista dos problemas conhecidos. |

