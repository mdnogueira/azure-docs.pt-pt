---
title: "Variáveis de ambiente de nó de computação do Azure Batch | Microsoft Docs"
description: "Referência de variável de ambiente de nó de computação para análise de lote do Azure."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: tamram
ms.openlocfilehash: 29f642754430957e77ef68946f721f8e15dba065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-batch-compute-node-environment-variables"></a>Azure variáveis de ambiente do nó de computação do Batch
O [serviço Azure Batch](https://azure.microsoft.com/services/batch/) define as seguintes variáveis de ambiente de nós de computação. Pode fazer referência a estas variáveis de ambiente linhas de comandos de tarefas e os programas e scripts são executados por linhas de comandos.

Para obter informações adicionais sobre como utilizar variáveis de ambiente com o Batch, consulte [definições de ambiente para tarefas](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilidade de variável de ambiente

Estas variáveis de ambiente são visíveis apenas no contexto do **utilizador da tarefa**, a conta de utilizador no nó em que é executada uma tarefa. *Não* as verá se [ligar remotamente](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a um nó de computação através do protocolo DRP (Remote Desktop Protocol) ou de Secure Shell (SSH) e as apresentar. Isto deve-se ao facto de a conta de utilizador utilizada na ligação remota não ser a mesma conta utilizada pela tarefa.

## <a name="command-line-expansion-of-environment-variables"></a>Expansão da linha de comandos de variáveis de ambiente

As linhas de comando executadas pelas tarefas na computação não nós execute sob uma shell. Por conseguinte, estas linhas de comando não é possível tirar partido nativamente das funcionalidades de shell, tais como a expansão de variáveis de ambiente (Isto inclui o `PATH`). Para tirar partido destas funcionalidades, tem de **invocar a shell** na linha de comandos. Por exemplo, iniciar `cmd.exe` nós de computação no Windows ou `/bin/sh` em nós do Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variáveis de ambiente

| Nome da variável                     | Descrição                                                              | Disponibilidade | Exemplo |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | O nome da conta de Batch que a tarefa pertence.                  | Todas as tarefas.   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | Um diretório dentro do [diretório de trabalho] [ files_dirs] nos certificados são armazenados de Linux nós de computação. Tenha em atenção que esta variável de ambiente não se aplica ao Windows nós de computação.                                                  | Todas as tarefas.   |  /Mnt/batch/Tasks/workitems/batchjob001/Job-1/task001/Certs |
| AZ_BATCH_JOB_ID                 | O ID do trabalho ao qual a tarefa pertence. | Todas as tarefas exceto iniciar a tarefa. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | O caminho completo da tarefa de preparação [diretório de tarefas] [ files_dirs] no nó. | Todas as tarefas, exceto a tarefa de início e a tarefa de preparação. Disponível apenas se a tarefa está configurada com uma tarefa de preparação. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | O caminho completo da tarefa de preparação [diretório de trabalho] [ files_dirs] no nó. | Todas as tarefas, exceto a tarefa de início e a tarefa de preparação. Disponível apenas se a tarefa está configurada com uma tarefa de preparação. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | O ID do nó que a tarefa está atribuída. | Todas as tarefas. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | O caminho completo da raiz de todos os [Batch diretórios] [ files_dirs] no nó. | Todas as tarefas. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | O caminho completo do [diretório partilhado] [ files_dirs] no nó. Todas as tarefas executadas num nó tem acesso de leitura/escrita a este diretório. Tarefas que são executadas nos outros nós não dispõe de acesso remoto para este diretório (não é um diretório de rede "partilhado"). | Todas as tarefas. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | O caminho completo do [iniciar o diretório de tarefas] [ files_dirs] no nó. | Todas as tarefas. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | O ID do conjunto no qual a tarefa é executada. | Todas as tarefas. | batchpool001 |
| AZ_BATCH_TASK_DIR               | O caminho completo do [diretório de tarefas] [ files_dirs] no nó. Este diretório contém a `stdout.txt` e `stderr.txt` para a tarefa e o AZ_BATCH_TASK_WORKING_DIR. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | O ID da tarefa atual. | Todas as tarefas exceto iniciar a tarefa. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | O caminho completo do [diretório de trabalho] [ files_dirs] no nó. A tarefa em execução tem acesso de leitura/escrita para este diretório. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A lista de nós e o número de núcleos por nó que são atribuídos a um [tarefa de várias instâncias][multi_instance]. Nós e núcleos estão listados no formato`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, onde o número de nós é seguido de um ou mais endereços IP do nó e o número de núcleos para cada. |  Principais de várias instâncias e subtarefas. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | A lista de nós que são atribuídos a um [tarefa de várias instâncias] [ multi_instance] no formato `nodeIP;nodeIP`. | Principais de várias instâncias e subtarefas. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | A lista de nós que são atribuídos a um [tarefa de várias instâncias] [ multi_instance] no formato `nodeIP,nodeIP`. | Principais de várias instâncias e subtarefas. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | O endereço IP e porta do nó de computação em que a tarefa principal de um [tarefa de várias instâncias] [ multi_instance] é executado. | Principais de várias instâncias e subtarefas. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | Um caminho de diretório é idêntico para a tarefa principal e cada subtarefa de um [tarefa de várias instâncias][multi_instance]. O caminho existe em cada nó em que várias instâncias de tarefas é executado, não sendo leitura/escrita acessível para os comandos de tarefas em execução nesse nó (tanto o [comando coordenação] [ coord_cmd] e [ comando de aplicação][app_cmd]). Subtarefas ou uma tarefa principal que são executadas nos outros nós não dispõe de acesso remoto para este diretório (não é um diretório de rede "partilhado"). | Principais de várias instâncias e subtarefas. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Especifica se o nó atual é o nó principal para um [tarefa de várias instâncias][multi_instance]. Os valores possíveis são `true` e `false`.| Principais de várias instâncias e subtarefas. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Se `true`, o nó atual é um nó dedicado. Se `false`, é um [baixa nó](batch-low-pri-vms.md). | Todas as tarefas. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
