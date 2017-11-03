---
title: "Saiba como gerir os serviços web do AzureML utilizando a gestão de API | Microsoft Docs"
description: "Um guia que mostra como gerir os serviços web do AzureML utilizar a API Management."
keywords: "aprendizagem automática, gestão de api"
services: machine-learning
documentationcenter: 
author: roalexan
manager: jhubbard
editor: 
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: roalexan
ms.openlocfilehash: 53a6b18fb74db46ccb66c7c70851a9bf364e927c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Saiba como gerir os serviços Web do Azure ML com a Gestão de API
## <a name="overview"></a>Descrição geral
Este guia mostra como começar rapidamente a utilizar a API Management para gerir os serviços web do AzureML.

## <a name="what-is-azure-api-management"></a>O que é a API Management do Azure?
Gestão de API do Azure é um serviço Azure que lhe permite gerir os pontos finais da API de REST através da definição de acesso de utilizador, a limitação de utilização e a monitorização do dashboard. Clique em [aqui](https://azure.microsoft.com/services/api-management/) para obter detalhes sobre a API Management do Azure. Clique em [aqui](../../api-management/api-management-get-started.md) para obter um guia sobre como começar a utilizar com a API Management do Azure. Este outro guia que este guia baseia-se no, abrange mais tópicos, incluindo configurações de notificação, escalão de preço, processamento de resposta, a autenticação de utilizador, criação de produtos, subscrições de programador e dashboarding de utilização.

## <a name="what-is-azureml"></a>O que é AzureML?
AzureML é um serviço do Azure do machine learning que lhe permite facilmente criar, implementar e partilhar as soluções de análise avançadas. Clique em [aqui](https://azure.microsoft.com/services/machine-learning/) para obter detalhes sobre AzureML.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia, tem de:

* Uma conta do Azure. Se não tiver uma conta do Azure, clique em [aqui](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes sobre como criar uma conta de avaliação gratuita.
* Uma conta do AzureML. Se não tiver uma conta do AzureML, clique em [aqui](https://studio.azureml.net/) para obter detalhes sobre como criar uma conta de avaliação gratuita.
* A área de trabalho, o serviço e o api_key para uma experimentação do AzureML implementada como um serviço web. Clique em [aqui](create-experiment.md) para obter detalhes sobre como criar uma experimentação do AzureML. Clique em [aqui](publish-a-machine-learning-web-service.md) para obter detalhes sobre como implementar um AzureML experimentação como um serviço web. Em alternativa, o apêndice A tem instruções sobre como criar e testar uma experimentação do AzureML simples e implementá-lo como um serviço web.

## <a name="create-an-api-management-instance"></a>Criar uma instância da API Management
Seguem-se os passos para utilizar a API Management para gerir o seu serviço web do AzureML. Em primeiro lugar, crie uma instância de serviço. Inicie sessão no [Portal clássico](https://manage.windowsazure.com/) e clique em **novo** > **serviços aplicacionais** > **API Management**  >  **Criar**.

![criar a instância](./media/manage-web-service-endpoints-using-api-management/create-instance.png)

Especifique um único **URL**. Este guia utiliza **demoazureml** – terá de escolher algo diferente. Selecione a **Subscrição** e a **Região** pretendidas para a sua instância de serviço. Depois de efetuar as seleções, clique no botão seguinte.

![Criar-service-1](./media/manage-web-service-endpoints-using-api-management/create-service-1.png)

Especifique um valor para o **nome da organização**. Este guia utiliza **demoazureml** – terá de escolher algo diferente. Introduza o seu endereço de e-mail no **e-mail do administrador** campo. Este endereço de e-mail é utilizado para notificações do sistema da API Management.

![criar serviço-2](./media/manage-web-service-endpoints-using-api-management/create-service-2.png)

Clique na caixa de verificação para criar a instância de serviço. *Demora até trinta minutos para um novo serviço que sejam criados*.

## <a name="create-the-api"></a>Criar a API
Uma vez criada a instância de serviço, o passo seguinte consiste em criar a API. Uma API consiste num conjunto de operações que podem ser invocadas a partir de uma aplicação cliente. As operações de API são suportadas com um proxy para serviços Web existentes. Este guia cria APIs se proxy aos serviços web existentes AzureML RRS e BES.

As APIs são criadas e configuradas a partir do portal do publicador da API, que é acedido através do Portal clássico do Azure. Para aceder ao portal do publicador, selecione a instância de serviço.

![instância de serviço selecione](./media/manage-web-service-endpoints-using-api-management/select-service-instance.png)

Clique em **gerir** no Portal clássico do Azure para o seu serviço de API Management.

![Gerir-service](./media/manage-web-service-endpoints-using-api-management/manage-service.png)

Clique em **APIs** do **API Management** menu à esquerda e, em seguida, clique em **adicionar API**.

![menu de gestão de API](./media/manage-web-service-endpoints-using-api-management/api-management-menu.png)

Tipo **API de demonstração do AzureML** como o **nome da Web API**. Tipo **https://ussouthcentral.services.azureml.net** como o **URL do serviço Web**. Tipo **azureml demonstração** como o **sufixo do URL da API Web**. Verifique **HTTPS** como o **URL da API Web** esquema. Selecione **Starter** como **produtos**. Quando terminar, clique em **guardar** para criar a API.

![Adicionar-nova-api](./media/manage-web-service-endpoints-using-api-management/add-new-api.png)

## <a name="add-the-operations"></a>Adicione as operações
Clique em **adicionar operação** adicionar operações a esta API.

![operação adicionar](./media/manage-web-service-endpoints-using-api-management/add-operation.png)

O **nova operação** janela será apresentada e o **assinatura** separador será selecionado por predefinição.

## <a name="add-rrs-operation"></a>Adicionar operação de RRS
Em primeiro lugar, crie uma operação para o serviço de RRS do AzureML. Selecione **POST** como o **verbo HTTP**. Tipo **/services/ /workspaces/ {área de trabalho} {serviço} / executar? api-version = {apiversion} & Detalhes = {Detalhes}** como o **modelo URL**. Tipo **RRS executar** como o **nome a apresentar**.

![Adicionar-rrs-operação-assinatura](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Clique em **respostas** > **adicionar** à esquerda e selecione **200 OK**. Clique em **guardar** para guardar esta operação.

![Adicionar-rrs--resposta da operação](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Adicionar operações BES
Capturas de ecrã não estão incluídas para as operações de BES como são muito semelhantes às de adicionar a operação de RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Submeter (mas não iniciar) uma tarefa de execução de lote
Clique em **adicionar operação** para adicionar a operação do AzureML BES para a API. Selecione **POST** para o **verbo HTTP**. Tipo **/services/ /workspaces/ {área de trabalho} {serviço} / tarefas? api-version = {apiversion}** para o **modelo URL**. Tipo **BES submeter** para o **nome a apresentar**. Clique em **respostas** > **adicionar** à esquerda e selecione **200 OK**. Clique em **guardar** para guardar esta operação.

### <a name="start-a-batch-execution-job"></a>Iniciar uma tarefa de execução de lote
Clique em **adicionar operação** para adicionar a operação do AzureML BES para a API. Selecione **POST** para o **verbo HTTP**. Tipo **/workspaces/ {área de trabalho} /services/ {serviço} /jobs/ {jobid} / iniciar? api-version = {apiversion}** para o **modelo URL**. Tipo **BES iniciar** para o **nome a apresentar**. Clique em **respostas** > **adicionar** à esquerda e selecione **200 OK**. Clique em **guardar** para guardar esta operação.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obter o estado ou o resultado de uma tarefa de execução de lote
Clique em **adicionar operação** para adicionar a operação do AzureML BES para a API. Selecione **obter** para o **verbo HTTP**. Tipo **/workspaces/ {área de trabalho} /services/ {serviço} /jobs/ {jobid}? api-version = {apiversion}** para o **modelo URL**. Tipo **BES estado** para o **nome a apresentar**. Clique em **respostas** > **adicionar** à esquerda e selecione **200 OK**. Clique em **guardar** para guardar esta operação.

### <a name="delete-a-batch-execution-job"></a>Eliminar uma tarefa de execução de lote
Clique em **adicionar operação** para adicionar a operação do AzureML BES para a API. Selecione **eliminar** para o **verbo HTTP**. Tipo **/workspaces/ {área de trabalho} /services/ {serviço} /jobs/ {jobid}? api-version = {apiversion}** para o **modelo URL**. Tipo **BES eliminar** para o **nome a apresentar**. Clique em **respostas** > **adicionar** à esquerda e selecione **200 OK**. Clique em **guardar** para guardar esta operação.

## <a name="call-an-operation-from-the-developer-portal"></a>Chamar uma operação a partir do Portal do Programador
Operações podem ser chamadas diretamente a partir do portal do programador, que fornece uma maneira conveniente para ver e testar as operações de uma API. Neste passo guia irá chamar o **RRS executar** método que foi adicionado para o **API de demonstração do AzureML**. Clique em **portal do programador** no menu na parte superior direita do Portal clássico.

![portal do Programador](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

Clique em **APIs** no menu superior e, em seguida, clique em **API de demonstração do AzureML** para ver as operações disponíveis.

![api de demoazureml](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Selecione **RRS executar** para a operação. Clique em **experimente**.

![Tente-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

Para os parâmetros do pedido, introduza o seu **área de trabalho**, **serviço**, **2.0** para o **apiversion**, e **true**para o **detalhes**. Pode encontrar o **área de trabalho** e **serviço** no dashboard de serviço web do AzureML (consulte **testar o serviço web** no anexo D).

Para os cabeçalhos de pedido, clique em **Adicionar cabeçalho** e tipo **Content-Type** e **application/json**, em seguida, clique em **Adicionar cabeçalho** e tipo  **Autorização** e **portador <YOUR AZUREML SERVICE API-KEY>** . Pode encontrar o **chave de api** no dashboard de serviço web do AzureML (consulte **testar o serviço web** no anexo D).

Tipo **{"Entradas": {"input1": {"ColumnNames": ["Col2"] "valores": [["Este é um dia boa"]]}}, "GlobalParameters": {}}** para o corpo do pedido.

![api de demonstração de azureml](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Clique em **enviar**.

![enviar](./media/manage-web-service-endpoints-using-api-management/send.png)

Após uma operação ser invocada, o portal do programador apresenta o **URL solicitado** do serviço de back-end, o **estado de resposta**, a **cabeçalhos de resposta**e quaisquer  **Conteúdo da resposta**.

![Estado de resposta](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Apêndice A – criar e testar um AzureML simple serviço web
### <a name="creating-the-experiment"></a>Criar a experimentação
Seguem-se os passos para criar uma experimentação do AzureML simples e implementá-lo como um serviço web. O guia de serviço web, como uma coluna de texto arbitrário de entrada e devolve um conjunto de funcionalidades representado como números inteiros. Por exemplo:

| Texto | Hash de texto |
| --- | --- |
| Esta é um boa dia |1 1 2 2 0 2 0 1 |

Em primeiro lugar, utilizando um browser à sua escolha, navegue para: [https://studio.azureml.net/](https://studio.azureml.net/) e introduza as suas credenciais para iniciar sessão. Em seguida, crie uma nova experimentação em branco.

![modelos de experiência de pesquisa](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Mude o nome para **SimpleFeatureHashingExperiment**. Expanda **conjuntos de dados guardado** e arraste **livro revisões da Amazon** na sua experimentação.

![simples-funcionalidade-hash-experimentação](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expanda **transformação de dados** e **manipulação** e arraste **selecionar colunas no conjunto de dados** na sua experimentação. Ligar **livro revisões da Amazon** para **selecionar colunas no conjunto de dados**.

![selecionar colunas](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Clique em **selecionar colunas no conjunto de dados** e, em seguida, clique em **Seletor de coluna** e selecione **Col2**. Clique na marca de verificação para aplicar estas alterações.

![selecionar colunas](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **análise de texto** e arraste **funcionalidade hash** para a experimentação. Ligar **selecionar colunas no conjunto de dados** para **funcionalidade hash**.

![ligar--colunas do projeto](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Tipo **3** para o **hash bitsize**. Esta ação irá criar 8 (23) colunas.

![bitsize de codificar](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Neste momento, pode querer clique **executar** para testar a experimentação.

![executar](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Criar um serviço Web
Crie um serviço web. Expanda **serviço Web** e arraste **entrada** na sua experimentação. Ligar **entrada** para **funcionalidade hash**. Arraste também **saída** na sua experimentação. Ligar **saída** para **funcionalidade hash**.

![saída-para-funcionalidade-hash](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Clique em **publicar o serviço web**.

![publicar-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Clique em **Sim** para publicar a experimentação.

![Sim para publicar](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testar o serviço web
Um serviço web do AzureML é composta por, o RSS (serviço de pedido/resposta) e os pontos finais do BES (serviço de execução do batch). O RSS é para execução síncrona. BES destina-se a execução da tarefa assíncrona. Para testar o seu serviço web com a origem de Python de exemplo abaixo, poderá ter de transferir e instalar o Azure SDK para Python (consulte o artigo: [como instalar o Python](../../python-how-to-install.md)).

Também terá do **área de trabalho**, **serviço**, e **api_key** da sua experimentação para a origem de exemplo abaixo. Pode encontrar a área de trabalho e o serviço clicando **pedido/resposta** ou **de execução de lote** para sua experimentação no dashboard do serviço web.

![localizar-área de trabalho-e-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Pode encontrar o **api_key** clicando sua experimentação no dashboard do serviço web.

![localizar a chave de api](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Ponto final RRS de teste
##### <a name="test-button"></a>Botão Testar
Uma forma fácil para testar o ponto final RRS consiste em clicar em **testar** no dashboard do serviço web.

![Teste](./media/manage-web-service-endpoints-using-api-management/test.png)

Tipo **esta é um boa dia** para **col2**. Clique na marca de verificação.

![introdução de dados](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Verá algo semelhante ao seguinte

![saída de exemplo](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Código de exemplo
Outra forma de testar a sua RRS é a partir do seu código de cliente. Se clicar em **pedido/resposta** no dashboard e desloque-se na parte inferior, irá ver o código de exemplo para c#, Python e R. Também poderá ver a sintaxe do pedido RRS, incluindo o pedido de URI, cabeçalhos e corpo.

Este guia mostra um exemplo de Python do trabalho. Terá de modificá-lo com o **área de trabalho**, **serviço**, e **api_key** da sua experimentação.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>Ponto final de BES de teste
Clique em **execução de lote** no dashboard e desloque-se na parte inferior. Irá ver o código de exemplo para c#, Python e R. Também poderá ver a sintaxe dos pedidos BES para submeter uma tarefa, iniciar uma tarefa, obter o estado ou resultados de uma tarefa e eliminar uma tarefa.

Este guia mostra um exemplo de Python do trabalho. Terá de modificá-lo com o **área de trabalho**, **serviço**, e **api_key** da sua experimentação. Além disso, terá de modificar o **nome da conta de armazenamento**, **chave da conta de armazenamento**, e **nome do contentor de armazenamento**. Por último, terá de modificar a localização do **ficheiro de entrada** e a localização do **ficheiro de saída**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
