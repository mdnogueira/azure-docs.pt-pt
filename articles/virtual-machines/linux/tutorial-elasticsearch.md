---
title: "Implementar ElasticSearch numa máquina virtual de desenvolvimento no Azure"
description: "Tutorial - instale a pilha elástico no desenvolvimento de um VM com Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 5b0b51504478cc0d501a89760ccd60808a69ccbd
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Instalar a pilha de elástica numa VM do Azure

Este artigo explica como implementar [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash), e [Kibana](https://www.elastic.co/products/kibana), numa VM com Ubuntu no Azure. Para ver a pilha elástico em ação, opcionalmente, pode ligar para Kibana e trabalhar com alguns dados de registo de exemplo. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM com Ubuntu num grupo de recursos do Azure
> * Instale Elasticsearch, Logstash e Kibana na VM
> * Enviar dados de exemplo para Elasticsearch com Logstash 
> * Abrir as portas e trabalhar com dados na consola do Kibana


 Esta implementação é adequada para o desenvolvimento básico com a pilha elástico. Para obter mais informações sobre a pilha de elástico, incluindo as recomendações para um ambiente de produção, consulte o [documentação elástica](https://www.elastic.co/guide/index.html) e [Centro de arquitetura do Azure](/azure/architecture/elasticsearch/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm#create). 

O exemplo seguinte cria uma VM com o nome *myVM* e cria chaves SSH caso estas ainda não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Se já não souber o endereço IP público da sua VM, execute o [lista de ip público de rede az](/cli/azure/network/public-ip#list) comando:

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP público correto da sua máquina virtual. Neste exemplo, o endereço IP é *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Instalar a pilha de elástica

Importar a chave de assinatura de Elasticsearch e atualizar a sua lista de origens APT para incluir o repositório de pacote elástico:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Instale o Virtual de Java na VM e configurar o JAVA_HOME variável este é necessário para os componentes de pilha elástico executar.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Execute os seguintes comandos para atualizar as origens de pacote Ubuntu e instalar Elasticsearch, Kibana e Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Instruções de instalação de detalhado, incluindo esquemas de diretório e a configuração inicial, são mantidas no [documentação do elástico](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Iniciar Elasticsearch 

Inicie Elasticsearch na VM com o seguinte comando:

```bash
sudo systemctl start elasticsearch.service
```

Este comando não produz nenhuma saída, por isso, certifique-se de que o Elasticsearch está em execução na VM com esta `curl` comando:

```bash
curl -XGET 'localhost:9200/'
```

Se estiver a executar Elasticsearch, consulte resultado como o seguinte:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Iniciar Logstash e adicionar dados Elasticsearch

Inicie Logstash com o seguinte comando:

```bash 
sudo systemctl start logstash.service
```

Teste Logstash no modo interativo para se certificar de que está a funcionar corretamente:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Este é um logstash básico [pipeline](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) que echoes entrada padrão para a saída padrão. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Configure Logstash para reencaminhar as mensagens de kernel da VM para Elasticsearch. Criar um novo ficheiro de um diretório vazio denominado `vm-syslog-logstash.conf` e cole a seguinte configuração de Logstash:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Esta configuração de teste e enviar os dados de syslog para Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

Consulte as entradas de syslog no seu terminal echoed como são enviadas para Elasticsearch. Utilize `CTRL+C` para sair sem Logstash assim que enviou alguns dados.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Iniciar Kibana e visualizar os dados da Elasticsearch

Editar `/etc/kibana/kibana.yml` e altere o endereço IP Kibana escuta para que possa aceder a partir do seu browser.

```bash
server.host:"0.0.0.0"
```

Inicie Kibana com o seguinte comando:

```bash
sudo systemctl start kibana.service
```

Abra a porta 5601 partir da CLI do Azure para permitir o acesso remoto para a consola Kibana:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Abrir a consola Kibana e selecione **criar** para gerar um índice predefinido com base nos dados syslog enviado para Elasticsearch anteriormente. 

![Procurar eventos Syslog Kibana](media/elasticsearch-install/kibana-index.png)

Selecione **detetar** na consola do Kibana para pesquisar, procurar e filtrar através de eventos do syslog.

![Procurar eventos Syslog Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou a pilha de elástico no desenvolvimento VM no Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM com Ubuntu num grupo de recursos do Azure
> * Instale Elasticsearch, Logstash e Kibana na VM
> * Enviar dados de exemplo para Elasticsearch de Logstash 
> * Abrir as portas e trabalhar com dados na consola do Kibana