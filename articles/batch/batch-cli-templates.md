---
title: "Executar tarefas de lote do Azure ponto-a-ponto sem escrever código (pré-visualização) | Microsoft Docs"
description: Crie ficheiros de modelo para a CLI do Azure criar conjuntos, trabalhos e tarefas do Batch.
services: batch
author: mscurrell
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 10/17/2017
ms.author: markscu
ms.openlocfilehash: 87ec0e1b6d01fc5d13e9b9f46987e416d8e1958f
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Utilizar os Modelos da CLI do Batch e a Transferência de Ficheiros (Pré-visualização)

Utilizar a CLI do Azure é possível executar tarefas de lote sem escrever código.

Criar e utilizar ficheiros de modelo com a CLI do Azure para criar conjuntos, trabalhos e tarefas do Batch. Ficheiros de entrada da tarefa podem ser facilmente também carregados para a conta do storage associada com as conta e a tarefa de saída ficheiros Batch transferidos.

## <a name="overview"></a>Descrição geral

Uma extensão para a CLI do Azure permite que o Batch ser utilizado ponto-a-ponto por utilizadores que não são os programadores. Um conjunto pode ser criado, dados de entrada carregados, trabalhos e tarefas associadas criadas, e os dados de saída resultante transferido – nenhum código necessário, a CLI que está a ser utilizado diretamente ou a ser integrado de scripts.

Criar modelos do batch no [existente Batch suporte na CLI do Azure](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation) que permite que os ficheiros JSON especificar valores de propriedade para a criação de agrupamentos, tarefas, tarefas e outros itens. Com modelos do Batch, as seguintes funcionalidades são adicionadas ao longo do que é possível com os ficheiros JSON:

-   Os parâmetros podem ser definidos. Quando o modelo é utilizado, apenas os valores de parâmetros são especificados para criar o item com outro item a ser especificados no corpo do modelo de valores de propriedade. Um utilizador que compreende Batch e as aplicações a ser executadas pelo Batch podem criar modelos, especificando o conjunto, o trabalho e os valores de propriedade de tarefas. Um utilizador menos estiver familiarizado com o Batch e/ou as aplicações apenas tem de especificar os valores dos parâmetros definidos.

-   Fábricas de tarefas da tarefa criar um ou mais tarefas associadas a uma tarefa, evitando a necessidade de muitas definições de tarefa a ser criado e simplificar significativamente a submissão da tarefa.


Os ficheiros de dados de entrada têm de ser fornecidos para tarefas e ficheiros de dados de saída, muitas vezes, são produzidos. Uma conta de armazenamento está associada, por predefinição, com cada conta do Batch e de ficheiros podem ser facilmente transferidos de e para esta conta de armazenamento com a CLI de sem codificação e sem credenciais de armazenamento necessárias.

