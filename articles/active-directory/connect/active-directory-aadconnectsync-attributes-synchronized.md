---
title: Atributos sincronizados pelo Azure AD Connect | Microsoft Docs
description: "Lista os atributos que são sincronizados com o Azure Active Directory."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: 08663916214cf2d34704ef2a355464bc6df9b138
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização do Azure AD Connect: atributos sincronizados com o Azure Active Directory
Este tópico lista os atributos que estão sincronizados com a sincronização do Azure AD Connect.  
Os atributos estão agrupados por relacionados do Azure AD aplicação.

## <a name="attributes-to-synchronize"></a>Atributos a sincronizar
Uma pergunta comum é *o que é a lista de atributos mínimo para sincronizar*. A predefinição e a abordagem recomendada é manter os atributos de predefinido para que um GAL completo (lista de endereços Global) pode ser construído numa nuvem e para obter todas as funcionalidades de cargas de trabalho do Office 365. Em alguns casos, existem alguns atributos que a organização não quer sincronizados para a nuvem, uma vez que estes atributos contêm confidencial ou dados do PII (informação identificativa), como neste exemplo:  
![atributos incorretos](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

Neste caso, comece com a lista de atributos neste tópico e identificar os atributos que deveria conter confidenciais ou dados PII e não não possível sincronizar. Em seguida, desmarque esses atributos durante a instalação utilizando [aplicação Azure AD e filtragem de atributos](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Quando desmarcando atributos, deve tenha cuidado e desmarque apenas esses atributos absolutamente não é possíveis sincronizar. Unselecting outros atributos pode ter um impacto negativo sobre funcionalidades.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nome do Atributo | Utilizador | Comentar |
| --- |:---:| --- |
| accountEnabled |X |Define se a uma conta estiver ativada. |
| CN |X | |
| displayName |X | |
| Sidobjeto |X |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| pwdLastSet |X |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. Utilizado pela sincronização de palavra-passe e a Federação. |
| sourceAnchor |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| usageLocation |X |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userPrincipalName |X |UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |

## <a name="exchange-online"></a>Exchange Online
| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se a uma conta estiver ativada. |
| Assistente de |X |X | | |
| altRecipient |X | | |Requer o Azure AD Connect compilação 1.1.552.0 ou após. |
| authOrig |X |X |X | |
| C |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| Indicativo do país |X |X | | |
| Departamento |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| HomePhone |X |X | | |
| informações |X |X |X |Atualmente, este atributo é consumido não para grupos. |
| iniciais |X |X | | |
| l |X |X | | |
| LegacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| Está | | |X | |
| Gestor |X |X | | |
| Membro | | |X | |
| Mobile |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Disponível no Azure AD Connect versão 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Este atributo é atualmente não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Este atributo é atualmente não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Este atributo é atualmente não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Este atributo é atualmente não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Este atributo é atualmente não é consumido pelo Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg IsOrganizational | | |X | |
| Sidobjeto |X | |X |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PostalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. Utilizado pela sincronização de palavra-passe e a Federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Derivado groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| Título |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se a uma conta estiver ativada. |
| authOrig |X |X |X | |
| C |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| Indicativo do país |X |X | | |
| Departamento |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| informações |X |X |X | |
| iniciais |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| capacidade de correio |X |X |X | |
| mailNickname |X |X |X | |
| Está | | |X | |
| Gestor |X |X | | |
| Membro | | |X | |
| middleName |X |X | | |
| Mobile |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| Sidobjeto |X | |X |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| OtherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PostalCode |X |X | | |
| postOfficeBox |X |X | |Este atributo é atualmente não é consumido pelo SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. Utilizado pela sincronização de palavra-passe e a Federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Derivado groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| Título |X |X | | |
| unauthOrig |X |X |X | |
| URL |X |X | | |
| usageLocation |X | | |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online (subsequentemente conhecido como Skype para empresas)
| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se a uma conta estiver ativada. |
| C |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| Departamento |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| capacidade de correio |X |X |X | |
| mailNickname |X |X |X | |
| Está | | |X | |
| Gestor |X |X | | |
| Membro | | |X | |
| Mobile |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-linha |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| Sidobjeto |X | |X |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PostalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. Utilizado pela sincronização de palavra-passe e a Federação. |
| securityEnabled | | |X |Derivado groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| Título |X |X | | |
| usageLocation |X | | |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>O Azure RMS
| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se a uma conta estiver ativada. |
| CN |X | |X |Nome ou alias comum. Mais frequentemente o prefixo do valor de [correio]. |
| displayName |X |X |X |Uma cadeia que representa o nome, muitas vezes, é apresentado como o nome amigável (Apelido Nome próprio). |
| capacidade de correio |X |X |X |endereço de e-mail completo. |
| Membro | | |X | |
| Sidobjeto |X | |X |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| proxyAddresses |X |X |X |propriedade mechanical. Utilizada pelo Azure AD. Contém todos os endereços de e-mail secundário para o utilizador. |
| pwdLastSet |X | | |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. |
| securityEnabled | | |X |Derivado groupType. |
| sourceAnchor |X |X |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userPrincipalName |X | | |Este UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |

## <a name="intune"></a>Intune
| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se a uma conta estiver ativada. |
| C |X |X | | |
| CN |X | |X | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| capacidade de correio |X |X |X | |
| mailNickname |X |X |X | |
| Membro | | |X | |
| Sidobjeto |X | |X |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. Utilizado pela sincronização de palavra-passe e a Federação. |
| securityEnabled | | |X |Derivado groupType |
| sourceAnchor |X |X |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se a uma conta estiver ativada. |
| C |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| Indicativo do país |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| Está | | |X | |
| Gestor |X |X | | |
| Membro | | |X | |
| Mobile |X |X | | |
| Sidobjeto |X | |X |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| physicalDeliveryOfficeName |X |X | | |
| PostalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. Utilizado pela sincronização de palavra-passe e a Federação. |
| securityEnabled | | |X |Derivado groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| telephoneNumber |X |X | | |
| Título |X |X | | |
| usageLocation |X | | |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |

## <a name="3rd-party-applications"></a>aplicações de terceiros 3rd
Este grupo é um conjunto de atributos utilizado como os atributos mínimo necessários para uma carga de trabalho genérica ou aplicação. Pode ser utilizado para uma carga de trabalho não listado no outra secção ou para uma aplicação de terceiros. Explicitamente é utilizado para o seguinte:

* Yammer (apenas o utilizador é consumido)
* [Cenários colaboração híbridos empresa-empresa (B2B) entre org oferecidos pelos recursos como o SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Este grupo é um conjunto de atributos que pode ser utilizado se o diretório do Azure AD não é utilizado para suportar o Office 365, o Dynamics ou o Intune. Tem um pequeno conjunto de atributos principais.

| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se a uma conta estiver ativada. |
| CN |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| capacidade de correio |X | |X | |
| Está | | |X | |
| mailNickName |X |X |X | |
| Membro | | |X | |
| Sidobjeto |X | | |propriedade mechanical. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mechanical. Utilizado para saberem invalidar tokens já emitidos. Utilizado pela sincronização de palavra-passe e a Federação. |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mechanical. Identificador imutável para manter a relação entre ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mechanical. País do utilizador. Utilizado para a atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Mais frequentemente é o valor igual a [correio]. |

## <a name="windows-10"></a>Windows 10
Um computer(device) associados a um domínio do Windows 10 efetua a sincronização de alguns atributos para o Azure AD. Para obter mais informações sobre os cenários, consulte [ligar dispositivos associados a um domínio ao Azure AD para experiências do Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Estes atributos sempre sincronizar e Windows 10 não aparece como uma aplicação que pode anular a seleção. Um computador associado a um domínio do Windows 10 é identificado por ter userCertificate o atributo preenchido.

| Nome do Atributo | Dispositivo | Comentar |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Valor de codificado para computadores associados a um domínio. |
| displayName |X | |
| MS-DS-CreatorSID |X |Também denominado registeredOwnerReference. |
| objectGUID |X |Também denominado deviceID. |
| Sidobjeto |X |Também denominado onPremisesSecurityIdentifier. |
| sistema operativo |X |Também denominado deviceOSType. |
| operatingSystemVersion |X |Também denominado deviceOSVersion. |
| userCertificate |X | |

Estes atributos para **utilizador** estão além de outras aplicações que selecionou.  

| Nome do Atributo | Utilizador | Comentar |
| --- |:---:| --- |
| domainFQDN |X |Também denominado dnsDomainName. Por exemplo, contoso.com. |
| domainNetBios |X |Também denominado netBiosName. Por exemplo, CONTOSO. |

## <a name="exchange-hybrid-writeback"></a>Repetição de escrita do Exchange híbrida
Estes atributos são repetidos do AD do Azure Active Directory no local ao selecionar a opção para ativar **híbrida do Exchange**. Dependendo da versão do Exchange, poderão estar sincronizados menos atributos.

| Nome do Atributo | Utilizador | Contacto | Grupo | Comentar |
| --- |:---:|:---:|:---:| --- |
| msDS-ExternalDirectoryObjectID |X | | |Derivado cloudAnchor no Azure AD. Este atributo é novo no Exchange 2016 e AD do Windows Server 2016. |
| msExchArchiveStatus |X | | |Arquivo online: Permite que os clientes Arquivar correio. |
| msExchBlockedSendersHash |X | | |Filtragem: Escreve novamente no local de filtragem e os dados de remetente seguro e bloqueadas online dos clientes. |
| msExchSafeRecipientsHash |X | | |Filtragem: Escreve novamente no local de filtragem e os dados de remetente seguro e bloqueadas online dos clientes. |
| msExchSafeSendersHash |X | | |Filtragem: Escreve novamente no local de filtragem e os dados de remetente seguro e bloqueadas online dos clientes. |
| msExchUCVoiceMailSettings |X | | |Ativar Unified Messaging (UM) - correio de voz Online: utilizadas pelo Microsoft Lync Server integração para indicar ao Lync Server no local que o utilizador tem o correio de voz nos serviços online. |
| msExchUserHoldPolicies |X | | |Suspensão de litígio: Permite que os serviços de nuvem para determinar quais os utilizadores que estão em litígio conter. |
| proxyAddresses |X |X |X |Apenas o x500 é inserido o endereço do Exchange Online. |
| publicDelegates |X | | |Permite que uma caixa de correio do Exchange Online ser concedida SendOnBehalfTo direitos para os utilizadores com caixa de correio do Exchange no local. Requer o Azure AD Connect compilação 1.1.552.0 ou após. |

## <a name="exchange-mail-public-folder"></a>Pasta de público de correio do Exchange
Estes atributos são sincronizados a partir do Active Directory no local ao Azure AD quando optar por ativar **pasta pública de correio eletrónico Exchange**.

| Nome do Atributo | PublicFolder | Comentar |
| --- | :---:| --- |
| displayName | X |  |
| capacidade de correio | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Repetição de escrita do dispositivo
Objetos de dispositivo são criados no Active Directory. Estes objetos podem ser dispositivos associados ao Azure AD ou a computadores associados a domínios do Windows 10.

| Nome do Atributo | Dispositivo | Comentar |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| DN |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Apenas com o esquema do AD do Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Notas
* Quando utilizar um ID alternativo, o atributo userPrincipalName no local está sincronizado com o onPremisesUserPrincipalName de atributo do Azure AD. O atributo ID alternativo, para o correio de exemplo, está sincronizado com o atributo userPrincipalName do Azure AD.
* Na lista acima, o tipo de objeto **utilizador** também se aplica para o tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
