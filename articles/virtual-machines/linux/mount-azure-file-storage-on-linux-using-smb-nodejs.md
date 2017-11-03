---
title: Montagem File storage do Azure em VMs do Linux utilizando o SMB com a CLI do Azure 1.0 | Microsoft Docs
description: Como montar o File storage do Azure em VMs do Linux utilizando o SMB
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 4951860630f0aad107d0846d52ebe4423ee0b91c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Montagem File storage do Azure em VMs do Linux utilizando o SMB com a CLI do Azure 1.0

Este artigo mostra como montar o File storage do Azure numa VM com Linux utilizando o protocolo Server Message Block (SMB). O File storage oferece partilhas de ficheiros na nuvem através do protocolo SMB padrão. Os requisitos são:

* Um [conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Secure Shell (SSH) pública e privada dos ficheiros de chaves](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>Versões CLI para utilizar
Pode concluir a tarefa utilizando uma das seguintes versões de interface de linha de comandos (CLI):

- [Azure CLI 1.0](#quick-commands) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-nossa próxima geração CLI para o modelo de implementação de gestão de recursos


## <a name="quick-commands"></a>Comandos rápidos
Para realizar a tarefa rapidamente, siga os passos nesta secção. Para obter mais informações e contexto, início ao ["Instruções detalhadas"](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) secção.

### <a name="prerequisites"></a>Pré-requisitos
* Um grupo de recursos
* Uma rede virtual do Azure
* Um grupo de segurança de rede com um SSH entrada
* Uma sub-rede
* Uma conta de armazenamento do Azure
* Chaves de conta de armazenamento do Azure
* Uma partilha de armazenamento de ficheiros do Azure
* Uma VM com Linux

Substitua qualquer exemplos as suas próprias definições.

### <a name="create-a-directory-for-the-local-mount"></a>Criar um diretório de montagem local

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Montar o partilha do SMB para o ponto de montagem de armazenamento de ficheiros

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Manter a montagem após um reinício
Adicione a seguinte linha para `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

O File storage oferece partilhas de ficheiros na nuvem que utilizam o protocolo SMB padrão. Com a versão mais recente do armazenamento de ficheiros, também é possível montar uma partilha de ficheiros de qualquer sistema operativo que suporta o SMB 3.0. Quando utiliza uma montagem SMB no Linux, obtenha cópias de segurança fácil para um robusta, permanente arquivo local de armazenamento que é suportado por um SLA.

A mover os ficheiros de uma VM para uma montagem SMB que está alojada no armazenamento de ficheiros é uma excelente forma de registos de depuração. Isto acontece porque a mesma partilha SMB pode ser montada localmente na estação de trabalho Mac, Linux ou do Windows. SMB não é a melhor solução para transmissão em fluxo Linux ou os registos de aplicação em tempo real, porque o protocolo SMB não está incorporado para processar as tarefas de registo pesada. Uma ferramenta de camada de registo dedicado, unificada como Fluentd seria uma melhor opção que SMB para a recolha de Linux e aplicação de registo de saída.

Nestas instruções detalhadas, iremos criar os pré-requisitos necessários para primeiro criar a partilha de ficheiros de armazenamento e montá-la através de SMB numa VM com Linux.

1. Crie uma conta de armazenamento do Azure utilizando o seguinte código:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Mostra as chaves de conta de armazenamento.

    Quando cria uma conta de armazenamento, as chaves de conta são criadas pares para que pode ser rotação sem qualquer interrupção do serviço. Quando mudar para a segunda chave na par, crie um novo par de chaves. Chaves de conta de armazenamento novas sempre são criadas pares, assegurando que tem sempre, pelo menos, uma chave de armazenamento não utilizados pronta para mudar para. Para mostrar as chaves de conta de armazenamento, utilize o seguinte código:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Crie partilha do File storage.

    A partilha do File storage contém a partilha SMB. A quota é sempre expresso em gigabytes (GB). Para criar a partilha do File storage, utilize o seguinte código:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Crie o diretório de ponto de montagem.

    Tem de criar um diretório local no sistema de ficheiros do Linux para montar a partilha SMB. Nada escritas ou ler a partir do diretório de montagem local é reencaminhado para a partilha do SMB que está alojada no armazenamento de ficheiros. Para criar o diretório, utilize o seguinte código:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Monte a partilha SMB, utilizando o seguinte código:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Manter a montagem SMB através de reinícios.

    Quando reiniciar a VM com Linux, a partilha SMB montada é desmontada durante o encerramento. Para remontar na partilha SMB no arranque, tem de adicionar uma linha para o /etc/fstab Linux. Linux utiliza o ficheiro de fstab para listar os sistemas de ficheiros que necessita de montagem durante o processo de arranque. Adicionar a partilha SMB garante que a partilha do File storage é um sistema de ficheiro permanentemente instalado para a VM com Linux. É possível adicionar o armazenamento de ficheiros de partilha do SMB para uma nova VM ao utilizar init de nuvem.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Passos seguintes

- [Nuvem init a utilizar para personalizar uma VM com Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adicionar um disco a uma VM do Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Encriptar discos uma VM com Linux utilizando a CLI do Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
