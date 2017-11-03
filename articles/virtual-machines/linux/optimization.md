---
title: Otimizar a VM com Linux no Azure | Microsoft Docs
description: "Saiba mais algumas sugestões de otimização para se certificar de que configurou a sua VM com Linux para um desempenho ideal no Azure"
keywords: "máquina virtual do Linux, máquina virtual linux, ubuntu máquina"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.openlocfilehash: e63b50e06ae280819aea88f61bf9f25b6e44eac7
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar a VM do Linux no Azure
A criação de uma máquina virtual (VM) do Linux é fácil fazer a partir da linha de comandos ou do portal. Este tutorial mostra como Certifique-se de que o configurou para otimizar o desempenho na plataforma Microsoft Azure. Este tópico utiliza uma VM do Ubuntu Server, mas também pode criar a máquina virtual do Linux utilizando [as suas próprias imagens como modelos](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Pré-requisitos
Este tópico pressupõe que já tem uma subscrição do Azure ([inscrição na avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e ter sido já aprovisionada uma VM na sua subscrição do Azure. Certifique-se de que a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e tiver sessão iniciada na sua subscrição do Azure com [início de sessão az](/cli/azure/#login) antes de [criar uma VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disco do SO do Azure
Depois de criar uma VM com Linux no Azure, tem dois discos associados à mesma. **dev/sda** é o disco do SO, **/dev/sdb** é o seu disco temporário.  Não utilize o disco de SO principal (**/dev/sda**) para tudo, exceto o sistema operativo que está otimizada para o tempo de arranque rápido VM e não fornece um bom desempenho para cargas de trabalho. Pretende anexar um ou mais discos para a VM para obter persistente e com otimização de armazenamento para os seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>A adição de discos para metas de desempenho e de tamanho
Com base no tamanho VM, pode anexar até 16 discos adicionais sobre uma série A, 32 discos uma série de D e o computador de 64 discos numa série-G - cada até 1 TB de tamanho. Adicionar discos adicionais conforme necessário, por sua espaço e requisitos de IOps. Cada disco tem um destino de desempenho de 500 IOps para o armazenamento padrão e até 5000 IOps por disco para armazenamento Premium.  Para obter mais informações acerca dos discos de armazenamento Premium, consulte [Premium Storage: armazenamento de elevado desempenho para as VMs do Azure](../windows/premium-storage.md)

Para alcançar o IOps máximo em discos de armazenamento Premium onde as respetivas definições da cache foram definidas para o **ReadOnly** ou **nenhum**, tem de desativar **barreiras as eficazes** ao montagem o sistema de ficheiros no Linux. Não é necessário barreiras as eficazes porque as operações de escrita para discos de armazenamento Premium suportado são duráveis para estas definições de cache.

* Se utilizar **reiserFS**, desative a barreiras as eficazes utilizando a opção de montagem `barrier=none` (para ativar barreiras as eficazes, utilize `barrier=flush`)
* Se utilizar **ext3/ext4**, desative a barreiras as eficazes utilizando a opção de montagem `barrier=0` (para ativar barreiras as eficazes, utilize `barrier=1`)
* Se utilizar **XFS**, desative a barreiras as eficazes utilizando a opção de montagem `nobarrier` (para ativar barreiras as eficazes, utilize a opção `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Considerações sobre contas de armazenamento não gerido
A ação predefinida quando criar uma VM com o 2.0 CLI do Azure é utilizar discos gerida do Azure.  Estes discos são processados pela plataforma do Azure e não necessitam de quaisquer de preparação ou localização para armazená-las.  Os discos não geridos precisam de uma conta de armazenamento e têm algumas considerações de desempenho adicionais.  Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../windows/managed-disks-overview.md).  A secção seguinte descreve considerações de desempenho apenas quando utilizar discos não geridos.  Novamente, a predefinição e a solução de armazenamento recomendada consiste em utilizar discos geridos.

Se criar uma VM com discos não geridos, certifique-se de que ligue os discos das contas do storage que residem na mesma região que a VM para se certificar próximos e minimizar a latência de rede.  Cada conta de armazenamento Standard tem um máximo de 20 IOps e uma capacidade de tamanho de 500 TB.  Este limite funciona aproximadamente 40 discos muito utilizada, incluindo o disco do SO e discos de dados que cria. Para contas de armazenamento Premium, não existe nenhum limite de IOps máximo, contudo, existe um limite de tamanho de 32 TB. 

Ao lidar com alta cargas de trabalho de IOps e tiver escolhido o armazenamento Standard para os discos, poderá ter de dividir os discos por várias contas do storage para se certificar de que não atingiu o limite de IOps 20.000 para contas de armazenamento Standard. A VM pode conter uma mistura de discos de em contas de armazenamento diferente e tipos de conta de armazenamento para alcançar a configuração ideal.
 

## <a name="your-vm-temporary-drive"></a>A unidade de VM temporária
Por predefinição quando cria uma VM, Azure fornece um disco de SO (**/dev/sda**) e um disco temporário (**/dev/sdb**).  Todos os discos adicionais adicionar mostrar cópias de segurança como **/dev/sdc**, **/dev/sdd**, **/dev/sde** e assim sucessivamente. Todos os dados no disco temporário (**/dev/sdb**) não são duráveis e podem ser perdidas se eventos específicos, como redimensionamento de VM, reimplementação, ou manutenção força reiniciar a VM.  O tamanho e tipo do disco temporário está relacionado com o tamanho da VM que escolheu no momento da implementação. Todas o premium tamanho VMs (série DS, G e DS_V2) unidade temporária são apoiados por um SSD local para um desempenho adicional do até 48k IOps. 

## <a name="linux-swap-file"></a>Ficheiro de comutação do Linux
Se a VM do Azure é a partir de uma imagem Ubuntu ou CoreOS, em seguida, pode utilizar CustomData para enviar uma configuração de nuvem para a nuvem init. Se lhe [carregado uma imagem personalizada do Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que utiliza init de nuvem, também é configurar partições de comutação utilizando init de nuvem.

Nas imagens de nuvem Ubuntu, tem de utilizar init da nuvem para configurar a partição de comutação. Para obter mais informações, consulte [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem suporte de nuvem init, imagens VM implementadas a partir do Azure Marketplace tem um agente da VM com Linux integrado com o sistema operativo. Este agente permite que a VM interagir com vários serviços do Azure. Partindo do princípio de que implementou uma imagem de padrão do Azure Marketplace, terá de fazer o seguinte para configurar corretamente as definições de ficheiro de troca de Linux:

Localize e modificar duas entradas no **/etc/waagent.conf** ficheiro. Controlarem a existência de um ficheiro de troca dedicado e o tamanho do ficheiro de troca. Os que procura para modificar os parâmetros são `ResourceDisk.EnableSwap=N` e`ResourceDisk.SwapSizeMB=0` 

Altere os parâmetros para as seguintes definições:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size em MB para satisfazer as necessidades} 

Depois de efetuar a alteração, terá de reiniciar o waagent ou reinicie a VM com Linux para refletir essas alterações.  Sabe que foi implementadas as alterações e um ficheiro de comutação foi criado quando utiliza o `free` comando para ver o espaço livre. O exemplo seguinte tem um ficheiro de troca de 512MB criado como resultado de modificar o **waagent.conf** ficheiro:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de agendamento de e/s para o Premium Storage
Com o 2.6.18 Linux kernel, a predefinição e/s agendamento algoritmo foi alterado de prazo para CFQ (algoritmo de colocação completamente justa). Padrões de e/s de acesso aleatório, é irrelevante diferença no desempenho diferenças CFQ e o prazo.  Para discos baseadas em SSD onde o padrão de e/s de disco é predominantemente sequencial, mudar novamente para o algoritmo NOOP ou prazo pode obter um melhor desempenho de e/s.

### <a name="view-the-current-io-scheduler"></a>Ver o Programador de e/s atual
Utilize o seguinte comando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Consulte os seguintes resultados, o que indica o programador atual.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (dev/sda) de e/s de algoritmo de agendamento
Utilize os seguintes comandos:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Aplicar esta definição para **/dev/sda** sozinho não é útil. Definir todos os discos de dados onde e/s sequenciais dominates o padrão de e/s.  

Deverá ver o seguinte resultado que indica que **grub.cfg** foi reconstruída com êxito e de que o Programador de predefinição foi atualizado para NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Para a família de distribuição de VM de Redhat, apenas é necessário o seguinte comando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Utilizando o RAID de Software para alcançar superior posso / Ops
Se as cargas de trabalho exigirem mais de IOps que pode fornecer um único disco, terá de utilizar uma configuração de RAID de software de vários discos. Porque o Azure executa já resiliência do disco na camada de recursos de infraestrutura local, o que poderá alcançar o nível mais elevado de desempenho de uma configuração de striping RAID 0.  Aprovisionar e criar discos no ambiente do Azure e anexe-os para a VM com Linux antes de criação de partições, formatação e montar as unidades.  Obter mais detalhes sobre como configurar um programa de configuração do RAID de software na sua VM com Linux no azure podem ser encontrados no  **[configuração RAID de Software no Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**  documento.

## <a name="next-steps"></a>Passos Seguintes
Lembre-se, tal como com todos os debates de otimização, terá de realizar testes antes e após cada alteração para medir o impacto que tem da alteração.  A otimização é um processo de passo a passo que tem resultados diferentes em vários computadores no seu ambiente.  O que funciona para uma configuração pode não funcionar para outras pessoas.

Algumas ligações útil para recursos adicionais: 

* [Armazenamento Premium: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Máquinas Virtuais do Azure](../windows/premium-storage.md)
* [Guia de utilizador do agente Linux do Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Otimizar o desempenho de MySQL em VMs do Linux do Azure](classic/optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurar Software RAID no Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

