---
title: "Grupo de definições de política e MDM | Microsoft Docs"
description: "Fornece informações sobre a política de grupo e dispositivos móveis definições de gestão (MDM) que deverão ser utilizadas em dispositivos pertencentes à empresa. Estas políticas são aplicadas ao dispositivo completo do utilizador."
services: active-directory
keywords: "quais são o grupo de política e definições de MDM para o Roaming de estado empresarial Roaming de estado empresarial, a nuvem do windows"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 71dd5281a618fe7367eab3e97daac069f77ab491
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="group-policy-and-mdm-settings"></a>Definições de política de grupo e MDM
Utilize estas política de grupo e definições de gestão (MDM) de dispositivos móveis apenas em dispositivos pertencentes à empresa porque estas políticas são aplicadas para o dispositivo do utilizador todo. Aplicar uma política MDM para desativar a sincronização de definições para um pessoal, dispositivos propriedade do utilizador irão afetar negativamente a utilização desse dispositivo. Além disso, outras contas de utilizador no dispositivo também serão afetadas pela política.

As empresas que pretende gerir roaming para dispositivos (geridos) pessoais podem utilizar o portal do Azure para ativar ou desativar o roaming, em vez de utilizar a política de grupo ou a MDM.
As tabelas seguintes descrevem as definições de política disponíveis.

## <a name="mdm-settings"></a>Definições de MDM
Aplicam as definições de política MDM para Windows 10 e Windows 10 Mobile.  Não existe suporte do Windows 10 Mobile apenas para a conta Microsoft com base em roaming através de conta do OneDrive.  Consulte a secção "Dispositivos e os pontos finais" para obter detalhes sobre quais os dispositivos que são suportados para sincronização do Azure AD com base.

| Nome | Descrição |
| --- | --- |
| Permitir ligação da conta Microsoft |Permite que os utilizadores se autentiquem utilizando uma conta Microsoft no dispositivo |
| Permitir Sincronização as minhas definições |Permite que os utilizadores sejam acedidas remotamente as definições do Windows e os dados da aplicação; Desativar esta política irá desativar a sincronização, bem como as cópias de segurança em dispositivos móveis |

## <a name="group-policy-settings"></a>Definições de política de grupo
As definições de política de grupo se aplicam aos dispositivos Windows 10 associados a um domínio do Active Directory. A tabela também inclui legadas as definições que aparece gerir as definições de sincronização, mas que não funcionam para Enterprise Estado Roaming para Windows 10, que são assinaladas com 'Não utilizam' na descrição.

| Nome | Descrição |
| --- | --- |
| Contas: Bloquear contas do Microsoft |Esta definição de política impede que os utilizadores adicionem novas contas Microsoft neste computador |
| Não sincronizar |Impede que os utilizadores para transferir as definições do Windows e os dados da aplicação |
| Não sincronizar a personalizar |Desativa a sincronização do grupo de temas |
| Não sincronizar as definições do browser |Desativa a sincronização do grupo do Internet Explorer |
| Não sincronizar as palavras-passe |Desativa a sincronização do grupo de palavras-passe |
| Não sincronizar a outras definições do Windows |Desativa a sincronização do grupo de definições de outras janelas |
| Não sincronizar a personalização de ambiente de trabalho |Não utilize; não tem qualquer efeito |
| Não sincronizar em ligações com tráfego limitado |Desativa em roaming limitados ligações, como 3G celular |
| Não sincronizar aplicações |Não utilize; não tem qualquer efeito |
| Não sincronizar as definições de aplicação |Desativa o roaming de dados de aplicação |
| Não sincronizar as definições de início |Não utilize; não tem qualquer efeito |

## <a name="related-topics"></a>Tópicos relacionados
* [Descrição geral do Roaming de estado empresarial](active-directory-windows-enterprise-state-roaming-overview.md)
* [Ativar roaming no Azure Active Directory de estado empresarial](active-directory-windows-enterprise-state-roaming-enable.md)
* [As definições e roaming FAQ de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Referência de definições de roaming Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

