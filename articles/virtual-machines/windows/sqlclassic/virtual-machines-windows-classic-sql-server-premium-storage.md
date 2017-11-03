---
title: Utilizar o armazenamento Premium do Azure com o SQL Server | Microsoft Docs
description: "Este artigo utiliza os recursos criados com o modelo de implementação clássica e fornece orientações sobre como utilizar o Premium Storage do Azure com o SQL Server em execução em máquinas de virtuais do Azure."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: jroth
ms.openlocfilehash: ad4b5aeed645512774f1a3ecf94de37beff26b22
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilizar o Armazenamento Premium do Azure com o SQL Server em Máquinas Virtuais
## <a name="overview"></a>Descrição geral
[Armazenamento Premium do Azure](../premium-storage.md) é a próxima geração de armazenamento que proporciona baixa latência e e/s de débito elevado. Funciona melhor para a chaves e/s intensivas cargas de trabalho, como o SQL Server em IaaS [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo fornece o planeamento e a orientação para migrar uma Máquina Virtual com o SQL Server para utilizar o armazenamento Premium. Isto inclui a infraestrutura do Azure (armazenamento de rede,) e passos de Windows VM do convidado. O exemplo a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) mostra uma migração completa abrangente de ponto a ponto sobre como mover VMs maior para tirar partido das melhorada armazenamento SSD local com o PowerShell.

É importante compreender o processo de ponto a ponto de através das Premium Storage do Azure com o SQL Server em VMs do IAAS. Isto inclui:

* Identificação dos pré-requisitos para utilizar o armazenamento Premium.
* Exemplos de implementação do SQL Server em IaaS para o Premium Storage para novas implementações.
* Exemplos de migrar as implementações existentes, dois servidores autónomos e implementações que utilizam sempre em grupos de disponibilidade SQL.
* Migração possíveis abordagens.
* Exemplo de completa ponto-a-ponto que mostra os passos do Azure, o Windows e o SQL Server para a migração de uma implementação existente Always On.

