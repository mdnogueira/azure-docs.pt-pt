---
title: "Azure Active Directory dos serviços de domínio: Associar um VM CentOS a um domínio gerido | Microsoft Docs"
description: "Associar uma máquina virtual CentOS Linux nos serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 0316d40abc6cf71d5f9218346fa6543c9167eaa4
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Associar uma máquina virtual CentOS Linux a um domínio gerido
Este artigo mostra como associar uma máquina virtual CentOS Linux no Azure a um domínio gerido dos serviços de domínio do Azure AD.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:
1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe para o seu domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Aprovisionar uma máquina virtual CentOS Linux
Aprovisionar uma máquina virtual do CentOS no Azure, utilizando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implementar a máquina virtual para o **mesma rede virtual em que tiver ativado os serviços de domínio do Azure AD**.
> * Escolha um **outra sub-rede** em que tiver ativado os serviços de domínio do Azure AD.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Ligar remotamente à máquina virtual do Linux recentemente aprovisionada
A máquina virtual do CentOS tiver sido aprovisionada no Azure. A tarefa seguinte consiste em ligar remotamente à máquina virtual utilizando a conta de administrador local criada durante o aprovisionamento da VM.

Siga as instruções no artigo [como iniciar sessão para uma máquina virtual com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o ficheiro de anfitriões da máquina virtual do Linux
No seu terminal SSH, edite o ficheiro /etc/hosts e atualizar o endereço IP da máquina e nome de anfitrião.

```
sudo vi /etc/hosts
```

No ficheiro de anfitriões, introduza o seguinte valor:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido. 'contoso-centos' é o nome do anfitrião da máquina virtual CentOS que são a associar ao domínio gerido.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar pacotes necessários na máquina virtual Linux
Em seguida, instale pacotes necessários para a associação a um domínio na máquina virtual. No seu terminal SSH, escreva o seguinte comando para instalar os pacotes necessários:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
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
    > * Especifique um utilizador que pertença ao grupo de 'AAD DC administradores'.
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Deverá receber uma mensagem de ("inscrito com êxito máquina no realm") quando a máquina está associada com êxito ao domínio gerido.


## <a name="verify-domain-join"></a>Certifique-se a associação a um domínio
Certifique-se de que o se o computador foi associado com êxito ao domínio gerido. Ligar para os CentOS associado a um domínio VM a utilizar uma ligação SSH diferentes. Utilize uma conta de utilizador de domínio e, em seguida, verifique se a conta de utilizador é resolvida corretamente.

1. O SSH terminal, escreva o seguinte comando para ligar ao domínio associado CentOS máquina através de SSH. Utilize uma conta de domínio que pertence ao domínio gerido (por exemplo, 'bob@CONTOSO100.COM' neste caso.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. No seu terminal SSH, escreva o seguinte comando para ver se o diretório de raiz foi inicializado correctamente.
    ```
    pwd
    ```

3. No seu terminal SSH, escreva o seguinte comando para ver se a membros do grupo estão a ser resolvidos corretamente.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Resolução de problemas de associação a um domínio
Consulte o [associação de domínio de resolução de problemas](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshooting-domain-join) artigo.

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como iniciar sessão para uma máquina virtual com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Instalar Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - guia de integração do Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
