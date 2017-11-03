---
title: "Atualização do DirSync e Azure AD Sync | Microsoft Docs"
description: Descreve como atualizar do DirSync e Azure AD Sync para o Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3674670e10500d2992539ab60fbdb31b666fcf9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Atualizar a sincronização do Active Directory de sincronização do Azure Active Directory do Windows e do Azure
O Azure AD Connect é a melhor forma de ligar o seu diretório no local ao Azure AD e ao Office 365. É uma excelente altura para atualizar o Azure AD Connect do Windows Azure Active Directory Sync (DirSync) ou do Azure AD Sync para o Azure AD Connect, uma vez que estas ferramentas foram preteridas e o seu suporte terminará a 13 de abril de 2017.

As ferramentas de sincronização de dois identidade foram preteridas foram disponibilizadas para clientes de floresta única (DirSync) e para várias florestas e outros avançadas clientes (Azure AD Sync). Estas ferramentas antigas foram substituídas por uma solução única que está disponível para todos os cenários: o Azure AD Connect. Oferece novas funcionalidades, melhoramentos de funcionalidades e suporte para cenários de novo. Para poder continuar a sincronizar os dados de identidade no local ao Azure AD e do Office 365, recomendamos vivamente que Atualize para o Azure AD Connect.

A última versão do DirSync foi lançada em Julho de 2014 e a última versão do Azure AD Sync foi lançada de Maio de 2015.

## <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect
Azure AD Connect é o sucessor do DirSync e Azure AD Sync. Combina todos os cenários estes dois suportados. Pode ler mais acerca do mesmo no [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Agenda de preterição
| Data | Comentário |
| --- | --- |
| 13 de Abril de 2016 |Sincronização de diretório Active Directory ("DirSync") do Windows Azure e o Microsoft Azure Active Directory Sync ("Azure AD Sync") são comunicadas como preterido. |
| 13 de Abril de 2017 |Suporta termina. Os clientes já não será capazes de abrir um incidente de suporte sem atualizar primeiro para o Azure AD Connect. |
|31 de Dezembro de 2017|Azure AD já não irá aceitar comunicações de sincronização de diretório Active Directory ("DirSync") do Windows Azure e o Microsoft Azure Active Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Como efetuar a transição para o Azure AD Connect
Se estiver a executar o DirSync, existem duas formas de poder atualizar: implementação de atualização e paralela no local. Recomenda-se uma atualização no local para a maioria dos clientes e se tiver um recente operativo sistema e inferior a 50 000 objetos. Noutros casos, recomenda-se para efetuar uma implementação paralela em que a configuração do DirSync é movida para um novo servidor com o Azure AD Connect.

Se utilizar o Azure AD Sync, em seguida, é recomendada uma atualização no local. Se pretender, é possível instalar um novo servidor do Azure AD Connect em paralelo e efetuar uma migração swing do seu servidor de sincronização do Azure AD para o Azure AD Connect.

| Solução | Cenário |
| --- | --- |
| [Atualizar do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Se tiver um servidor existente do DirSync já em execução.</li> |
| [Atualização do Azure AD Sync](active-directory-aadconnect-upgrade-previous-version.md) |<li>Se estiver a mover do Azure AD Sync.</li> |

Se quiser ver como efetuar uma atualização no local a partir do DirSync para o Azure AD Connect, em seguida, veja este vídeo de Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>FAQ
**P: Posso ter recebido uma notificação de correio eletrónico da equipa do Azure e/ou uma mensagem a partir do Centro de mensagens do Office 365, mas estiver a utilizar o ligar.**  
A notificação também foi enviada a clientes que utilizam o Azure AD Connect com um número de compilação 1.0. \*.0 (utilizando uma versão de pré-1.1). A Microsoft recomenda que os clientes Mantenha-se atualizado com as versões do Azure AD Connect. O [a atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) funcionalidade introduzida no 1.1 torna mais fácil ter sempre uma versão recente do Azure AD Connect instalado.

**P: será DirSync/Azure AD Sync parar de funcionar em 13 de Abril de 2017?**  
DirSync/Azure AD Sync irá continuar a trabalhar em 13 de Abril de 2017.  No entanto, do Azure AD já não irá aceitar comunicações de DirSync/Azure AD Sync no 31 de Dezembro de 2017.

**P: quais as versões de DirSync pode atualizar do**  
É possível para atualizar a partir de qualquer versão do DirSync atualmente a ser utilizado.

**P: E sobre o conector Azure AD para FIM/MIM?**  
O conector do Azure AD para FIM/MIM tem **não** foi comunicada como preterido. Se encontra na **funcionalidade congelar**; não é adicionada nenhuma funcionalidade nova e recebe não correções de erros. A Microsoft recomenda que os clientes a utilizar para planear mover-se de para o Azure AD Connect. É vivamente recomendado não iniciar todas as implementações novas utilizá-la. Este conector será comunicado preterido no futuro.

## <a name="additional-resources"></a>Recursos Adicionais
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
