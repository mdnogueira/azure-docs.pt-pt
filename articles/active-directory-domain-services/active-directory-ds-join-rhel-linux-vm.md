---
title: "Azure Active Directory dos serviços de domínio: Associar um VM RHEL a um domínio gerido | Microsoft Docs"
description: "Associar uma máquina virtual do Red Hat Enterprise Linux nos serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: maheshu
ms.openlocfilehash: 20cecf0b3e38e8f2241f3589b9548c93730c7783
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Associar uma máquina virtual do Red Hat Enterprise Linux 7 a um domínio gerido
Este artigo mostra como associar uma máquina virtual do Red Hat Enterprise Linux (RHEL) 7 a um domínio gerido dos serviços de domínio do Azure AD.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:  
1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe para o seu domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Aprovisionar uma máquina virtual do Red Hat Enterprise Linux
Aprovisionar uma máquina virtual de RHEL 7 no Azure, utilizando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implementar a máquina virtual para o **mesma rede virtual em que tiver ativado os serviços de domínio do Azure AD**.
> * Escolha um **outra sub-rede** em que tiver ativado os serviços de domínio do Azure AD.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Ligar remotamente à máquina virtual do Linux recentemente aprovisionada
A máquina virtual do RHEL 7.2 tiver sido aprovisionada no Azure. A tarefa seguinte consiste em ligar remotamente à máquina virtual utilizando a conta de administrador local criada durante o aprovisionamento da VM.

Siga as instruções no artigo [como iniciar sessão para uma máquina virtual com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o ficheiro de anfitriões da máquina virtual do Linux
No seu terminal SSH, edite o ficheiro /etc/hosts e atualizar o endereço IP da máquina e nome de anfitrião.

```
sudo vi /etc/hosts
```

No ficheiro de anfitriões, introduza o seguinte valor:

```
127.0.0.1 contoso-rhel.contoso100.com contoso-rhel
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido. 'contoso-rhel' é o nome do anfitrião da máquina virtual RHEL que são a associar ao domínio gerido.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar pacotes necessários na máquina virtual Linux
Em seguida, instale pacotes necessários para a associação a um domínio na máquina virtual. No seu terminal SSH, escreva o seguinte comando para instalar os pacotes necessários:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Associar a máquina virtual Linux para o domínio gerido
Agora que os pacotes necessários são instalados na máquina virtual Linux, a próxima tarefa consiste associar a máquina virtual ao domínio gerido.

1. Detete o domínio gerido dos serviços de domínio do AAD. No seu terminal SSH, escreva o seguinte comando:

    ```
    sudo realm discover CONTOSO100.COM
    ```

     > [!NOTE] 
     > **Resolução de problemas:** se *realm detetar* não conseguiu encontrar o seu domínio gerido:
     * Certifique-se de que o domínio é acessível a partir da máquina virtual (ping tente).
     * Certifique-se de que a máquina virtual, de facto, implementada da mesma rede virtual em que o domínio gerido está disponível.
     * Verifique se foi atualizado com as definições do servidor DNS para a rede virtual para que apontem para os controladores de domínio do domínio gerido.
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Deverá receber uma mensagem de ("inscrito com êxito máquina no realm") quando a máquina está associada com êxito ao domínio gerido.


## <a name="verify-domain-join"></a>Certifique-se a associação a um domínio
Certifique-se de que o se o computador foi associado com êxito ao domínio gerido. Ligar ao domínio associado VM RHEL utilizando uma ligação SSH diferentes. Utilize uma conta de utilizador de domínio e, em seguida, verifique se a conta de utilizador é resolvida corretamente.

1. O SSH terminal, escreva o seguinte comando para ligar ao domínio associado RHEL máquina através de SSH. Utilize uma conta de domínio que pertence ao domínio gerido (por exemplo, 'bob@CONTOSO100.COM' neste caso.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-rhel.contoso100.com
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
