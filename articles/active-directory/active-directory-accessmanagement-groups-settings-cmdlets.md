---
title: "Configurar definições de grupo com o PowerShell no Azure Active Directory | Microsoft Docs"
description: "Como gerir as definições de grupos utilizando cmdlets do Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 06384d1a1fb7fcc36e9ab97e38c6524a7e260140
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para configurar definições de grupo

> [!IMPORTANT]
> Este conteúdo aplica-se apenas a grupos do Office 365. Para obter mais informações sobre como permitir aos utilizadores criar grupos de segurança, defina `Set-MSOLCompanySettings -UsersPermissionToCreateGroupsEnabled $True` conforme descrito em [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Definições de grupos do Office 365 são configuradas utilizando um objeto de definições e um objeto de SettingsTemplate. Inicialmente, não vir quaisquer objetos de definições no seu diretório, porque o diretório está configurado com as predefinições. Para alterar as definições predefinidas, tem de criar um novo objeto de definições através de um modelo de definições. Modelos de definições são definidos pela Microsoft. Existem vários modelos diferentes definições. Para configurar as definições de grupo do Office 365 para o seu diretório, pode utilizar o modelo com o nome "Group.Unified". Configurar definições de grupo do Office 365 num único grupo, utilize o modelo com o nome "Group.Unified.Guest". Este modelo é utilizado para gerir o acesso de convidado para um grupo do Office 365. 

Os cmdlets são parte do módulo do Azure Active Directory PowerShell V2. Para obter instruções sobre como transferir e instalar o módulo no seu computador, consulte o artigo [do Azure Active Directory PowerShell versão 2](https://docs.microsoft.com/powershell/azuread/). Pode instalar a versão de versão 2 do módulo de [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Obter um valor de definições específicas
Se souber o nome da definição de que pretende obter, pode utilizar o cmdlet para obter o valor atual de definições abaixo. Neste exemplo, estamos a obter o valor para uma definição com o nome "UsageGuidelinesUrl." Pode ler que mais sobre as definições do diretório e os respetivos nomes mais baixo neste artigo.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Criar as definições ao nível do diretório
Estes passos criar definições ao nível do diretório, que se aplicam a todos os grupos do Office 365 (Unified grupos) no diretório.

1. Os cmdlets DirectorySettings, tem de especificar o ID de SettingsTemplate que pretende utilizar. Se não souber este ID, este cmdlet devolve a lista de todos os modelos de definições:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Esta chamada de cmdlet devolve todos os modelos que estão disponíveis:
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Para adicionar um URL de orientação de utilização, primeiro tem de obter o objeto de SettingsTemplate que define o valor de URL de orientação de utilização; ou seja, o modelo de Group.Unified:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Em seguida, crie um novo objeto de definições com base em que o modelo:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Em seguida, atualize o valor de orientação de utilização:
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. Por fim, aplique as definições:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Após a conclusão com êxito, o cmdlet devolve o ID do novo objeto de definições:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
Seguem-se as definições especificadas no Group.Unified SettingsTemplate.

| **Definição** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Tipo: booleano<li>Predefinição: VERDADEIRO |O sinalizador que indica se a criação de grupo unificado é permitida no diretório por utilizadores de não administrador. |
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: Cadeia<li>Predefinição: "" |GUID do grupo de segurança para os quais os membros estão autorizados a criar unificados grupos mesmo quando EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: Cadeia<li>Predefinição: "" |Uma ligação para as diretrizes de utilização do grupo. |
|  <ul><li>ClassificationDescriptions<li>Tipo: Cadeia<li>Predefinição: "" | Uma lista delimitada por vírgulas de descrições de classificação. |
|  <ul><li>DefaultClassification<li>Tipo: Cadeia<li>Predefinição: "" | A classificação que está a ser utilizado como a classificação predefinida para um grupo se foi especificado nenhum.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Tipo: Cadeia<li>Predefinição: "" | Não utilize. Não implementado. |
| <ul><li>CustomBlockedWordsList<li>Tipo: Cadeia<li>Predefinição: "" | Não utilize. Não implementado. |
| <ul><li>EnableMSStandardBlockedWords<li>Tipo: booleano<li>Predefinição: "False" | Não utilize
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tipo: booleano<li>Predefinição: False | Valor boleano que indica se é ou não um utilizador convidado pode ser um proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tipo: booleano<li>Predefinição: VERDADEIRO | Valor boleano que indica se é ou não um utilizador convidado pode ter acesso ao conteúdo dos grupos de unificada. |
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: Cadeia<li>Predefinição: "" | O url de uma ligação para as diretrizes de utilização do convidado. |
|  <ul><li>AllowToAddGuests<li>Tipo: booleano<li>Predefinição: VERDADEIRO | Um booleano que indica se é ou não tem permissão para adicionar os convidados a este diretório.|
|  <ul><li>ClassificationList<li>Tipo: Cadeia<li>Predefinição: "" |Uma lista delimitada por vírgulas dos valores de classificação válido que pode ser aplicado aos grupos de unificada. |


## <a name="read-settings-at-the-directory-level"></a>Ler definições ao nível do diretório
Estes passos ler definições ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Ler todas as definições de diretório existente:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Este cmdlet devolve uma lista de todas as definições de diretório:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Ler todas as definições para um grupo específico:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Ler todos os valores de definições de diretório de um objeto de definições de diretório específico, utilizando o GUID de Id de definições:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Este cmdlet devolve os nomes e valores deste objeto de definições para este grupo específico:
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Atualizar as definições de um grupo específico

1. Procurar o modelo de definições denominado "Groups.Unified.Guest"
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Obtenha o objeto de modelo para o modelo de Groups.Unified.Guest:
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Crie um novo objeto de definições do modelo:
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Configure a definição para o valor necessário:
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Crie a nova definição para o grupo necessária no diretório:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Atualizar as definições ao nível do diretório

Estes passos atualizar as definições ao nível do diretório, que são aplicadas a todos os grupos de unificado no diretório. Estes exemplos assumem que já há um objeto de definições no seu diretório.

1. Encontrar o objeto de definições existente:
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Atualize o valor:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Atualize a definição:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Remova as definições ao nível do diretório
Este passo remove as definições ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Referência de sintaxe de cmdlet
Pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Leitura adicional

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
