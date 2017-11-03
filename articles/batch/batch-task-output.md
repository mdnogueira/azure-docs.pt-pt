---
title: "Manter resultados ou os registos de tarefas e as tarefas concluídas para um arquivo de dados - Azure Batch | Microsoft Docs"
description: "Saiba mais sobre as diferentes opções para persistentes dados de saída de tarefas e tarefas do Batch. Pode manter os dados para armazenamento do Azure ou para outro arquivo de dados."
services: batch
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ca93e823f02b1483ed290cf89de191937d1e2c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="persist-job-and-task-output"></a>Manter saída de trabalhos e tarefas

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alguns exemplos comuns do resultado da tarefa incluem:

- Ficheiros criados quando os processos de tarefa de dados de entrada.
- Ficheiros de registo associados com a execução da tarefa. 

Este artigo descreve várias opções para o resultado da tarefa persistentes e cenários para o qual cada opção é mais adequada.   

## <a name="about-the-batch-file-conventions-standard"></a>Sobre a norma de convenções de ficheiro Batch

Batch define um conjunto opcional de convenções de nomenclatura de ficheiros de saída da tarefa no armazenamento do Azure. O [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) descreve estas convenções. O padrão de ficheiro convenções determina os nomes do contentor e BLOBs de caminho de destino no armazenamento do Azure para um ficheiro de saída determinado com base nos nomes de tarefa e tarefas.

Está a funcionar para si se optar por utilizar o padrão de convenções de ficheiro para atribuir nomes aos seus ficheiros de dados de saída. Também pode nome do contentor de destino e do blob no entanto, assim o desejar. Se utilizar o padrão de convenções de ficheiro para atribuir nomes a ficheiros de saída, em seguida, os ficheiros de saída estão disponíveis para visualização no [portal do Azure][portal].

Existem várias formas que pode utilizar o padrão de convenções de ficheiro:

