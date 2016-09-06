<properties
    pageTitle="Criar uma VM do Azure com o PowerShell | Microsoft Azure"
    description="Utilize o Azure PowerShell e o Azure Resource Manager para criar facilmente uma nova VM com o Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/07/2016"
    ms.author="davidmu"/>

# Criar uma VM do Windows utilizando o Resource Manager e o PowerShell

Este artigo mostra-lhe como criar rapidamente uma Máquina Virtual do Azure a executar o Windows Server e os recursos de que necessita para utilizar o [Resource Manager](../resource-group-overview.md) e o PowerShell. 

Todos os passos neste artigo são necessários para criar uma máquina virtual e deve demorar cerca de 30 minutos para os efetuar.

## Passo 1: instalar o Azure PowerShell

Consulte o artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecione a subscrição que pretende utilizar e inicie sessão na sua conta do Azure.
        
## Passo 2: criar um grupo de recursos

Primeiro, crie um grupo de recursos.

1. Obtenha uma lista de localizações disponíveis onde os recursos podem ser criados.

        Get-AzureRmLocation | sort Location | Select Location
        
    Deverá ver algo semelhante ao seguinte:
    
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus

2. Substitua o valor de **$locName** por uma localização da lista. Crie a variável.

        $locName = "centralus"
        
3. Substitua o valor do **$rgName** por um nome para o novo grupo de recursos. Crie a variável e o grupo de recursos.

        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Passo 3: criar uma conta do Storage

É necessária uma [Conta do Storage](../storage/storage-introduction.md) para armazenar o disco rígido virtual que é utilizado pela máquina virtual que criar.

1. Substitua o valor do **$stName** por um nome para a conta do Storage. Teste a exclusividade do nome.

        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName

    Se este comando devolver **Verdadeiro**, o nome proposto é exclusivo no Azure. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
    
2. Agora, execute o comando para criar a conta do Storage.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName
        
## Passo 4: criar uma rede virtual

Todas as máquinas virtuais fazem parte de uma [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Substitua o valor de **$subnetName** por um nome para a sub-rede. Crie a variável e a sub-rede.
        
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Substitua o valor de **$vnetName** por um nome para a rede virtual. Crie a variável e a rede virtual com a sub-rede.

        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
    Deve utilizar valores que façam sentido para a sua aplicação e o ambiente.
        
## Passo 5: criar um endereço IP público e uma interface de rede

Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Substitua o valor de **$ipName** por um nome para o endereço IP público. Crie a variável e o endereço IP público.

        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Substitua o valor de **$nicName** por um nome para a interface de rede. Crie a variável e a interface de rede.

        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Passo 6: criar uma máquina virtual

Agora que tem todas as peças no local, é a altura de criar a máquina virtual.

1. Execute o comando para configurar o nome de conta de administrador e a palavra-passe para a máquina virtual.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    A palavra-passe tem de ter entre 8 e 123 carateres e cumprir três dos quatro requisitos de complexidade: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial. Saiba mais sobre os [requisitos de nomes de utilizador e palavras-passe](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).
        
2. Substitua o valor de **$vmName** por um nome para a máquina virtual. Crie a variável e a configuração da máquina virtual.

        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Consulte o artigo [Tamanhos de máquinas virtuais no Azure](virtual-machines-windows-sizes.md) para obter uma lista de tamanhos disponíveis para uma máquina virtual.
    
3. Substitua o valor de **$compName** por um nome de computador para a máquina virtual. Crie a variável e adicione as informações do sistema operativo à configuração.

        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Defina a imagem a utilizar para aprovisionar a máquina virtual. 

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Consulte o artigo [Navegar e selecionar as imagens da máquina virtual do Windows no Azure com o PowerShell ou a CLI](virtual-machines-windows-cli-ps-findimage.md) para obter mais informações acerca da seleção de imagens a utilizar.
        
5. Adicione a interface de rede que criou à configuração.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Substitua o valor de **$blobPath** por um caminho e nome do ficheiro no armazenamento que o disco rígido virtual irá utilizar. Normalmente, o ficheiro do disco rígido virtual é armazenado num contentor, por exemplo, **vhds/WindowsVMosDisk.vhd**. Crie as variáveis.

        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Substitua o valor de **$diskName** por um nome para o disco do sistema operativo. Crie a variável e adicione as informações do disco à configuração.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Por fim, crie a máquina virtual.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Deverá ver o grupo de recursos e todos os respetivos recursos no Portal do Azure e um estado de êxito na janela do PowerShell:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Passos Seguintes

- Se ocorreram problemas com a implementação, um passo seguinte será consultar [Resolução de problemas com implementações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerir a máquina virtual que acabou de criar ao rever [Gerir máquinas virtuais utilizando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).
- Tire partido da utilização de um modelo para criar uma máquina virtual, utilizando as informações em [Criar uma máquina virtual do Windows com um modelo do Resource Manager](virtual-machines-windows-ps-template.md)



<!--HONumber=ago16_HO5-->


