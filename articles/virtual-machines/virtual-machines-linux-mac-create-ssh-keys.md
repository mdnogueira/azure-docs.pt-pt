<properties
    pageTitle="Criar chaves SSH no Linux e Mac | Microsoft Azure"
    description="Gere e utilize as chaves SSH no Mac e Linux para os modelos de implementação clássica e do Resource Manager no Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="v-livech"/>

# Criar chaves SSH no Linux e Mac para VMs com Linux no Azure

Para criar uma chave SSH pública e privada protegida por palavra-passe, precisa de um terminal aberto na sua estação de trabalho.  Assim que tiver chaves SSH, pode criar novas VMs com essa chave por predefinição ou adicionar a chave pública para VMs existentes utilizando a CLI do Azure e os modelos do Azure.  Isto permitirá inícios de sessão sem palavras-passe através de SSH utilizando o método de autenticação muito mais seguro das Chaves em comparação com as palavras-passe.

## Lista de Comandos Rápidos

Nos seguintes exemplos de comandos, substitua os valores entre &lt; e &gt; pelos valores do seu próprio ambiente.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Introduza o nome do ficheiro que será guardado no diretório `~/.ssh/`:

```bash
<azure_fedora_id_rsa>
```

Introduza a frase de acesso para azure_fedora_id_rsa:

```bash
<correct horse battery staple>
```

Adicione a chave recentemente criada para `ssh-agent` no Linux e Mac (também adicionada ao OSX Keychain):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Copie a chave pública SSH para o Servidor Linux:

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

Teste o início de sessão através de chaves em vez de uma palavra-passe:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## Introdução

