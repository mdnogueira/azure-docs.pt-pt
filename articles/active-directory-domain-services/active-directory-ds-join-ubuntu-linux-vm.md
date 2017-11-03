---
title: "Azure Active Directory dos serviços de domínio: Associar uma VM com Ubuntu a um domínio gerido | Microsoft Docs"
description: "Associar uma máquina virtual do Ubuntu Linux nos serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: maheshu
ms.openlocfilehash: 8946166c04ce778d751ad79f7a010c9a5e71a05c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Associar uma máquina virtual do Ubuntu no Azure a um domínio gerido
Este artigo mostra como associar uma máquina virtual do Ubuntu Linux a um domínio gerido dos serviços de domínio do Azure AD.


## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:  
1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe para o seu domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Aprovisionar uma máquina virtual do Ubuntu Linux
Aprovisionar uma máquina virtual de Ubuntu Linux no Azure, utilizando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implementar a máquina virtual para o **mesma rede virtual em que tiver ativado os serviços de domínio do Azure AD**.
> * Escolha um **outra sub-rede** em que tiver ativado os serviços de domínio do Azure AD.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Ligar remotamente à máquina virtual Ubuntu Linux
A máquina virtual do Ubuntu tiver sido aprovisionada no Azure. A tarefa seguinte consiste em ligar remotamente à máquina virtual utilizando a conta de administrador local criada durante o aprovisionamento da VM.

Siga as instruções no artigo [como iniciar sessão para uma máquina virtual com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o ficheiro de anfitriões da máquina virtual do Linux
No seu terminal SSH, edite o ficheiro /etc/hosts e atualizar o endereço IP da máquina e nome de anfitrião.

```
sudo vi /etc/hosts
```

No ficheiro de anfitriões, introduza o seguinte valor:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido. 'contoso-ubuntu' é o nome do anfitrião da máquina virtual Ubuntu que são a associar ao domínio gerido.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar pacotes necessários na máquina virtual Linux
Em seguida, instale pacotes necessários para a associação a um domínio na máquina virtual. Execute os seguintes passos:

1.  No seu terminal SSH, escreva o seguinte comando para transferir as listas de pacote de repositórios do. Este comando atualiza as listas de pacote para obter informações sobre as versões mais recentes dos pacotes e as respetivas dependências.

    ```
    sudo apt-get update
    ```

2. Escreva o seguinte comando para instalar os pacotes necessários.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Durante a instalação de Kerberos, verá um ecrã pink. A instalação do pacote 'krb5 user' pede-lhe o nome do realm (em todas as letras maiúsculas). A instalação escreve [realm] e [domain_realm] secções /etc/krb5.conf.

    > [!TIP]
    > Se o nome do seu domínio gerido contoso100.com, introduza CONTOSO100.COM como o realm. Lembre-se de que o nome do realm tem de ser especificado no UPPERCASE.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Configurar as definições do NTP (protocolo de tempo da rede) da máquina virtual do Linux
A data e hora da VM com Ubuntu tem de sincronizar com o domínio gerido. Adicione o nome de anfitrião do seu domínio gerido NTP no ficheiro /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

No ficheiro ntp.conf, introduza o seguinte valor e guarde o ficheiro:

```
server contoso100.com
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido.

Sincronizar agora a VM com Ubuntu data e hora com o servidor NTP e, em seguida, inicie o serviço NTP:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Associar a máquina virtual Linux para o domínio gerido
Agora que os pacotes necessários são instalados na máquina virtual Linux, a próxima tarefa consiste associar a máquina virtual ao domínio gerido.

1. Detete o domínio gerido dos serviços de domínio do AAD. No seu terminal SSH, escreva o seguinte comando:

    ```
    sudo realm discover CONTOSO100.COM
    ```

      > [!NOTE] 
      > **Resolução de problemas:** se *realm detetar* não conseguiu encontrar o seu domínio gerido:
        * Ensure that the domain is reachable from the virtual machine (try ping).
        * Check that the virtual machine has indeed been deployed to the same virtual network in which the managed domain is available.
        * Check to see if you have updated the DNS server settings for the virtual network to point to the domain controllers of the managed domain.
      >

2. Inicializar o Kerberos. No seu terminal SSH, escreva o seguinte comando: 

    > [!TIP] 
    > * Certifique-se de que especificou um utilizador que pertença ao grupo de 'AAD DC administradores'. 
    > * Especifique o nome de domínio em maiúsculas, kinit pessoa falha.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Associe a máquina ao domínio. No seu terminal SSH, escreva o seguinte comando: 

    > [!TIP] 
    > Utilize a mesma conta de utilizador que especificou no passo anterior ('kinit').
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Deverá receber uma mensagem de ("inscrito com êxito máquina no realm") quando a máquina está associada com êxito ao domínio gerido.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Atualize a configuração de SSSD e reinicie o serviço
1. No seu terminal SSH, escreva o seguinte comando. Abra o ficheiro de sssd.conf e efetuar a alteração seguinte
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Comente a linha **use_fully_qualified_names = True** e guarde o ficheiro.
    ```
    # use_fully_qualified_names = True
    ```

3. Reinicie o serviço SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Configurar a criação automática de diretório raiz
Para permitir a criação automática do diretório raiz após iniciar sessão de utilizadores, escreva os seguintes comandos no seu terminal PuTTY:
```
sudo vi /etc/pam.d/common-session
```
    
Adicione a seguinte linha neste ficheiro abaixo da linha 'sessão opcional pam_sss.so' e guarde-o:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Certifique-se a associação a um domínio
Certifique-se de que o se o computador foi associado com êxito ao domínio gerido. Ligar ao domínio associado a VM com Ubuntu utilizando uma ligação SSH diferentes. Utilize uma conta de utilizador de domínio e, em seguida, verifique se a conta de utilizador é resolvida corretamente.

1. No seu terminal SSH, escreva o seguinte comando para ligar ao domínio associado a máquina virtual de Ubuntu utilizando SSH. Utilize uma conta de domínio que pertence ao domínio gerido (por exemplo, 'bob@CONTOSO100.COM' neste caso.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. No seu terminal SSH, escreva o seguinte comando para ver se o diretório de raiz foi inicializado correctamente.
    ```
    pwd
    ```

3. No seu terminal SSH, escreva o seguinte comando para ver se a membros do grupo estão a ser resolvidos corretamente.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder privilégios 'AAD DC administradores' grupo sudo
Pode conceder os privilégios administrativos 'AAD DC administradores' grupo na VM com Ubuntu membros. O ficheiro de sudo está localizado em /etc/sudoers. Os membros de grupos do AD adicionados no sudoers podem efetuar o sudo.

1. No seu terminal SSH, certifique-se de que tem sessão iniciada com privilégios de Superutilizador. Pode utilizar a conta de administrador local que especificou ao criar a VM. Execute o seguinte comando:
    ```
    sudo vi /etc/sudoers
    ```

2. Adicione a seguinte entrada para o ficheiro /etc/sudoers e guardá-lo:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Pode agora iniciar sessão como membro do grupo 'AAD DC administradores' e deve ter privilégios administrativos na VM.


## <a name="troubleshooting-domain-join"></a>Resolução de problemas de associação a um domínio
Consulte o [associação de domínio de resolução de problemas](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshooting-domain-join) artigo.


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como iniciar sessão para uma máquina virtual com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
