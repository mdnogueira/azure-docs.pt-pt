---
title: Gerir o Cofre de chaves na pilha do Azure utilizando o PowerShell | Microsoft Docs
description: Saiba como gerir o Cofre de chaves na pilha do Azure utilizando o PowerShell
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: e920ee20268f5f43592e5a27fe82dcf27cb85af1
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-powershell"></a>Gerir o Cofre de chaves na pilha do Azure utilizando o PowerShell

Este artigo ajuda-o a começar a utilizar para criar e gerir o Cofre de chaves na pilha do Azure utilizando o PowerShell. Cmdlets do PowerShell do Cofre de chave descritos neste artigo estão disponíveis como parte do Azure PowerShell SDK. As secções seguintes descrevem os cmdlets do PowerShell que são necessários para:
   - Crie um cofre. 
   - Armazenar e gerir chaves criptográficas e segredos. 
   - Autorize utilizadores ou aplicações para invocar operações no cofre. 

## <a name="prerequisites"></a>Pré-requisitos
* Tem de subscrever uma oferta, que inclui o serviço Cofre de chaves do Azure.
* [Instale o PowerShell para a pilha do Azure](azure-stack-powershell-install.md).  
* [Configurar o Azure pilha ambiente do utilizador PowerShell](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Ativar a sua subscrição de inquilino para operações do Cofre de chaves

Pode emitir quaisquer operações contra um cofre de chaves, terá de Certifique-se de que a sua subscrição de inquilino está ativada para operações do cofre. Para verificar que as operações do cofre estão ativadas, execute o seguinte comando:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```
**Saída**

Se a sua subscrição está ativada para operações do cofre, o resultado mostra "RegistrationState" é igual a "Registada" para todos os tipos de recursos de um cofre de chaves.

![Estado de registo](media/azure-stack-kv-manage-powershell/image1.png)

Se não estiverem ativadas operações do cofre, invoque o seguinte comando para registar o serviço Cofre de chaves na sua subscrição:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Saída**

Se o registo for bem-sucedida, é devolvido o seguinte resultado:

![Registar](media/azure-stack-kv-manage-powershell/image2.png) quando invocar os comandos do Cofre de chaves, poderá obter um erro, tal como "a subscrição não está registada para utilizar o espaço de nomes 'Keyvault'." Se obtiver um erro, confirme que tem [ativado o fornecedor de recursos do Cofre de chaves](#enable-your-tenant-subscription-for-vault-operations) ao seguir as instruções que foram mencionadas anteriormente.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves 

Antes de criar um cofre de chaves, crie um grupo de recursos para que todos os recursos relacionados com o Cofre de chaves existem num grupo de recursos. Utilize o seguinte comando para criar um novo grupo de recursos:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Saída**

![novo grupo de recursos](media/azure-stack-kv-manage-powershell/image3.png)

Agora, utilize o **New-AzureRMKeyVault** comando para criar um cofre de chaves no grupo de recursos que criou anteriormente. Este comando lê três parâmetros obrigatórios: nome do grupo de recursos, nome do Cofre de chaves e localização geográfica. 

Execute o seguinte comando para criar um cofre de chaves:

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```
**Saída**

![Novo cofre de chaves](media/azure-stack-kv-manage-powershell/image4.png)

O resultado deste comando mostra as propriedades do Cofre de chaves que criou. Quando acede a uma aplicação neste cofre, utiliza o **cofre do URI** propriedade apresentada no resultado. Por exemplo, o Cofre Uniform Resource Identifier (URI) neste caso é "https://vault01.vault.local.azurestack.external". As aplicações que interagem com este Cofre de chaves através da REST API tem de utilizar este URI.

Nos serviços de Federação do Active Directory (AD FS)-com base em implementações, quando cria uma chave de cofre utilizando o PowerShell, poderá receber um aviso que indica "política de acesso não está definida. Nenhum utilizador ou a aplicação tem permissão de acesso para utilizar este cofre". Para resolver este problema, defina uma política de acesso para o Cofre utilizando o [Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) comando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value 

#Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation 
```

## <a name="manage-keys-and-secrets"></a>Gerir chaves e segredos

Depois de criar um cofre, utilize os seguintes passos para criar e gerir chaves e segredos no cofre.

### <a name="create-a-key"></a>Criar uma chave

Utilize o **Add-AzureKeyVaultKey** comando para criar ou importar uma chave protegida por software um cofre de chaves. 

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```
O **destino** parâmetro é utilizado para especificar que a chave está protegida de software. Quando a chave é criada com êxito, o comando produz os detalhes da chave criada.

**Saída**

![Nova chave](media/azure-stack-kv-manage-powershell/image5.png)

Agora, pode referenciar a chave criada utilizando o seu respetivo URI. Se criar ou importar uma chave que tem o mesmo nome como uma chave existente, a chave original é atualizada com os valores especificados na nova chave. Pode aceder a versão anterior utilizando o URI de específico da versão da chave. Por exemplo: 

* Utilize "chaves/https://vault10.vault.local.azurestack.external:443/key01" para obter sempre a versão atual. 
* Utilize "https://vault010.vault.local.azurestack.external:443/chaves/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" para obter esta versão específica.

### <a name="get-a-key"></a>Obter uma chave

Utilize o **Get-AzureKeyVaultKey** comando para ler uma chave e os respetivos detalhes.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>Criar um segredo

Utilize o **conjunto AzureKeyVaultSecret** comando para criar ou atualizar um segredo no cofre. Um segredo é criado se ainda não existir. Uma nova versão do segredo do é criada se já existir.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Saída**

![Criar um segredo](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Obter um segredo

Utilize o **Get-AzureKeyVaultSecret** comando para ler um segredo no Cofre de chaves. Este comando pode devolver todos os ou versões específicas de um segredo. 

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

Depois de criar as chaves e segredos, podem autorizar aplicações externas para utilizá-los.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorizar uma aplicação para utilizar uma chave ou segredo

Utilize o **Set-AzureRmKeyVaultAccessPolicy** comando autorizar uma aplicação para aceder a uma chave ou segredo no Cofre de chaves.
No exemplo seguinte, é o nome do cofre *ContosoKeyVault* e a aplicação que pretende autorizar tem um ID de cliente de *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Para autorizar a aplicação, execute o seguinte comando. Opcionalmente, pode especificar o **PermissionsToKeys** parâmetro para definir as permissões para um utilizador, aplicação ou um grupo de segurança.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Se pretender autorizar essa mesma aplicação a ler os segredos no seu Cofre, execute o seguinte cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma VM com uma palavra-passe armazenada no Cofre de chaves](azure-stack-kv-deploy-vm-with-secret.md) 
* [Implementar uma VM com um certificado armazenado no Cofre de chaves](azure-stack-kv-push-secret-into-vm.md)

