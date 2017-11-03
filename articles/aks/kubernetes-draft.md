---
title: Utilizar rascunho com AKS e registo de contentor do Azure | Microsoft Docs
description: Use e rascunho com AKS e registo de contentor do Azure
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: draft, helm, aks, azure-container-service
keywords: "Docker, Contentores, microsserviços, Kubernetes, Draft, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3e607a6ce5662f6ff597fafbcec8c864f25ff54c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Use e rascunho com o serviço de contentor do Azure (AKS)

Rascunho é uma ferramenta open-source que ajuda-o pacote e execute código num Kubernetes cluster. Rascunho é direcionado para o ciclo de desenvolvimento de iteração; à medida que está a ser desenvolvido o código, mas antes de consolidar para o controlo de versão. Com o rascunho, pode rapidamente implementar novamente uma aplicação Kubernetes tal como as alterações de código. Para obter mais informações sobre rascunho, consulte o [rascunho documentação no Github](https://github.com/Azure/draft/tree/master/docs).

Este detalhes de documento com rascunho de um cluster de Kubernetes AKS.

## <a name="prerequisites"></a>Pré-requisitos

Os passos detalhados neste documento partem do princípio de que já criou um cluster do AKS e estabeleceu uma ligação de kubectl com o cluster. Se precisar destes itens, consulte o [início rápido AKS](./kubernetes-walkthrough.md).

Também precisa de um registo de Docker privado no registo de contentor do Azure (ACR). Para obter instruções sobre como implementar uma instância ACR, consulte o [início rápido do registo de contentor do Azure](../container-registry/container-registry-get-started-azure-cli.md).

## <a name="install-helm"></a>Instalar Helm

A CLI Helm for um cliente que é executado no seu sistema de desenvolvimento e permite-lhe iniciar, parar e gerir aplicações com gráficos Helm.

Para instalar a CLI Helm num Mac, utilize `brew`. Para opções de instalação adicionais, consulte [instalar Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md).

```console
brew install kubernetes-helm
```

Saída:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Instalar rascunho

A CLI de rascunho é um cliente que é executado no seu sistema de desenvolvimento e permite-lhe quicky implementa código num Kubernetes cluster.

Para instalar a CLI de rascunho num utilize Mac `brew`. Para opções de instalação adicionais consulte, a [rascunho instalar guia](https://github.com/Azure/draft/blob/master/docs/install.md).

```console
brew install draft
```

Saída:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Configurar rascunho

Quando configurar rascunho, um registo de contentor tem de ser especificado. Neste exemplo é utilizado o registo de contentor do Azure.

Execute o seguinte comando para obter o nome e o nome do servidor de início de sessão da sua instância ACR. Atualize o comando com o nome do grupo de recursos que contém a instância ACR.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

A palavra-passe de instância ACR também é necessário.

Execute o seguinte comando para devolver a palavra-passe ACR. Atualize o comando com o nome da instância ACR.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

Inicializar rascunho com o `draft init` comando.

```console
draft init
```

Durante este processo, são-lhe pedido para as credenciais de registo do contentor. Quando utilizar um registo de contentor do Azure, o URL de registo é o nome ACR do servidor de início de sessão, o nome de utilizador é o nome da instância ACR e a palavra-passe é a palavra-passe ACR.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

Depois de concluído, o rascunho é configurado no cluster de Kubernetes e está pronto a utilizar.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>Executar uma aplicação

O repositório de rascunho inclui várias aplicações de exemplo que podem ser utilizados para a demonstração de rascunho. Crie uma cópia clonada do repositório.

```console
git clone https://github.com/Azure/draft
```

Mude para o diretório de exemplos de Java.

```console
cd draft/examples/java/
```

Utilize o `draft create` comando para iniciar o processo. Este comando cria os objetos que são utilizados para executar a aplicação num Kubernetes cluster. Estes itens incluem um Dockerfile, um gráfico de Helm e um `draft.toml` ficheiro, que é o ficheiro de configuração de rascunho.

```console
draft create
```

Saída:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Para executar a aplicação num Kubernetes cluster, utilize o `draft up` comando. Este comando carrega os ficheiros de código e a configuração de aplicação para o cluster Kubernetes. É, em seguida, executa Dockerfile para criar uma imagem de contentor, pushes a imagem para o registo de contentor e, finalmente, executado o gráfico de Helm para iniciar a aplicação.

```console
draft up
```

Saída:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>Testar a aplicação

Para testar a aplicação, utilize o `draft connect` comando. Proxies este comando uma ligação para o Kubernetes pod, permitindo uma ligação de local segura. Quando terminar, a aplicação pode ser acedida no URL fornecido.

Em alguns casos, pode demorar alguns minutos para que a imagem do contentor ser transferida e a aplicação para iniciar. Se receber um erro ao aceder a aplicação, tente novamente a ligação.

```console
draft connect
```

Saída:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Quando terminar de testar a utilização de aplicação `Control+C` para parar a ligação de proxy.

## <a name="expose-application"></a>Expor a aplicação

Ao testar uma aplicação numa Kubernetes, poderá querer disponibilizar a aplicação na internet. Isto pode ser feito através de um serviço de Kubernetes com um tipo de [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) ou um [controlador entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/). Este detalhes de documentos através de um serviço Kubernetes.


Em primeiro lugar, o rascunho do pacote tem de ser atualizado para especificar que um serviço com um tipo `LoadBalancer` deve ser criado. Para tal, atualizar o tipo de serviço a `values.yaml` ficheiro.

```console
vi chart/java/values.yaml
```

Localize o `service.type` propriedade e atualize o valor de `ClusterIP` para `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Executar `draft up` para executar novamente a aplicação.

```console
draft up
```

Pode demorar alguns minutos para que o serviço devolver um endereço IP público. Para monitorizar a utilização de progresso de `kubectl get service` comando com uma veja.

```console
kubectl get service -w
```

Inicialmente, o *IP externo* para o serviço é apresentado como `pending`.

```
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Depois do endereço IP externo mudou de `pending` para um `IP address`, utilize `Control+C` parar o processo de veja kubectl.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Para ver a aplicação, navegue para o endereço IP externo.

```console
curl 52.175.224.118
```

Saída:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Repetir a aplicação

Agora que foi configurado um rascunho e a aplicação é executada no Kubernetes, são definidas para iteração do código. Código de sempre que pretende testar atualizada, execute o `draft up` comando para atualizar a aplicação em execução.

Neste exemplo, atualize a aplicação do Java Olá mundo.

```console
vi src/main/java/helloworld/Hello.java
```

Atualize o texto de Olá, mundo.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

Execute o `draft up` comando para voltar a implementar a aplicação.

```console
draft up
```

Saída

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

Por fim, ver a aplicação para ver as atualizações.

```console
curl 52.175.224.118
```

Saída:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização de rascunho, consulte a documentação de rascunho no GitHub.

> [!div class="nextstepaction"]
> [Documentação de rascunho](https://github.com/Azure/draft/tree/master/docs)