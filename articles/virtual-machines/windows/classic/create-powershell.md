---
title: Criar uma VM do Windows com o PowerShell | Microsoft Docs
description: "Crie máquinas virtuais do Windows utilizando o Azure PowerShell e o modelo de implementação clássica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 75c6cf17ee269ae169d9f2f748d0985ca07e454e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Criar uma máquina virtual do Windows PowerShell e o modelo de implementação clássica
> [!div class="op_single_selector"]
> * [Portal do Azure – Windows](tutorial.md)
> * [PowerShell – Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Estes passos mostram como personalizar um conjunto de comandos do PowerShell do Azure que criar e pré-configurar uma máquina virtual baseado no Windows Azure, utilizando uma abordagem de bloco modular. Pode utilizar este processo para rapidamente criar um conjunto de comandos para uma nova máquina virtual baseado no Windows e expanda uma implementação existente ou criar vários conjuntos de comandos que criar rapidamente um dev/teste personalizado ou o ambiente de profissional de TI.

Estes passos siga uma abordagem de fill-em-a-espaços em branco para criar conjuntos de comandos do Azure PowerShell. Esta abordagem poderá ser útil se estiver familiarizado com o PowerShell ou apenas pretende saber quais os valores para especificar para configuração com êxito. Utilizadores avançados de PowerShell podem executar os comandos e substitua os seus próprios valores para as variáveis (as linhas a começar com "$").

Se não tiver o feito, utilize as instruções no [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalar o Azure PowerShell no computador local. Em seguida, abra uma linha de comandos do Windows PowerShell.

## <a name="step-1-add-your-account"></a>Passo 1: Adicionar a sua conta
1. Na linha de comandos do PowerShell, escreva **Add-AzureAccount** e clique em **Enter**. 
2. Escreva o endereço de correio eletrónico associado à sua subscrição do Azure e clique em **continuar**. 
3. Escreva a palavra-passe para a sua conta. 
4. Clique em **sessão**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Passo 2: Definir a sua subscrição e a conta de armazenamento
Defina a sua subscrição do Azure e a conta de armazenamento, executando estes comandos na linha de comandos do Windows PowerShell. Substituir tudo dentro de aspas, incluindo os < e > carateres, com os nomes corretos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Pode obter o nome da subscrição correta da propriedade SubscriptionName de saída do **Get-AzureSubscription** comando. Pode obter o nome da conta de armazenamento correta da propriedade Label do resultado do **Get-AzureStorageAccount** comando depois de executar o **Select-AzureSubscription** comando.

## <a name="step-3-determine-the-imagefamily"></a>Passo 3: Determinar o ImageFamily
Em seguida, precisa de determinar o valor ImageFamily ou uma etiqueta para a imagem específica correspondente a máquina virtual do Azure que pretende criar. Pode obter a lista de valores de ImageFamily disponíveis com este comando.

    Get-AzureVMImage | select ImageFamily -Unique

Seguem-se alguns exemplos de valores de ImageFamily para computadores baseados em Windows:

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise no Windows Server 2012

Se encontrar a imagem que procura, abra uma nova instância do editor de texto de à sua escolha ou o PowerShell Integrated Scripting Environment (ISE). Copie o seguinte para o novo ficheiro de texto ou o ISE do PowerShell, substituindo o valor de ImageFamily.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Em alguns casos, o nome da imagem é na propriedade Label em vez do valor de ImageFamily. Se não encontrar a imagem que está a procurar utilizando a propriedade ImageFamily, lista as imagens pela respetiva propriedade de etiqueta com este comando.

    Get-AzureVMImage | select Label -Unique

Se encontrar a imagem correta com este comando, abra uma nova instância do editor de texto de à sua escolha ou o ISE do PowerShell. Copie o seguinte para o novo ficheiro de texto ou o ISE do PowerShell, substituindo o valor de etiqueta.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>Passo 4: Criar o conjunto de comandos
Criar o resto do comando de definido por copiar o conjunto adequado de blocos abaixo para o novo ficheiro de texto ou o ISE e, em seguida, preencher os valores das variáveis e remover o < e > carateres. Consulte os dois [exemplos](#examples) no final deste artigo para uma ideia do resultado final.

Inicie o comando definir escolhendo uma destes blocos de dois comandos (obrigatório).

Opção 1: Especificar um nome de máquina virtual e um tamanho.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opção 2: Especificar um nome, o tamanho e o nome do conjunto de disponibilidade.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para os valores de InstanceSize D-, DS-ou máquinas virtuais de série de G, consulte [Máquina Virtual e tamanhos do serviço em nuvem do Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Se tiver um Enterprise Agreement com Software Assurance e pretende tirar partido do Windows Server [híbrida utilize benefício](https://azure.microsoft.com/pricing/hybrid-use-benefit/), adicione o **- LicenseType** parâmetro para o  **Novo AzureVMConfig** cmdlet, transmitir o valor **Windows_Server** o típicas para utilizar as maiúsculas e minúsculas.  Certifique-se de que está a utilizar uma imagem que carregou; Não é possível utilizar uma imagem de padrão da galeria com a vantagem de utilizar híbrida.
> 
> 

Opcionalmente, para um computador autónomo do Windows, especifique a conta de administrador local e a palavra-passe.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Escolha uma palavra-passe segura. Para verificar a sua força, consulte [Verificador de palavra-passe: utilizando palavras-passe de forte](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Opcionalmente, para adicionar o computador com o Windows a um domínio do Active Directory existente, especifique a conta de administrador local e a palavra-passe, a domínio e o nome e a palavra-passe de uma conta de domínio.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Para opções de pré-configuração de adicionais para máquinas virtuais baseadas no Windows, consulte a sintaxe para o **Windows** e **WindowsDomain** conjuntos de parâmetros [AzureProvisioningConfig adicionar ](/powershell/module/azure/add-azureprovisioningconfig).

Opcionalmente, atribua a máquina virtual um endereço IP específico, conhecido como um DIP estático.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Pode verificar que está disponível com um endereço IP específico:

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Opcionalmente, atribua a máquina virtual para uma sub-rede específica, numa rede virtual do Azure.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Opcionalmente, adicione um disco de dados individual para a máquina virtual.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Para um controlador de domínio do Active Directory, defina $hcaching para "None".

Opcionalmente, adicione a máquina virtual a um conjunto com balanceamento de carga existente para o tráfego externo.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Por fim, escolha uma destes blocos de comando necessário para criar a máquina virtual.

Opção 1: Crie a máquina virtual num serviço em nuvem existente.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

O nome abreviado do serviço de nuvem é o nome que aparece na lista de serviços em nuvem no portal do Azure ou na lista de grupos de recursos no portal do Azure.

Opção 2: Crie a máquina virtual, um serviço em nuvem existente e a rede virtual.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>Passo 5: Executar o conjunto de comandos
Reveja o conjunto de comandos do Azure PowerShell criadas no seu editor de texto ou o ISE do PowerShell consiste em vários blocos de comandos a partir do passo 4. Certifique-se de que especificou todas as variáveis necessárias e que têm os valores corretos. Certifique-se de que tem de remover todos os também o < e > carateres.

Se estiver a utilizar um editor de texto, copie o conjunto de comandos para a área de transferência e, em seguida, clique com botão direito sua linha de comandos do Windows PowerShell aberta. Isto irá emitir o conjunto de comandos como uma série de comandos do PowerShell e criar a máquina virtual do Azure. Em alternativa, execute o comando definido no ISE do PowerShell.

Se a criar esta máquina virtual novamente ou um semelhante, pode:

* Guarde este comando definido como um ficheiro de script do PowerShell (*.ps1).
* Guardar este comando definido como um runbook de automatização do Azure no **as contas de automatização** secção do portal do Azure.

## <a id="examples"></a>Exemplos
Seguem-se dois exemplos de como utilizar os passos acima para criar conjuntos de comando do PowerShell do Azure que criar baseado no Windows virtual machines do Azure.

### <a name="example-1"></a>Exemplo 1
Preciso de um PowerShell conjunto de comandos criar a máquina virtual inicial para um controlador de domínio do Active Directory que:

* Utiliza a imagem do Windows Server 2012 R2 Datacenter.
* Tem o nome AZDC1.
* É um computador autónomo.
* Tem um disco de dados adicionais de 20 GB.
* Tem o endereço IP estático 192.168.244.4.
* Está na sub-rede da rede virtual AZDatacenter back-end.
* É no serviço de nuvem do Azure-TailspinToys.

Eis o comando correspondente do Azure PowerShell definido para criar esta máquina virtual, com linhas em branco entre cada bloco para legibilidade.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>Exemplo 2
Preciso de um PowerShell conjunto de comandos criar uma máquina virtual para um servidor de linha de negócio que:

* Utiliza a imagem do Windows Server 2012 R2 Datacenter.
* Tem o nome LOB1.
* É um membro do domínio corp.contoso.com.
* Tem um disco de dados adicionais de 200 GB.
* Está na sub-rede da rede virtual AZDatacenter front-end.
* É no serviço de nuvem do Azure-TailspinToys.

Eis o comando correspondente do Azure PowerShell definido para criar esta máquina virtual.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Passos seguintes
Se precisar de um disco de SO que é superior a 127 GB, pode [expanda a unidade de SO](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

