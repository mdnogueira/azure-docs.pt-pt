---
title: Como repor o Linux palavra-passe local em VMs do Azure | Microsoft Docs
description: Introduzir os passos para repor o Linux palavra-passe local na VM do Azure
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/3/2017
ms.author: delhan
ms.openlocfilehash: d24d1f194b74b9da6a463d3efd741b402102fde3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Como repor o Linux palavra-passe local em VMs do Azure

Este artigo apresenta vários métodos para repor palavras-passe de Linux Máquina Virtual (VM) local. Se a conta de utilizador expirou ou se pretende criar uma nova conta, pode utilizar os seguintes métodos para criar uma nova conta de administrador local e obter novamente acesso à VM.

## <a name="symptoms"></a>Sintomas

Não é possível iniciar sessão para a VM e receberá uma mensagem que indica que a palavra-passe que utilizou está incorreta. Além disso, não é possível utilizar VMAgent para repor a palavra-passe no Portal do Azure. 

## <a name="manual-password-reset-procedure"></a>Procedimento de palavra-passe de reposição manual

1.  Elimine a VM e manter os discos anexados.

2.  Anexe o disco de SO como um disco de dados para outra VM temporal na mesma localização.

3.  Execute o seguinte comando SSH na VM para se tornar um utilizador Super-obrigatórios temporal.


    ~~~~
    sudo su
    ~~~~

4.  Executar **fdisk -l** ou consulte os registos de sistema para localizar o disco anexado recentemente. Localize o nome de unidade para montar. Em seguida, na temporal VM, procure no ficheiro de registo relevantes.

    ~~~~
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ~~~~

    Exemplo de saída do comando grep é o seguinte:

    ~~~~
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ~~~~

5.  Criar um ponto de montagem chamado **tempmount**.

    ~~~~
    mkdir /tempmount
    ~~~~

6.  Monte o disco do SO no ponto de montagem. Normalmente, terá de montagem sdc1 ou sdc2. Isto irá depender a partição de alojamento no diretório de /etc/hosts do disco de máquina interrompida.

    ~~~~
    mount /dev/sdc1 /tempmount
    ~~~~

7.  Efetue uma cópia de segurança antes de efetuar alterações:

    ~~~~
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ~~~~

8.  Repor palavra-passe do utilizador que precisa de:

    ~~~~
    passwd <<USER>> 
    ~~~~

9.  Mova os ficheiros de modificação para a localização correta no disco da máquina interrompida.

    ~~~~
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    
10. Go back to the root and unmount the disk.

    ~~~~
    CD / umount /tempmount
    ~~~~

11. Detach the disk from the management portal.

12. Recreate the VM.

## Next steps

* [Troubleshoot Azure VM by attaching OS disk to another Azure VM](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: How to delete and re-deploy a VM from VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)