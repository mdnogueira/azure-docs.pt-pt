---
title: "Azure políticas de dispositivos de acesso condicional de Active Directory para serviços do Office 365 | Microsoft Docs"
description: "Saiba mais sobre como aprovisionar políticas de dispositivos de acesso condicional para ajudar a tornar os recursos da empresa mais seguro, mantendo a conformidade de utilizadores e aceder aos serviços."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d33babd331570624ca6a0fc967c79dbc467a1b40
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Active Directory dispositivo políticas de acesso condicional serviços do Office 365

Acesso condicional requer várias peças funcione. Envolve um multifator autenticada de utilizador, um dispositivo autenticado e um dispositivo compatível, entre outros fatores. Neste artigo, vamos principalmente focar-se em condições baseado nos dispositivos que a sua organização pode utilizar para o ajudar a controlar o acesso aos serviços do Office 365. 

Os utilizadores empresariais pretendem serviços de acesso do Office 365 como o Exchange e ao SharePoint Online no trabalho ou escola dos respetivos dispositivos pessoais. Pretende que o acesso seguro. Pode aprovisionar políticas de dispositivos de acesso condicional para ajudar a tornar os recursos da empresa mais seguros, concedendo acesso aos serviços para os utilizadores que utilizam dispositivos em conformidade. Pode definir políticas de acesso condicional para o Office 365 no portal de acesso condicional do Microsoft Intune.

Azure Active Directory (Azure AD) impõe as políticas de acesso condicional para ajudar a proteger o acesso a serviços do Office 365. Pode criar uma política de acesso condicional que bloqueia um utilizador que está a utilizar um dispositivo não conforme acedam a um serviço do Office 365. O utilizador deve estar em conformidade com as políticas de dispositivos da empresa antes de é concedido acesso ao serviço. Em alternativa, pode criar uma política que requer que os utilizadores inscrever os respetivos dispositivos para obterem acesso a um serviço do Office 365. Políticas podem ser aplicadas a todos os utilizadores numa organização ou limitadas a alguns grupos de destino. Pode adicionar mais grupos de destino para uma política ao longo do tempo.

Um pré-requisito para impor políticas de dispositivos é que os utilizadores tem de registar os seus dispositivos com o serviço de registo de dispositivos do Azure AD. Pode optar por ativar a autenticação multifator para os dispositivos que registar com o serviço de registo de dispositivos do Azure AD. Para o serviço de registo de dispositivos do Azure Active Directory, recomenda-se a autenticação multifator. Quando a autenticação multifator estiver ativada, os utilizadores para registar os dispositivos com o serviço de registo de dispositivos do Azure AD são solicitados para autenticação de segundo fator.

## <a name="how-does-a-conditional-access-policy-work"></a>Como funciona uma política de acesso condicional?

Quando um utilizador pedidos acesso a um serviço do Office 365 a partir de uma plataforma de dispositivos suportados, o Azure AD autentica o utilizador e o dispositivo. Do Azure AD concede acesso ao serviço apenas se o utilizador está em conformidade com a política definida para o serviço. Os utilizadores em dispositivos que não estão inscritos recebem instruções sobre a inscrição e conformidade para aceder aos serviços empresariais do Office 365. Os utilizadores em dispositivos iOS e Android têm de inscrever os respetivos dispositivos ao utilizar a aplicação Portal da empresa do Intune. Quando um utilizador inscreve um dispositivo, o dispositivo está registado com o Azure AD e estiver inscrito para gestão de dispositivos e conformidade. Tem de utilizar o serviço de registo de dispositivos do Azure AD com o Microsoft Intune para gestão de dispositivos móveis para serviços do Office 365. Inscrição de dispositivos é necessária para os utilizadores para serviços de acesso do Office 365 quando as políticas de dispositivo são impostas.

Quando um utilizador inscrever com êxito um dispositivo, o dispositivo se tornar fidedigno. Do Azure AD permite o utilizador autenticado único início de sessão acesso a aplicações da empresa. Azure AD impõe uma política de acesso condicional para conceder acesso a um serviço não só pela primeira vez o utilizador solicitar acesso, mas sempre que o utilizador renova um pedido de acesso. O utilizador é negado o acesso a serviços quando as credenciais de início de sessão forem alteradas, o dispositivo é perdido ou roubado, ou não são satisfeitas as condições da política no momento de pedido de renovação.

## <a name="deployment-considerations"></a>Considerações sobre implementação

Tem de utilizar o serviço de registo de dispositivos do Azure AD para registar dispositivos.

Quando os utilizadores no local estão prestes a ser autenticado, serviços de Federação do Active Directory (AD FS) (versão 1.0 e versões posteriores) é necessário. Autenticação multifator para Workplace Join falha quando o fornecedor de identidade não é capaz de autenticação multifator. Por exemplo, não é possível utilizar a autenticação multifator com o AD FS 2.0. Certifique-se de que no local do AD FS funciona com a autenticação multifator e de que um método de autenticação multifator válido está em vigor antes de ativar o multi-factor authentication para o serviço de registo de dispositivos do Azure AD. Por exemplo, o AD FS no Windows Server 2012 R2 tem capacidades de autenticação multifator. Também tem de definir um método de autenticação válido adicionais (multi-factor authentication) no servidor do AD FS antes de ativar o multi-factor authentication para o serviço de registo de dispositivos do Azure AD. Para obter mais informações sobre métodos de autenticação multifator suportadas no AD FS, consulte [configurar métodos de autenticação adicionais para AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Passos seguintes

*   Para obter respostas a questões recorrentes, consulte [acesso condicional do Azure Active Directory perguntas mais frequentes](active-directory-conditional-faqs.md).
