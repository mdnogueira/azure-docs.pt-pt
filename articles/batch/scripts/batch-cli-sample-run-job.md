---
title: Exemplo de Script CLI do Azure - executar uma tarefa com o Batch | Microsoft Docs
description: Exemplo de Script CLI do Azure - executar uma tarefa com o Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: 73d93622d418359be421e043d0af4e4befc6f4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Tarefas em execução no Azure Batch com a CLI do Azure

Este script cria uma tarefa de lote e adiciona uma série de tarefas ao trabalho. É também demonstra como monitorizar uma tarefa e as respetivas tarefas. Por fim, mostra como consultar o serviço de Batch de forma eficiente para obter informações sobre as tarefas do trabalho.

## <a name="prerequisites"></a>Pré-requisitos

- Instalar a CLI do Azure utilizando as instruções fornecidas a [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se ainda não o tiver feito.
- Se ainda não tiver um, crie uma conta do Batch. Consulte [criar uma conta do Batch com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para um script de exemplo que cria uma conta.
- Configure uma aplicação para executar a partir de uma tarefa de início se ainda não o ainda feito. Consulte [adicionar as aplicações do Azure Batch com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) para um script de exemplo que cria uma aplicação e carrega um pacote de aplicação para o Azure.
- Configure um conjunto no qual a tarefa será executada. Consulte [conjuntos de gestão do Azure Batch com a CLI do Azure](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool) para um script de exemplo que cria um agrupamento com uma configuração de serviço em nuvem ou uma configuração de Máquina Virtual.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-job"></a>Limpar a tarefa

Depois de executar o script de exemplo acima, execute o seguinte comando para remover a tarefa e as respetivas tarefas. Tenha em atenção que o conjunto precisam de ser eliminadas separadamente. Consulte [conjuntos de gestão do Azure Batch com a CLI do Azure](./batch-cli-sample-manage-pool.md) para obter mais informações sobre como criar e a eliminação de conjuntos.

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar uma tarefa de lote e tarefas. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [início de sessão de conta de batch de AZ](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autenticar face a uma conta do Batch.  |
| [Criar trabalho do batch AZ](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Cria uma tarefa de lote.  |
| [conjunto de trabalho do batch AZ](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Atualiza as propriedades de uma tarefa de lote.  |
| [Mostrar de tarefa do batch AZ](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Obtém os detalhes de uma tarefa de lote especificado.  |
| [Criar tarefa do batch AZ](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | Adiciona uma tarefa para a tarefa de lote especificada.  |
| [Mostrar de tarefa do batch AZ](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Obtém os detalhes de uma tarefa da tarefa de lote especificada.  |
| [lista de tarefas de lote AZ](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_list) | Lista as tarefas associadas a tarefa especificada.  |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de Batch CLI adicionais podem ser encontrados no [documentação da CLI do Azure Batch](../batch-cli-samples.md).
