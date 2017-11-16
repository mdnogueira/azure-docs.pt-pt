---
title: Cargas de trabalho do docker contentor do Azure batch | Microsoft Docs
description: "Saiba como a execução de aplicações do Docker imagens de contentor do Azure batch."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/15/2017
ms.author: v-dotren
ms.openlocfilehash: fc15b2db051b5ebbf39665b803b22d3a5e4885f9
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="run-docker-container-applications-on-azure-batch"></a>Executar aplicações de contentor de Docker do Azure batch

O Azure Batch permite-lhe executar e dimensionar um grande número de batch de computação de tarefas no Azure. Até agora, as tarefas de lote foram executados diretamente em máquinas virtuais (VMs) num conjunto do Batch, mas agora pode configurar um conjunto do Batch para executar tarefas nos contentores do Docker.

Utilizar contentores fornece uma forma fácil de executar tarefas de lote sem ter de gerir pacotes de aplicações e dependências. Contentores implementar aplicações, como unidades de simples, portáteis, self-sufficient que podem executar uma variedade de ambientes. Por exemplo, pode criar e testar um contentor localmente e carregue a imagem do contentor para um registo no Azure ou noutro local. O modelo de implementação do contentor assegura que o ambiente de tempo de execução da sua aplicação é sempre corretamente instalado e configurado, independentemente de onde alojar a aplicação. Este tutorial mostra como utilizar o SDK .NET do Batch para criar um conjunto de nós de computação que suportam as tarefas em execução do contentor e como executar tarefas de contentor no conjunto.

Este artigo pressupõe familiaridade com conceitos de contentor do Docker e como criar um conjunto do Batch e a tarefa utilizando o SDK .NET. Os fragmentos de código se destinam a ser utilizado numa aplicação de cliente semelhante para o [exemplo DotNetTutorial](batch-dotnet-get-started.md), e são exemplos de código que precisa para suportar aplicações de contentor no Batch.


## <a name="prerequisites"></a>Pré-requisitos

* Versões do SDK: os SDKs do Batch suporte contentor imagens nas seguintes versões:
    * API de REST do batch versão 2017-09-01.6.0
    * SDK .NET do batch versão 8.0.0
    * SDK Python do batch versão 4.0
    * Batch Java SDK versão 3.0
    * SDK Node.js versão 3.0 do batch

* Contas: Na sua conta do Azure, terá de criar uma conta do Batch e, opcionalmente, uma conta do Storage para fins gerais.

* Uma imagem de VM suportada. Contentores só são suportados em conjuntos criados com a configuração de Máquina Virtual a partir de imagens detalhadas na secção seguinte, "suportadas imagens da máquina virtual".

* Se fornecer uma imagem personalizada, a aplicação tem de utilizar a autenticação do Azure Active Directory (Azure AD) para executar cargas de trabalho com base no contentor. Se utilizar uma imagem do Azure Marketplace, não precisa de autenticação do Azure AD autenticação de chave partilhada irá funcionar. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Imagens de máquina virtual suportadas

Tem de fornecer um Windows ou nós de computação do Linux imagem para criar um conjunto de VM.

### <a name="windows-images"></a>Imagens do Windows

Para cargas de trabalho do Windows contentor, atualmente, o Batch suporta imagens personalizadas que criar a partir de VMs do Docker em execução no Windows ou pode utilizar o Centro de dados do Windows Server 2016 com a imagem de contentores do Azure Marketplace. Esta imagem é compatível com o `batch.node.windows amd64` ID do SKU do agente de nó O tipo de contentor suportada está limitado ao Docker.

### <a name="linux-images"></a>Imagens de Linux

Para cargas de trabalho do Linux contentor Batch atualmente suporta imagens apenas personalizadas que criar a partir de VMs em execução Docker as distribuições de Linux seguintes: Ubuntu 16.04 LTS ou CentOS 7.3. Se optar por fornecer a sua imagem personalizada do Linux, consulte as instruções no [utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais](batch-custom-images.md).

