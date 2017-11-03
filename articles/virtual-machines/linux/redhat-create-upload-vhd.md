---
title: "Criar e carregar um VHD do Red Hat Enterprise Linux para utilização no Azure | Microsoft Docs"
description: "Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo Red Hat Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/28/2017
ms.author: szark
ms.openlocfilehash: b753c76b8c3d789c681d7fbff6aa07590b860be5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em Red Hat para o Azure
Neste artigo, irá aprender como preparar uma máquina virtual do Red Hat Enterprise Linux (RHEL) para utilização no Azure. As versões do RHEL que são abordadas neste artigo são 6.7 + e 7.1 +. Os hipervisores para preparação que são abordados neste artigo são Hyper-V, com base em kernel a máquina virtual (KVM) e VMware. Para obter mais informações sobre os requisitos de elegibilidade para participar no programa de acesso à nuvem do Red Hat, consulte [Web site de acesso à nuvem do Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) e [RHEL em execução no Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina de virtual baseado no Red Hat a partir do Gestor de Hyper-V

### <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já obteve um ficheiro ISO a partir do site do Red Hat e instalar a imagem RHEL para um disco rígido virtual (VHD). Para obter mais detalhes sobre como utilizar o Gestor de Hyper-V para instalar uma imagem do sistema operativo, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do RHEL**

* Azure não suporta o formato VHDX. Azure suporta apenas fixo VHD. Pode utilizar o Gestor de Hyper-V para converter disco em formato VHD ou pode utilizar o cmdlet convert-vhd. Se utilizar VirtualBox, selecione **um tamanho fixo** , por oposição a predefinição dinamicamente atribuído opção ao criar o disco.
* Azure suporta apenas máquinas de virtuais de geração 1. Pode converter uma máquina virtual de geração 1 VHDX para o formato de ficheiro VHD e de expansão dinâmica para um disco de tamanho fixo. Não é possível alterar a geração de uma máquina virtual. Para obter mais informações, consulte [deve criar máquinas virtuais de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* O tamanho máximo permitido para o VHD é 1,023 GB.
* Quando instala o sistema operativo Linux, recomendamos que utilize partições padrão em vez de Gestor de Volume lógica (LVM), que é, muitas vezes, a predefinição para instalações muitos. Esta prática evitará LVM nome entra em conflito com as máquinas virtuais Clonadas, particularmente se alguma vez precisar de anexar um disco de sistema operativo a outra máquina virtual idêntica para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizado em discos de dados.
* É necessário suporte de kernel para montar a sistemas de ficheiros de formato de disco Universal (UDF). No primeiro arranque no Azure, o suporte de dados formatado UDF, que está ligado para o convidado passa a configuração de aprovisionamento para a máquina virtual Linux. O agente Linux do Azure tem de ser capaz de montar o sistema de ficheiros UDF ler a configuração e aprovisionar a máquina virtual.
* Versões do Linux kernel anteriores ao 2.6.37 não suportam acesso não uniforme memória (NUMA) no Hyper-V com maior tamanhos de máquina virtual. Isto emitir principalmente impactos distribuições mais antigas que utilizam o montante kernel do Red Hat 2.6.32 e foi corrigido em RHEL 6.6 (kernel-2.6.32-504). Sistemas que executam kernels personalizados que são mais antigos do que 2.6.37 ou baseado em RHEL kernels que são mais antigas do que 2.6.32-504 tem de definir o `numa=off` de arranque parâmetro da linha de comandos de kernel grub.conf. Para obter mais informações, consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Configure uma partição de comutação em disco do sistema operativo. O agente Linux pode ser configurado para criar um ficheiro de comutação no disco de recursos temporário.  Podem encontrar mais informações sobre esta nos passos seguintes.
* Todos os VHDs têm de ter tamanhos que estão em múltiplos de 1 MB.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual do RHEL 6 a partir do Gestor de Hyper-V

1. No Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Connect** para abrir uma janela da consola da máquina virtual.

3. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote, executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

4. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas para a interface de Ethernet. Estas regras provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou o Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:

        # sudo chkconfig network on

8. Registe a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido feito o push para o repositório de extras do Red Hat. Ative o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer esta modificação, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o kernel predefinida inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isto também vai assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração.
    
    Além disso, recomendamos que remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série.  Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual 128 MB ou mais. Esta configuração pode ser problemática em tamanhos de máquinas virtuais mais pequenos.

    >[!Important]
    RHEL 6.5 e anterior também tem de definir o `numa=off` parâmetro de kernel. Consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

11. Certifique-se de que o servidor de secure shell (SSH) está instalado e configurado para iniciar no momento do arranque, que é, normalmente, a predefinição. Modificar /etc/ssh/sshd_config para incluir a seguinte linha:

        ClientAliveInterval 180

12. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Instalar o pacote de WALinuxAgent remove o NetworkManager e pacotes de NetworkManager gnome se estes não foram removidos já no passo 3.

13. Não crie o espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação, utilizando o disco de recurso local que esteja anexado à máquina virtual depois da máquina virtual estar aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário e que se poderá de ser esvaziada quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modifique os parâmetros seguintes /etc/waagent.conf conforme apropriado:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Anular o registo da subscrição (se necessário), executando o seguinte comando:

        # sudo subscription-manager unregister

15. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

16. Clique em **ação** > **Encerrar** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual do RHEL 7 a partir do Gestor de Hyper-V

1. No Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Connect** para abrir uma janela da consola da máquina virtual.

3. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:

        # sudo chkconfig network on

6. Registe a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer esta modificação, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isto também vai assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Esta configuração também desativa-se o novo RHEL 7 as convenções de nomenclatura para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

8. Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para reconstruir a configuração de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque, que é, normalmente, a predefinição. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

10. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido feito o push para o repositório de extras do Red Hat. Ative o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. Não crie o espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação, utilizando o disco de recurso local que esteja anexado à máquina virtual depois da máquina virtual estar aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário e poderá ser esvaziada quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Se pretende anular o registo da subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

14. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Clique em **ação** > **Encerrar** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar uma máquina de virtual baseado no Red Hat do KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Preparar uma máquina virtual do RHEL 6 do KVM

1. Transferir a imagem KVM do RHEL 6 do Web site do Red Hat.

2. Defina uma palavra-passe de raiz.

    Gerar uma palavra-passe encriptada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma palavra-passe de raiz com guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Altere o campo do utilizador raiz de segundo "!" a palavra-passe encriptada.

3. Crie uma máquina virtual no KVM da imagem de qcow2. Defina o tipo de disco para **qcow2**e definir o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, iniciar a máquina virtual e inicie sessão como raiz.

4. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas para a interface de Ethernet. Estas regras provocar problemas ao clonar uma máquina virtual no Azure ou do Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:

        # chkconfig network on

8. Registe a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para efetuar esta configuração, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o kernel predefinida inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isto também vai assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração.
    
    Além disso, recomendamos que remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

    >[!Important]
    RHEL 6.5 e anterior também tem de definir o `numa=off` parâmetro de kernel. Consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

10. Adicione módulos do Hyper-V para initramfs:  

    Editar `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Reconstrua initramfs:

        # dracut -f -v

11. Desinstale init de cloud:

        # yum remove cloud-init

12. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque:

        # chkconfig sshd on

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido feito o push para o repositório de extras do Red Hat. Ative o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Instale o agente Linux do Azure executando o seguinte comando:

        # yum install WALinuxAgent

        # chkconfig waagent on

15. O agente Linux do Azure podem configurar automaticamente o espaço de comutação, utilizando o disco de recurso local que esteja anexado à máquina virtual depois da máquina virtual estar aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário e poderá ser esvaziada quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes **/etc/waagent.conf** corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Anular o registo da subscrição (se necessário), executando o seguinte comando:

        # subscription-manager unregister

17. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Encerre a máquina virtual no KVM.

19. Converta a imagem de qcow2 para o formato do VHD.

    Converta primeiro a imagem em formato não processado:

        # qemu-img convert -f qcow2 -O raw rhel-6.8.qcow2 rhel-6.8.raw

    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Arredonda caso contrário, o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Converter disco em bruto de um VHD de tamanho fixo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.8.raw rhel-6.8.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Preparar uma máquina virtual do RHEL 7 do KVM

1. Transferir a imagem KVM RHEL 7 do Web site do Red Hat. Este procedimento utiliza RHEL 7 como exemplo.

2. Defina uma palavra-passe de raiz.

    Gerar uma palavra-passe encriptada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma palavra-passe de raiz com guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Altere o campo de utilizador de raiz de segundo "!" a palavra-passe encriptada.

3. Crie uma máquina virtual no KVM da imagem de qcow2. Defina o tipo de disco para **qcow2**e definir o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, iniciar a máquina virtual e inicie sessão como raiz.

4. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:

        # chkconfig network on

7. Registe a sua subscrição do Red Hat para ativar a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para efetuar esta configuração, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Este comando também garante que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. O comando também desativa-se o novo RHEL 7 as convenções de nomenclatura para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

9. Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para reconstruir a configuração de grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Adicione módulos do Hyper-V em initramfs.

    Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Reconstrua initramfs:

        # dracut -f -v

11. Desinstale init de cloud:

        # yum remove cloud-init

12. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque:

        # systemctl enable sshd

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido feito o push para o repositório de extras do Red Hat. Ative o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Instale o agente Linux do Azure executando o seguinte comando:

        # yum install WALinuxAgent

    Ative o serviço de waagent:

        # systemctl enable waagent.service

15. Não crie o espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação, utilizando o disco de recurso local que esteja anexado à máquina virtual depois da máquina virtual estar aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário e poderá ser esvaziada quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Anular o registo da subscrição (se necessário), executando o seguinte comando:

        # subscription-manager unregister

17. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Encerre a máquina virtual no KVM.

19. Converta a imagem de qcow2 para o formato do VHD.

    Converta primeiro a imagem em formato não processado:

        # qemu-img convert -f qcow2 -O raw rhel-7.3.qcow2 rhel-7.3.raw

    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Arredonda caso contrário, o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Converter disco em bruto de um VHD de tamanho fixo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.3.raw rhel-7.3.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar uma máquina de virtual baseado no Red Hat do VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já instalou uma máquina virtual do RHEL no VMware. Para obter mais informações sobre como instalar um sistema operativo no VMware, consulte [guia de instalação de sistema operativo de convidado de VMware](http://partnerweb.vmware.com/GOSIG/home.html).

* Quando instala o sistema operativo Linux, recomendamos que utilize partições padrão em vez de LVM, que é, muitas vezes, a predefinição para instalações muitos. Evitará LVM nome entra em conflito com a máquina virtual clonada, particularmente se um disco de sistema operativo alguma vez precisar de ser ligado a outra máquina virtual para resolução de problemas. LVM RAID pode ser utilizado ou em discos de dados se preferencial.
* Configure uma partição de comutação em disco do sistema operativo. Pode configurar o agente do Linux para criar um ficheiro de comutação no disco de recursos temporário. Pode encontrar mais informações sobre esta nos passos que se seguem.
* Ao criar o disco rígido virtual, selecione **disco virtual de armazenamento como um único ficheiro**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Preparar uma máquina virtual do RHEL 6 do VMware
1. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote, executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

2. Crie um ficheiro denominado **rede** no /etc/hosts/sysconfig/diretório que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas para a interface de Ethernet. Estas regras provocar problemas ao clonar uma máquina virtual no Azure ou do Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

5. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:

        # sudo chkconfig network on

6. Registe a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido feito o push para o repositório de extras do Red Hat. Ative o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para tal, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Isto também vai assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

9. Adicione módulos do Hyper-V para initramfs:

    Editar `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Reconstrua initramfs:

        # dracut -f -v

10. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque, que é, normalmente, a predefinição. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ClientAliveInterval 180

11. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

12. Não crie o espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação, utilizando o disco de recurso local que esteja anexado à máquina virtual depois da máquina virtual estar aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário e poderá ser esvaziada quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Anular o registo da subscrição (se necessário), executando o seguinte comando:

        # sudo subscription-manager unregister

14. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Encerre a máquina virtual e converter o ficheiro VMDK num ficheiro. vhd.

    Converta primeiro a imagem em formato não processado:

        # qemu-img convert -f vmdk -O raw rhel-6.8.vmdk rhel-6.8.raw

    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Arredonda caso contrário, o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Converter disco em bruto de um VHD de tamanho fixo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.8.raw rhel-6.8.vhd

### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Preparar uma máquina virtual do RHEL 7 do VMware
1. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:

        # sudo chkconfig network on

4. Registe a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer esta modificação, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Esta configuração também garante que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. É também desativa as convenções de nomenclatura RHEL 7 novo para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

6. Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para reconstruir a configuração de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. Adicione módulos do Hyper-V para initramfs.

    Editar `/etc/dracut.conf`, adicionar conteúdo:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Reconstrua initramfs:

        # dracut -f -v

8. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque. Esta definição é normalmente, a predefinição. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

9. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido feito o push para o repositório de extras do Red Hat. Ative o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. Não crie o espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação, utilizando o disco de recurso local que esteja anexado à máquina virtual depois da máquina virtual estar aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário e poderá ser esvaziada quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Se pretende anular o registo da subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

13. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. Encerre a máquina virtual e converter o ficheiro VMDK para o formato do VHD.

    Converta primeiro a imagem em formato não processado:

        # qemu-img convert -f vmdk -O raw rhel-7.3.vmdk rhel-7.3.raw

    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Arredonda caso contrário, o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Converter disco em bruto de um VHD de tamanho fixo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.3.raw rhel-7.3.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar uma máquina de virtual baseado no Red Hat a partir de um ficheiro ISO utilizando um ficheiro de kickstart automaticamente
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Preparar uma máquina virtual de RHEL 7 de um ficheiro de kickstart

1.  Criar um ficheiro de kickstart que inclui o seguinte conteúdo e guarde o ficheiro. Para obter detalhes sobre a instalação de kickstart, consulte o [guia de instalação Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2. Coloque o ficheiro de kickstart onde o sistema de instalação pode aceder ao mesmo.

3. No Gestor de Hyper-V, crie uma nova máquina virtual. No **ligar disco rígido Virtual** página, selecione **anexar um disco rígido virtual mais tarde**e conclua o Assistente de Nova Máquina Virtual.

4. Abra as definições de máquina virtual:

    a.  Anexe um novo disco rígido virtual à máquina virtual. Certifique-se de que seleciona **formato VHD** e **de tamanho fixo**.

    b.  Anexe a instalação ISO à unidade de DVD.

    c.  Defina o BIOS para arrancar a partir do CD.

5. Inicie a máquina virtual. Quando o guia de instalação for apresentada, prima **separador** para configurar as opções de arranque.

6. Introduza `inst.ks=<the location of the kickstart file>` no fim das opções de arranque e prima **Enter**.

7. Aguarde a conclusão da instalação. Quando estiver concluída, a máquina virtual será automaticamente encerrada. O VHD de Linux está agora pronto para ser carregado para o Azure.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O controlador de Hyper-V não foi incluído no disco de RAM inicial quando utilizar um hipervisor não Hyper-V

Em alguns casos, programas de instalação do Linux podem não incluir os controladores para o Hyper-V inicial disco de RAM (initrd ou initramfs), a menos que Linux Deteta que está em execução num ambiente Hyper-V.

Quando estiver a utilizar um sistema de Virtualização diferente (ou seja, Virtualbox Xen, etc.) para preparar a imagem do Linux, poderá ter de reconstruir initrd para se certificar de que, pelo menos, os módulos de kernel hv_vmbus e hv_storvsc estão disponíveis no disco de RAM inicial. Este é um problema conhecido, pelo menos, em sistemas baseados na distribuição do Red Hat a montante.

Para resolver este problema, adicione módulos do Hyper-V para initramfs e reconstrui-lo:

Editar `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

Reconstrua initramfs:

        # dracut -f -v

Para obter mais detalhes, consulte as informações [reconstruir initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passos seguintes
Agora, está pronto a utilizar o seu disco rígido virtual do Red Hat Enterprise Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, consulte os passos 2 e 3 na [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Para obter mais detalhes sobre os hipervisores que são certificados para serem executadas Red Hat Enterprise Linux, consulte [o Web site do Red Hat](https://access.redhat.com/certified-hypervisors).
