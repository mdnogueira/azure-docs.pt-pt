---
title: "Azure Active Directory dos serviços de domínio: Associar uma VM do Linux CoreOS a um domínio gerido | Microsoft Docs"
description: "Associar uma máquina virtual CoreOS Linux nos serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: a75871f0395ceb7d7abd79a0b9d336b400a88542
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Associar uma máquina virtual CoreOS Linux a um domínio gerido
Este artigo mostra como associar uma máquina virtual CoreOS Linux no Azure a um domínio gerido dos serviços de domínio do Azure AD.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:
1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe para o seu domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Aprovisionar uma máquina virtual CoreOS Linux
Aprovisionar uma máquina virtual de CoreOS no Azure, utilizando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Este artigo utiliza o **CoreOS Linux (Stable)** imagem de máquina virtual no Azure.

> [!IMPORTANT]
> * Implementar a máquina virtual para o **mesma rede virtual em que tiver ativado os serviços de domínio do Azure AD**.
> * Escolha um **outra sub-rede** em que tiver ativado os serviços de domínio do Azure AD.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Ligar remotamente à máquina virtual do Linux recentemente aprovisionada
A máquina virtual de CoreOS tiver sido aprovisionada no Azure. A tarefa seguinte consiste em ligar remotamente à máquina virtual utilizando a conta de administrador local criada durante o aprovisionamento da VM.

Siga as instruções no artigo [como iniciar sessão para uma máquina virtual com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o ficheiro de anfitriões da máquina virtual do Linux
No seu terminal SSH, edite o ficheiro /etc/hosts e atualizar o endereço IP da máquina e nome de anfitrião.

```
sudo vi /etc/hosts
```

No ficheiro de anfitriões, introduza o seguinte valor:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido. 'contoso-coreos' é o nome do anfitrião da máquina virtual CoreOS que são a associar ao domínio gerido.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Configurar o serviço SSSD na máquina virtual Linux
Em seguida, atualize o ficheiro de configuração de SSSD no ('/ etc/sssd/sssd.conf') para fazer corresponder o seguinte exemplo:

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
Substitua ' CONTOSO100. COM' com o nome de domínio DNS do seu domínio gerido. Certifique-se de que especificar o nome de domínio no caso de capital no ficheiro conf.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Associar a máquina virtual Linux para o domínio gerido
Agora que os pacotes necessários são instalados na máquina virtual Linux, a próxima tarefa consiste associar a máquina virtual ao domínio gerido.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Resolução de problemas:** se *adcli* não conseguiu encontrar o seu domínio gerido:
  * Certifique-se de que o domínio é acessível a partir da máquina virtual (ping tente).
  * Certifique-se de que a máquina virtual, de facto, implementada da mesma rede virtual em que o domínio gerido está disponível.
  * Verifique se foi atualizado com as definições do servidor DNS para a rede virtual para que apontem para os controladores de domínio do domínio gerido.
>

Inicie o serviço SSSD. No seu terminal SSH, escreva o seguinte comando:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Certifique-se a associação a um domínio
Certifique-se de que o se o computador foi associado com êxito ao domínio gerido. Ligar ao domínio associado VM CoreOS utilizando uma ligação SSH diferentes. Utilize uma conta de utilizador de domínio e, em seguida, verifique se a conta de utilizador é resolvida corretamente.

1. O SSH terminal, escreva o seguinte comando para ligar ao domínio associado CoreOS máquina através de SSH. Utilize uma conta de domínio que pertence ao domínio gerido (por exemplo, 'bob@CONTOSO100.COM' neste caso.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
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
