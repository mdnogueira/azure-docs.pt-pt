---
title: Criar um par de chaves SSH para as VMs do Linux no Azure | Microsoft Docs
description: "Criar um par de chaves pública e privada SSH para as VMs do Azure Linux de forma segura."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: rasquill
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19acd4efca7ef043f31b436b96f9129caee9591b
ms.lasthandoff: 04/03/2017



---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Criar um par de chaves pública e privada SSH para as VMs do Linux

Este artigo mostra-lhe como gerar um par de ficheiros de chaves públicas e privadas RSA versão 2 do protocolo SSH para utilização em VMs do Linux.  Com um par de chaves SSH pode criar Máquinas Virtuais no Azure que estão predefinidas para utilizar chaves SSH para autenticação, eliminando a necessidade de palavras-passe para iniciar sessão.  As palavras-passe podem ser adivinhadas e abrir as VMs até as tentativas de força bruta excessiva desvendarem a sua palavra-passe. As VMs criadas com Modelos do Azure ou a `azure-cli` podem incluir a sua chave pública do SSH como parte da implementação, removendo um passo de configuração de pós-implementação de desativação de inícios de sessão de palavra-passe para SSH.

## <a name="quick-commands"></a>Comandos Rápidos

Execute os seguintes comandos a partir de uma shell do Bash, substituindo os exemplos pelas suas escolhas.

O ficheiro de chave pública SSH é criado por predefinição em `~/.ssh/id_rsa.pub`. Quando lhe for pedido para utilizar o comando seguinte, deve criar uma "frase de acesso" para proteger a chave privada. (A frase de acesso é uma palavra-passe utilizada para encriptar a chave privada.)

```bash
ssh-keygen -t rsa -b 2048 
```

Adicione a chave recentemente criada para `ssh-agent`:

```bash
ssh-add ~/.ssh/id_rsa
```

> [!IMPORTANT] 
> Os comandos acima funcionam em sistemas operativos Linux de quase todas as distribuições, mas não funcionam necessariamente em contentores, visto que o ambiente pode estar radicalmente restrito. 

## <a name="detailed-walkthrough"></a>Instruções Detalhadas

Utilizar chaves públicas e privadas de SSH é a forma mais fácil de iniciar sessão nos seus servidores Linux. [Criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) é uma maneira muito mais segura de iniciar sessão na sua VM do Linux ou BSD no Azure do que as palavras-passe, que podem ser forçadas de maneira bruta com maior facilidade.

