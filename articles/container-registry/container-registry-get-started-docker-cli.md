---
title: Imagem de Docker push privada registo do Azure
description: Enviar e extrair imagens do Docker para um registo privado de contentor do Azure com a CLI do Docker
services: container-registry
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42877b0aeecf08602d31ba21dccc814e542fd174
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Enviar a sua primeira imagem para um registo privado de contentor Docker com a CLI do Docker

Os registos de contentores do Azure armazenam e gerem imagens de contentores do [Docker](http://hub.docker.com) privadas, de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens do Docker públicas. Pode utilizar o [interface de linha de comandos do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (CLI do Docker) para [início de sessão](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [solicitação](https://docs.docker.com/engine/reference/commandline/pull/)e outras operações no seu contentor registo.

Nos passos seguintes, pode transfere um oficial [Nginx imagem](https://store.docker.com/images/nginx) a partir do registo de Hub de Docker público, marcá-la para o registo de contentor do Azure privado, enviá-lo para o registo e, em seguida, solicitá-lo a partir do registo.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure 2.0](container-registry-get-started-azure-cli.md).
* **CLI do docker** - para configurar o computador local como um anfitrião do Docker e aceder aos comandos da CLI do Docker, instalar [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Iniciar sessão num registo

Existem [várias formas de autenticar](container-registry-authentication.md) ao seu registo de contentor privada. O método recomendado ao trabalhar numa linha de comandos é com o comando da CLI do Azure [início de sessão do az acr](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Por exemplo iniciar sessão para um registo com o nome *myregistry*:

```azurecli
az acr login --name myregistry
```

Também pode iniciar sessão com [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/). O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, poderá ter [atribuído um principal de serviço](container-registry-authentication.md#service-principal) para o registo para um cenário de automatização.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Ambos os comandos devolvem `Login Succeeded` depois de concluir. Se utilizar `docker login`, também poderá ver um aviso de segurança, recomendamos a utilização do `--password-stdin` parâmetro. Enquanto a sua utilização está fora do âmbito deste artigo, recomendamos a seguir esta melhor prática. Para obter mais informações, consulte o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) referência de comandos.

> [!TIP]
> Sempre especificar o nome do registo completamente qualificado (todo em minúsculas) ao utilizar `docker login` e quando a tag imagens para enviar para o registo. Os exemplos neste artigo, o nome completamente qualificado é *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>A imagem de Nginx oficial de extração

Em primeiro lugar, extrair a imagem de Nginx pública para o seu computador local.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Executar o seguinte [docker run](https://docs.docker.com/engine/reference/run/) comando para iniciar uma instância local do contentor Nginx interativamente (`-it`) na porta 8080. O `--rm` argumento especifica que o contentor deve ser removido quando deixa-lo.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Navegue até à [http://localhost:8080](http://localhost:8080) para ver a página predefinida de web servidos pelo Nginx no contentor em execução. Deverá ver uma página semelhante ao seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Porque é iniciado o contentor de forma interativa com `-it`, pode ver o servidor de Nginx de saída na linha de comandos após navegar para o mesmo no seu browser.

Para parar e remover o contentor, prima `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Criar um alias da imagem

Utilize [etiquetas de docker](https://docs.docker.com/engine/reference/commandline/tag/) para criar um alias da imagem com o caminho completamente qualificado para o registo. Este exemplo especifica o espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Para obter mais informações sobre a etiquetagem com espaços de nomes, consulte o [espaços de nomes do repositório](container-registry-best-practices.md#repository-namespaces) secção [melhores práticas para o registo de contentor do Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Enviar a imagem para o registo

Agora que já etiquetados a imagem com o caminho completamente qualificado para o seu registo privado, pode enviá-lo para o registo com [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Solicitar a imagem a partir do registo

Utilize o [solicitação docker](https://docs.docker.com/engine/reference/commandline/pull/) comando para solicitar a imagem a partir do registo:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Iniciar o contentor Nginx

Utilize o [docker run](https://docs.docker.com/engine/reference/run/) comando a executar a imagem já solicitados a partir do seu registo:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navegue para [http://localhost:8080](http://localhost:8080) para ver o contentor em execução.

Para parar e remover o contentor, prima `Control` + `C`.

## <a name="remove-the-image-optional"></a>Remova a imagem (opcional)

Se já não necessita da imagem de Nginx, podem eliminá-la localmente com a [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) comando.

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Para remover imagens a partir do seu registo de contentor do Azure, pode utilizar o comando da CLI do Azure [delete de repositório az acr](/cli/azure/acr/repository#az_acr_repository_delete). Por exemplo, o seguinte comando elimina o manifesto referenciado por uma etiqueta, todos os dados associados a camada e todas as outras etiquetas de referenciar o manifesto.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Passos seguintes

Agora que conhece as noções básicas, estará pronto começar a utilizar o seu registo! Por exemplo, implementar imagens do contentor a partir do seu registo para um [serviço de contentor do Azure (AKS)](../aks/tutorial-kubernetes-prepare-app.md) cluster.
