---
title: Criar e carregar um VHD do SUSE Linux no Azure
description: "Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo SUSE Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: szark
ms.openlocfilehash: c829f5d9a90b4260c6f41b2d9e511a0c6cb48f18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar uma máquina virtual SLES ou openSUSE para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já tiver instalado um SUSE ou openSUSE sistema operativo Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização, tais como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE notas de instalação
* Consulte também [geral notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para mais sugestões no preparar Linux para o Azure.
* O formato VHDX não é suportado no Azure, apenas **fixo VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux é recomendado que utilize partições padrão em vez de LVM (muitas vezes, a predefinição para instalações muitos). Evitará LVM nome entra em conflito com VMs Clonadas, particularmente se um disco de SO alguma vez precisar de ser ligado a outra VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizado em discos de dados se preferencial.
* Não configure uma partição de comutação no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de comutação no disco de recursos temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.
* Todos os VHDs têm de ter tamanhos que estão em múltiplos de 1 MB.

## <a name="use-suse-studio"></a>Utilize o SUSE Studio
[SUSE Studio](http://www.susestudio.com) pode facilmente criar e gerir as imagens SLES e openSUSE para o Azure e o Hyper-V. Esta é a abordagem recomendada para personalizar as suas próprias imagens SLES e openSUSE.

Como alternativa para criar o seu próprio VHD, SUSE publica também imagens BYOS (traga a sua subscrição própria) para o SLES em [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparar SP4 do SUSE Linux Enterprise Server 11
1. No painel central do Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir a janela para a máquina virtual.
3. Registe o seu sistema SUSE Linux Enterprise para permitir que as atualizações de transferir e instalar pacotes.
4. Atualize o sistema com as correções mais recentes:
   
        # sudo zypper update
5. Instale o agente Linux do Azure do repositório SLES:
   
        # sudo zypper install WALinuxAgent
6. Verifique se waagent estiver definido como "ativado" na chkconfig e, se não estiver, ativá-la para autostart:
   
        # sudo chkconfig waagent on
7. Verifique se o serviço de waagent está em execução e se não, inicie-o: 
   
        # sudo service waagent start
8. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer este open "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinida inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Isto irá garantir a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração.
9. Certifique-se de que /boot/grub/menu.lst e /etc/fstab ambos referenciam o disco utilizando o respetivo UUID (por uuid) em vez do ID de disco (pelo id). 
   
    Obter o UUID de disco
   
        # ls /dev/disk/by-uuid/
   
    Se /dev/disk/by-id / é utilizado, atualizar fstab /boot/grub/menu.lst tanto etc/com o valor adequado ao uuid
   
    Antes de alteração
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Após a alteração
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Modificar regras de udev para evitar a geração de regras estáticas para a interfaces Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou o Hyper-v:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. É recomendado editar o ficheiro "/ etc/sysconfig/rede/dhcp" e altere o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:
    
     DHCLIENT_SET_HOSTNAME = "não"
12. "Etc/sudoers", comente ou remover as seguintes linhas caso existam:
    
     Predefinições targetpw # pedir a palavra-passe do utilizador de destino ou seja, raiz todos os todos os ALL=(ALL) # aviso! Apenas utilizá-lo juntamente com 'Predefinições targetpw'!
13. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Normalmente, esta é a predefinição.
14. Não crie o espaço de comutação no disco do SO.
    
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes /etc/waagent.conf conforme apropriado:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=&#2048; # Nota: defina esta opção como tudo o que precisar que seja.
15. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - desaprovisionamento
    # <a name="export-histsize0"></a>Exportar HISTSIZE = 0
    # <a name="logout"></a>terminar sessão
16. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.

- - -
## <a name="prepare-opensuse-131"></a>Preparar openSUSE 13.1 +
1. No painel central do Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir a janela para a máquina virtual.
3. Na shell, execute o comando '`zypper lr`'. Se este comando devolve o resultado semelhante ao seguinte, em seguida, repositórios do estiverem configurados como esperado – sem ajustes são necessários (tenha em atenção que os números de versão podem variar):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Se o comando não devolve "Nenhum repositórios definidos...", em seguida, utilize os seguintes comandos para adicionar estes repos:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Em seguida, pode verificar os repositórios foram adicionados ao executar o comando '`zypper lr`' novamente. No caso de um dos repositórios do atualização relevantes não estiver ativado, ativá-la com o seguinte comando:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Atualização do kernel para a versão mais recente disponível:
   
        # sudo zypper up kernel-default
   
    Ou para atualizar o sistema com todas as correções mais recentes:
   
        # sudo zypper update
5. Instale o agente Linux do Azure.
   
   # <a name="sudo-zypper-install-walinuxagent"></a>sudo zypper instalação WALinuxAgent
6. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para tal, abra "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinida inclui os seguintes parâmetros:
   
     consola = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Isto irá garantir a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Além disso, remova os seguintes parâmetros da linha de arranque de kernel caso existam:
   
     reserva de libata.atapi_enabled=0 = 0x1f0, 0x8
7. É recomendado editar o ficheiro "/ etc/sysconfig/rede/dhcp" e altere o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:
   
     DHCLIENT_SET_HOSTNAME = "não"
8. **Importante:** "etc/sudoers", comente ou remover as seguintes linhas caso existam:
   
     Predefinições targetpw # pedir a palavra-passe do utilizador de destino ou seja, raiz todos os todos os ALL=(ALL) # aviso! Apenas utilizá-lo juntamente com 'Predefinições targetpw'!
9. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Normalmente, esta é a predefinição.
10. Não crie o espaço de comutação no disco do SO.
    
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes /etc/waagent.conf conforme apropriado:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=&#2048; # Nota: defina esta opção como tudo o que precisar que seja.
11. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - desaprovisionamento
    # <a name="export-histsize0"></a>Exportar HISTSIZE = 0
    # <a name="logout"></a>terminar sessão
12. Certifique-se de que o agente Linux do Azure é executado no arranque:
    
        # sudo systemctl enable waagent.service
13. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.

## <a name="next-steps"></a>Passos seguintes
Agora, está pronto a utilizar o seu disco rígido virtual do SUSE Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, consulte os passos 2 e 3 na [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

