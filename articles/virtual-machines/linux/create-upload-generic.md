---
title: Criar e carregar um VHD do Linux no Azure
description: "Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: ccadf55c492c097ef96f25e469dbf36fc87b6102
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="information-for-non-endorsed-distributions"></a>Informações para Distribuições Não Apoiadas
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

A plataforma do Azure SLA se aplica a máquinas virtuais com o SO Linux apenas quando um do [distribuições aprovadas pelo](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) é utilizado. Todas as distribuições de Linux que são fornecidas na galeria da imagem do Azure são distribuições aprovadas com a configuração necessária.

* [Distribuições aprovadas pelo Linux no Azure-](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Suporte para imagens de Linux no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure, serão necessário satisfazer um número de pré-requisitos para ter uma oportunidade para executar adequadamente na plataforma.  Este artigo não é de abrangente como cada distribuição for diferente; e é bastante possível que, mesmo que cumprem os critérios abaixo irá ainda terá significativamente otimizar o seu sistema Linux para garantir que funciona corretamente na plataforma.

É por este motivo que recomendamos que comece com um dos nossos [Linux no Azure aprovadas distribuições](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) sempre que possível. Os artigos seguintes descrevem como preparar as distribuições de Linux endorsed vários que são suportadas no Azure:

* **[Distribuições baseado em centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

O resto deste artigo foca-se no orientações gerais para executar a distribuição de Linux no Azure.

## <a name="general-linux-installation-notes"></a>Notas de instalação do Linux geral
* O formato VHDX não é suportado no Azure, apenas **fixo VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet convert-vhd. Se estiver a utilizar VirtualBox significa que a seleção **um tamanho fixo** , por oposição a predefinição dinamicamente atribuída ao criar o disco.
* Azure suporta apenas máquinas virtuais de geração 1. Pode converter uma máquina virtual de geração 1 VHDX para o formato de ficheiro VHD e de expansão dinâmica um disco de tamanho fixo. Mas não é possível alterar a geração de uma máquina virtual. Para obter mais informações, consulte [deve criar máquinas virtuais de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* O tamanho máximo permitido para o VHD é 1,023 GB.
* Ao instalar o sistema Linux é *recomendado* que utilize partições padrão em vez de LVM (muitas vezes, a predefinição para instalações muitos). Evitará LVM nome entra em conflito com VMs Clonadas, particularmente se um disco de SO alguma vez precisar de ser ligado a outra VM idêntico para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizado em discos de dados.
* É necessário suporte de kernel para montar a sistemas de ficheiros UDF. No primeiro arranque no Azure a configuração de aprovisionamento é transmitida para a VM com Linux através de multimédia formatado UDF ligada para o convidado. O agente Linux do Azure tem de ser capaz de montar o sistema de ficheiros UDF ler a configuração e aprovisionar a VM.
* Versões de kernel do Linux abaixo 2.6.37 não suportam no Hyper-V com tamanhos de VM maiores. Isto emitir principalmente impactos distribuições mais antigas utilizando o montante kernel do Red Hat 2.6.32 e foi corrigido em RHEL 6.6 (kernel-2.6.32-504). Sistemas com kernels personalizados anteriores 2.6.37 ou baseado em RHEL kernels mais antigos que 2.6.32-504 tem de definir o parâmetro de arranque `numa=off` no kernel da linha de comandos no grub.conf. Para obter mais informações consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Não configure uma partição de comutação no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de comutação no disco de recursos temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.
* Todos os VHDs têm de ter tamanhos que estão em múltiplos de 1 MB.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalar módulos de kernel sem Hyper-V
Azure é executado num hipervisor Hyper-V, Linux requer a determinados módulos de kernel para executar no Azure. Se tiver uma VM que foi criada fora do Hyper-V, os programas de instalação do Linux não podem incluir os controladores para o Hyper-V no disco de RAM inicial (initrd ou initramfs), a menos que Deteta que está a ser executado num ambiente Hyper-V. Quando utilizar um sistema de Virtualização diferente (ou seja, Virtualbox KVM, etc.) para preparar a imagem do Linux, poderá ter de reconstruir o initrd para garantir que, pelo menos, o `hv_vmbus` e `hv_storvsc` módulos de kernel estão disponíveis em disco de RAM inicial.  Este é um problema conhecido, pelo menos, em sistemas com base na distribuição de Red Hat a montante.

O mecanismo para recriar a imagem initrd ou initramfs pode variar consoante a distribuição. . Consulte a documentação da sua distribuição ou fornece suporte para o procedimento adequado.  Eis um exemplo sobre como reconstruir a initrd utilizando o `mkinitrd` utilitário:

Em primeiro lugar, criar cópias de segurança a imagem de initrd existente:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Em seguida, reconstrua o initrd com o `hv_vmbus` e `hv_storvsc` módulos de kernel:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>O redimensionamento de VHDs
Imagens VHD no Azure tem de ter um tamanho virtual alinhado com 1MB.  Normalmente, os VHDs criados com o Hyper-V devem já estão alinhados corretamente.  Se o VHD não está alinhado corretamente, em seguida, poderá receber uma mensagem de erro semelhante ao seguinte ao tentar criar um *imagem* do seu VHD:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Para resolver isto pode redimensionar a VM utilizando a consola de Gestor de Hyper-V ou o [redimensionamento VHD](http://technet.microsoft.com/library/hh848535.aspx) cmdlet do Powershell.  Se não estiver a executar num ambiente Windows, em seguida, é recomendado utilizar qemu img converter (se necessário) e redimensionar o VHD.

> [!NOTE]
> Há um erro conhecido em versões de qemu img > = 2.2.1 que resulte num VHD incorretamente formatado. O problema no QEMU 2.6. É recomendado utilizar qemu-img 2.2.0 ou inferior, ou atualizar para o 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. O VHD diretamente através de ferramentas, como o redimensionamento `qemu-img` ou `vbox-manage` poderá resultar num VHD de arranque.  Por isso, recomenda-se ao converter a primeira o VHD para uma imagem de disco não processados.  Se a imagem VM já foi criada como imagem de disco não PROCESSADO (a predefinição para alguns Hipervisores, tais como KVM), em seguida, pode ignorar este passo:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Calcule o tamanho necessário da imagem de disco para se certificar de que o tamanho virtual está alinhado 1MB.  O seguinte script de shell de deteção pode ajudá-lo com esta.  O script utiliza "`qemu-img info`" para determinar o tamanho da imagem de disco virtual e, em seguida, calcula o tamanho de 1 MB seguinte:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Redimensione o disco não processado utilizando $rounded_size conforme definido no script acima:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Converter agora, o disco não PROCESSADO para um VHD de tamanho fixo:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Ou, com a versão de qemu **2.6 +** incluem o `force_size` opção:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Requisitos de Kernel do Linux
Os controladores de serviços de integração Linux (LIS) para o Hyper-V e do Azure são contribuíram diretamente para o kernel do Linux a montante. Muitas distribuições que incluem uma versão de kernel recente Linux (ou seja, 3) irão já tem estes controladores disponíveis ou, caso contrário, fornecer versões backported estes controladores com os respetivos kernels.  Estes controladores constantemente estão a ser atualizados no montante kernel com novo correções e funcionalidades, pelo que, sempre que possível recomenda-se para executar um [aprovadas distribuição](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que incluirá estas correções e atualizações.

Se estiver a executar uma variante de versões do Red Hat Enterprise Linux **6.0 6.3**, em seguida, terá de instalar os controladores mais recentes de LIS para Hyper-V. Os controladores podem ser encontrados [nesta localização](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A partir do RHEL **6.4 +** (e derivados) os controladores LIS já estão incluídos com o kernel e, por isso, não existem pacotes de instalação adicionais são necessárias para executar esses sistemas no Azure.

Se for necessário um kernel personalizado, é recomendado utilizar uma versão mais recente do kernel (ou seja, **3.8 +**). Para esses distribuições ou fornecedores que mantêm os seus próprios kernel, ser algum esforço necessários para backport regularmente os controladores LIS de kernel para o kernel personalizado a montante.  Mesmo se já estiver a executar uma versão de kernel relativamente recente, é altamente recomendado para controlar quaisquer correções montante os controladores de LIS e backport os conforme necessário. A localização dos ficheiros de origem do controlador LIS está disponível no [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) ficheiros na árvore de origem de kernel Linux:

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

Um muito mínimo, a ausência dos patches seguintes ter sido conhecido causar problemas no Azure e, por isso, estes têm de ser incluídos do kernel. Esta lista não é de exaustiva ou completos para todas as distribuições de:

* [ata_piix: diferir discos para os controladores de Hyper-V por predefinição](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: para os pacotes em trânsito no caminho de reposição de conta](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: evitar a utilização de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: desativar a escrita mesmo para RAID e controladores do adaptador de anfitrião virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: um ponteiro NULL cancelar a referência de correção](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: falhas de memória intermédia de anel podem resultar em congelar de e/s](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: proteger contra a execução dupla de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>O agente Linux do Azure
O [agente Linux do Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (waagent) é necessário para corretamente aprovisionar uma máquina virtual do Linux no Azure. Pode obter a versão mais recente, problemas de ficheiros ou submeter pedidos de solicitação, o [repositório do GitHub de agente do Linux](https://github.com/Azure/WALinuxAgent).

* O agente Linux é libertado com a licença do Apache 2.0. Muitas distribuições já fornecem RPM ou deb pacotes para o agente e, por isso, em alguns casos esta situação pode ser instalada e atualizada com pouca esforço.
* O agente Linux do Azure requer Python v2.6 +.
* O agente necessita também o módulo de python pyasn1. A maioria das distribuições fornecem esta como um pacote separado que pode ser instalado.
* Em alguns casos, o agente Linux do Azure não podem ser compatível com NetworkManager. Muitos dos pacotes RPM/Deb fornecidos pelo distribuições configurar NetworkManager como um conflito ao pacote de waagent e, por conseguinte, irão desinstalar NetworkManager quando instala o pacote de agente do Linux.

## <a name="general-linux-system-requirements"></a>Requisitos de sistema de Linux geral

* Modifique a linha de arranque de kernel no GRUB ou GRUB2 para incluir os seguintes parâmetros. Isto também vai assegurar todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Isto também vai assegurar todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração.
  
    Para além dos acima, é recomendado *remover* os seguintes parâmetros caso existam:
  
        rhgb quiet crashkernel=auto
  
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série. O `crashkernel` opção pode ser esquerda configurado se assim o desejar, mas tenha em atenção que este parâmetro irá reduzir a quantidade de memória disponível na VM 128 MB ou mais, que pode ser problemático nos tamanhos VM mais pequenos.

* Instalar o agente Linux do Azure
  
    O agente Linux do Azure é necessário para o aprovisionamento de uma imagem do Linux no Azure.  Muitas distribuições fornecem o agente como um pacote RPM ou Deb (o pacote é normalmente denominado 'WALinuxAgent' ou 'walinuxagent').  O agente também pode ser instalado manualmente, seguindo os passos a [manual de agente do Linux](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Normalmente, esta é a predefinição.

* Não crie o espaço de comutação no disco do SO
  
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes /etc/waagent.conf conforme apropriado:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Como passo final, execute os seguintes comandos para retirar o aprovisionamento da máquina virtual:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > No Virtualbox poderá ver o seguinte erro após a execução ' waagent-force - deprovision': `[Errno 5] Input/output error`. Esta mensagem de erro não crítico e pode ser ignorada.
  > 
  > 

* Em seguida, terá de encerrar a máquina virtual e carrega o VHD para o Azure.

