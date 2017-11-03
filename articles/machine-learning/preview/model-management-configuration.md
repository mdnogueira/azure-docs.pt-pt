---
title: "A configuração de gestão de modelo do Azure Machine Learning e configuração | Microsoft Docs"
description: "Este documento descreve os conceitos e os passos envolvidos na configuração e como configurar a gestão de modelo do Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: c89596a6d721c4cba899b8a6e2859ee36cba7b80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-setup"></a>Configuração de gestão de modelo

## <a name="overview"></a>Descrição geral
Este documento obtém-lo a começar a utilizar a gestão de modelo do Azure ML para implementar e gerir os seus modelos de machine learning como serviços web. 

Utilizando a gestão de modelo do Azure ML, pode implementar e gerir eficientemente os modelos de Machine Learning que são criados utilizando um número de estruturas, incluindo SparkML, Keras, TensorFlow, o Toolkit de cognitivos ou Python. 

No final deste documento, deve ser capaz de ter o seu ambiente de gestão de modelo configurado e pronto para implementar os modelos de machine learning.

## <a name="what-you-need-to-get-started"></a>O que precisa de começar
Para tirar o máximo partido deste guia, deve ter acesso de proprietário para uma subscrição do Azure que pode implementar os seus modelos para.
A CLI é previamente instalada o Workbench do Azure Machine Learning e no [Azure DSVMs](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Utilizar a CLI
Para utilizar as interfaces de linha de comandos (CLIs) do Workbench, clique em **ficheiro** -] **Interface de linha de comandos aberta**. 

Em dados de ciência de Máquina Virtual, ligue e abra a linha de comandos. Tipo `az ml -h` para ver as opções. Para obter mais detalhes sobre os comandos, utilize o - sinalizador de ajuda.

Em todos os outros sistemas, terá de instalar os CLIs.

### <a name="installing-or-updating-on-windows"></a>Instalar (ou atualizar) no Windows

Instale o Python https://www.python.org/. Certifique-se de que selecionou para instalar o pip.

Abra uma linha de comandos utilizando executar como administrador e execute os seguintes comandos:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Se tiver uma versão anterior, desinstale-pela primeira vez utilizando o seguinte comando:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Instalar (ou atualizar) no Linux
Execute o seguinte comando na linha de comandos e siga as instruções:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Depois de concluída a intallation, execute o seguinte comando:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Terminar sessão e voltar a iniciar sessão para a sua sessão SSH para que as alterações entrem em vigor.
>Pode utilizar os comandos anteriores para atualizar uma versão anterior do CLIs no DSVM.
>

## <a name="deploying-your-model"></a>Implementar o modelo
Utilize os CLIs para implementar modelos como serviços web. Os serviços web podem ser implementados localmente ou para um cluster.

Começar com uma implementação local, validar que o modelo e o código de trabalho, em seguida, implementar um cluster para utilização de escala de produção.

Para começar, terá de configurar o ambiente de implementação. O programa de configuração do ambiente é uma tarefa de tempo. Assim que a configuração estiver concluída, pode reutilizar o ambiente para implementações subsequentes. Consulte a secção seguinte para obter mais detalhes.

Quando concluir a configuração de ambiente:
- Lhe for pedido para iniciar sessão no Azure. Para iniciar sessão, utilize um web browser para abrir a página https://aka.ms/devicelogin e introduza o código fornecido para se autenticar.
- Durante o processo de autenticação, é-lhe pedida uma conta autenticar com. Importante: Selecione uma conta que tenha uma subscrição do Azure válida e de permissões suficientes para criar recursos na conta. - quando o início de sessão estiver concluída, são apresentadas informações da sua subscrição e lhe for perguntado se pretende continuar com o conta selecionada.

### <a name="environment-setup"></a>Configuração do ambiente
Para iniciar o processo de configuração, tem de registar o fornecedor de ambiente, introduzindo o seguinte comando:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Implementação de local
Para implementar e testar o seu serviço web no computador local, configure um ambiente local utilizando o seguinte comando:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Implementação de serviços local web exige a instalar Docker no computador local. 
>

O comando de configuração do ambiente local cria os seguintes recursos na sua subscrição:
- Um grupo de recursos (se não for indicado)
- uma conta de armazenamento
- Um registo de contentor do Azure (ACR)
- Informações de aplicação

Após a conclusão da configuração com êxito, defina o ambiente para ser utilizado com o seguinte comando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Implementação de cluster
Utilize a implementação de Cluster para cenários de produção de grande escala. Configura um cluster de ACS com Kubernetes como o orchestrator. O cluster de ACS pode ser ampliado para lidar com maior débito para as chamadas de serviço web.

Para implementar o serviço web para um ambiente de produção, configure primeiro o ambiente utilizando o seguinte comando:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

O comando de configuração do ambiente de cluster cria os seguintes recursos na sua subscrição:
- Um grupo de recursos (se não for indicado)
- uma conta de armazenamento
- Um registo de contentor do Azure (ACR)
- Uma implementação de Kubernetes num cluster do serviço de contentor do Azure (ACS)
- Informações de aplicação

O grupo de recursos, a conta de armazenamento e o ACR são criados rapidamente. A implementação de ACS pode demorar até 20 minutos. 

Após a conclusão da configuração, tem de definir o ambiente para ser utilizado para esta implementação. Utilize o seguinte comando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Depois de criar o ambiente, para implementações subsequentes, apenas terá de utilizar o comando set acima para reutilizá-lo.
>

>[!NOTE] 
>Para criar um ponto final de HTTPS, especifique um certificado SSL ao criar um cluster utilizando o – nome do certificado e – cert pem as opções do programa de configuração do az ml env. Esta ação configura o cluster para servir pedidos em https, protegida com o certificado fornecido. Após a conclusão da configuração, crie um registo de CNAME DNS que aponta para o FQDN do cluster.

### <a name="create-an-account"></a>Criar uma conta
Uma conta é necessária para a implementação de modelos. Precisa de fazer uma vez por conta e pode reutilizar a mesma conta de múltiplas implementações.

Para criar uma nova conta, utilize o seguinte comando:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Para utilizar uma conta existente, utilize o seguinte comando:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Implementar o seu modelo
Agora está pronto para implementar o seu modelo guardado como um serviço web. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Passos Seguintes
Experimente um dos muitos exemplos na galeria.
