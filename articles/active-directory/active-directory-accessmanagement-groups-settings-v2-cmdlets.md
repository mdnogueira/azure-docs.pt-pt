---
title: Exemplos do PowerShell para gerir grupos no Azure Active Directory | Microsoft Docs
description: "Esta página fornece exemplos do PowerShell para o ajudar a gerir os grupos no Azure Active Directory"
keywords: "O Azure AD, o Azure Active Directory, PowerShell, gestão de grupos, grupo"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: curtand
ms.reviewer: rodejo
ms.openlocfilehash: 5cad44dc7bf415002b3c9872fffdcf0d54bb6ad6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Cmdlets de versão 2 do Azure Active Directory para gestão de grupo
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Este artigo contém exemplos de como utilizar o PowerShell para gerir os grupos no Azure Active Directory (Azure AD).  Também indica como obter configurar com o módulo Azure AD PowerShell. Em primeiro lugar, tem [transferir o módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="installing-the-azure-ad-powershell-module"></a>Instalar o módulo do PowerShell do Azure AD
Para instalar o módulo Azure AD PowerShell, utilize os seguintes comandos:

    PS C:\Windows\system32> install-module azuread

Para verificar se o módulo foi instalado, utilize o seguinte comando:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo do Azure AD, consulte a documentação de referência online para [do Azure Active Directory PowerShell versão 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connecting-to-the-directory"></a>Ligar para o diretório
Antes de começar a gerir grupos utilizando cmdlets do Azure AD PowerShell, tem de ligar a sessão do PowerShell para o diretório que pretende gerir. Utilize o seguinte comando:

    PS C:\Windows\system32> Connect-AzureAD

O cmdlet irá pedir-lhe para as credenciais que pretende utilizar para aceder ao seu diretório. Neste exemplo, estamos a utilizar karen@drumkit.onmicrosoft.com para aceder ao diretório de demonstração. O cmdlet devolve uma confirmação para mostrar que a sessão foi ligada com êxito para o diretório:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Agora pode começar a utilizar os cmdlets AzureAD para gerir grupos no seu diretório.

## <a name="retrieving-groups"></a>A obter grupos
Para obter os grupos existentes do diretório pode utilizar o cmdlet Get-AzureADGroups. Para obter todos os grupos no diretório, utilize o cmdlet sem parâmetros:

    PS C:\Windows\system32> get-azureadgroup

O cmdlet devolve todos os grupos no diretório ligado.

Pode utilizar o parâmetro - objectID para obter um grupo específico para o qual o objectID do grupo que especificar:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

O cmdlet devolve agora o grupo cujo objectID corresponde ao valor do parâmetro introduziu:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Pode procurar um grupo específico utilizando o filtro parâmetro-. Este parâmetro assume uma cláusula de filtro ODATA e devolve todos os grupos que correspondem ao filtro, como no exemplo seguinte:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

> [!NOTE] 
> Os cmdlets do PowerShell de AzureAD implementa o padrão de consulta de OData. Para obter mais informações, consulte **$filter** no [opções de consulta OData sistema utilizando o ponto final de OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Criar grupos
Para criar um novo grupo no seu diretório, utilize o cmdlet New-AzureADGroup. Este cmdlet cria um novo grupo de segurança chamado "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Grupos de atualização
Para atualizar um grupo existente, utilize o cmdlet Set-AzureADGroup. Neste exemplo, vamos está a alterar a propriedade DisplayName do grupo "Os administradores do Intune." Em primeiro lugar, iremos estiver a localizar o grupo utilizando o cmdlet Get-AzureADGroup e filtrar utilizando o atributo DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Em seguida, iremos estiver a alterar a propriedade de descrição para o novo valor de "Administradores de dispositivos do Intune":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Agora se estamos a encontrar o grupo, Vemos que a propriedade Description é atualizada para refletir o novo valor:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Eliminar grupos
Para eliminar grupos do seu diretório, utilize o cmdlet Remove-AzureADGroup da seguinte forma:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Gerir membros de grupos
Se precisar de adicionar novos membros a um grupo, utilize o cmdlet Add-AzureADGroupMember. Este comando adiciona um membro do grupo de administradores do Intune que é utilizado no exemplo anterior:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ObjectId é o ObjectID do grupo a que queremos adicionar um membro, não sendo - RefObjectId ObjectID do utilizador que pretende adicionar como um membro ao grupo.

Para obter os membros de um grupo existentes, utilize o cmdlet Get-AzureADGroupMember, tal como neste exemplo:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Para remover o membro que adicionamos anteriormente ao grupo, utilize o cmdlet Remove-AzureADGroupMember, conforme é mostrado aqui:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Para verificar as associações de grupo de um utilizador, utilize o cmdlet Select-AzureADGroupIdsUserIsMemberOf. Este cmdlet aceita como os respetivos parâmetros ObjectId do utilizador para o qual pretende verificar as associações de grupo e uma lista de grupos para o qual pretende verificar as associações. A lista de grupos têm de ser fornecidos sob a forma de uma variável complexo de tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", por isso, vamos primeiro tem de criar uma variável com esse tipo:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Em seguida, iremos fornecer valores para o groupIds verificar o atributo "GroupIds" desta variável complexas:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Agora, se quisermos verificar as associações de um utilizador com ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea contra os grupos no $g, que devemos utilizar:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


O valor devolvido é uma lista de grupos dos quais este utilizador é membro. Também pode aplicar este método para verificar a associação de contactos, grupos ou principais de serviço para uma determinada lista de grupos, selecione-AzureADGroupIdsContactIsMemberOf, selecione AzureADGroupIdsGroupIsMemberOf a utilizar ou Selecione AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="managing-owners-of-groups"></a>Gerir proprietários de grupos
Para adicionar os proprietários de um grupo, utilize o cmdlet Add-AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ObjectId é o ObjectID do grupo a que queremos adicionar um proprietário, não sendo - RefObjectId ObjectID do utilizador que pretende adicionar como um proprietário do grupo.

Para obter os proprietários de um grupo, utilize o cmdlet Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

O cmdlet devolve a lista de proprietários para o grupo especificado:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Se pretender remover um proprietário de um grupo, utilize o cmdlet Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Aliases reservados 
Quando é criado um grupo, determinado pontos finais de permitir que o utilizador final especificar uma mailNickname ou alias para ser utilizado como parte do endereço de e-mail do grupo. Só é possível criar grupos com os seguintes aliases de e-mail com privilégios elevados por um administrador global do Azure AD. 
  
* abuso 
* Admin 
* Administrador 
* hostmaster 
* majordomo 
* postmaster 
* raiz 
* Proteger 
* Segurança 
* administrador de SSL 
* Webmaster 

## <a name="next-steps"></a>Passos seguintes
Pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
