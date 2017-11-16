---
title: "Referência da interface de linha de comandos de gestão de modelo do Machine Learning do Azure | Microsoft Docs"
description: "Referência da interface de linha de comandos de gestão de modelo do Machine Learning do Azure."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 373abb8f40a8acf557b7cd4a0d0b3fb55f4a545c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="model-management-command-line-interface-reference"></a>Referência de interface de linha de comandos de gestão de modelo

## <a name="base-cli-concepts"></a>Conceitos do CLI base:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Comandos de conta
É necessária uma conta de gestão de modelo a utilizar os serviços, que lhe permitem implementar e gerir modelos. Utilize `az ml account modelmanagement -h` para ver a lista seguinte:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Criar conta de gestão de modelo**

Crie uma conta de gestão de modelo utilizando o seguinte comando. Esta conta será utilizada para faturação.

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Argumentos locais:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Comandos de ambiente

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Configurar o ambiente de implementação**

O comando de configuração requer que tenha acesso de Contribuidor na subscrição. Se não tiver que, pelo menos precisa de acesso de contribuinte ao grupo de recursos que está a implementar. Para fazer a última opção, tem de especificar o nome do grupo de recursos como parte do comando de configuração utilizando `-g` o sinalizador. 

Existem duas opções para a implementação: *local* e *cluster*. Definir o `--cluster` (ou `-c`) sinalizador permite a implementação de cluster, o que aprovisiona um cluster de ACS. A sintaxe de configuração básica é o seguinte:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Isto inicia do Azure machine learning ambiente com uma conta de armazenamento, o registo ACR e o serviço de informações de aplicação criado na sua subscrição. Por predefinição, o ambiente é inicializado para locais apenas implementações (nenhum ACS) se não for especificado nenhum sinalizador. Se precisar de dimensionar o serviço, especifique o `--cluster` (ou `-c`) sinalizador para criar um cluster de ACS.

Detalhes do comando:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Will be created if it does not exist.
                                     If not provided, resource group will be created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command will fail if user is not logged in.

Argumentos global
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Comandos de modelo

    list
    register
    show

**Registar um modelo**

Comando para registar o modelo.

`az ml model register --model [path to model file] --name [model name]`

Detalhes do comando:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Argumentos global

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Comandos de manifesto

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Criar o manifesto**

Cria um ficheiro de manifesto para o modelo. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Detalhes do comando:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Argumentos de modelo registado

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Argumentos de anular o registo de modelo

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Argumentos global

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Comandos de imagem

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Criar imagem**

Pode criar uma imagem com a opção de ter criado o manifesto antes. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Ou pode criar o manifesto e da imagem com um único comando. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Detalhes do comando:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Argumentos de manifesto registados

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Anular o registo de argumentos manifesto

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Comandos de serviço
São suportados os seguintes comandos para o serviço. Para ver os parâmetros para cada comando, utilize a opção -h. Por exemplo, utilizar `az ml service create realtime -h` para ver detalhes do comando de criar.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Criar um serviço**

Para criar um serviço com uma imagem criada anteriormente, utilize o seguinte comando:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Para criar um serviço, o manifesto e imagem com um comando único, utilizam o seguinte comando:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Detalhes de comandos:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Argumentos de imagem registado

    --image-id                        : [Required] Image to deploy to the service.

Argumentos de imagem anular o registo

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Argumentos global

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Tenha em atenção no `-d` sinalizador para anexar dependências: se a transmitir o nome de um diretório que não se encontre incluídas (zip, tar, etc.), esse directório automaticamente obtém tar'ed e é transferido along, em seguida, automaticamente unbundled na outra extremidade. 

Se passar num diretório que já está agrupado, iremos processá-la como um ficheiro e transmiti-lo ao longo que é. Não será unbundled automaticamente; é esperado para processar que no seu código.

**Obter os detalhes do serviço**

Obter os detalhes do serviço, incluindo o URL, utilização (incluindo dados de exemplo, se tiver sido criado um esquema).

`az ml service show realtime --name [service name]`

Detalhes do comando:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argumentos global

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Executar o serviço**

`az ml service run realtime -n [service name] -d [input_data]`

Detalhes do comando:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argumentos global

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Comando

    az ml service run realtime

Argumentos – id -i: [necessário] o id de serviço para pontuar contra.
-d: os dados a utilizar para chamar o serviço web.
-v: sinalizador verbosidade.

Argumentos global

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
