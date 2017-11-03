---
title: "Associar um VM do Windows Server para os serviços de domínio do Active Directory do Azure | Microsoft Docs"
description: "Associe uma máquina virtual do Windows a um domínio gerido utilizando os modelos Azure Resource Manager."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 8cfa13ee028b5e367158de42ceab0a1cd99d45c2
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Associar uma máquina virtual do Windows a um domínio gerido utilizando um modelo do Resource Manager
Este artigo mostra como associar uma máquina virtual do Windows a um domínio gerido de serviços de domínio do Azure AD com modelos do Resource Manager.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:
1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe para o seu domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Instalar e configurar ferramentas necessárias
Pode utilizar qualquer uma das seguintes opções para efetuar os passos descritos neste documento:
* **O Azure PowerShell**: [instalar e configurar](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Interface de linha de comandos de plataforma do Azure**: [instalar e configurar](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opção 1: Aprovisionar uma nova VM do Windows Server e associar a um domínio gerido
**Nome do modelo de início rápido**: [201-vm-associação de domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Para implementar uma máquina virtual do Windows Server e associar a um domínio gerido, execute os seguintes passos:
1. Navegue para o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Clique em **Implementar no Azure**.
3. No **implementação personalizada** , indique as informações necessárias para aprovisionar a máquina virtual.
4. Selecione o **subscrição do Azure** no qual pretende aprovisionar a máquina virtual. Escolha a mesma subscrição do Azure em que tiver ativado os serviços de domínio do Azure AD.
5. Selecione um existente **grupo de recursos** ou crie um novo.
6. Escolha um **localização** no qual pretende implementar a nova máquina virtual.
7. No **nome da VNET existente**, especifique a rede virtual no qual implementou o seu domínio gerido dos serviços de domínio do Azure AD.
8. No **nome de sub-rede existente**, especifique a sub-rede na rede virtual onde pretende implementar esta máquina virtual. Não selecione a sub-rede do gateway da rede virtual. Além disso, não selecione a sub-rede dedicada em que o seu domínio gerido é implementado.
9. No **prefixo de etiqueta de DNS**, especifique o nome do anfitrião da máquina virtual que está a ser aprovisionado. Por exemplo, "contoso-win'.
10. Selecione as adequadas **tamanho da VM** para a máquina virtual.
11. No **para associar ao domínio**, especifique o nome de domínio DNS do seu domínio gerido.
12. No **nome de utilizador do domínio**, especifique o nome de conta de utilizador no seu domínio gerido que deve ser utilizado para associar a VM para o domínio gerido.
13. No **palavra-passe do domínio**, especifique a palavra-passe da conta de utilizador de domínio referida pelo parâmetro 'domainUsername'.
14. Opcional: Pode especificar um **UO caminho** a uma UO personalizada, na qual pretende adicionar a máquina virtual. Se não especificar um valor para este parâmetro, a máquina virtual é adicionada à predefinição **AAD DC computadores** UO no domínio gerido.
15. No **nome de utilizador de administrador de VM** campo, especifique um nome de conta de administrador local para a máquina virtual.
16. No **palavra-passe de administrador de VM** campo, especifique uma palavra-passe de administrador local para a máquina virtual. Forneça uma palavra-passe de administrador local segura para a máquina virtual proteger contra ataques de força bruta de palavra-passe.
17. Clique em **concordo com os termos e condições indicadas acima**.
18. Clique em **Compra** para aprovisionar a máquina virtual.

> [!WARNING]
> **Processe as palavras-passe com cuidado.**
> O ficheiro de parâmetros de modelo contém palavras-passe para contas de domínio, bem como as palavras-passe de administrador local para a máquina virtual. Certifique-se de que não deixe este ficheiro lying em torno em partilhas de ficheiros ou outras partilhadas localizações. Recomenda-se eliminar este ficheiro quando tiver terminado a implementar as máquinas virtuais.
>

Após a implementação for concluída com êxito, a máquina virtual do Windows recentemente aprovisionada está associada ao domínio gerido.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opção 2: Associar um VM do Windows Server existente para um domínio gerido
**Modelo de início rápido**: [201 vm-domínio-associação-existente](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Para associar uma máquina virtual do Windows Server a um domínio gerido, execute os seguintes passos:
1. Navegue para o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Clique em **Implementar no Azure**.
3. No **implementação personalizada** , indique as informações necessárias para aprovisionar a máquina virtual.
4. Selecione o **subscrição do Azure** no qual pretende aprovisionar a máquina virtual. Escolha a mesma subscrição do Azure em que tiver ativado os serviços de domínio do Azure AD.
5. Selecione um existente **grupo de recursos** ou crie um novo.
6. Escolha um **localização** no qual pretende implementar a nova máquina virtual.
7. No **VM lista** campo, especifique os nomes das máquinas virtuais existentes sejam associados ao domínio gerido. Utilize uma vírgula para separar os nomes VM individuais. Por exemplo, **web da contoso, contoso-api**.
8. No **nome de utilizador de adesão ao domínio**, especifique o nome de conta de utilizador no seu domínio gerido que deve ser utilizado para associar a VM para o domínio gerido.
9. No **domínio associar utilizadores a palavra-passe**, especifique a palavra-passe da conta de utilizador de domínio referida pelo parâmetro 'domainUsername'.
10. No **FQDN do domínio**, especifique o nome de domínio DNS do seu domínio gerido.
11. Opcional: Pode especificar um **UO caminho** a uma UO personalizada, na qual pretende adicionar a máquina virtual. Se não especificar um valor para este parâmetro, a máquina virtual é adicionada à predefinição **AAD DC computadores** UO no domínio gerido.
12. Clique em **concordo com os termos e condições indicadas acima**.
13. Clique em **Compra** para aprovisionar a máquina virtual.

> [!WARNING]
> **Processe as palavras-passe com cuidado.**
> O ficheiro de parâmetros de modelo contém palavras-passe para contas de domínio, bem como as palavras-passe de administrador local para a máquina virtual. Certifique-se de que não deixe este ficheiro lying em torno em partilhas de ficheiros ou outras partilhadas localizações. Recomenda-se eliminar este ficheiro quando tiver terminado a implementar as máquinas virtuais.
>

Após a implementação for concluída com êxito, as máquinas de virtuais do Windows especificadas estão associadas ao domínio gerido.


## <a name="related-content"></a>Conteúdo relacionado
* [Descrição geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Modelo de início rápido do Azure - domínio associar uma nova VM](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Modelo de início rápido do Azure - VMs existentes associar ao domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
