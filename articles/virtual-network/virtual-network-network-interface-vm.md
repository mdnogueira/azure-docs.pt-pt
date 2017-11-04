---
title: "Interfaces de rede para adicionar ou remover máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como interfaces de rede para adicionar ou remover as interfaces de rede de máquinas virtuais."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 7df1dfbea8c985907d5330819dc1e7bf1578aafa
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Interfaces de rede para adicionar ou remover provenientes de máquinas virtuais

Saiba como adicionar uma interface de rede existente, quando criar uma VM ou adicionar ou remover as interfaces de rede de VM existente no Estado parada (desalocada). Uma interface de rede permite que uma Máquina Virtual do Azure (VM) a comunicar com a Internet, do Azure e recursos no local. Uma VM pode ter uma ou mais interfaces de rede. 

Se precisar de adicionar, alterar ou remover os endereços IP para uma interface de rede, leia o [gerir endereços IP da interface de rede](virtual-network-network-interface-addresses.md) artigo. Se é necessário para criar, alterar ou eliminar as interfaces de rede, leia o [gerir interfaces de rede](virtual-network-network-interface.md) artigo.

## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Saiba mais sobre o número de interfaces de rede, cada tamanho de Linux e Windows VM suporta revendo o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigos de tamanhos de VM.
- Inicie sessão para o Azure [portal](https://portal.azure.com), interface de linha de comandos (CLI) do Azure ou o Azure PowerShell com uma conta do Azure. Se ainda não tiver uma conta do Azure, inscreva-se um [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente dos mini-comandos de Azure PowerShell instaladas. Para obter ajuda para comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, [instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente da CLI do Azure instalados. Para obter ajuda para comandos da CLI, escreva `az <command> --help`. Em vez de instalar a CLI e respetivos pré-requisitos, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Para utilizar a Shell de nuvem, clique na Shell de nuvem **> _** na parte superior da parte a [portal](https://portal.azure.com).

## <a name="about"></a>Sobre as VMs e interfaces de rede

Pode adicionar (ligar) uma interface de rede existente para uma VM ao criar a VM, fornecida a interface de rede não estiver atualmente ligada a outra VM. Pode adicionar uma interface de rede para ou remova (desanexar) uma interface de rede a partir de uma VM existente, fornecida a VM se encontra no estado parado (desalocado). Se criar uma VM com o portal do Azure, o portal cria uma interface de rede de com as predefinições. O portal não permite-lhe:

- Especifique uma interface de rede existente para adicionar ao criar a VM
- Criar uma VM com várias interfaces de rede
- Especifique um nome para a interface de rede (o portal cria a interface de rede com um nome predefinido)

Pode utilizar o Azure PowerShell ou a CLI para criar uma interface de rede ou de VM com todos os atributos anteriores que não é possível utilizar o portal para. Antes de concluir as tarefas nas secções seguintes, considere as seguintes restrições e comportamentos:

- Todos os tamanhos de VM suportam, pelo menos, duas interfaces de rede, mas alguns dos tamanhos da VM suportam mais de duas interfaces de rede. No passado, alguns tamanhos de VM apenas suportada uma interface de rede. Para saber mais interfaces de rede quantos cada suporta de tamanho VM, leia o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigos de tamanhos de VM. 
- No passado, interfaces de rede só podem ser adicionadas para VMs que são suportadas várias interfaces de rede e criadas com, pelo menos, duas interfaces de rede. Não foi possível adicionar uma interface de rede a uma VM que foi criada com uma interface de rede, mesmo que o tamanho da VM suportado várias interfaces de rede. Por outro lado, só foi possível remover as interfaces de rede de uma VM com, pelo menos, três interfaces de rede, porque as VMs criadas com a rede, pelo menos, duas interfaces sempre tinham de ter, pelo menos, duas interfaces de rede. Nenhuma destas restrições aplicam-se delas. Agora pode criar uma VM com qualquer número de interfaces de rede (até ao número suportados pelo tamanho da VM) e adicionar ou remover qualquer número de interfaces de rede (nas VMs no Estado parada (desalocada)), desde que a VM tenha sempre, pelo menos, uma interface de rede.
- Por predefinição, a primeira interface de rede numa VM está definida como o *primário* interface de rede. Todas as outras interfaces de rede VM são *secundário* interfaces de rede.
- Interfaces de rede principal são atribuídas um gateway predefinido, os servidores DHCP do Azure, mas as interfaces de rede secundárias não são. Uma vez que as interfaces de rede secundárias não são atribuídas um gateway predefinido, não conseguirem comunicar com os recursos fora da sua sub-rede, por predefinição. Para ativar as interfaces de rede secundárias comunicar com os recursos fora da sua sub-rede, consulte [encaminhamento dentro de um sistema de operativo da máquina virtual com várias interfaces de rede](#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces).
- Por predefinição, todo o tráfego de saída da VM é enviado o endereço IP atribuído à configuração de IP primária da interface de rede principal. Pode controlar qual o endereço IP é utilizado para tráfego de saída dentro do sistema de operativo da VM, mas por predefinição, é através da interface de rede principal.
- No passado, eram necessárias para ter interfaces de rede único ou vários, todas as VMs no mesmo conjunto de disponibilidade. As VMs com qualquer número de interfaces de rede podem agora existir no mesmo conjunto de disponibilidade, até ao número suportado pelo tamanho da VM. Só é possível adicionar uma VM para um conjunto quando é criado apesar de disponibilidade. Para obter mais informações sobre conjuntos de disponibilidade, leia o [gerir a disponibilidade das VMs no Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) artigo.
- Enquanto as interfaces de rede na mesma VM podem ser ligadas a sub-redes diferentes numa VNet, as interfaces de rede devem todos estar ligadas para a mesma VNet.
- Pode adicionar qualquer endereço IP de qualquer configuração de IP de qualquer interface de rede primária ou secundária para um conjunto de back-end de Balanceador de carga do Azure. No passado, apenas o endereço IP primário para a interface de rede principal foi possível adicionar a um conjunto de back-end. Para obter mais informações sobre endereços IP e as configurações, leia o [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md) artigo.
- Eliminar uma VM não elimina as interfaces de rede que estão anexadas ao mesmo. Quando uma VM é eliminada, as interfaces de rede estiverem desligadas da VM. Pode adicionar as interfaces de rede para diferentes VMs ou eliminá-los.
- Se uma interface de rede tiver um endereço IPv6 privado atribuído, poderá anexá-la a uma VM ao criar a VM. Não é possível anexar uma interface de rede com um endereço de IPv6 atribuído a uma VM, depois da VM é criada. Se anexar uma interface de rede com um endereço IPv6 privada atribuído ao criar uma máquina virtual, apenas pode anexar essa interface de rede à máquina virtual, independentemente de quantas interfaces de rede suporta o tamanho da VM. Consulte [endereços IP da interface de rede](virtual-network-network-interface-addresses.md) para saber mais sobre a atribuição de endereços IP a interfaces de rede.

## <a name="vm-create"></a>Adicione as interfaces de rede existente para uma nova VM

Quando cria uma VM através do portal, o portal cria uma interface de rede com as predefinições e anexa-lo para a VM para si. Não é possível adicionar as interfaces de rede existente para uma nova VM ou crie uma VM com várias interfaces de rede, utilizando o portal do Azure. Pode efetuar ambas as utilizando a CLI ou PowerShell. Pode adicionar tantos interfaces de rede a uma VM dado que suporta o tamanho da VM que está a criar. Para saber mais sobre o número de interfaces de rede suporta cada tamanho da VM, leia o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigos de tamanhos de VM. Atualmente não não possível expor interfaces de rede, que adicionar a uma VM para outra VM. Para saber mais sobre a criação de interfaces de rede, leia o [gerir interfaces de rede](virtual-network-network-interface.md#create-a-network-interface) artigo.

### <a name="routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces"></a>Encaminhamento dentro de um sistema de operativo da máquina virtual com várias interfaces de rede

Azure atribui um gateway predefinido para a primeira interface de rede (principal) ligada à máquina virtual. Azure não atribuir um gateway predefinido para interfaces de rede (secundário) adicionais ligado a uma máquina virtual. Por conseguinte, não é possível comunicar com recursos fora da sub-rede de uma interface de rede secundária, por predefinição. Interfaces de rede secundárias podem, no entanto, comunicar com recursos fora da sua sub-rede, apesar dos passos para ativar a comunicação são diferentes para sistemas operativos diferentes.

### <a name="windows"></a>Windows

Conclua os seguintes passos de uma linha de comandos do Windows:

1. Execute o `route print` comando, que devolve o resultado semelhante ao seguinte resultado de uma máquina virtual com duas interfaces de rede anexada:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Neste exemplo, **4 de # do adaptador de rede do Microsoft Hyper-V** (interface 7) é a interface de rede secundárias que não tem um gateway predefinido atribuído ao mesmo.

2. A partir de uma linha de comandos, execute o `ipconfig` comando para ver qual o endereço IP está atribuído à interface de rede secundária. Neste exemplo, 192.168.2.4 é atribuído a interface 7. Nenhum endereço de gateway predefinido é devolvido para a interface de rede secundárias.

3. Para encaminhar todo o tráfego destinado a endereços fora da sub-rede da interface de rede secundárias para o gateway para a sub-rede, execute o seguinte comando:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    O endereço de gateway para a sub-rede é o primeiro endereço IP (que termine em.1) no intervalo de endereços definido para a sub-rede. Se não pretender encaminhar todo o tráfego fora da sub-rede, pode adicionar rotas individuais para determinados destinos, em vez disso. Por exemplo, se pretendesse apenas encaminhar o tráfego da interface de rede secundária para a 192.168.3.0 rede, introduza o comando:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Para confirmar a comunicação com êxito com um recurso no 192.168.3.0 rede, por exemplo, introduza o seguinte comando para executar o ping 192.168.3.4 utilizando a interface 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Terá de abrir ICMP através da firewall do Windows do dispositivo que está a efetuar o ping com o seguinte comando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Para confirmar a rota adicionada é na tabela de rota, introduza o `route print` comando, que devolve o resultado semelhante ao seguinte texto:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    A rota listada com *192.168.1.1* em **Gateway**, a rota existe por predefinição para a interface de rede principal. A rota com *192.168.2.1* em **Gateway**, a rota que adicionou.

### <a name="linux"></a>Linux

Uma vez que o comportamento predefinido utiliza anfitrião fraca encaminhamento, recomendamos que restringir o tráfego da interface de rede secundária entre os recursos na mesma sub-rede. Se necessitar de comunicação fora da sub-rede para interfaces de rede secundárias, tem de criar regras de encaminhamento que permitem que a máquina virtual para enviar e receber tráfego através de uma interface de rede específicas. Caso contrário, o tráfego que pertença a eth1, por exemplo, não é possível processar corretamente, a rota predefinida definida. Para saber como configurar as regras de encaminhamento, consulte [Linux configurar para vários NICs](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics).

> [!WARNING]
> Se uma interface de rede tiver um endereço IPv6 privado atribuído, só é possível adicionar a interface de rede para a máquina virtual ao criar a máquina virtual. Não é possível anexar mais de uma interface de rede para a máquina virtual, quando criar a máquina virtual ou após a máquina virtual está criada, quanto IPv6 endereço é atribuído a uma interface de rede ligada a uma máquina virtual. Consulte [endereços IP da interface de rede](virtual-network-network-interface-addresses.md) para saber mais sobre a atribuição de endereços IP a interfaces de rede.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[Criar AZ vm](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Novo-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Adicione uma interface de rede existente para uma VM existente

Pode adicionar tantos interfaces de rede a uma VM conforme o tamanho VM que estiver a adicionar as interfaces de rede para suportar. Para saber mais interfaces de rede quantos cada suporta de tamanho VM, leia o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigos de tamanhos de VM. A VM que pretende adicionar uma interface de rede tem de suportar o número de interfaces de rede que pretende adicionar e tem de estar no estado parado (desalocado). As interfaces de rede que pretende adicionar atualmente não podem ser ligadas a outra VM. Não é possível adicionar as interfaces de rede para uma VM existente no portal do Azure. Para adicionar as interfaces de rede a uma VM existente, tem de utilizar a CLI ou PowerShell. 

Azure atribui um gateway predefinido para a primeira interface de rede (principal) ligada à máquina virtual. Azure não atribuir um gateway predefinido para interfaces de rede (secundário) adicionais ligado a uma máquina virtual. Por conseguinte, não é possível comunicar com recursos fora da sub-rede de uma interface de rede secundária, por predefinição. Interfaces de rede secundárias podem, no entanto, comunicar com os recursos fora da sua sub-rede. Se necessitar de rede secundária interfaces de comunicação com os recursos fora da sua sub-rede, consulte [encaminhamento dentro de um sistema de operativo da máquina virtual com várias interfaces de rede](#routing-within-a virtual-machine-operating-system-with-multiple-network-interfaces).

> [!WARNING]
> Se uma interface de rede tiver um endereço IPv6 privado atribuído, não é possível adicionar uma máquina virtual existente. Só é possível adicionar uma interface de rede com um endereço IPv6 privada atribuído a uma máquina virtual quando criar uma máquina virtual. Consulte [endereços IP da interface de rede](virtual-network-network-interface-addresses.md) para saber mais sobre a atribuição de endereços IP a interfaces de rede.

|Ferramenta|Comando|
|---|---|
|CLI|[Adicionar AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (referência) ou [detalhadas passos](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[AzureRmVMNetworkInterface adicionar](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [detalhadas passos](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a>Vista de interfaces de rede para uma VM

Pode ver as interfaces de rede atualmente ligadas a uma VM para saber mais sobre a configuração de cada interface de rede e os endereços IP atribuídos a cada interface de rede. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta que está atribuída a função de proprietário, Contribuidor ou contribuinte de rede para a sua subscrição. Para obter mais informações sobre como atribuir funções de contas, consulte [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *máquinas virtuais*. Quando **máquinas virtuais** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **máquinas virtuais** painel apresentado, clique no nome da VM que pretende visualizar as interfaces de rede.
4. No **definições** selecionado da secção do painel da máquina virtual que aparece para a VM, clique em **redes**. Para saber mais sobre as definições da interface de rede e como alterá-los, leia o [gerir interfaces de rede](virtual-network-network-interface.md) artigo. Para saber mais sobre como adicionar, alterar ou remover os endereços IP atribuídos a uma interface de rede, consulte o artigo [endereços IP gerir](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[Mostrar de vm AZ](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a>Remover uma interface de rede a partir de uma VM

A VM que pretende remover (ou desanexar) uma interface de rede de deve estar no estado parado (desalocado) e tem atualmente ter, pelo menos, duas interfaces de rede ligados ao mesmo. Pode remover qualquer interface de rede, mas a VM tem sempre de ter, pelo menos, uma interface de rede ligada ao mesmo. Se remover uma interface de rede principal, o Azure atribui o atributo principal para a interface de rede foi anexado à VM mais longo. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta que está atribuída a função de proprietário, Contribuidor ou contribuinte de rede para a sua subscrição. Para obter mais informações sobre como atribuir funções de contas, consulte [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *máquinas virtuais*. Quando **máquinas virtuais** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **máquinas virtuais** painel apresentado, clique no nome da VM que pretende remover uma interface de rede.
4. No **definições** selecionado da secção do painel da máquina virtual que aparece para a VM, clique em **redes**. Para saber mais sobre as definições da interface de rede e como alterá-los, leia o [gerir interfaces de rede](virtual-network-network-interface.md) artigo. Para saber mais sobre como adicionar, alterar ou remover os endereços IP atribuídos a uma interface de rede, consulte o artigo [endereços IP gerir](virtual-network-network-interface-addresses.md).
5. Clique em **X desanexar a interface de rede**.
6. Selecione a interface de rede que pretende desanexar na lista pendente, em seguida, clique em **OK**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[remover AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (referência) ou [detalhadas passos](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remover AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [detalhadas passos](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Passos seguintes
Para criar uma VM com várias interfaces de rede ou endereços IP, leia os artigos seguintes:

**Comandos**

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com vários NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM de NIC único com vários endereços de IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM de NIC único com um endereço IPv6 privado (atrás de um balanceador de carga do Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
