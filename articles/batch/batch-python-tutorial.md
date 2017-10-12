---
title: "Tutorial – Utilizar o SDK do Azure Batch para Python | Microsoft Docs"
description: "Obter informações sobre os conceitos básicos do Azure Batch e criar uma solução simples utilizando o Python."
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd5a977c10d3955639beb893cd7a37581b14f7c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Introdução ao Batch SDK para Python

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Conheça os princípios básicos do [Azure Batch][azure_batch] e o cliente [Batch Python][py_azure_sdk]; à medida que debatemos uma pequena aplicação Batch escrita em Python. Vemos como dois scripts de exemplo utilizam o serviço Batch para processar uma carga de trabalho paralela em máquinas virtuais do Linux na nuvem, e como eles interagem com o [Armazenamento do Azure](../storage/common/storage-introduction.md) para teste e obtenção de ficheiros. Irá conhecer um fluxo de trabalho de aplicações Batch comuns e obter uma compreensão base dos componentes principais do Batch, como trabalhos, conjuntos e nós de computação.

![Fluxo de trabalho da solução Batch (básico)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem um conhecimento de trabalho do Python e familiaridade com o Linux. Também parte do princípio de que consegue satisfazer os requisitos de criação de conta que são especificados abaixo para o Azure e os serviços Batch e Armazenamento.

### <a name="accounts"></a>Contas
* **Conta do Azure**: se ainda não tiver uma subscrição do Azure, [crie uma conta do Azure gratuita][azure_free_account].
* **Conta do Batch**: assim que tiver uma subscrição do Azure, [crie uma conta do Azure Batch](batch-account-create-portal.md).
* **Conta de Armazenamento**: veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

### <a name="code-sample"></a>Exemplo de código
O [exemplo de código ][github_article_samples] do tutorial do Python é um dos muito exemplos de código do Batch encontrados no repositório [azure-batch-samples][github_samples] do GitHub. Pode transferir todos os exemplos ao clicar em **Clonar ou transferir > Transferir ZIP** na home page do repositório ou ao clicar na ligação de transferência direta [azure-batch-samples-master.zip][github_samples_zip]. Assim que tiver extraído o conteúdo do ficheiro ZIP, os dois scripts deste tutorial encontram-se no diretório `article_samples`:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Ambiente do Python
Para executar o script de exemplo *python_tutorial_client.py* na sua estação de trabalho local, precisa de um **interpretador Python** compatível com a versão **2.7** ou **3.3+**. O script foi testado em Linux e Windows.

### <a name="cryptography-dependencies"></a>dependências de criptografia
Tem de instalar as dependências da biblioteca [criptografia][crypto], de que os pacotes do Python `azure-batch` e `azure-storage` precisam. Execute uma das operações seguintes adequada à sua plataforma ou veja os detalhes da [instalação da criptografia][crypto_install] para obter mais informações:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-devel libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Se instalar para Python 3.3 + no Linux, utilize os equivalentes python3 para as dependências de Python. Por exemplo, no Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Pacotes do Azure
Depois, instale os pacotes do Python **Azure Batch** e **Armazenamento do Azure**. Pode instalar ambos os pacotes utilizando **pip** e *requirements.txt*, que pode encontrar aqui:

`/azure-batch-samples/Python/Batch/requirements.txt`

Problema ao seguir o comando **pip** para instalar os pacotes Batch e Armazenamento:

`pip install -r requirements.txt`

Em alternativa, pode instalar manualmente os pacotes do Python [azure-batch][pypi_batch] e [azure-storage][pypi_storage]:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Se estiver a utilizar uma conta sem privilégios, poderá ter de utilizar o prefixo `sudo` nos comandos. Por exemplo, `sudo pip install -r requirements.txt`. Para obter mais informações sobre como instalar pacotes Python, veja [Instalar Pacotes][pypi_install] em python.org.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Exemplo de código do tutorial do Batch Python
O exemplo de código do tutorial do Batch Python é constituído por dois scripts Python e alguns ficheiros de dados.

* **python_tutorial_client.py**: interage com os serviços de Batch e Armazenamento para executar uma carga de trabalho paralela em nós de computação (máquinas virtuais). O script *python_tutorial_client.py* é executado na sua estação de trabalho local.
* **python_tutorial_task.py**: o script que é executado em nós do Azure para realizar o trabalho real de computação. No exemplo, *python_tutorial_task.py* analisa o texto num ficheiro transferido a partir do Armazenamento do Azure (o ficheiro de entrada). Em seguida, gera um ficheiro de texto (o ficheiro de saída) que contém uma lista das três palavras principais que aparecem no ficheiro de entrada. Depois de criar o ficheiro de saída, *python_tutorial_task.py* carrega o ficheiro para o Armazenamento do Azure. Isto disponibiliza-o para transferência para o script de cliente em execução na estação de trabalho. O script *python_tutorial_task.py* é executado em paralelo em diversos nós de computação no serviço Batch.
* **./data/taskdata\*. txt**: estes três ficheiros de texto fornecem a entrada para as tarefas que são executadas nos nós de computação.

O diagrama seguinte ilustra as operações primárias que são realizadas pelos scripts de cliente e de tarefas. Este fluxo de trabalho básico é típico de várias soluções de computação que são criadas com o Batch. Embora não demonstrem todas as funcionalidades disponíveis no serviço Batch, quase todos os cenários do Batch incluem partes deste fluxo de trabalho.

![Exemplo de fluxo de trabalho do Batch][8]<br/>

[**Passo 1.**](#step-1-create-storage-containers) Criar **contentores** no Armazenamento de Blobs do Azure.<br/>
[**Passo 2.**](#step-2-upload-task-script-and-data-files) Carregar scripts de tarefas e ficheiros de entrada para contentores.<br/>
[**Passo 3.**](#step-3-create-batch-pool) Criar um **conjunto** do Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** O conjunto **StartTask** transfere o script de tarefas (python_tutorial_task.py) para nós, à medida que se associam ao conjunto.<br/>
[**Passo 4.**](#step-4-create-batch-job) Criar um **trabalho** do Batch.<br/>
[**Passo 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** ao trabalho.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** As tarefas estão agendadas para serem executadas em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarefa transfere os respetivos dados de entrada a partir do Armazenamento do Azure e, em seguida, começa a execução.<br/>
[**Passo 6.**](#step-6-monitor-tasks) Monitorizar tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** À medida que as tarefas são concluídas, carregam os respetivos dados de saída para o Armazenamento do Azure.<br/>
[**Passo 7.**](#step-7-download-task-output) Transferir o resultado da tarefa do Armazenamento.

Conforme mencionado, nem todas as soluções do Batch efetuam estes passos exatos e podem incluir muitos mais, mas este exemplo demonstra processos comuns encontrados numa solução Batch.

## <a name="prepare-client-script"></a>Preparar o script de cliente
Antes de executar o exemplo, adicione as suas credenciais de conta do Batch e Armazenamento ao *python_tutorial_client.py*. Se ainda não o tiver feito, abra o ficheiro no seu editor favorito e atualize as seguintes linhas com as suas credenciais.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
BATCH_ACCOUNT_NAME = ""
BATCH_ACCOUNT_KEY = ""
BATCH_ACCOUNT_URL = ""

# Storage account credentials
STORAGE_ACCOUNT_NAME = ""
STORAGE_ACCOUNT_KEY = ""
```

Pode encontrar as credenciais da conta do Batch e do Armazenamento no painel de conta de cada serviço do [portal do Azure][azure_portal]:

![Credenciais do Batch no portal][9]
![Credenciais do Armazenamento no portal][10]<br/>

Nas secções seguintes, vamos analisar os passos utilizados pelos scripts para processar uma carga de trabalho no serviço Batch. Aconselhamo-lo a consultar regularmente os scripts no seu editor enquanto estiver a trabalhar no resto do artigo.

Navegue para a linha seguinte em **python_tutorial_client.py** para iniciar o Passo 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Passo 1: criar contentores de armazenamento
![Criar contentores no Armazenamento do Azure][1]
<br/>

O Batch inclui suporte incorporado para interagir com o Armazenamento do Azure. Os contentores na sua conta de Armazenamento fornecerão os ficheiros necessários para as tarefas que são executadas na sua conta do Batch. Os contentores fornecem também um local para armazenar os dados de saída que as tarefas produzem. A primeira coisa que o script *python_tutorial_client.py* faz é criar três contentores no [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md#blob-storage):

* **aplicação**: este contentor irá armazenar o script do Python executado pelas tarefas, *python_tutorial_task.py*.
* **entrada**: as tarefas irão transferir os ficheiros de dados a processar a partir do contentor de *entrada*.
* **saída**: quando as tarefas concluírem o processamento do ficheiro de entrada, irão carregar os resultados para o contentor de *saída*.

Para interagir com uma conta de Armazenamento e criar contentores, utilizamos o pacote [azure-storage][pypi_storage] para criar um objeto [BlockBlobService][py_blockblobservice] -- o "cliente do blob". Em seguida, criamos três contentores na conta de Armazenamento com o cliente do blob.

```python
import azure.storage.blob as azureblob

# Create the blob client, for use in obtaining references to
# blob storage containers and uploading files to containers.
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)

# Use the blob client to create the containers in Azure Storage if they
# don't yet exist.
APP_CONTAINER_NAME = 'application'
INPUT_CONTAINER_NAME = 'input'
OUTPUT_CONTAINER_NAME = 'output'
blob_client.create_container(APP_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(INPUT_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(OUTPUT_CONTAINER_NAME, fail_on_exist=False)
```

Depois de os contentores estarem criados, a aplicação pode agora carregar os ficheiros que serão utilizados pelas tarefas.

> [!TIP]
> [Como utilizar o armazenamento de Blobs do Azure a partir do Python](../storage/blobs/storage-python-how-to-use-blob-storage.md) fornece uma boa descrição geral e como trabalhar com blobs e contentores de Armazenamento do Azure. Deve estar perto do topo da sua lista de leitura à medida que começa a trabalhar com o Batch.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>Passo 2: carregar o script da tarefa e ficheiros de dados
![Carregar a aplicação de tarefa e os ficheiros de entrada (dados) para contentores][2]
<br/>

Na operação de carregar ficheiros, primeiro, o *python_tutorial_client.py* define coleções de caminhos de entrada de **aplicação** e **entrada**, conforme existem no computador local. Em seguida, carrega estes ficheiros para os contentores que criou no passo anterior.

```python
# Paths to the task script. This script will be executed by the tasks that
# run on the compute nodes.
application_file_paths = [os.path.realpath('python_tutorial_task.py')]

# The collection of data files that are to be processed by the tasks.
input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                    os.path.realpath('./data/taskdata2.txt'),
                    os.path.realpath('./data/taskdata3.txt')]

# Upload the application script to Azure Storage. This is the script that
# will process the data files, and is executed by each of the tasks on the
# compute nodes.
application_files = [
    upload_file_to_container(blob_client, APP_CONTAINER_NAME, file_path)
    for file_path in application_file_paths]

# Upload the data files. This is the data that will be processed by each of
# the tasks executed on the compute nodes in the pool.
input_files = [
    upload_file_to_container(blob_client, INPUT_CONTAINER_NAME, file_path)
    for file_path in input_file_paths]
```

Ao utilizar a compreensão de lista, a função `upload_file_to_container` é chamada para cada ficheiro nas coleções e as duas coleções [ResourceFile][py_resource_file] são povoadas. A função `upload_file_to_container` aparece abaixo:

```python
def upload_file_to_container(block_blob_client, container_name, path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """

    import datetime
    import azure.storage.blob as azureblob
    import azure.batch.models as batchmodels

    blob_name = os.path.basename(path)

    print('Uploading file {} to container [{}]...'.format(path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
Um [ResourceFile][py_resource_file] fornece tarefas no Batch com o URL para um ficheiro no Armazenamento do Azure que é transferido para um nó de computação antes de essa tarefa ser executada. A propriedade [ResourceFile][py_resource_file].**blob_source** especifica o URL completo do ficheiro, tal como existe no Armazenamento do Azure. O URL também pode incluir uma assinatura de acesso partilhado (SAS) que proporciona acesso seguro ao ficheiro. A maioria dos tipos de tarefas no Batch incluem uma propriedade *ResourceFiles*, incluindo:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

Este exemplo não utiliza os tipos de tarefas JobPreparationTask ou JobReleaseTask, mas pode ler mais sobre os mesmos em [Executar a preparação de trabalhos e tarefas de conclusão nos nós de computação do Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso partilhado (SAS)
As assinaturas de acesso partilhado são cadeias que fornecem acesso seguro aos contentores e blobs do Armazenamento do Azure. O script *python_tutorial_client.py* utiliza as assinaturas de acesso partilhado do blob e do contentor, e demonstra como obter estas cadeias de assinatura de acesso partilhado a partir do serviço de Armazenamento.

* **Assinaturas de acesso partilhado do blob**: o StartTask do conjunto utiliza assinaturas de acesso partilhado do blob quando transfere o script de tarefas e os ficheiros de dados de entrada a partir do Armazenamento (veja o [Passo 3](#step-3-create-batch-pool) abaixo). A função `upload_file_to_container` em *python_tutorial_client.py* contém o código que obtém a assinatura de acesso partilhado de cada blob. Isto é feito chamando [BlockBlobService.make_blob_url][py_make_blob_url] no módulo de Armazenamento.
* **Assinatura de acesso partilhado do contentor**: à medida que cada tarefa conclui o respetivo trabalho no nó de computação, carrega o ficheiro de saída para o contentor de *saída* no Armazenamento do Azure. Para fazê-lo, o *python_tutorial_task.py* utiliza uma assinatura de acesso partilhado do contentor, que fornece acesso de escrita ao contentor. A função `get_container_sas_token` em *python_tutorial_client.py* obtém a assinatura de acesso partilhado do contentor, que é então transmitida como um argumento da linha de comandos para as tarefas. O Passo 5, [Adicionar tarefas a um trabalho](#step-5-add-tasks-to-job), aborda a utilização do SAS do contentor.

> [!TIP]
> Consulte a série de duas partes sobre assinaturas de acesso partilhado, [Parte 1: compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) e [Parte 2: criar e utilizar um SAS com o serviço Blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro aos dados da sua conta de Armazenamento.
>
>

## <a name="step-3-create-batch-pool"></a>Passo 3: criar conjunto do Batch
![Criar um conjunto do Batch][3]
<br/>

Um **conjunto** do Batch é uma coleção de nós de computação (máquinas virtuais) nos quais o Batch executa tarefas de um trabalho.

Depois de carregar o script da tarefa e os ficheiros de dados para a conta de Armazenamento, o *python_tutorial_client.py* inicia a interação com o serviço Batch, utilizando o módulo Batch Python. Para fazê-lo, é criado um [BatchServiceClient][py_batchserviceclient]:

```python
# Create a Batch service client. We'll now be interacting with the Batch
# service in addition to Storage.
credentials = batchauth.SharedKeyCredentials(BATCH_ACCOUNT_NAME,
                                             BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL)
```

Em seguida, é criado um conjunto de nós de computação na conta do Batch, com uma chamada para `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Quando cria um conjunto, define um [PoolAddParameter][py_pooladdparam] que especifica várias propriedades para o conjunto:

* **ID** do conjunto de (*id* - necessário)<p/>Tal como com a maior parte das entidades no Batch, o seu novo conjunto tem de ter um ID exclusivo dentro da sua conta do Batch. O código refere-se a este conjunto com o respetivo ID, sendo assim que identifica o conjunto no [portal][azure_portal] do Azure.
* **Número de nós de computação** (*target_dedicated* - necessário)<p/>Esta propriedade especifica quantas VMs devem ser implementadas no conjunto. É importante que tenha em atenção que todas as contas do Batch têm uma **quota** predefinida que limita o número de **núcleos** (e, portanto, dos nós de computação) numa conta do Batch. Pode encontrar as quotas predefinidas e instruções sobre como [aumentar uma quota](batch-quota-limit.md#increase-a-quota) (por exemplo, o número máximo de núcleos na sua conta do Batch) em [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md). Caso se questione "Por que motivo o meu conjunto não alcança mais de X nós?" esta quota de núcleos pode ser a causa.
* **Sistema operativo** para nós (*virtual_machine_configuration* **ou** *cloud_service_configuration* - necessário)<p/>Em *python_tutorial_client.py*, criamos um conjunto de nós do Linux com uma [VirtualMachineConfiguration][py_vm_config]. A função `select_latest_verified_vm_image_with_node_agent_sku` em `common.helpers` permite trabalhar mais facilmente com imagens do [Marketplace das Máquinas Virtuais do Azure][vm_marketplace]. Veja [Provision Linux compute nodes in Azure Batch pools (Aprovisionar nós de computação do Linux em conjuntos do Azure Batch)](batch-linux-nodes.md) para obter mais informações sobre a utilização de imagens do Marketplace.
* **Tamanho dos nós de computação** (*vm_size* - necessário)<p/>Uma vez que vamos especificar nós do Linux para [VirtualMachineConfiguration][py_vm_config], especificamos um tamanho de VM (`STANDARD_A1` neste exemplo) a partir de [Tamanhos de máquinas virtuais no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Novamente, veja [Aprovisionar nós de computação do Linux em conjuntos do Azure Batch](batch-linux-nodes.md) para obter mais informações.
* **Tarefa de início** (*start_task* - não é necessário)<p/>Juntamente com as propriedades de nó físico anteriores, também pode especificar um [StartTask][py_starttask] para o conjunto (não é necessário). O StartTask é executado em cada nó à medida que esse nó se associa ao conjunto, e sempre que um nó for reiniciado. O StartTask é especialmente útil para preparar nós de computação para a execução de tarefas, como instalar as aplicações que as suas tarefas executam.<p/>Nesta aplicação de exemplo, o StartTask copia os ficheiros que transfere do Armazenamento (que são especificados com a propriedade **resource_files**) do StartTask, a partir do *diretório de trabalho*do StartTask para o diretório *partilhado* a que todas as tarefas em execução no nó podem aceder. Essencialmente, esta ação copia o `python_tutorial_task.py` para o diretório partilhado em cada nó, à medida que o nó se associa ao conjunto, para que quaisquer tarefas executadas no nó lhe consigam aceder.

Repare na chamada para a função auxiliar `wrap_commands_in_shell`. Esta função aceita um conjunto de comandos separados e cria uma única linha de comandos adequada à propriedade de linha de comandos de uma tarefa.

A utilização de duas variáveis de ambiente na propriedade **command_line** do StartTask, `AZ_BATCH_TASK_WORKING_DIR` e `AZ_BATCH_NODE_SHARED_DIR`, é também visível no fragmento de código acima. Cada nó de computação num conjunto do Batch é automaticamente configurado com várias variáveis de ambiente que são específicas do Batch. Qualquer processo executado por uma tarefa tem acesso a estas variáveis de ambiente.

> [!TIP]
> Para obter mais informações sobre as variáveis de ambiente que estão disponíveis nos nós de computação de um conjunto do Batch, bem como informações sobre os diretórios de trabalho de tarefas, veja **Definições de ambiente para tarefas** e **Ficheiros e diretórios** na [descrição geral das funcionalidades do Azure Batch](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Passo 4: criar trabalho do Batch
![Criar trabalho do Batch][4]<br/>

Um **trabalho** do Batch é uma coleção de tarefas e está associado a um conjunto de nós de computação. As tarefas num trabalho são executadas nos nós de computação do conjunto associado.

Pode utilizar um trabalho não só para organizar e controlar tarefas em cargas de trabalho relacionadas, mas também para impor determinadas restrições, como o tempo de execução máximo para a tarefa (e, por extensão, as tarefas), e a prioridade do trabalho em relação a outros trabalhos da conta do Batch. Neste exemplo, no entanto, o trabalho está associado apenas ao conjunto que foi criado no passo 3. Não estão configuradas propriedades adicionais.

Todos os trabalhos do Batch estão associados a um conjunto específico. Esta associação indica os nós nos quais as tarefas do trabalho são executadas. Especifique o conjunto com a propriedade [PoolInformation][py_poolinfo], conforme ilustrado no fragmento de código abaixo.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Agora que criou um trabalho, as tarefas são adicionadas para realizar o trabalho.

## <a name="step-5-add-tasks-to-job"></a>Passo 5: adicionar tarefas ao trabalho
![Adicionar tarefas ao trabalho][5]<br/>
*(1) As tarefas são adicionadas ao trabalho, (2) as tarefas são agendadas para serem executadas em nós e (3) as tarefas transferem os ficheiros de dados a processar*

As **tarefas** do Batch são as unidades de trabalho individuais que são executadas nos nós de computação. Uma tarefa tem uma linha de comandos e executa os scripts ou executáveis que especificar nessa linha de comandos.

Para trabalhar de facto, as tarefas têm de ser adicionadas a um trabalho. Cada [CloudTask][py_task] está configurado com uma propriedade de linha de comandos e [ResourceFiles][py_resource_file] (tal como com o StartTask do conjunto) que a tarefa transfere para o nó antes de a linha de comandos ser executada automaticamente. No exemplo, cada tarefa processa apenas um ficheiro. Assim, a respetiva coleção ResourceFiles contém um único elemento.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> Quando acedem a variáveis de ambiente, como `$AZ_BATCH_NODE_SHARED_DIR`, ou executam uma aplicação que não está no `PATH` do nó, as linhas de comandos de tarefas devem invocar a shell explicitamente, assim como em `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Este requisito é desnecessário se as suas tarefas estiverem a executar uma aplicação no nó `PATH` e não fazem referência a quaisquer variáveis de ambiente.
>
>

Dentro do ciclo `for` no fragmento de código acima, pode ver que a linha de comandos da tarefa está construída com cinco argumentos da linha de comandos que são transmitidos para *python_tutorial_task.py*:

1. **FilePath**: este é o caminho local para o ficheiro, conforme existe no nó. Quando o objeto ResourceFile `upload_file_to_container` foi criado no Passo 2 acima, o nome do ficheiro foi utilizado para esta propriedade (o parâmetro `file_path` no construtor ResourceFile). Isto indica que o ficheiro pode ser encontrado no mesmo diretório no nó de *python_tutorial_task.py*.
2. **numwords**: as palavras *N* na parte superior devem ser escritas para o ficheiro de saída.
3. **storageaccount**: o nome da conta de Armazenamento que é proprietária do contentor para a qual o resultado da tarefa deve ser carregado.
4. **storagecontainer**: o nome do contentor de Armazenamento para o qual os ficheiros de saída devem ser carregados.
5. **sastoken**: a assinatura de acesso partilhado (SAS) que fornece acesso de escrita ao contentor de **saída** no Armazenamento do Azure. O script *python_tutorial_task.py* utiliza esta assinatura de acesso partilhado quando cria a referência BlockBlobService. Isto fornece acesso de escrita ao contentor sem necessidade de uma chave de acesso para a conta de armazenamento.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Passo 6: monitorizar tarefas
![Monitorizar tarefas][6]<br/>
*O script (1) monitoriza as tarefas de estado de conclusão e (2) as tarefas carregam os dados dos resultados para o Armazenamento do Azure*

Quando as tarefas são adicionadas a um trabalho, são colocadas automaticamente em fila de espera e agendadas para execução em nós de computação dentro do conjunto associado ao trabalho. Com base nas definições especificadas, o Batch processa todas as tarefas de colocação em fila, agendamento, repetições e outras funções de administração de tarefas por si.

Existem várias abordagens à monitorização da execução de tarefas. A função `wait_for_tasks_to_complete` em *python_tutorial_client.py* fornece um exemplo simples de tarefas de monitorização para um determinado estado, neste caso, o estado [concluído][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Passo 7: transferir o resultado da tarefa
![Transferir o resultado da tarefa do Armazenamento][7]<br/>

Agora que o trabalho está concluído, o resultado das tarefas pode ser transferido do Armazenamento do Azure. Isto é feito através de uma chamada para o `download_blobs_from_container` no *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> A chamada para o `download_blobs_from_container` no *python_tutorial_client.py* especifica que os ficheiros devem ser transferidos para o seu diretório principal. Pode modificar esta localização de saída.
>
>

## <a name="step-8-delete-containers"></a>Passo 8: eliminar contentores
Uma vez que os dados que residem no Armazenamento do Azure lhe são cobrados, é sempre boa ideia remover blobs que já não são necessários para as tarefas do Batch. Em *python_tutorial_client.py*, isto é feito com três chamadas para [BlockBlobService.delete_container][py_delete_container]:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Passo 9: eliminar o trabalho e o conjunto
No passo final, é-lhe pedido para eliminar o trabalho e o conjunto que foram criados pelo script *python_tutorial_client.py*. Apesar de os próprios trabalhos e tarefas não lhe serem cobrados, os nós de computação *são* cobrados. Assim, recomendamos que atribua nós apenas conforme necessário. A eliminação de conjuntos não utilizados pode fazer parte do processo de manutenção.

O [JobOperations][py_job] e o [PoolOperations][py_pool] do BatchServiceClient têm métodos de eliminação correspondentes, que são chamados se confirmar a eliminação:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Tenha em atenção que os recursos de computação são-lhe cobrados - a eliminação dos conjuntos não utilizados irá minimizar os custos. Além disso, tenha em atenção que eliminar um conjunto elimina todos os nós de computação nesse conjunto, e que quaisquer dados nos nós serão irrecuperáveis depois de o conjunto ser eliminado.
>
>

## <a name="run-the-sample-script"></a>Executar a aplicação de exemplo
Quando executa o script *python_tutorial_client.py* a partir do tutorial [exemplo de código][github_article_samples], o resultado da consola é semelhante ao seguinte. Há uma pausa no `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` enquanto os nós de computação do conjunto são criados, iniciados, e os comandos na tarefa de início do conjunto são executados. Utilize o [portal do Azure][azure_portal] para monitorizar o conjunto, nós de computação, o trabalho e as tarefas durante e após a execução. Utilize o [portal do Azure][azure_portal] ou o [Explorador de Armazenamento do Microsoft Azure][storage_explorer] para ver os recursos de Armazenamento (contentores e blobs) que são criados pela aplicação.

> [!TIP]
> Execute o script *python_tutorial_client.py* a partir do diretório `azure-batch-samples/Python/Batch/article_samples`. Utiliza um caminho relativo para a importação do módulo `common.helpers`, pelo que poderá ver `ImportError: No module named 'common'` se não executar o script a partir deste diretório.
>
>

O tempo de execução típico é de, **aproximadamente, cinco a sete minutos** quando executa o exemplo na configuração predefinida.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Passos seguintes
Pode efetuar alterações em *python_tutorial_client.py* e *python_tutorial_task.py* para testar cenários de computação diferentes. Por exemplo, tente adicionar um atraso de execução a *python_tutorial_task.py* para simular tarefas demoradas e monitorizá-las no portal. Experimente adicionar mais tarefas ou ajustar o número de nós de computação. Adicione lógica para procurar e permitir a utilização de um conjunto existente para acelerar o tempo de execução.

Agora que está familiarizado com o fluxo de trabalho básico de uma solução do Batch, está na altura de aprofundar as funcionalidades adicionais do serviço Batch.

* Reveja o artigo [Descrição geral das funcionalidades do Azure Batch](batch-api-basics.md), que recomendamos se não estiver familiarizado com o serviço.
* Comece pelos outros artigos de desenvolvimento do Batch em **Desenvolvimento aprofundado** no [percurso de aprendizagem do Batch][batch_learning_path].
* Veja uma implementação diferente de processamento da carga de trabalho "palavras N principais" com o Batch no exemplo [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Criar contentores no Armazenamento do Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Carregar a aplicação de tarefa e os ficheiros de entrada (dados) para contentores"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Criar um conjunto do Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Criar um trabalho do Batch"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Adicionar tarefas ao trabalho"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Monitorizar tarefas"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Transferir o resultado da tarefa do Armazenamento"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Fluxo de trabalho da solução Batch (diagrama completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenciais do Batch no Portal"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenciais do Armazenamento no Portal"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Fluxo de trabalho da solução Batch (diagrama mínimo)"
