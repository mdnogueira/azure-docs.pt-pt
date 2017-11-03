---
title: Exemplo de Script da CLI do Azure - Gerir agrupamentos no Batch | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Gerir agrupamentos no Batch
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
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>Gerir conjuntos do Azure Batch com a CLI do Azure

Estes scripts demonstra Algumas das ferramentas disponíveis na CLI do Azure para criar e gerir conjuntos de nós de computação no serviço Azure Batch.

> [!NOTE]
> Os comandos neste exemplo, criar máquinas virtuais do Azure. VMs em execução serão acumular encargos à sua conta. Para minimizar estes custos, eliminar as VMs depois de concluir a executar o exemplo. Consulte [limpar agrupamentos](#clean-up-pools).

Conjuntos do batch podem ser configurados de duas formas, com uma configuração de serviços em nuvem (apenas Windows) ou uma configuração de Máquina Virtual (Windows e Linux). Os scripts de exemplo abaixo mostram como criar conjuntos com ambas as configurações.

## <a name="prerequisites"></a>Pré-requisitos

- Instalar a CLI do Azure utilizando as instruções fornecidas a [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se ainda não o tiver feito.
- Se ainda não tiver um, crie uma conta do Batch. Consulte [criar uma conta do Batch com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para um script de exemplo que cria uma conta.
- Configure uma aplicação para executar a partir de uma tarefa de início se ainda não o ainda feito. Consulte [adicionar as aplicações do Azure Batch com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) para um script de exemplo que cria uma aplicação e carrega um pacote de aplicação para o Azure.

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Conjunto com o script de exemplo de configuração de serviço de nuvem

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Conjunto com o script de exemplo de configuração de máquina virtual

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

## <a name="clean-up-pools"></a>Limpar os agrupamentos

Depois de executar o script de exemplo acima, execute o seguinte comando para eliminar os agrupamentos.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar e manipular conjuntos do Batch.
Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [início de sessão de conta de batch de AZ](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autenticar face a uma conta do Batch.  |
| [lista de resumo de aplicações de batch de AZ](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | Liste as aplicações disponíveis na conta do Batch.  |
| [Criar conjunto do batch AZ](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Crie um conjunto de VMs.  |
| [conjunto de conjunto do batch AZ](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | Atualize propriedades de um conjunto.  |
| [lista de skus de agente de nó de conjunto de batch AZ](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Agente de listas de nó disponível SKUs e as informações da imagem.  |
| [redimensionamento de conjunto do batch AZ](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Redimensione o número de VMs em execução no conjunto especificado.  |
| [Mostrar de conjunto do batch AZ](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Apresente as propriedades de um conjunto.  |
| [eliminação de conjunto do batch AZ](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | Elimine o conjunto especificado.  |
| [Ativar o dimensionamento automático de conjunto do batch AZ](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Ativar o dimensionamento automático num agrupamento e aplicar uma fórmula.  |
| [desativar o dimensionamento automático de conjunto do batch AZ](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Desative o dimensionamento automático num agrupamento.  |
| [lista de nós de batch AZ](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Liste todos os o nó de computação no conjunto especificado.  |
| [reinício do nó AZ batch](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Reinicie o nó de computação especificado.  |
| [eliminação de nó do batch AZ](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Elimine os nós listadas do agrupamento especificado.  |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de Batch CLI adicionais podem ser encontrados no [documentação da CLI do Azure Batch](../batch-cli-samples.md).

