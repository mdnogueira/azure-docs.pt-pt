---
title: Capturar uma imagem de uma VM com Linux no Azure utilizando o CLI 2.0 | Microsoft Docs
description: "Capture uma imagem de VM do Azure a utilizar para implementações em massa com o 2.0 CLI do Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 19b573f77f2ee84600955d00d30bdb16c84e3623
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Como criar uma imagem de uma máquina virtual ou o VHD

<!-- generalize, image - extended version of the tutorial-->

Para criar várias cópias de uma máquina virtual (VM) a utilizar no Azure, capture uma imagem de VM ou o VHD de SO. Para criar uma imagem, tem de remover as informações de conta pessoal que torna mais segura implementar várias vezes. Nos passos seguintes desaprovisionar uma VM existente, Desalocação e criar uma imagem. Pode utilizar esta imagem para criar VMs em qualquer grupo de recursos na sua subscrição.

Se pretender criar uma cópia da sua VM do Linux existente para cópia de segurança ou de depuração, ou carregar um VHD de Linux especializado de uma VM no local, consulte [carregar e criar uma VM com Linux a partir da imagem de disco personalizado](upload-vhd.md).  

Também pode utilizar **Packer** para criar a configuração personalizada. Para obter mais informações sobre como utilizar Packer, consulte [como utilizar Packer para criar imagens da máquina virtual com Linux no Azure](build-image-with-packer.md).


## <a name="before-you-begin"></a>Antes de começar
Certifique-se de que cumpre os seguintes pré-requisitos:

* Precisa de uma VM do Azure criado no modelo de implementação Resource Manager com discos geridos. Se ainda não criou uma VM com Linux, pode utilizar o [portal](quick-create-portal.md), a [CLI do Azure](quick-create-cli.md), ou [modelos do Resource Manager](create-ssh-secured-vm-from-template.md). Configure a VM conforme necessário. Por exemplo, [adicionar discos de dados](add-disk.md), aplicar atualizações e instalar aplicações. 

* Terá também de ter a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e de ter sessão iniciada para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

## <a name="quick-commands"></a>Comandos rápidos

Para uma versão simplificada neste tópico, para fins de teste, avaliar ou a saber mais sobre as VMs do Azure, consulte o artigo [criar uma imagem personalizada da VM do Azure utilizando a CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Passo 1: Retirar o aprovisionamento de VM
Desaprovisionar a VM, com o agente da VM do Azure, para eliminar dados e ficheiros específicos do computador. Utilize o `waagent` comando com o *-deprovision + utilizador* parâmetro na sua origem de VM com Linux. Para obter mais informações, consulte o [guia de utilizador do agente Linux do Azure](../windows/agent-user-guide.md).

1. Ligar à VM com Linux utilizando um cliente de SSH.
2. Na janela do SSH, escreva o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Apenas execute este comando numa VM que pretende capturar como uma imagem. Garante que a imagem é limpa de todas as informações confidenciais ou de que é adequada para a redistribuição. O *+ utilizador* parâmetro também remove a última conta de utilizador aprovisionado. Se pretender manter as credenciais da conta na VM, utilize apenas *-deprovision* para deixar a conta de utilizador no local.
 
3. Tipo **y** para continuar. Pode adicionar o **-forçar** parâmetro para evitar este passo de confirmação.
4. Depois do comando for concluído, escreva **sair**. Este passo se fechar o cliente SSH.

## <a name="step-2-create-vm-image"></a>Passo 2: Criar a imagem de VM
Utilize o 2.0 CLI do Azure para marcar a VM como generalizado e capturar a imagem. Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem *myResourceGroup*, *myVnet*, e *myVM*.

1. Desalocar a VM que desaprovisionada com [az vm desalocar](/cli//azure/vm#deallocate). O exemplo seguinte deallocates VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Marcar a VM como generalizado com [az vm Generalizar](/cli//azure/vm#generalize). As marcas de exemplo seguintes a VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup* como generalizado:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Agora criar uma imagem do recurso VM com [criar imagem az](/cli//azure/image#create). O exemplo seguinte cria uma imagem com o nome *myImage* no grupo de recursos denominado *myResourceGroup* utilizando o recurso VM com o nome *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos VM de origem. Pode criar VMs em qualquer grupo de recursos na sua subscrição a partir desta imagem. Numa perspetiva de gestão, pode pretender criar um grupo de recursos específicos para os recursos da VM e imagens.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passo 3: Criar uma VM a partir da imagem capturada
Criar uma VM utilizando a imagem que criou com [az vm criar](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada *myVMDeployed* na imagem com o nome do *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Criar a VM no outro grupo de recursos 

Pode criar VMs a partir de uma imagem em qualquer grupo de recursos na sua subscrição. Para criar uma VM no grupo de recursos diferente que a imagem, especifique o ID de recurso completo para a imagem. Utilize [lista de imagens az](/cli/azure/image#list) para ver uma lista de imagens. O resultado é semelhante ao seguinte exemplo:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo seguinte utiliza [az vm criar](/cli/azure/vm#create) para criar uma VM de um grupo de recursos diferente que a imagem de origem especificando o ID de recurso de imagem:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Passo 4: Verificar a implementação

Agora SSH para a máquina virtual que criou para verificar a implementação e começar a utilizar a nova VM. Para ligar através de SSH, localizar o endereço IP ou FQDN do seu VM com [mostrar de vm az](/cli/azure/vm#show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Passos seguintes
Pode criar várias VMs a partir da imagem de VM de origem. Se precisar de efetuar alterações à sua imagem: 

- Crie uma VM a partir da imagem.
- Fazer quaisquer atualizações ou alterações de configuração.
- Siga os passos novamente para desaprovisionar, desalocar, generalize e criar uma imagem.
- Utilize esta nova imagem para implementações futuras. Se assim o desejar, elimine a imagem original.

Para obter mais informações sobre gerir as VMs com a CLI, consulte [Azure CLI 2.0](/cli/azure/overview).
