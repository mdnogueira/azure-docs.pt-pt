---
title: "Criar uma Máquina Virtual do SQL Server no Azure PowerShell (Gestor de recursos) | Microsoft Docs"
description: "Fornece os passos e scripts do PowerShell para criar uma VM do Azure com imagens de Galeria de máquina virtual do SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/29/2017
ms.author: jroth
ms.openlocfilehash: 33c306258b6be40f2c5cbc016e3c84e36bf61e0d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Aprovisionar uma máquina virtual de SQL Server com o Azure PowerShell (Resource Manager)
> [!div class="op_single_selector"]
> * [Portal](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>Descrição geral
Este tutorial mostra como criar uma única máquina virtual do Azure utilizando o **do Azure Resource Manager** modelo de implementação utilizando cmdlets do PowerShell do Azure. Neste tutorial, iremos criar uma máquina virtual utilizando uma única unidade de disco a partir de uma imagem na galeria do SQL Server. Iremos criar novos fornecedores de armazenamento, rede e recursos de computação que serão utilizados pela máquina virtual. Se tiver fornecedores existentes para qualquer um destes recursos, pode utilizar em vez disso, os fornecedores.

Se tiver a versão clássica deste tópico, consulte [aprovisionar uma máquina virtual de SQL Server utilizando o Azure PowerShell clássico](../classic/ps-sql-create.md).

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial precisa de:

* Uma conta do Azure e subscrição antes de começar. Se não tiver uma, inscreva-se um [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [O Azure PowerShell)](/powershell/azure/overview), versão mínima do 1.4.0 ou posterior (Este tutorial escrita utilizando a versão 1.5.0).
  * Para obter a versão, escreva **Azure Get-Module - ListAvailable**.

## <a name="configure-your-subscription"></a>Configurar a sua subscrição
Abra o Windows PowerShell e estabelecer o acesso à sua conta do Azure executando o cmdlet seguinte. Será apresentada com um início de sessão no ecrã para introduzir as suas credenciais. Utilize o mesmo correio eletrónico e palavra-passe que utiliza para iniciar sessão no portal do Azure.

```PowerShell
Add-AzureRmAccount
```

Depois de iniciar sessão com êxito a consultar algumas informações no ecrã que incluem o nome da subscrição e o ID da sua subscrição predefinida. Esta é a subscrição na qual serão criados os recursos para este tutorial, a menos que alterar para uma subscrição diferente. Se tiver várias subscrições, execute o seguinte cmdlet para devolver uma lista de todas as subscrições:

```PowerShell
Get-AzureRmSubscription
```
Para alterar para outra subscrição, execute o seguinte comando com o seu destino SubscriptionName.

```PowerShell
Select-AzureRmSubscription -SubscriptionName YourTargetSubscriptionName
```

## <a name="define-image-variables"></a>Definir as variáveis de imagem
Para simplificar a facilidade de utilização e a compreensão do script conclusão deste tutorial, iremos irá começar por definir um número de variáveis. Altere os valores de parâmetros como julgar, mas cuidado com de nomenclatura restrições relacionadas com o comprimentos de nome e carateres especiais ao modificar os valores fornecidos.

### <a name="location-and-resource-group"></a>Localização e grupo de recursos
Utilize as duas variáveis para definir a região de dados e o grupo de recursos na qual irá criar os outros recursos para a máquina virtual.

Modifique conforme pretendido e, em seguida, execute os seguintes cmdlets para inicializar estas variáveis.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"
```

### <a name="storage-properties"></a>Propriedades de armazenamento
Utilize as seguintes variáveis para definir a conta de armazenamento e o tipo de armazenamento a serem utilizadas pela máquina virtual.

Modifique conforme pretendido e, em seguida, execute o seguinte cmdlet para inicializar estas variáveis. Tenha em atenção que neste exemplo, estamos a utilizar [armazenamento Premium](../premium-storage.md), que é recomendada para cargas de trabalho de produção. Para obter detalhes sobre esta orientação e outras recomendações, consulte [práticas recomendadas do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propriedades da rede
Utilize as seguintes variáveis para definir a interface de rede, o método de alocação de TCP/IP, o nome de rede virtual, o nome de sub-rede virtual, o intervalo de endereços IP para a rede virtual, o intervalo de endereços IP para a sub-rede e a etiqueta de nome de domínio público para ser utilizado pela rede na máquina virtual.

Modifique conforme pretendido e, em seguida, execute o seguinte cmdlet para inicializar estas variáveis.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = "sqlvm1"
```

### <a name="virtual-machine-properties"></a>Propriedades da máquina virtual
Utilize as seguintes variáveis para definir o nome da máquina virtual, o nome do computador, o tamanho da máquina virtual e o nome do disco de sistema operativo da máquina virtual.

Modifique conforme pretendido e, em seguida, execute o seguinte cmdlet para inicializar estas variáveis.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="image-properties"></a>Propriedades da imagem
Utilize as seguintes variáveis para definir a imagem a utilizar para a máquina virtual. Neste exemplo, a imagem de edição do SQL Server 2016 programador é utilizada. A edição programador livremente estiver licenciada para o desenvolvimento e teste, paga apenas o custo de VM a executar.

Modifique conforme pretendido e, em seguida, execute o seguinte cmdlet para inicializar estas variáveis.

```PowerShell
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "SQLDEV"
$Version = "latest"
```

Tenha em atenção que pode obter uma lista completa das ofertas de imagem do SQL Server com o comando Get-AzureRmVMImageOffer:

```PowerShell
Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'
```

E pode ver os Skus disponíveis para uma oferta com o comando Get-AzureRmVMImageSku. O comando seguinte mostra todos os Skus disponíveis para o **SQL2016SP1 WS2016** oferecem.

```PowerShell
Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer 'SQL2016SP1-WS2016' | Select Skus
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Com o modelo de implementação do Resource Manager, o objeto primeiro que criou é o grupo de recursos. Utilizaremos o [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet para criar um grupo de recursos do Azure e os respetivos recursos com o nome do grupo de recursos e a localização definida pelas variáveis que anteriormente inicializado.

Execute o cmdlet seguinte para criar o novo grupo de recursos.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
A máquina virtual necessita de recursos de armazenamento para o disco do sistema operativo e para os ficheiros de dados e de registo do SQL Server. De simplicidade, iremos criar um único disco para ambos. Pode anexar mais discos posteriormente utilizando o [Azure adicionar disco](/powershell/module/azure/add-azuredisk) cmdlet para colocar os dados do SQL Server e o registo de ficheiros em discos dedicados. Utilizaremos o [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet para criar um padrão de armazenamento e de contas no seu novo grupo de recursos com o nome da conta de armazenamento, o nome do Sku de armazenamento e a localização definidos utilizando as variáveis que anteriormente foi inicializado.

Execute o cmdlet seguinte para criar a sua nova conta de armazenamento.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location
```

## <a name="create-network-resources"></a>Criar recursos de rede
A máquina virtual requer um número de recursos de rede para conectividade de rede.

* Cada máquina virtual necessita de uma rede virtual.
* Uma rede virtual tem de ter pelo menos uma subrede definida.
* Uma interface de rede tem de ser definida com um público ou um endereço IP privado.

### <a name="create-a-virtual-network-subnet-configuration"></a>Criar uma configuração de sub-rede de rede virtual
Iremos irá começar por criar uma configuração de sub-rede para a nossa rede virtual. Para o nosso tutorial, iremos criar uma sub-rede de predefinido utilizando o [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet. Iremos criar a configuração de sub-rede de rede virtual com o prefixo de nome e endereço de sub-rede definido utilizando as variáveis que anteriormente inicializado.

> [!NOTE]
> Pode definir propriedades adicionais da configuração de sub-rede da rede virtual com este cmdlet, mas que está fora do âmbito deste tutorial.

Execute o cmdlet seguinte para criar a configuração de sub-rede virtual.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Em seguida, iremos criar nossa rede virtual a utilizar o [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet. Iremos criar nossa rede virtual no seu novo grupo de recursos, com o nome, a localização e o endereço prefixo definido utilizando as variáveis que anteriormente inicializado e utilizar a configuração de sub-rede que definiu no passo anterior.

Execute o cmdlet seguinte para criar a rede virtual.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público
Agora que temos nossa rede virtual definido, é necessário configurar um endereço IP para conectividade para a máquina virtual. Para este tutorial, iremos criar um endereço IP público a utilizar para suportar a conectividade à Internet de endereçamento de IP dinâmico. Utilizaremos o [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) cmdlet para criar o IP público e de endereços no prevously de grupo que criou recurso com o nome, a localização e o método de alocação e a etiqueta de nome de domínio DNS definidos utilizando as variáveis que inicializar anteriormente.

> [!NOTE]
> Pode definir propriedades adicionais do endereço IP público com este cmdlet, mas que está fora do âmbito deste tutorial inicial. Pode também criar um endereço privado ou um endereço com um endereço estático, mas que também está fora do âmbito deste tutorial.

Execute o cmdlet seguinte para criar o seu endereço IP público.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-interface"></a>Criar a interface de rede
Iremos agora está prontos para criar a interface de rede que irá utilizar o nosso máquina virtual. Utilizaremos o [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) cmdlet para criar a nossa interface de rede no grupo de recursos que criou anteriormente e com o nome, a localização e a sub-rede e o endereço IP público definido anteriormente.

Execute o cmdlet seguinte para criar a sua interface de rede.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id
```

## <a name="configure-a-vm-object"></a>Configurar um objeto VM
Agora que temos de recursos de armazenamento e rede definidos, estamos prontos definir os recursos de computação para a máquina virtual. Para o nosso tutorial, iremos irá especificar o tamanho de máquina virtual e várias propriedades do sistema operativo, especificar a interface de rede que são criados anteriormente, definir o armazenamento de BLOBs e, em seguida, especifique o disco do sistema operativo.

### <a name="create-the-vm-object"></a>Criar o objeto VM
Iremos será iniciada, especificando o tamanho da máquina virtual. Para este tutorial, estamos a especificar um DS13. Utilizaremos o [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) cmdlet para criar um objeto configuráveis máquina virtual com o nome e o tamanho definidos utilizando as variáveis que anteriormente inicializado.

Execute o cmdlet seguinte para criar o objeto de máquina virtual.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Criar um objeto de credencial para conter o nome e a palavra-passe para as credenciais de administrador local
Antes, pode definir as propriedades do sistema operativo da máquina virtual, é necessário fornecer as credenciais da conta de administrador local como uma cadeia segura. Para tal, iremos utilizar o [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

Executar o cmdlet seguinte e, na janela de pedido de credencial do Windows PowerShell, escreva o nome e a palavra-passe para a conta de administrador local em que a máquina virtual do Windows.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Definir as propriedades do sistema operativo da máquina virtual
Agora, está prontos definir propriedades do sistema operativo da máquina virtual. Utilizaremos o [conjunto AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) necessitam de cmdlet para definir o tipo do sistema operativo como Windows, o [agente da máquina virtual](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) instalado, especifique que o cmdlet permite a atualização automática e Defina o nome de máquina virtual, o nome do computador e a credencial utilizando as variáveis que anteriormente inicializado.

Execute o cmdlet seguinte para definir as propriedades do sistema operativo para a máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adicione a interface de rede para a máquina virtual
Em seguida, iremos adicionar a interface de rede que criámos anteriormente para a máquina virtual. Utilizaremos o [adicionar AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) cmdlet para adicionar a interface de rede utilizando a variável de interface de rede definido anteriormente.

Execute o cmdlet seguinte para definir a interface de rede para a máquina virtual.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Defina a localização de armazenamento de BLOBs para o disco a ser utilizado pela máquina virtual
Em seguida, iremos irá definir a localização de armazenamento de BLOBs para o disco a serem utilizadas pela máquina virtual que utiliza as variáveis que definidas anteriormente.

Execute o cmdlet seguinte para definir a localização de armazenamento de Blobs.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Definir o sistema operativo propriedades de disco para a máquina virtual
Em seguida, iremos irá definir o sistema operativo propriedades do disco para a máquina virtual. Utilizaremos o [conjunto AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) cmdlet para especificar que o sistema operativo da máquina virtual vai ser proveniente de uma imagem, para definir a colocação em cache para apenas de leitura (porque do SQL Server está a ser instalado no mesmo disco) e definir virtual nome do computador e o disco do sistema operativo definido utilizando as variáveis que iremos definidos anteriormente.

Execute o cmdlet seguinte para definir o sistema operativo propriedades de disco para a máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especifique a imagem de plataforma para a máquina virtual
É nossa último passo de configuração para especificar a imagem de plataforma para a nossa máquina virtual. Para o nosso tutorial, estamos a utilizar a imagem do SQL Server 2016 CTP mais recente. Utilizaremos o [conjunto AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) cmdlet para utilizar esta imagem, tal como definido pelas variáveis que definidas anteriormente.

Execute o cmdlet seguinte para especificar a imagem de plataforma para a máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Criar a VM do SQL Server
Agora que tiver concluído os passos de configuração, está pronto para criar a máquina virtual. Utilizaremos o [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet para criar a máquina virtual utilizando as variáveis que definiu.

Execute o cmdlet seguinte para criar a máquina virtual.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A máquina virtual é criada. Tenha em atenção que é criada uma conta de armazenamento standard para diagnóstico de arranque, porque a conta de armazenamento especificado para o disco da máquina virtual é uma conta de armazenamento premium.

Agora, pode ver esta máquina no Portal do Azure para ver [respetivo endereço IP público e o respetivo nome de domínio completamente qualificado](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="example-script"></a>Script de exemplo
O script seguinte contém o script do PowerShell concluído para este tutorial. Parte do pressuposto de que configurou já a subscrição do Azure para utilizar com o **Add-AzureRmAccount** e **Select-AzureRmSubscription** comandos.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = "sqlvm1"

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "Enterprise"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

## <a name="next-steps"></a>Passos seguintes
Depois da máquina virtual é criada, está pronto para ligar à máquina virtual com conectividade RDP e o programa de configuração. Para obter mais informações, consulte [ligar a um SQL Server Máquina Virtual no Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
