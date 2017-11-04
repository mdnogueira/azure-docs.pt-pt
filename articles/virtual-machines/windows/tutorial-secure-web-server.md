---
title: Protege o IIS com certificados SSL no Azure | Microsoft Docs
description: Saiba como proteger o servidor de web IIS com certificados SSL numa VM do Windows no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6567853e9ef3cad63595dc0afe7a793bdc5d972c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Proteger o servidor web IIS com certificados SSL na máquina virtual do Windows no Azure
Para proteger servidores web, um certificado Secure Sockets mais tarde (SSL) pode ser utilizado para encriptar o tráfego da web. Estes certificados SSL podem ser armazenados no Cofre de chaves do Azure e permitem implementações seguras de certificados para máquinas de virtuais (VMs) do Windows no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre de chaves do Azure
> * Gerar ou carregar um certificado para o Cofre de chaves
> * Criar uma VM e instalar o servidor web do IIS
> * Inserir o certificado para a VM e configurar o IIS com um enlace SSL

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).


## <a name="overview"></a>Descrição geral
O Cofre de chaves do Azure salvaguarda as chaves criptográficas e segredos, esses certificados ou palavras-passe. O Cofre de chaves ajuda a simplificar o processo de gestão de certificados e permite-lhe manter o controlo de teclas que aceder a esses certificados. Pode criar um certificado autoassinado no interior do Cofre de chaves ou carregar um certificado existente, fidedigno que já possui.

Em vez de utilizar uma imagem de VM personalizada, incluindo certificados integrada-in, inserir certificados para uma VM em execução. Este processo garante que os certificados mais atualizadas à sua estão instalados num servidor web durante a implementação. Se renovar ou substituir um certificado, também não tem de criar uma nova imagem VM personalizada. Os certificados mais recentes são automaticamente injetados como criar VMs adicionais. Durante o processo de todo, os certificados nunca deixam a plataforma do Azure ou estão expostos num script, o histórico da linha de comandos ou o modelo.


## <a name="create-an-azure-key-vault"></a>Criar um cofre de chaves do Azure
Antes de poder criar um cofre de chaves e certificados, crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupSecureWeb* no *EUA Leste* localização:

```powershell
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Em seguida, crie um cofre de chaves com [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Cada Cofre de chaves requer um nome exclusivo e deve ser todas as letras maiúsculas e minúsculas. Substitua `<mykeyvault>` no exemplo seguinte, com o seu próprio nome exclusivo do Cofre de chaves:

```powershell
$keyvaultName="<mykeyvault>"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Gerar um certificado e armazenar no Cofre de chaves
Para utilização em produção, deve importar um certificado válido assinado por um fornecedor fidedigno com [importação AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). Para este tutorial, o exemplo seguinte mostra como pode gerar um certificado autoassinado com [adicionar AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) que utiliza a política de certificado predefinido do [ Novo AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```powershell
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Definir um administrador do nome de utilizador e palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Agora pode criar a VM com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo seguinte cria os componentes de rede virtual necessário, a configuração do SO e, em seguida, cria uma VM chamada *myVM*:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myVnet" `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleRDP"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 443
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleWWW"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name "myNic" `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2" | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2016-Datacenter" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Demora alguns minutos para que a VM ser criada. O último passo utiliza a extensão de Script personalizado do Azure para instalar o servidor de web IIS com [conjunto AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Adicionar um certificado a VM do Cofre de chaves
Para adicionar o certificado a partir do Cofre de chaves para uma VM, obter o ID do certificado com [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Adicionar o certificado para a VM com [adicionar AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```powershell
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configurar o IIS para utilizar o certificado
Utilizar a extensão de Script personalizado com [conjunto AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para atualizar a configuração do IIS. Esta atualização aplica-se o certificado de IIS injetado do Cofre de chaves e configura o enlace de web:

```powershell
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testar a aplicação web seguro
Obter o endereço IP público da sua VM com [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). O exemplo seguinte obtém o endereço IP para `myPublicIP` criado anteriormente:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIP" | select "IpAddress"
```

Agora pode abrir um browser e introduza `https://<myPublicIP>` na barra de endereço. Para aceitar a segurança de aviso se utilizou um certificado autoassinado, selecione **detalhes** e, em seguida, **avance para a página Web**:

![Aceitar o aviso de segurança do browser web](./media/tutorial-secure-web-server/browser-warning.png)

O Web site do IIS protegido, em seguida, é apresentado como no exemplo seguinte:

![Site do IIS está em execução segura vista](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, protegidos por um servidor web do IIS com um certificado SSL armazenado no Cofre de chaves do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar um cofre de chaves do Azure
> * Gerar ou carregar um certificado para o Cofre de chaves
> * Criar uma VM e instalar o servidor web do IIS
> * Inserir o certificado para a VM e configurar o IIS com um enlace SSL

Siga esta ligação para ver os exemplos de script pré-criadas máquina virtual.

> [!div class="nextstepaction"]
> [Exemplos de script de máquina virtual do Windows](./powershell-samples.md)