<properties
    pageTitle="Noções básicas do serviço Azure Batch | Microsoft Azure"
    description="Saiba como utilizar o serviço Azure Batch para cargas de trabalho em larga e escala e de HPC"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>


# Noções básicas do Azure Batch

O Azure Batch permite-lhe executar aplicações de computação de alto desempenho (HPC) e paralelas em larga escala, de forma eficaz na nuvem. É um serviço de plataforma que agenda trabalho de computação intensivo para ser executado numa coleção gerida de máquinas virtuais, e pode dimensionar automaticamente recursos de computação para satisfazer as necessidades das suas tarefas.

Com o serviço Batch, define os recursos de computação do Azure para executar as aplicações em paralelo e com dimensionamento. Pode executar tarefas a pedido ou agendadas e não precisa de criar, configurar e gerir at manualmente um cluster HPC, máquinas virtuais individuais, redes virtuais ou uma tarefa complexa e a infraestrutura de agendamento de tarefas.

## Casos de utilização do Batch

O Batch é um serviço gerido do Azure, que é utilizado para *processamento em lotes* ou *computação em batch*, executando um grande volume de tarefas semelhantes para obter o resultado pretendido. A computação em batch é utilizada frequentemente por organizações que processam, transformam e analisam regularmente grandes volumes de dados.

O Batch funciona bem com aplicações e cargas de trabalho intrinsecamente paralelas (também conhecidas como constrangedoramente paralelas). As cargas de trabalho intrinsecamente paralelas são facilmente divididas em várias tarefas que realizam tarefas simultaneamente em vários computadores.

![Tarefas paralelas][1]<br/>

Alguns exemplos de cargas de trabalho que são normalmente processadas com esta técnica são:

* Modelação de riscos financeiros
* Análise de dados climáticos e de hidrologia
* Composição, análise e processamentos de imagens
* Codificação e transcodificação de suporte de dados
* Análise de sequência genética
* Análise de tensão de engenharia
* Testes de software

O Batch também pode efetuar cálculos paralelos com um passo reduzido no final e executar cargas de trabalho HPC mais complexas, como aplicações de [Message Passing Interface (MPI)](batch-mpi.md).

Para uma comparação entre o Batch e outras opções de solução HPC no Azure, veja [Batch e soluções HPC](batch-hpc-solutions.md).

## Programar com o Batch

