---
title: "Operações do Docker num registo de contentores | Microsoft Docs"
description: Enviar e enviar imagens do Docker para um registo de contentores do Azure com a CLI do Docker
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: aa4b960ed75b5a4702317bf557b4588e7a54fa0e
ms.openlocfilehash: 923e1a045062a817dd6726dfce94485be7211ca2

---
# <a name="push-your-first-image-to-a-container-registry-using-the-docker-cli"></a>Enviar a sua primeira imagem para um registo de contentores com a CLI do Docker
Os registos de contentores do Azure armazenam e gerem imagens de contentores do [Docker](http://hub.docker.com) privadas, de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens do Docker públicas. Utilize a [Interface de Linha de Comandos do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (CLI do Docker) para [iniciar sessão](https://docs.docker.com/engine/reference/commandline/login/), [enviar](https://docs.docker.com/engine/reference/commandline/push/), [receber](https://docs.docker.com/engine/reference/commandline/pull/) e fazer outras operações no registo de contentores. 

Para obter mais informações e conceitos, veja [What is Azure Container Registry? (O que é o Registo de Contentores do Azure?)](container-registry-intro.md)


> [!NOTE]
> O Registo de Contentores está em pré-visualização atualmente.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure 2.0 (Pré-visualização)](container-registry-get-started-azure-cli.md).
* **Docker CLI** - para configurar o seu computador local como anfitrião Docker e aceder aos comandos da CLI do Docker, instale o [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Iniciar sessão num registo
Execute `docker login` para iniciar sessão no registo de contentores com as suas [credenciais do registo](container-registry-authentication.md).

O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização. 

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Confirme que especifica o nome de registo completamente qualificado (em minúsculas). Neste exemplo, é `myregistry-contoso.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Passos para enviar e receber imagens
O exemplo seguinte transfere uma imagem da Nginx a partir do registo público do Docker Hub, etiqueta-a para o seu registo de contentores do Azure privado, envia-a para o registo e, depois, volta a extraí-la.

**1. Extrair a imagem oficial do Docker para Nginx**

Primeiro, extraia a imagem pública da Nginx para o computador local.

```
docker pull nginx
```
**2. Iniciar o contentor de Nginx**

O comando seguinte inicia o contentor de Nginx local interativamente (para que possa ver o resultado a partir da Nginx) e à escuta na porta 8080. Remove o contentor em execução, depois de parado.

```
docker run -it --rm -p 8080:80 nginx
```

Navegue para [http://localhost:8080](http://localhost:8080) para ver o contentor em execução. Verá um ecrã semelhante ao seguinte.

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Para parar o contentor em execução, prima [CTRL]+[C].

**3. Criar um alias da imagem no registo**

O comando seguinte cria um alias da imagem, com um caminho completamente qualificado para o seu registo. Este exemplo especifica o espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

```
docker tag nginx myregistry-exp.azurecr.io/samples/nginx
```  

**4. Enviar a imagem para o registo**

```
docker push myregistry-contoso.azurecr.io/samples/nginx
``` 

**5. Extrair a imagem do registo**

```
docker pull myregistry-contoso.azurecr.io/samples/nginx
``` 

**6. Iniciar o contentor de Nginx a partir do registo**

```
docker run -it --rm -p 8080:80 myregistry-exp.azurecr.io/samples/nginx
```

Navegue para [http://localhost:8080](http://localhost:8080) para ver o contentor em execução.

Para parar o contentor em execução, prima [CTRL]+[C].

**6. Remover a imagem**

```
docker rmi myregistry-contoso.azurecr.io/samples/nginx
```



## <a name="next-steps"></a>Passos seguintes
Agora que sabe as noções básicas, está pronto para começar a utilizar o seu registo! Por exemplo, comece a implementar imagens do contentor num cluster do [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/).






<!--HONumber=Nov16_HO3-->


