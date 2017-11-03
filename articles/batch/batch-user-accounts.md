---
title: Executar tarefas em contas de utilizador no Azure Batch | Microsoft Docs
description: "Configurar contas de utilizador para as tarefas em execução no Azure Batch"
services: batch
author: tamram
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.openlocfilehash: d408c0565c0ed81fc97cc2b3976a4fc233e31302
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Executar tarefas em contas de utilizador no Batch

Uma tarefa no Azure Batch é sempre executado sob uma conta de utilizador. Por predefinição, as tarefas executadas em contas de utilizador padrão, sem permissões de administrador. Estas definições de conta de utilizador predefinidas são normalmente suficientes. No entanto, para certos cenários, é útil poder configurar a conta de utilizador onde pretende que uma tarefa para ser executada. Este artigo aborda os tipos de contas de utilizador e a forma como pode configurá-las para o seu cenário.

## <a name="types-of-user-accounts"></a>Tipos de contas de utilizador

O Azure Batch fornece dois tipos de contas de utilizador para as tarefas em execução:

- **Contas de utilizador automaticamente.** Contas de utilizador automática são contas de utilizador incorporado que são criadas automaticamente pelo serviço Batch. Por predefinição, as tarefas executadas sob uma conta de utilizador automaticamente. Pode configurar a especificação de utilizador automática para uma tarefa indicar que conta de utilizador automática deve executar uma tarefa. A especificação de auto-utilizador permite-lhe especificar o nível de elevação e o âmbito da conta de utilizador automática que irá executar a tarefa. 

- **Uma conta de utilizador nomeado.** Pode especificar uma ou mais contas de utilizador nomeado para um conjunto quando criar o conjunto. Cada conta de utilizador é criada em cada nó do conjunto. Para além do nome da conta, especifique o utilizador conta palavra-passe, elevação nível e, para os conjuntos de Linux, a chave SSH privada. Quando adiciona uma tarefa, pode especificar a conta de utilizador nomeado sob as quais essa tarefa deve ser executada.

