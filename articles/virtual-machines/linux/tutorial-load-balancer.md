---
title: Como carregar equilibrar os computadores virtuais Linux no Azure | Microsoft Docs
description: "Saiba como utilizar o Balanceador de carga do Azure para criar uma aplicação de elevada disponibilidade e segura entre três VMs do Linux"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dc25d6106ad67710660b1a5c48270a7082688d51
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="how-to-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Como carregar equilibrar os computadores virtuais Linux no Azure para criar uma aplicação altamente disponível
Balanceamento de carga fornece um nível mais elevado de disponibilidade propagando-se os pedidos recebidos em várias máquinas virtuais. Neste tutorial, pode saber mais sobre os diferentes componentes do Balanceador de carga do Azure que distribui o tráfego de e para fornecem elevada disponibilidade. Saiba como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do Balanceador de carga
> * Criar regras de tráfego de Balanceador de carga
> * Utilize init da nuvem para criar uma aplicação Node.js básica
> * Criar máquinas virtuais e ligue a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover as VMs a partir de um balanceador de carga


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="azure-load-balancer-overview"></a>Descrição geral do Balanceador de carga do Azure
Um balanceador de carga do Azure é um balanceador de carga de camada 4 (TCP, UDP) que fornece elevada disponibilidade, distribuição de tráfego de entrada entre VMs em bom estado. Uma sonda de estado de funcionamento do Balanceador de carga monitoriza uma porta especificada em cada VM e apenas distribui o tráfego para uma VM operacional.

É possível definir uma configuração de IP Front-end que contém um ou mais endereços IP públicos. Esta configuração de IP Front-end permite que o Balanceador de carga e aplicações para estar acessível através da Internet. 

As máquinas virtuais ligar a um balanceador de carga com o seu cartão de interface de rede virtual (NIC). Para distribuir o tráfego para as VMs, um conjunto de endereços de back-end contém o IP endereços dos virtuais (NICs) ligadas ao balanceador de carga.

Para controlar o fluxo de tráfego, é possível definir as regras de Balanceador de carga para portas específicas e protocolos que mapeiam para as suas VMs.

Se seguiu o tutorial anterior para [criar um conjunto de dimensionamento de máquina virtual](tutorial-create-vmss.md), um balanceador de carga foi criado por si. Todos estes componentes foram configurados automaticamente como parte do conjunto de dimensionamento.