A utilização de chaves públicas e privadas SSH é a forma mais fácil de iniciar sessão nos servidores Linux, mas, além disso, a [criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) também fornece uma forma muito mais segura para iniciar sessão no Linux ou na VM BSD no Azure do que as palavras-passe, que podem sofrer um ataque de força bruta mais facilmente. A sua chave pública pode ser partilhada com qualquer pessoa; mas apenas o utilizador (ou a infraestrutura de segurança local) possui a chave privada.  A chave SSH privada criada terá uma [palavra-passe segura](https://www.xkcd.com/936/) para a proteger e esta palavra-passe é simplesmente para aceder à chave SSH privada e **não é** a palavra-passe da conta de utilizador.  Quando adiciona uma palavra-passe para a chave SSH, encripta a chave privada para que a chave privada não seja utilizável sem a palavra-passe para a desbloquear.  Se um atacante conseguir roubar a sua chave privada e não tiver uma palavra-passe, deverá conseguir utilizar essa chave privada para iniciar sessão nos servidores que têm a chave pública correspondente instalada.  Se a chave privada for protegida por palavra-passe, não pode ser utilizada por esse atacante, fornecendo uma camada adicional de segurança na sua infraestrutura no Azure.


Este artigo cria ficheiros de chave com o formato *ssh-rsa*, pois são recomendados para implementações no Resource Manager e necessários no [portal](https://portal.azure.com) para implementações clássicas e do resource manager.


## Criar as chaves SSH

O Azure requer, pelo menos, 2048 bits e chaves públicas e privadas com o formato ssh-rsa. Para criar o par, utilizaremos `ssh-keygen`, que coloca uma série de perguntas e, em seguida, escreve uma chave privada e uma chave pública correspondente. Ao criar a VM do Azure, passa o conteúdo da chave pública, que é copiado para a VM com Linux e é utilizado com a sua chave privada local e armazenada em segurança para o autenticar quando iniciar sessão.

## Utilizar o ssh-keygen

Este comando cria um Par de Chaves SSH protegido por palavra-passe (encriptado) através de uma RSA com 2048 bits e será comentado para o identificar facilmente.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_Comando explicado_

`ssh-keygen` = o programa utilizado para criar as chaves

`-t rsa` = o tipo de chave a criar, que é o [formato RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bits da chave

`-C "ahmet@fedoraVMAzure"` = um comentário acrescentado ao fim do ficheiro da chave pública para o identificar facilmente.  Normalmente, um e-mail é utilizado como o comentário, mas pode utilizar o que funcionar melhor para a sua infraestrutura.

## Instruções de ssh-keygen

Cada passo é explicado detalhadamente.  Comece por executar `ssh-keygen`.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

O nome do par de chaves para este artigo.  A predefinição é ter um par de chaves com o nome **id_rsa** e algumas ferramentas podem contar com o nome de ficheiro de chave privada **id_rsa**, pelo que é uma boa ideia ter um. (`~/.ssh/` é a localização predefinida típica para todos os pares de chaves SSH e o ficheiro de configuração SSH.)

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
Isto mostra os novos pares de chaves e as respetivas permissões. `ssh-keygen` cria o diretório `~/.ssh`, se não estiver presente, e também define os modos de propriedade e de ficheiro corretos.

Palavra-passe da chave:

`Enter passphrase (empty for no passphrase):`

É vivamente recomendado adicionar uma palavra-passe (`ssh-keygen` chama a isto uma "frase de acesso") para os pares de chaves. Sem uma palavra-passe a proteger o par de chaves, qualquer pessoa com uma cópia do ficheiro de chave privada pode utilizá-la para iniciar sessão em qualquer servidor – os seus servidores – com a chave pública correspondente. Adicionar uma palavra-passe, por conseguinte, oferece uma maior proteção no caso de alguém conseguir obter acesso ao seu ficheiro de chave privada, dando-lhe tempo para alterar as chaves utilizadas para o autenticar.

## Utilizar o ssh-agent para armazenar a palavra-passe da chave privada

Para evitar escrever a palavra-passe do ficheiro de chave privada com cada início de sessão SSH, pode utilizar `ssh-agent` para colocar em cache a palavra-passe do ficheiro de chave privada, permitindo inícios de sessão rápidos e seguros para a VM com Linux.  Se estiver a utilizar o OSX, a Keychain armazenará as palavras-passe das chaves privadas em segurança quando invocar `ssh-agent`.

Primeiro, certifique-se de que `ssh-agent` está em execução

```bash
eval "$(ssh-agent -s)"
```

Agora, adicione a chave privada a `ssh-agent` utilizando o comando `ssh-add`. Voltando ao OSX, este procedimento inicia a Keychain que irá armazenar as credenciais.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

A palavra-passe da chave privada está agora armazenada para que não tenha de escrever a palavra-passe da chave em cada início de sessão SSH.

## Criar e configurar um ficheiro de configuração SSH

Embora não seja absolutamente necessário executá-lo com uma VM com Linux, é uma melhor prática criar e configurar um ficheiro `~/.ssh/config` para o impedir de utilizar acidentalmente palavras-passe para iniciar sessão com as VMs, automatizar utilizando pares de chaves diferentes para diferentes VMs do Azure e configurar outros programas, tais como o **git** para vários servidores de destino.

O exemplo seguinte mostra uma configuração padrão.

### Criar o ficheiro

```bash
touch ~/.ssh/config
```

### Edite o ficheiro para adicionar a nova configuração de SSH:

```bash
vim ~/.ssh/config
```

### Ficheiro `~/.ssh/config` de exemplo:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Esta configuração de SSH dá-lhe secções para cada serviço para que cada um tenha o seu próprio par de chaves dedicado. As predefinições são para quaisquer anfitriões nos quais iniciou sessão que não correspondam a qualquer um dos grupos acima.


### Ficheiro de configuração explicado

`Host` = ao nome do anfitrião que está a ser chamado no terminal.  `ssh fedora22` indica a `SSH` para utilizar os valores no bloco de definições denominado `Host fedora22` NOTA: isto pode ser qualquer etiqueta que seja lógica para a sua utilização e não representa o nome do anfitrião real de qualquer servidor.

`Hostname 102.160.203.241` = o endereço IP ou o nome DNS para o servidor no qual está a iniciar sessão. Isto é utilizado para encaminhar para o servidor e pode ser um IP externo que mapeia para um IP interno.

`User git` = a conta de utilizador remota a utilizar ao iniciar sessão na VM do Azure.

`PubKeyAuthentication yes` = isto indica a SSH que pretende utilizar uma chave SSH para o início de sessão.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = isto indica a SSH que par de chaves pretende apresentar ao servidor para autenticar o início de sessão.


## SSH no Linux sem uma palavra-passe

Agora que tem um par de chaves SSH e um ficheiro de configuração SSH configurado, pode iniciar sessão na VM com Linux rapidamente e em segurança. Na primeira vez que iniciar sessão num servidor utilizando uma chave SSH, o comando pede-lhe a frase de acesso para esse ficheiro de chave.

```bash
ssh fedora22
```

### Comando explicado

Quando `ssh fedora22` é executado, o SSH primeiro localiza e carrega todas as definições do bloco `Host fedora22` e, em seguida, carrega todas as restantes definições a partir do último bloqueio, `Host *`.

## Passos Seguintes

O passo seguinte consiste na criação de VMs com Linux do Azure utilizando a nova chave pública SSH.  As VMs do Azure que são criadas com uma chave pública SSH como o início de sessão estão mais protegidas do que as criadas com as palavras-passe do método de início de sessão predefinido.  As VMs do Azure com chaves SSH para inícios de sessão estão, por predefinição, configuradas para desativar inícios de sessão de palavra-passe, evitando tentativas de invasão à força bruta.

- [Criar uma VM com Linux segura utilizando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM com Linux segura utilizando o Portal do Azure](virtual-machines-linux-quick-create-portal.md)
- [Criar uma VM com Linux segura utilizando a CLI do Azure](virtual-machines-linux-quick-create-cli.md)



<!--HONumber=Jun16_HO2-->


