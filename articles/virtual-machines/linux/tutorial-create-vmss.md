---
title: "Criar conjuntos de dimensionamento de Máquina Virtual para Linux no Azure | Microsoft Docs"
description: "Criar e implementar uma aplicação altamente disponível em VMs do Linux utilizando um conjunto de dimensionamento de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/08/2017
ms.author: iainfou
ms.openlocfilehash: 1f54bb04023ad61f4eae51389c6a902a029e9399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Criar um conjunto de dimensionamento de Máquina Virtual e implementar uma aplicação altamente disponível no Linux
Um conjunto de dimensionamento de máquina virtual permite-lhe implementar e gerir um conjunto de máquinas virtuais idênticas de dimensionamento automático. Pode aumentar o número de VMs no conjunto de dimensionamento manualmente ou definir as regras para dimensionar automaticamente com base na utilização de recursos, tais como CPU, a pedido de memória ou tráfego de rede. Neste tutorial, implementa um conjunto no Azure de dimensionamento de máquina virtual. Saiba como:

> [!div class="checklist"]
> * Utilize init de nuvem para criar uma aplicação a dimensionar
> * Criar um conjunto de dimensionamento de máquina virtual
> * Aumentar ou reduzir o número de instâncias de um conjunto de dimensionamento
> * Criar regras de dimensionamento automático
> * Ver informações de ligação para as instâncias do conjunto de dimensionamento
> * Utilizar discos de dados num conjunto de dimensionamento


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="scale-set-overview"></a>Descrição geral do conjunto de dimensionamento
Um conjunto de dimensionamento de máquina virtual permite-lhe implementar e gerir um conjunto de máquinas virtuais idênticas de dimensionamento automático. VMs num conjunto de dimensionamento estão distribuídas por domínios de falhas e de atualização de lógica num ou mais *grupos colocação*. Estes são os grupos de VMs configurados da mesma forma, semelhantes à [conjuntos de disponibilidade](tutorial-availability-sets.md).

As VMs são criadas conforme necessário de um conjunto de dimensionamento. Pode definir regras de dimensionamento automático para controlar como e quando VMs são adicionadas ou removidas do conjunto de dimensionamento. Estas regras podem acionar com base nas métricas, tais como a carga de CPU, utilização de memória ou tráfego de rede.

A conjuntos de dimensionamento suporte até 1000 VMs ao utilizar uma imagem de plataforma do Azure. Para cargas de trabalho com requisitos de personalização de VM ou instalação significativa, poderá pretender [criar uma imagem VM personalizada](tutorial-custom-images.md). Pode criar até 300 VMs em escala definida quando utilizar uma imagem personalizada.


## <a name="create-an-app-to-scale"></a>Criar uma aplicação a dimensionar
Para utilização em produção, poderá pretender [criar uma imagem VM personalizada](tutorial-custom-images.md) que inclui a aplicação instalada e configurada. Para este tutorial, permite personalizar as VMs no primeiro arranque para ver rapidamente um conjunto em ação de dimensionamento.