> [!IMPORTANT]
> A sua chave pública pode ser partilhada com qualquer pessoa; mas apenas o utilizador (ou a infraestrutura de segurança local) possui a chave privada.  A chave privada SSH deve ter uma [palavra-passe muito segura](https://www.xkcd.com/936/) (origem:[xkcd.com](https://xkcd.com)) para salvaguardá-la.  Esta palavra-passe permite aceder facilmente à chave privada SSH e **não é** a palavra-passe da conta de utilizador.  Quando adiciona uma palavra-passe à chave SSH, esta encripta a chave privada para que a chave privada com AES de 128 bits não seja utilizável sem a palavra-passe para a desencriptar.  Se um atacante roubou a sua chave privada e esta não tiver uma palavra-passe, o atacante consegue utilizar essa chave privada para iniciar sessão em quaisquer servidores que tenham a chave pública correspondente.  Se a chave privada for protegida por palavra-passe, não pode ser utilizada por esse atacante, fornecendo uma camada adicional de segurança à sua infraestrutura no Azure.

Este artigo cria ficheiros de chaves públicas e privadas RSA versão 2 do protocolo SSH, que são recomendados para implementações no Resource Manager.  As chaves *ssh-rsa* são necessárias no [portal](https://portal.azure.com) para implementações clássicas e de Resource Manager.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>Desativar as palavras-passe SSH com as chaves SSH

O Azure requer, pelo menos, 2048 bits e chaves públicas e privadas com o formato ssh-rsa. Para criar as chaves, utilize `ssh-keygen`, que coloca uma série de perguntas e, em seguida, cria uma chave privada e uma chave pública correspondente. Quando é criada uma VM do Azure, a chave pública é copiada para `~/.ssh/authorized_keys`.  As chaves SSH no `~/.ssh/authorized_keys` são utilizadas para desafiar o cliente para corresponder a chave privada correspondente numa ligação de início de sessão SSH.  Quando uma VM Linux do Azure é criada com chaves SSH para a autenticação, o Azure configura o servidor SSHD para não permitir inícios de sessão de palavra-passe, apenas chaves SSH.  Por conseguinte, com a criação de VMs Linux do Azure com chaves SSH, pode ajudar a protegera implementação da VM e guardar o passo de configuração de implementação de publicação típica da desativação de palavras-passe no ficheiro de configuração sshd_config.

## <a name="using-ssh-keygen"></a>Utilizar o ssh-keygen

Este comando cria um par de chaves SSH protegidas por palavra-passe (encriptado) através de uma RSA com 2048 bits e será inserido um comentário para uma fácil identificação.  

Por predefinição, as chaves SSH são mantidas no diretório `~/.ssh`.  Se não tiver um diretório `~/.ssh`, o comando `ssh-keygen` cria-o por si com as permissões corretas.

```bash
ssh-keygen \
-t rsa \
-b 2048 
```

*Comando explicado*

`ssh-keygen` = o programa utilizado para criar as chaves

`-t rsa` = o tipo de chave a criar, que é o formato [wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bits da chave


## <a name="classic-portal-and-x509-certs"></a>Portal clássico e certificados X.509

Se estiver a utilizar [portal clássico](https://manage.windowsazure.com/) do Azure, requer certificados X.509 para as chaves SSH.  Não existem outros tipos de chaves públicas SSH permitidas, estas *devem* ser certificados X.509.

Para criar um certificado X.509 a partir da chave privada SSH-RSA existente:

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>Implementação clássica com `asm`

Se estiver a utilizar o modelo de implementação clássica (CLI de gestão do serviço do Azure `asm`), pode utilizar uma chave pública SSH-RSA ou uma chave formatada RFC4716 num contentor **pem**.  A chave pública SSH-RSA é o que foi criado anteriormente neste artigo com a `ssh-keygen`.

Para criar uma chave formatada RFC4716 a partir de uma chave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemplo de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ahmet/.ssh/id_rsa.
Your public key has been saved in /home/ahmet/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Ficheiros de chave guardados:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

O nome do par de chaves para este artigo.  A predefinição é ter um par de chaves com o nome **id_rsa** e algumas ferramentas podem contar com o nome de ficheiro de chave privada **id_rsa**, pelo que é uma boa ideia ter um. O diretório `~/.ssh/` é a localização predefinida de todos os pares de chaves SSH e do ficheiro de configuração SSH.  Se não for especificado com um caminho completo, a `ssh-keygen` irá criar as chaves no atual diretório de trabalho, não a predefinição `~/.ssh`.

Uma listagem do diretório `~/.ssh`.

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

Palavra-passe da chave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` refere-se a uma palavra-passe utilizada para encriptar a chave privada como "frase de acesso".  Recomenda-se *vivamente* que adicione uma frase de acesso aos seus pares de chaves. Sem uma frase de acesso a proteger a chave privada, qualquer pessoa com o ficheiro de chave pode utilizá-la para iniciar sessão em qualquer servidor com a chave pública correspondente. Adicionar uma frase de acesso oferece uma maior proteção no caso de alguém conseguir obter acesso ao seu ficheiro de chave privada, dando-lhe tempo para alterar as chaves utilizadas para o autenticar.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Utilizar o ssh-agent para armazenar a palavra-passe da chave privada

Para evitar escrever a frase de acesso do ficheiro de chave privada com cada início de sessão SSH, pode utilizar `ssh-agent` para colocar em cache a palavra-passe do ficheiro de chave privada. Se estiver a utilizar um Mac, a keychain armazena as palavras-passe das chaves privadas em segurança quando invocar `ssh-agent`.

Verifique e utilize `ssh-agent` e `ssh-add` para informar o sistema SSH sobre os ficheiros de chave para que a frase de acesso não tenha de ser utilizada de forma interativa.

```bash
eval "$(ssh-agent -s)"
```

Em seguida, adicione a chave privada a `ssh-agent` utilizando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

A palavra-passe da chave privada está agora armazenada numa `ssh-agent`.

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>Utilizar `ssh-copy-id` para instalar a nova chave
Se já tiver criado uma VM, pode instalar a nova chave pública SSH na VM do Linux com o seguinte comando ao substituir o nome de utilizador da VM e o endereço do servidor pelos seus próprios valores:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um ficheiro de configuração SSH

É uma melhor prática para criar e configurar um ficheiro `~/.ssh/config` para acelerar os inícios de sessão e otimizar o comportamento do cliente SSH.

O exemplo seguinte mostra uma configuração padrão.

### <a name="create-the-file"></a>Criar o ficheiro

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite o ficheiro para adicionar a nova configuração de SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Ficheiro `~/.ssh/config` de exemplo:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Esta configuração de SSH fornece-lhe secções para cada servidor para que cada um tenha o seu próprio par de chaves dedicado. As predefinições (`Host *`) são para quaisquer anfitriões que não correspondam a quaisquer anfitriões específicos mais acima no ficheiro de configuração.

### <a name="config-file-explained"></a>Ficheiro de configuração explicado

`Host` = o nome do anfitrião que está a ser chamado no terminal.  `ssh fedora22` indica a `SSH` para utilizar os valores no bloco de definições denominado `Host fedora22` NOTA: o anfitrião pode ser qualquer etiqueta que seja lógica para a sua utilização e não representa o nome do anfitrião real de qualquer servidor.

`Hostname 102.160.203.241` = o endereço IP ou o nome DNS do servidor ao qual está a aceder.

`User ahmet` = a conta de utilizador remota a utilizar ao iniciar sessão no servidor.

`PubKeyAuthentication yes` = indica ao SSH que pretende utilizar uma chave SSH para iniciar sessão.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = a chave privada SSH e a chave pública correspondente a utilizar para autenticação.

## <a name="ssh-into-linux-without-a-password"></a>SSH no Linux sem uma palavra-passe

Agora que tem um par de chaves SSH e um ficheiro de configuração SSH configurado, pode iniciar sessão na VM do Linux de maneira rápida e segura. A primeira vez que iniciar sessão num servidor utilizando uma chave SSH, o comando pede-lhe a frase de acesso desse ficheiro de chave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Comando explicado

Quando `ssh fedora22` é executado, o SSH primeiro localiza e carrega todas as definições do bloco `Host fedora22` e, em seguida, carrega todas as restantes definições a partir do último bloqueio, `Host *`.

## <a name="next-steps"></a>Passos Seguintes

O passo seguinte consiste na criação de VMs com Linux do Azure utilizando a nova chave pública SSH.  As VMs do Azure criadas com uma chave pública SSH como início de sessão estão mais protegidas do que as criadas com as palavras-passe, o método de início de sessão predefinido.  As VMs do Azure criadas com chaves SSH são, por predefinição, configuradas com as palavras-passe desativadas, evitando tentativas de adivinhação forçadas. Se necessitar de mais assistência na criação do par de chaves SSH ou precisar de certificados adicionais, como para utilização com o portal clássico, veja [Passos detalhados para criar pares de chaves e certificados SSH](create-ssh-keys-detailed.md).

* [Criar uma VM do Linux segura com um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux segura com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux segura com a CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