Pode utilizar [Docker Comunidade Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Se pretender tirar partido do desempenho GPU dos tamanhos NC do Azure ou NV VM, terá de instalar controladores NVIDIA na imagem. Além disso, tem de instalar e executar o utilitário de motor de Docker para NVIDIA GPUs, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Para aceder à rede Azure RDMA, utilize VMs os tamanhos seguintes: A8, A9, H16r, H16mr ou NC24r. Os controladores RDMA necessários estão instalados nas imagens do CentOS 7.3 HPC e Ubuntu 16.04 LTS no Azure Marketplace. Para executar cargas de trabalho MPI, poderá ser necessária configuração adicional. Consulte [utilização com capacidade RDMA ou preparados para a GPU instâncias num conjunto do Batch](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Limitações

* O batch fornece suporte RDMA apenas para contentores em execução em conjuntos do Linux.


## <a name="authenticate-using-azure-active-directory"></a>Autenticar com o Azure Active Directory

Se utilizar uma imagem VM personalizada para criar o conjunto do Batch, a aplicação cliente tem de ser autenticado utilizando a autenticação integrada do Azure AD (autenticação de chave partilhada não funciona). Antes de executar a aplicação, certifique-se de que registá-lo no Azure AD para estabelecer uma identidade para a mesma e especificar as respetivas permissões para outras aplicações.

Além disso, quando utilizar uma imagem VM personalizada, terá de conceder controlo de acesso IAM à aplicação aceder à imagem da VM. No portal do Azure, abra **todos os recursos**, selecione a imagem do contentor e para o **(IAM) do controlo de acesso** secção do painel de imagem e clique em **adicionar**. No **adicionar permissões** painel, especifique um **função**, na **atribuir acesso**, selecione **utilizador do Azure AD, grupo ou aplicação**, em seguida, no  **Selecione** introduza o nome da aplicação.

Na sua aplicação, transmita um token de autenticação do Azure AD quando cria o cliente do Batch utilizar [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), conforme descrito nas [soluções de serviço de Batch de autenticar com o Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Referência a uma imagem de VM para a criação de conjunto

No código da aplicação, fornecem uma referência à imagem de VM para utilizar na criação de nós de computação do conjunto. Fazê-lo através da criação de um [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) objeto. Pode especificar a imagem a utilizar das seguintes formas:

* Se estiver a utilizar uma imagem personalizada, introduza um identificador de recurso do Azure Resource Manager para a imagem de máquina virtual. O identificador de imagem tem um formato de caminho, conforme mostrado no exemplo seguinte:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Para obter este ID de imagem do portal do Azure, abra **todos os recursos**, selecione a imagem personalizada e para o **descrição geral** secção do painel de imagem, copie o caminho no **ID de recurso**.

* Se estiver a utilizar um [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) de imagem, forneça um grupo de parâmetros que descrevem a imagem: o tipo de oferta, publicador, SKU e versão da imagem, conforme indicado em [lista de imagens da máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Configuração do contentor para o conjunto do Batch

O conjunto do Batch é uma coleção de nós de computação em que o Batch executa tarefas numa tarefa. Quando criar o agrupamento, fornecê-lo com a configuração de VM para os nós de computação. O [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objeto contém uma referência para o [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) objeto. Para ativar as cargas de trabalho do contentor no conjunto, especifique o `ContainerConfiguration` definições. A configuração de VM também é onde pode especificar a referência da imagem e o agente de nó da imagem ID do SKU, conforme mostrado nos exemplos seguintes.

Existem várias opções para a criação de conjunto. Pode criar um conjunto com ou sem imagens prefetched contentor. 

O processo de extração (ou obtenção prévia) permite-lhe pré-carregar imagens contentor a partir do Hub de Docker ou outro registo de contentor na Internet. A vantagem de prefetching imagens do contentor é que quando tarefas primeiro iniciar a execução não têm de aguardar que a imagem do contentor transferir. A configuração do contentor obtém as imagens de contentor para as VMs quando o conjunto for criado. Tarefas que são executadas no conjunto, em seguida, podem referenciar a lista de imagens de contentor e opções de execução do contentor.



### <a name="pool-without-prefetched-container-images"></a>Conjunto sem imagens prefetched contentor

Para configurar o conjunto sem imagens prefetched contentor, utilize um `ContainerConfiguration` conforme mostrado no exemplo seguinte. Este e os exemplos seguintes partem do princípio de que está a utilizar uma imagem personalizada do Ubuntu 16.04 LTS com o motor de Docker instalado.

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration(
    type: "Docker");

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```

### <a name="prefetch-images-for-container-configuration"></a>Prefetch imagens para a configuração do contentor

Para prefetch imagens do contentor no conjunto, adicionar a lista de imagens de contentor (`containerImageNames`) para a configuração do contentor e conceder a imagem de lista um nome. O exemplo seguinte parte do princípio de que está a utilizar uma imagem personalizada do Ubuntu 16.04 LTS, prefetch uma imagem de TensorFlow de [Docker Hub](https://hub.docker.com), e iniciar TensorFlow numa tarefa de início.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    type: "Docker",
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prefetch imagens a partir de um registo de contentor privada

Também pode prefetch imagens contentor através da autenticação para um servidor de registo do contentor privada. O exemplo seguinte assume que está a utilizar uma imagem personalizada do Ubuntu 16.04 LTS e são prefetching uma imagem de TensorFlow privada de um registo de contentor do Azure privado.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    type: "Docker",
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Definições de contentor para a tarefa

Quando configurar tarefas para executar em nós de computação, tem de especificar definições específicas do contentor, tais como executar as opções, imagens a utilizar e registo de tarefas.

Utilize a propriedade de ContainerSettings as classes de tarefas para configurar definições específicas do contentor. Estas definições são definidas pelo [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) classe.

Se executar tarefas nas imagens de contentor, a [tarefas nuvem](/dotnet/api/microsoft.azure.batch.cloudtask) e [tarefa do Gestor de tarefas](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) exigir definições de contentor. No entanto, o [iniciar a tarefa](/dotnet/api/microsoft.azure.batch.starttask), [tarefa de preparação](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), e [tarefa de libertação](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) não necessitam de definições de contentor (ou seja, podem ser executados no contexto de um contentor ou diretamente no nó).

Quando configura as definições de contentor, todos os diretórios em modo recursivo abaixo o `AZ_BATCH_NODE_ROOT_DIR` (a raiz de diretórios do Azure Batch no nó) estão mapeadas para o contentor, ambiente de tarefas todas as variáveis são mapeadas para o contentor e a linha de comandos de tarefas é executado no contentor.

O exemplo de código no [obtenção prévia imagens para a configuração do contentor](#prefetch-images-for-container-configuration) mostrou como especificar uma configuração de contentor para uma tarefa de início. Exemplo de código seguinte mostra como especificar a configuração do contentor para uma tarefa de cloud:

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Passos seguintes

* Para uma descrição geral aprofundada do Batch, consulte [paralelo em grande escala desenvolver soluções com o Batch de computação](batch-api-basics.md).

* Para obter mais informações sobre como instalar e utilizar o Docker CE no Linux, consulte o [Docker](https://docs.docker.com/engine/installation/) documentação.

* Para obter mais informações sobre como utilizar imagens personalizadas, consulte [utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais ](batch-custom-images.md).
