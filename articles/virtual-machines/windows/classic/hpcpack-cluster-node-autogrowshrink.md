---
title: "Nós de cluster de dimensionamento automático HPC Pack | Microsoft Docs"
description: "Automaticamente aumentar e diminuir o número de nós de computação de cluster HPC Pack no Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: 
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 0c8a5aacd19d83b26cfeb3750d57dd783687f1c4
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automaticamente aumentar e diminuir os recursos de cluster HPC Pack no Azure, de acordo com a carga de trabalho de cluster
Se implementar nós do Azure "rajada" no seu cluster HPC Pack ou criar um cluster HPC Pack em VMs do Azure, poderá pretender uma forma de automaticamente aumentar ou diminuir os recursos de cluster como nós ou núcleos, de acordo com a carga de trabalho no cluster. Dimensionar os recursos de cluster desta forma permite-lhe utilizar os recursos do Azure de forma mais eficiente e controlar os custos.

Este artigo mostra-lhe duas formas de HPC Pack fornece para dimensionar automaticamente recursos de computação

* A propriedade de cluster HPC Pack **AutoGrowShrink**

* O **AzureAutoGrowShrink.ps1** script do HPC PowerShell

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Atualmente só automaticamente pode aumentar e diminuir a nós de computação de HPC Pack que estejam a executar um sistema operativo Windows Server.


## <a name="set-the-autogrowshrink-cluster-property"></a>Defina a propriedade de cluster AutoGrowShrink
### <a name="prerequisites"></a>Pré-requisitos

