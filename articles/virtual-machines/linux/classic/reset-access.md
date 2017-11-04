---
title: Repor palavra-passe de VM com Linux e a chave SSH da CLI | Microsoft Docs
description: "Como utilizar a extensão VMAccess da Interface de linha de comandos do Azure (CLI) para repor uma palavra-passe de VM com Linux ou uma chave SSH, corrija a configuração de SSH e verificação de consistência de disco"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: cynthn
ms.openlocfilehash: 74765877e7836d6878284b350a25d8355dc83d7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Como repor uma palavra-passe de VM com Linux ou uma chave SSH, corrija a configuração de SSH e verificação de consistência de disco com a extensão VMAccess
Se não é possível ligar a uma máquina virtual do Linux no Azure devido a uma palavra-passe da esquecida, uma chave Secure Shell (SSH) incorreto, ou um problema com a configuração de SSH, utilizar a extensão VMAccessForLinux com a CLI do Azure para repor a palavra-passe ou chave SSH, corrija a configuração de SSH e verificação de consistência de disco. 

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Com a CLI do Azure, utilize o **conjunto de extensão de vm do azure** comando a partir da sua interface de linha de comandos (Bash, Terminal, linha de comandos) para aceder aos comandos. Executar **conjunto de extensão de vm do azure ajuda** para utilização de extensão de detalhado.

Com a CLI do Azure, pode efetuar as seguintes tarefas:

* [Repor a palavra-passe](#pwresetcli)
* [Repor a chave SSH](#sshkeyresetcli)
* [Repor a palavra-passe e a chave SSH](#resetbothcli)
* [Criar uma nova conta de utilizador de sudo](#createnewsudocli)
* [Reposição da configuração SSH](#sshconfigresetcli)
* [Eliminar um utilizador](#deletecli)
* [Apresentar o estado da extensão VMAccess](#statuscli)
* [Verificação de consistência de discos adicionados](#checkdisk)
* [Reparar discos adicionados na sua VM do Linux](#repairdisk)

## <a name="prerequisites"></a>Pré-requisitos
Terá de fazer o seguinte:

* Terá [instalar a CLI do Azure](../../../cli-install-nodejs.md) e [ligar à sua subscrição](../../../xplat-cli-connect.md) a utilização de recursos do Azure associados à sua conta.
* Defina o modo correto para o modelo de implementação clássica, escrevendo o seguinte na linha de comandos:
    ``` 
        azure config mode asm
    ```
* Ter uma nova palavra-passe ou o conjunto de chaves SSH, se pretender repor deles. Não precisa de estes se de que pretende repor a configuração de SSH.

## <a name="pwresetcli"></a>Repor a palavra-passe
1. Crie um ficheiro no seu computador local com o nome PrivateConf.json com estas linhas. Substitua **myUserName** e  **myP@ssW0rd**  com o seu nome de utilizador e palavra-passe e definir a sua própria data de expiração.

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. Executar este comando, substituindo o nome da sua máquina virtual para **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>Repor a chave SSH
1. Crie um ficheiro denominado PrivateConf.json com estes conteúdos. Substitua o **myUserName** e **mySSHKey** valores pelas suas informações.

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. Executar este comando, substituindo o nome da sua máquina virtual para **myVM**.
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Repor a palavra-passe e a chave SSH
1. Crie um ficheiro denominado PrivateConf.json com estes conteúdos. Substitua o **myUserName**, **mySSHKey** e  **myP@ssW0rd**  valores pelas suas informações.

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. Executar este comando, substituindo o nome da sua máquina virtual para **myVM**.

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>Criar uma nova conta de utilizador de sudo

Se se esquecer da sua nome de utilizador, pode utilizar o VMAccess para criar um novo com a autoridade de sudo. Neste caso, o nome de utilizador e palavra-passe existentes não serão modificados.

Para criar um novo utilizador de sudo com acesso de palavra-passe, utilize o script em [repor a palavra-passe](#pwresetcli) e especifique o novo nome de utilizador.

Para criar um novo utilizador de sudo com acesso de chave SSH, utilize o script em [repor a chave SSH](#sshkeyresetcli) e especifique o novo nome de utilizador.

Também pode utilizar [repor a palavra-passe e a chave SSH](#resetbothcli) para criar um novo utilizador com palavra-passe e acesso de chave SSH.

## <a name="sshconfigresetcli"></a>Reposição da configuração SSH
Se a configuração de SSH está num estado indesejado, poderá também perder o acesso à VM. Pode utilizar a extensão VMAccess para repor a configuração no estado predefinido. Para tal, basta definir a chave "reset_ssh" para "True". A extensão irá reiniciar o servidor SSH, abra a porta SSH no VM e reposição da configuração SSH para os valores predefinidos. A conta de utilizador (nome, palavra-passe ou chaves SSH) não será alterada.

> [!NOTE]
> O ficheiro de configuração de SSH que obtém repor está localizado em /etc/ssh/sshd_config.
> 
> 

1. Crie um ficheiro denominado PrivateConf.json com este conteúdo.

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. Executar este comando, substituindo o nome da sua máquina virtual para **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>Eliminar um utilizador
Se pretender eliminar uma conta de utilizador sem iniciar sessão para a VM diretamente, pode utilizar este script.

1. Crie um ficheiro denominado PrivateConf.json com este conteúdo, substituindo o nome de utilizador para remover para **removeUserName**. 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. Executar este comando, substituindo o nome da sua máquina virtual para **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>Apresentar o estado da extensão VMAccess
Para apresentar o estado da extensão VMAccess, execute este comando.

```
        azure vm extension get
```

## <a name='checkdisk'></a>Verificação de consistência de discos adicionados
Para executar ou a fsck em todos os discos na sua máquina virtual do Linux, terá de fazer o seguinte:

1. Crie um ficheiro denominado PublicConf.json com este conteúdo. Verifique o que disco assume um valor booleano para a verificação discos ligados à máquina virtual ou não. 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. Execute este comando a executar, substituindo o nome da sua máquina virtual para **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>Discos de reparação
Para reparar discos que não estão a montar ou que têm erros de configuração de montagem, utilize a extensão VMAccess para repor a configuração de montagem na sua máquina virtual do Linux. Substituindo o nome do disco para **myDisk**.

1. Crie um ficheiro denominado PublicConf.json com este conteúdo. 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. Execute este comando a executar, substituindo o nome da sua máquina virtual para **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>Passos seguintes
* Se pretender utilizar cmdlets do Azure PowerShell ou modelos Azure Resource Manager para repor a palavra-passe ou chave SSH, corrija o consulte SSH de configuração e verificação de consistência de disco, o [documentação da extensão VMAccess no GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Também pode utilizar o [portal do Azure](https://portal.azure.com) para repor a palavra-passe ou chave SSH de uma VM com Linux implementados no modelo de implementação clássica. Atualmente não é possível utilizar o portal efetue este para uma VM com Linux implementados no modelo de implementação Resource Manager.
* Consulte [sobre extensões de máquina virtual e funcionalidades](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a utilização de extensões VM para máquinas virtuais do Azure.

