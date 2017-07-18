---
title: Criar registo privado do Docker - Portal do Azure | Microsoft Docs
description: "Introdução à criação e gestão de registos privados de contentores Docker com o portal do Azure"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: pt-pt
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Criar um registo de contentores gerido com o portal do Azure

O Azure Container Registry é um serviço de registo do contentor Docker gerido, utilizado para armazenar imagens de contentor do Docker privadas. Este guia detalha a criação de uma instância do Azure Container Registry com o portal do Azure.

Os registos de contentores do Azure geridos estão em pré-visualização e não estão disponível em todas as regiões.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registo de contentores

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Pesquise o **registo de contentores do Azure** no marketplace e selecione-o.

3. Clique em **Criar**, que irá abrir o painel de criação do ACR.

    ![Definições do registo de contentores](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. No painel **Registo de Contentores do Azure**, introduza as informações seguintes. Clique em **Criar** quando tiver concluído.

    a. **Nome do registo**: um nome de domínio de nível superior globalmente exclusivo para o seu registo específico. Neste exemplo, o nome do registo é *myAzureContainerRegistry1*, mas substitua-o por um nome exclusivo seu. O nome só pode conter letras e números.

    b. **Grupo de recursos**: selecione um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) ou escreva o nome de um grupo novo.

    c. **Localização**: selecione a localização de um datacenter do Azure onde o serviço esteja [disponível](https://azure.microsoft.com/regions/services/), como **E.U.A. Centro-Sul**.

    d. **Utilizador administrador**: se quiser, ative um utilizador administrador para aceder ao registo. Pode alterar esta definição depois de criar o registo.

    e. **Utilizar registo gerido**: selecione Sim para que o ACR gira automaticamente o armazenamento de registos, utilize webhooks e utilize a autenticação do AAD.

    f. **Escalão de Preço**: selecione um escalão de preço, veja aqui os preços do ACR para obter mais informações.

## <a name="log-in-to-acr-instance"></a>Iniciar sessão na instância do ACR

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. 

Para tal, utilize a CLI 2.0 do Azure. Em primeiro lugar, se necessário, inicie sessão no Azure com o comando [az login](/cli/azure/#login). 

```azurecli
az login
```

Em seguida, utilize o comando [az acr login](/cli/azure/acr#login) para iniciar sessão no Azure Container Registry.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Utilizar o Azure Container Registry

### <a name="list-container-images"></a>Listar imagens de contentor

Utilize os comandos da CLI `az acr` para consultar as imagens e etiquetas num repositório.

> [!NOTE]
> Atualmente, o Registo de Contentores não suporta o comando `docker search` para consultar imagens e etiquetas.

### <a name="list-repositories"></a>Listar repositórios

Os exemplos seguintes listam os repositórios num registo, no formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Listar etiquetas

O exemplo seguinte lista as etiquetas no repositório **samples/nginx**, no formato JSON:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, criou uma instância gerida do Azure Container Registry com o portal do Azure.

> [!div class="nextstepaction"]
> [Push your first image using the Docker CLI (Enviar a sua primeira imagem com a CLI do Docker)](container-registry-get-started-docker-cli.md)
