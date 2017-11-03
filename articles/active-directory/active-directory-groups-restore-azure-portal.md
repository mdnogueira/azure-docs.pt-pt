---
title: Restaurar um grupo eliminado do Office 365 no Azure Active Directory | Microsoft Docs
description: Como restaurar um grupo eliminado, vista de grupos que possam ser restauradas e permamnently eliminar um grupo no Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 5d06cee492e3360bcaf8c7663c97d0c8ed3e243f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Restaurar um grupo eliminado do Office 365 no Azure Active Directory

Quando elimina um grupo do Office 365 no Azure Active Directory (Azure AD), grupo eliminado destina retidos, mas não é visível de 30 dias a contar da data de eliminação. Isto é, de modo a que o grupo e o respetivo conteúdo pode ser restaurado se for necessário. Esta funcionalidade é restringida exclusivamente para grupos do Office 365 no Azure AD. Não está disponível para grupos de segurança e de grupos de distribuição.

> [!NOTE] 
> Não utilize `Remove-MsolGroup` porque elimina o grupo de forma permanente. Utilize sempre `Remove-AzureADMSGroup` para eliminar um grupo do Office 365. 

As permissões necessárias para restaurar um grupo podem ser qualquer um dos seguintes:

Função  | Permissões 
--------- | ---------
Administrador da empresa, suporte de parceiro Tier2 e administradores de serviço do InTune | Pode restaurar a qualquer grupo eliminado do Office 365 
Suporte de administrador de conta de utilizador e Tier1 de parceiro | Pode restaurar a qualquer grupo eliminado do Office 365 exceto aos que estão atribuídos à função de administrador da empresa 
Utilizador | Pode restaurar a qualquer grupo eliminado do Office 365 que detinham 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Ver os grupos do Office 365 eliminados, que estão disponíveis para restaurar
Os seguintes cmdlets pode ser utilizados para ver os grupos de eliminados e certifique-se de que o ou aqueles que estiver interessado em tem não ainda foi permanentemente removidos. Estes cmdlets fazem parte do [módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Podem encontrar mais informações sobre este módulo no [do Azure Active Directory PowerShell versão 2](/powershell/azure/install-adv2?view=azureadps-2.0) artigo.

1.  Execute o cmdlet seguinte para apresentar todos os grupos do Office 365 eliminados no seu inquilino que ainda estão disponíveis para restaurar.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  Em alternativa, se souber o objectID de um grupo específico (e pode obtê-lo a partir do cmdlet no passo 1), execute o cmdlet seguinte para verificar que o grupo foi eliminado específico não ainda foi permanentemente removido.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>Como restaurar o seu grupo eliminado do Office 365
Depois de ter verificado que o grupo ainda está disponível para restaurar, restaure o grupo foi eliminado com um dos seguintes passos. Se o grupo contém documentos, SP sites ou outros objetos persistentes, pode demorar até 24 horas para restaurar completamente um grupo e o respetivo conteúdo.

1.  Execute o cmdlet seguinte para restaurar o grupo e o respetivo conteúdo.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

Em alternativa, o cmdlet seguinte pode ser executado para remover o grupo foi eliminado permanentemente.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Como pode saber isto trabalhado?
Para verificar que foi restaurada com êxito um grupo do Office 365, execute o `Get-AzureADGroup –ObjectId <objectId>` cmdlet para ver informações sobre o grupo. Após o restauro conclusão do pedido:
- O grupo irá aparecer na barra de navegação esquerda no Exchange
- O plano para o grupo irá aparecer na Planner
- Todos os sites Sharepoint e todos os respetivos conteúdos estarão disponíveis
- O grupo pode ser acedido a partir de qualquer um dos pontos finais do Exchange e outras cargas de trabalho do Office 365 que suportam a grupos do Office 365


## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais sobre os grupos do Azure Active Directory.

* [Consulte os grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerir as definições de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerir os membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerir membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas para os utilizadores de um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
