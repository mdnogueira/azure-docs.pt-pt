---
title: "O Azure AD Connect: A atualização automática | Microsoft Docs"
description: "Este tópico descreve a funcionalidade de atualização automática incorporada na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 894e387b4b83ed859139b4aecb3d8bb5df9ab56f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: atualização automática
Esta funcionalidade foi introduzida com compilação 1.1.105.0 (Fevereiro de 2016 lançada).

## <a name="overview"></a>Descrição geral
Certificar-se de que a instalação do Azure AD Connect é sempre atualizada nunca foi mais fácil e com o **a atualização automática** funcionalidade. Esta funcionalidade está ativada por predefinição para instalações rápidas e atualiza o DirSync. Quando for lançada uma nova versão, a instalação é atualizada automaticamente.
A atualização automática está ativada por predefinição para o seguinte:

* Expressar a instalação de definições e atualizações do DirSync.
* Utilizar o SQL Server Express LocalDB, que é que as definições rápidas utilizam sempre. Também o DirSync com SQL Server Express utilizar LocalDB.
* A conta de AD é a conta MSOL_ predefinida criada pelas definições rápidas e DirSync.
* Tiver menos de 100 000 objetos no metaverso.

O estado atual da atualização automática pode ser visualizado com o cmdlet do PowerShell `Get-ADSyncAutoUpgrade`. Tem os seguintes Estados:

| Estado | Comentário |
| --- | --- |
| Ativado |A atualização automática está ativada. |
| Suspenso |Definido pelo sistema apenas. O sistema já não é elegível para receber as atualizações automáticas. |
| Desativado |A atualização automática está desativada. |

Pode alternar entre **ativado** e **desativado** com `Set-ADSyncAutoUpgrade`. Só o sistema deve ser definido o estado **suspenso**.

A atualização automática está a utilizar o Azure AD Connect Health para a infraestrutura de atualização. Para a atualização automática funcionar, certifique-se de que abriu os URLs no seu servidor proxy para **do Azure AD Connect Health** conforme documentado no [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Se o **Synchronization Service Manager** IU está em execução no servidor, em seguida, a atualização é suspenso até que a IU está fechada.

## <a name="troubleshooting"></a>Resolução de problemas
Se a instalação de ligar não atualizar-se conforme esperado, em seguida, siga estes passos para saber o que poderá estar errado.

Em primeiro lugar, deve não espera que a atualização automática ao ser tentada o primeiro dia que é lançada uma nova versão. Não há um randomness intencional antes de uma atualização é tentada para não ser alarmed se a instalação não é imediatamente atualizada.

Se pensa que algo não for adequado, em seguida, primeiro execute `Get-ADSyncAutoUpgrade` para garantir que a atualização automática está ativada.

Em seguida, certifique-se de que abriu os URLs necessários no proxy ou na firewall. A atualização automática está a utilizar o Azure AD Connect Health, tal como descrito no [descrição geral](#overview). Se utilizar um proxy, certifique-se de que o estado de funcionamento foi configurado para utilizar um [servidor proxy](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Também testar o [conectividade de estado de funcionamento](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para o Azure AD.

Com a conectividade com o Azure AD verificada, está na altura parecer para o eventlogs. Iniciar o Visualizador de eventos e procure no **aplicação** registo de eventos. Adicionar um filtro de registo de eventos para a origem **do Azure AD Connect atualizar** e o intervalo de id de evento **300 399**.  
![Filtro de registo de eventos para a atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Agora, pode ver o eventlogs associadas com o estado de atualização automática.  
![Filtro de registo de eventos para a atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

O código de resultado tem um prefixo com uma descrição geral do Estado.

| Prefixo de código de resultado | Descrição |
| --- | --- |
| Êxito |A instalação foi atualizada com êxito. |
| UpgradeAborted |Uma condição temporária parado a atualização. Será repetida novamente e as expectativas são tiver êxito mais tarde. |
| UpgradeNotSupported |O sistema tem uma configuração que está a bloquear o sistema de que está a ser atualizado automaticamente. Será repetida para ver se está a alterar o estado, mas as expectativas são de que o sistema tem de ser atualizado manualmente. |

Aqui está uma lista das mensagens mais comuns que encontrar. Não listar todas as, mas a mensagem de resultado deve ser desmarque com que o problema.

| Mensagem de resultado | Descrição |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Não foi possível escrever no registo. |
| UpgradeAbortedInsufficientDatabasePermissions |O grupo de Administradores incorporado não tem permissões para a base de dados. Atualize manualmente para a versão mais recente do Azure AD Connect, para resolver este problema. |
| UpgradeAbortedInsufficientDiskSpace |Não há espaço em disco suficiente para suportar uma atualização. |
| UpgradeAbortedSecurityGroupsNotPresent |Não foi possível localizar e resolva todos os grupos de segurança utilizados pelo motor de sincronização. |
| UpgradeAbortedServiceCanNotBeStarted |O serviço NT **Microsoft Azure AD Sync** Falha ao iniciar. |
| UpgradeAbortedServiceCanNotBeStopped |O serviço NT **Microsoft Azure AD Sync** não conseguiu parar. |
| UpgradeAbortedServiceIsNotRunning |O serviço NT **Microsoft Azure AD Sync** não está em execução. |
| UpgradeAbortedSyncCycleDisabled |A opção de SyncCycle no [programador](active-directory-aadconnectsync-feature-scheduler.md) foi desativada. |
| UpgradeAbortedSyncExeInUse |O [Gestor do serviço de sincronização da IU](active-directory-aadconnectsync-service-manager-ui.md) está aberta no servidor. |
| UpgradeAbortedSyncOrConfigurationInProgress |O Assistente de instalação está em execução ou foi agendada uma sincronização fora do programador. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Selecionou Adfs como método de início de sessão. | 
| UpgradeNotSupportedCustomizedSyncRules |Adicionou as suas próprias regras personalizadas para a configuração. |
| UpgradeNotSupportedDeviceWritebackEnabled |Ativou o [repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md) funcionalidade. |
| UpgradeNotSupportedGroupWritebackEnabled |Ativou o [repetição de escrita do grupo](active-directory-aadconnect-feature-preview.md#group-writeback) funcionalidade. |
| UpgradeNotSupportedInvalidPersistedState |A instalação não é um definições Express ou uma atualização do DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Tiver mais de 100 000 objetos no metaverso. |
| UpgradeNotSupportedMultiForestSetup |Está a ligar mais do que uma floresta. Configuração rápida liga-se apenas a uma floresta. |
| UpgradeNotSupportedNonLocalDbInstall |Não estão a utilizar uma base de dados do SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |O [conta do conector AD](active-directory-aadconnect-accounts-permissions.md#active-directory-account) já não faz a conta MSOL_ predefinida. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Quando configurar o AAD Connect, optou por *não configurar* quando selecionar o método de início de sessão. | 
| UpgradeNotSupportedPtaSignInMethod | Tiver selecionado autenticação pass-through como método de início de sessão. |
| UpgradeNotSupportedStagingModeEnabled |O servidor está definido para ser [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Ativou o [repetição de escrita do utilizador](active-directory-aadconnect-feature-preview.md#user-writeback) funcionalidade. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
