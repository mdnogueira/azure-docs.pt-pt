---
title: Instalar MongoDB numa VM com Linux com a CLI do Azure | Microsoft Docs
description: "Saiba como instalar e configurar o MongoDB um iusing de máquina virtual Linux 2.0 de CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2017
ms.author: iainfou
ms.openlocfilehash: e19c09558285497f29eb78b4f4ae5b15d7f1a191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Como instalar e configurar o MongoDB numa VM com Linux
[MongoDB](http://www.mongodb.org) é um popular open source e de elevado desempenho base de dados NoSQL. Este artigo mostra como instalar e configurar o MongoDB numa VM com Linux com o 2.0 CLI do Azure. Também pode efetuar estes passos com a [CLI 1.0 do Azure](install-mongodb-nodejs.md). Os exemplos são apresentados como esse detalhe para:

* [Instalar e configurar uma instância do MongoDB básica manualmente](#manually-install-and-configure-mongodb-on-a-vm)
* [Criar uma instância do MongoDB básica com um modelo do Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Criar um MongoDB complexo define de cluster em partição horizontal com a réplica com um modelo do Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar e configurar o MongoDB numa VM manualmente
MongoDB [fornecem instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para distros Linux, incluindo Red Hat / CentOS, SUSE, Ubuntu e Debian. O exemplo seguinte cria um *CentOS* VM. Para criar este ambiente, tem a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada *myVM* com um utilizador com o nome *azureuser* utilizando a autenticação de chave pública SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a VM com o seu próprio nome de utilizador e a `publicIpAddress` listado no resultado do passo anterior:

```bash
ssh azureuser@<publicIpAddress>
```

Para adicionar as origens de instalação para o MongoDB, crie um **yum** ficheiro repositório da seguinte forma:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Abra o ficheiro de repositório do MongoDB para edição. Adicione as seguintes linhas:

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Instalar MongoDB utilizando **yum** da seguinte forma:

```bash
sudo yum install -y mongodb-org
```

Por predefinição, é imposta SELinux nas imagens do CentOS que o impede de aceder a MongoDB. Instalar ferramentas de gestão de política e configurar SELinux para permitir o MongoDB funcionar no respetiva 27017 a porta TCP predefinida da seguinte forma:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço do MongoDB da seguinte forma:

```bash
sudo service mongod start
```

Verificar a instalação do MongoDB através da ligação utilizando local `mongo` cliente:

```bash
mongo
```

Teste agora a instância do MongoDB adicionar alguns dados e, em seguida, procure:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se assim o pretender, configure o MongoDB para iniciar automaticamente durante um reinício do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Criar a instância do MongoDB básica no CentOS através de um modelo
Pode criar uma instância do MongoDB básica numa única VM CentOS utilizando o modelo seguinte de início rápido do Azure a partir do GitHub. Este modelo utiliza a extensão de Script personalizado para Linux para adicionar um **yum** repositório à sua VM do CentOS recém-criado e, em seguida, instalar MongoDB.

* [Instância do MongoDB básica no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Para criar este ambiente, tem a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login). Em primeiro lugar, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implementar o modelo MongoDB com [criar a implementação do grupo az](/cli/azure/group/deployment#create). Definir os seus próprios recursos os nomes e tamanhos sempre que necessário, tais como para *newStorageAccountName*, *virtualNetworkName*, e *vmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"},
    "virtualNetworkName": {"value": "myVnet"},
    "vmSize": {"value": "Standard_DS2_v2"},
    "vmName": {"value": "myVM"},
    "publicIPAddressName": {"value": "myPublicIP"},
    "nicName": {"value": "myNic"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Inicie sessão para a VM com o endereço DNS público da sua VM. Pode ver o endereço DNS público com [mostrar de vm az](/cli/azure/vm#show):

```azurecli
az vm show -g myResourceGroup -n myVM -d --query [fqdns] -o tsv
```

SSH para a VM com o seu próprio nome de utilizador e o endereço DNS público:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Verificar a instalação do MongoDB através da ligação utilizando local `mongo` cliente da seguinte forma:

```bash
mongo
```

A instância de teste agora ao adicionar alguns dados e procura da seguinte forma:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Criar um Cluster de em partição horizontal do MongoDB complexos no CentOS através de um modelo
Pode criar um cluster a MongoDB complexo utilizando o modelo seguinte de início rápido do Azure a partir do GitHub. Este modelo segue-se a [melhores práticas do MongoDB em partição horizontal cluster](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer redundância e elevada disponibilidade. O modelo cria dois shards, com três nós em cada conjunto de réplicas. Uma réplica do servidor de configuração definida com três nós também é criada, juntamente com dois **mongos** servidores de router para fornecer consistência a partir de aplicações através de partições horizontais.

* [Cluster de fragmentação do MongoDB em CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Este cluster em partição horizontal do MongoDB complexo a implementação requer mais de 20 núcleos, que é, geralmente, o número de núcleos predefinido por região para uma subscrição. Abra um pedido de suporte do Azure para aumentar a contagem de núcleos.

Para criar este ambiente, tem a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login). Em primeiro lugar, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implementar o modelo MongoDB com [criar a implementação do grupo az](/cli/azure/group/deployment#create). Definir os seus próprios recursos os nomes e tamanhos sempre que necessário, tais como para *mongoAdminUsername*, *sizeOfDataDiskInGB*, e *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Esta implementação pode demorar mais de uma hora para implementar e configurar todas as instâncias VM. O `--no-wait` sinalizador é utilizado no final do comando anterior para devolver o controlo a linha de comandos assim que a implementação do modelo foi aceite pela plataforma do Azure. Em seguida, pode ver o estado de implementação com [mostrar de implementação do grupo de az](/cli/azure/group/deployment#show). O exemplo seguinte visualiza o estado para o *myMongoDBCluster* implementação no *myResourceGroup* grupo de recursos:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Passos seguintes
Nestes exemplos, estabelece ligação à instância do MongoDB localmente da VM. Se pretender ligar à instância do MongoDB a partir de outro VM ou rede, certifique-se as adequadas [regras do grupo de segurança de rede são criadas](nsg-quickstart.md).

Estes exemplos implementar o ambiente do MongoDB de núcleos para fins de desenvolvimento. Aplique as opções de configuração de segurança necessárias para o seu ambiente. Para obter mais informações, consulte o [docs de segurança do MongoDB](https://docs.mongodb.com/manual/security/).

Para obter mais informações sobre como criar modelos de utilização, consulte o [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Os modelos Azure Resource Manager utilizam a extensão de Script personalizado para transferir e executar scripts nas suas VMs. Para obter mais informações, consulte [utilizando a extensão de Script personalizado do Azure com as máquinas virtuais Linux](extensions-customscript.md).

