---
title: "Como criar DSVM e HDI como destinos de computação do Azure ML"
description: "Crie cluster DSVM e HDI Spark como destinos de experimentação do Azure ML de computação."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 9ce1d32a2785bec1164d2a89dea9946fe113cb33
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Criar cluster DSVM e HDI Spark como destinos de computação

Pode facilmente aumentar verticalmente ou ampliar a sua experimentação de aprendizagem adicionando destinos de computação adicionais, tais como baseado no Ubuntu DSVM (máquina de Virtual de ciência de dados) e o Apache Spark para o cluster do Azure HDInsight. Este artigo explica como os passos de criação de estes computação destinos no Azure. Para obter mais informações sobre metas de computação do Azure ML, consulte [descrição geral do serviço de experimentação do Azure Machine Learning](experimentation-service-configuration.md).

>[!NOTE]
>Certifique-se de que tem permissões adequadas para criar recursos, tais como clusters VM e HDI no Azure antes de continuar. Também ambos estes recursos podem consumir muitos computação núcleos, consoante a configuração. Certifique-se a que sua subscrição tem capacidade suficiente para os núcleos de CPU virtuais. Pode obter sempre entre em contacto com o suporte do Azure para aumentar o número máximo de núcleos permitido na sua subscrição.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Criar um DSVM Ubuntu no portal do Azure

Pode criar um DSVM a partir do portal do Azure. 

1. Inicie sessão no portal do Azure de https://portal.azure.com
2. Clique em de **+ novo** ligação e procure "dados máquina de virtual de ciência para Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Escolha **Máquina Virtual de ciência de dados para Linux (Ubuntu)** na lista e siga o ecrã instruções para criar o DSVM.

>[!IMPORTANT]
>Certifique-se de que escolhe **palavra-passe** como o _tipo de autenticação_.

![utilizar pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Criar um DSVM Ubuntu utilizando a cli do azure

Também pode utilizar um modelo de gestão de recursos do Azure para implementar um DSVM.

>[!NOTE]
>Todos os comandos seguintes são considerados como ser emitido a partir da pasta raiz de um projeto do Azure ML.

Em primeiro lugar, crie um `mydsvm.json` ficheiros utilizando o editor de texto favorito no `docs` pasta. (Se não tiver um `docs` pasta na pasta raiz do projeto, crie uma.) Este ficheiro são utilizadas para configurar alguns parâmetros básicos para o modelo de gestão de recursos do Azure. 

Copie e cole o fragmento JSON seguinte para o `mydsvm.json` de ficheiros e preencha os valores apropriados:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Para o _vmSize_ campo, pode utilizar qualquer tamanho VM suppported listado no [modelo de gestão de recursos do Azure de DSVM Ubuntu](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Recomendamos que utilize um do abaixo tamanhos como destinos de computação do Azure ML. 

- Standard_DS2_v2 
- Standard_DS3_v2 
- Standard_DS4_v2 
- Standard_DS12_v2 
- Standard_DS13_v2 
- Standard_DS14_v2 
- Standard_NC6 
- Standard_NC12 
- Standard_NC24 
 
>[!TIP]
> Os tamanhos VM começar a utilizar "NC" são os equipados com uma GPU.

Saiba mais sobre estes [tamanhos de máquinas virtuais do Linux no Azure](../../virtual-machines/linux/sizes.md) e os respetivos [obter informações sobre preços](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Inicie a janela de CLI da aplicação do Azure ML Workbench ao clicar no **ficheiro** --> **abra a linha de comandos**, ou **PowerShell aberta** item de menu. 

>[!NOTE]
>Também pode fazer isto em qualquer ambiente da linha de comandos em que tenha instalado az-cli.

Na janela da linha de comandos, introduza o abaixo comandos:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Anexar um destino de computação DSVM
Assim que for criado o DSVM, agora pode anexá-la ao projeto do Azure ML.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> --type remotedocker

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Agora deve estar pronto para executar experimentações neste DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Desalocar uma DSVM e reiniciar mais tarde
Quando concluir as tarefas de computação do Azure ML, pode anular a atribuição a DSVM. Esta ação será encerrado a VM, liberta os recursos de computação, mas que ela vai preservando os discos virtuais. Não lhe serem cobrados o custo de computação quando a VM é desalocada.

Ao anular atribuição de uma VM:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Para colocar a VM vida, utilize o `az ml start` comando:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Expanda o disco de SO DSVM
VM com Linux no Azure, normalmente, é fornecido com um disco de sistema de operativo 30 GB. Quando utilizado como destino de computação do Azure ML, este pode ser eaten rapidamente pelo motor de Docker extrair para baixo de imagens do Docker e a criação de camadas de conda seguir. É uma boa ideia para expandir o disco de SO para um tamanho maior (por exemplo, 200 GB) para evitar o erro de "disco está cheio" enquanto estiver no meio de uma execução. Referência [como expandir os discos rígidos virtuais numa VM com Linux com a CLI do Azure](../../virtual-machines/linux/expand-disks.md) para saber como fazê-lo facilmente da cli do azure. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Criar do Apache Spark para o cluster do Azure HDInsight no portal do Azure

Para executar tarefas do Spark de escalamento horizontal, terá de criar do Apache Spark para o cluster do Azure HDInsight no portal do Azure.

1. Inicie sessão no portal do Azure de https://portal.azure.com
2. Clique em de **+ novo** ligação e procure "HDInsight".

    ![localizar hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Escolha **HDInsight** na lista e, em seguida, clique em de **criar** botão.
4. No **Noções básicas** ecrã de configuração, **tipo de Cluster** definições, certifique-se de que escolhe **Spark** como o _tipo de Cluster_, **Linux** como o _sistema operativo_, e **Spark 2.1.0 (HDI 3.6)** como o _Version.

    ![Configurar hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Observe que o ecrã acima, o cluster tem um _nome de utilizador de início de sessão de Cluster_ campo e um _Secure Shell (SSH) username_ campo. Estes são dois identidades de utilizador diferente, embora para sua comodidade, pode especificar a mesma palavra-passe para ambos os inícios de sessão. O _nome de utilizador de início de sessão de Cluster_ é utilizado para iniciar sessão na Web de gestão da IU do HDI cluster. O _nome de utilizador de início de sessão SSH_ é utilizado para iniciar sessão no nó principal do cluster, este é o que é necessário para o Azure ML emitir as tarefas do Spark.

5. Escolha o tamanho do cluster e o tamanho de nó que precisa e concluir o Assistente de criação. Pode demorar até 30 minutos para o cluster concluir o aprovisionamento. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Anexar um destino de computação de cluster do HDI Spark

Depois do cluster do Spark HDI for criado, pode agora ligá-lo ao seu projeto do Azure ML.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> --type cluster

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Agora deve estar pronto para executar experimentações deste cluster do Spark.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:
- [Descrição geral do serviço de experimentação do Azure Machine Learning](experimentation-service-configuration.md)
- [Azure ficheiros de configuração do serviço de experimentação do Machine Learning Workbench](experimentation-service-configuration-reference.md)
- [Apache Spark para o cluster do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Máquina de Virtual de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