Por exemplo, [ffmpeg](http://ffmpeg.org/) é uma aplicação popular que processa os ficheiros de áudio e vídeos. Pode ser utilizada a CLI do Azure Batch para invocar ffmpeg para poderem transcodificar vídeo os ficheiros de origem várias resoluções.

-   É criado um modelo de agrupamento. O utilizador criar o modelo sabe como chamar a aplicação de ffmpeg e os respetivos requisitos; se especificarem a VM, SO adequado tamanho, como ffmpeg está instalado (a partir de um pacote de aplicação ou utilizando o Gestor de pacotes, por exemplo) e outros valores de propriedade de conjunto. Os parâmetros são criados para que quando o modelo é utilizado, apenas o ID do conjunto e o número de VMs tem de ser especificado.

-   É criado um modelo de tarefa. O utilizador criar o modelo sabe como ffmpeg precisa de ser invocado a origem de transcodificar vídeo para uma resolução diferente e especifica a linha de comandos de tarefas; eles também saberem que existe uma pasta que contém os ficheiros de vídeo de origem, com uma tarefa necessária por ficheiro de entrada.

-   Um utilizador final com um conjunto de ficheiros de vídeo para poderem transcodificar primeiro cria um conjunto utilizando o modelo de agrupamento, especificar apenas o ID do conjunto e o número de VMs necessário. Em seguida, elas podem carregar os ficheiros de origem para poderem transcodificar. Em seguida, pode ser submetida uma tarefa utilizando o modelo de tarefa, especificar apenas o ID do conjunto e a localização dos ficheiros de origem carregado. A tarefa do Batch é criada com uma tarefa por ficheiro de entrada que está a ser gerado. Por fim, os ficheiros de saída transcodificação podem ser a transferência.

## <a name="installation"></a>Instalação

Os modelo e um ficheiro as capacidades de transferência necessitam de uma extensão para ser instalada.

Para obter instruções sobre como instalar a CLI do Azure, consulte [instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Depois de instalada a CLI do Azure, a versão mais recente da extensão do Batch pode ser instalada utilizando o seguinte comando da CLI:

```azurecli
az extension add --source https://github.com/Azure/azure-batch-cli-extensions/releases/download/azure-batch-cli-extensions-2.0.0/azure_batch_cli_extensions-2.0.0-py2.py3-none-any.whl
```

Para obter mais informações sobre a extensão do Batch, consulte [Microsoft Azure Batch CLI extensões para o Windows, Mac e Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Modelos

A CLI do Azure Batch permite itens como conjuntos, trabalhos e tarefas a criar, especificando um ficheiro JSON que contém valores e nomes de propriedade. Por exemplo:

```azurecli
az batch pool create –-json-file AppPool.json
```

Os modelos do Azure Batch são semelhantes aos modelos Azure Resource Manager, na sintaxe e funcionalidade. São ficheiros JSON que contêm valores e nomes de propriedade do item, mas adiciona os seguintes conceitos principais:

-   **Parâmetros**

    -   Permitir que os valores de propriedade para ser especificado numa secção corpo, com apenas valores de parâmetros que necessita de ser fornecido quando o modelo é utilizado. Por exemplo, a definição de completa para um conjunto foi possível colocar no corpo e apenas um parâmetro definido para o id de agrupamento; apenas uma cadeia de ID do conjunto, por conseguinte, tem de ser fornecidos para criar um conjunto.
        
    -   O corpo do modelo pode ser criado por alguém com dados de conhecimento do Batch e as aplicações a ser executado por lote; apenas os valores dos parâmetros definidos pelo autor tem de ser fornecidos quando o modelo é utilizado. Um utilizador sem o Batch aprofundada e/ou dados de conhecimento da aplicação, por conseguinte, pode utilizar os modelos.

-   **Variáveis**

    -   Permitir que os valores de parâmetros simples ou complexas ser especificados num único local e utilizado em locais de um ou mais no corpo do modelo. Variáveis podem simplificar e reduzir o tamanho do modelo, bem como torná-lo mais sustentável por meio de uma localização para alterar as propriedades cujo valor pode ser alterada.

-   **Construções de nível mais elevadas**

    -   Algumas construções de nível mais elevadas estão disponíveis no modelo que ainda não estão disponíveis as APIs do Batch. Por exemplo, uma fábrica de tarefas pode ser definida num modelo de tarefa que cria várias tarefas do trabalho com uma definição comuns de tarefas. Estes construções evitar a necessidade de código dinamicamente criar vários ficheiros JSON, tais como um ficheiro por tarefas, bem como criar ficheiros de script para instalar aplicações através do Gestor de pacote, por exemplo.

    -   A determinada altura, quando aplicável, estes construções podem ser adicionado para o serviço Batch e estão disponíveis na APIs do Batch, UIs, etc.

### <a name="pool-templates"></a>Modelos de conjunto

Além das funcionalidades de parâmetros e variáveis de modelo padrão, as construções de nível mais elevadas seguintes são suportadas pelo modelo de agrupamento:

-   **Referências do pacote**

    -   Opcionalmente, permite que software ser copiado para nós de agrupamento através da utilização de gestores de pacote. O Gestor de pacotes e o ID de pacote estão especificados. A capacidade de declarar um ou mais pacotes evita a necessidade de criar um script que obtém os pacotes necessários, instale o script e execute o script em cada nó do conjunto.

Segue-se um exemplo de um modelo que cria um agrupamento de VMs com Linux com ffmpeg instalado e apenas necessita de uma cadeia de ID de agrupamento e o número de VMs para indicar a utilizar:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Se o ficheiro de modelo foi atribuído o nome _conjunto ffmpeg.json_, em seguida, o modelo deverá ser invocado da seguinte forma:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Modelos de tarefa

Além das funcionalidades de parâmetros e variáveis de modelo padrão, as construções de nível mais elevadas seguintes são suportadas pelo modelo de tarefa:

-   **Fábrica de tarefas**

    -   Cria várias tarefas para uma tarefa da definição de uma tarefa. Três tipos de fábrica de tarefas são suportados – varrimento paramétrico, tarefas por ficheiro e a coleção de tarefas.

Segue-se um exemplo de um modelo que cria uma tarefa que utiliza ffmpeg para ficheiros de vídeo MP4 transcodificar para um dos dois resoluções inferiores, com uma tarefa criada por ficheiro de vídeo de origem:

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Se o ficheiro de modelo foi chamado _tarefa ffmpeg.json_, em seguida, o modelo deverá ser invocado da seguinte forma:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Grupos de ficheiros e a transferência de ficheiros

A maioria das tarefas e tarefas exigem que os ficheiros de entrada e produzem ficheiros de saída. Ambos os ficheiros de entrada e saída ficheiros normalmente, tem de ser transferida, o cliente para o nó, ou do nó para o cliente. A extensão da CLI do Azure Batch deduz transferência de ficheiros away e utiliza a conta de armazenamento que é criada por predefinição, cada conta de Batch.

Um grupo de ficheiros equivale a um contentor que é criado na conta do storage do Azure. O grupo de ficheiros pode ter subpastas.

A extensão de Batch CLI fornece comandos para carregamento do cliente a um grupo de ficheiro especificado e transferir ficheiros do grupo de ficheiro especificado para um cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Os modelos de conjunto e tarefa permitem ficheiros armazenados nos grupos de ficheiros ser especificado para cópia em nós de agrupamento ou desativar nós de conjunto para um grupo de ficheiros. Por exemplo, do modelo de tarefa especificado anteriormente, o ficheiro grupo "ffmpeg-de entrada" especificada para a fábrica de tarefas como a localização dos ficheiros de vídeo de origem copiado para baixo para o nó para transcodificação; o ficheiro grupo "ffmpeg-output" é utilizado como a localização onde os ficheiros de saída transcodificação são copiados a partir do nó executar cada tarefa.

## <a name="summary"></a>Resumo

Suporte de transferência de ficheiros e modelo atualmente foram adicionadas apenas para a CLI do Azure. O objetivo é para expandir o público-alvo que pode utilizar o Batch para os utilizadores que não é necessário para desenvolver código utilizando as APIs do Batch, como os investigadores, utilizadores de TI e assim sucessivamente. Sem codificação, os utilizadores com dados de conhecimento do Azure, o Batch e as aplicações a ser executadas pelo Batch podem criar modelos para a criação do conjunto e tarefa. Com os parâmetros do modelo, os utilizadores sem conhecimentos detalhados do Batch e as aplicações podem utilizar os modelos.

Experimentar a extensão de Batch para a CLI do Azure e forneça quaisquer comentários ou sugestões, nos comentários para este artigo ou através de [fórum do Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Passos seguintes

- Consulte a mensagem de blogue do modelos Batch: [tarefas a executar o Azure Batch com a CLI do AZURE – nenhum código necessário](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Documentação detalhada de instalação e utilização, amostras e código de origem estão disponíveis no [repositório do Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).
