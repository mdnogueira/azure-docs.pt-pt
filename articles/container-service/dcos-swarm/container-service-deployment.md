---
title: Implementar um cluster de contentor Docker no Azure | Microsoft Docs
description: "Implemente uma solução Kubernetes, DC/OS ou Docker Swarm no Azure Container Service através do portal do Azure ou de um modelo do Resource Manager."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs
ms.service: container-service
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 0ef256537bf095e2a5d582bd345a9c8dcede2095
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-portal"></a>Implementar uma solução de alojamento de contentor Docker com o portal do Azure



O Serviço de Contentor do Azure fornece uma implementação rápida de soluções de orquestração e de clustering populares e de open source do contentor. Este documento explica-lhe como utilizar o portal do Azure ou um modelo de início rápido do Azure Resource Manager para implementar um cluster do Azure Container Service. 

Também pode implementar um cluster do Azure Container Service com a [CLI do Azure 2.0](container-service-create-acs-cluster-cli.md) ou as APIs do Azure Container Service.

Para obter informações de contexto, veja [Introdução ao Azure Container Service](../container-service-intro.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tiver uma, inscreva-se numa [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Para um cluster maior, considere uma subscrição pay-as-you-go ou outras opções de compra.

    > [!NOTE]
    > A utilização da subscrição do Azure e as [quotas de recursos](../../azure-subscription-service-limits.md), como as quotas de núcleos, podem limitar o tamanho do cluster que implementa. Para pedir um aumento de quota, abra um [pedido de suporte ao cliente online](../../azure-supportability/how-to-create-azure-support-request.md), sem custos.
    >

* **Chave pública RSA SSH**: ao implementar através do portal ou de modelos de início rápido do Azure, tem de indicar a chave pública para autenticação nas máquinas virtuais do Azure Container Service. Para criar chaves RSA Secure Shell (SSH), veja as orientações para [OS X e Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../../virtual-machines/linux/ssh-from-windows.md). 

* **ID e segredo de cliente de principal de serviço** (apenas Kubernetes): para obter mais informações e orientações para criar um principal de serviço do Azure Active Directory, veja [Sobre o principal de serviço para um cluster de Kubernetes](../kubernetes/container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Utilizar o portal do Azure para criar um cluster
1. Inicie sessão no portal do Azure, selecione **Novo** e procure o Azure Marketplace em **Serviço de Contentor do Azure**.

    ![Azure Container Service no Marketplace](./media/container-service-deployment/acs-portal1.png)  <br />

2. Clique em **Azure Container Service** e clique em **Criar**.

3. No painel **Noções Básicas**, introduza as seguintes informações:

    * **Orchestrator**: selecione um dos orquestradores do contentor para implementar no cluster.
        * **DC/OS**: implementa um cluster DC/OS.
        * **Swarm**: implementa um cluster Docker Swarm.
        * **Kubernetes**: implementa um cluster de Kubernetes.
    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: introduza o nome de um novo grupo de recursos para a implementação.
    * **Localização**: selecione uma região do Azure para a implementação do Serviço de Contentor do Azure. Para obter informações de disponibilidade, veja [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).
    
    ![Definições básicas](./media/container-service-deployment/acs-portal3.png)  <br />
    
    Clique em **OK** quando estiver pronto para continuar.

4. No painel **Configuração do modelo de estrutura mestre**, introduza as seguintes definições para o nó principal do Linux ou para os nós do cluster (algumas definições são específicas de cada orquestrador):

    * **Nome DNS do modelo de estrutura mestre**: o prefixo utilizado para criar um nome de domínio completamente qualificado (FQDN) exclusivo para o modelo de estrutura mestre. O formato do FQDN do modelo de estrutura mestre é *prefix*mgmt.*location*.cloudapp.azure.com.
    * **Nome de utilizador**: o nome de utilizador para uma conta em cada uma das máquinas virtuais Linux no cluster.
    * **Chave pública RSA SSH**: adicione a chave pública que vai ser utilizada para autenticação nas máquinas virtuais Linux. É importante que esta chave não contenha quebras de linha e que inclua o prefixo `ssh-rsa`. O sufixo `username@domain` é opcional. Deve ter um aspeto semelhante a **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 
    * **Principal de serviço**: se selecionou o orquestrador Kubernetes, introduza um **ID de cliente de principal de serviço** (também designado por appId) do Azure Active Directory e um **Segredo do cliente de principal de serviço** (palavra-passe). Para obter mais informações, veja [About the service principal for a Kubernetes cluster (Sobre o principal de serviço para um cluster de Kubernetes)](../kubernetes/container-service-kubernetes-service-principal.md).
    * **Contagem de modelos de estrutura mestres**: o número de modelos de estrutura mestres no cluster.
    * **Diagnóstico de VM**: em alguns orquestradores, pode ativar diagnósticos de VM nos modelos de estrutura mestres.

    ![Configuração do modelo de estrutura mestre](./media/container-service-deployment/acs-portal4.png)  <br />

    Clique em **OK** quando estiver pronto para continuar.

5. No painel **Configuração do agente**, introduza as seguintes informações:

    * **Contagem de agentes**: para o Docker Swarm e para o Kubernetes, este valor é o número inicial de agentes no conjunto de dimensionamento dos agentes. Para o DC/OS, é o número inicial de agentes num conjunto de dimensionamento privado. Além disso, é criado um conjunto de dimensionamento público para o DC/OS, que contém um número pré-determinado de agentes. O número de agentes neste conjunto de dimensionamento público é determinado pelo número de modelos de estrutura mestres no cluster: um agente público para um modelo de estrutura mestre e dois agentes públicos para três ou cinco modelos de estrutura mestres.
    * **Tamanho das máquinas virtuais dos agentes**: o tamanho das máquinas virtuais dos agentes.
    * **Sistema operativo**: atualmente, esta definição só está disponível se tiver selecionado o orquestrador Kubernetes. Escolha uma distribuição Linux ou um sistema operativo Windows Server para executar nos agentes. Esta definição determina se o cluster pode executar aplicações de contentor Linux ou Windows. 

        > [!NOTE]
        > O suporte do contentor Windows está em pré-visualização para clusters do Kubernetes. Em clusters DC/OS e Swarm, atualmente apenas são suportados agentes Linux no Azure Container Service.

    * **Credenciais do agente**: se selecionou o sistema operativo Windows, introduza um **Nome de utilizador** e uma **Palavra-passe** de administrador para as VMs do agente. 

    ![Configuração do agente](./media/container-service-deployment/acs-portal5.png)  <br />

    Clique em **OK** quando estiver pronto para continuar.

6. Quando a validação do serviço terminar, clique em **OK**.

    ![Validação](./media/container-service-deployment/acs-portal6.png)  <br />

7. Reveja os termos. Para iniciar o processo de implementação, clique em **Criar**.

    Se optou por afixar a implementação no portal do Azure, o estado da implementação pode ser visto.

    ![Estado da implementação](./media/container-service-deployment/acs-portal8.png)  <br />

A conclusão da implementação demora vários minutos. Depois, o cluster do Azure Container Service está pronto para ser utilizado.


## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Utilizar um modelo de início rápido para criar um cluster
Estão disponíveis modelos de início rápido do Azure para implementar clusters no Azure Container Service. Os modelos de início rápido fornecidos podem ser modificados para incluir configuração do Azure, adicional ou avançada. Para criar um cluster do Azure Container Service com um modelo de início rápido do Azure, precisa de uma subscrição do Azure. Se não tiver uma, inscreva-se numa [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Siga estes passos para implementar um cluster com um modelo e a CLI do Azure 2.0 (veja [installation and setup instructions (Instruções de instalação e configuração)](/cli/azure/install-az-cli2)).

> [!NOTE] 
> Se estiver num sistema Windows, pode utilizar passos semelhantes para implementar um modelo com o Azure PowerShell. Veja os passos mais adiante nesta secção. Também pode implementar modelos através do [portal](../../azure-resource-manager/resource-group-template-deploy-portal.md) ou de outros métodos.

1. Para implementar um cluster DC/OS, Docker Swarm ou Kubernetes, selecione um dos modelos de início rápido disponíveis no GitHub. Segue uma lista parcial. Os modelos do DC/OS e do Swarm são iguais, exceto na seleção do orquestrador predefinido.

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

5. Depois de criar um grupo de recursos, pode criar o cluster com o comando seguinte. O URI do modelo pretendido é especificado com o parâmetro `-TemplateUri`. Quando executa este comando, o PowerShell pede-lhe os valores dos parâmetros da implementação.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Fornecer os parâmetros do modelo
Se estiver familiarizado com o PowerShell, sabe que pode percorrer os parâmetros disponíveis de um cmdlet escrevendo um sinal de subtração (-) e, em seguida, premir a tecla de tabulação. Esta mesma funcionalidade também funciona com os parâmetros que define no modelo. Assim que escrever o nome do modelo, o cmdlet obtém o modelo, analisa os parâmetros e adiciona os parâmetros do modelo ao comando de forma dinâmica. Isto torna mais fácil especificar os valores dos parâmetros do modelo. E, caso se esqueça de um valor de parâmetro necessário, o PowerShell pede-lhe o valor.

Eis o comando completo, com parâmetros incluídos. Forneça os seus próprios valores para os nomes dos recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Passos seguintes
Agora que tem um cluster a funcionar, veja estes documentos para obter os detalhes de ligação e de gestão:

* [Ligar a um cluster do Azure Container Service](../container-service-connect.md)
* [Trabalhar com o Azure Container Service e o DC/OS](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Azure Container Service e o Docker Swarm](container-service-docker-swarm.md)
* [Trabalhar com o Azure Container Service e o Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md)