- Se estiver a utilizar a API do serviço de Batch para manter os ficheiros de saída, pode optar por nome destino contentores e blobs, de acordo com a norma de convenções de ficheiro. A API do serviço Batch permite-lhe manter os ficheiros de saída a partir do código de cliente, sem modificar a aplicação de tarefa.
- Se estiver a desenvolver com o .NET, pode utilizar o [biblioteca convenções de ficheiro do Azure Batch para .NET][nuget_package]. Uma vantagem de utilizar esta biblioteca é que suporta a consultar os ficheiros de saída, de acordo com os respetivos ID ou o objetivo. A funcionalidade de consulta incorporada torna mais fácil de aceder aos ficheiros de saída de uma aplicação cliente ou de outras tarefas. No entanto, a aplicação de tarefas têm de ser modificada para chamar a biblioteca de convenções de ficheiro. Para obter mais informações, consulte a referência para o [biblioteca convenções de ficheiro para o .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Se estiver a desenvolver com um idioma diferente do .NET, pode implementar a norma de convenções de ficheiros na sua aplicação.

## <a name="design-considerations-for-persisting-output"></a>Considerações de design para o resultado persistentes 

Ao conceber a sua solução do Batch, considere os seguintes fatores relacionados com tarefas e saídas.

* **Duração do nó de computação**: nós costumam ser transitórios, especialmente em conjuntos de ativar o dimensionamento automático de computação. Resultado de uma tarefa que é executada num nó está disponível apenas enquanto o nó existe e apenas dentro do período de retenção de ficheiros que tiver configurado para a tarefa. Se uma tarefa produz saída que pode ser necessárias quando a tarefa estiver concluída, em seguida, a tarefa tem de carregar os ficheiros de saída para um arquivo durável, como o armazenamento do Azure.

* **Armazenamento de saída**: armazenamento do Azure é recomendado como um arquivo de dados para o resultado da tarefa, mas pode utilizar qualquer armazenamento durável. Escrever o resultado da tarefa ao Storage do Azure está integrado API do serviço de Batch. Se utilizar outro formulário de armazenamento durável, terá de escrever a lógica de aplicação para manter a tarefa de saída por si.   

* **Obtenção de saída**: pode obter o resultado da tarefa diretamente a partir de nós de computação do conjunto, ou a partir de armazenamento do Azure ou noutro arquivo de dados se tiver mantido o resultado da tarefa. Para obter o resultado de uma tarefa diretamente a partir de um nó de computação, é necessário o nome de ficheiro e a respetiva localização de saída no nó. Se a manter o resultado da tarefa ao Storage do Azure, em seguida, terá do caminho completo para o ficheiro no armazenamento do Azure para transferir os ficheiros de saída com o SDK de armazenamento do Azure.

* **Ver a saída**: quando navegar para uma tarefa de lote do Azure portal e selecione **ficheiros num nó**, são apresentados com todos os ficheiros associados a tarefa, não apenas os ficheiros de saída que está interessado em. Novamente, ficheiros em nós de computação estão disponíveis apenas enquanto o nó existe e apenas dentro do período de retenção de ficheiros que tiver configurado para a tarefa. Para ver o resultado da tarefa que tenha transferido para o Storage do Azure, pode utilizar o portal do Azure ou uma aplicação de cliente do Storage do Azure, tal como o [Explorador de armazenamento do Azure][storage_explorer]. Para ver os dados de saída no armazenamento do Azure com o portal ou outra ferramenta, tem de conhecer a localização do ficheiro e navegue para a mesma diretamente.

## <a name="options-for-persisting-output"></a>Opções de saída persistentes

Dependendo do seu cenário, existem alguns diferentes abordagens que pode tomar para manter o resultado da tarefa:

- Utilize a API do serviço de Batch.  
- Utilize a biblioteca de convenções de ficheiro Batch para .NET.  
- Implemente a norma de convenções de ficheiro Batch na sua aplicação.
- Implemente uma solução de movimento do ficheiro personalizado.

As secções seguintes descrevem cada abordagem mais detalhadamente.

### <a name="use-the-batch-service-api"></a>Utilize a API do serviço de Batch

Com a versão de 2017-05-01, o serviço Batch adiciona suporte para especificar os ficheiros de saída no armazenamento do Azure para dados de tarefa quando tem [adiciona uma tarefa a uma tarefa](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) ou [adicionar uma coleção de tarefas para uma tarefa](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

A API do serviço Batch suporta persistentes dados de tarefa a uma conta de armazenamento do Azure em agrupamentos criados com a configuração de máquina virtual. Com a API do serviço Batch, pode manter os dados de tarefa sem modificar a aplicação que a tarefa é executada. Opcionalmente, pode aderir à [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) para atribuir nomes os ficheiros são mantidas ao Storage do Azure. 

Utilize a API do serviço de Batch para manter a tarefa quando de saída:

- Pretende manter os dados de tarefas de lote e o Gestor de tarefas em conjuntos criadas com a configuração de máquina virtual.
- Pretende manter os dados para um contentor de armazenamento do Azure com um nome arbitrário.
- Pretende manter os dados para um contentor de armazenamento do Azure com o nome em conformidade com a [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> A API do serviço Batch não suporta dados persistentes de tarefas em execução em conjuntos criados com a configuração do serviço de nuvem. Para obter informações sobre tarefas persistentes de saída em agrupamentos de executar a configuração de serviços em nuvem, consulte [manter os dados de tarefas e ao armazenamento do Azure com a biblioteca de convenções de ficheiro Batch para .NET manter](batch-task-output-file-conventions.md)
> 
> 

Para obter mais informações sobre o resultado da tarefa persistentes com a API do serviço Batch, consulte [API do serviço de dados de tarefa de persistência ao Storage do Azure com o Batch](batch-task-output-files.md). Consulte também o projeto de exemplo [PersistOutputs] [github_persistoutputs] no GitHub, que demonstra como utilizar a biblioteca de cliente de Batch para .NET para manter o resultado da tarefa para o armazenamento durável.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Utilize a biblioteca de convenções de ficheiro Batch para .NET

Os programadores criar soluções do Batch com c# e .NET podem utilizar o [biblioteca convenções de ficheiro para o .NET] [ nuget_package] para manter os dados de tarefa a um armazenamento do Azure da conta, according para o [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). A biblioteca de convenções de ficheiro processa mover ficheiros de saída para o Storage do Azure e o destino contentores e blobs de nomenclatura de forma bem conhecida.

A biblioteca de convenções de ficheiro suporta consultas de ficheiros de saída por fim, facilitando a localizá-las sem necessitar de ficheiro completo URIs ou de um ID. 

Utilize a biblioteca de convenções de ficheiro Batch para .NET para manter a tarefa quando de saída:

- Pretende-se aos dados de fluxo para o Storage do Azure enquanto a tarefa ainda está em execução.
- Pretende manter os dados a partir de conjuntos criados com a configuração do serviço em nuvem ou a configuração de máquina virtual.
- A aplicação cliente ou outras tarefas na tarefa tem de localizar e transferir ficheiros de saída de tarefas por ID ou por objetivo. 
- Pretende efetuar o carregamento antecipado ou apontar de verificação dos resultados iniciais.
- Pretende ver o resultado da tarefa no portal do Azure.

Para obter mais informações sobre o resultado da tarefa persistentes com a biblioteca de convenções de ficheiro para o .NET, consulte [manter os dados de tarefas e ao armazenamento do Azure com a biblioteca de convenções de ficheiro Batch para .NET manter ](batch-task-output-file-conventions.md). Consulte também o projeto de exemplo [PersistOutputs] [github_persistoutputs] no GitHub, que demonstra como utilizar a biblioteca de convenções de ficheiro para .NET para manter o resultado da tarefa para o armazenamento durável.

O projeto de exemplo no GitHub [PersistOutputs] [github_persistoutputs] demonstra como utilizar a biblioteca de cliente de Batch para .NET para manter o resultado da tarefa para o armazenamento durável.

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar a norma de convenções de ficheiro Batch

Se estiver a utilizar um idioma diferente do .NET, pode implementar o [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) na sua própria aplicação. 

Poderá pretender implementar a norma de nomenclatura de convenções de ficheiro por si quando quiser que um esquema de nomenclatura comprovado ou quando pretender ver o resultado da tarefa no portal do Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar uma solução de movimento do ficheiro personalizado

Também pode implementar a sua própria solução de movimento de ficheiro completo. Utilize esta abordagem quando:

- Pretende manter os dados de tarefas para um arquivo de dados diferente de armazenamento do Azure. Para carregar ficheiros para um arquivo de dados SQL do Azure ou DataLake do Azure, pode criar um script personalizado ou executável para carregar para essa localização. Em seguida, pode chamá-lo na linha de comandos depois de executar o executável principal. Por exemplo, num nó Windows, pode chamar estes dois comandos:`doMyWork.exe && uploadMyFilesToSql.exe`
- Pretende efetuar o carregamento antecipado ou apontar de verificação dos resultados iniciais.
- Pretende manter o controlo granular sobre o processamento de erros. Por exemplo, poderá implementar a sua própria solução se pretender utilizar as ações de dependência de tarefas para efetuar determinadas ações de carregamento baseadas em códigos de saída de tarefas específica. Para obter mais informações sobre ações de dependência de tarefas, consulte [criar as dependências de tarefas para executar as tarefas que dependem de outras tarefas](batch-task-dependencies.md). 

## <a name="next-steps"></a>Passos seguintes

- Explorar, utilizando as novas funcionalidades na API do serviço de Batch para manter os dados de tarefa na [API do serviço de dados de tarefa de persistência ao Storage do Azure com o Batch](batch-task-output-files.md).
- Saiba como utilizar a biblioteca de convenções de ficheiro Batch para .NET no [manter os dados de tarefas e ao armazenamento do Azure com a biblioteca de convenções de ficheiro Batch para .NET manter ](batch-task-output-file-conventions.md).
- Ver o projeto de exemplo [PersistOutputs] [github_persistoutputs] no GitHub, que demonstra como utilizar tanto a biblioteca de clientes de Batch para .NET e da biblioteca de convenções de ficheiro para o .NET para manter o resultado da tarefa para o armazenamento durável.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
