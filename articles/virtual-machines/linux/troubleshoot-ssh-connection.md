---
title: "Resolver problemas de ligação SSH para uma VM do Azure | Microsoft Docs"
description: "Como resolver problemas, tais como 'Falhada de ligação SSH' ou 'Recusada a ligação de SSH' para uma VM do Azure com o Linux."
keywords: "SSH ligação recusada, ssh erro, azure ssh, ligação SSH falhou"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 3a282c8b2c2ba2749de6a2d3688bd57d75703b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Resolver problemas de uma VM com Linux do Azure que falhe, erros de saída, ou é recusada ligações SSH
Existem várias razões se encontrar erros de Secure Shell (SSH), falhas de ligação de SSH, ou SSH é recusou-se ao tentar ligar a uma máquina virtual (VM) do Linux. Este artigo ajuda-o a localizar e corrigir os problemas. Pode utilizar o portal do Azure, CLI do Azure ou a extensão de acesso de VM para Linux para resolver problemas de ligação.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](http://azure.microsoft.com/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](http://azure.microsoft.com/support/options/) e selecione **obter suporte**. Para informações sobre como utilizar o suporte do Azure, leia o [suporte do Microsoft Azure FAQ](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Depois de cada passo de resolução de problemas, tente restabelecer a ligação à VM.

1. Reposição da configuração SSH.
2. Repor as credenciais do utilizador.
3. Certifique-se a [grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md) regras permitem tráfego SSH.
   * Certifique-se de que existe uma regra de grupo de segurança de rede para permitir tráfego SSH (por predefinição, a porta TCP 22).
   * Não é possível utilizar o redirecionamento de porta / mapeamento sem utilizar um balanceador de carga do Azure.
4. Verifique o [estado de funcionamento de recursos VM](../../resource-health/resource-health-overview.md). 
   * Certifique-se de que a VM relatórios como estando em bom estado.
   * Se tiver de diagnóstico de arranque ativado, certifique-se de que a VM não está a comunicar erros de arranque nos registos.
5. Reinicie a VM.
6. Volte a implementar a VM.

Continue a ler para explicações e passos de resolução de problemas mais detalhados.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para resolver problemas de ligação de SSH
Pode repor as credenciais ou configuração de SSH utilizando um dos seguintes métodos:

* [Portal do Azure](#use-the-azure-portal) - excelente se precisar de repor rapidamente a configuração de SSH ou uma chave SSH e não tem as ferramentas do Azure instaladas.
* [Azure CLI 2.0](#use-the-azure-cli-20) - se de que já está na linha de comandos, rapidamente repor a configuração SSH ou credenciais. Também pode utilizar o [CLI do Azure 1.0](#use-the-azure-cli-10)
* [Azure extensão VMAccessForLinux](#use-the-vmaccess-extension) - criar e reutilizar os ficheiros de definição de json para repor as credenciais de utilizador ou de configuração de SSH.

Depois de cada passo de resolução de problemas, tente ligar novamente à VM. Se ainda não é possível estabelecer ligação, repita o passo seguinte.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
O portal do Azure fornece uma forma rápida de repor as credenciais de utilizador ou de configuração de SSH sem instalar quaisquer ferramentas no seu computador local.

Selecione a VM no portal do Azure. Desloque para baixo até o **suporte + resolução de problemas** secção e selecione **Repor palavra-passe** como no exemplo seguinte:

![Repor configuração SSH ou as credenciais no portal do Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Reposição da configuração SSH
Como primeiro passo, selecione `Reset configuration only` do **modo** menu pendente de captura de ecrã anterior, em seguida, clique o **repor** botão. Quando esta ação tiver sido concluída, tente aceder novamente à sua VM.

### <a name="reset-ssh-credentials-for-a-user"></a>Repor as credenciais de SSH para um utilizador
Para repor as credenciais de um utilizador existente, selecione `Reset SSH public key` ou `Reset password` do **modo** menu pendente, como a captura de ecrã anterior. Especifique o nome de utilizador e uma chave SSH ou nova palavra-passe, em seguida, clique em de **repor** botão.

Também pode criar um utilizador com privilégios sudo na VM este menu. Introduza um novo nome de utilizador e palavra-passe associada ou chave SSH e, em seguida, clique em de **repor** botão.

## <a name="use-the-azure-cli-20"></a>Utilizar a CLI do Azure 2.0
Se ainda não o fez, instale a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) e início de sessão para um Azure conta através de [início de sessão az](/cli/azure/#login).

Se criar e carregar uma imagem de disco do Linux personalizada, certifique-se a [agente Linux do Microsoft Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) versão 2.0.5 ou posterior está instalado. Para VMs criadas com imagens Gallery, esta extensão de acesso já está instalado e configurado por si.

### <a name="reset-ssh-configuration"></a>Repor configuração SSH
Pode inicialmente tente repor a configuração SSH para os valores predefinidos e reinício do servidor SSH na VM. Tenha em atenção que isto não alterar o nome de conta de utilizador, palavra-passe ou chaves SSH.
O exemplo seguinte utiliza [az vm utilizador reposição-ssh](/cli/azure/vm/user#reset-ssh) para repor a configuração de SSH no VM com o nome `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Repor as credenciais de SSH para um utilizador
O exemplo seguinte utiliza [atualização de utilizador de vm az](/cli/azure/vm/user#update) para repor as credenciais para `myUsername` para o valor especificado no `myPassword`, na VM com o nome `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se utilizar a autenticação de chave SSH, pode repor a chave SSH para um determinado utilizador. O exemplo seguinte utiliza **utilizador do linux do conjunto de acesso de az vm** atualizar a chave SSH armazenada no `~/.ssh/id_rsa.pub` para o utilizador com o nome `myUsername`, na VM com o nome `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Utilizar a extensão VMAccess
A extensão de acesso de VM para Linux lê num ficheiro json que define as ações a realizar. Estas ações incluem repor SSHD, repor uma chave SSH ou adicionar um utilizador. Pode continuar a utilizar a CLI do Azure para chamar a extensão VMAccess, mas pode reutilizar os ficheiros json em várias VMs se assim o desejar. Esta abordagem permite-lhe criar um repositório de ficheiros json que pode ser chamado para fornecido cenários.

### <a name="reset-sshd"></a>Repor SSHD
Crie um ficheiro denominado `settings.json` com o seguinte conteúdo:

```json
{  
    "reset_ssh":"True"
}
```

Utilizar a CLI do Azure, em seguida, chama o `VMAccessForLinux` extensão para repor a sua ligação de SSHD especificando o ficheiro json. O exemplo seguinte utiliza [conjunto de extensão de vm az](/cli/azure/vm/extension#set) repor SSHD na VM com o nome `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Repor as credenciais de SSH para um utilizador
Se for apresentada SSHD funcione corretamente, pode repor as credenciais para um utilizador giver. Para repor a palavra-passe para um utilizador, crie um ficheiro denominado `settings.json`. O exemplo seguinte repõe as credenciais para `myUsername` para o valor especificado no `myPassword`. Introduza as seguintes linhas no seu `settings.json` ficheiro, utilizando os seus próprios valores:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou para repor a chave SSH para um utilizador, primeiro crie um ficheiro denominado `settings.json`. O exemplo seguinte repõe as credenciais para `myUsername` para o valor especificado no `myPassword`, na VM com o nome `myVM` no `myResourceGroup`. Introduza as seguintes linhas no seu `settings.json` ficheiro, utilizando os seus próprios valores:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Depois de criar o ficheiro json, utilizar a CLI do Azure para chamar o `VMAccessForLinux` extensão para repor as credenciais de utilizador do SSH, especificando o ficheiro json. O exemplo seguinte repõe as credenciais no VM com o nome `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Utilizar a CLI do Azure 1.0
Se ainda não o fez, [instalar a CLI do Azure 1.0 e ligar à sua subscrição do Azure](../../cli-install-nodejs.md). Certifique-se de que estiver a utilizar o modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Se criar e carregar uma imagem de disco do Linux personalizada, certifique-se a [agente Linux do Microsoft Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) versão 2.0.5 ou posterior está instalado. Para VMs criadas com imagens Gallery, esta extensão de acesso já está instalado e configurado por si.

### <a name="reset-ssh-configuration"></a>Repor configuração SSH
A configuração de SSHD próprio pode estar configurado incorretamente ou o serviço encontrou um erro. Só pode repor SSHD para se certificar de que a configuração de SSH em si é válida. Repor SSHD deve ser o primeiro passo de resolução de problemas que tomar.

O exemplo seguinte repõe SSHD numa VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`. Utilize os seus próprios nomes de grupo de recursos e de VM da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Repor as credenciais de SSH para um utilizador
Se for apresentada SSHD funcione corretamente, pode repor a palavra-passe para um utilizador giver. O exemplo seguinte repõe as credenciais para `myUsername` para o valor especificado no `myPassword`, na VM com o nome `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Se utilizar a autenticação de chave SSH, pode repor a chave SSH para um determinado utilizador. O exemplo seguinte atualiza a chave SSH armazenada no `~/.ssh/id_rsa.pub` para o utilizador com o nome `myUsername`, na VM com o nome `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Reiniciar uma VM
Se tiver de repor as credenciais de utilizador e a configuração de SSH ou encontrou um erro na fazê-lo, pode tentar reiniciar a VM para o endereço subjacente problemas de computação.

### <a name="azure-portal"></a>Portal do Azure
Para reiniciar uma VM com o portal do Azure, selecione a VM e clique em de **reiniciar** botão como no exemplo seguinte:

![Reiniciar uma VM no portal do Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>CLI do Azure 1.0
O exemplo seguinte reinicia a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
O exemplo seguinte utiliza [reinício de vm az](/cli/azure/vm#restart) para reiniciar a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Reimplementar uma VM
Pode voltar a implementar uma VM para outro nó no Azure, o que poderá corrigir problemas de rede subjacentes. Para obter informações sobre voltar a implementar uma VM, consulte [Reimplementar a máquina virtual para o novo nó de Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Depois de concluída esta operação, dados do disco efémeras serão perdidos e serão atualizados em endereços IP dinâmicos que estão associados a máquina virtual.
> 
> 

### <a name="azure-portal"></a>Portal do Azure
Para voltar a implementar uma VM com o portal do Azure, selecione a VM e desloque para baixo até o **suporte + resolução de problemas** secção. Clique em de **Reimplementar** botão como no exemplo seguinte:

![Volte a implementar uma VM no portal do Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>CLI do Azure 1.0
O exemplo seguinte redeploys VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
A utilização de exemplo seguintes [volte a implementar az vm](/cli/azure/vm#redeploy) ao Reimplementar a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criadas utilizando o modelo de implementação clássica
Repita estes passos para resolver falhas de ligação de SSH mais comuns para as VMs que foram criadas utilizando o modelo de implementação clássica. Depois de cada passo, tente restabelecer a ligação à VM.

* Reponha o acesso remoto do [portal do Azure](https://portal.azure.com). No portal do Azure, selecione a VM e clique em de **repor remoto...**  botão.
* Reinicie a VM. No [portal do Azure](https://portal.azure.com), selecione a VM e clique o **reiniciar** botão.
    
* Volte a implementar a VM para um novo nó de Azure. Para obter informações sobre como voltar a implementar uma VM, consulte [Reimplementar a máquina virtual para o novo nó de Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Depois de concluída esta operação, dados do disco efémeras serão perdidos e serão atualizados em endereços IP dinâmicos que estão associados a máquina virtual.
* Siga as instruções em [como repor uma palavra-passe ou SSH para máquinas virtuais baseadas em Linux](classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para:
  
  * Repor a palavra-passe ou chave SSH.
  * Criar um *sudo* conta de utilizador.
  * Reposição da configuração SSH.
* Verifique o estado de funcionamento de recursos da VM para quaisquer problemas de plataforma.<br>
     Selecione a VM e desloque para baixo **definições** > **verificar o estado de funcionamento**.

## <a name="additional-resources"></a>Recursos adicionais
* Se ainda não é possível SSH para a VM depois de seguir os passos após, consulte o artigo [mais passos de resolução de problemas](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para rever os passos adicionais para resolver o problema.
* Para obter mais informações sobre resolução de problemas de acesso de aplicação, consulte [resolver problemas de acesso a uma aplicação em execução numa máquina virtual do Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Para obter mais informações sobre resolução de problemas de máquinas virtuais que foram criadas utilizando o modelo de implementação clássica, consulte [como repor uma palavra-passe ou SSH para máquinas virtuais baseadas em Linux](classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

