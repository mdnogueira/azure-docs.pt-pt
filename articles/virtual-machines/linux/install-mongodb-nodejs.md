---
title: Instalar MongoDB numa VM com Linux utilizando a CLI do Azure 1.0 | Microsoft Docs
description: "Saiba como instalar e configurar o MongoDB numa máquina virtual com Linux no Azure utilizando o modelo de implementação Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: ff9e23de41245ea21ba6e9c3efe13ca13b0b0ae1
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Como instalar e configurar o MongoDB numa VM com Linux utilizando a CLI do Azure 1.0
[MongoDB](http://www.mongodb.org) é um popular open source e de elevado desempenho base de dados NoSQL. Este artigo mostra como instalar e configurar o MongoDB numa VM com Linux no Azure utilizando o modelo de implementação Resource Manager. Os exemplos são apresentados como esse detalhe para:

* [Instalar e configurar uma instância do MongoDB básica manualmente](#manually-install-and-configure-mongodb-on-a-vm)
* [Criar uma instância do MongoDB básica com um modelo do Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Criar um MongoDB complexo define de cluster em partição horizontal com a réplica com um modelo do Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- Azure CLI 1.0 – CLI para os modelos de implementação de gestão clássica e de recursos (este artigo)
- [CLI 2.0 do Azure](create-cli-complete-nodejs.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar e configurar o MongoDB numa VM manualmente
MongoDB [fornecem instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para distros Linux, incluindo Red Hat / CentOS, SUSE, Ubuntu e Debian. O exemplo seguinte cria um *CentOS* VM com uma chave SSH armazenada em *~/.ssh/id_rsa.pub*. Responda os pedidos para o nome da conta de armazenamento, o nome DNS e credenciais de administrador:

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Inicie sessão para a VM com o endereço IP público apresentado no final do passo anterior para a criação de VM:

```bash
ssh azureuser@40.78.23.145
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

Por predefinição, é imposta SELinux nas imagens do CentOS que o impede de aceder a MongoDB. Instalar ferramentas de gestão de política e configurar SELinux para permitir o MongoDB funcionar no respetiva 27017 a porta TCP predefinida da seguinte forma. 

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
Pode criar uma instância do MongoDB básica numa única VM CentOS utilizando o modelo seguinte de início rápido do Azure a partir do GitHub. Este modelo utiliza a extensão de Script personalizado para Linux para adicionar um `yum` repositório à sua VM do CentOS recém-criado e, em seguida, instalar MongoDB.

* [Instância do MongoDB básica no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` no `eastus` região. Introduza os seus próprios valores da seguinte forma:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> A CLI do Azure devolve para uma linha de comandos dentro de alguns segundos de criação da implementação, mas a instalação e configuração demora alguns minutos a concluir. Verificar o estado da implementação com `azure group deployment show myResourceGroup`, introduzir o nome do grupo de recursos em conformidade. Aguarde até que o **ProvisioningState** mostra *com êxito* antes de tentar SSH para a VM.

Assim que a implementação estiver concluída, o SSH para a VM. Obter o endereço IP da VM utilizando o `azure vm show` comando como no exemplo seguinte:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Perto do final do resultado, é apresentado o endereço IP público. SSH para a VM com o endereço IP da sua VM:

```bash
ssh azureuser@138.91.149.74
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
> Implementar este cluster em partição horizontal do MongoDB complexo requer mais de 20 vCPUs, que é, geralmente, a contagem de vCPU predefinida por região para uma subscrição. Abra um pedido de suporte do Azure para aumentar a contagem de vCPU.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* no *eastus* região. Introduza os seus próprios valores da seguinte forma:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> A CLI do Azure devolve para uma linha de comandos dentro de alguns segundos de criação da implementação, mas a instalação e configuração podem demorar mais de uma hora para concluir. Verificar o estado da implementação com `azure group deployment show myResourceGroup`, ajuste o nome do grupo de recursos em conformidade. Aguarde até que o **ProvisioningState** mostra *com êxito* antes de ligar às VMs.


## <a name="next-steps"></a>Passos seguintes
Nestes exemplos, estabelece ligação à instância do MongoDB localmente da VM. Se pretender ligar à instância do MongoDB a partir de outro VM ou rede, certifique-se as adequadas [regras do grupo de segurança de rede são criadas](nsg-quickstart.md).

Para obter mais informações sobre como criar modelos de utilização, consulte o [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Os modelos Azure Resource Manager utilizam a extensão de Script personalizado para transferir e executar scripts nas suas VMs. Para obter mais informações, consulte [utilizando a extensão de Script personalizado do Azure com as máquinas virtuais Linux](extensions-customscript.md).

