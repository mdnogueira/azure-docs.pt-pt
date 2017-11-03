---
ms.assetid: 
title: Chaves de conta de armazenamento do Cofre de chaves do Azure
description: "Chaves de conta de armazenamento fornecem uma integração seemless entre o Cofre de chaves do Azure e a chave de acesso com base para a conta de armazenamento do Azure."
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 1d92ffc03b60695c5ff7b6c3d2ac54808c527efd
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-key-vault-storage-account-keys"></a>Chaves de conta de armazenamento do Cofre de chaves do Azure

Antes de chaves de conta do Azure chave de Cofre de armazenamento, os programadores tinham de gerir as suas próprias chaves de conta de armazenamento do Azure (ASA) e rodá-las manualmente ou através de uma automatização externa. Agora, a chave de Cofre de chaves de conta de armazenamento são implementadas como [segredos do Cofre de chaves](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) para autenticar com uma conta de armazenamento do Azure. 

A funcionalidade de chave de conta de armazenamento do Azure (ASA) gere rotação secreta para si. Também remove a necessidade do contacto direto com uma chave ASA por oferta de acesso partilhado assinaturas (SAS) como um método. 

Para obter mais informações sobre contas do Storage do Azure, consulte [contas do storage do Azure sobre](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Interfaces de suporte

Irá encontrar uma lista completa e ligações para as nossas interfaces de programação e scripts no [Guia do programador do Cofre de chave](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>O que gere o Cofre de chaves

O Cofre de chaves efetua várias funções de gestão interno em seu nome quando utilizar chaves de conta de armazenamento gerido.

- O Cofre de chaves do Azure gere as chaves de uma conta de armazenamento do Azure (ASA).
    - Internamente, o Cofre de chaves do Azure pode listar as chaves de (sincronização) com uma conta de armazenamento do Azure.  
    - Gera de novo cofre de chaves do Azure (roda) periodicamente as chaves. 
    - Valores de chave nunca são devolvidos em resposta ao autor da chamada. 
    - O Cofre de chaves do Azure gere as chaves de contas de armazenamento e as contas de armazenamento clássico. 
- O Cofre de chaves do Azure permite-lhe, o proprietário do cofre/objeto, para criar definições de SAS (conta ou o serviço SAS).
    - O valor SAS, criado com a definição de SAS, é devolvido como um segredo através do caminho URI de REST. Para obter mais informações, consulte [operações de conta de armazenamento do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Orientações de nomenclatura

- Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.  
- Um nome de definição de SAS tem de ser 1 102 carateres de comprimento apenas 0-9, a-z, A-Z.

## <a name="developer-experience"></a>Experiência de programação

### <a name="before-azure-key-vault-storage-keys"></a>Antes das chaves de armazenamento do Cofre de chaves do Azure 

Programadores utilizados para precisa de fazer as seguintes práticas com uma chave de conta do storage para aceder ao armazenamento do Azure. 
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Depois das chaves de armazenamento do Cofre de chaves do Azure 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Orientações para programadores

- Permitir apenas o Cofre de chaves para gerir as chaves do ASA. Não tente geri-los por si, irá interferir com processos de Cofre de chaves. 
- Não permitir que as chaves do ASA para serem geridos pelo mais do que um objeto do Cofre de chaves. 
- Se precisar de voltar a gerar as chaves do ASA manualmente, recomendamos que voltar a gerá-los através do Cofre de chaves. 

## <a name="getting-started"></a>Introdução

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuração de permissões de controlo (RBAC) de acesso baseado em funções

A identidade de aplicação do Cofre de chaves do Azure necessita de permissões para *lista* e *regenerar* chaves para uma conta de armazenamento. Configure estas permissões utilizando os seguintes passos:

- Obtenha o ObjectId de identidade do Cofre de chaves do Azure: 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- Atribua a função de operador de chave de armazenamento a identidade de Cofre de chaves do Azure: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Para um tipo de conta clássica, defina o parâmetro de função *"Clássico armazenamento conta chave serviço função de operador."*

## <a name="working-example"></a>Exemplo de trabalho

O exemplo seguinte demonstra a criar um cofre de chaves geridas conta do Storage do Azure e as definições de assinatura de acesso partilhado (SAS) associados.

### <a name="assumptions"></a>Pressupostos

As seguintes declarações são givens para este exemplo de trabalho.

- O recurso de armazenamento está localizado em: */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- O nome do seu Cofre de chaves é: *yourtest1*

### <a name="get-a-service-principal"></a>Obter um principal de serviço

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

O resultado do comando anterior irá incluir o ServicePrincipal que receberá um telefonema nosso *yourKeyVaultServicePrincipalId*. 

### <a name="set-permissions"></a>Defina as permissões

Certifique-se de que tem as permissões de armazenamento definidas como *todos os*. Pode obter yourKeyVaultServicePrincipalId e definir as permissões no cofre utilizando os seguintes comandos.

```powershell
Get-AzureRmADUser -SearchString "your name"
```
Agora procurar o nome e obtenha o ObjectId relacionado, o que irá utilizar ao definir as permissões no cofre.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $yourKeyVaultServicePrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Permitir o acesso

Terá de conceder acesso de serviço Cofre de chaves para as contas de armazenamento, antes de poder criar uma conta de armazenamento gerida e definições de SAS.

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Criar conta de armazenamento

Crie uma conta de armazenamento gerida e duas definições de SAS. A conta SAS fornece acesso ao serviço blob com permissões diferentes.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Regeneração da

Definir o período de regeneração uma vez através dos seguintes comandos.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Definições de SAS do conjunto

Defina as definições de SAS no Cofre de chaves para a sua conta de armazenamento gerido.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Obtenha o token

Obter os tokens SAS correspondentes e efetuar chamadas para o armazenamento.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Criar armazenamento

Repare que tentar aceder com *$sastoken1* falhar, mas que estamos a conseguir aceder com *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Resumo de exemplo

É capaz de acesso a conteúdo com o token SAS que tem acesso de escrita de BLOBs de armazenamento.

### <a name="relevant-powershell-cmdlets"></a>Cmdlets do Powershell relevantes

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [AzureKeyVaultManagedStorageAccount adicionar](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Atualização AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remover AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remover AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Conjunto AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

## <a name="storage-account-onboarding"></a>Integração de conta de armazenamento 

Exemplo: como proprietário do objeto um cofre de chaves que adiciona um objeto de conta de armazenamento para o seu Cofre de chaves do Azure para carregar uma conta de armazenamento.

Durante a integração, Cofre de chaves tem de verificar que a identidade da conta de integração tem permissões para *lista* e *regenerar* chaves de armazenamento. Para verificar a estas permissões, o Cofre de chaves obtém um OBO (no nome de) token do serviço de autenticação, o público-alvo definido para o Azure Resource Manager e faz com que um *lista* chave chamada para o serviço de armazenamento do Azure. Se o *lista* chamada falhar, o Cofre de chave de criação do objecto falha com um código de estado HTTP de *proibido*. As chaves listadas neste modo, são colocadas em cache com o armazenamento de entidade do Cofre de chaves. 

O Cofre de chaves tem de verificar que a identidade tem *regenerar* permissões antes-pode assumir a propriedade de regenerar as chaves. Para verificar que a identidade, através do token OBO, bem como a identidade de terceiros primeiro do Cofre de chaves com estas permissões:

- O Cofre de chaves apresenta uma lista de permissões de RBAC no recurso de conta de armazenamento.
- O Cofre de chaves valida a resposta através de expressão regular de correspondência de ações e não as ações. 

Localizar alguns exemplos de suporte em [Cofre de chaves - geridos amostras de chaves de conta de armazenamento](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Se não tiver a identidade *regenerar* permissões ou se a identidade de terceiros primeiro Cofre de chaves não tiver *lista* ou *regenerar* falha devolver um código de erro apropriada e a mensagem de pedido de permissão, em seguida, a integração. 

O token OBO funcionará apenas quando utilizar originais, aplicações de cliente nativo do PowerShell ou a CLI.

## <a name="other-applications"></a>Outras aplicações

- Tokens SAS, construído de acordo com as chaves de conta de armazenamento do Cofre de chaves, fornecem acesso controlado ainda mais a uma conta de armazenamento do Azure. Para obter mais informações, consulte [utilizar assinaturas de acesso partilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Consultar também

- [Sobre chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blogue da equipa do Cofre de chaves](https://blogs.technet.microsoft.com/kv/)
