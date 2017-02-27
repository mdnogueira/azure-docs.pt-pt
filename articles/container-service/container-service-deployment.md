---
title: Implementar um cluster de contentor Docker no Azure | Microsoft Docs
description: Utilizar o portal do Azure ou um modelo do Azure Resource Manager para implementar um cluster do Azure Container Service.
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: b9be92498f9daf1d2f964cc689bacb2358b237be


---
# <a name="deploy-an-azure-container-service-cluster"></a>Implementar um cluster do Serviço de Contentor do Azure



O Serviço de Contentor do Azure fornece uma implementação rápida de soluções de orquestração e de clustering populares e de open source do contentor. Este documento explica-lhe como utilizar o portal do Azure ou um modelo de início rápido do Azure Resource Manager para implementar um cluster do Azure Container Service. 

Também pode implementar um cluster do Azure Container Service com a [CLI do Azure 2.0](container-service-create-acs-cluster-cli.md) ou as APIs do Azure Container Service.



## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tiver uma, inscreva-se numa [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

* **Chave pública RSA SSH**: ao implementar através do portal ou de modelos de início rápido do Azure, tem de indicar a chave pública para autenticação nas máquinas virtuais do Azure Container Service. Para criar chaves RSA Secure Shell (SSH), veja as orientações para [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **ID e segredo de cliente de principal de serviço** (apenas Kubernetes): para obter mais informações e orientações para criar um principal de serviço, veja [Sobre o principal de serviço para um cluster de Kubernetes](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Utilizar o portal do Azure para criar um cluster
1. Inicie sessão no portal do Azure, selecione **Novo** e procure o Azure Marketplace em **Serviço de Contentor do Azure**.

    ![Azure Container Service no Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Selecione **Serviço de Contentor do Azure** e clique em **Criar**.

    ![Criar um serviço de contentor](media/container-service-deployment/acs-portal2.png)  <br />

3. Introduza as seguintes informações:

    * **Nome de utilizador**: o nome de utilizador de uma conta em cada uma das máquinas virtuais e nos conjuntos de dimensionamento de máquinas virtuais no cluster do Azure Container Service.
    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo. Como melhor prática, utilize um grupo de recursos novo para cada implementação.
    * **Localização**: selecione uma região do Azure para a implementação do Serviço de Contentor do Azure.
    * **Chave pública RSA SSH**: adicione a chave pública que vai ser utilizada para autenticação nas máquinas virtuais do Azure Container Service. É importante que esta chave não contenha quebras de linha e que inclua o prefixo `ssh-rsa`. O sufixo `username@domain` é opcional. Deve ter um aspeto semelhante a **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 

4. Clique em **OK** quando estiver pronto para continuar.

    ![Definições básicas](media/container-service-deployment/acs-portal3.png)  <br />

5. No painel **Configuração do framework**, selecione uma **Configuração do Orchestrator**. As opções incluem:

  * **DC/OS**: implementa um cluster DC/OS.
  * **Swarm**: implementa um cluster Docker Swarm.
  * **Kubernetes**: implementa um cluster de Kubernetes.


6. Clique em **OK** quando estiver pronto para continuar.

    ![Escolher um orquestrador](media/container-service-deployment/acs-portal4-new.png)  <br />

7. Se o **Kubernetes** estiver selecionado na lista pendente, tem de introduzir um ID de cliente do principal de serviço (também chamado como appId) e o segredo do cliente do principal de serviço (palavra-passe). Para obter mais informações, veja [About the service principal for a Kubernetes cluster (Sobre o principal de serviço para um cluster de Kubernetes)](container-service-kubernetes-service-principal.md).

    ![Introduzir o principal de serviço do Kubernetes](media/container-service-deployment/acs-portal10.png)  <br />

7. No painel de definições do **Azure Container Service**, introduza as informações seguintes:

    * **Contagem de modelos de estrutura mestres**: o número de modelos de estrutura mestres no cluster.
    * **Contagem de agentes**: para o Docker Swarm e para o Kubernetes, este valor é o número inicial de agentes no conjunto de dimensionamento dos agentes. Para o DC/OS, é o número inicial de agentes num conjunto de dimensionamento privado. Além disso, é criado um conjunto de dimensionamento público para o DC/OS, que contém um número pré-determinado de agentes. O número de agentes neste conjunto de dimensionamento público é determinado pelo número de modelos de estrutura mestres criados no cluster: um agente público para um modelo de estrutura mestre e dois agentes públicos para três ou cinco modelos de estrutura mestres.
    * **Tamanho das máquinas virtuais dos agentes**: o tamanho das máquinas virtuais dos agentes.
    * **Prefixo DNS**: um nome exclusivo a nível mundial que é utilizado para o prefixo das partes de chave dos nomes de domínio completamente qualificados do serviço.
    * **Diagnóstico de VM**: em alguns orquestradores, pode optar por ativar os diagnósticos de VM.

8. Clique em **OK** quando estiver pronto para continuar.

    ![Definições do Container Service](media/container-service-deployment/acs-portal5.png)  <br />

9. Clique em **OK** após a conclusão da validação de serviço.

    ![Validação](media/container-service-deployment/acs-portal6.png)  <br />

10. Reveja os termos. Para começar o processo de implementação, clique em **Comprar**.

    ![Comprar](media/container-service-deployment/acs-portal7.png)  <br />

    Se optou por afixar a implementação no portal do Azure, o estado da implementação pode ser visto.

    ![Estado da implementação](media/container-service-deployment/acs-portal8.png)  <br />

A conclusão da implementação demora vários minutos. Depois, o cluster do Azure Container Service está pronto para ser utilizado.



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Utilizar um modelo de início rápido para criar um cluster
Estão disponíveis modelos de início rápido do Azure para implementar clusters no Azure Container Service. Os modelos de início rápido fornecidos podem ser modificados para incluir configuração do Azure, adicional ou avançada. Para criar um cluster do Azure Container Service com um modelo de início rápido do Azure, precisa de uma subscrição do Azure. Se não tiver uma, inscreva-se numa [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Siga estes passos para implementar um cluster com um modelo e a CLI do Azure 2.0 (veja [installation and setup instructions (Instruções de instalação e configuração)](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> Se estiver num sistema Windows, pode utilizar passos semelhantes para implementar um modelo com o Azure PowerShell. Veja os passos mais adiante nesta secção. Também pode implementar modelos através do [portal](../azure-resource-manager/resource-group-template-deploy-portal.md) ou de outros métodos.

1. Para implementar um cluster DC/OS, Docker Swarm ou Kubernetes, selecione um dos modelos de início rápido disponíveis no GitHub. Segue uma lista parcial. Tenha em conta que os modelos do DC/OS e do Swarm são iguais, com exceção da seleção do orquestrador predefinido.

    * [Modelo do DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modelo do Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Inicie sessão na sua conta do Azure (`az login`) e confirme que a CLI do Azure está ligada à sua subscrição do Azure. Pode utilizar o comando seguinte para ver a subscrição predefinida:

    ```azurecli
    az account show
    ```
    
    Se tiver mais de uma subscrição e for necessário definir uma subscrição predefinida diferente, execute `az account set --subscription` e especifique o ID ou o nome da subscrição.

3. Como melhor prática, utilize um grupo de recursos novo para a implementação. Para criar um grupo de recursos, utilize o comando `az group create` e especifique um nome e uma localização para o mesmo: 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Crie um ficheiro JSON que contenha os parâmetros do modelo necessários. Transfira o ficheiro de parâmetros com o nome `azuredeploy.parameters.json` que acompanha o modelo `azuredeploy.json` do Azure Container Service no GitHub. Introduza os valores dos parâmetros necessários para o cluster. 

    Por exemplo, para utilizar o [modelo DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos), indique valores de parâmetros para `dnsNamePrefix` e `sshRSAPublicKey`. Veja as descrições em `azuredeploy.json` e as opções relativas a outros parâmetros.  
 

5. Crie um cluster do Container Service ao transmitir o ficheiro de parâmetros da implementação com o comando seguinte, em que:

    * **RESOURCE_GROUP** é o nome do grupo de recursos que criou no passo anterior.
    * **DEPLOYMENT_NAME** (opcional) é o nome que vai dar à implementação.
    * **TEMPLATE_URI** é a localização do ficheiro de implementação `azuredeploy.json`. Este URI tem de ser um ficheiro Raw, não um ponteiro para a IU do GitHub. Para encontrar o URI, selecione o ficheiro `azuredeploy.json` no GitHub e clique no botão **Raw**.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    Também pode indicar os parâmetros como cadeias formatadas como JSON na linha de comandos. Utilize um comando semelhante ao seguinte:

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > A conclusão da implementação demora vários minutos.
    > 

### <a name="equivalent-powershell-commands"></a>Comandos do PowerShell equivalentes
Também pode implementar um modelo de cluster do Azure Container Service com o PowerShell. Este documento baseia-se na versão 1.0 do [módulo do Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

1. Para implementar um cluster DC/OS, Docker Swarm ou Kubernetes, selecione um dos modelos de início rápido disponíveis no GitHub. Segue uma lista parcial. Tenha em conta que os modelos do DC/OS e do Swarm são iguais, com exceção da seleção do orquestrador predefinido.

    * [Modelo do DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modelo do Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Antes de criar um cluster na subscrição do Azure, certifique-se de que a sua sessão do PowerShell iniciou sessão no Azure. Pode fazer isto com o comando `Get-AzureRMSubscription`:

    ```powershell
    Get-AzureRmSubscription
    ```

3. Se precisar de iniciar sessão no Azure, utilize o comando `Login-AzureRMAccount`:

    ```powershell
    Login-AzureRmAccount
    ```

4. Como melhor prática, utilize um grupo de recursos novo para a implementação. Para criar um grupo de recursos, utilize o comando `New-AzureRmResourceGroup` e especifique um nome e uma região de destino para o mesmo:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Depois de criar um grupo de recursos, pode criar o cluster com o comando seguinte. O URI do modelo pretendido será especificado para o parâmetro `-TemplateUri`. Quando executa este comando, o PowerShell pede-lhe os valores dos parâmetros da implementação.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Fornecer os parâmetros do modelo
Se estiver familiarizado com o PowerShell, sabe que pode percorrer os parâmetros disponíveis de um cmdlet escrevendo um sinal de subtração (-) e, em seguida, premir a tecla de tabulação. Esta mesma funcionalidade também funciona com os parâmetros que define no modelo. Assim que escrever o nome do modelo, o cmdlet obtém o modelo, analisa os parâmetros e adiciona os parâmetros do modelo ao comando de forma dinâmica. Isto torna muito mais fácil especificar os valores dos parâmetros do modelo. E, caso se esqueça de um valor de parâmetro necessário, o PowerShell pede-lhe o valor.

Veja abaixo o comando completo, com parâmetros incluídos. Pode fornecer os seus próprios valores para os nomes dos recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Passos seguintes
Agora que tem um cluster a funcionar, veja estes documentos para obter os detalhes de ligação e de gestão:

* [Ligar a um cluster do Azure Container Service](container-service-connect.md)
* [Trabalhar com o Azure Container Service e o DC/OS](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Azure Container Service e o Docker Swarm](container-service-docker-swarm.md)
* [Trabalhar com o Azure Container Service e o Kubernetes](container-service-kubernetes-walkthrough.md)



<!--HONumber=Feb17_HO4-->


