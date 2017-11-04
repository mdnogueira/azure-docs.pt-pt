---
title: "Instalar pacotes de aplicações em nós de computação - Azure Batch | Microsoft Docs"
description: "Utilize a funcionalidade de pacotes de aplicações do Azure Batch para gerir facilmente várias aplicações e versões para a instalação do batch nós de computação."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d1f9951c9cc1b9380e166834afaeb18a4687e2d8
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implementar aplicações em nós com pacotes de aplicações de Batch de computação

A funcionalidade de pacotes de aplicações do Azure Batch fornece o facilitar a gestão de aplicações de tarefas e a respetiva implementação em nós de computação do conjunto. Com os pacotes de aplicação, pode carregar e gerir várias versões das aplicações que executam as suas tarefas, incluindo os respetivos ficheiros de suporte. Pode, em seguida, implementar automaticamente uma ou mais destas aplicações em nós de computação do conjunto.

Neste artigo, irá aprender a carregar e gerir pacotes de aplicação no portal do Azure. Em seguida, irá aprender como instalá-las em nós de computação de um conjunto com o [.NET do Batch] [ api_net] biblioteca.

> [!NOTE]
> 
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações.
>
> As APIs para criar e gerir pacotes de aplicações façam parte do [gestão de lotes .NET] [[api_net_mgmt]] biblioteca. As APIs para instalar pacotes de aplicações num nó de computação são parte a [.NET do Batch] [ api_net] biblioteca.  
>
> A funcionalidade de pacotes de aplicação descrita aqui substitui a funcionalidade de aplicações de Batch disponível em versões anteriores do serviço.
> 
> 

