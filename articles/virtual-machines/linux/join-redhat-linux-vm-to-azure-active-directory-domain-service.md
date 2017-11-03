---
title: Associar um Linux de VM de RedHat VM a um DS do Azure Active Directory | Microsoft Docs
description: "Como associar a VM de RedHat Enterprise Linux 7 VM existente para um serviço de domínio do Active Directory do Azure."
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
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 2e46a0f3c9bdbe267d121b4bf62e25d5d411fcc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Associar um Linux de VM de RedHat VM a um serviço de domínio do Active Directory do Azure

Este artigo mostra como associar uma máquina virtual do Red Hat Enterprise Linux (RHEL) 7 a um domínio gerido do Azure Active Directory domínio dos serviços (AADDS).  Os requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

- [Ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md)

- [DC de serviços de um domínio do Active Directory do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos Rápidos

_Substitua qualquer exemplos as suas próprias definições._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Mudar a cli do azure para o modo de implementação clássica

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Procurar uma versão RHEL e imagem

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Criar uma VM de Redhat de Linux VM

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>SSH para a VM

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>Pacotes de atualização de YUM

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Instalar pacotes necessários

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Agora que os pacotes necessários são instalados na máquina virtual Linux, a próxima tarefa consiste associar a máquina virtual ao domínio gerido.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Detetar o domínio gerido dos serviços de domínio do AAD

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Inicializar kerberos

Certifique-se de que especificou um utilizador que pertença ao grupo de 'AAD DC administradores'. Apenas estes utilizadores podem associar computadores ao domínio gerido.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Associar a máquina ao domínio

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Certifique-se de que o computador está associado ao domínio

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Passos Seguintes

* [Infraestrutura de atualização do Red Hat (RHUI) para a pedido Red Hat Enterprise Linux VMs no Azure](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurar o Cofre de chaves para máquinas virtuais no Gestor de recursos do Azure](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implementar e gerir máquinas virtuais utilizando modelos Azure Resource Manager e a CLI do Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