## <a name="create-azure-load-balancer"></a>Criar Balanceador de carga do Azure
Esta secção descreve em detalhe como pode criar e configurar cada componente do Balanceador de carga. Antes de poder criar seu Balanceador de carga, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupLoadBalancer* no *eastus* localização:

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Para aceder à sua aplicação na Internet, terá um endereço IP público para o Balanceador de carga. Criar um endereço IP público com [az público-ip da rede criar](/cli/azure/network/public-ip#create). O exemplo seguinte cria um endereço IP público com o nome *myPublicIP* no *myResourceGroupLoadBalancer* grupo de recursos:

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Criar um balanceador de carga com [az rede lb criar](/cli/azure/network/lb#create). O exemplo seguinte cria um balanceador de carga com o nome *myLoadBalancer* e atribui o *myPublicIP* endereço para a configuração de IP Front-end:

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Criar uma sonda do Estado de funcionamento
Para permitir que o Balanceador de carga monitorizar o estado da sua aplicação, pode utilizar uma sonda do Estado de funcionamento. A sonda de estado de funcionamento dinamicamente adiciona ou remove as VMs a rotação de Balanceador de carga com base na respetiva resposta para verificações de estado de funcionamento. Por predefinição, uma VM é removida da distribuição do Balanceador de carga após duas falhas consecutivas em intervalos de 15 segundo. Criar uma sonda do Estado de funcionamento com base num protocolo ou uma página de verificação do Estado de funcionamento específico para a sua aplicação. 

O exemplo seguinte cria uma sonda TCP. Também pode criar das sondas personalizadas do HTTP para obter mais detalhada do Estado de funcionamento verificações. Quando utilizar uma pesquisa HTTP personalizada, tem de criar a página de verificação do Estado de funcionamento, tal como *healthcheck.js*. A sonda tem de devolver um **HTTP 200 OK** resposta para o Balanceador de carga manter o anfitrião no rotação.

Para criar uma sonda do Estado de funcionamento TCP, utilize [sonda do az rede lb criar](/cli/azure/network/lb/probe#create). O exemplo seguinte cria uma sonda do Estado de funcionamento com o nome *myHealthProbe*:

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de Balanceador de carga
Uma regra de Balanceador de carga é utilizada para definir a forma como o tráfego é distribuído para as VMs. É possível definir a configuração de IP Front-end o tráfego de entrada e o conjunto IP back-end para receber o tráfego, juntamente com a porta de origem e de destino necessário. Para garantir que apenas as VMs bom receberem tráfego, também é possível definir a sonda de estado de funcionamento para utilizar.

Criar uma regra de Balanceador de carga com [criar regra de lb az rede](/cli/azure/network/lb/rule#create). O exemplo seguinte cria uma regra com o nome *myLoadBalancerRule*, utiliza o *myHealthProbe* sonda do Estado de funcionamento e saldos de tráfego na porta *80*:

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Configurar uma rede virtual
Antes de implementar algumas VMs e pode testar o seu balanceador, crie os suporte recursos de rede virtual. Para obter mais informações sobre as redes virtuais, consulte o [gerir redes virtuais do Azure](tutorial-virtual-network.md) tutorial.

### <a name="create-network-resources"></a>Criar recursos de rede
Criar uma rede virtual com [az rede vnet criar](/cli/azure/network/vnet#create). O exemplo seguinte cria uma rede virtual denominada *myVnet* com uma sub-rede denominada *mySubnet*:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Para adicionar um grupo de segurança de rede, utilize [az rede nsg criar](/cli/azure/network/nsg#create). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Criar uma regra de grupo de segurança de rede com [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create). O exemplo seguinte cria uma regra de grupo de segurança de rede com o nome *myNetworkSecurityGroupRule*:

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

NICs virtuais são criados com [nic da rede az criar](/cli/azure/network/nic#create). O exemplo seguinte cria três NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar o NICs virtuais adicionais e VMs em qualquer altura e adicioná-los para o Balanceador de carga:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Quando todos os NICs virtuais três são criados, avançar para o passo seguinte


## <a name="create-virtual-machines"></a>Criar máquinas virtuais

### <a name="create-cloud-init-config"></a>Criar a configuração de nuvem init
Um tutorial anterior em [como personalizar uma máquina virtual do Linux no primeiro arranque](tutorial-automate-vm-deployment.md), aprendeu a automatizar a personalização de VM com init de nuvem. Pode utilizar o mesmo ficheiro de configuração de nuvem init para instalar NGINX e executar um simples "Olá, mundo" aplicação Node.js no próximo passo. Para ver o Balanceador de carga em ação, no final do tutorial, vai aceder a esta aplicação simple num web browser.

Na sua shell atual, crie um ficheiro denominado *nuvem init.txt* e cole a seguinte configuração. Por exemplo, crie o ficheiro na Shell na nuvem não no seu computador local. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista de editores disponíveis. Certifique-se de que o ficheiro de toda a nuvem-init é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais
Para melhorar a elevada disponibilidade da sua aplicação, coloque as suas VMs num conjunto de disponibilidade. Para obter mais informações sobre conjuntos de disponibilidade, consulte o artigo anterior [como criar máquinas virtuais altamente disponíveis](tutorial-availability-sets.md) tutorial.

Criar um conjunto de disponibilidade com [az vm conjunto de disponibilidade criar](/cli/azure/vm/availability-set#create). O exemplo seguinte cria um conjunto nomeado de disponibilidade *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Agora pode criar as VMs com [az vm criar](/cli/azure/vm#create). O exemplo seguinte cria três VMs e gera chaves SSH, caso ainda não existam:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Existem tarefas em segundo plano que continuam a ser executado após a CLI do Azure devolve ao pedido. O `--no-wait` parâmetro não aguarda para todas as tarefas a concluir. Pode ser outro alguns minutos antes de poder aceder a aplicação. A sonda de estado de funcionamento do Balanceador de carga Deteta automaticamente quando a aplicação está em execução em cada VM. Assim que a aplicação está em execução, a regra de Balanceador de carga começa a distribuir o tráfego.


## <a name="test-load-balancer"></a>Balanceador de carga de teste
Obter o endereço IP público do seu Balanceador de carga com [mostrar de ip público de rede az](/cli/azure/network/public-ip#show). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Em seguida, pode introduzir o endereço IP público para um web browser. Lembre-se - demora alguns minutos para que as VMs para estar pronto antes de começa o Balanceador de carga distribuir o tráfego aos mesmos. A aplicação é apresentada, incluindo o nome do anfitrião da VM que o Balanceador de carga distribuído tráfego como no exemplo seguinte:

![Aplicação Node.js em execução](./media/tutorial-load-balancer/running-nodejs-app.png)

Para ver o Balanceador de carga distribuir o tráfego entre todas as três VMs executar a sua aplicação, pode forçar atualizar o browser.


## <a name="add-and-remove-vms"></a>Adicionar e remover VMs
Terá de efetuar a manutenção em VMs executar a sua aplicação, como a instalação de atualizações do SO. Para lidar com maior tráfego para a sua aplicação, poderá ter de adicionar VMs adicionais. Esta secção mostra como remover ou adicionar uma VM do Balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do Balanceador de carga
Pode remover uma VM de conjunto de endereços back-end com [az nic configuração de ip-conjunto de endereços rede remover](/cli/azure/network/nic/ip-config/address-pool#remove). O exemplo a seguir remove o NIC virtual para **myVM2** de *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Para ver o Balanceador de carga distribuir o tráfego entre as restantes duas VMs, executar a sua aplicação, pode forçar-atualizar o browser. Agora pode efetuar a manutenção da VM, tais como instalar atualizações do SO ou efetuar um reinício VM.

Para ver uma lista de VMs com NICs virtuais ligadas ao balanceador de carga, utilize [mostrar de conjunto de endereços do az rede lb](/cli/azure/network/lb/address-pool#show). Consultar e filtrar o ID de virtual NIC da seguinte forma:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

O resultado será semelhante ao exemplo seguinte, que mostra que o NIC virtual para a VM 2 já não faz parte do conjunto de endereços back-end:

```bash
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM para o Balanceador de carga
Depois de efetuar a manutenção VM, ou se precisa de expandir a capacidade, pode adicionar uma VM para o conjunto de endereços de back-end com [az nic configuração de ip-conjunto de endereços rede adicionar](/cli/azure/network/nic/ip-config/address-pool#add). O exemplo seguinte adiciona o NIC virtual para **myVM2** para *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Para verificar que o virtual NIC está ligado ao conjunto de endereços back-end, utilize [mostrar de conjunto de endereços do az rede lb](/cli/azure/network/lb/address-pool#show) novamente a partir do passo anterior.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um balanceador de carga e anexados VMs. Aprendeu a:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do Balanceador de carga
> * Criar regras de tráfego de Balanceador de carga
> * Utilize init da nuvem para criar uma aplicação Node.js básica
> * Criar máquinas virtuais e ligue a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover as VMs a partir de um balanceador de carga

Avançar para o próximo tutorial para saber mais sobre os componentes de rede virtual do Azure.

> [!div class="nextstepaction"]
> [Gerir VMs e redes virtuais](tutorial-virtual-network.md)
