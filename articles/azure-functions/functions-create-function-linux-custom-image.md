---
title: "Criar uma função no Linux utilizando uma imagem personalizada (pré-visualização) | Microsoft Docs"
description: "Saiba como criar funções do Azure em execução numa imagem personalizada do Linux."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/08/2017
ms.topic: article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 40fbbd0ec020320b26ed343aec8ac31d60b646dc
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Criar uma função no Linux utilizando uma imagem personalizada (pré-visualização)

As funções do Azure permite-lhe alojar as suas funções no Linux no seu próprio contentor personalizado. Esta funcionalidade está atualmente em pré-visualização. Também pode [anfitrião num contentor predefinido App Service do Azure](functions-create-first-azure-function-azure-cli-linux.md).  

Neste tutorial, irá aprender a implementar uma aplicação de função como uma imagem personalizada do Docker. Este padrão é útil se precisar de personalizar a imagem de contentor do serviço de aplicações incorporada. Poderá utilizar uma imagem personalizada quando as suas funções tem uma versão de idioma específico ou exigem uma dependência específica ou a configuração não foi fornecida na imagem incorporada.

Este tutorial explica como utilizar as funções do Azure para criar e emitir uma imagem personalizada para o Hub de Docker. Em seguida, utilizar esta imagem como a origem de implementação para uma aplicação de função que é executado no Linux. Pode utilizar o Docker para criar e emitir a imagem. Pode utilizar a CLI do Azure para criar uma aplicação de função e implementar a imagem do Hub de Docker. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma imagem personalizada utilizando o Docker.
> * Publica uma imagem personalizada para um registo de contentor. 
> * Crie uma conta de armazenamento do Azure. 
> * Crie um plano de serviço de aplicações do Linux. 
> * Implemente uma aplicação de função do Hub de Docker.
> * Adicione as definições da aplicação para a aplicação de função. 

São suportados os seguintes passos num computador Mac, Windows ou Linux.  

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* [Git](https://git-scm.com/downloads)
* Um Active Directory [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* A [conta Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela de terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local, em seguida, mude para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Compilar a imagem do ficheiro Docker

Neste repositório de Git, observe o _Dockerfile_. Este ficheiro descreve o ambiente que é necessário para executar a aplicação de função no Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Quando uma imagem de um registo de contentor privada de alojamento, deve adicionar as definições de ligação para a aplicação de função utilizando **ENV** variáveis no Dockerfile. Uma vez que este tutorial não é possível garantir a utilização de um registo privado, as definições de ligação são [adicionado após a implementação através da CLI do Azure](#configure-the-function-app) como melhor prática de segurança.   

### <a name="run-the-build-command"></a>Execute o comando de compilação
Para compilar a imagem do Docker, execute o `docker build` de comandos e forneça um nome, `mydockerimage`e etiqueta, `v1.0.0`. Substitua `<docker-id>` com o seu Hub de Docker conta ID.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

O comando produz o resultado semelhante ao seguinte:

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testar a imagem localmente
Certifique-se de que a imagem incorporada funciona ao executar a imagem de Docker num contentor de local. Problema de [docker run](https://docs.docker.com/engine/reference/commandline/run/) de comandos e passar o nome e a etiqueta da imagem ao mesmo. É necessário especificar a porta a utilizar o `-p` argumento.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução num contentor de Docker local, certifique-se a aplicação de função e um contentor estão a funcionar corretamente, navegando até <http://localhost:8080>.

![Teste a aplicação de função localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Depois de hav verificada a aplicação de função no contentor, pare a execução. Agora, pode emitir a imagem personalizada para a sua conta do Hub de Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Enviar a imagem personalizada para o Hub de Docker

Um registo é uma aplicação que aloja as imagens e fornece serviços de imagem e contentor de serviços. Para partilhar a imagem, tem de enviá-lo para um registo. Hub de docker é um registo para imagens de Docker que permite-lhe alojar o seus repositórios, públicos ou privados. 

Antes de pode emitir uma imagem, tem de iniciar sessão Hub de Docker utilizando o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) comando. Substitua `<docker-id>` com o nome de conta e escreva a palavra-passe para a consola de linha. Para outras opções de palavra-passe do Docker Hub, consulte o [documentação de comando de início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

Uma mensagem "início de sessão foi efetuado com êxito" confirma que tiver iniciado a sessão. Depois de ter sessão iniciada, enviar a imagem para o Docker Hub utilizando o [docker push](https://docs.docker.com/engine/reference/commandline/push/) comando.

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Certifique-se de que o push com sucesso, examinando o comando de saída do.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Agora, pode utilizar esta imagem como a origem de implementação para uma nova aplicação de função no Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tópico requer a CLI do Azure versão 2.0 ou posterior. Executar `az --version` para localizar a versão do. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um plano de serviço de aplicações do Linux

Linux que alojam funções de não é atualmente suportado em planos de consumo. Tem de executar um plano de serviço de aplicações do Linux. Para saber mais acerca de alojamento, consulte o artigo [que alojam as funções do Azure planos de comparação](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Criar e implementar a imagem personalizada

A aplicação de função aloja a execução das suas funções. Criar uma aplicação de função a partir de uma imagem de Docker Hub utilizando o [az functionapp criar](/cli/azure/functionapp#create) comando. 

O seguinte comando, substitua um nome de aplicação de função exclusivo onde vir o `<app_name>` marcador de posição e a conta de armazenamento de nome para `<storage_name>`. O `<app_name>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. Como anteriormente, `<docker-id>` é o nome da sua conta de Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
Depois de a aplicação Function App ter sido criada, a CLI do Azure mostra informações semelhantes às do exemplo seguinte:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

O _implementação--imagem-nome do contentor_ parâmetro indica a imagem alojada num Hub de Docker a utilizar para criar a aplicação de função. 


## <a name="configure-the-function-app"></a>Configurar a aplicação de função

A função tem da cadeia de ligação para ligar à conta do storage predefinida. Quando publicar a sua imagem personalizada para uma conta privada contentor, em vez disso, deverá definir estas definições de aplicação como variáveis de ambiente em Dockerfile, utilizando o [instrução ENV](https://docs.docker.com/engine/reference/builder/#env), ou equivalente. 

Neste caso, `<storage_account>` é o nome da conta de armazenamento que criou. Obter a cadeia de ligação com o [cadeia de ligação de mostrar de conta de armazenamento az](/cli/azure/storage/account#show-connection-string) comando. Adicionar estas definições de aplicação na aplicação de função com o [az functionapp configuração appsettings conjunto](/cli/azure/functionapp/config/appsettings#set) comando.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Agora pode testar as suas funções em execução no Linux no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie uma imagem personalizada utilizando o Docker.
> * Publica uma imagem personalizada para um registo de contentor. 
> * Crie uma conta de armazenamento do Azure. 
> * Crie um plano de serviço de aplicações do Linux. 
> * Implemente uma aplicação de função do Hub de Docker.
> * Adicione as definições da aplicação para a aplicação de função.

Saiba mais sobre como desenvolver as funções do Azure localmente, utilizando as ferramentas de núcleos de funções do Azure.

> [!div class="nextstepaction"] 
> [Código e teste das funções do Azure localmente](functions-run-local.md)
