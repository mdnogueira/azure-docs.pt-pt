---
title: Manter os ficheiros para Bash na Shell de nuvem do Azure | Microsoft Docs
description: "Instruções sobre como Bash na Shell de nuvem do Azure persistir ficheiros."
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: a04342f77126afe1e0a2707f1d213e0a8eb5825d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>Como funciona o Bash no armazenamento de nuvem Shell 
Bash na Shell de nuvem persistir ficheiros através de ambos dos seguintes métodos: 
* Criar uma imagem de disco do seu `$Home` diretório para manter todo o conteúdo dentro do diretório. A imagem de disco é guardada na partilha de ficheiro especificado como `acc_<User>.img` em `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, e alterações sincroniza-se automaticamente. 
* Montar a partilha de ficheiro especificado como `clouddrive` no seu `$Home` diretório para interação direta de partilha de ficheiros. `/Home/<User>/clouddrive`está mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os ficheiros na sua `$Home` diretório, tal como chaves SSH, são mantidas da imagem de disco do utilizador, que é armazenada na partilha de ficheiro instalado. Aplicar as melhores práticas, ao manter as informações no seu `$Home` diretório e partilha de ficheiros montada.

## <a name="use-the-clouddrive-command"></a>Utilize o `clouddrive` comando
Com Bash na Shell de nuvem, pode executar um comando chamado `clouddrive`, que lhe permite atualizar manualmente a partilha de ficheiros que está montada shell de nuvem.
![Executar o comando "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montar um novo clouddrive

### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para a montagem manual
Pode atualizar a partilha de ficheiros associado à Shell de nuvem utilizando o `clouddrive mount` comando.

Se montar uma partilha de ficheiros existentes, as contas de armazenamento tem de ser:
* Armazenamento localmente redundante ou armazenamento georredundante para suportar a partilhas de ficheiros.
* Localizado na sua região atribuído. Quando a integração, a região que está atribuídos ao está listada no nome do grupo de recursos `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>O comando de montagem clouddrive

> [!NOTE]
> Se estiver a montar uma nova partilha de ficheiros, uma nova imagem do utilizador foi criada para o `$Home` diretório. O anterior `$Home` imagem é mantida na partilha de ficheiros de anterior.

Execute o `clouddrive mount` comando com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver mais detalhes, execute `clouddrive mount -h`, conforme mostrado aqui:

![Com o ' clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Desmonte clouddrive
Pode desmontar uma partilha de ficheiros que está montada para nuvem Shell em qualquer altura. Uma vez que a Shell de nuvem requer uma partilha de ficheiros instalado para ser utilizado, será solicitado para criar e montar outra partilha de ficheiros na sessão seguinte.

1. Execute `clouddrive unmount`.
2. Confirmar e confirme os avisos.

A partilha de ficheiros irá continuar a existir, a menos que eliminá-lo manualmente. Shell de nuvem já não pesquisará esta partilha de ficheiros em sessões subsequentes. Para ver mais detalhes, execute `clouddrive unmount -h`, conforme mostrado aqui:

![Com o ' clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Embora a executar este comando não eliminará quaisquer recursos, como eliminar manualmente um grupo de recursos, a conta de armazenamento, ou partilha de ficheiros que está mapeada para a nuvem Shell erase sua `$Home` imagem de disco de diretório e os ficheiros a partilha de ficheiros. Esta ação não pode ser anulada.

## <a name="list-clouddrive"></a>Lista`clouddrive`
Para detetar que partilha de ficheiros está montada como `clouddrive`, execute o `df` comando. 

O caminho de ficheiro para clouddrive irá mostrar que o nome da conta de armazenamento e o ficheiro partilham no URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Passos seguintes
[Bash no guia de introdução de Shell de nuvem](quickstart.md) <br>
[Saiba mais sobre armazenamento de ficheiros do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Saiba mais sobre as etiquetas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
