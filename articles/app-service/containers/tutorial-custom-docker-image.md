---
title: "Utilizar uma imagem personalizada do Docker da aplicação Web para contentores - Azure | Microsoft Docs"
description: "Como utilizar uma imagem personalizada do Docker da aplicação Web para contentores."
keywords: "serviço de aplicações do Azure, aplicação web, linux, docker, contentor"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 4ba53dd1239290c64907ed431d404b2d1be66c36
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Utilizar uma imagem personalizada do Docker da aplicação Web para contentores

[Aplicação de contentores Web](app-service-linux-intro.md) fornece imagens incorporadas do Docker no Linux com suporte para versões específicas, tais como PHP 7.0 e 4.5 do Node.js. Web App para contentores utiliza a tecnologia de contentor do Docker para alojar imagens incorporadas e imagens personalizadas como uma plataforma como um serviço. Neste tutorial, irá aprender a criar uma imagem personalizada do Docker e implemente-a aplicação Web para contentores. Este padrão é útil quando as imagens incorporadas não incluem o seu idioma à escolha ou quando a aplicação requer uma configuração específica que não foi fornecida nas imagens incorporadas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* [Git](https://git-scm.com/downloads)
* Um Active Directory [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* A [conta Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela de terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local, em seguida, mude para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Compilar a imagem do ficheiro Docker

No repositório de Git, observe _Dockerfile_. Este ficheiro descreve o ambiente Python que é necessário para executar a sua aplicação. Além disso, configura a imagem de um [SSH](https://www.ssh.com/ssh/protocol/) servidor para comunicação segura entre o anfitrião e do contentor.

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Para compilar a imagem do Docker, execute o `docker build` de comandos e forneça um nome, `mydockerimage`e etiqueta, `v1.0.0`. Substitua `<docker-id>` com o seu Hub de Docker conta ID.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

O comando produz o resultado semelhante ao seguinte:

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Teste a compilação funciona executando o contentor de Docker. Problema de [docker run](https://docs.docker.com/engine/reference/commandline/run/) de comandos e passar o nome e a etiqueta da imagem ao mesmo. É necessário especificar a porta a utilizar o `-p` argumento.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Certifique-se de que a aplicação web e o contentor estão a funcionar corretamente, navegando até `http://localhost:2222`.

![Aplicação do teste web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>Enviar a imagem de Docker para Docker Hub

Um registo é uma aplicação que aloja as imagens e fornece serviços de imagem e contentor de serviços. Para partilhar a imagem, tem de enviá-lo para um registo. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Enviar um registo de Docker privada? Consulte as instruções opcionais para [Push uma imagem de Docker registo privada](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Hub de docker é um registo para imagens de Docker que permite-lhe alojar o seus repositórios, públicos ou privados. Para enviar uma imagem personalizada do Docker ao Hub Docker público, utilize o [docker push](https://docs.docker.com/engine/reference/commandline/push/) de comandos e forneça um nome de imagem completa e a etiqueta. Um nome de imagem completa e etiqueta aspeto o exemplo seguinte:

```bash
<docker-id>/image-name:tag
```

Se ainda não registado no Hub de Docker, fazê-lo ao utilizar o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) comando antes de tentar enviar uma imagem.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Uma mensagem "início de sessão foi efetuado com êxito" confirma que tiver iniciado a sessão. Depois de a sessão, pode emitir a imagem a utilizar o Hub de Docker o [docker push](https://docs.docker.com/engine/reference/commandline/push/) comando.

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Certifique-se de que o push com sucesso, examinando o comando de saída do.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Pode alojar aplicações nativas do Linux na nuvem através da utilização de Web Apps do Azure. Para criar uma aplicação Web para contentores, tem de executar os comandos da CLI do Azure que criar um grupo, em seguida, um plano de serviço e, finalmente, a aplicação web. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Criar um plano de serviço de aplicações do Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Criar uma aplicação Web

No Cloud Shell, crie uma [aplicação Web](app-service-linux-intro.md) no plano do Serviço de Aplicações `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp#create). Não se esqueça de substituir `<app_name>` com um nome de aplicação único e < docker-ID > com o ID de Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

Quando a aplicação web tiver sido criada, a CLI do Azure mostra resultado semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

A maioria das imagens de Docker tem variáveis de ambiente que têm de ser configurados. Se estiver a utilizar uma imagem do Docker existente criada por outra pessoa, a imagem poderá utilizar uma porta diferente 80. Indicar ao Auzre sobre a porta que utiliza a imagem utilizando o `WEBSITES_PORT` definição de aplicação. Página do GitHub para o [exemplo Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que tem de definir `WEBSITES_PORT` para _8000_.

Para configurar definições de aplicação, utilize o [atualização az webapp configuração appsettings](/cli/azure/webapp/config/appsettings#update) comando na Shell de nuvem. As definições de aplicação são sensíveis a maiúsculas e espaço por vírgulas.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Implementação a partir de um registo de Docker privada? Consulte as instruções opcionais para [configurar aplicação Web para utilizar o contentor de Docker um registo privada](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Testar a aplicação web

Certifique-se de que a aplicação web funciona ao navegar para o mesmo (`http://<app_name>azurewebsites.net`). 

![Configuração da porta do teste web app](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Aplicação web de alteração e volte a implementar

No seu repositório de Git local, abra app/templates/app/index.html. Localize o primeiro elemento HTML e altere-o para.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Depois de modificar o ficheiro de Python e o guardou, tem de reconstruir e push a nova imagem de Docker. Em seguida, reinicie a aplicação web para que as alterações entrem em vigor. Utilize os mesmos comandos que tiver utilizado anteriormente neste tutorial. Pode fazer referência a [compilar a imagem do ficheiro Docker](#build-the-image-from-the-docker-file) e [Push a imagem de Docker Docker Hub](#push-the-docker-image-to-docker-hub). Testar a aplicação web, seguindo as instruções em [testar a aplicação web](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Ligar a aplicação Web para contentores utilizando o SSH

SSH permite a comunicação segura entre um contentor e um cliente. Ordem de uma imagem personalizada do Docker suportar o SSH, tem de a criar para um Dockerfile. Ativar SSH no próprio ficheiro do Docker. As instruções de SSH já foram adicionadas ao dockerfile exemplo, pelo que pode seguir estas instruções com a sua imagem personalizada:

* A [executar](https://docs.docker.com/engine/reference/builder/#run) instrução que chame `apt-get`, em seguida, define a palavra-passe da conta de raiz para `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Esta configuração não permite ligações externas ao contentor. SSH está disponível apenas através do Site do Kudu/SCM. O site do Kudu/SCM é autenticado com as credenciais de publicação.

* A [cópia](https://docs.docker.com/engine/reference/builder/#copy) instrução que dá instruções ao motor de Docker para copiar o [sshd_config](http://man.openbsd.org/sshd_config) do ficheiro para o */etc/ssh/* diretório. O ficheiro de configuração deve ser baseado no [este ficheiro sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O *sshd_config* ficheiro tem de incluir os seguintes itens: 
    > * `Ciphers`tem de incluir, pelo menos, um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs`tem de incluir, pelo menos, um item nesta lista: `hmac-sha1,hmac-sha1-96`.

* Um [expor](https://docs.docker.com/engine/reference/builder/#expose) instrução que expõe porta 2222 no contentor. Apesar da palavra-passe de raiz é conhecida, porta 2222 não pode ser acedida através da internet. É uma porta interna acessível apenas por contentores dentro da rede de bridge de uma rede privada virtual. Depois disso, os comandos Copiar detalhes de configuração de SSH e iniciar o `ssh` serviço.

    ```docker
    EXPOSE 8000 2222

    RUN service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Abrir ligação SSH ao contentor

Web App para contentores não permite ligações externas ao contentor. SSH está disponível apenas através do site Kudu, que é acessível na `https://<app_name>.scm.azurewebsites.net`.

Para ligar, navegue até à `https://<app_name>.scm.azurewebsites.net/webssh/host` e inicie sessão com a sua conta do Azure.

Em seguida, são redirecionados para uma página que apresenta uma consola interativa. 

Pode ser útil verificar que determinadas aplicações estão em execução no contentor. Para inspecionar o contentor e certifique-se a processos em execução, emita o `top` comando na linha de.

```bash
top
```

O `top` comando expõe todos os processos em execução num contentor.

```bash
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Parabéns! Configurou uma imagem personalizada do Docker para uma aplicação Web para contentores.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Utilizar uma imagem privada do Hub de Docker (opcional)

No [criar uma aplicação web](#create-a-web-app), que especificou uma imagem no Docker Hub o `az webapp create` comando. Este é boa para uma imagem de pública. Para utilizar uma imagem privada, terá de configurar o seu ID de conta do Docker e a palavra-passe na sua aplicação web do Azure.

Na Shell de nuvem, siga o `az webapp create` comando com [az webapp configuração contentor conjunto](/cli/azure/webapp/config/container#az_webapp_config_container_set). Substitua  *\<APP_NAME>.azurewebsites.NET >*e também _< id do docker >_ e  _<password>_  com o ID de Docker e a palavra-passe.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

O comando de revela resultado semelhante a seguinte cadeia JSON, que mostra que a alteração de configuração foi concluída com êxito:

```bash
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Utilizar uma imagem de Docker qualquer registo privada (opcional)

Nesta secção, irá aprender a utilizar uma imagem de Docker a partir de um registo privado na aplicação Web para contentores e utiliza o registo de contentor do Azure como um exemplo. Os passos para utilizar outros registos privados são semelhantes. 

Registo de contentor do Azure é um serviço de Docker gerido a partir do Azure para alojar imagens privadas. As implementações podem ser qualquer tipo, incluindo [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/)e a aplicação Web para contentores. 

### <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

Na Shell de nuvem, utilize o [az acr criar](https://docs.microsoft.com/cli/azure/acr#az_acr_create) comando para criar um registo de contentor do Azure. Transmita o nome, o grupo de recursos, e `Basic` para o SKU. SKUs disponíveis são `Classic`, `Basic`, `Standard`, e `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Criar um contentor produz a seguinte saída:

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Inicie sessão no registo de contentor do Azure

Para enviar uma imagem no registo, terá de fornecer credenciais para que o registo aceita push. Pode obter estas credenciais utilizando o [mostrar de acr az](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) comando na Shell de nuvem. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

O comando de revela duas palavras-passe que podem ser utilizadas com o nome de utilizador.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

A janela de terminal local, início de sessão para o registo de contentor Azure utilizando o `docker login` comando. O nome do servidor é necessária para iniciar sessão. Utilize o formato `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username> --password <password> 
```

Certifique-se de que o início de sessão foi efetuado com êxito. 

### <a name="push-an-image-to-azure-container-registry"></a>Emitir uma imagem para o registo de contentor do Azure

Push a imagem utilizando o `docker push` comando. Tag de imagem com o nome do registo, seguido pelo seu nome de imagem e a etiqueta.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Certifique-se de que o push adicionada com êxito um contentor para o registo enumerando repositórios do ACR. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Listar as imagens no registo confirma que `mydockerimage` está no registo.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Configurar aplicação Web para utilizar a imagem a partir do registo de contentor do Azure (ou qualquer registo privado)

Pode configurar a aplicação Web para contentores para que seja executado um contentor armazenado no registo de contentor do Azure. Com o registo de contentor do Azure é semelhante à utilização de qualquer registo privado, pelo que o se precisar de utilizar os seus próprios registo privado, os passos para concluir esta tarefa são semelhantes.

Na Shell na nuvem, execute [mostrar de credencial de acr az](/cli/azure/acr/credential#az_acr_credential_show) para apresentar o nome de utilizador e palavra-passe para o registo de contentor do Azure. Copie o nome de utilizador e um das palavras-passe de forma a poder utilizá-lo para configurar a aplicação web no próximo passo.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

Na Shell na nuvem, execute o [az webapp configuração contentor conjunto](/cli/azure/webapp/config/container#az_webapp_config_container_set) comando para atribuir a imagem de Docker personalizada para a aplicação web. Substitua  *\<APP_NAME>.azurewebsites.NET >*,  *\<docker-registo--url do servidor >*,  _\<registo-username >_e  _\<palavra-passe >_. Para o registo de contentor do Azure,  *\<docker-registo--url do servidor >* está no formato `https://<azure-container-registry-name>.azurecr.io`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://`é necessário no  *\<docker-registo--url do servidor >*.
>

O comando de revela resultado semelhante a seguinte cadeia JSON, que mostra que a alteração de configuração foi concluída com êxito:

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web Docker Python e PostgreSQL no Azure](tutorial-docker-python-postgresql-app.md)
