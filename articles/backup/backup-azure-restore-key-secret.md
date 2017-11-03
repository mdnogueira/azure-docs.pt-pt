---
title: "Restaurar a chave de Cofre de chaves e o segredo para VMs encriptadas utilizando a cópia de segurança do Azure | Microsoft Docs"
description: "Saiba como restaurar a chave de Cofre de chaves e o segredo na cópia de segurança do Azure com o PowerShell"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar a chave de Cofre de chaves e o segredo para VMs encriptadas utilizando a cópia de segurança do Azure
Este artigo aborda utilizando cópia de segurança do Azure para efetuar o restauro de VMs do Azure encriptado, se a sua chave e o segredo não existe no Cofre de chaves. Estes passos também podem ser utilizados se pretender manter uma cópia separada da chave (chave de encriptação de chave) e o segredo (chave de encriptação do BitLocker) para a VM restaurada.

## <a name="prerequisites"></a>Pré-requisitos
* **Cópia de segurança encriptado VMs** - encriptados as VMs do Azure tem uma cópia de segurança utilizando a cópia de segurança do Azure. Consulte o artigo [gerir cópia de segurança e restauro de VMs do Azure através do PowerShell](backup-azure-vms-automation.md) para obter detalhes sobre como a cópia de segurança encriptado VMs do Azure.
* **Configurar o Cofre de chaves do Azure** – Certifique-se de que Cofre de chaves para que as chaves e segredos tem de ser restaurada já se encontra presente. Consulte o artigo [introdução ao Cofre de chaves do Azure](../key-vault/key-vault-get-started.md) para obter detalhes sobre a gestão do Cofre de chaves.
* **Restaurar disco** -Certifique-se de que tem acionada as tarefas de restauro de restauro dos discos para a utilização de VM encriptados [PowerShell passos](backup-azure-vms-automation.md#restore-an-azure-vm). Isto acontece porque esta tarefa gera um ficheiro JSON na sua conta de armazenamento que contém chaves e segredos para a VM encriptado para serem restaurados.

## <a name="get-key-and-secret-from-azure-backup"></a>Obter a chave e o segredo do Backup do Azure

> [!NOTE]
> Assim que o disco foi restaurado para a VM encriptada, certifique-se de que:
> 1. $details é preenchido com detalhes de tarefa do disco de restauro, tal como mencionado na [PowerShell os passos em restaurar a secção de discos](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. VM deve ser criada a partir de discos restaurados apenas **após o restauro chave e o segredo ao Cofre de chaves**.
>
>

Consulta as propriedades de disco restauradas para os detalhes da tarefa.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Definir o contexto de armazenamento do Azure e restaurar o ficheiro de configuração JSON que contém a chave e detalhes secretos para a VM encriptado.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Restaurar a chave
Depois do ficheiro JSON é gerado no caminho de destino mencionado acima, gerar ficheiro de blob de chave a partir de JSON e feed para restaurar a chave cmdlet colocar novamente a chave (de chaves KEK) no Cofre de chaves.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Restaurar o segredo
Utilize o ficheiro JSON gerado acima para obter o nome secreto e o valor e feed-la para definir o cmdlet secreto colocar novamente o segredo (BEK) no Cofre de chaves. **Utilize estes cmdlets, se a VM é encriptada utilizando BEK e KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Se a VM **encriptada utilizando apenas BEK**, gerar ficheiro blob segredo a partir de JSON e feed para restaurar o cmdlet secreto para colocar o segredo (BEK) para trás no Cofre de chaves.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. Valor de $secretname pode ser obtido através de referir-se à saída do $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl e utilizar texto após segredos / por exemplo, o URL de segredos de saída é https://keyvaultname.vault.azure.net/secrets/ O nome B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 e secreto é B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. O valor da etiqueta DiskEncryptionKeyFileName é igual ao nome secreto.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Criar máquina virtual a partir do disco restaurado
Se efetuou uma cópia de segurança encriptado VM utilizando a cópia de segurança do Azure, os cmdlets do PowerShell mencionado ajuda que restaurar a chave e o back segredo ao Cofre de chaves. Depois de restaurá-las, consulte o artigo [gerir cópia de segurança e restauro de VMs do Azure através do PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) criar VMs encriptadas a partir de disco restaurado, a chave e o segredo.

## <a name="legacy-approach"></a>Abordagem legada
A abordagem mencionada funciona para todos os pontos de recuperação. No entanto, a abordagem mais antiga de obter a chave e as informações secretas do ponto de recuperação, será válido para pontos de recuperação mais antigos do que 11 de Julho de 2017 para VMs encriptadas utilizando BEK e KEK. Assim que a tarefa de disco de restauro está concluída para a utilização de VM encriptados [PowerShell passos](backup-azure-vms-automation.md#restore-an-azure-vm), certifique-se de que $rp é preenchido com um valor válido.

### <a name="restore-key"></a>Restaurar a chave
Utilize os seguintes cmdlets para obter informações de chaves (KEK) do ponto de recuperação e o feed para restaurar a chave cmdlet para colocá-la novamente no Cofre de chaves.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Restaurar o segredo
Utilize os seguintes cmdlets para obter as informações secretas (BEK) do ponto de recuperação e o feed para definir o cmdlet secreto para colocá-la novamente no Cofre de chaves.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. É possível obter o valor para $secretname ao referir-se à saída do $rp1. KeyAndSecretDetails.SecretUrl e utilizar texto após segredos / por exemplo, o URL de segredos de saída é https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 e é o nome secreto B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. O valor da etiqueta DiskEncryptionKeyFileName é igual ao nome secreto.
> 3. Valor de DiskEncryptionKeyEncryptionKeyURL pode obter o Cofre de chaves depois de restaurar novamente as chaves e utilizar [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet
>
>

## <a name="next-steps"></a>Passos seguintes
Após o restauro de chave e o back segredo ao Cofre de chaves, consulte o artigo [gerir cópia de segurança e restauro de VMs do Azure através do PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) criar VMs encriptadas a partir de disco restaurado, chave e o segredo.