> [!IMPORTANT] 
> A versão do serviço Batch 2017-01-01.4.0 apresenta uma alteração inovadora que requer que Atualize o código para chamar essa versão. Se estiver a migrar código a partir de uma versão mais antiga do Batch, tenha em atenção de que o **runElevated** propriedade já não é suportada em bibliotecas de cliente REST API ou lote. Utilize a nova **userIdentity** propriedade de uma tarefa para especificar o nível de elevação. Consulte a secção intitulada [Atualize o código para a biblioteca de cliente mais recente do Batch](#update-your-code-to-the-latest-batch-client-library) para rápido diretrizes para atualizar o código do Batch, se estiver a utilizar uma das bibliotecas de cliente.
>
>

> [!NOTE] 
> As contas de utilizador abordadas neste artigo não suportam o protocolo RDP (Remote Desktop Protocol) ou de Secure Shell (SSH), por motivos de segurança. 
>
> Para ligar a um nó com a configuração de máquina virtual com Linux através de SSH, consulte [ambiente de trabalho remoto do utilização para uma VM com Linux no Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Para ligar a nós com o Windows através do RDP, consulte [ligar a uma VM do Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Para ligar a um nó com a configuração do serviço em nuvem através de RDP, consulte [ativar a ligação ao ambiente de trabalho remoto para uma função na Cloud Services do Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Acesso de conta de utilizador aos ficheiros e diretórios

Uma conta de utilizador automática e uma conta de utilizador nomeado tem acesso de leitura/escrita ao diretório de trabalho, o diretório partilhado e o diretório de tarefas de várias instâncias da tarefa. Ambos os tipos de contas de tem acesso de leitura para os diretórios de arranque e a tarefa de preparação.

Se uma tarefa é executada sob a mesma conta que foi utilizada para executar uma tarefa de início, a tarefa tem acesso de leitura / escrita ao diretório da tarefa de início. Da mesma forma, se uma tarefa é executada sob a mesma conta que foi utilizada para executar uma tarefa de preparação, a tarefa tem acesso de leitura / escrita ao diretório de tarefas de preparação da tarefa. Se uma tarefa é executada sob uma conta diferente que a tarefa de início ou a tarefa de preparação, a tarefa tem acesso de leitura para o respetivo diretório.

Para obter mais informações sobre aceder a ficheiros e diretórios a partir de uma tarefa, consulte [paralelo em grande escala desenvolver soluções com o Batch de computação](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Acesso elevado para tarefas 

Nível de elevação a conta de utilizador indica se uma tarefa é executada com acesso elevado. Uma conta de utilizador automática e uma conta de utilizador nomeado podem executar com acesso elevado. As duas opções para o nível de elevação são:

- **NonAdmin:** a tarefa é executada como um utilizador padrão sem acesso elevado. O nível de elevação de predefinido para uma conta de utilizador do Batch é sempre **NonAdmin**.
- **Administrador:** a tarefa é executada como um utilizador com acesso elevado e funciona com todas as permissões de administrador. 

## <a name="auto-user-accounts"></a>Contas de utilizador automática

Por predefinição, tarefas são executadas no Batch com uma conta de utilizador automática, como um utilizador padrão sem acesso elevado e com âmbito de tarefas. Quando a especificação de auto-utilizador está configurada para o âmbito da tarefa, o serviço Batch cria uma conta de utilizador automática para essa tarefa apenas.

A alternativa ao âmbito da tarefa é o âmbito de agrupamento. Quando a especificação de utilizador automática para uma tarefa está configurada para o âmbito de agrupamento, a tarefa é executada com uma conta de utilizador automática que está disponível para qualquer tarefa no conjunto. Para mais informações sobre o âmbito de conjunto, consulte a secção intitulada [executar uma tarefa que o utilizador automática com âmbito de agrupamento](#run-a-task-as-the-autouser-with-pool-scope).   

O âmbito de predefinido é diferente em nós do Windows e Linux:

- Em nós do Windows, as tarefas são executadas no âmbito da tarefa por predefinição.
- Nós do Linux executam sempre sob o âmbito de agrupamento.

Existem quatro configurações possíveis para a especificação de utilizador automática, cada um dos quais corresponde a uma conta de utilizador exclusivo de automática:

- Não-administrador acesso com âmbito de tarefas (a especificação de auto-utilizador predefinido)
- Acesso de administrador (elevados) com o âmbito da tarefa
- Não-administrador acesso com âmbito de conjunto
- Acesso de administrador com âmbito de conjunto

> [!IMPORTANT] 
> As tarefas em execução no âmbito da tarefa não tem acesso de facto para outras tarefas num nó. No entanto, um utilizador mal intencionado com acesso à conta foi contornar esta restrição, submeter uma tarefa que é executado com privilégios de administrador e acede aos outros diretórios de tarefas. Um utilizador mal intencionado pode também utilizar RDP ou SSH para ligar a um nó. É importante proteger o acesso as chaves de conta de Batch para impedir o cenário. Se suspeitar a que sua conta poderá ter sido comprometida, lembre-se de que voltar a gerar as chaves.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Executar uma tarefa como um utilizador automática com acesso elevado

Pode configurar a especificação de utilizador automática de privilégios de administrador quando tiver de executar uma tarefa com o acesso elevado. Por exemplo, uma tarefa de início pode precisar de acesso elevado para instalar software no nó.

> [!NOTE] 
> Em geral, é melhor utilizar o acesso elevado apenas quando necessário. Melhores práticas recomendado conceder o privilégio mínimo necessário para alcançar o resultado desejado. Por exemplo, se uma tarefa de início instala software para o utilizador atual, em vez de para todos os utilizadores, poderá conseguir Evite conceder acesso elevado para tarefas. Pode configurar a especificação de auto-utilizador para acesso de não administrador e o âmbito de agrupamento para todas as tarefas que necessitam de ser executado sob a mesma conta, incluindo a tarefa de início. 
>
>

Os fragmentos de código seguintes mostram como configurar a especificação do utilizador automaticamente. Os exemplos definido para a elevação `Admin` e o âmbito para `Task`. Âmbito da tarefa é a definição predefinida, embora esteja incluído aqui com vista à, exemplo.

#### <a name="batch-net"></a>.NET do Batch

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Executar uma tarefa como um utilizador automática com âmbito de conjunto

Quando um nó é aprovisionado, são criadas contas de dois em todo o conjunto de auto-utilizador em cada nó no conjunto, uma com acesso elevado e outro sem acesso elevado. Definir o âmbito do utilizador automaticamente ao âmbito de agrupamento para uma determinada tarefa é executada a tarefa sob uma destas duas contas de utilizador automática em todo o conjunto. 

Quando especificar o âmbito de agrupamento para auto-utilizador, todas as tarefas executadas com acesso de administrador a ser executado sob a mesma conta de utilizador automática em todo o conjunto. Da mesma forma, tarefas que são executadas sem as permissões de administrador também ser executados com uma única em todo o conjunto de auto-conta de utilizador. 

> [!NOTE] 
> As duas contas de utilizador automática ao nível do conjunto são contas separadas. As tarefas em execução sob a conta administrativa em todo o conjunto não podem partilhar os dados com as tarefas em execução sob a conta padrão e vice-versa. 
>
>

A vantagem para em execução na mesma conta de utilizador automática é que as tarefas são capazes de partilhar os dados com outras tarefas em execução no mesmo nó.

Os segredos entre as tarefas de partilha é um cenário onde executar tarefas das duas contas de utilizador automática em todo o agrupamento é útil. Por exemplo, suponha que uma tarefa de início tem de aprovisionar um segredo no nó que podem utilizar outras tarefas. Pode utilizar a API de proteção de dados (DPAPI) do Windows, mas requer privilégios de administrador. Em vez disso, pode proteger o segredo ao nível do utilizador. As tarefas em execução na mesma conta de utilizador podem aceder ao segredo sem acesso elevado.

Outro cenário onde poderá executar tarefas com uma conta de utilizador automática com âmbito de conjunto é uma partilha de ficheiros de Interface de passagem de mensagens (MPI). Uma partilha de ficheiros MPI é útil quando os nós da tarefa de MPI precisam trabalhar os mesmos dados de ficheiro. O nó principal cria uma partilha de ficheiros que podem aceder aos nós subordinados, se estiverem a executar na mesma conta de utilizador automaticamente. 

O seguinte fragmento de código define o âmbito do utilizador automaticamente ao âmbito de agrupamento para uma tarefa no .NET do Batch. O nível de elevação for omitido, para a tarefa seja executada sob a conta de auto-utilizador em todo o conjunto padrão.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Contas de utilizador nomeado

Pode definir as contas de utilizador nomeado quando cria um conjunto. Uma conta de utilizador nomeado tem um nome e uma palavra-passe que fornecer. Pode especificar o nível de elevação de uma conta de utilizador nomeado. Para nós do Linux, pode também fornecer uma chave privada SSH.

Uma conta de utilizador nomeado existe em todos os nós no conjunto e está disponível para todas as tarefas a executar em nós. Pode definir qualquer número de utilizadores com o nome de um conjunto. Quando adiciona uma tarefa ou a coleção de tarefas, pode especificar que a tarefa é executada sob uma das contas de utilizador nomeado definidas no conjunto.

Uma conta de utilizador nomeado é útil quando pretender executar todas as tarefas numa tarefa sob a mesma conta de utilizador, mas isolá-los a partir de tarefas em execução nas outras tarefas ao mesmo tempo. Por exemplo, pode criar um utilizador com o nome de cada tarefa e executar tarefas de cada tarefa nessa conta de utilizador nomeado. Cada tarefa, em seguida, pode partilhar um segredo com as suas próprias tarefas, mas não com as tarefas em execução nas outras tarefas.

Também pode utilizar uma conta de utilizador nomeado para executar uma tarefa que define as permissões de recursos externos, como partilhas de ficheiros. Com uma conta de utilizador nomeado, controlar a identidade do utilizador e pode utilizar essa identidade de utilizador para definir as permissões.  

As contas de utilizador nomeado permitem sem palavra-passe SSH entre os nós do Linux. Pode utilizar uma conta de utilizador nomeado connosco do Linux que tem de executar tarefas de várias instâncias. Cada nó no conjunto pode executar tarefas com uma conta de utilizador definida no conjunto de todo. Para mais informações sobre tarefas de várias instâncias, consulte [utilizar várias\-instância tarefas a executar aplicações MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Criar contas de utilizador nomeado

Para criar contas de utilizador nomeado no lote, adicione uma coleção de contas de utilizador para o conjunto. Os fragmentos de código seguinte mostram como criar contas de utilizador nomeado no .NET, Java e Python. Estes fragmentos de código mostram como criar admin e não com o nome contas num agrupamento de admin. Os exemplos criar agrupamentos utilizando a configuração do serviço em nuvem, mas utiliza a mesma abordagem ao criar um agrupamento do Windows ou Linux utilizando a configuração de máquina virtual.

#### <a name="batch-net-example-windows"></a>Exemplo de .NET do batch (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                                         
    virtualMachineSize: "small",                                                
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Exemplo de .NET do batch (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Exemplo de Java do batch

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Exemplo de Python do batch

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Executar uma tarefa com uma conta de utilizador nomeado com acesso elevado

Para executar uma tarefa como um utilizador elevados, defina a tarefa **UserIdentity** propriedade para uma conta de utilizador com o nome que foi criada com o respetivo **ElevationLevel** propriedade definida como `Admin`.

Este fragmento de código especifica que a tarefa deve ser executado sob uma conta de utilizador nomeado. Esta conta de utilizador nomeado foi definida no conjunto quando o conjunto que foi criado. Neste caso, a conta de utilizador nomeado foi criada com permissões de administrador:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Atualize o código para a biblioteca de cliente mais recente do Batch

A versão do serviço Batch 2017-01-01.4.0 introduz uma alteração inovadora, substituindo o **runElevated** propriedade disponível em versões anteriores com a **userIdentity** propriedade. As tabelas seguintes fornecem um mapeamento simple que pode utilizar para atualizar o código de versões anteriores das bibliotecas de cliente.

### <a name="batch-net"></a>.NET do Batch

| Se utiliza o seu código...                  | A atualização para...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`Não foi especificado | Não existe nenhuma atualização necessária                                                                                               |

### <a name="batch-java"></a>Batch Java

| Se utiliza o seu código...                      | A atualização para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`Não foi especificado | Não existe nenhuma atualização necessária                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Se utiliza o seu código...                      | A atualização para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated`Não foi especificado | Não existe nenhuma atualização necessária                                                                                                                                  |


## <a name="next-steps"></a>Passos seguintes

### <a name="batch-forum"></a>Fórum do batch

O [fórum do Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch) no MSDN é um excelente local para discutir Batch e de fazer perguntas sobre o serviço. HEAD na ativação pós-falha para publicações afixadas úteis e publique as suas perguntas que possam surgir ao criar as soluções do Batch.