* **HPC Pack 2012 R2 Update 2 ou posterior cluster** -nó principal do cluster pode ser implementado no local ou numa VM do Azure. Consulte [configurar um cluster de híbrido com o HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar com um nó principal no local e nós do Azure "rajada". Consulte o [script de implementação de HPC Pack IaaS](hpcpack-cluster-powershell-script.md) implementar rapidamente um cluster HPC Pack em VMs do Azure.

* **Para um cluster com um nó principal no Azure (modelo de implementação do Resource Manager)** - a partir de HPC Pack 2016, autenticação de certificado de uma aplicação do Azure Active Directory é utilizada para a crescer automaticamente e reduzir o cluster VMs implementadas utilizando O Azure Resource Manager. Configure um certificado da seguinte forma:

  1. Após a implementação de cluster, estabelecer ligação ao ambiente de trabalho remoto para um nó principal.

  2. Carregue o certificado (formato PFX com chave privada) para cada nó principal e instalar certificados: \LocalMachine\My e Cert: \LocalMachine\Root.

  3. Inicie o Azure PowerShell como administrador e execute os seguintes comandos num nó principal:

    ```powershell
        cd $env:CCP_HOME\bin

        Login-AzureRmAccount
    ```
        
    Se a sua conta está em mais do que um inquilino do Azure Active Directory ou a subscrição do Azure, pode executar o seguinte comando para selecionar o inquilino correto e subscrição:
  
    ```powershell
        Login-AzureRMAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Execute o seguinte comando para ver o inquilino atualmente selecionado e de subscrição:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Execute o seguinte script

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    onde

    **DisplayName** -nome de apresentação da aplicação Active Directory do Azure. Se a aplicação não existe, é criada no Azure Active Directory.

    **Home page** -a home page da aplicação. Pode configurar um URL fictício, como no exemplo anterior.

    **IdentifierUri** -identificador da aplicação. Pode configurar um URL fictício, como no exemplo anterior.

    **CertificateThumbprint** -Thumbprint do certificado que instalou no nó principal no passo 1.

    **TenantId** -ID do seu Azure Active Directory de inquilino. Pode obter o ID do inquilino a partir do portal do Azure Active Directory **propriedades** página.

    Para obter mais detalhes sobre **ConfigARMAutoGrowShrinkCert.ps1**, execute `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **Para um cluster com um nó principal no Azure (modelo de implementação clássica)** - se utilizar o script de implementação do HPC Pack IaaS para criar o cluster no modelo de implementação clássica, ative o **AutoGrowShrink** propriedade do cluster definir a opção de AutoGrowShrink no ficheiro de configuração de cluster. Para obter mais informações, consulte a documentação que acompanha o [transferências de script](https://www.microsoft.com/download/details.aspx?id=44949).

    Em alternativa, ativar o **AutoGrowShrink** propriedade de cluster, depois de implementar o cluster utilizando comandos do HPC PowerShell descritos na secção seguinte. Para se preparar para tal, primeiro conclua os seguintes passos:

  1. Configure um certificado de gestão do Azure no nó principal e na subscrição do Azure. Para uma implementação de teste, pode utilizar o certificado autoassinado predefinido Microsoft HPC Azure que instala o pacote HPC no nó principal e, em seguida, carregue esse certificado para a sua subscrição do Azure. Para opções e passos, consulte o [orientações de biblioteca do TechNet](https://technet.microsoft.com/library/gg481759.aspx).

  2. Executar **regedit** no nó principal, aceda a HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo e adicione um valor de cadeia. Definir o nome do valor para "ThumbPrint" e o valor de dados para o thumbprint do certificado no passo 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Comandos do HPC PowerShell para definir a propriedade AutoGrowShrink
Seguem-se os comandos do HPC PowerShell de exemplo para definir **AutoGrowShrink** e para otimizar o seu comportamento com parâmetros adicionais. Consulte [AutoGrowShrink parâmetros](#AutoGrowShrink-parameters) posteriormente neste artigo para obter a lista completa das definições.

Para executar estes comandos, inicie o HPC PowerShell no nó principal do cluster como um administrador.

**Para ativar a propriedade AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Para desativar a propriedade AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Para alterar o intervalo que pode aumentar em minutos**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Para alterar o intervalo de operação de encolhimento em minutos**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Para ver a configuração atual do AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Para excluir grupos de nó da AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Este parâmetro é suportado a partir de 2016 do HPC Pack
>

### <a name="autogrowshrink-parameters"></a>Parâmetros de AutoGrowShrink
Seguem-se AutoGrowShrink parâmetros que pode modificar utilizando o **conjunto HpcClusterProperty** comando.

* **EnableGrowShrink** -comutador para ativar ou desativar o **AutoGrowShrink** propriedade.
* **ParamSweepTasksPerCore** -número de tarefas de varrimento paramétrico a crescer um núcleo. A predefinição é a crescer um núcleo por tarefa.

  > [!NOTE]
  > Alterações de HPC Pack QFE KB3134307 **ParamSweepTasksPerCore** para **TasksPerResourceUnit**. -Baseia-se no tipo de recurso de tarefa e pode ser nó, socket ou core.
  >
  >
* **GrowThreshold** -limiar de tarefas na fila para acionar o aumento automático. A predefinição é 1, o que significa que se existirem 1 ou mais tarefas num Estado em fila, aumentar automaticamente nós.
* **GrowInterval** -intervalo em minutos para acionar o aumento automático. O intervalo predefinido é 5 minutos.
* **ShrinkInterval** -intervalo em minutos para acionar a reduzir automática. O intervalo predefinido é de 5 minutos. |
* **ShrinkIdleTimes** -número de verificações contínuas para reduzir para indicar os nós está inativo. A predefinição é 3 vezes. Por exemplo, se o **ShrinkInterval** é de 5 minutos, HPC Pack verifica se o nó está inativo a cada 5 minutos. Se os nós estiverem num estado inativo após 3 contínua verifica (15 minutos), pacote HPC diminui nesse nó.
* **ExtraNodesGrowRatio** -percentagem adicional de nós a crescer para tarefas de Interface de passagem de mensagens (MPI). O valor predefinido é 1, o que significa que o pacote HPC cresce nós % 1 para tarefas MPI.
* **GrowByMin** -comutador para indicar se a política de aumento automático é baseada nos recursos mínimo necessários para a tarefa. A predefinição é false, o que significa que o pacote HPC cresce nós para as tarefas com base no máximos recursos necessários para as tarefas.
* **SoaJobGrowThreshold** -processo de crescimento de limiar de pedidos recebidos de SOA para acionar o automático. O valor predefinido é 50000.

  > [!NOTE]
  > Este parâmetro é suportado a partir de HPC Pack 2012 R2 Update 3.
  >
  >
* **SoaRequestsPerCore** -número de SOA recebido pedidos a crescer um núcleo. O valor predefinido é de 20000.

  > [!NOTE]
  > Este parâmetro é suportado a partir de HPC Pack 2012 R2 Update 3.
  >
* **ExcludeNodeGroups** – nós nos grupos de nó especificado não automaticamente aumentar e diminuir.
  
  > [!NOTE]
  > Este parâmetro é suportado a partir de HPC Pack 2016.
  >

### <a name="mpi-example"></a>Exemplo MPI
Por predefinição HPC Pack crescimentos de 1% nós adicionais para os trabalhos MPI (**ExtraNodesGrowRatio** está definida como 1). O motivo é que MPI pode necessitar de vários nós e a tarefa só pode ser executada quando todos os nós estão prontos. Quando Azure é iniciado nós, ocasionalmente, um nó pode necessitar de mais tempo para iniciar a outras pessoas, fazendo com que outros nós Inativos enquanto aguardam que esse nó preparar o. Incrementando nós adicionais, HPC Pack reduz o tempo de espera este recurso e, potencialmente, guarda os custos. Para aumentar a percentagem de nós adicionais para os trabalhos MPI (por exemplo, para 10%), execute um comando semelhante ao

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Exemplo SOA
Por predefinição, **SoaJobGrowThreshold** está definido como 50000 e **SoaRequestsPerCore** está definido como 20000. Se submeter uma tarefa SOA com 70000 pedidos, existe uma tarefa em fila e os pedidos recebidos são 70000. Neste caso HPC Pack cresce 1 núcleo para a tarefa em fila e para pedidos recebidos, o crescimentos (70000 50000) / core 20000 = 1, no total crescimentos de 2 núcleos para esta tarefa SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Execute o script de AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>Pré-requisitos

* **HPC Pack 2012 R2 Update 1 ou posterior cluster** - **AzureAutoGrowShrink.ps1** script está instalado na pasta de reciclagem a % CCP_HOME %. Nó principal do cluster pode ser implementado no local ou numa VM do Azure. Consulte [configurar um cluster de híbrido com o HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar com um nó principal no local e nós do Azure "rajada". Consulte o [script de implementação de HPC Pack IaaS](hpcpack-cluster-powershell-script.md) rapidamente implementar um cluster HPC Pack em VMs do Azure ou utilizar um [modelo de início rápido do Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **O Azure PowerShell 1.4.0** -o script depende atualmente esta versão específica do Azure PowerShell.
* **Para um cluster com o Azure burst nós** -execute o script num computador cliente onde o HPC Pack está instalado, ou no nó principal. Se em execução num computador cliente, certifique-se de que definiu a variável $env: CCP_SCHEDULER para apontar para o nó principal. Os nós do Azure "rajada" tem de ser adicionados ao cluster, mas poderão estar num Estado implementado em não.
* **Para um cluster implementado em VMs do Azure (modelo de implementação do Resource Manager)** -num cluster de VMs do Azure implementadas no modelo de implementação Resource Manager, o script suporta dois métodos de autenticação do Azure: iniciar sessão na sua conta do Azure para executar o Sempre que o script (executando `Login-AzureRmAccount`, ou configurar um principal de serviço para autenticar com um certificado. Pacote HPC fornece o script **ConfigARMAutoGrowShrinkCert.ps** para criar um principal de serviço com o certificado. O script cria uma aplicação do Azure Active Directory (Azure AD) e um principal de serviço e atribui a função de contribuinte ao principal de serviço. Para executar o script, inicie o Azure PowerShell como administrador e execute os seguintes comandos:

    ```powershell
    cd $env:CCP_HOME\bin

    Login-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Para obter mais detalhes sobre **ConfigARMAutoGrowShrinkCert.ps1**, execute `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`,

* **Para um cluster implementado em VMs do Azure (modelo de implementação clássica)** -execute o script no nó principal do VM, porque depende de **HpcIaaSNode.ps1 início** e **Stop-HpcIaaSNode.ps1** scripts que são instalados não existe. Esses scripts adicionalmente requerem um certificado de gestão do Azure ou publicar o ficheiro de definições (consulte [cluster de nós de computação de gerir num pacote HPC no Azure](hpcpack-cluster-node-manage.md)). Certifique-se de que todas as VMs que precisa de nó de computação já são adicionados ao cluster. Estes podem estar no estado parado.



### <a name="syntax"></a>Sintaxe
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Parâmetros
* **NodeTemplates** -os nomes dos modelos de nó para definir o âmbito para os nós aumentar e diminuir. Se não for especificado (o valor predefinido é @()), todos os nós a **AzureNodes** grupo de nós estão no âmbito quando **NodeType** tem um valor de AzureNodes e todos os nós no **ComputeNodes**grupo de nós estão no âmbito quando **NodeType** tem um valor de ComputeNodes.
* **JobTemplates** -os nomes dos modelos da tarefa para definir o âmbito para os nós a crescer.
* **NodeType** -o tipo de nó para aumentar e diminuir. Os valores suportados são:

  * **AzureNodes** – para nós do Azure PaaS (rajada) no local ou cluster do IaaS do Azure.
  * **ComputeNodes** – para o nó de computação VMs apenas um cluster do IaaS do Azure.

* **NumOfQueuedJobsPerNodeToGrow** -número de tarefas em fila necessários para aumentar um nó.
* **NumOfQueuedJobsToGrowThreshold** -o número de limiar de tarefas em fila para iniciar o processo de medida.
* **NumOfActiveQueuedTasksPerNodeToGrow** - o número de ativos em fila tarefas necessárias para aumentar um nó. Se **NumOfQueuedJobsPerNodeToGrow** for especificado com um valor maior que 0, este parâmetro será ignorado.
* **NumOfActiveQueuedTasksToGrowThreshold** -o número de limiar de tarefas na fila do Active Directory para iniciar o processo de medida.
* **NumOfInitialNodesToGrow** -o número mínimo inicial de nós a crescer se todos os nós no âmbito são **implementadas não** ou **parado (Deallocated)**.
* **GrowCheckIntervalMins** -o intervalo em minutos, entre as verificações a crescer.
* **ShrinkCheckIntervalMins** -o intervalo em minutos, entre as verificações para reduzir.
* **ShrinkCheckIdleTimes** -verificações de reduzir o número de contínua (separados por **ShrinkCheckIntervalMins**) para indicar os nós estão inativos.
* **UseLastConfigurations** -as configurações anteriores guardadas no ficheiro de argumento.
* **ArgFile**-o nome do ficheiro argumento utilizado para guardar e atualizar as configurações para executar o script.
* **LogFilePrefix** -o nome de prefixo do ficheiro de registo. Pode especificar um caminho. Por predefinição, o registo é escrito para o atual diretório de trabalho.

### <a name="example-1"></a>Exemplo 1
O exemplo a seguir configura os nós do Azure rajada implementados com o modelo de AzureNode predefinido para aumentar e diminuir automaticamente. Se todos os nós estão em inicialmente o **implementadas não** Estado, pelo menos 3 nós são iniciados. Se o número de tarefas em fila exceder 8, o script inicia nós até que o respetivo número excede o rácio de tarefas em fila a **NumOfQueuedJobsPerNodeToGrow**. Se não for encontrado um nó para estar inativo em 3 vezes consecutivas de inatividade, está parado.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exemplo 2
O exemplo a seguir configura o nó de computação do Azure VMs implementadas com o modelo de ComputeNode predefinido para aumentar e diminuir a automaticamente.
As tarefas configuradas para o modelo de tarefa predefinida de definem o âmbito da carga de trabalho no cluster. Se todos os nós inicialmente são parados, pelo menos 5 nós são iniciados. Se o número de tarefas em fila Active Directory exceder 15, o script inicia nós até que o respetivo número excede o rácio do Active Directory tarefas em fila para **NumOfActiveQueuedTasksPerNodeToGrow**. Se não for encontrado um nó para estar inativo em 10 vezes consecutivas de inatividade, está parado.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
