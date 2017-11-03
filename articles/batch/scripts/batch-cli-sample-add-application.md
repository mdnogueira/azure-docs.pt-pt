---
title: "CLI do Azure Script exemplo - adicionar uma aplicação no Batch | Microsoft Docs"
description: "CLI do Azure Script exemplo - adicionar uma aplicação no Batch"
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
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Adicionar as aplicações do Azure Batch com a CLI do Azure

Este script demonstra como configurar uma aplicação para utilização com um conjunto do Azure Batch ou tarefas. Para configurar uma aplicação, o executável, juntamente com quaisquer dependências, num ficheiro. zip do pacote. Neste exemplo é denominado o ficheiro zip executável ' meu-aplicação-exe.zip'.

## <a name="prerequisites"></a>Pré-requisitos

- Instalar a CLI do Azure utilizando as instruções fornecidas a [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se ainda não o tiver feito.
- Se ainda não tiver um, crie uma conta do Batch. Consulte [criar uma conta do Batch com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para um script de exemplo que cria uma conta.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-application"></a>Limpar a aplicação

Depois de executar o script de exemplo acima, execute os seguintes comandos para remover a aplicação e todos os seus pacotes de aplicação foi carregado.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar uma aplicação e carregar um pacote de aplicação.
Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar aplicação do batch AZ](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | Cria uma aplicação.  |
| [conjunto de aplicações de batch AZ](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | Propriedades de uma aplicação de atualizações.  |
| [Criar pacote de aplicações de batch AZ](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | Adiciona um pacote de aplicação para a aplicação especificada.  |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de Batch CLI adicionais podem ser encontrados no [documentação da CLI do Azure Batch](../batch-cli-samples.md).
