---
title: Capturar uma VM com Linux do Azure para utilizar como um modelo | Microsoft Docs
description: "Saiba como capturar e generalizar uma imagem de uma baseado em Linux máquina virtual do Azure (VM) criada com o modelo de implementação Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: b1164fbd816eea5189786850f096438e32f8f802
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capturar uma máquina virtual do Linux em execução no Azure
Siga os passos neste artigo para generalizar e capturar a máquina virtual do Linux do Azure (VM) no modelo de implementação Resource Manager. Quando generalize a VM, pode remover as informações de conta pessoal e preparar a VM para ser utilizado como uma imagem. Pode, em seguida, captura de imagem do SO, VHDs anexado dos discos de dados, um disco de rígido virtual (VHD) generalizado e um [modelo do Resource Manager](../../azure-resource-manager/resource-group-overview.md) para novas implementações de VM. Este artigo fornece detalhes sobre como capturar uma imagem de VM com a CLI do Azure 1.0 para uma VM com discos não geridos. Também pode [capturar uma VM com discos gerida do Azure com o 2.0 CLI do Azure](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Discos geridos são processados pela plataforma do Azure e não necessitam de quaisquer de preparação ou localização para armazená-las. Para obter mais informações, veja [Azure Managed Disks overview (Descrição geral dos Managed Disks do Azure)](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Para criar as VMs com a imagem, configurar recursos de rede para cada nova VM e utilizar o modelo (um ficheiro JavaScript Object Notation ou JSON,) para implementá-la a partir de imagens VHD capturadas. Desta forma, pode replicar uma VM com o software configuração atual, forma semelhante ao que utilizar imagens no Azure Marketplace.

> [!TIP]
> Se pretender criar uma cópia da sua VM do Linux existente com o respetivo estado especializado para cópia de segurança ou de depuração, consulte [criar uma cópia de uma máquina virtual de Linux em execução no Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). E se pretende carregar um VHD de Linux a partir de uma VM no local, consulte o artigo [carregar e criar uma VM com Linux a partir da imagem de disco personalizado](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#before-you-begin) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="before-you-begin"></a>Antes de começar
Certifique-se de que cumpre os seguintes pré-requisitos:

* **VM do Azure criado no modelo de implementação Resource Manager** -se de que ainda não criou uma VM com Linux, pode utilizar o [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), a [CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), ou [modelos do Resource Manager ](create-ssh-secured-vm-from-template.md). 
  
    Configure a VM conforme necessário. Por exemplo, [adicionar discos de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), aplicar atualizações e instalar aplicações. 
* **CLI do Azure** -instalar o [CLI do Azure](../../cli-install-nodejs.md) num computador local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Passo 1: Remover o agente Linux do Azure
Primeiro, execute o **waagent** comando com o **deprovision** parâmetro na VM do Linux. Este comando elimina os ficheiros e dados para tomar a VM pronto para generalizar. Para obter mais informações, consulte o [guia de utilizador do agente Linux do Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Ligar à VM com Linux utilizando um cliente de SSH.
2. Na janela do SSH, escreva o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Apenas execute este comando numa VM que pretende capturar como uma imagem. Garante que a imagem é limpa de todas as informações confidenciais ou de que é adequada para a redistribuição.
 
3. Tipo **y** para continuar. Pode adicionar o **-forçar** parâmetro para evitar este passo de confirmação.
4. Depois do comando for concluído, escreva **sair**. Este passo se fechar o cliente SSH.

## <a name="step-2-capture-the-vm"></a>Passo 2: Capturar a VM
Utilize a CLI do Azure para generalizar e capturar a VM. Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem **myResourceGroup**, **myVnet**, e **myVM**.

1. A partir do seu computador local, abra a CLI do Azure e [início de sessão para a sua subscrição do Azure](../../xplat-cli-connect.md). 
2. Certifique-se de que está no modo Resource Manager.
   
    ```azurecli
    azure config mode arm
    ```
3. Encerre a VM que já desaprovisionada usando o seguinte comando:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Generalize a VM com o seguinte comando:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Agora, execute o **captura da vm do azure** comando, que captura a VM. No exemplo seguinte, a imagem VHD que é capturada com os nomes de início com **MyVHDNamePrefix**e o **-t** opção especifica um caminho para o modelo **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > Os ficheiros de imagem do VHD obterem criados por predefinição na mesma conta de armazenamento utilizada da VM original. Utilize o *mesma conta de armazenamento* para armazenar os VHDs de quaisquer novas VMs que criou a partir da imagem. 

6. Para localizar a localização de uma imagem capturada, abra o modelo JSON num editor de texto. No **storageProfile**, localizar o **uri** do **imagem** localizado no **sistema** contentor. Por exemplo, o URI da imagem de disco do SO é semelhante a`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passo 3: Criar uma VM a partir da imagem capturada
Agora utilize a imagem com um modelo para criar uma VM com Linux. Estes passos mostram como utilizar a CLI do Azure e o modelo de ficheiro JSON capturadas para criar a VM numa rede virtual novo.

### <a name="create-network-resources"></a>Criar recursos de rede
Para utilizar o modelo, terá primeiro de configurar uma rede virtual e a NIC para a nova VM. Recomendamos que crie um grupo de recursos para estes recursos na localização onde está armazenada a imagem VM. Execute os comandos semelhantes aos seguintes, substituindo nomes para os recursos e uma localização do Azure adequada ("centralus" nestes comandos):

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Obter o Id de NIC
Para implementar uma VM a partir da imagem utilizando o JSON guardados durante a captura, terá do Id do NIC. Obtê-lo executando o seguinte comando:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

O **Id** no resultado é semelhante a`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Criar uma VM
Agora, execute o seguinte comando para criar a VM a partir da imagem VM capturada. Utilize o **-f** parâmetro para especificar o caminho para o ficheiro de JSON do modelo que guardou.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

No resultado do comando, lhe for pedido para fornecer um novo nome VM, o nome de utilizador de admin e palavra-passe e o Id de NIC que criou anteriormente.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

O exemplo seguinte mostra o que vê para uma implementação com êxito:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Verificar a implementação
Agora SSH para a máquina virtual que criou para verificar a implementação e começar a utilizar a nova VM. Para ligar através de SSH, localize o endereço IP da VM que criou, executando o seguinte comando:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

O endereço IP público está listado no resultado do comando. Por predefinição, ligar para a VM com Linux através do SSH na porta 22.

## <a name="create-additional-vms"></a>Criar VMs adicionais
Utilize a imagem capturada e o modelo para implementar VMs adicionais utilizando os passos na secção anterior. Outras opções para criar as VMs a partir da imagem incluem através de um modelo de início rápido ou a executar o **vm do azure crie** comando.

### <a name="use-the-captured-template"></a>Utilize o modelo capturado
Para utilizar a imagem capturada e o modelo, siga estes passos (detalhados na secção anterior):

* Certifique-se de que a imagem VM está na mesma conta de armazenamento que aloja o VHD à VM.
* Copie o ficheiro de JSON do modelo e especifique um nome exclusivo para o disco de SO a nova VM VHD (ou VHDs). Por exemplo, no **storageProfile**, em **vhd**, na **uri**, especifique um nome exclusivo para o **osDisk** VHD, semelhante a`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Crie um NIC na mesma ou uma rede virtual diferente.
* Utilize o ficheiro de JSON do modelo de modificação para criar uma implementação no grupo de recursos na qual pode configurar a rede virtual.

### <a name="use-a-quickstart-template"></a>Utilize um modelo de início rápido
Se quiser configurar automaticamente quando criar uma VM da imagem de rede, pode especificar esses recursos num modelo. Por exemplo, consulte o [modelo de vm de 101 da imagem utilizador](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) a partir do GitHub. Este modelo cria uma VM na sua imagem personalizada e de rede virtual necessário, endereço IP público e recursos NIC. Para obter instruções sobre utilizando o modelo no portal do Azure, consulte [como criar uma máquina virtual a partir de uma imagem personalizada utilizando um modelo do Resource Manager](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>A vm do azure de utilizar o comando de criação
Normalmente, é mais fácil de utilizar um modelo do Resource Manager para criar uma VM a partir da imagem. No entanto, pode criar a VM *imperatively* utilizando o **vm do azure crie** comando com o **-Q** (**– imagem urn**) parâmetro. Se utilizar este método, tem de passar também o **-d** (**vhd de disco de SO –**) parâmetro para especificar a localização do ficheiro. vhd SO para a nova VM. Este ficheiro tem de ser o contentor de vhds da conta do storage onde está armazenado o ficheiro de imagem do VHD. O comando copia o VHD para a nova VM automaticamente para o **vhds** contentor.

Antes de executar **vm do azure crie** com a imagem, conclua os seguintes passos:

1. Criar um grupo de recursos ou identificar um grupo de recursos existente para a implementação.
2. Crie um recurso NIC e de um recurso de endereço IP público para a nova VM. Para obter os passos criar uma rede virtual, o endereço IP público e a NIC com a CLI do AZURE, consulte anteriormente neste artigo. (**vm do azure crie** também pode criar uma NIC, mas tem de passar os parâmetros adicionais para uma rede virtual e a sub-rede.)

Em seguida, execute um comando que transmite os URIs para o novo ficheiro de VHD de SO e a imagem existente. Neste exemplo, um tamanho de Standard_A1 VM é criado na região EUA Leste.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Para opções de comandos adicionais, execute `azure help vm create`.

## <a name="next-steps"></a>Passos seguintes
Para gerir as suas VMs com a CLI, consulte as tarefas [implementar e gerir máquinas virtuais utilizando modelos Azure Resource Manager e a CLI do Azure](create-ssh-secured-vm-from-template.md).

