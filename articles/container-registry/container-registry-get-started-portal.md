---
title: "Guia de introdução - criar um registo de Docker privado no Azure com o portal do Azure"
description: Saiba mais rapidamente criar um registo de contentor do Docker privado com o portal do Azure.
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 514fa3490e480647f0923c99bd9606a3726d4d30
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Criar um registo de contentores com o portal do Azure

Um registo de contentor do Azure é um registo de Docker privado no Azure, onde pode armazenar e gerir as imagens de contentor do Docker privadas. Este guia de introdução, crie um registo de contentor com o portal do Azure.

Para concluir este guia de introdução, tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Selecione **novo** > **contentores** > **registo de contentor do Azure**.

![Criar um registo de contentor no portal do Azure][qs-portal-01]

Introduza os valores para **nome do registo** e **grupo de recursos**. O nome do registo tem de ser exclusivo no Azure e pode incluir carateres alfanuméricos de 5 a 50. Criar um novo grupo de recursos com o nome `myResourceGroup`e para **SKU**, selecione 'Basic'. Selecione **criar** para implementar a instância ACR.

![Criar um registo de contentor no portal do Azure][qs-portal-03]

Este guia de introdução, criamos um *básico* registo. Registo de contentor do Azure está disponível em vários SKUs diferentes, brevemente descritos na seguinte tabela. Para obter detalhes expandidos em cada um, consulte [registo de contentor SKUs](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Quando o **implementação concluída com êxito** mensagem for apresentada, selecione o registo do contentor no portal, em seguida, selecione **chaves de acesso**.

![Criar um registo de contentor no portal do Azure][qs-portal-05]

Em **utilizador de Admin**, selecione **ativar**. Tome nota dos seguintes valores:

* Servidor de início de sessão
* Nome de utilizador
* palavra-passe

Utilize estes valores nos passos seguintes ao trabalhar com o registo com a CLI do Docker.

![Criar um registo de contentor no portal do Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Inicie sessão no ACR

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Para tal, utilize o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) comando. Substitua o *username*, *palavra-passe*, e *servidor de início de sessão* valores com os que anotou no passo anterior.

```bash
docker login --username <username> --password <password> <login server>
```

O comando devolve `Login Succeeded` depois de concluir. Também poderá ver um aviso de segurança, recomendamos a utilização do `--password-stdin` parâmetro. Enquanto a sua utilização está fora do âmbito deste artigo, recomendamos a seguir esta melhor prática. Consulte o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) comando de referência para obter mais informações.

## <a name="push-image-to-acr"></a>Imagem de push para o ACR

Para enviar uma imagem para o seu registo de contentor do Azure, primeiro tem de ter uma imagem. Se for necessário, execute o seguinte comando para solicitar uma imagem existente a partir do Hub de Docker.

```bash
docker pull microsoft/aci-helloworld
```

Antes de enviar a imagem para o registo, tem de etiquetar a imagem com o nome de servidor de início de sessão ACR. Marcar a imagem a utilizar o [tag de docker](https://docs.docker.com/engine/reference/commandline/tag/) comando. Substitua *servidor de início de sessão* com o nome do servidor de início de sessão que registou anteriormente.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Por último, utilize [docker push](https://docs.docker.com/engine/reference/commandline/push/) para enviar a imagem para a instância ACR. Substitua *servidor de início de sessão* com o nome do servidor de início de sessão da sua instância ACR.

```
docker push <login server>/aci-helloworld:v1
```

O resultado de um bem-sucedida `docker push` comando é semelhante a:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Listar imagens de contentor

Para listar as imagens na sua instância ACR, navegue para o registo no portal e selecione **repositórios**, em seguida, selecione o repositório que criou com `docker push`.

Neste exemplo, selecione o **aci olámundo** repositório e pode ver o `v1`-imagem marcado em **etiquetas**.

![Criar um registo de contentor no portal do Azure][qs-portal-09]

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário eliminar o **myResourceGroup** grupo de recursos. Se o fizer, irá eliminar o grupo de recursos, instância ACR e todas as imagens de contentor.

![Criar um registo de contentor no portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, criou um registo de contentor do Azure com a CLI do Azure. Se gostaria de utilizar o registo de contentor do Azure com instâncias de contentor do Azure, continue para o tutorial de instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Azure Container Instances tutorials](../container-instances/container-instances-tutorial-prepare-app.md) (Tutoriais do Azure Container Instances)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png