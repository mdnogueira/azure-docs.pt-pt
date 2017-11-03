---
title: "Repositórios de registo de contentor do Azure | Microsoft Docs"
description: "Como utilizar os repositórios de registo de contentor do Azure para imagens de Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: d737df98fee955b57166cb44f45f61dff906514e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Repositórios de registo de contentor do Azure

Registo de contentor do Azure permite-lhe armazenar imagens de contentor no repositórios. Ao armazenar imagens em repositórios, pode ter grupos de imagens (ou versão das imagens) em ambientes isolados. Pode especificar estes repositórios quando push imagens no seu registo.


## <a name="prerequisites"></a>Pré-requisitos
* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure 2.0](container-registry-get-started-azure-cli.md).
* **Docker CLI** - para configurar o seu computador local como anfitrião Docker e aceder aos comandos da CLI do Docker, instale o [Docker Engine](https://docs.docker.com/engine/installation/).
* **Solicitar uma imagem** - solicitar uma imagem a partir do registo de Hub de Docker público, marcá-la e enviá-lo para o registo. Para obter orientações sobre como push e pull imagens, consulte [imagem Push Docker registo privado do Azure](container-registry-get-started-docker-cli.md).


## <a name="viewing-repositories-in-the-portal"></a>Repositórios de visualização no Portal

Depois de o ter feito o Push imagens no seu registo de contentor, pode ver uma lista dos repositórios que alojam as imagens no portal do Azure.

Se seguiu os passos a [imagem Push Docker registo privado do Azure](container-registry-get-started-docker-cli.md) artigo, agora, deve ter uma imagem de Nginx no seu registo de contentor. Como parte das instruções, deverá ter especificado um espaço de nomes para a imagem. No exemplo abaixo, o comando pushes a imagem de NGinx para o repositório de "exemplos":

```
docker push myregistry.azurecr.io/samples/nginx
```
 O Registo de Contentores do Azure suporta espaços de nomes de repositórios com múltiplos níveis. Esta funcionalidade permite-lhe agrupar coleções de imagens relacionadas com uma aplicação específica ou uma coleção de aplicações para equipas de programação ou operacionais específicas. Para ler mais sobre repositórios nos registos do contentor, consulte [os registos do contentor de Docker privados no Azure](container-registry-intro.md).

Para ver os repositórios de registo do contentor:

1. Iniciar sessão no portal do Azure
2. No **registo de contentor do Azure** painel, selecione de registo que pretende inspecionar
3. No painel do registo, clique em **repositórios** para ver uma lista de todos os repositórios do e suas imagens
4. (Opcional) Selecione a imagem específica para ver as etiquetas

![Repositórios no portal](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>Passos seguintes
Agora que sabe as noções básicas, está pronto para começar a utilizar o seu registo! Por exemplo, comece a implementar imagens do contentor num cluster do [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/).