Um tutorial anterior, aprendeu [como personalizar uma máquina virtual do Linux no primeiro arranque](tutorial-automate-vm-deployment.md) com init de nuvem. Pode utilizar o mesmo ficheiro de configuração de nuvem init para instalar NGINX e executar uma aplicação Node.js "Olá, mundo" simples. 

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


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Antes de poder criar um conjunto de dimensionamento, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupScaleSet* no *eastus* localização:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Agora criar um conjunto com de dimensionamento de máquina virtual [az vmss criar](/cli/azure/vmss#create). O exemplo seguinte cria um conjunto nomeado de dimensionamento *myScaleSet*, utiliza o ficheiro de nuvem init para personalizar a VM e gera chaves SSH, caso não existam:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Demora alguns minutos para criar e configurar todos os recursos de conjunto de dimensionamento e VMs. Existem tarefas em segundo plano que continuam a ser executado após a CLI do Azure devolve ao pedido. Pode ser outro alguns minutos antes de poder aceder a aplicação.


## <a name="allow-web-traffic"></a>Permitir o tráfego da web
Um balanceador de carga foi criado automaticamente como parte do conjunto de dimensionamento de máquina virtual. O Balanceador de carga distribui o tráfego por um conjunto de VMs definidos através de regras de Balanceador de carga. Pode saber mais sobre conceitos de Balanceador de carga e a configuração no próximo tutorial [como carregar saldo máquinas de virtuais no Azure](tutorial-load-balancer.md).

Para permitir o tráfego alcançar a aplicação web, crie uma regra com [criar regra de lb az rede](/cli/azure/network/lb/rule#create). O exemplo seguinte cria uma regra com o nome *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Testar a aplicação
Para ver a sua aplicação Node.js na web, obter o endereço IP público do seu Balanceador de carga com [mostrar de ip público de rede az](/cli/azure/network/public-ip#show). O exemplo seguinte obtém o endereço IP para *myScaleSetLBPublicIP* criada como parte do conjunto de dimensionamento:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Introduza o endereço IP público para um web browser. A aplicação é apresentada, incluindo o nome do anfitrião da VM que o Balanceador de carga distribuído tráfego para:

![Aplicação Node.js em execução](./media/tutorial-create-vmss/running-nodejs-app.png)

Para ver o conjunto na ação de dimensionamento, pode forçar atualizar o web browser para ver o Balanceador de carga distribuir o tráfego entre todas as VMs com a sua aplicação.


## <a name="management-tasks"></a>Tarefas de gestão
Ao longo do ciclo de vida do conjunto de dimensionamento, poderá ter de executar um ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizem várias tarefas de ciclo de vida. O 2.0 CLI do Azure fornece uma forma rápida para realizar essas tarefas. Seguem-se algumas tarefas comuns.

### <a name="view-vms-in-a-scale-set"></a>Vista VMs num conjunto de dimensionamento
Para ver uma lista de VMs em execução no seu conjunto de dimensionamento, utilize [az vmss-instâncias da lista](/cli/azure/vmss#list-instances) da seguinte forma:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

O resultado é semelhante ao seguinte exemplo:

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentar ou diminuir instâncias de VM
Para ver o número de instâncias tem atualmente num conjunto de dimensionamento, utilize [mostrar de vmss az](/cli/azure/vmss#show) e consultar no *sku.capacity*:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Pode, em seguida, manualmente aumentar ou reduzir o número de máquinas virtuais da escala com [escala de vmss az](/cli/azure/vmss#scale). O exemplo a seguir define o número de VMs no seu dimensionamento definido como *5*:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 5
```


### <a name="configure-autoscale-rules"></a>Configurar regras de dimensionamento automático
Em vez de conjunto de dimensionamento manualmente o número de instâncias no seu dimensionamento, pode definir regras de dimensionamento automático. Estas regras monitorizar instâncias do conjunto de dimensionamento e respondem em conformidade com base nas métricas e os limiares que define. O exemplo seguinte aumenta horizontalmente de forma o número de instâncias por um quando a carga média da CPU é superior a 60% durante um período de 5 minutos. Se a carga de CPU média, em seguida, descerem abaixo de 30% durante um período de 5 minutos, as instâncias são ampliadas por uma instância. O ID de subscrição é utilizado para criar o recurso de URI para a escala de vários componentes do conjunto. Para criar estas regras com [criar definições de dimensionamento automático do monitor az](/cli/azure/monitor/autoscale-settings#create), copie e cole o seguinte perfil de dimensionamento automático de comando:

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

Para reutilizar o perfil de dimensionamento automático, pode criar um ficheiro JSON (JavaScript Object Notation) e que para passar o `az monitor autoscale-settings create` comando com o `--parameters @autoscale.json` parâmetro. Para obter mais informações de conceção sobre a utilização de dimensionamento automático, consulte [melhores práticas de dimensionamento automático](/azure/architecture/best-practices/auto-scaling).


### <a name="get-connection-info"></a>Obter as informações de ligação
Para obter informações sobre as VMs no seu conjuntos de dimensionamento da ligação, utilize [az vmss lista-instância--informações de ligação](/cli/azure/vmss#list-instance-connection-info). Este comando devolve o endereço IP público e a porta para cada VM que permite-lhe estabelecer ligação com o SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Utilizar discos de dados com conjuntos de dimensionamento
Pode criar e utilizar discos de dados com conjuntos de dimensionamento. Um tutorial anterior, aprendeu como [discos Azure gerir](tutorial-manage-disks.md) que descreve as melhores práticas e melhorias de desempenho para a criação de aplicações em discos de dados em vez do disco do SO.

### <a name="create-scale-set-with-data-disks"></a>Criar conjunto de dimensionamento com discos de dados
Para criar um conjunto de dimensionamento e anexe discos de dados, adicione o `--data-disk-sizes-gb` parâmetro para o [az vmss criar](/cli/azure/vmss#create) comando. O exemplo seguinte cria um conjunto com de dimensionamento *50*discos de dados de Gb anexados a cada instância:

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Quando instâncias são removidas de um conjunto de dimensionamento, também são removidos quaisquer discos de dados anexados.

### <a name="add-data-disks"></a>Adicionar discos de dados
Para adicionar um disco de dados para instâncias do conjunto de dimensionamento, utilize [anexar o disco de vmss az](/cli/azure/vmss/disk#attach). O exemplo seguinte adiciona um *50*disco Gb para cada instância:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Desanexar os discos de dados
Para remover um disco de dados para instâncias do conjunto de dimensionamento, utilize [desanexar o disco de vmss az](/cli/azure/vmss/disk#detach). O exemplo a seguir remove o disco de dados em LUN *2* de cada instância:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, vai criar um conjunto de dimensionamento de máquina virtual. Aprendeu a:

> [!div class="checklist"]
> * Utilize init de nuvem para criar uma aplicação a dimensionar
> * Criar um conjunto de dimensionamento de máquina virtual
> * Aumentar ou reduzir o número de instâncias de um conjunto de dimensionamento
> * Criar regras de dimensionamento automático
> * Ver informações de ligação para as instâncias do conjunto de dimensionamento
> * Utilizar discos de dados num conjunto de dimensionamento

Avançar para o próximo tutorial para saber mais sobre conceitos para máquinas virtuais de balanceamento de carga.

> [!div class="nextstepaction"]
> [Máquinas virtuais de balanceamento de carga](tutorial-load-balancer.md)
