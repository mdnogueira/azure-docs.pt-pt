---
title: Encriptar discos numa VM do Windows no Azure | Microsoft Docs
description: "Como encriptar discos virtuais numa VM do Windows para a segurança avançada com o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/10/2017
ms.author: iainfou
ms.openlocfilehash: 98b42b252a601af090579e3939f3c7ab91c3803b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Como encriptar discos virtuais numa VM do Windows
Para segurança avançada máquina virtual (VM) e conformidade, discos virtuais no Azure podem ser encriptados. Discos estão encriptados com as chaves criptográficas que são protegidas um cofre de chaves do Azure. Pode controla estas chaves criptográficas e pode auditar a sua utilização. Este artigo fornece detalhes sobre como encriptar discos virtuais numa VM do Windows com o Azure PowerShell. Também pode [encriptar uma VM com Linux utilizando o 2.0 CLI do Azure](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Descrição geral de encriptação de disco
Discos virtuais em VMs do Windows são encriptados em descanso ao utilizar o Bitlocker. Não há sem encargos de encriptação de discos virtuais no Azure. As chaves criptográficas são armazenadas no Cofre de chaves do Azure utilizando a proteção de software, ou pode importar ou gerar as suas chaves nos módulos de segurança de Hardware (HSMs) com certificação FIPS 140-2 normas de nível 2. Estas chaves criptográficas são utilizados para encriptar e desencriptar discos virtuais anexados à VM. Manter o controlo destas chaves criptográficas e pode auditar a sua utilização. Um principal de serviço do Azure Active Directory fornece um mecanismo seguro para emitir estas chaves criptográficas como VMs estão ligados à corrente ou desligar.

O processo de encriptação de uma VM é o seguinte:

1. Crie uma chave criptográfica num cofre de chaves do Azure.
2. Configure a chave criptográfica para ser utilizada para encriptação de discos.
3. Para ler a chave criptográfica no Cofre de chaves do Azure, crie um serviço do Azure Active Directory principal com as permissões adequadas.
4. Emita o comando para encriptar os discos virtuais, especificando o Azure Active Directory serviço principal e adequado chave criptográfica a ser utilizado.
5. O principal de serviço do Azure Active Directory pedidos a chave criptográfica necessária a partir do Cofre de chaves do Azure.
6. Os discos virtuais estão encriptados com a chave criptográfica fornecida.

## <a name="encryption-process"></a>Processo de encriptação
Encriptação de disco baseia-se nos seguintes componentes adicionais:

* **O Cofre de chaves do Azure** - utilizado para salvaguardar as chaves criptográficas e segredos utilizados para o processo de encriptação/desencriptação de disco. 
  * Se existir, pode utilizar um cofre de chaves do Azure existente. Não dispõe de dedique um cofre de chaves para encriptação de discos.
  * Separar os limites administrativos e visibilidade chave, pode criar um cofre de chaves dedicado.
* **Azure Active Directory** -processa segura troca de chaves criptográficas necessárias e a autenticação para ações de pedido. 
  * Normalmente, pode utilizar uma instância existente do Azure Active Directory para o alojamento da sua aplicação.
  * O principal de serviço fornece um mecanismo seguro para pedir e ser emitido as chaves criptográficas adequadas. Não estiver a desenvolver uma aplicação real, que se integra com o Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisitos e limitações
Cenários suportados e os requisitos para encriptação de disco de:

* Ativar a encriptação em novas VMs do Windows de imagens do Azure Marketplace ou imagem personalizada do VHD.
* Ativar a encriptação em VMs do Windows existente no Azure.
* Ativar a encriptação em VMs do Windows que são configurados utilizando os espaços de armazenamento.
* A desativação da encriptação nos dados e SO unidades para VMs do Windows.
* Todos os recursos (por exemplo, o Cofre de chaves, a conta de armazenamento e a VM) tem de ser na mesma região do Azure e subscrição.
* O escalão Standard VMs, tais como uma, série D, DS, G e GS VMs.

Encriptação de disco não é atualmente suportada nos seguintes cenários:

* Escalão básico VMs.
* VMs criadas utilizando o modelo de implementação clássica.
* A atualizar as chaves criptográficas numa VM já encriptada.
* Integração com o serviço de gestão de chaves no local.

## <a name="create-azure-key-vault-and-keys"></a>Criar Cofre de chaves do Azure e as chaves
Antes de começar, certifique-se de que a versão mais recente do módulo do Azure PowerShell foi instalada. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Em todos os exemplos de comando, substitua todos os parâmetros de exemplo com os seus próprios nomes, localização e valores de chave. Os exemplos seguintes utilizam uma convenção de *myResourceGroup*, *myKeyVault*, *myVM*, etc.

O primeiro passo é criar um cofre de chaves do Azure para armazenar as chaves criptográficas. O Cofre de chaves do Azure pode armazenar as chaves, os segredos ou palavras-passe que permitem-lhe implementá-los em segurança nos seus serviços e aplicações. Para a encriptação de disco virtual, crie um cofre de chaves para armazenar uma chave criptográfica utilizada para encriptar ou desencriptar os discos virtuais. 

Ativar o fornecedor do Cofre de chaves do Azure na sua subscrição do Azure com [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), em seguida, crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo seguinte cria um nome de grupo de recursos *myResourceGroup* no *EUA Leste* localização:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

O Cofre de chaves do Azure que contém as chaves criptográficas e os recursos de computação associados, como o armazenamento e a própria VM tem de residir na mesma região. Criar um cofre de chaves do Azure com [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) e ativar o Cofre de chaves para utilização com a encriptação de disco. Especifique um nome exclusivo do Cofre de chaves para *keyVaultName* da seguinte forma:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Pode armazenar as chaves criptográficas utilizando software ou proteção de modelo de segurança de Hardware (HSM). Utilizar um HSM necessita de um cofre de chaves de premium. Não há um custos adicionais para criar um premium Cofre de chaves em vez de padrão Cofre de chaves que armazena as chaves protegidas por software. Para criar um cofre de chaves de premium, no passo anterior de adicionar o *- Sku "Premium"* parâmetros. O exemplo seguinte utiliza as chaves protegidas de software, uma vez que criámos um cofre de chaves padrão. 

Para ambos os modelos de proteção, a plataforma do Azure tem de ser concedido acesso ao pedir as chaves criptográficas quando arranca a VM para desencriptar os discos virtuais. Criar uma chave criptográfica no seu Cofre de chaves com [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). O exemplo seguinte cria uma chave denominada *myKey*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Criar o Azure Active Directory principal de serviço
Quando os discos virtuais são encriptados ou desencriptados, especifique uma conta para processar a autenticação e a troca de chaves criptográficas do Cofre de chaves. Esta conta, um principal de serviço do Azure Active Directory, permite que a plataforma do Azure pedir as chaves criptográficas adequadas em nome da VM. Uma instância do Azure Active Directory predefinida está disponível na sua subscrição, apesar de muitas organizações têm dedicado diretórios do Azure Active Directory.

Criar um principal de serviço no Azure Active Directory com [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Para especificar uma palavra-passe segura, siga o [políticas de palavra-passe e restrições no Azure Active Directory](../../active-directory/active-directory-passwords-policy.md):

```powershell
$appName = "My App"
$securePassword = "P@ssword!"
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Para encriptar ou desencriptar discos virtuais com sucesso, as permissões a chave criptográfica armazenada no Cofre de chaves tem de ser definidas para permitir o principal de serviço do Azure Active Directory para ler as chaves. Definir as permissões no seu Cofre de chaves com [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Criar a máquina virtual
Para testar o processo de encriptação, vamos criar uma VM. O exemplo seguinte cria uma VM chamada *myVM* utilizando um *Datacenter do Windows Server 2016* imagem:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "mypublicdns$(Get-Random)"

$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$cred = Get-Credential

$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```


## <a name="encrypt-virtual-machine"></a>Encriptar a máquina virtual
Para encriptar os discos virtuais, pode reunir todos os componentes anteriores:

1. Especifique o principal de serviço do Azure Active Directory e a palavra-passe.
2. Especifique o Cofre de chaves para armazenar os metadados para os discos encriptados.
3. Especifique as chaves criptográficas a utilizar para a encriptação real e a desencriptação.
4. Especifique se pretende encriptar o disco do SO, os discos de dados ou todos.

Encriptar a VM com [conjunto AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) utilizando a chave de Cofre de chaves do Azure e as credenciais de principais de serviço do Azure Active Directory. O exemplo seguinte obtém todas as informações de chaves, em seguida, encripta VM com o nome *myVM*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName $vmName `
    -AadClientID $app.ApplicationId `
    -AadClientSecret $securePassword `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Aceite o pedido para continuar com a encriptação de VM. Reinicia a VM durante o processo. Depois de concluir o processo de encriptação e a VM foi reiniciado, reveja o estado de encriptação com [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName $vmName
```

O resultado é semelhante ao seguinte exemplo:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como gerir o Cofre de chaves do Azure, consulte [configurar o Cofre de chaves para máquinas virtuais](key-vault-setup.md).
* Para obter mais informações sobre a encriptação de disco, tais como preparar uma VM personalizada encriptada para carregar para o Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
