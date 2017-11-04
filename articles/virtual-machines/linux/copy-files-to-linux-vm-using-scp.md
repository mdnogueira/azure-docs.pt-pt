---
title: Mover os ficheiros de e para as VMs do Linux do Azure com o SCP | Microsoft Docs
description: "Mover com segurança os ficheiros de e para uma VM com Linux no Azure utilizando o SCP e um par de chaves SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: 736f7c11ec3de04f1ad52ee29d0a4c952c9b0545
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Mover os ficheiros de e para uma VM com Linux através de SCP

Este artigo mostra como mover os ficheiros da sua estação de trabalho até uma VM com Linux do Azure ou a partir de uma VM do Azure Linux para baixo para a estação de trabalho, utilizando Secure cópia (SCP). Mover ficheiros entre a estação de trabalho e de uma VM com Linux, rapidamente e segura, é essencial para gerir a sua infraestrutura do Azure. 

Para este artigo, é necessário um Linux VM implementado no Azure com [ficheiros de chaves públicos e privados SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Também precisa de um cliente de SCP para o seu computador local. É desenvolvida com SSH e incluído na shell de deteção predefinido da maioria dos computadores Linux e Mac e algumas shells do Windows.

## <a name="quick-commands"></a>Comandos rápidos

Copiar um ficheiro até a VM com Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Copie um ficheiro da VM com Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

Como exemplos, podemos mover um ficheiro de configuração do Azure até uma VM com Linux e a solicitação para baixo de um diretório do ficheiro de registo, ambos com chaves SCP e SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticação do par de chaves SSH

SCP utiliza SSH para a camada de transporte. SSH processa a autenticação no anfitrião de destino e o ficheiro de um túnel encriptado fornecido por predefinição com SSH, é movido. Para a autenticação SSH, podem ser utilizadas nomes de utilizador e palavras-passe. No entanto, a autenticação de chave pública e privada SSH são recomendadas como melhor prática de segurança. Depois de SSH tem de autenticar a ligação, o SCP começa, em seguida, copiar o ficheiro. Utilizar está corretamente configurada `~/.ssh/config` e SSH chaves públicas e privadas, a ligação de SCP podem ser estabelecidas utilizando apenas um nome de servidor (ou endereço IP). Se tiver apenas uma chave SSH, SCP procura-na `~/.ssh/` diretório e utiliza-o por predefinição para iniciar sessão VM.

Para obter mais informações sobre como configurar a sua `~/.ssh/config` e chaves públicas e privadas SSH, consulte [criar SSH chaves](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP um ficheiro para uma VM com Linux

Para o primeiro exemplo, vamos copie um ficheiro de configuração do Azure até uma VM com Linux que é utilizada para implementar a automatização. Porque este ficheiro contém credenciais de API do Azure, nomeadamente segredos, segurança, é importante. O túnel encriptado fornecido pelo SSH protege o conteúdo do ficheiro.

O comando seguinte copia local *.azure/configuração* ficheiro para uma VM do Azure com o FQDN *myserver.eastus.cloudapp.azure.com*. O nome de utilizador de administrador na VM do Azure é *azureuser*. O ficheiro está direcionado para o */home/azureuser/* diretório. Substitua os seus próprios valores neste comando.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP um diretório de uma VM com Linux

Neste exemplo, vamos copie um diretório de ficheiros de registo da VM com Linux para baixo para a estação de trabalho. Um ficheiro de registo pode ou não pode conter dados confidenciais ou secretos. No entanto, utilizar o SCP garante que o conteúdo dos ficheiros de registo é encriptado. Utilizar o SCP para transferir os ficheiros é a forma mais fácil para obter o diretório de registo e ficheiros para baixo para a estação de trabalho tendo simultaneamente segura.

O comando seguinte copia os ficheiros no */homeazureuser/registos/* diretório na VM do Azure para o diretório /tmp dos locais:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

O `-r` cli sinalizador dá instruções ao SCP para copiar recursivamente os ficheiros e diretórios a partir do ponto do diretório listados no comando.  Repare também que a sintaxe da linha de comandos é semelhante a um `cp` copiar o comando.

## <a name="next-steps"></a>Passos seguintes

* [Gerir utilizadores, SSH e verificação ou reparar discos em VMs do Linux do Azure com a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)