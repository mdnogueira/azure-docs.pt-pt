---
title: "Como ativar a virtualização aninhada em Azure Virtual Machines | Microsoft Docs"
description: "Como ativar a virtualização aninhada em Azure Virtual Machines"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: philmea
manager: timlt
ms.author: philmea
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: c2f511cd024accc099423f2ed5bbb15d2dd23414
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Como ativar a virtualização aninhada numa VM do Azure

Virtualização aninhada é suportada a série Dv3 e Ev3 de máquinas virtuais do Azure. Esta capacidade fornece uma enorme flexibilidade para suportar cenários tais como ambientes de desenvolvimento, teste, formação e demonstração. 

Passos neste artigo ativar virtualização aninhada numa VM do Azure e configurar a ligação à Internet para que a máquina virtual convidada.

## <a name="create-a-dv3-or-ev3-series-azure-vm"></a>Criar uma série de Dv3 ou Ev3 VM do Azure

Criar uma nova Azure VM do Windows Server 2016 e escolher um tamanho da série de Dv3 ou Ev3. Certifique-se de que escolher um tamanho grande o suficiente para suportar as exigências de uma máquina virtual de convidado. Neste exemplo, estamos a utilizar um tamanho de D3_v3 VM do Azure. 

Pode ver a disponibilidade das máquinas de virtuais série Dv3 ou Ev3 regional [aqui](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Para obter instruções detalhadas sobre como criar uma nova máquina virtual, consulte [criar e gerir VMs do Windows com o módulo Azure PowerShell](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Ligar à VM do Azure

Crie uma ligação de ambiente de trabalho remoto para a máquina virtual.

1. Clique no botão **Ligar** nas propriedades da máquina virtual. É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (ficheiro .rdp).

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) a partir da Mac App Store.

3. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual e, em seguida, clique em **Ok**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Ativar a funcionalidade de Hyper-V na VM do Azure
Pode configurar estas definições manualmente ou fornecemos um script do PowerShell para automatizar a configuração.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opção 1: Utilizar um script do PowerShell para configurar a virtualização de aninhadas
Um script do PowerShell para ativar a virtualização aninhada num anfitrião Windows Server 2016 não está disponível na [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). O script verifica os pré-requisitos e, em seguida, configura virtualização aninhada na VM do Azure. É necessário para concluir a configuração reiniciar a VM do Azure. Este script pode funcionar nos outros ambientes, mas não é garantido. Consulte a mensagem de blogue do Azure com uma demonstração em vídeo em direto virtualização aninhados em execução no Azure! https://aka.MS/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opção 2: Configurar a virtualização de aninhada manualmente

1. Na VM do Azure, abra o PowerShell como administrador. 

2. Ative a funcionalidade Hyper-V e ferramentas de gestão.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Este comando reinicia a VM do Azure. Irá perder a ligação de RDP durante o processo de reinício.
    
3. Depois de reiniciar a VM do Azure, restabeleça a ligação à VM através de RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade à internet para a máquina virtual convidada
Criar um novo adaptador de rede virtual para a máquina virtual do convidado e configurar um Gateway de NAT para ativar a conectividade à Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Criar um comutador de rede virtual de NAT

1. Na VM do Azure, abra o PowerShell como administrador.
   
2. Crie um comutador interno.

    ```powershell
    New-VMSwitch -SwitchName "InternalNATSwitch" -SwitchType Internal
    ```

3. Ver as propriedades do comutador e tenha em atenção o ifIndex para o adaptador de novo.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Tome nota do "ifIndex" para o comutador virtual que acabou de criar.
    
4. Crie um endereço IP para o Gateway de NAT.
    
Para configurar o gateway, terá de algumas informações sobre a sua rede:    
  * IPAddress - o IP do NAT Gateway Especifica o endereço IPv4 ou IPv6 para utilizar como o endereço gateway predefinido para a sub-rede de rede virtual. O formulário genérico é a.b.c.1 (por exemplo, "192.168.0.1"). Enquanto a posição final não tem de ser.1, que normalmente (baseado no comprimento do prefixo). Normalmente, deve utilizar um espaço de endereços de rede privada RFC 1918. 
  * PrefixLength - o comprimento do prefixo de sub-rede define o tamanho da sub-rede local (máscara de sub-rede). O comprimento do prefixo de sub-rede será um valor inteiro entre 0 e 32. 0 seria mapear internet todo, 32 apenas permitiria que um IP mapeado. Comuns intervalo de valores de 24 e 12, dependendo de quantos IPs tem de ser ligado para o NAT. Um PrefixLength comum é 24 – esta é uma máscara de sub-rede de 255.255.255.0.
  * InterfaceIndex - **ifIndex** é o índice da interface do comutador virtual criado no passo anterior. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Criar a rede NAT

Para configurar o gateway, terá de fornecer informações sobre a rede e Gateway de NAT:
  * Nome - este é o nome da rede NAT. 
  * InternalIPInterfaceAddressPrefix - o prefixo de sub-rede NAT descreve tanto o prefixo de IP do Gateway de NAT de acima, bem como o comprimento do prefixo de sub-rede NAT do acima. O formulário genérico será a.b.c.0/NAT comprimento do prefixo de sub-rede. 

No PowerShell, crie uma nova rede NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Criar a máquina virtual convidada

1. Abra o Gestor de Hyper-V e criar uma nova máquina virtual. Configure a máquina virtual para utilizar a nova rede interna que criou.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Instale um sistema operativo na máquina virtual convidada.
    
    >[!NOTE] 
    >
    >É necessário o suporte de dados de instalação para um sistema operativo instalar na VM. Neste caso, estamos a utilizar Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Atribuir um endereço IP para a máquina virtual convidada

Pode atribuir um endereço IP à máquina virtual de convidado manualmente a definição de um endereço IP estático numa máquina virtual convidada ou configurar o DHCP na VM do Azure para atribuir o endereço IP dinamicamente.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opção 1: Configurar DHCP para atribuir dinamicamente um endereço IP para a máquina virtual convidada
Siga os passos abaixo para configurar o DHCP da máquina virtual do anfitrião para a atribuição de endereço dinâmico.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalar o servidor DHCP na VM do Azure

1. Abra o Gestor de servidor. No Dashboard, clique em **para adicionar funções e funcionalidades**. É apresentada a adicionar funções e funcionalidades do assistente.
  
2. No assistente, clique em **seguinte** até a página de funções de servidor.
  
3. Clique para selecionar o **servidor DHCP** caixa de verificação, clique em **adicionar funcionalidades**e, em seguida, clique em **seguinte** até que conclua o assistente.
  
4. Clique em **Instalar**.

#### <a name="configure-a-new-dhcp-scope"></a>Configurar um novo âmbito DHCP

1. Abra o Gestor de DHCP.
  
2. No painel de navegação, expanda o nome do servidor, clique com botão direito **IPv4**e clique em **novo âmbito**. O Assistente de novo âmbito for apresentada, clique em **seguinte**.
  
3. Introduza um nome e descrição para o âmbito e clique em **seguinte**.
  
4. Defina um intervalo de IP para o servidor DHCP (por exemplo, 192.168.0.100 para 192.168.0.200).
  
5. Clique em **seguinte** até a página de Gateway predefinido. Introduza o endereço IP que criou anteriormente (por exemplo, 192.168.0.1) como o Gateway predefinido.
  
6. Clique em **seguinte** até concluir o assistente, deixando todos os valores predefinidos, clique em **concluir**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opção 2: Definir manualmente um endereço IP estático na máquina virtual convidada
Se não tiver configurado o DHCP para atribuir dinamicamente um endereço IP para a máquina virtual convidada, siga estes passos para definir um endereço IP estático.

1. Na VM do Azure, abra o PowerShell como administrador.

2. A máquina virtual convidada com o botão direito e clique em ligar.

3. Inicie sessão na máquina virtual convidada.

4. A máquina virtual convidada, abra o Centro de partilha de rede e.

5. Configure o adaptador de rede para um endereço dentro do intervalo da rede NAT que criou na secção anterior.

Neste exemplo irá utilizar um endereço no intervalo 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testar a conectividade na máquina virtual convidada

A máquina virtual convidada, abra o browser e navegue para uma página web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)