O processamento de cargas de trabalho paralelas com o Batch é geralmente realizado através de programação, utilizando uma das [APIs do Batch](#batch-development-apis). Com as APIs do Batch, pode criar e gerir conjuntos de nós de computação (máquinas virtuais) e agendar trabalhos e tarefas a executar nesses nós. Uma aplicação ou serviço de cliente criada por si, que utiliza APIs do Batch para comunicar com o serviço Batch.

Pode processar de forma eficaz cargas de trabalho em grande escala para a sua organização ou fornecer um front-end de serviço aos seus clientes para que eles possam executar tarefas — a pedido ou com base numa agenda — em um, centenas ou mesmo em milhares de nós. Também pode utilizar o Batch como parte de um fluxo de trabalho maior, gerido por ferramentas como o [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Quando estiver pronto para aprofundar a API do Batch para uma maior compreensão das funcionalidades que fornece, veja [Descrição geral da funcionalidade Batch para desenvolvedores](batch-api-basics.md).

### As contas do Azure de que irá precisar

Quando desenvolver soluções Batch, irá utilizar as seguintes contas no Microsoft Azure.

- **Conta e subscrição do Azure** - se ainda não tiver uma subscrição do Azure, pode ativar o seu [benefício de subscritor do MSDN][msdn_benefits] ou inscrever-se numa [conta gratuita do Azure][free_account]. Quando cria uma conta, é criada uma subscrição predefinida para si.

- **Conta do Batch** - quando as aplicações interagem com o serviço Batch, o nome da conta, o URL da conta e uma tecla de acesso são utilizados como credenciais. Todos os recursos do Batch, como conjuntos, nós de computação, trabalhos e tarefas estão associados a uma conta do Batch. Pode [criar uma conta do Batch](batch-account-create-portal.md) no portal do Azure.

- **Conta de armazenamento** - o Batch inclui suporte incorporado para trabalhar com ficheiros no [Armazenamento do Azure][azure_storage]. Quase todos os cenários do Batch utilizam o Armazenamento do Azure para testar os programas que as tarefas executam e os dados que processam, e para o armazenamento de dados de saída por elas gerados. Para criar uma conta de Armazenamento, veja [Acerca de contas de armazenamento do Azure](./../storage/storage-create-storage-account.md).

### APIs de programação do Batch

As suas aplicações e serviços podem emitir chamadas diretas de API REST, utilize uma ou mais das seguintes bibliotecas de cliente ou uma combinação das duas para gerir recursos de computação e executar cargas de trabalho paralelas com dimensionamento, com o serviço Batch.

| API    | Referência da API | Transferência | Exemplos de código |
| ----------------- | ------------- | -------- | ------------ |
| **REST do Batch** | [MSDN][batch_rest] | N/D | [MSDN][batch_rest] |
| **.NET do Batch**    | [MSDN][api_net] | [NuGet ][api_net_nuget] | [GitHub][api_sample_net] |
| **Batch Python**  | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Batch Java** (pré-visualização) | [github.io][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### Gestão de recursos do Batch

Além das APIs de cliente, também pode utilizar o seguinte procedimento para gerir os recursos na sua conta do Batch.

- [Cmdlets do Batch PowerShell][batch_ps]: os cmdlets do Azure Batch no módulo [Azure PowerShell](../powershell-install-configure.md) permitem-lhe gerir recursos do Batch com o PowerShell.

- [CLI do Azure](../xplat-cli-install.md): a Interface de Linha de Comandos do Azure (CLI do Azure) é um conjunto de ferramentas de várias plataformas que fornece comandos shell para interagir com vários serviços do Azure, incluindo o Batch.

- Biblioteca de cliente [Batch Management .NET](batch-management-dotnet.md): também disponível através de [NuGet][api_net_mgmt_nuget], pode utilizar a biblioteca de cliente .NET de Gestão do Batch para gerir contas do Batch, quotas e pacotes de aplicações, através de programação. A referência para a biblioteca de gestão está em [MSDN][api_net_mgmt].

### Ferramentas do Batch

Embora não sejam necessárias para criar soluções com o Batch, existem algumas ferramentas valiosas a utilizar durante a criação e a depuração das suas aplicações e serviços Batch.

 - [Portal do Azure][portal]: pode criar, monitorizar e eliminar conjuntos, trabalhos e tarefas do Batch nos painéis Batch do portal do Azure. Pode ver as informações de estado para estes e outros recursos ao executar os trabalhos, e até mesmo transferir os ficheiros de nós de computação nos seus conjuntos (por exemplo, transferir o `stderr.txt` de uma tarefa falhada durante a resolução de problemas). Também pode transferir ficheiros de Ambiente de Trabalho Remoto (RDP) que pode utilizar para iniciar sessão em nós de computação.

 - [Azure Batch Explorer][batch_explorer]: o Batch Explorer fornece uma funcionalidade de gestão de recursos do Batch semelhante à do portal do Azure, mas numa aplicação autónoma de cliente do Windows Presentation Foundation (WPF). Uma das aplicações Batch .NET de exemplo disponível no [GitHub][github_samples], que pode criar com o Visual Studio 2015 ou superior e utilizá-la para procurar e gerir os recursos na sua conta do Batch enquanto desenvolve e depura soluções Batch. Veja o trabalho, o conjunto e os detalhes da tarefa, transfira ficheiros a partir de nós de computação e ligue a nós remotamente, com ficheiros de Ambiente de Trabalho Remoto (RDP) que pode transferir com o Explorador do Batch.

 - [Explorador de Armazenamento do Microsoft Azure][storage_explorer]: embora não seja estritamente uma ferramenta do Azure Batch, o Explorador de Armazenamento é outra ferramenta valiosa a ter enquanto estiver a desenvolver e a depurar as soluções do Batch.

## Cenário: aumentar horizontalmente uma carga de trabalho paralela

Uma solução comum que utiliza as APIs do Batch para interagir com o serviço Batch envolve aumentar horizontalmente o trabalho intrinsecamente paralelo - como a composição de imagens de cenas 3D - num conjunto de nós de computação. Este conjunto de nós de computação pode ser o "farm de composição" que fornece dezenas, centenas ou mesmo milhares de núcleos para a tarefa de composição, por exemplo.

O diagrama seguinte mostra um fluxo de trabalho comum do Batch, com uma aplicação de cliente ou serviço alojado, utilizando o Batch para executar uma carga de trabalho paralela.

![Fluxo de trabalho de solução Batch][2]

Neste cenário comum, a aplicação ou o serviço processa uma carga de trabalho computacional no Azure Batch, efetuando os seguintes passos:

1. Carrega os **ficheiros de entrada** e a **aplicação** que processará esses ficheiros para a sua conta de Armazenamento do Azure. Os ficheiros de entrada podem ser quaisquer dados que a sua aplicação irá processar, tais como dados de modelação financeira ou ficheiros de vídeo para transcodificação. Os ficheiros de aplicação podem ser de qualquer aplicação que é utilizada para processar os dados, tais como uma aplicação de composição 3D ou um transcodificador de suporte de dados.

2. Crie um **conjunto** de nós de computação do Batch na sua conta do Batch - estes nós são as máquinas virtuais que irão executar as tarefas. Especifique as propriedades, como o [tamanho do nó](./../cloud-services/cloud-services-sizes-specs.md), o sistema operativo e a localização no armazenamento do Azure da aplicação a instalar quando os nós se associam ao conjunto (a aplicação que carregou no passo 1). Também pode configurar o conjunto para [dimensionar automaticamente](batch-automatic-scaling.md) - ajuste dinamicamente o número de nós de computação no conjunto - em resposta à carga de trabalho que as suas tarefas geram.

3. Crie um **trabalho** do Batch para executar a carga de trabalho no conjunto de nós de computação. Quando cria um trabalho, associa-o a um conjunto do Batch.

4. Adicionar **tarefas** ao trabalho. Ao adicionar tarefas a um trabalho, o serviço Batch agenda automaticamente as tarefas para execução nos nós de computação do conjunto. Cada tarefa utiliza a aplicação que carregou para processar os ficheiros de entrada.

    - 4a. Antes de uma tarefa ser executada, pode transferir os dados (os ficheiros de entrada) a processar para o nó de computação ao qual foram atribuídos. Se a aplicação ainda não tiver sido instalada no nó (ver passo 2), pode ser transferida aqui em vez disso. Quando as transferências estiverem concluídas, as tarefas são executadas nos respetivos nós atribuídos.

5. À medida que as tarefas são executadas, pode consultar o Batch para monitorizar o progresso do trabalho e respetivas tarefas. A aplicação ou serviço de cliente comunica com o serviço Batch através de HTTPS e, porque poderá estar a monitorizar milhares de tarefas em execução em milhares de nós de computação, certifique-se de que [consulta o serviço Batch de forma eficiente](batch-efficient-list-queries.md).

6. À medida que as tarefas são concluídas, elas podem carregar os respetivos dados de resultados para o Armazenamento do Azure. Também pode obter ficheiros diretamente de nós de computação.

7. Quando a monitorização deteta que concluiu as tarefas no seu trabalho, a aplicação ou serviço de cliente pode transferir os dados de saída para processamento adicional ou avaliação.

Tenha em atenção que esta é apenas uma forma de utilizar o Batch, e este cenário descreve apenas algumas das funcionalidades disponíveis. Por exemplo, pode executar [várias tarefas em paralelo](batch-parallel-node-tasks.md) em cada nó de computação, e pode utilizar a [ preparação de trabalhos e tarefas de conclusão](batch-job-prep-release.md) para preparar nós para os trabalhos e limpar posteriormente.

## Passos seguintes

Agora que tem um elevado nível de descrição geral do serviço Batch, está na altura de aprofundar para saber como pode utilizá-lo para processar cargas de trabalho de computação intensivas e paralelas.

- Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.

- [Introdução à biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md) para saber como utilizar C# e a biblioteca .NET do Batch para executar uma carga de trabalho simples utilizando um fluxo de trabalho Batch comum. Este artigo deve ser uma das suas primeiras paragens ao aprender a utilizar o serviço Batch. Também existe a [versão Python](batch-python-tutorial.md) do tutorial.

- Transfira as [amostras de código no GitHub][github_samples] para ver como tanto o C# como o Python pode interagir com o Batch para agendar e processar amostras de cargas de trabalho.

- Consulte [Percurso de aprendizagem do Batch][learning_path] para ter uma ideia dos recursos disponíveis à medida que aprende a trabalhar com o Batch.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=Sep16_HO3-->


