---
title: "Resolver problemas relacionados com alterações de nome de dispositivo de VM com Linux no Azure | Microsoft Docs"
description: "Explica por que motivo o dispositivo de VM com Linux os nomes de alteração e como resolver o problema."
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.openlocfilehash: 249d2cb42e2d8534af1e27da7f5d909b71eccbc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Resolver problemas relacionados com alterações de nome de dispositivo de VM com Linux

Este artigo explica por que razão os nomes de dispositivo alterar depois de reiniciar uma VM com Linux ou reattach os discos de dados. O artigo também fornece soluções para este problema.

## <a name="symptoms"></a>Sintomas
Pode deparar-se os seguintes problemas quando VMs do Linux em execução no Microsoft Azure:

- A VM não consegue efetuar o arranque após um reinício.
- Quando os discos de dados estiver desligados e novamente ligados, os nomes de dispositivo de disco são alterados.
- Uma aplicação ou script que faça referência a um disco ao utilizar o nome do dispositivo falha porque o nome do dispositivo foi alterada.

## <a name="cause"></a>Causa

Caminhos de dispositivo no Linux não são garantidos para estar consistente entre reinícios. Nomes de dispositivos é composto por números principais (letras) e números secundários. Quando o controlador de dispositivo de armazenamento do Linux Deteta um novo dispositivo, o controlador atribui números principais e secundários do intervalo disponível para o dispositivo. Quando um dispositivo for removido, os números de dispositivo são libertados para reutilização.

O problema ocorre porque o dispositivo de análise no Linux está agendado pelo subsistema SCSI ocorrer no modo assíncrono. Como resultado, um nome de caminho do dispositivo pode variar entre reinícios. 

## <a name="solution"></a>Solução

Para resolver este problema, utilize a nomenclatura persistente. Existem quatro formas de utilizar a atribuição de nomes persistente: por etiqueta de sistema de ficheiros, pelo UUID de, pelo ID ou pelo caminho. Recomendamos que utilize a etiqueta de sistema de ficheiros ou UUID de VMs do Linux do Azure. 

A maioria das distribuições de fornecer o `fstab` **nofail** ou **nobootwait** parâmetros. Estes parâmetros ativar um sistema de arranque quando o disco não é possível montar no arranque. Consulte a documentação de distribuição para obter mais informações sobre estes parâmetros. Para obter informações sobre como configurar uma VM com Linux para utilizar um UUID quando adicionar um disco de dados, consulte [ligar à VM do Linux para montar o disco novo](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Quando é instalado o agente Linux do Azure numa VM, o agente utiliza regras de Udev para construir um conjunto de ligações simbólicas sob o caminho de /dev/disk/azure. Aplicações e scripts utilizam regras de Udev para identificar discos que estão ligados à VM, juntamente com o tipo de disco e LUNs do disco.

### <a name="identify-disk-luns"></a>Identificar os LUNs do disco

As aplicações utilizar LUNs para localizar todos os discos ligados e construir as ligações simbólicas. O agente Linux do Azure inclui regras de Udev que configurar as ligações simbólicas de LUN para os dispositivos:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 
Informações de LUN da conta de convidado do Linux são obtidas utilizando `lsscsi` ou uma ferramenta semelhante:

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

As informações de LUN do convidado são utilizadas com os metadados de subscrição do Azure para localizar o VHD no armazenamento do Azure que contém os dados de partição. Por exemplo, pode utilizar o `az` CLI:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                            
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Detetar UUIDs não de sistema de ficheiros utilizando blkid

O resultado de leitura de aplicações e scripts `blkid`, ou semelhantes fontes de informação, construir as ligações simbólicas no caminho /dev. O resultado mostra o UUIDs não de todos os discos que estão anexados a VM e os respetivos ficheiros de dispositivo associadas:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

As regras de Udev do agente Linux do Azure construir um conjunto de ligações simbólicas sob o caminho de /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplicações utilizam as ligações para identificar o dispositivo de disco de arranque e o disco (efémeras) de recursos. No Azure, as aplicações devem ter um aspeto nos caminhos /dev/disk/azure/root-part1 ou /dev/disk/azure-resource-part1 para detetar estas partições.

Quaisquer partições adicionais do `blkid` lista residem num disco de dados. Aplicações manter o UUID para estas partições e utilizam um caminho para detetar o nome de dispositivo em tempo de execução:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Obter as regras de armazenamento do Azure mais recentes

Para obter as regras de armazenamento do Azure mais recentes, execute os seguintes comandos:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Consultar também

Para obter mais informações, veja os artigos seguintes:

- [Ubuntu: Utilizando o UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: A nomenclatura persistente](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: O UUIDs não pode fazer por si](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introdução gestão de dispositivos num sistema Linux Moderno](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

