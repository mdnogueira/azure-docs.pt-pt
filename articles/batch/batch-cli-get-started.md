---
title: Introdução ao Azure Batch CLI | Microsoft Docs
description: Obtenha uma introdução rápida aos comandos do Batch na CLI do Azure para gerir recursos do serviço Azure Batch
services: batch
documentationcenter: ''
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/06/2016
ms.author: marsma

---
# Introdução ao Azure Batch CLI
A Interface de linha de comandos para várias plataformas do Azure (CLI do Azure) permite-lhe gerir as suas contas e recursos do Batch, como conjuntos, trabalhos e tarefas em shells de comando do Linux, Mac e Windows. Com a CLI do Azure, pode efetuar e encriptar muitas das mesmas tarefas que desempenha com as APIs do Batch, o portal do Azure e as cdmlets da Batch PowerShell.

Este artigo é baseado na CLI do Azure, versão 0.10.3.

## Pré-requisitos
* [Instalar a CLI do Azure](../xplat-cli-install.md)
* [Ligar a CLI do Azure à sua subscrição do Azure](../xplat-cli-connect.md)
* Mude para o **modo Resource Manager**: `azure config mode arm`

> [!TIP]
> Recomendamos que atualize frequentemente a instalação da CLI do Azure para tirar partido das atualizações e melhoramentos do serviço.
> 
> 

## Ajuda do comando
Pode apresentar o texto de ajuda para cada comando na CLI do Azure, acrescentando `-h` como a única opção após o comando. Por exemplo:

* Para obter ajuda para o comando `azure`, introduza: `azure -h`
* Para obter uma lista de todos os comandos do Batch na CLI, utilize: `azure batch -h`
* Para obter ajuda sobre a criação de uma conta do Batch, introduza: `azure batch account create -h`

Em caso de dúvida, utilize a opção da linha de comandos `-h` para obter ajuda sobre qualquer comando da CLI do Azure.

## Criar uma conta do Batch
Utilização:

    azure batch account create [options] <name>

Exemplo:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Cria uma nova conta do Batch com os parâmetros especificados. Tem de especificar pelo menos uma localização, o grupo de recursos e o nome da conta. Caso ainda não tenha um grupo de recursos, crie um executando `azure group create`, e especifique uma das regiões do Azure (por exemplo, "E.U.A. Oeste") para a opção `--location`. Por exemplo:

    azure group create --name "resgroup001" --location "West US"

> [!NOTE]
> Este nome da conta do Batch tem de ser exclusivo na região do Azure onde a conta está criada. Pode conter apenas carateres alfanuméricos em minúsculas e tem de ter 3 a 24 carateres de comprimento. Não é possível utilizar carateres especiais como `-` ou `_` nos nomes de conta do Batch.
> 
> 

### Conta de armazenamento ligada (armazenamento automático)
Quando a criar, pode (opcionalmente) ligar uma conta de Armazenamento **para fins gerais** à conta do Batch. A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do Batch utiliza o armazenamento de blobs numa conta de Armazenamento para Fins Gerais, tal como o faz a biblioteca [.NET de Convenções de Ficheiros do Batch](batch-task-output.md). Estas funcionalidades opcionais ajudam-no a implementar as aplicações nas quais as suas tarefas do Batch são executadas e a manter os dados que aquelas produzem.

Para ligar uma conta de armazenamento do Azure existente a uma nova conta do Batch quando a criar, especifique a opção `--autostorage-account-id`. Esta opção necessita do ID de recurso completamente qualificado da conta de armazenamento.

Em primeiro lugar, mostre os detalhes da sua conta de armazenamento:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Em seguida, utilize o valor de **Url** para a opção `--autostorage-account-id`. O valor de Url começa com "/subscriptions/" e contém o caminho do recurso e o ID da subscrição para a conta de Armazenamento:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## Eliminar uma conta do Batch
Utilização:

    azure batch account delete [options] <name>

Exemplo:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Elimina a conta do Batch especificada. Quando lhe for perguntado, confirme se pretende remover a conta (a remoção de conta pode demorar algum tempo a concluir).

