---
title: "Criar uma máquina virtual do Azure com acelerados rede | Microsoft Docs"
description: "Saiba como criar uma máquina virtual com acelerados da rede."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e887230a102f5c6289ca2eec0e4700a0e1fdfde
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual com acelerados da rede

Neste tutorial, irá aprender a criar uma Máquina Virtual do Azure (VM) com acelerados da rede. Redes na melhoria são GA para Windows e numa pré-visualização pública para as distribuições do Linux específicas. Na melhoria de rede permite que a virtualização de e/s de raiz única (SR-IOV) para uma VM, melhorando em grande medida o desempenho de rede. Este caminho de elevado desempenho ignora o anfitrião datapath reduzindo a latência, interferência e utilização da CPU, para utilização com cargas de trabalho de rede mais demanding em tipos VM suportados. A imagem seguinte mostra a comunicação entre duas máquinas virtuais (VM) com e sem redes na melhoria:

![Comparação](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Sem redes na melhoria, todo o tráfego de rede que entra e sai da VM tem atravessar o anfitrião e o comutador virtual. O comutador virtual fornece todos os imposição de política, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços de rede virtualizado para tráfego de rede. Para obter mais informações sobre comutadores virtuais, leia o [Virtualização de rede do Hyper-V e do comutador virtual](https://technet.microsoft.com/library/jj945275.aspx) artigo.

Com redes na melhoria, o tráfego de rede chega à interface de rede da VM (NIC) e, em seguida, é reencaminhado para a VM. Todas as políticas de rede que se aplica o comutador virtual sem redes na melhoria são descarregadas sendo aplicadas no hardware. Aplicar a política de hardware permite que o NIC para reencaminhar o tráfego de rede diretamente para a VM, ignorando o anfitrião e o comutador virtual, enquanto mantém todos os a política que aplicada no anfitrião.

As vantagens do funcionamento em rede na melhoria só se aplicam à VM que está ativada no. Para obter os melhores resultados, é ideal ativar esta funcionalidade em, pelo menos, duas VMs ligadas à mesma rede de Virtual do Azure (VNet). Quando a comunicação entre VNets ou ligação no local, esta funcionalidade tem um impacto mínimo para geral de latência.

> [!WARNING]
> Nesta pré-visualização pública do Linux não pode ter o mesmo nível de disponibilidade e fiabilidade como versão de funcionalidades que estão em geral disponibilidade. A funcionalidade não é suportada, pode ter restrita capacidades e poderão não estar disponível em todas as localizações do Azure. Para as notificações mais atualizadas à sua disponibilidade e o estado desta funcionalidade, consulte a página de atualizações de rede Virtual do Azure.

## <a name="benefits"></a>Benefícios
* **Reduzir a latência / superiores pacotes por segundo (pps):** removendo o comutador virtual do datapath remove o tempo de pacotes gastam no anfitrião para o processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Reduzido interferência:** comutador Virtual de processamento depende da quantidade de política que tem de ser aplicadas e a carga de trabalho da CPU que está a fazer o processamento. Descarregar a imposição de política para o hardware remove esse variabilidade fornecendo pacotes diretamente para a VM, a remoção do anfitrião para comunicação de VM e todas as interrupções de software e comutadores de contexto.
* **Diminuir a utilização da CPU:** ignorar o comutador virtual no anfitrião leva a menor utilização da CPU para processar o tráfego de rede.

## <a name="Limitations"></a>Limitações
Existem as seguintes limitações ao utilizar esta capacidade:

* **Criação de interface de rede:** Accelerated redes só podem ser ativada para uma NIC de novo. Não pode ser ativada para uma NIC que existente.
* **A criação de VM:** A NIC com redes na melhoria ativada só podem ser anexado a uma VM quando é criada a VM. O NIC não pode ser ligado a uma VM existente.
* **Regiões:** VMs do Windows com redes na melhoria são disponibilizadas em regiões mais do Azure. VMs com Linux com redes na melhoria são disponibilizadas em várias regiões. As regiões esta capacidade está disponível no está a expandir. Consulte o blogue de atualizações de rede Virtual do Azure abaixo para obter as informações mais recentes.   
* **Sistemas operativos suportados:** Windows: Centro de dados do Microsoft Windows Server 2012 R2 e Windows Server 2016. Linux: Ubuntu Server 16.04 LTS com 4.4.0-77 de kernel ou superior, SLES 12 SP2, RHEL 7.3 e CentOS 7.3 (publicado por "Não autorizado Wave Software").
* **Tamanho da VM:** fins gerais e os tamanhos de instância com otimização de computação com oito ou vários núcleos. Para obter mais informações, consulte o [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigos de tamanhos de VM. O conjunto de tamanhos de instância VM suportados irá expandir no futuro.
* **Apenas a implementação através do Azure Resource Manager (ARM):** acelerados redes não está disponível para implementação através da ASM/RDFE.

As alterações a estas limitações sejam anunciadas através do [a rede Virtual do Azure atualiza](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview/) página.

## <a name="create-a-windows-vm"></a>Criar uma VM do Windows
Pode utilizar o portal do Azure ou o Azure [PowerShell](#windows-powershell) para criar a VM.

### <a name="windows-portal"></a>Portal

1. A partir do browser da Internet, abra o Azure [portal](https://portal.azure.com) e inicie sessão com o Azure [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se ainda não tiver uma conta, pode inscrever-se para obter um [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. No portal, clique em **+ novo** > **computação** > **Datacenter do Windows Server 2016**. 
3. No **Datacenter do Windows Server 2016** painel que aparece, deixe *Resource Manager* selecionado em **selecionar um modelo de implementação**e clique em **criar** .
4. No **Noções básicas** painel que aparece, introduza os seguintes valores, deixe as restantes opções predefinidas ou selecione ou introduza os seus próprios valores e clique em de **OK** botão:

    |Definição|Valor|
    |---|---|
    |Nome|MyVm|
    |Grupo de recursos|Deixe **criar nova** selecionada e introduza *MyResourceGroup*|
    |Localização|EUA Oeste 2|

    Se estiver familiarizado com o Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [subscrições](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), e [localizações](https://azure.microsoft.com/regions) (que são também conhecidos como regiões).
5. No **escolher um tamanho** painel apresentado, introduza *8* no **núcleos mínimo** caixa, em seguida, clique em **ver todos os**.
6. Clique em **DS4_V2 padrão**, ou suportada qualquer uma VM, em seguida, clique o **selecione** botão.
7. No **definições** painel que aparece, deixe todas as definições como-é, exceto clique em **ativado** em **acelerados redes**, em seguida, clique no **OK** botão. **Nota:** se, nos passos anteriores, selecionou os valores de tamanho VM, sistema operativo ou localização que não estão listados no [limitações](#Limitations) secção deste artigo, **acelerados redes** não está visível.
8. No **resumo** painel apresentado, clique em de **OK** botão. Azure começa a criar a VM. Criação de VM demora alguns minutos.
9. Para instalar o controlador de rede na melhoria para Windows, concluir os passos a [configurar Windows](#configure-windows) secção deste artigo.

### <a name="windows-powershell"></a>PowerShell
1. Instale a versão mais recente do Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) módulo. Se estiver familiarizado com o Azure PowerShell, leia o [descrição geral do Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo.
2. Iniciar uma sessão do PowerShell ao clicar no botão Iniciar do Windows, escrevendo **powershell**, em seguida, clicar em **PowerShell** de resultados da pesquisa.
3. Na janela do PowerShell, introduza o `login-azurermaccount` comando para iniciar sessão com o Azure [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se ainda não tiver uma conta, pode inscrever-se para obter um [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. No seu browser, copie o seguinte script:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. Na janela do PowerShell, faça duplo clique para cole o script e iniciar a execução-lo. Lhe for pedido um nome de utilizador e palavra-passe. Utilize estas credenciais para iniciar sessão para a VM ao ligar ao mesmo no próximo passo. Se o script falha e alterar os valores no script antes de executar este, confirme os valores utilizados para o tamanho da VM, sistema operativo, e localização estão listadas no [limitações](#Limitations) secção deste artigo.
6. Para instalar o controlador de rede na melhoria para Windows, concluir os passos a [configurar Windows](#configure-windows) secção deste artigo.

### <a name="configure-windows"></a>Configurar o Windows
Depois de criar a VM no Azure, tem de instalar o controlador de rede na melhoria para Windows. Antes de concluir os passos seguintes, tem de ter criado uma VM do Windows com redes na melhoria através de [portal](#windows-portal) ou [PowerShell](#windows-powershell) passos neste artigo. 

1. A partir do browser da Internet, abra o Azure [portal](https://portal.azure.com) e inicie sessão com o Azure [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se ainda não tiver uma conta, pode inscrever-se para obter um [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *MyVm*. Quando **MyVm** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **MyVm** painel apresentado, clique em de **Connect** botão no canto superior esquerdo do painel. **Nota:** se **criar** é visível no **Connect** botão, Azure não ainda acabou de criar a VM. Clique em **Connect** apenas depois de já não vê **criar** sob o **Connect** botão.
4. Permitir que o browser para transferir o **MyVm.rdp** ficheiro.  Depois de transferido, clique no ficheiro para abri-lo. 
5. Clique em de **Connect** clique no botão no **ligação ao ambiente de trabalho remoto** caixa que aparece, notificam que não seja possível identificar o publicador da ligação remota.
6. No **segurança do Windows** caixa apresentada, clique em **mais opções**, em seguida, clique em **utilizar uma conta diferente**. Introduza o nome de utilizador e palavra-passe que introduziu no passo 4, em seguida, clique em de **OK** botão.
7. Clique em de **Sim** botão na caixa de ligação de ambiente de trabalho remoto que aparece, notificam que não é possível verificar a identidade do computador remoto.
8. Faça duplo clique no botão Iniciar do Windows e clique em **Gestor de dispositivos**. Expanda o **adaptadores de rede** nós. Confirme que o **adaptador de Ethernet de função Virtual Mellanox ConnectX 3** for apresentada, conforme mostrado na imagem seguinte:
   
    ![Gestor de dispositivos](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. Na melhoria de redes está agora ativada para a VM.

## <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Pode utilizar o portal do Azure ou o Azure [PowerShell](#linux-powershell) para criar um Ubuntu ou SLES VM. Para RHEL e CentOS VMs é um fluxo de trabalho diferentes.  Consulte as instruções abaixo.

### <a name="linux-portal"></a>Portal
1. Registar o funcionamento em rede na melhoria para pré-visualização do Linux, efetuando os passos 1 a 5 do [criar uma VM com Linux - PowerShell](#linux-powershell) secção deste artigo.  Não é possível registar para a pré-visualização no portal.
2. Conclua os passos 1-8 no [criar uma VM do Windows - portal](#windows-portal) secção deste artigo. No passo 2, clique em **Ubuntu Server 16.04 LTS** em vez de **Datacenter do Windows Server 2016**. Para este tutorial, optar por utilizar uma palavra-passe, em vez de uma chave SSH, embora para implementações de produção, pode utilizar. Se **acelerados redes** não aparecer quando concluir o passo 7 a [criar uma VM do Windows - portal](#windows-portal) secção deste artigo, é provável para uma das seguintes razões:
    - Ainda não está registado para a pré-visualização. Confirme que o seu estado de registo é **registada**, conforme explicado no passo 4 do [criar uma VM com Linux - Powershell](#linux-powershell) secção deste artigo. **Nota:** se participaram na rede de Accelerated para pré-visualização de VMs do Windows (já não é necessário registar a utilizar Accelerated redes para o Windows VMs), não está automaticamente registados para o funcionamento em rede Accelerated para VMs com Linux de pré-visualização. Tem de registar o Accelerated funcionamento em rede para a pré-visualização de VMs com Linux participar no mesmo.
    - Não selecionou um tamanho VM, o sistema operativo, ou localização listadas no [limitações](#limitations) secção deste artigo.
3. Para instalar o controlador de rede na melhoria de Linux, concluir os passos a [configurar Linux](#configure-linux) secção deste artigo.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Se criar VMs com Linux com redes na melhoria numa subscrição e, em seguida, tentar criar uma VM do Windows com redes na melhoria na mesma subscrição, a criação de VM do Windows poderão falhar. Durante esta pré-visualização, recomenda-se que teste o Linux e VMs do Windows com redes na melhoria em subscrições diferentes.
>

1. Instale a versão mais recente do Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) módulo. Se estiver familiarizado com o Azure PowerShell, leia o [descrição geral do Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo.
2. Iniciar uma sessão do PowerShell ao clicar no botão Iniciar do Windows, escrevendo **powershell**, em seguida, clicar em **PowerShell** de resultados da pesquisa.
3. Na janela do PowerShell, introduza o `login-azurermaccount` comando para iniciar sessão com o Azure [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se ainda não tiver uma conta, pode inscrever-se para obter um [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Registar-se a rede na melhoria do Azure pré-visualização, efetuando os seguintes passos:
    - Envie um e-mail para [ axnpreview@microsoft.com ](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com o ID de subscrição do Azure e a utilização pretendida. Aguarde um e-mail de confirmação da Microsoft sobre a sua subscrição que está a ser ativada.
    - Introduza o seguinte comando para confirmar que está registados para a pré-visualização:
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        Não continue com o passo 5 até **registada** aparece na saída depois de introduzir o comando anterior. A saída deve ter o seguinte aspeto a seguinte saída antes de continuar:
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Se participaram na rede de Accelerated para pré-visualização de VMs do Windows (já não é necessário registar a utilizar Accelerated redes para VMs do Windows), a não automaticamente registada para o funcionamento em rede Accelerated para VMs com Linux de pré-visualização. Tem de registar o Accelerated funcionamento em rede para a pré-visualização de VMs com Linux participar no mesmo.
      >
5. No seu browser, copie o script seguinte, substituindo Ubuntu ou SLES conforme pretendido.  Novamente, VM de Redhat e CentOS têm um fluxo de trabalho diferentes descrito abaixo:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. Na janela do PowerShell, faça duplo clique para cole o script e iniciar a execução-lo. Lhe for pedido um nome de utilizador e palavra-passe. Utilize estas credenciais para iniciar sessão para a VM ao ligar ao mesmo no próximo passo. Se o script falhar, confirme se a:
    - Estão registados para a pré-visualização, conforme explicado no passo 4
    - Se alterar o tamanho da VM, tipo de sistema operativo ou valores da localização do script antes de executar esta, se os valores estão listados no [limitações](#Limitations) secção deste artigo.
7. Para instalar o controlador de rede na melhoria de Linux, concluir os passos a [configurar Linux](#configure-linux) secção deste artigo.

### <a name="configure-linux"></a>Configurar Linux

Depois de criar a VM no Azure, tem de instalar o controlador de rede na melhoria de Linux. Antes de concluir os passos seguintes, tem de ter criado uma VM com Linux com redes na melhoria através de [portal](#linux-portal) ou [PowerShell](#linux-powershell) passos neste artigo. 

1. A partir do browser da Internet, abra o Azure [portal](https://portal.azure.com) e inicie sessão com o Azure [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se ainda não tiver uma conta, pode inscrever-se para obter um [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na parte superior do portal, à direita do *procurar recursos* barra, clique em de **> _** ícone para iniciar uma shell de nuvem de deteção (pré-visualização). O painel de shell de nuvem de Bash aparece na parte inferior do portal e após alguns segundos, apresenta um  **username@Azure:~ $** linha. Embora pode SSH para a VM a partir do seu computador, em vez da shell de nuvem, as instruções deste tutorial partem do princípio de que está a utilizar a shell de nuvem.
3. Na parte superior do portal, na caixa que contém o texto *procurar recursos*, tipo *MyVm*. Quando **MyVm** é apresentado nos resultados da pesquisa, clique no mesmo.
4. No **MyVm** painel apresentado, clique em de **Connect** botão no canto superior esquerdo do painel. **Nota:** se **criar** é visível no **Connect** botão, Azure não ainda acabou de criar a VM. Clique em **Connect** apenas depois de já não vê **criar** sob o **Connect** botão.
5. Azure abre uma caixa informá-lo para introduzir o `ssh adminuser@<ipaddress>`. Introduza este comando na nuvem shell (ou cópia na caixa que antes eram no passo 4 e cole-a para a shell de nuvem), em seguida, prima Enter.
6. Introduza **Sim** à pergunta solicitar que se pretender continuar a estabelecer a ligação, em seguida, prima Enter.
7. Introduza a palavra-passe que introduziu ao criar a VM. Uma vez iniciado sessão com êxito para a VM, verá um adminuser@MyVm:~ linha$. Agora são registados à VM através de sessão de shell na nuvem. **Nota:** nuvem shell tempo limite de sessões após 10 minutos de inatividade.

Neste momento, as instruções variam em função de distribuição que está a utilizar. 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. Na linha de comandos, introduza `uname -r` e confirmar a versão para:

    * Ubuntu é "4.4.0-77-generic", ou superior
    * SLES é "4.4.59-92.20-default" ou superior

2. Crie um bond entre o vNIC padrão de rede e o vNIC na melhoria de rede, executando os comandos que se seguem. Tráfego de rede utiliza o mais elevado desempenho na melhoria rede vNIC, enquanto o bond garante que o tráfego de rede não é interrompido em determinadas alterações de configuração.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. Depois de executar o script, a VM será reiniciado depois de um segundo 60 colocar em pausa.
4. Assim que a VM é reiniciada, restabeleça a ligação ao mesmo, efetuando os passos 5 a 7 novamente.
5. Execute o `ifconfig` comando e confirme que bond0 ficar cópias de segurança e a interface está a ser mostrada como cópia de segurança. 
 
 >[!NOTE]
      >As aplicações que utilizam na melhoria de rede têm de comunicar através de *bond0* interface, não *eth0*.  O nome da interface podem ser alteradas antes de rede na melhoria atinge disponibilidade geral.

#### <a name="rhelcentos"></a>RHEL/CentOS

Criar um Red Hat Enterprise Linux ou VM do CentOS 7.3 requer alguns passos adicionais para carregar os controladores mais recentes necessários para a SR-IOV e o controlador de função Virtual (VF) para a placa de rede. A primeira fase das instruções prepara uma imagem que pode ser utilizada para tornar um ou mais máquinas virtuais que tem os controladores previamente carregados.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>A primeira fase: preparar uma imagem de base do Red Hat Enterprise Linux ou CentOS 7.3. 

1.  Aprovisionar um não - SRIOV CentOS 7.3 VM no Azure

2.  Instale o LIS 4.2.2:
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  Transferir ficheiros de configuração
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  Desaprovisionar esta VM

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  No portal do Azure, parar esta VM; e aceda a "Discos" da VM, capturar a OSDisk URI de VHD. Este URI contém o nome do VHD a imagem de base e a respetiva conta de armazenamento. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>Fase dois: aprovisionar novas VMs no Azure

1.  Aprovisionar novas VMs com base com o New-AzureRMVMConfig utilizando a imagem de base de VHD capturado na primeira fase, com AcceleratedNetworking ativado o vNIC:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  Depois de VMs efetuar o arranque, verifique o dispositivo VF por "lspci" e verifique a entrada de Mellanox. Por exemplo, deveremos ver este item na saída lspci:
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  Execute o script de bonding por:

    ```bash
    sudo bondvf.sh
    ```

4.  Reinicie novas VMs:

    ```bash
    sudo reboot
    ```

A VM deve começar com bond0 configurada e o caminho de rede acelerados ativada.  Executar `ifconfig` para confirmar.
