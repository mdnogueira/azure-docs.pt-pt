---
title: "Executar tarefas de em instâncias de contentor do Azure"
description: "Saiba como utilizar as instâncias de contentor do Azure para executar tarefas serem concluídas, tal como na compilação, as tarefas de composição de imagem ou teste."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: ae2466bdd3b21984b141c9178ea46bd25bb6357b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Executar uma tarefa de instâncias de contentor do Azure

O facilitar e velocidade da implementação de contentores em instâncias de contentor do Azure fornece uma plataforma apelativa para executar tarefas de execução-uma vez, como a criação, teste e composição de imagem numa instância de contentor.

Com a política de reinício configuráveis, pode especificar que os contentores estão parados quando os respetivos processos terem sido concluídos. Porque as instâncias de contentor são cobradas pelo segundo, está a cobrados apenas os recursos de computação utilizados enquanto o contentor de executar a tarefa está em execução.

Os exemplos apresentadas neste artigo utilizar a CLI do Azure. Tem de ter a CLI do Azure versão 2.0.21 ou superior [instalado localmente](/cli/azure/install-azure-cli), ou utilizar a CLI no [Shell de nuvem do Azure](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Política de reinício do contentor

Quando cria um contentor em instâncias de contentor do Azure, pode especificar uma das três definições de política de reinício.

| Reinicie a política   | Descrição |
| ---------------- | :---------- |
| `Always` | Contentores no grupo contentor sempre forem reiniciados. Este é o **predefinido** definição aplicada quando nenhuma política de reinício é especificada durante a criação do contentor. |
| `Never` | Nunca são reiniciadas contentores no grupo de contentor. Os contentores de executam no máximo uma vez. |
| `OnFailure` | Contentores no grupo contentor são reiniciadas apenas quando o processo executado no contentor de falha (quando termina com um código de saída diferente de zero). Os contentores são executados, pelo menos, uma vez. |

## <a name="specify-a-restart-policy"></a>Especifique uma política de reinício

Como especificar uma política de reinício depende de como criar as instâncias de contentor, tal como com a CLI do Azure, cmdlets do PowerShell do Azure, ou no portal do Azure. A CLI do Azure, especifique o `--restart-policy` parâmetro quando chamar [criar contentor de az][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Executar o exemplo de conclusão

Para ver a política de reinício em ação, criar uma instância do contentor do [aci/microsoft-wordcount](https://hub.docker.com/r/microsoft/aci-wordcount/) imagem e especifique o `OnFailure` reiniciar política. Este contentor de exemplo executa um script de Python que, por predefinição, analisa o texto de Shakespeare [Hamlet](http://shakespeare.mit.edu/hamlet/full.html), escreve as 10 palavras mais comuns STDOUT e, em seguida, sai.

Execute o contentor de exemplo com o seguinte [criar contentor de az] [ az-container-create] comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Instâncias de contentor do Azure inicia o contentor e, em seguida, interrompe mesmo quando sai das suas aplicações (ou script, neste caso). Quando as instâncias de contentor do Azure deixa de um contentor cuja política de reinício é `Never` ou `OnFailure`, estado do contentor está definido como **Terminated**. Pode verificar o estado de um contentor com o [mostrar de contentor az] [ az-container-show] comando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Exemplo de saída:

```bash
"Terminated"
```

Depois do Estado do contentor de exemplo mostra *Terminated*, pode ver o resultado da tarefa ao visualizar os registos do contentor. Execute o [az contentor registos] [ az-container-logs] da saída do comando para ver o script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Saída:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Este exemplo mostra a saída que o script enviado para STDOUT. As tarefas de, no entanto, poderão em vez disso, escrever respetivo resultado ao armazenamento persistente para obtenção posterior. Por exemplo, para um [partilha de ficheiros do Azure](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Configurar contentores no tempo de execução

Quando cria uma instância de contentor, pode definir o **variáveis de ambiente**, bem como especificar personalizadas **linha de comandos** a executar quando é iniciado o contentor. Pode utilizar estas definições nas suas tarefas batch para preparar cada contentor com a configuração de tarefas específica.

## <a name="environment-variables"></a>Variáveis de ambiente

Definir variáveis de ambiente no contentor de para fornecer a configuração dinâmica da aplicação ou script execute ao contentor. Isto é semelhante a `--env` argumento da linha de comandos para `docker run`.

Por exemplo, pode modificar o comportamento do script no contentor de exemplo, especificando as seguintes variáveis de ambiente quando criar a instância do contentor:

*NumWords*: O número de palavras enviado para STDOUT.

*MinLength*: O número mínimo de carateres de uma palavra-lo a contar. Um número mais alto ignora palavras comuns, como "de" e "a".

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Ao especificar `NumWords=5` e `MinLength=8` nas variáveis de ambiente do contentor, os registos do contentor devem apresentar um resultado diferente. Assim que mostra o estado do contentor como *Terminated* (utilizar `az container show` para verificar o estado do mesmo), apresentar os seus registos para ver o resultado novo:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>Substituição de linha de comandos

Especifique uma linha de comandos quando cria uma instância de contentor para substituir a linha de comandos integrada com a imagem de contentor. Isto é semelhante a `--entrypoint` argumento da linha de comandos para `docker run`.

Por exemplo, pode fazer com que o contentor de exemplo analisar texto diferente de *Hamlet* , especificando uma linha de comandos diferente. O script do Python executado por contentor, *wordcount.py*, aceita um URL como um argumento e irá processar conteúdo nessa página, em vez do predefinido.

Por exemplo, para determinar as palavras de cinco letra 3 principais do *Romeo e Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Novamente, depois do contentor é *Terminated*, ver o resultado para mostrar registos do contentor:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Saída:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passos seguintes

### <a name="persist-task-output"></a>Manter o resultado da tarefa

Para obter mais informações sobre como manter o resultado dos contentores que serem concluídas, consulte [montar uma partilha de ficheiros do Azure com instâncias de contentor do Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show