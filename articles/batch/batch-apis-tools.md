---
title: "Utilize as APIs e ferramentas do Azure Batch para desenvolver soluções de processamento paralelo em grande escala | Microsoft Docs"
description: "Saiba mais sobre as APIs e ferramentas disponíveis para o desenvolvimento de soluções com o serviço Azure Batch."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9ed3e60679a0c637c99f8019119289c71be81f83
ms.lasthandoff: 04/17/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Descrição geral dos APIs e ferramentas do Batch

O processamento de cargas de trabalho paralelas com o Azure Batch é geralmente realizado através de programação, utilizando uma das [APIs do Batch](#batch-development-apis). A aplicação ou serviço de cliente pode utilizar APIs do Batch para comunicar com o serviço Batch. Com as APIs do Batch, pode criar e gerir conjuntos de nós de computação, máquinas virtuais ou serviços cloud. Pode, então, agendar tarefas para serem executadas nesses nós. 

Pode processar de forma eficaz cargas de trabalho em grande escala para a sua organização ou fornecer um front-end de serviço aos seus clientes para que eles possam executar tarefas — a pedido ou com base numa agenda — em um, centenas ou mesmo em milhares de nós. Também pode utilizar o Azure Batch como parte de um fluxo de trabalho maior, gerido por ferramentas como o [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Quando estiver pronto para aprofundar a API do Batch para uma maior compreensão das funcionalidades que fornece, veja [Descrição geral da funcionalidade Batch para desenvolvedores](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-youll-need"></a>As contas do Azure de que irá precisar
Quando desenvolver soluções Batch, irá utilizar as seguintes contas no Microsoft Azure.

* **Conta e subscrição do Azure** - se ainda não tiver uma subscrição do Azure, pode ativar o seu [benefício de subscritor do MSDN][msdn_benefits] ou inscrever-se numa [conta gratuita do Azure][free_account]. Quando cria uma conta, é criada uma subscrição predefinida para si.
* **Conta do Batch** - os recursos do Azure Batch, como conjuntos, nós de computação, trabalhos e tarefas estão associados a uma conta do Azure Batch. Quando a aplicação faz um pedido com o serviço do Batch, autentica o pedido com o nome de conta do Azure Batch, o URL da conta e uma tecla de acesso. Pode [criar uma conta do Batch](batch-account-create-portal.md) no portal do Azure.
* **Conta de armazenamento** - o Batch inclui suporte incorporado para trabalhar com ficheiros no [Armazenamento do Azure][azure_storage]. Quase todos os cenários do Batch utilizam o Armazenamento de blobs para testar os programas que as tarefas executam e os dados que processam, e para o armazenamento de dados de saída por elas gerados. Para criar uma conta de Armazenamento, veja [Acerca de contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

## <a name="batch-development-apis"></a>APIs de programação do Batch
As suas aplicações e serviços podem emitir chamadas diretas de API REST ou utilizar uma ou mais das bibliotecas de cliente seguintes para executar e gerir as cargas de trabalho do Azure Batch.

| API | Referência da API | Transferência | Tutorial | Exemplos de código | Mais Informações |
| --- | --- | --- | --- | --- | --- |
| **REST do Batch** |[MSDN][batch_rest] |N/D |- |- | [Versões Suportadas](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET do Batch** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Tutorial](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Notas de Versão](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Tutorial](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Leia-me](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [Leia-me](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[github.io][api_java] |[Maven][api_java_jar] |- |[Leia-me][api_sample_java] | [Leia-me](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>Ferramentas de linha de comandos do Batch

A funcionalidade fornecida pelas APIs de desenvolvimento também está disponível com as ferramentas da linha de comandos: 

* [Cmdlets do Batch PowerShell][batch_ps]: os cmdlets do Azure Batch no módulo [Azure PowerShell](/powershell/azureps-cmdlets-docs) permitem-lhe gerir recursos do Batch com o PowerShell.
* [CLI do Azure](/cli/azure/overview): a Interface de Linha de Comandos do Azure (CLI do Azure) é um conjunto de ferramentas de várias plataformas que fornece comandos shell para interagir com vários serviços do Azure, incluindo o Batch.

## <a name="batch-resource-management"></a>Gestão de recursos do Batch

As APIs do Azure Resource Manager para o Batch concedem acesso programático às contas do Batch. Com estas APIs, pode gerir programaticamente contas do Batch, quotas e pacotes de aplicações.  

| API | Referência da API | Transferência | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **REST do Gestor de Recursos do Batch** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET do Gestor de Recursos do Batch** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Tutorial](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>Ferramentas do Batch
Embora não sejam necessárias para criar soluções com o Batch, existem algumas ferramentas valiosas a utilizar durante a criação e a depuração das suas aplicações e serviços Batch.

* [Portal do Azure][portal]: pode criar, monitorizar e eliminar conjuntos, trabalhos e tarefas do Batch nos painéis Batch do portal do Azure. Pode ver as informações de estado para estes e outros recursos ao executar os trabalhos, e até mesmo transferir os ficheiros de nós de computação nos seus conjuntos (por exemplo, transferir o `stderr.txt` de uma tarefa falhada durante a resolução de problemas). Também pode transferir ficheiros de Ambiente de Trabalho Remoto (RDP) que pode utilizar para iniciar sessão em nós de computação.
* [Azure Batch Explorer][batch_explorer]: o Batch Explorer fornece uma funcionalidade de gestão de recursos do Batch semelhante à do portal do Azure, mas numa aplicação autónoma de cliente do Windows Presentation Foundation (WPF). Uma das aplicações Batch .NET de exemplo disponível no [GitHub][github_samples], que pode criar com o Visual Studio 2015 ou mais recente e utilizá-la para procurar e gerir os recursos na sua conta do Batch enquanto desenvolve e depura soluções Batch. Veja o trabalho, o conjunto e os detalhes da tarefa, transfira ficheiros a partir de nós de computação e ligue a nós remotamente, com ficheiros de Ambiente de Trabalho Remoto (RDP) que pode transferir com o Explorador do Batch.
* [Explorador de Armazenamento do Microsoft Azure][storage_explorer]: embora não seja estritamente uma ferramenta do Azure Batch, o Explorador de Armazenamento é outra ferramenta valiosa a ter enquanto estiver a desenvolver e a depurar as soluções do Batch.

## <a name="next-steps"></a>Passos seguintes

* Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.
* [Introdução à biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md) para saber como utilizar C# e a biblioteca .NET do Batch para executar uma carga de trabalho simples utilizando um fluxo de trabalho Batch comum. Este artigo deve ser uma das suas primeiras paragens ao aprender a utilizar o serviço Batch. Também existe a [versão Python](batch-python-tutorial.md) do tutorial.
* Transfira as [amostras de código no GitHub][github_samples] para ver como tanto o C# como o Python pode interagir com o Batch para agendar e processar amostras de cargas de trabalho.
* Consulte [Percurso de aprendizagem do Batch][learning_path] para ter uma ideia dos recursos disponíveis à medida que aprende a trabalhar com o Batch.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