## Gerir chaves de acesso à conta
Necessita de uma chave de acesso para [criar e modificar os recursos](#create-and-modify-batch-resources) na sua conta do Batch.

### Listar chaves de acesso
Utilização:

    azure batch account keys list [options] <name>

Exemplo:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Lista as chaves de conta para a conta do Batch especificada.

### Gerar uma nova chave de acesso
Utilização:

    azure batch account keys renew [options] --<primary|secondary> <name>

Exemplo:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Volta a gerar a chave de conta especificada para a conta do Batch especificada.

## Criar e modificar recursos do Batch
Pode utilizar a CLI do Azure para criar, ler, atualizar e eliminar (CRUD) recursos do Batch como conjuntos, tarefas, nós de computação trabalhos e tarefas. Estas operações CRUD requerem o nome da conta do Batch, a chave de acesso e o ponto final. Pode especificá-los com as opções `-a`, `-k` e `-u` ou definir [variáveis de ambiente](#credential-environment-variables) que a CLI utiliza automaticamente (caso estejam preenchidas).

### Variáveis de ambiente de credencial
Pode definir as variáveis de ambiente `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` e `AZURE_BATCH_ENDPOINT` em vez de especificar as opções `-a`, `-k` e `-u` na linha de comandos de cada comando que executar. A CLI do Batch utiliza estas variáveis (caso estejam definidas), pelo que pode omitir as opções `-a`, `-k`, e `-u`. O resto deste artigo pressupõe a utilização destas variáveis de ambiente.

> [!TIP]
> Liste as chaves com `azure batch account keys list`, e inclua o ponto final da conta com `azure batch account show`.
> 
> 

### Ficheiros JSON
Ao criar recursos do Batch como conjuntos e tarefas, pode especificar um ficheiro JSON que contenha a configuração do novo recurso em vez de transmitir os respetivos parâmetros como opções de linha de comandos. Por exemplo:

`azure batch pool create my_batch_pool.json`

Apesar de poder executar várias operações de criação de recursos utilizando apenas as opções da linha de comandos, algumas funcionalidades requerem um ficheiro formatado em JSON que contenha os detalhes dos recursos. Por exemplo, tem de utilizar um ficheiro JSON se pretender especificar os ficheiros de recursos de uma tarefa de início.

Para localizar o JSON necessário para criar um recurso, veja a documentação [Referência da API de REST do Batch][rest_api] no MSDN. Cada tópico "Adicionar *tipo de recurso*" contém o JSON de exemplo para criar o recurso, que pode utilizar como modelos para os seus ficheiros JSON. Por exemplo, o JSON para a criação de conjuntos pode ser encontrado em [Adicionar um conjunto a uma conta][rest_add_pool].

> [!NOTE]
> Se especificar um ficheiro JSON ao criar um recurso, todos os outros parâmetros que especificar na linha de comandos desse recurso são ignorados.
> 
> 

## Criar um conjunto
Utilização:

    azure batch pool create [options] [json-file]

Exemplo (Configuração da Máquina Virtual):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exemplo (configuração dos Serviços Cloud):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Cria um conjunto de nós de computação no serviço Batch.

Como referido na [Descrição geral da funcionalidade Batch](batch-api-basics.md#pool), tem duas opções quando seleciona um sistema operativo para os nós no seu conjunto: **Configuração da Máquina Virtual** e **Configuração dos Serviços Cloud**. Utilize as opções `--image-*` para criar conjuntos de Configuração da Máquina Virtual, e `--os-family` para criar conjuntos de Configuração de Serviços Cloud. Não é possível especificar as opções `--os-family` e `--image-*`.

Pode especificar [pacotes de aplicações](batch-application-packages.md) de conjuntos e a linha de comandos para um [tarefa de início](batch-api-basics.md#start-task). Para especificar ficheiros de recursos para a tarefa de início, no entanto, é necessário utilizar um [ficheiro JSON](#json-files).

Elimine um conjunto com:

    azure batch pool delete [pool-id]

> [!TIP]
> Verifique a [lista de imagens da máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images) para valores adequados para as opções `--image-*`.
> 
> 

## Criar uma tarefa
Utilização:

    azure batch job create [options] [json-file]

Exemplo:

    azure batch job create --id "job001" --pool-id "pool001"

Adiciona uma tarefa à conta do Batch e especifica o conjunto no qual as respetivas tarefas são executadas.

Elimine uma tarefa com:

    azure batch job delete [job-id]

## Listar conjuntos, trabalhos, tarefas e outros recursos
Cada tipo de recurso do Batch suporta um comando `list` que consulta a sua conta do Batch e apresenta uma lista de recursos desse tipo. Por exemplo, pode listar os conjuntos na sua conta e as tarefas num trabalho:

    azure batch pool list
    azure batch task list --job-id "job001"

### Fazer uma lista de recursos de forma eficiente
Para consultas mais rápidas, pode especificar as opções de cláusulas **selecionar**, **filtrar** e **expandir** para operações `list`. Utilize estas opções para limitar a quantidade de dados devolvidos pelo serviço Batch. Uma vez que todas as filtragens ocorrem do lado do servidor, somente são apresentados os dados que lhe interessam. Utilize estas cláusulas para poupar largura de banda (e, por conseguinte, tempo) ao executar operações de lista.

Por exemplo, isto irá devolver apenas os conjuntos cujos ids começam por "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

A CLI do Batch suporta as três cláusulas suportadas pelo serviço Batch:

* `--select-clause [select-clause]`  Devolve um subconjunto de propriedades para cada entidade
* `--filter-clause [filter-clause]`  Devolve apenas as entidades que correspondem à expressão OData especificada
* `--expand-clause [expand-clause]`  Obtem as informações da entidade numa única chamada REST subjacente. A cláusula de expansão só suporta a propriedade `stats` neste momento.

Para obter detalhes sobre as três cláusulas e a execução de consultas de lista com as mesmas, veja o artigo [Consultar o serviço Azure Batch de forma eficiente](batch-efficient-list-queries.md).

## Gestão de pacotes de aplicações
Os pacotes de aplicações proporcionam uma forma simplificada para implementar aplicações nos nós de computação nos seus conjuntos. Com a CLI do Azure, pode carregar pacotes de aplicações, gerir versões de pacotes e eliminar pacotes.

Para criar uma nova aplicação e adicionar uma versão de pacote:

**Criar** uma aplicação:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Adicionar** um pacote de aplicações:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Ativar** o pacote:

    azure batch application package activate "resgroup002" "azbatch002" "MyTaskApplication" "1.10-beta3" zip

### Implementar um pacote de aplicações
Quando cria um novo conjunto, pode especificar um ou mais pacotes de aplicações para implementação. Quando especifica um pacote durante a criação de conjuntos, é implementado em cada nó no momento em que o nó é associado ao conjunto. Os pacotes também são implementados quando um nó é reiniciado ou recriado.

Este comando especifica um pacote durante a criação do conjunto e é implementado quando cada nó é associado ao novo conjunto:

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

De momento, não é possível especificar a versão do pacote que é implementada utilizando opções da linha de comandos. Tem de definir primeiro uma versão predefinida para a aplicação utilizando o portal do Azure para poder atribuí-lo a um conjunto. Veja como definir uma versão predefinida em [Implementação de aplicações com pacotes de aplicações do Azure Batch](batch-application-packages.md). No entanto, pode especificar uma versão predefinida se utilizar um [ficheiro JSON](#json-files) em vez de opções de linha de comandos quando criar um conjunto.

> [!IMPORTANT]
> Tem de [associar uma conta de armazenamento do Azure](#linked-storage-account-autostorage) à sua conta do Batch para utilizar pacotes de aplicações.
> 
> 

## Sugestões de resolução de problemas
Esta secção destina-se a fornecer-lhe recursos para utilizar na resolução de problemas da CLI do Azure. Isto não irá necessariamente resolver todos os problemas, mas pode ajudar a restringir as causas e direcioná-lo para recursos de ajuda.

* Utilize `-h` para obter o **texto da ajuda** de qualquer comando da CLI
* Utilize `-v` e `-vv` para a apresentação da saída do comando **verboso**; `-vv` é verboso "extra" e apresenta os pedidos e respostas REST. Estes parâmetros são úteis para apresentar a saída de erro completa.
* Pode ver a **saída de comandos como JSON** com a opção `--json`. Por exemplo, `azure batch pool show "pool001" --json` apresenta as propriedades de pool001 no formato JSON. Em seguida, pode copiar e modificar esta saída para utilizar num `--json-file` (veja [ficheiros JSON](#json-files) anteriormente neste artigo).
* O [Fórum do Batch no MSDN][batch_forum] é um excelente recurso de ajuda e está a ser monitorizado de perto pelos membros da equipa do Batch. Certifique-se de que publica as suas perguntas caso se depare com problemas ou se quiser obter ajuda para uma operação específica.
* Nem todas as operações de recursos do Batch são suportadas pela CLI do Azure. Por exemplo, não pode especificar uma *versão* do pacote de aplicação para um conjunto, apenas o ID do pacote. Nestes casos, pode ser necessário fornecer um `--json-file` para o comando em vez de utilizar opções da linha de comandos. Certifique-se de que se mantém atualizado com a versão mais recente da CLI para obter melhoramentos futuros.

## Passos seguintes
* Veja o artigo [Implementação de aplicações com pacotes de aplicações do Azure Batch](batch-application-packages.md) para saber como utilizar esta funcionalidade para gerir e implementar as aplicações que executa em nós de computação do Batch.
* Veja [Consultar o serviço Batch de forma eficiente](batch-efficient-list-queries.md) para obter mais informações sobre a redução do número de itens e o tipo de informação devolvido para as consultas ao Batch.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx


<!--HONumber=Sep16_HO3-->


