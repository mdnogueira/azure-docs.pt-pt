---
title: "Funcionalidades do serviço de sincronização do Azure AD Connect e configuração | Microsoft Docs"
description: "Descreve as funcionalidades do lado do serviço para o serviço de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: c2873510c280a2683c235cfdce3d2617c3b665cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-service-features"></a>Funcionalidades do serviço de sincronização do Azure AD Connect
A funcionalidade de sincronização do Azure AD Connect tem dois componentes:

* O componente no local com o nome **sincronização do Azure AD Connect**, também chamado **motor de sincronização**.
* O serviço que reside no Azure AD também conhecido como **serviço de sincronização do Azure AD Connect**

Este tópico explica como as seguintes funcionalidades do **serviço de sincronização do Azure AD Connect** trabalho e como pode configurá-los através do Windows PowerShell.

Estas definições são configuradas pelo [módulo Azure Active Directory para Windows PowerShell](http://aka.ms/aadposh). Transfira e instale-o em separado a partir do Azure AD Connect. Os cmdlets documentados neste tópico foram introduzidos no [versão de Março de 2016 (compilação 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se não tiver os cmdlets documentados neste tópico ou não produzem o mesmo resultado, em seguida, certifique-se de que executa a versão mais recente.

Para ver a configuração no diretório do Azure AD, execute `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures resultado](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Muitas destas definições só podem ser alteradas pelo Azure AD Connect.

As seguintes definições podem ser configuradas por `Set-MsolDirSyncFeature`:

| DirSyncFeature | Comentário |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Permite que os objetos associar o userPrincipalName para além do endereço SMTP principal. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Permite que o motor de sincronização atualizar o atributo userPrincipalName gerido/licenciado utilizadores (não federada). |

Depois de ter ativado uma funcionalidade, não é possível desativar novamente.

> [!NOTE]
> De 24 de Agosto de 2016 a funcionalidade *duplicado resiliência de atributo* está ativada por predefinição para o novo Azure diretórios AD. Esta funcionalidade será também revertida e ativada em diretórios criados antes desta data. Receberá uma notificação por e-mail quando o diretório está prestes a esta funcionalidade ativada.
> 
> 

As seguintes definições são configuradas pelo Azure AD Connect e não pode ser modificadas por `Set-MsolDirSyncFeature`:

| DirSyncFeature | Comentário |
| --- | --- |
| DeviceWriteback |[O Azure AD Connect: Ativar a repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md) |
| DirectoryExtensions |[Sincronização do Azure AD Connect: extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Permite que um atributo a ser colocados em quarentena quando é um duplicado de outro objeto em vez de todo o objeto a falhar durante a exportação. |
| PasswordSync |[Implementar a sincronização de palavras-passe com a sincronização do Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md) |
| UnifiedGroupWriteback |[Pré-visualização: Repetição de escrita de grupo](active-directory-aadconnect-feature-preview.md#group-writeback) |
| UserWriteback |Não é atualmente suportado. |

## <a name="duplicate-attribute-resiliency"></a>Resiliência de atributo duplicado
Em vez de falha para aprovisionar os objetos com UPNs duplicados / proxyAddresses, o atributo duplicado foi "colocado em quarentena" e um valor temporário está atribuído. Quando o conflito é resolvido, o UPN temporário é alterado automaticamente para o valor adequado. Para obter mais detalhes, consulte [resiliência de atributo de sincronização e duplicado identidade](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Correspondência de forma recuperável UserPrincipalName
Quando esta funcionalidade está ativada, a configuração soft-match está ativada para UPN, para além de [endereço SMTP principal](https://support.microsoft.com/kb/2641663), que está sempre ativada. Correspondência de forma recuperável é utilizada para corresponder a utilizadores de nuvem existente no Azure AD com utilizadores no local.

Se precisar de correspondência no local Contas de anúncios com contas existentes criadas na nuvem e não estiver a utilizar Exchange Online, em seguida, esta funcionalidade é útil. Neste cenário, geralmente, não tem um motivo para definir o atributo de SMTP na nuvem.

Esta funcionalidade está em por predefinição para recentemente criada diretórios do Azure AD. Pode ver se esta funcionalidade está ativada para si, executando a aplicação:  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se esta funcionalidade não está ativada para o diretório do Azure AD, pode ativá-la ao executar:  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar atualizações de userPrincipalName
Historicamente, as atualizações para o atributo UserPrincipalName utilizando o serviço de sincronização no local foi bloqueado, exceto se ambas estas condições forem verdadeiras:

* O utilizador é gerido (não federada).
* Não foi atribuída uma licença do utilizador.

Para obter mais detalhes, consulte [nomes de utilizador no Office 365, Azure ou Intune não corresponde ao ID de início de sessão alternativo de UPN no local ou](https://support.microsoft.com/kb/2523192).

Ativar esta funcionalidade permite que o motor de sincronização atualizar o userPrincipalName quando é alterado no local e utilizar a sincronização de palavra-passe. Se utilizar a Federação, esta funcionalidade não é suportada.

Esta funcionalidade está em por predefinição para recentemente criada diretórios do Azure AD. Pode ver se esta funcionalidade está ativada para si, executando a aplicação:  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se esta funcionalidade não está ativada para o diretório do Azure AD, pode ativá-la ao executar:  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Depois de ativar esta funcionalidade, os valores de userPrincipalName existentes permanecerá como-é. Na próxima alteração do userPrincipalName atributo no local, a sincronização de diferenças normal em utilizadores atualizará o UPN.  

## <a name="see-also"></a>Consultar também
* [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

