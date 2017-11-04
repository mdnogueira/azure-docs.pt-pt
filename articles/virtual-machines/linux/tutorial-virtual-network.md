---
title: "Redes virtuais do Azure e máquinas virtuais do Linux | Microsoft Docs"
description: "Tutorial - Gerir redes virtuais do Azure e máquinas virtuais do Linux com a CLI do Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a49b4c2d4ddd6d686675cee53d46cd4dd6ad3811
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Gerir redes virtuais do Azure e máquinas virtuais do Linux com a CLI do Azure

Máquinas virtuais do Azure utilizar redes do Azure para a comunicação de rede internos e externos. Este tutorial explica duas máquinas virtuais a implementar e configurar redes do Azure para estas VMs. Os exemplos neste tutorial partem do princípio de que as VMs estão a alojar uma aplicação web com um back-end da base de dados, no entanto, uma aplicação não está implementada no tutorial. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual e sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteja o tráfego de rede
> * Criar uma VM de back-end

Ao concluir este tutorial, pode ver estes recursos criados:

![Rede virtual com duas sub-redes](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* -a rede virtual que as VMs utilizam para comunicar entre si e a internet.
- *myFrontendSubnet* -a sub-rede no *myVNet* utilizado por recursos front-end.
- *myPublicIPAddress* -o endereço IP público utilizado para acesso *myFrontendVM* da internet.
- *myFrontentNic* -interface de rede utilizado pelo *myFrontendVM* para comunicar com *myBackendVM*.
- *myFrontendVM* -VM o utilizada para comunicar entre a internet e *myBackendVM*.
- *myBackendNSG* -o grupo de segurança de rede que controla a comunicação entre o *myFrontendVM* e *myBackendVM*.
- *myBackendSubnet* -a sub-rede associada *myBackendNSG* e utilizados pelos recursos de back-end.
- *myBackendNic* -interface de rede utilizado pelo *myBackendVM* para comunicar com *myFrontendVM*.
- *myBackendVM* -a VM que utiliza a porta 22 e 3306 para comunicar com *myFrontendVM*.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="vm-networking-overview"></a>Descrição geral de redes VM

Redes virtuais do Azure permitem ligações de rede seguro entre máquinas virtuais, a internet e outros serviços do Azure, tais como a base de dados SQL do Azure. Redes virtuais são divididas em segmentos lógicos denominados sub-redes. Sub-redes são utilizadas para o fluxo de controlo de rede e, como um limite de segurança. Quando implementar uma VM, geralmente inclui uma interface de rede virtual, o que está ligada a uma sub-rede.

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e sub-rede

Para este tutorial, é criada uma única rede virtual com duas sub-redes. Uma sub-rede do front-end para alojar uma aplicação web e uma sub-rede de back-end para o alojamento de um servidor de base de dados.

Antes de poder criar uma rede virtual, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myRGNetwork* na localização eastus.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Criar a rede virtual

Utilize o [az rede vnet criar](/cli/azure/network/vnet#create) comando para criar uma rede virtual. Neste exemplo, é denominada rede *mvVNet* e é dado um prefixo de endereço de *10.0.0.0/16*. Uma sub-rede também é criada com um nome de *myFrontendSubnet* e um prefixo de *10.0.1.0/24*. Mais tarde no tutorial uma VM de front-end está ligada a esta sub-rede. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Criar sub-rede

Uma nova sub-rede é adicionada à utilização de rede virtual a [az rede vnet sub-rede](/cli/azure/network/vnet/subnet#create) comando. Neste exemplo, a sub-rede é o nome *myBackendSubnet* e é dado um prefixo de endereço de *10.0.2.0/24*. Esta sub-rede é utilizada com todos os serviços de back-end.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Neste momento, uma rede foi criada e segmentada em duas sub-redes, um para serviços front-end e outra para serviços de back-end. Na secção seguinte, as máquinas virtuais são criadas e ligadas a estas sub-redes.

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Um endereço IP público permite que os recursos do Azure esteja acessível na internet. O método de atribuição do endereço IP público pode ser configurado como dinâmico ou estático. Por predefinição, um endereço IP público é dinamicamente atribuído. Endereços IP dinâmicos são lançados quando uma VM é desalocada. Este comportamento faz com que o endereço IP alterar durante qualquer operação que inclua uma Desalocação da VM.

O método de alocação pode ser definido como estático, que garante que o endereço IP permanecerá atribuído a uma VM, mesmo durante um Estado desalocado. Quando utilizar um endereço IP estaticamente atribuído, não é possível especificar o endereço IP de si próprio. Em vez disso, é atribuído partir de um conjunto de endereços disponíveis.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

Quando cria uma VM com o [az vm criar](/cli/azure/vm#create) comando, o método alocação de predefinido público IP endereço for dinâmico. Ao criar uma máquina virtual utilizando o [az vm criar](/cli/azure/vm#create) comando, que incluem o `--public-ip-address-allocation static` argumento para atribuir um endereço IP público estático. Esta operação não é demonstrada neste tutorial, no entanto, na secção seguinte, um endereço IP alocado dinamicamente é alterado para um endereço estaticamente atribuído. 

### <a name="change-allocation-method"></a>Alterar o método de alocação

O método de alocação de endereço IP pode ser alterado utilizando o [atualização de ip público de rede az](/cli/azure/network/public-ip#update) comando. Neste exemplo, o método de alocação de endereço IP da VM front-end é alterado para estático.

Em primeiro lugar, Desalocação da VM.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Utilize o [atualização de ip público de rede az](/cli/azure/network/public-ip#update) comando para atualizar o método de alocação. Neste caso, o `--allocation-method` está a ser definido como *estático*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Inicie a VM.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Nenhum endereço IP público

Muitas vezes, uma VM não precisa de estar acessível através da internet. Para criar uma VM sem um endereço IP público, utilize o `--public-ip-address ""` argumento com um conjunto vazio de aspas duplas. Esta configuração é demonstrada mais à frente neste tutorial.

## <a name="create-a-front-end-vm"></a>Criar uma VM de front-end

Utilize o [az vm criar](/cli/azure/vm#create) comando para criar a VM com o nome *myFrontendVM* utilizando *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Proteja o tráfego de rede

Os grupos de segurança de rede (NSG) contêm uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a Redes Virtuais do Azure (VNet). Os NSGs podem ser associados a sub-redes ou interfaces de rede individuais. Quando um NSG é associado uma interface de rede, aplica-se apenas a VM associada. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. 

### <a name="network-security-group-rules"></a>Regras do grupo de segurança de rede

Regras do NSG definem portas de rede durante o qual o tráfego é permitido ou negado. As regras podem incluir intervalos de endereços IP de origem e de destino para que o tráfego é controlado entre sistemas específicos ou sub-redes. Regras do NSG também incluem uma prioridade de (entre 1 — e 4096). As regras são avaliadas por ordem de prioridade. Uma regra com uma prioridade de 100 é avaliada antes de uma regra com prioridade 200.

Todos os NSGs contêm um conjunto de regras predefinidas. As regras predefinidas não podem ser eliminadas, mas como lhes é atribuída a prioridade mais baixa, podem ser substituídas pelas regras que criar.

- **Rede virtual** - tráfego com origem e de fim numa rede virtual é permitido nas direções de entrada e saídas.
- **Internet** - o tráfego de saída é permitido, mas o tráfego de entrada está bloqueado.
- **O Balanceador de carga** -Balanceador de carga permitir do Azure sonde o estado de funcionamento das VMs e instâncias de função. Se não estiver a utilizar um conjunto com balanceamento de carga, pode substituir esta regra.

### <a name="create-network-security-groups"></a>Criar grupos de segurança de rede

Um grupo de segurança de rede pode ser criado ao mesmo tempo, como uma VM utilizando o [az vm criar](/cli/azure/vm#create) comando. Ao fazê-lo, o NSG é associado a interface de rede de VMs e uma regra NSG é automaticamente criada para permitir tráfego na porta *22* de uma origem. Anteriormente no tutorial, o NSG front-end foi criado automaticamente com a VM de front-end. Uma regra NSG também foi criada para a porta 22 automaticamente. 

Em alguns casos, poderá ser útil para a pré-criar um NSG, por exemplo, quando não devem ser criadas regras SSH predefinidas ou quando o NSG deve ser ligado a uma sub-rede. 

Utilize o [az rede nsg criar](/cli/azure/network/nsg#create) comando para criar um grupo de segurança de rede.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

Em vez de associar o NSG a uma interface de rede, está associada a uma sub-rede. Nesta configuração, qualquer VM que está ligada à sub-rede herda as regras do NSG.

Atualizar a sub-rede existente com o nome *myBackendSubnet* com o novo NSG.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Proteger o tráfego de entrada

Quando foi criada a VM de front-end, foi criada uma regra NSG para permitir tráfego de entrada na porta 22. Esta regra permite ligações SSH para a VM. Neste exemplo, também deve ser permitido tráfego na porta *80*. Esta configuração permite que uma aplicação web para ser acedido na VM.

Utilize o [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create) comando para criar uma regra para a porta *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

A VM de front-end só é acessível na porta *22* e a porta *80*. Todos os outro tráfego de entrada é bloqueado no grupo de segurança de rede. Poderá ser útil visualizar as configurações de regras do NSG. Devolver a configuração de regras do NSG com o [lista de regras de rede az](/cli/azure/network/nsg/rule#list) comando. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>VM segura para o tráfego VM

Regras do grupo de segurança de rede também podem aplicar entre as VMs. Neste exemplo, a VM de front-end necessita para comunicar com a VM de back-end na porta *22* e *3306*. Esta configuração permite ligações SSH da VM front-end e também permitir que uma aplicação na VM front-end para comunicar com uma base de dados MySQL back-end. Deve ser bloqueado todo o restante tráfego entre as máquinas virtuais de front-end e back-end.

Utilize o [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create) comando para criar uma regra para a porta 22. Tenha em atenção que o `--source-address-prefix` argumento especifica um valor de *10.0.1.0/24*. Esta configuração assegura que apenas o tráfego da sub-rede do front-end é permitido através do NSG.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Agora, adicione uma regra para o tráfego de MySQL na porta 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Por fim, porque os NSGs tem uma regra predefinida, permitindo que todo o tráfego entre VMs na mesma VNet, pode ser criada uma regra para os NSGs de back-end bloquear todo o tráfego. Repare-se de que o `--priority` é atribuído um valor de *300*, que é menor que as regras do NSG e o MySQL. Esta configuração assegura que o tráfego SSH e o MySQL ainda é permitido através do NSG.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Criar a VM de back-end

Agora criar uma máquina virtual, que esteja anexada à *myBackendSubnet*. Tenha em atenção que o `--nsg` argument tem um valor de aspas duplas vazios. Um NSG não precisa de ser criado com a VM. A VM está ligada à sub-rede de back-end, o que se encontra protegido com o NSG de back-end previamente criado. Este NSG aplica-se à VM. Além disso, repare que o `--public-ip-address` argument tem um valor de aspas duplas vazios. Esta configuração cria uma VM sem um endereço IP público. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A VM de back-end só é acessível na porta *22* e a porta *3306* partir da sub-rede do front-end. Todos os outro tráfego de entrada é bloqueado no grupo de segurança de rede. Poderá ser útil visualizar as configurações de regras do NSG. Devolver a configuração de regras do NSG com o [lista de regras de rede az](/cli/azure/network/nsg/rule#list) comando. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou e redes do Azure como relacionados para máquinas virtuais protegidas. Aprendeu a:

> [!div class="checklist"]
> * Criar uma rede virtual e sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteja o tráfego de rede
> * Criar a VM de back-end

Avançar para o próximo tutorial para saber mais sobre como proteger dados em máquinas virtuais utilizando cópia de segurança do Azure. 

> [!div class="nextstepaction"]
> [Cópia de segurança de computadores virtuais Linux no Azure](./tutorial-backup-vms.md)