Para obter mais informações de fundo no SQL Server em Azure Virtual Machines, consulte [do SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **revisores técnicos:** Luis Carlos Vargas Herring Sanjay Mishra, Pravin Mital, Juergen blogue, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para o Premium Storage
Existem vários pré-requisitos para utilizar o Premium Storage.

### <a name="machine-size"></a>Tamanho da máquina
Para utilizar o Premium Storage, terá de utilizar série DS máquinas virtuais (VM). Se não tiver utilizado máquinas de série DS no seu serviço de nuvem antes, tem de eliminar a VM existente, manter os discos ligados e, em seguida, crie um novo serviço em nuvem antes de recriar a VM como o tamanho de função DS *. Para obter mais informações sobre os tamanhos de Máquina Virtual, consulte [Máquina Virtual e tamanhos do serviço em nuvem do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Serviços em nuvem
Só pode utilizar DS * VMs com o Premium Storage quando forem criadas num novo serviço em nuvem. Se estiver a utilizar SQL Server Always On no Azure, o sempre no serviço de escuta irá referir-se para o endereço interno do Azure ou de IP de Balanceador de carga externo que está associado um serviço em nuvem. Este artigo foca-se sobre como migrar, mantendo a disponibilidade neste cenário.

> [!NOTE]
> Uma série DS * tem de ser a primeira VM for implementada para o novo serviço em nuvem.
>
>

### <a name="regional-vnets"></a>VNETS regionais
Para as VMs DS * tem de configurar o Virtual Network (VNET) que aloja as suas VMs ser regional. Isto "widens" a VNET é permitir que as VMs maior para aprovisionadas nos outros clusters e permitir a comunicação entre elas. Na captura de ecrã seguinte, a localização realçada mostra regionais VNETs, enquanto que o resultado primeiro mostra uma VNET "estreito".

![RegionalVNET][1]

Pode emitir um pedido de suporte da Microsoft para migrar para uma VNET regional, a Microsoft irá efetuar uma alteração, em seguida, para concluir a migração para VNETs regionais, alterar a propriedade AffinityGroup na configuração de rede. Primeiro exportar a configuração de rede no PowerShell e, em seguida, substitua o **AffinityGroup** propriedade no **VirtualNetworkSite** elemento com um **localização** propriedade. Especifique `Location = XXXX` onde `XXXX` é uma região do Azure. Em seguida, importe a configuração de novo.

Por exemplo, considerar a seguinte configuração de VNET:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Para mover este para uma VNET regional na Europa Ocidental, altere a configuração para o seguinte:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Contas de armazenamento
Terá de criar uma nova conta de armazenamento que está configurada para o Premium Storage. Tenha em atenção que a utilização do armazenamento Premium é definida na conta de armazenamento, não em VHDs individuais, no entanto, quando utilizar uma VM de série DS * pode anexar o VHD das contas do Standard Storage e Premium. Pode considerar este se não pretender colocar o VHD de SO para a conta de armazenamento Premium.

O seguinte **New-AzureStorageAccountPowerShell** comando com "Premium_LRS" **tipo** cria uma conta de armazenamento Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Definições da Cache de VHDs
A principal diferença entre a criação de discos que fazem parte de uma conta de armazenamento Premium é a definição de cache do disco. Para discos de volume de dados do SQL Server, é recomendado que utilize '**colocação em cache de leitura**'. Para volumes de registo de transações, a definição de cache do disco deve ser definida como '**nenhum**'. Isto é diferente das recomendações para contas de armazenamento Standard.

Depois de tem sido anexados os VHDs, não é possível alterar a definição de cache. Terá de anular a exposição e reattach o VHD com uma definição de cache atualizado.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows
Pode utilizar [espaços de armazenamento do Windows](https://technet.microsoft.com/library/hh831739.aspx) como fez com armazenamento de padrão anterior, esta ação irá permitir a migração de uma VM que já está a utilizar os espaços de armazenamento. O exemplo na [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (passo 9 e reencaminhar) demonstra o código do Powershell para extrair e importar uma VM com vários VHDs anexados.

Agrupamentos de armazenamento foram utilizados com a conta de armazenamento do Standard Azure para melhorar o débito e reduzir a latência. Poderá encontrar o valor nos testes de agrupamentos de armazenamento com o Premium Storage para novas implementações, mas adicionam complexidade adicional com a configuração de armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como localizar o mapa de discos virtuais do Azure para agrupamentos de armazenamento
Como existem recomendações de definição de cache diferente para os VHDs ligados, pode optar por copiar os VHDs para uma conta de armazenamento Premium. No entanto, quando reattach-las para a série DS nova VM, poderá ter de alterar as definições de cache. É mais simples de aplicar o Premium Storage recomendada definições da cache, quando tiver VHDs separados para os ficheiros de dados do SQL Server e os ficheiros de registo (em vez de um único VHD que contém dados).

> [!NOTE]
> Se tiver os ficheiros de dados e de registo do SQL Server no mesmo volume, a opção de colocação em cache que escolher depende de padrões de acesso de e/s para as cargas de trabalho de base de dados. Testar apenas pode demonstram que opção de colocação em cache é melhor para este cenário.
>
>

No entanto, se estiver a utilizar os espaços de armazenamento do Windows que são constituídos por vários VHDs, terá de observar os scripts originais para identificar ligados que os VHDs estão no conjunto que específicos, por isso, pode configurar as definições da cache em conformidade para cada disco.

Se não tiver script original disponível para mostrar que VHDs mapeiam para o agrupamento de armazenamento, pode utilizar os seguintes passos para determinar o mapeamento de agrupamento de armazenamento do disco.

Para cada disco, utilize os seguintes passos:

1. Obter a lista de discos anexados a VM com o **Get-AzureVM** comando:

    Get-AzureVM - ServiceName <servicename> -nome <vmname> | Get-AzureDataDisk
2. Tenha em atenção o Diskname e LUN.

    ![DisknameAndLUN][2]
3. Ambiente de trabalho remoto para a VM. Em seguida, aceda a **gestão de computadores** | **Gestor de dispositivos** | **unidades de disco**. Observe as propriedades de cada um dos 'Microsoft discos virtuais'

    ![VirtualDiskProperties][3]
4. O número LUN aqui é uma referência para o número LUN que especificar ao anexar o VHD à VM.
5. Para o "Disco Virtual Microsoft', avance para o **detalhes** separador, em seguida, no **propriedade** lista, aceda a **controlador chave**. No **valor**, tenha em atenção o **deslocamento**, que é 0002 na captura de ecrã seguinte. O 0002 indica PhysicalDisk2 que referencia o agrupamento de armazenamento.

    ![VirtualDiskPropertyDetails][4]
6. Para cada agrupamento de armazenamento, informação do Estado da saída os discos associados:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Agora, pode utilizar estas informações para associar ligado VHDs em discos físicos nos agrupamentos de armazenamento.

Depois de ter mapeado os VHDs em discos físicos nos agrupamentos de armazenamento, em seguida, pode anular a exposição e copiá-los através de uma conta de armazenamento Premium, em seguida, anexe-las com a definição de cache correto. Consulte o exemplo a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), os passos 8 a 12. Estes passos mostram como extrair uma configuração de disco da VM anexada VHD para um ficheiro CSV, copiar os VHDs, alterar as definições de cache de configuração do disco e, finalmente, volte a implementar a VM como uma série de DS VM com todos os discos anexados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Largura de banda de armazenamento VM e débito de armazenamento VHD
A quantidade de desempenho de armazenamento depende do tamanho de VM DS * especificado e os tamanhos VHD. As VMs têm allowances diferentes para o número de VHDs que podem ser anexados e a largura de banda máxima que irão suportar (MB/s). Para os números de largura de banda específicos, consulte [Máquina Virtual e tamanhos do serviço em nuvem do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aumento IOPS são atingir com tamanhos de disco maiores. Deve considerar este quando tem em consideração o caminho de migração. Para obter detalhes, [consulte a tabela de IOPS e tipos de disco](../premium-storage.md#scalability-and-performance-targets).

Finalmente, considere a que as VMs têm larguras de banda de outro disco máximo será suportam para todos os discos anexados. Em carga elevada, foi sob saturar à largura de banda máximo de disco disponível para esse tamanho de função da VM. Por exemplo um Standard_DS14 irá suportar até 512 MB/s; Por conseguinte, com três discos P30, foi sob saturar a largura de banda de disco da VM. Mas, neste exemplo, é foi excedido o limite de débito, consoante a mistura de leitura e escrita IOs.

## <a name="new-deployments"></a>Novas implementações
As duas secções seguintes demonstram como pode implementar VMs de SQL Server para o Premium Storage. Tal como mencionado anteriormente, não necessariamente tem de colocar o disco de SO para armazenamento Premium. Pode optar por fazê-lo se são destinar colocar quaisquer cargas de trabalho de e/s intensivas no VHD SO.

O primeiro exemplo demonstra a utilização de imagens de galeria do Azure existentes. O segundo exemplo mostra como utilizar uma imagem VM personalizada que tiver uma conta de armazenamento padrão existente.

> [!NOTE]
> Estes exemplos assumem que já criou uma VNET Regional.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Criar uma nova VM com o armazenamento Premium com a imagem de galeria
O exemplo abaixo mostra como colocar o VHD de SO para armazenamento premium e anexe o VHD de armazenamento Premium. No entanto, pode também colocar o disco de SO numa conta do Standard Storage e, em seguida, anexe os VHDs que residem numa conta do Premium Storage. Ambos os cenários são demonstrados.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Passo 1: Criar uma conta de armazenamento Premium
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Passo 2: Criar um novo serviço em nuvem
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Passo 3: Reservar um VIP de serviço em nuvem (opcional)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Passo 4: Criar um contentor VM
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Passo 5: Colocar OS VHD no Standard ou Premium
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Passo 6: Criar a VM
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Criar uma nova VM ao utilizar o armazenamento Premium com uma imagem personalizada
Este cenário demonstra onde tenha imagens personalizadas existentes que residem numa conta do Standard Storage. Tal como mencionado se pretende colocar o VHD do SO no armazenamento Premium, terá de copiar a imagem que existe na conta do Standard Storage e transferi-los para um armazenamento Premium antes de poder ser utilizada. Se tiver uma imagem no local, pode também utilizar este método para copiar que diretamente para a conta de armazenamento Premium.

#### <a name="step-1-create-storage-account"></a>Passo 1: Criar a conta de armazenamento
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Passo 2-criar serviço em nuvem
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Passo 3: Utilizar a imagem existente
Pode utilizar uma imagem existente. Em alternativa, pode [tirar uma imagem de uma máquina existente](../classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Tenha em atenção a máquina a imagem tem de ser DS * máquina. Assim que tiver a imagem, os passos seguintes mostram como copiá-lo para a conta de armazenamento Premium com a **início AzureStorageBlobCopy** commandlet do PowerShell.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Passo 4: Copiar BLOBs entre contas de armazenamento
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Passo 5: Verificar regularmente o estado de cópia:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Passo 6: Adicionar o disco de imagem para o disco repositório na subscrição do Azure
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Pode considerar que, apesar de os relatórios de estado como êxito, ainda foi possível obter um erro de concessão de disco. Neste caso, aguarde cerca de 10 minutos.
>
>

#### <a name="step-7--build-the-vm"></a>Passo 7: Criar a VM
Aqui está a criar a VM de imagem e anexar dois VHDs de armazenamento Premium:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implementações existentes que não utilizam grupos de disponibilidade Always
> [!NOTE]
> Para as implementações existentes, consulte o [pré-requisitos](#prerequisites-for-premium-storage) secção deste tópico.
>
>

Existem considerações diferentes para implementações do SQL Server que não utilizam grupos de disponibilidade Always e aqueles que efetuar. Se não estiver a utilizar Always On e tiver uma autónoma existente do SQL Server, pode atualizar para o Premium Storage utilizando uma nova conta de serviço e o armazenamento de nuvem. Considere as seguintes opções:

* **Criar uma nova VM do SQL Server**. Pode criar uma nova VM do servidor de SQL Server que utiliza uma conta de armazenamento Premium, conforme documentado nas novas implementações. Em seguida, a cópia de segurança e restaurar as bases de dados do utilizador e a configuração do SQL Server. A aplicação terá de ser atualizado para referenciar o novo SQL Server, se estiver a ser acedido internamente ou externamente. Terá de copiar todos os objetos de 'fora de BD' como se estava a fazer uma migração do lado a lado (SxS) SQL Server. Isto inclui objetos, tais como inícios de sessão, certificados e servidores ligados.
* **Migrar um servidor de VM existente do SQL Server**. Isto irá necessitar de colocar offline a VM do SQL Server, em seguida, transferi-lo para um novo serviço em nuvem, o que inclui todos os VHDs a si ligados a copiar para a conta de armazenamento Premium. Quando a VM fica online, a aplicação será referenciar o nome de anfitrião do servidor como antes. Lembre-se de que o tamanho do disco existente irá afetar as características de desempenho. Por exemplo, um disco 400 GB obtém arredondar por excesso para um P20. Se souber que não necessitar desse desempenho de disco, foi de recrie a VM como uma VM de série DS e anexe o Premium Storage VHDs da especificação de desempenho de tamanho/precisa. Em seguida, pode anular a exposição e reattach os ficheiros de base de dados SQL.

> [!NOTE]
> Quando a cópia dos discos VHD deve estar ciente do tamanho, dependendo do tamanho significa que tipo de disco de armazenamento Premium se enquadram, isto determina a especificação de desempenho de disco. O tamanho do Azure irá arredondar até o disco mais próximo, pelo que o se tiver um disco 400 GB, este irá ser arredondado para um P20. Dependendo dos requisitos de e/s existentes do VHD SO, não poderá ter de migrar isto para uma conta de armazenamento Premium.
>
>

Se o SQL Server é acedido externamente, em seguida, o VIP do serviço de nuvem, a alteração. Também terá DNS, as ACLs e pontos finais de atualização de definições.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implementações existentes que utilizam grupos de disponibilidade Always
> [!NOTE]
> Para as implementações existentes, consulte o [pré-requisitos](#prerequisites-for-premium-storage) secção deste tópico.
>
>

Inicialmente nesta secção veremos como Always On interage com redes do Azure. Em seguida, iremos irá dividir migrações em para dois cenários: as migrações em que pode ser tolerated algum período de indisponibilidade e migrações onde deve atingir o período de indisponibilidade mínimo.

Local no SQL Server em grupos de disponibilidade Always utilizar um serviço de escuta no local que regista um nome de DNS virtual juntamente com um endereço IP que é partilhado entre um ou mais servidores de SQL. Quando os clientes ligam estes são encaminhados através do serviço de escuta IP para o servidor primário do SQL Server. Este é o servidor que possui o recurso de sempre IP nessa altura.

![DeploymentsUseAlways no][6]

No Microsoft Azure que pode ter apenas um endereço IP atribuído a um NIC na VM, por isso, para alcançar a mesma camada de abstração, como no local, Azure utiliza o endereço IP que está atribuído a balanceadores de carga interno/externa (ILB/ELB). O recurso IP que é partilhado entre os servidores está definido para o mesmo IP como o ILB/ELB. Isto é publicado no DNS e o tráfego de cliente é transmitido através do ILB/ELB à réplica primária do SQL Server. O ILB/ELB sabe que o SQL Server é primária, uma vez que utiliza as pesquisas para o recurso sempre IP de pesquisa. No exemplo anterior, as sondas de cada nó que tem um ponto final referenciado pelo ELB/ILB, que responde é o servidor primário do SQL Server.

> [!NOTE]
> O ILB e ELB estão ambos atribuídos a um serviço em nuvem do Azure específico, por conseguinte, qualquer migração para a nuvem no Azure provavelmente significa que o IP de Balanceador de carga será alterado.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrar Always On implementações que podem permitir que alguns períodos de inatividade
Existem dois estratégias a migração de implementações de Always On que permitem que alguns períodos de inatividade:

1. **Adicionar mais réplicas secundárias num sempre num cluster existente**
2. **Migrar para um novo sempre no Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Adicionar mais réplicas secundárias num sempre num cluster existente
Uma estratégia consiste em adicionar mais secundárias ao sempre no grupo de disponibilidade. Terá de adicionar estas para um novo serviço de nuvem e atualizar o serviço de escuta com o IP de Balanceador de carga novo.

##### <a name="points-of-downtime"></a>Pontos de indisponibilidade:
* Validação de cluster.
* Testar as ativações pós-falha Always On para novas bases de dados secundárias.

Se estiver a utilizar agrupamentos de armazenamento do Windows dentro da VM para maior débito de e/s, em seguida, estes serão colocadas offline durante uma validação completa do Cluster. O teste de validação é necessário quando adicionar nós ao cluster. O tempo que demora a executar o teste pode variar, pelo que deverá testar isto no seu ambiente de teste representativo para obter uma hora aproximada da quanto tempo demorará.

Deve aprovisionar a hora em que pode efetuar ativação pós-falha manual e chaos testar em nós adicionados recentemente para assegurar sempre na disponibilidade elevada funciona conforme esperado.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Todas as instâncias do SQL Server onde são utilizados os agrupamentos de armazenamento deve ser interrompida antes da execução de validação.
>
> ##### <a name="high-level-steps"></a>Passos de alto nível
>

1. Crie dois novos servidores do SQL Server no novo serviço em nuvem com o armazenamento Premium ligado.
2. Copie através de cópias de segurança completas e restaurar com **NORECOVERY**.
3. Copie 'fora do utilizador DB' objetos dependentes, tais como inícios de sessão etc.
4. Criar um novo um novo interno carga balanceador (ILB) ou utilizar um balanceador de carga externo (ELB) e, em seguida, configure os pontos finais com balanceamento de carga em ambos os nós de novo.

   > [!NOTE]
   > Verifique todos os nós têm a configuração de ponto final correta antes de continuar
   >
   >
5. Pare o acesso de aplicação do utilizador para o SQL Server (se utilizar agrupamentos de armazenamento).
6. Pare os serviços de motor do SQL Server em todos os nós (se utilizar agrupamentos de armazenamento).
7. Adicione novos nós de cluster e executar a validação completa.
8. Assim que a validação for bem sucedida, inicie todos os serviços do SQL Server.
9. Os registos de transações de cópia de segurança e restaurar bases de dados do utilizador.
10. Adicionar novos nós sempre no grupo de disponibilidade e coloque da replicação em **síncrono**.
11. Adicione o recurso de endereço IP do novo nuvem serviço ILB/ELB através do PowerShell para Always On com base no exemplo de vários site no [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). Clustering do Windows, defina o **possíveis proprietários** do **endereço IP** recursos para os novos nós antigos. Consulte a secção 'Adicionar endereço recurso de IP na mesma sub-rede' o [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Ativação pós-falha de um de nós de novo.
13. Certifique-os novos nós ativações pós-falha de parceiros de ativação pós-falha automática e teste.
14. Remova nós originais do grupo de disponibilidade.

##### <a name="advantages"></a>Vantagens
* Novos servidores do SQL Server pode ser testada (SQL Server e aplicação) antes de serem adicionados ao Always On.
* Pode alterar o tamanho da VM e personalizar o armazenamento aos seus requisitos exatos. No entanto, seria vantajoso manter todos os caminhos de ficheiro de SQL Server da mesma.
* Pode controlar quando a transferência das cópias de segurança da base de dados para réplicas secundárias são iniciados. Isto difere da utilização do Azure **início AzureStorageBlobCopy** commandlet copiar VHDs, uma vez que é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens
* Quando utilizar agrupamentos de armazenamento do Windows, não há tempo de inatividade do Cluster durante a validação do Cluster completo para os novos nós adicionais.
* Consoante a versão do SQL Server e o número de réplicas secundárias existente, poderá não conseguir adicionar mais réplicas secundárias sem remover bases de dados secundárias existentes.
* Pode ser muito tempo de transferência de dados SQL ao configurar as réplicas secundárias.
* Não há custos adicionais durante a migração enquanto tiver novos computadores a executar em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrar para um novo sempre no Cluster
É outra estratégia para criar uma nova sempre no Cluster connosco novas no novo serviço em nuvem e, em seguida, redirecionar os clientes para utilizá-lo.

##### <a name="points-of-downtime"></a>Pontos de indisponibilidade
Não há um período de indisponibilidade quando transferir aplicações e os utilizadores para o serviço de escuta de nova Always On. O período de indisponibilidade depende em:

* O tempo necessário para restaurar cópias de segurança de registos de transação final para as bases de dados em servidores de novo.
* O tempo necessário para atualizar aplicações de cliente para utilizar o novo sempre no serviço de escuta.

##### <a name="advantages"></a>Vantagens
* Pode testar o ambiente de produção real, SQL Server, e as alterações de compilação do SO.
* Tem a opção para personalizar o armazenamento e, possivelmente, reduzir o tamanho da VM. Isto pode resultar numa redução de custos.
* Pode atualizar a compilação do SQL Server ou a versão durante este processo. Também pode atualizar o sistema operativo.
* Anterior sempre em Cluster pode atuar como um destino de reversão sólida.

##### <a name="disadvantages"></a>Desvantagens
* Terá de alterar o nome DNS do serviço de escuta, se pretender que ambos os clusters sempre em execução em simultâneo. Esta ação adiciona a administração sobrecarga durante a migração, como cadeias da aplicação de cliente devem refletir o novo nome de serviço de escuta.
* Tem de implementar um mecanismo de sincronização entre os dois ambientes mantê-los como fechar quanto possível minimizar os requisitos de sincronização final antes da migração.
* Foi adicionada custo durante a migração enquanto tiver o novo ambiente em execução.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrar sempre em implementações de tempo de inatividade mínimo
Existem dois estratégias para migrar implementações Always On para o período de indisponibilidade mínimo:

1. **Utilizar um existente secundário: Site único**
2. **Utilizar réplicas secundárias existentes: multilocal**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utilizar um existente secundário: Site único
Uma estratégia para o período de indisponibilidade mínimo é tirar uma nuvem existente secundária e removê-lo a partir do serviço de nuvem atual. Em seguida, copie os VHDs para a nova conta de armazenamento Premium e criar a VM no novo serviço em nuvem. Em seguida, atualize o serviço de escuta de clustering e de ativação pós-falha.

##### <a name="points-of-downtime"></a>Pontos de indisponibilidade
* Não há um período de indisponibilidade quando atualizar o nó final com o ponto final com balanceamento de carga.
* O restabelecimento de ligação do cliente poderá ser atrasado consoante a configuração de cliente/DNS.
* Não há um período de indisponibilidade adicional se optar por colocar o grupo sempre Cluster offline para trocar os endereços IP. Para evitar esta situação, utilizando uma dependência ou e os possíveis proprietários para o recurso de endereço IP foi adicionado. Consulte a secção 'Adicionar endereço recurso de IP na mesma sub-rede' o [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Quando pretender que o nó adicionado partake na como um sempre no parceiro de ativação pós-falha, terá de adicionar um ponto final do Azure com uma referência ao carregamento com balanceamento de conjunto. Quando executa o **adicionar AzureEndpoint** comando para efetuar este procedimento, abrir ligações atuais que deverá permanecer disponível, mas novas ligações ao serviço de escuta não poderá ser estabelecida até que o Balanceador de carga foi atualizado. Nos testes Isto foi visto a última 90-120seconds, este deve ser testada.
>
>

##### <a name="advantages"></a>Vantagens
* Não existem extra custo tarifas durante a migração.
* Uma migração de um para um.
* Complexidade reduzida.
* Permite uma maior IOPS de SKUs de armazenamento Premium. Quando os discos estão anular a exposição da VM e copiados para o novo serviço em nuvem, uma 3rd terceiros ferramenta pode ser utilizada para aumentar o tamanho do VHD, que fornece throughputs superiores. Para aumentar o tamanho do VHD, consulte este [debate no fórum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desvantagens
* Há uma perda temporária de HA e DR durante a migração.
* Dado que se trata de uma migração de 1:1, terá de utilizar um tamanho mínimo de VM que irão suportar o número de VHDs, pelo que poderá não conseguir downsize as suas VMs.
* Neste cenário, deverá utilizar o Azure **início AzureStorageBlobCopy** commandlet, que é assíncrona. Não há nenhum SLA na conclusão da cópia. A hora em que as cópias varia, embora isto depende em espera na fila, também irá depender da quantidade de dados para transferir. O tempo de cópia aumenta se a transferência for para outro Datacenter do Azure que suporte o Premium Storage noutra região. Se tiver apenas 2 nós, considere uma mitigação possíveis no caso da cópia demora mais que nos testes. Isto pode incluir ideias seguintes.
  * Adicione um nó do SQL Server 3rd temporário para HA antes da migração com o período de indisponibilidade definido.
  * Execute a migração fora do Azure manutenção agendada.
  * Certifique-se de que configurou corretamente o quórum do cluster.  

##### <a name="high-level-steps"></a>Passos de alto nível
Este documento não demonstre um exemplo de ponto a ponto completo, no entanto, a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) fornece detalhes que podem ser aproveitados para efetuar isto.

![MinimalDowntime][8]

* Recolher configuração do disco e remove o nó (não elimine VHDs ligados).
* Criar uma conta do Premium Storage e copie o VHD da conta do Standard Storage
* Criar novo serviço em nuvem e volte a implementar a VM SQL2 no serviço em nuvem. Crie a VM utilizando o VHD de SO original copiado e anexar os VHDs copiados.
* Configurar o ILB / ELB e adicionar pontos finais.
* Atualize o serviço de escuta ao:
  * Colocar offline o sempre no grupo e a atualizar o sempre no serviço de escuta com o ILB nova / endereço IP de ELB.
  * Ou adicionar o recurso de endereço IP do novo nuvem serviço ILB/ELB através do PowerShell para clustering do Windows. Em seguida, defina os possíveis proprietários do recurso de endereço IP para o nó migrado, SQL2 e definir esta opção como dependência ou no nome da rede. Consulte a secção 'Adicionar endereço recurso de IP na mesma sub-rede' o [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Verifique a configuração de DNS/propogation aos clientes.
* Migrar a SQL1 VM e percorrer os passos 2 a 4.
* Se utilizar 5ii passos, em seguida, adicione SQL1 como possível proprietário para o recurso de endereço IP adicionado
* As ativações pós-falha de teste.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utilizar réplicas secundárias existentes: multilocal
Se tiver nós em mais do que um datacenter do Azure (DC) ou se tiver um ambiente híbrido, pode utilizar uma configuração Always On neste ambiente, para minimizar o período de indisponibilidade.

A abordagem é para alterar a sincronização Always On para síncrono para no local ou DC secundário do Azure e, em seguida, ativação pós-falha ao longo do que o SQL Server. Em seguida, copie o VHD para uma conta de armazenamento Premium e volte a implementar a máquina para um novo serviço em nuvem. Atualize o serviço de escuta e, em seguida, voltar a falhar.

##### <a name="points-of-downtime"></a>Pontos de indisponibilidade
O período de indisponibilidade é constituído pelo tempo para ativação pós-falha para o DC e o back alternativo. Além de depender da configuração de cliente/DNS e o restabelecimento de ligação de cliente pode sofrer um atraso.
Considere o seguinte exemplo de uma configuração híbrida Always On:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens
* Pode utilizar a infraestrutura existente.
* Tem a opção de pré-atualização de armazenamento do Azure no DC DR Azure pela primeira vez.
* O armazenamento do Azure de DR DC pode ser reconfigurado.
* Não há um mínimo de dois as ativações pós-falha durante a migração, excluindo as ativações pós-falha de teste.
* Não é necessário mover dados do SQL Server com a cópia de segurança e restaurar.

##### <a name="disadvantages"></a>Desvantagens
* Consoante o acesso de cliente para o SQL Server, poderá haver uma maior latência ao SQL Server está em execução num DC alternativo para a aplicação.
* O tempo de cópia de VHDs para armazenamento Premium pode ser longo. Isto poderá afetar a sua decisão sobre se pretende manter o nó no grupo de disponibilidade. Considere o seguinte para quando trabalho exigente em termos de registo cargas estão em execução durante a migração não é necessário, porque o nó principal vai ter de manter as transações unreplicated no respetivo registo de transações. Por conseguinte, isto pode aumentar significativamente.
* Neste cenário, deverá utilizar o Azure **início AzureStorageBlobCopy** commandlet, que é assíncrona. Não há nenhum SLA na conclusão. A hora em que as cópias varia, embora isto depende em espera na fila, também irá depender da quantidade de dados para transferir. Por conseguinte tiver apenas um nó no seu centro de dados 2nd, que deve tomar passos de mitigação no caso da cópia demora mais que nos testes. Isto pode incluir ideias seguintes.
  * Adicione um nó do SQL Server 2nd temporário para HA antes da migração com o período de indisponibilidade definido.
  * Execute a migração fora do Azure manutenção agendada.
  * Certifique-se de que configurou corretamente o quórum do cluster.

Este cenário pressupõe que tem documentados a instalação e saber como o armazenamento está mapeado para efetuar as alterações para as definições de cache do disco ideal.

##### <a name="high-level-steps"></a>Passos de alto nível
![Multisite2][10]

* Certifique-no local / alternativa Azure DC o principal de servidor do SQL Server e torná-lo a outros automática ativação pós-falha parceiro (AFP).
* Reunir informações de configuração de disco de SQL2 e remove o nó (não elimine VHDs ligados).
* Criar uma conta do Premium Storage e copie o VHD da conta do Standard Storage.
* Criar um novo serviço de nuvem e criar a VM SQL2 com os discos de armazenamento de Premiums ligados.
* Configurar o ILB / ELB e adicionar pontos finais.
* Atualizar o sempre no serviço de escuta com o ILB nova / ELB IP endereço e o teste de ativação pós-falha.
* Verifique a configuração de DNS.
* Altere o AFP para SQL2 e, em seguida, migrar SQL1 e percorrer os passos 2 e 5.
* As ativações pós-falha de teste.
* Mudar o AFP para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apêndice: Migrar uma implementação multilocal sempre no Cluster para o Premium Storage
O resto deste tópico fornece um exemplo de detalhado de conversão de um cluster multilocal Always On para o Premium storage. Também converte-o serviço de escuta de utilizar um balanceador de carga externo (ELB) para um balanceador de carga interno (ILB).

### <a name="environment"></a>Ambiente
* Windows 2 mil 12 / SQL 2 mil 12
* Ficheiros de base de dados 1 em SP
* 2x agrupamentos de armazenamento por nó

![Appendix1][11]

### <a name="vm"></a>VM:
Neste exemplo, vamos demonstrar mover de um ELB para o ILB. ELB estava disponível antes de ILB, pelo que esta ação mostra como mudar este durante a migração.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Passos de pré-instalação: Ligar à subscrição
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Passo 1: Criar nova conta do Storage e o serviço em nuvem
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Passo 2: Aumentar as falhas permitidas nos recursos<Optional>
Em certos recursos que pertencem à sua sempre no grupo de disponibilidade existem limites no número de falhas que podem ocorrer num período, onde o serviço de cluster irá tentar reiniciar o grupo de recursos. Recomenda-se que aumentar este enfrenta são walking através deste procedimento, dado que se não o manualmente as ativações pós-falha de ativação pós-falha e acionador por encerrar as máquinas que pode obter próximo este limite.

Seja prudente duplicar allowance falha, para efetuar este procedimento no Gestor de clusters de ativação pós-falha, aceda às propriedades do grupo de recursos Always On:

![Appendix3][13]

Altere o máximo de falhas para 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Passo 3: Recurso de endereço de IP de adição para o grupo de Cluster<Optional>
Se tiver apenas um endereço IP para o grupo de Cluster e este está alinhada à sub-rede de nuvem, cuidado com, se, acidentalmente colocar offline todos os nós do cluster na nuvem nessa rede, em seguida, o recurso de IP do Cluster e nome de rede de Cluster não será possível fique online. Em caso de este impedirá atualizações para outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Passo 4: Configuração de DNS
Para implementar um uniforme transição depende de como DNS está a ser utilizada e atualizado.
Quando o Always On está instalado, cria um grupo de recursos de Cluster do Windows, se abrir o Gestor de clusters de ativação pós-falha, verá que, no mínimo terá três recursos, são os dois que o documento refere-se a:

* Nome de rede virtual (VNN) – este é o nome DNS que o cliente ligar quando intenção ligar aos servidores SQL através de Always On.
* Recurso de endereço IP – este é o endereço IP associadas a VNN, pode ter mais de uma e numa configuração multilocal tiver um endereço IP por sub-rede/site.

Quando ligar ao SQL Server, o SQL Server Client controladores irão obter os registos DNS associados com o serviço de escuta e tente estabelecer ligação com cada Always On associados o endereço IP, abaixo Vamos discutir alguns fatores que podem influenciar isto.

O número de registos DNS em simultâneo que estão associados com o nome do serviço de escuta depende não apenas o número de endereços IP associados, mas o ' RegisterAllIpProviders'setting Clustering de ativação pós-falha para o recurso de Always ON VNN.

Quando implementa o Always On no Azure existem vários passos para criar o serviço de escuta e endereços IP, tem de configurar manualmente o RegisterAllIpProviders 1, isto é diferente para uma implementação de Always On no local em que já está definida para 1.

Se 'RegisterAllIpProviders' for 0, em seguida, apenas verá um registo DNS no DNS associado com o serviço de escuta:

![Appendix4][14]

Se 'RegisterAllIpProviders' 1:

![Appendix5][15]

O código abaixo será informação do Estado da saída as definições de VNN e defini-lo por si, consulte a nota, para que a alteração tenha efeito, é necessário colocar offline o VNN e ativá-la novamente online, esta colocar o serviço de escuta offline fazendo com que a interrupção de conectividade do cliente.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

No passo de migração posterior, terá de atualizar o serviço de escuta Always On com um endereço IP atualizado que irá fazer referência a um balanceador de carga, este irá implicar uma remoção de recursos de endereço IP e a adição. Após a atualização IP, tem de garantir o novo endereço IP tiver sido atualizado na zona DNS e que os clientes estão a atualizar os respetivos cache DNS local.

Se os clientes residirem num segmento de rede diferente e fazer referência a um servidor DNS diferente, precisa de considerar o que acontece sobre a transferência de zona DNS durante a migração, como a aplicação de restabelecer ligação hora irá ser restrita por, pelo menos, o tempo de transferência de zona de qualquer os novos endereços IP para o serviço de escuta. Se estiver em aqui a restrição de tempo, devem discutir e testar a imposição de uma transferência de zona incremental com as equipas de Windows, e também colocar o registo de anfitrião DNS para um menor tempo para em direto (TTL), por isso, os clientes de atualização. Para obter mais informações, consulte [Incremental transferências de zona](https://technet.microsoft.com/library/cc958973.aspx) e [início DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Por predefinição, o valor de TTL para registo de DNS que estão associados com o serviço de escuta Always On no Azure é segundos 1200. Pode ser útil reduzir esta se estiverem em tempo de restrição durante a migração para garantir que os clientes atualizar os respetivos DNS com o endereço IP atualizado para o serviço de escuta. Pode ver e modificar a configuração através da captura da configuração do VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Tenha em atenção, o inferior a 'HostRecordTTL', irá ocorrer uma maior quantidade de tráfego DNS.

##### <a name="client-application-settings"></a>Definições da aplicação cliente
Se a aplicação de cliente do SQL Server suporta o .net 4.5 SQLClient, em seguida, que pode utilizar ' MULTISUBNETFAILOVER = TRUE' palavra-chave, é recomendado aplicar como permite a ligação mais rápidas sempre no grupo de disponibilidade SQL durante a ativação pós-falha. Enumera através de todos os endereços IP associados a escuta Always On em paralelo e efetua uma velocidade de tentativas de ligação de TCP mais agressiva durante uma ativação pós-falha.

Para obter mais informações sobre as definições acima, consulte [palavra-chave de MultiSubnetFailover e funcionalidades associadas](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte também [SqlClient suporte para elevada disponibilidade, recuperação após desastre](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Passo 5: As definições de quórum do Cluster
Como vai estar a demorar enviados pelo menos um servidor de SQL para baixo um momento, deve modificar a definição de quórum do cluster, se utilizar o testemunho de partilha de ficheiros (FSW) com 2 nós, deve definir o quórum para permitir a maioria de nós e utilizar o voto dinâmico , este é permitir a um único nó permaneça colocado.

    Set-ClusterQuorum -NodeMajority  

Para obter mais informações sobre como gerir e configurar o quórum do cluster, consulte [configurar e gerir o quórum num Cluster de ativação pós-falha do Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Passo 6: Extrair os pontos finais existentes e ACLs
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Guarde estes para um ficheiro de texto.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Passo 7: Alterar modos de replicação e parceiros de ativação pós-falha
Se tiver mais do que 2 SQL Servers, deve alterar a ativação pós-falha da outra secundário noutro DC ou no local para 'Síncrono' e torná-lo um parceiro de ativação pós-falha automática (AFP), isto é, para manter HA enfrenta estiver a efetuar alterações. Pode fazê-lo através de TSQL de modificar apesar SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Passo 8: Remover VM secundária do serviço de nuvem
Deve planear a migração de um nó secundário nuvem em primeiro lugar, se este for atualmente primário, deve iniciar uma ativação pós-falha manual.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 9: Altere as definições no ficheiro CSV a colocação em cache do disco e guardar
Para volumes de dados estes devem ser definidos como READONLY.

Para volumes TLOG estes devem ser definidos como NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Passo 10: Copiar VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Pode verificar o estado de cópia de VHDs para a conta do Premium Storage:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Aguarde até que todas estas são registadas como êxito.

Para obter informações para os blobs individuais:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Passo 11: Disco do SO o registo
    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Passo 12: Importar secundário para o novo serviço em nuvem
O código abaixo também utiliza a opção adicionada aqui que pode importar a máquina e utiliza o VIP retainable.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Passo 13: Criar ILB no novo Svc de nuvem, adicionar carga balanceamento pontos finais e ACLs
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>Passo 14: Atualizar-se sempre em
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Agora, remova o serviço em nuvem antigo endereço IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Passo 15: Verificação de atualizações de DNS
Agora, deve verificar servidores DNS nas redes de cliente do SQL Server e certifique-se de que a clustering adicionou o registo de anfitrião adicionais para o endereço IP adicionado. Se esses servidores DNS não tem atualizado, considere forçar uma transferência de zona DNS e certifique-se de que os clientes na existe sub-rede são capazes de resolver para ambos os sempre em endereços IP, isto é, pelo que não terá de aguardar a replicação de DNS automática.

#### <a name="step-16-reconfigure-always-on"></a>Passo 16: Reconfigurar-se sempre em
Neste momento, aguarde secundário nesse nó que foi migrado totalmente ressincronizar com o nó no local e mude para o nó de replicação síncrona e torná-lo a AFP.  

#### <a name="step-17-migrate-second-node"></a>Passo 17: Migrar o segundo nó
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 18: Altere as definições no ficheiro CSV a colocação em cache do disco e guardar
Para volumes de dados estes devem ser definidos como READONLY.

Para volumes TLOG estes devem ser definidos como NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Passo 19: Criar nova conta do Storage independente para o nó secundário
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Passo 20: Copiar VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Pode verificar o estado de cópia do VHD para todos os VHDs: ForEach ($disk no $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. DiskLabel $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Aguarde até que todas estas são registadas como êxito.

Para obter informações para os blobs individuais:

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Passo 21: Disco do SO o registo
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Passo 22: Adicionar carga balanceamento pontos finais e ACLs
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Passo 23: Ativação pós-falha de teste
Deve permitem agora que o nó migrado sincronizar com o nó de Always On no local, colocá-lo no modo de replicação síncrona e aguarde até que seja sincronizada. Em seguida, a ativação pós-falha do local para o primeiro nó migrada, que é o AFP. Uma vez que trabalhou, altere o último nó migrado para o AFP.

Deve testar as ativações pós-falha entre todos os nós e executar embora chaos testes para garantir que as ativações pós-falha de trabalho como esperado e um manor atempadamente.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Passo 24: Reverter as definições de quórum de cluster / TTL do DNS / Pntrs de ativação pós-falha / definições de sincronização
##### <a name="adding-ip-address-resource-on-same-subnet"></a>A adição de recursos de endereços IP na mesma sub-rede
Se tiver apenas 2 SQL Servers e pretender migrá-los para um novo serviço em nuvem, mas pretender mantê-las na mesma sub-rede, pode evitar colocar offline o serviço de escuta para eliminar sempre no endereço IP original e adicionar o novo endereço IP. Se estiver a migrar as VMs para outra sub-rede não terá de fazê-lo, existirá uma rede de cluster adicionais que fará referência dessa sub-rede.

Depois de ter produzidos secundário migrado e adicionou no novo recurso de endereço IP para o novo serviço de nuvem antes da ativação pós-falha primário existente, deve efetuar estes passos dentro do Gestor de ativação pós-falha do Cluster:

Para adicionar endereço IP, consulte o [apêndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), passo 14.

1. Para o recurso de endereço IP atual, altere o proprietário possíveis para 'Existente principal do SQL Server', no exemplo abaixo, 'dansqlams4':

    ![Appendix13][23]
2. Para o novo recurso do endereço IP, altere o proprietário possíveis para 'Migrado secundário do SQL Server', no exemplo abaixo, 'dansqlams5':

    ![Appendix14][24]
3. Depois de esta está definida possam efetuar ativações pós-falha e, quando o último nó é migrado os possíveis proprietários deve ser editados desse nó é adicionado como possível proprietário:

    ![Appendix15][25]

## <a name="additional-resources"></a>Recursos adicionais
* [Armazenamento Premium do Azure](../premium-storage.md)
* [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server em Virtual Machines do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