## <a name="application-package-requirements"></a>Requisitos do pacote de aplicação
Para utilizar pacotes de aplicações, terá de [associar uma conta de armazenamento do Azure](#link-a-storage-account) à sua conta do Batch.

Esta funcionalidade foi introduzida no [API REST do Batch] [ api_rest] versão 2015-12-01.2.2 e correspondente [.NET do Batch] [ api_net] versão da biblioteca 3.1.0. Recomendamos que utilize sempre a versão mais recente da API ao trabalhar com o Batch.

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações.
>
>

## <a name="about-applications-and-application-packages"></a>Sobre as aplicações e pacotes de aplicações
Dentro do Azure Batch, um *aplicação* refere-se a um conjunto com a versão dos binários do que pode ser transferido automaticamente em nós de computação do conjunto. Um *pacote de aplicação* refere-se a um *conjunto específico* desses binários e representa uma determinada *versão* da aplicação.

![Diagrama de alto nível das aplicações e pacotes de aplicações][1]

### <a name="applications"></a>Aplicações
Uma aplicação no Batch contém um ou mais aplicações, pacotes e especifica as opções de configuração para a aplicação. Por exemplo, uma aplicação pode especificar a versão do pacote de aplicação predefinido para instalar em nós de computação e se os pacotes podem ser atualizados ou eliminados.

### <a name="application-packages"></a>Pacotes de aplicações
Um pacote de aplicação é um ficheiro. zip que contém os binários da aplicação e ficheiros de suporte que são necessários para as suas tarefas executar a aplicação. Cada pacote de aplicações representa uma versão específica da aplicação.

Pode especificar os pacotes de aplicações nos níveis de agrupamento e tarefas. Pode especificar um ou mais destes pacotes e (opcionalmente) uma versão quando cria um conjunto ou tarefas.

* **Agrupamento de pacotes de aplicações** são implementadas nos *cada* nó no conjunto. As aplicações são implementadas quando um nó se associa um conjunto e quando é reiniciado ou recriada.
  
    Pacotes de aplicações do conjunto são adequados quando todos os nós num conjunto executar tarefas de uma tarefa. Pode especificar um ou mais pacotes de aplicações quando cria um conjunto, e pode adicionar ou atualizar os pacotes de um conjunto existente. Se atualizar pacotes de aplicações de um conjunto existente, tem de reiniciar respetivos nós para instalar o novo pacote.
* **Pacotes de aplicações de tarefas** são implementadas apenas a um nó de computação agendado para ser executada uma tarefa, antes de executar a linha de comandos da tarefa. Se o pacote de aplicações especificado e a versão já se encontra no nó, não é implementada novamente e é utilizado o pacote existente.
  
    Pacotes de aplicação de tarefas são úteis em ambientes de agrupamento partilhado, onde as diferentes tarefas são executadas num agrupamento de uma e o conjunto não é eliminado quando uma tarefa estiver concluída. Se o trabalho tiver menos tarefas do que nós no conjunto, os pacotes de aplicações de tarefas podem minimizar a transferência de dados, uma vez que a aplicação é implementada apenas nos nós que executam tarefas.
  
    Outros cenários que podem beneficiar dos pacotes de aplicação de tarefa são tarefas que executam uma aplicação de grandes dimensões, mas apenas algumas tarefas. Por exemplo, uma fase de pré-processamento ou uma tarefa de intercalação, onde a aplicação de intercalação ou pré-processamento de é pesadas, pode tirar partido da utilização de pacotes de aplicação de tarefas.

> [!IMPORTANT]
> Existem restrições no número de aplicações e pacotes de aplicações dentro de uma conta do Batch e o tamanho do pacote de aplicação máximo. Consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter detalhes sobre estes limites.
> 
> 

### <a name="benefits-of-application-packages"></a>Vantagens dos pacotes de aplicações
Pacotes de aplicações podem simplificar o código na sua solução do Batch e reduzir os custos gerais necessários para gerir as aplicações que executam as suas tarefas.

Com os pacotes de aplicação, tarefa de início do conjunto não tem de especificar uma longa lista de ficheiros de recurso individual para instalar em nós. Não tem de gerir várias versões dos ficheiros de aplicação no armazenamento do Azure ou nos seus nós manualmente. E, não precisa de preocupar com a gerar [SAS URLs](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso a ficheiros na sua conta do Storage. O batch funciona em segundo plano com o Storage do Azure para armazenar pacotes de aplicações e implementá-las em nós de computação.

> [!NOTE] 
> O tamanho total de uma tarefa de início tem de ser inferior ou igual a 32 768 carateres, incluindo ficheiros de recursos e variáveis de ambiente. Se a tarefa de início excede este limite, em seguida, utilizar pacotes de aplicações é outra opção. Pode também criar um arquivo zipped que contém os ficheiros de recursos, carregue-o como um blob de armazenamento do Azure e, em seguida, deszipe-lo a partir da linha de comandos da tarefa de início. 
>
>

## <a name="upload-and-manage-applications"></a>Carregar e gerir aplicações
Pode utilizar o [portal do Azure] [ portal] ou [gestão de lotes .NET](batch-management-dotnet.md) biblioteca para gerir os pacotes de aplicações na sua conta do Batch. Nas secções seguintes alguns, vamos primeiro mostram como ligar uma conta de armazenamento, em seguida, abordar adicionar aplicações e pacotes e gerir com o portal.

### <a name="link-a-storage-account"></a>Ligar uma conta de armazenamento
Para utilizar pacotes de aplicações, primeiro tem de associar uma conta de armazenamento do Azure à sua conta do Batch. Se ainda não tiver configurado uma conta de armazenamento, o portal do Azure apresenta um aviso a primeira vez que clicar no **aplicações** na peça de mosaico do **conta do Batch** painel.

> [!IMPORTANT]
> Atualmente, o batch suporta *apenas* o **para fins gerais** tipo de conta de armazenamento, conforme descrito no passo 5, [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account), na [sobre o Azure as contas do Storage](../storage/common/storage-create-storage-account.md). Quando liga uma conta de armazenamento do Azure para a sua conta do Batch, ligue *apenas* um **para fins gerais** conta de armazenamento.
> 
> 

![Aviso de 'Não existe nenhuma conta de armazenamento configurada' no portal do Azure][9]

O serviço Batch utiliza a conta de armazenamento associada para armazenar os pacotes de aplicações. Depois de tiver ligado as duas contas, o Batch pode implementar automaticamente os pacotes armazenados na conta de armazenamento ligada para os nós de computação. Para associar uma conta de armazenamento à sua conta do Batch, clique em **as definições de conta de armazenamento** no **aviso** painel e, em seguida, clique em **conta de armazenamento** no  **Conta de armazenamento** painel.

![Escolha o painel de conta de armazenamento no portal do Azure][10]

Recomendamos que crie uma conta de armazenamento *especificamente* para utilização com a sua conta do Batch e selecione-a aqui. Para obter mais informações sobre como criar uma conta de armazenamento, consulte "Criar uma conta de armazenamento" [contas do Storage do Azure sobre](../storage/common/storage-create-storage-account.md). Depois de criar uma conta de armazenamento, pode, em seguida, ligue-o a sua conta do Batch, utilizando o **conta de armazenamento** painel.

> [!WARNING]
> O serviço Batch utiliza o armazenamento do Azure para armazenar os pacotes de aplicações como blobs de blocos. É [cobrado como normal] [ storage_pricing] para os dados de BLOBs de blocos. Lembre-se de que considere o tamanho e número de pacotes de aplicações e, periodicamente remover pacotes preteridos para minimizar os custos.
> 
> 

### <a name="view-current-applications"></a>Aplicações atuais de vista
Para ver as aplicações na sua conta do Batch, clique o **aplicações** item de menu no menu à esquerda, ao visualizar o **conta do Batch** painel.

![Mosaico de aplicações][2]

A seleção desta opção de menu é aberto o **aplicações** painel:

![Lista de aplicações][3]

O **aplicações** painel apresenta o ID de cada aplicação na sua conta e as seguintes propriedades:

* **Pacotes**: O número de versões associadas esta aplicação.
* **Versão predefinida**: A versão da aplicação instalada se não indicam uma versão quando especificar a aplicação para um conjunto. Esta definição é opcional.
* **Permitir atualizações**: O valor que especifica se o pacote de atualizações, eliminações e adições são permitidos. Se isto estiver definido como **não**, pacote de atualizações e eliminações estão desativadas para a aplicação. Podem ser adicionadas apenas versões de pacote de aplicação nova. A predefinição é **Sim**.

### <a name="view-application-details"></a>Ver detalhes da aplicação
Para abrir o painel que inclui os motivos para uma aplicação, selecione a aplicação no **aplicações** painel.

![Detalhes da aplicação][4]

No painel de detalhes da aplicação, pode configurar as seguintes definições para a sua aplicação.

* **Permitir atualizações**: Especifique se os respetivos pacotes de aplicações podem ser atualizados ou eliminados. Consulte "Atualizar ou eliminar um pacote de aplicações" mais à frente neste artigo.
* **Versão predefinida**: Especifique um pacote de aplicações predefinido a implementar em nós de computação.
* **Nome a apresentar**: Especifique um nome amigável que a sua solução do Batch pode utilizar quando apresenta informações sobre a aplicação, por exemplo, na IU de um serviço que fornecer aos seus clientes através do Batch.

### <a name="add-a-new-application"></a>Adicionar uma nova aplicação
Para criar uma nova aplicação, adicione um pacote de aplicação e especifique um ID exclusivo, novas aplicações. O primeiro pacote de aplicações que adicionar com o novo ID de aplicação também cria a nova aplicação.

Clique em **adicionar** no **aplicações** painel para abrir o **nova aplicação** painel.

![Nova aplicação painel no portal do Azure][5]

O **nova aplicação** painel fornece os campos seguintes para especificar as definições da sua nova aplicação e o pacote de aplicação.

**Id da aplicação**

Este campo especifica o ID da sua aplicação nova, que está sujeita as regras de validação padrão do ID de lote do Azure. As regras para fornecer um ID de aplicação são os seguintes:

* Em nós do Windows, o ID pode conter qualquer combinação de carateres alfanuméricos, hífenes e carateres de sublinhado. Em nós do Linux, são permitidas apenas carateres alfanuméricos e carateres de sublinhado.
* Não pode conter mais de 64 carateres.
* Tem de ser exclusivos dentro da conta do Batch.
* É sensível e preservação de maiúsculas e minúsculas.

**Versão**

Este campo especifica a versão do pacote de aplicação que estiver a carregar. Cadeias de versão estão sujeitos às seguintes regras de validação:

* Em nós do Windows, a cadeia de versão pode conter qualquer combinação de carateres alfanuméricos, hífenes, carateres de sublinhado e pontos finais. Em nós do Linux, a cadeia de versão pode conter apenas carateres alfanuméricos e carateres de sublinhado.
* Não pode conter mais de 64 carateres.
* Tem de ser exclusivos dentro da aplicação.
* São preservação de maiúsculas e minúsculas e sensível.

**Pacote de aplicação**

Este campo especifica o ficheiro. zip que contém os binários da aplicação e os ficheiros de suporte que são necessárias para executar a aplicação. Clique em de **selecione um ficheiro** caixa ou no ícone de pasta para procurar e selecione um ficheiro. zip que contém os ficheiros da aplicação.

Depois de selecionar um ficheiro, clique em **OK** para iniciar o carregamento para o armazenamento do Azure. Quando a operação de carregamento estiver concluída, o portal apresenta uma notificação e fecha o painel. Consoante o tamanho do ficheiro que estiver a carregar e a velocidade da sua ligação de rede, esta operação pode demorar algum tempo.

> [!WARNING]
> Não feche o **nova aplicação** painel antes da operação de carregamento está concluída. Se o fizer, irá parar o processo de carregamento.
> 
> 

### <a name="add-a-new-application-package"></a>Adicionar um novo pacote de aplicação
Para adicionar uma nova versão do pacote de aplicação de uma aplicação existente, selecione uma aplicação no **aplicações** painel, clique em **pacotes**, em seguida, clique em **adicionar** para abrir o **Adicionar pacote** painel.

![Adicionar o painel do pacote de aplicação no portal do Azure][8]

Como pode ver, os campos corresponderem do **nova aplicação** painel, mas o **id da aplicação** caixa está desativada. Como fez para a nova aplicação, especifique o **versão** para o novo pacote, navegue até à sua **pacote de aplicação** . zip do ficheiro, em seguida, clique em **OK** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou eliminar um pacote de aplicação
Para atualizar ou eliminar um pacote de aplicação existente, abra o painel de detalhes para a aplicação, clique em **pacotes** para abrir o **pacotes** painel, clique em de **reticências** no a linha do pacote de aplicação que pretende modificar e selecione a ação que pretende efetuar.

![Atualizar ou eliminar o pacote no portal do Azure][7]

**Atualização**

Ao clicar em **atualização**, a *pacote de atualização* é apresentado o painel. Este painel é semelhante para o *novo pacote de aplicações* painel, no entanto, apenas o campo de seleção do pacote estiver ativado, permitindo-lhe especificar um novo ficheiro ZIP para carregar.

![Painel de pacote de atualização no portal do Azure][11]

**Eliminar**

Ao clicar em **eliminar**, é-lhe pedido para confirmar a eliminação da versão de pacote e Batch elimina o pacote do armazenamento do Azure. Se eliminar a versão predefinida de uma aplicação, o **versão predefinida** definição é removida da aplicação.

![Eliminar a aplicação][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicações em nós de computação
Agora que aprendeu como gerir pacotes de aplicações com o portal do Azure, pode discutimos como implementá-las em nós de computação e executá-los com tarefas do Batch.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicações do agrupamento
Para instalar um pacote de aplicação em todos os nós de computação num conjunto, especifique o pacote de aplicação de um ou mais *referências* para o conjunto. Os pacotes de aplicações que especificar para um conjunto estão instalados em cada nó de computação quando esse nó se associa ao conjunto e quando o nó é reiniciado ou recriado.

No .NET do Batch, especificar um ou mais [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] quando criar um novo conjunto, ou para um conjunto existente. O [ApplicationPackageReference] [ net_pkgref] classe especifica um ID da aplicação e a versão a instalar um conjunto de nós de computação.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Se uma implementação de pacote de aplicação falhar por algum motivo, o serviço Batch marca o nó [inutilizável][net_nodestate], e não existem tarefas são agendadas para execução nesse nó. Neste caso, deve **reiniciar** o nó para reinicie o processo de implementação do pacote. Reiniciar o nó também lhe permite agendamento de tarefas novamente no nó.
> 
> 

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicação de tarefa
Semelhante a um conjunto, especificar o pacote de aplicação *referências* para uma tarefa. Quando uma tarefa é agendada para ser executada num nó, o pacote é transferiu e extraiu imediatamente antes de linha de comandos da tarefa é executada. Se um pacote especificado e a versão já estiver instalado no nó, o pacote não é transferido e o pacote existente é utilizado.

Para instalar um pacote de aplicação de tarefa, configurar a tarefa [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] propriedade:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Executar as aplicações instaladas
Os pacotes que tiver especificado para um conjunto ou tarefas são transferidos e extraiu para um diretório denominado o `AZ_BATCH_ROOT_DIR` do nó. Batch também cria uma variável de ambiente que contém o caminho para o diretório com nome. As linhas de comandos de tarefas utilizar esta variável de ambiente quando a aplicação no nó de referência. 

Em nós do Windows, a variável é o seguinte formato:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Em nós do Linux, o formato é ligeiramente diferente. Pontos (.), hífenes (-) e inicia número (#) é simplificado para a variável de ambiente de carateres de sublinhado. Além disso, tenha em atenção que as maiúsculas e minúsculas do ID de aplicação é preservada. Por exemplo:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`e `version` são valores que correspondem à versão de aplicações e pacotes que especificou para a implementação. Por exemplo, se tiver especificado que 2.7 a versão da aplicação *blender* deve ser instalado em nós do Windows, as linhas de comandos de tarefas utilizará esta variável de ambiente para aceder aos respetivos ficheiros:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Em nós do Linux, especifique a variável de ambiente neste formato. Aplanar períodos (.), hífenes (-) e o número sinais (#) carateres de sublinhado e preservar o caso do ID da aplicação:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Ao carregar um pacote de aplicação, pode especificar uma versão predefinida para implementar os nós de computação. Se especificar uma versão predefinida para uma aplicação, pode omitir o sufixo de versão quando referir a aplicação. Pode especificar a versão de aplicação predefinida no portal do Azure, no painel de aplicações, conforme mostrado no [carregar e gerir aplicações](#upload-and-manage-applications).

Por exemplo, se definir "2.7" como a versão predefinida para a aplicação *blender*e as suas tarefas referenciam a variável de ambiente seguintes, em seguida, os nós do Windows irão executar a versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O fragmento de código seguinte mostra uma linha de comandos de tarefas de exemplo que inicia a versão predefinida do *blender* aplicação:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Consulte [definições de ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) no [descrição geral da funcionalidade do Batch](batch-api-basics.md) para obter mais informações sobre definições de ambiente de nó de computação.
> 
> 

## <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto
Se já tiver sido configurado um conjunto existente com um pacote de aplicação, pode especificar um novo pacote para o conjunto. Se especificar uma nova referência de pacote para um conjunto, situações seguintes:

* O serviço Batch instala o pacote recentemente especificado em todos os novos nós que associam ao conjunto e em qualquer nó existente que é reiniciado ou recriada.
* Computação nós que já estão no conjunto quando atualizar o pacote referenciará não instalam automaticamente o novo pacote de aplicação. Estes computação nós tem de ser reiniciados ou recriados para receber o novo pacote.
* Quando um novo pacote é implementado, as variáveis de ambiente criado refletem as referências de pacote de aplicação nova.

Neste exemplo, o conjunto existente tem a versão 2.7 do *blender* aplicações configurada como um dos respetivos [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. Para atualizar os nós do conjunto com a versão 2.76b, especifique um novo [ApplicationPackageReference] [ net_pkgref] com a nova versão e a consolidação da alteração.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Agora que a nova versão tiver sido configurada, o serviço Batch instala a versão 2.76b a qualquer *novo* nó se associa ao conjunto. Para instalar 2.76b em nós que são *já* no agrupamento, reiniciar ou recriá-los. Tenha em atenção que nós reinicializada manter os ficheiros de implementações de pacote anterior.

## <a name="list-the-applications-in-a-batch-account"></a>Listar as aplicações numa conta do Batch
Pode listar as aplicações e os respetivos pacotes numa conta do Batch, utilizando o [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] método.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Moldar cópias de segurança
Com os pacotes de aplicação, pode ajudar os seus clientes, selecione as aplicações para as respetivas tarefas e especifique a versão exata a utilizar durante o processamento de tarefas com o serviço Batch-ativado. Também pode fornecer a capacidade para os seus clientes carregar e controlar as suas próprias aplicações no seu serviço.

## <a name="next-steps"></a>Passos seguintes
* O [API REST do Batch] [ api_rest] também fornece suporte para trabalhar com pacotes de aplicações. Por exemplo, consulte o [applicationPackageReferences] [ rest_add_pool_with_packages] elemento [adicionar um conjunto para uma conta] [ rest_add_pool] para obter informações sobre como especificar pacotes para instalar utilizando a API REST. Consulte [aplicações] [ rest_applications] para obter detalhes sobre como obter informações sobre a aplicação utilizando a API de REST do Batch.
* Saiba como programaticamente [gerir contas do Azure Batch e quotas com gestão de lotes .NET](batch-management-dotnet.md). O [gestão de lotes .NET][api_net_mgmt] biblioteca, pode ativar funcionalidades de criação e eliminação de conta para a aplicação do Batch ou serviço.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/en-us/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível de pacotes de aplicações"
[2]: ./media/batch-application-packages/app_pkg_02.png "Mosaico de aplicações no portal do Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Painel de aplicações no portal do Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Painel de detalhes da aplicação no portal do Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nova aplicação painel no portal do Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Atualizar ou eliminar pacotes pendente no portal do Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Novo painel do pacote de aplicação no portal do Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Não existem alertas de conta de armazenamento ligada"
[10]: ./media/batch-application-packages/app_pkg_10.png "Escolha o painel de conta de armazenamento no portal do Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Painel de pacote de atualização no portal do Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Eliminar a caixa de diálogo de confirmação de pacote no portal do Azure"
