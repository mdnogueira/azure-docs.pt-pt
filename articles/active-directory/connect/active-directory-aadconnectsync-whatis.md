---
title: "Sincronização do Azure AD Connect: Noções e personalizar a sincronização | Microsoft Docs"
description: Explica como o Azure AD Connect sincronizar funciona e como personalizar.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.openlocfilehash: b00f756fdbd059b917ac7d28f24b1a0ec22594a9
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronização do Azure AD Connect: Noções e personalizar a sincronização
Os serviços de sincronização do Azure Active Directory Connect (sincronização do Azure AD Connect) é um componente principal do Azure AD Connect. Trata-se de que todas as operações relacionadas com a sincronizar dados de identidade entre o seu ambiente no local e o Azure AD. Sincronização do Azure AD Connect é o sucessor do DirSync, Azure AD Sync e Forefront Identity Manager com o conector Azure Active Directory configurado.

Este tópico é a base para **sincronização do Azure AD Connect** (também denominado **motor de sincronização**) e apresenta uma lista de ligações para todos os outros tópicos relacionados com o mesmo. Para ligações ao Azure AD Connect, consulte [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

O serviço de sincronização é composta por dois componentes, o local **sincronização do Azure AD Connect** componente e do lado do serviço no Azure AD chamado **serviço de sincronização do Azure AD Connect**. 

## <a name="azure-ad-connect-sync-topics"></a>Tópicos de sincronização do Azure AD Connect
| Tópico | O que abrange a e a ler |
| --- | --- |
| **Noções básicas de sincronização do Azure AD Connect** | |
| [Noções sobre a arquitetura](active-directory-aadconnectsync-understanding-architecture.md) |Os dos que são novos para o motor de sincronização e pretender saber mais sobre a arquitetura e termos utilizados. |
| [Conceitos técnicos](active-directory-aadconnectsync-technical-concepts.md) |Uma versão abreviada do tópico de arquitetura e brevemente explica termos utilizados. |
| [Topologias do Azure AD Connect](active-directory-aadconnect-topologies.md) |Descreve os cenários que suporta o motor de sincronização e topologias diferentes. |
| **Configuração personalizada** | |
| [Executar novamente o Assistente de instalação](active-directory-aadconnectsync-installation-wizard.md) |Explica o que as opções tem disponíveis quando executar novamente o Assistente de instalação do Azure AD Connect. |
| [Noções sobre aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |Descreve o modelo de configuração chamado aprovisionamento declarativo. |
| [Compreender as expressões do aprovisionamento declarativas](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Descreve a sintaxe para o idioma de expressão utilizada no aprovisionamento declarativo. |
| [Entender a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md) |Descreve as regras de out-of-box e a configuração predefinida. Também descreve como as regras de trabalham em conjunto para os cenários de out-of-box funcione. |
| [Entender utilizadores e contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Continua no tópico anterior e descreve a forma como a configuração para os utilizadores e contactos funciona em conjunto, em particular num ambiente multifloresta. |
| [Como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md) |Explica como efetuar uma alteração para fluxos de atributos de configuração comum. |
| [Melhores práticas para alterar a configuração predefinida](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Limitações do suporte e para efetuar alterações à configuração de out-of-box. |
| [Configurar a Filtragem](active-directory-aadconnectsync-configure-filtering.md) |Descreve as diferentes opções limitar os objetos que estão a ser sincronizados com o Azure AD e passo a passo como configurar estas opções. |
| **Cenários e funcionalidades** | |
| [Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Descreve o *impedir eliminações acidentais* funcionalidade e como configurá-lo. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |Descreve o programador incorporado, que está a importar, sincronizar e exportar dados. |
| [Implementar a sincronização de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) |Descreve como funciona a sincronização de palavra-passe, como implementar e como operar e resolver problemas. |
| [Repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md) |Descreve como funciona a repetição de escrita do dispositivo no Azure AD Connect. |
| [Extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md) |Descreve como expandir o esquema do Azure AD com os seus próprios atributos personalizados. |
| **Serviço de sincronização** | |
| [Funcionalidades do serviço de sincronização do Azure AD Connect](active-directory-aadconnectsyncservice-features.md) |Descreve o lado do serviço de sincronização e como alterar as definições de sincronização no Azure AD. |
| [Resiliência de atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Descreve como ativar e utilizar **userPrincipalName** e **proxyAddresses** resiliência de valores de atributo duplicado. |
| **Operações e a IU** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |Descreve a IU do Gestor do serviço de sincronização, incluindo [operações](active-directory-aadconnectsync-service-manager-ui-operations.md), [conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), e [depesquisadeMetaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) separadores. |
| [Considerações e tarefas operacionais](active-directory-aadconnectsync-operations.md) |Descreve as preocupações operacionais, tais como a recuperação após desastre. |
| **Como...** | |
| [Repor a conta do Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) |Como repor as credenciais da conta de serviço utilizada para ligar a partir da sincronização do Azure AD Connect para o Azure AD. |
| **Mais informações e referências** | |
| [Portas](active-directory-aadconnect-ports.md) |Lista de portas que é necessário abrir entre o motor de sincronização e os diretórios no local e o Azure AD. |
| [Atributos sincronizados com o Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Apresenta uma lista de todos os atributos que está a ser sincronizados entre no local AD e o Azure AD. |
| [Referência das Funções](active-directory-aadconnectsync-functions-reference.md) |Apresenta uma lista de todas as funções disponíveis no aprovisionamento declarativo. |

## <a name="additional-resources"></a>Recursos Adicionais
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

