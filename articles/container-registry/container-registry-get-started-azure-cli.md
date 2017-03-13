---
title: Criar registo privado de contentor Docker - CLI do Azure | Microsoft Docs
description: "Introdução à criação e gestão de registos privados de contentores Docker com a CLI 2.0 do Azure"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 6ef43ed43358357c94460a27d3e2b2c8530b6c54
ms.lasthandoff: 03/06/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Criar um registo privado de contentor Docker com a CLI 2.0 do Azure
Utilize os comandos da [CLI do Azure 2.0](https://github.com/Azure/azure-cli) para criar um registo de contentores e gerir as respetivas definições a partir do seu computador Linux, Mac ou Windows. Também pode criar e gerir registos de contentores com o [portal do Azure](container-registry-get-started-portal.md) ou programaticamente com a [API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) do Registo de Contentores.


* Para explicações e conceitos, veja [a descrição geral](container-registry-intro.md)
* Para obter ajuda com os comandos da CLI do Registo de Contentores (comandos `az acr`), transmita o parâmetro `-h` a cada comando.

> [!NOTE]
> O Registo de Contentores está em pré-visualização atualmente.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* **CLI 2.0 do Azure**: para instalar e começar a trabalhar com a CLI 2.0, veja as [instruções de instalação](/cli/azure/install-azure-cli). Execute `az login` para iniciar sessão na sua subscrição do Azure. Para obter mais informações, veja [Introdução à CLI 2.0](/cli/azure/get-started-with-azure-cli).
* **Grupo de recursos**: crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) antes de criar um registo de contentores ou utilize um grupo de recursos existente. Confirme que o grupo de recursos está numa localização na qual o serviço do Registo de Contentores esteja [disponível](https://azure.microsoft.com/regions/services/). Para criar um grupo de recursos com a CLI 2.0, veja [a referência da CLI 2.0](/cli/azure/group). 
* **Conta de armazenamento** (opcional): crie uma [conta de armazenamento](../storage/storage-introduction.md) standard do Azure para colocar o registo de contentores na mesma localização. Se não especificar uma conta de armazenamento quando criar um registo com `az acr create`, o comando cria uma por si. Para criar uma conta de armazenamento com a CLI 2.0, veja [a referência da CLI 2.0](/cli/azure/storage/account). Atualmente, não há suporte para o Armazenamento Premium.
* **Principal de serviço** (opcional): quando cria um registo com a CLI, este não está configurado para acesso, por predefinição. Consoante as suas necessidades, pode atribuir um principal de serviço existente do Azure Active Directory a um registo (ou criar e atribuir um novo) ou ativar a conta de utilizador administrador do registo. Veja as secções posteriores deste artigo. Para obter mais informações sobre o acesso ao registo, veja [Authenticate with a container registry (Autenticar num registo de contentores)](container-registry-authentication.md). 

## <a name="create-a-container-registry"></a>Criar um registo de contentores
Execute o comando `az acr create` para criar um registo de contentores. 

> [!TIP]
> Quando criar um registo, especifique um nome de domínio de nível superior globalmente exclusivo, que contenha apenas letras e números. O nome do registo nos exemplos é `myRegistry`, mas substitua por um nome exclusivo seu. 
> 
> 

O comando seguinte utiliza os parâmetros mínimos para criar o registo de contentores `myRegistry` no grupo de recursos `myResourceGroup`, na localização E.U.A. Centro-Sul:

```azurecli
az acr create -n myRegistry -g myResourceGroup -l southcentralus
```

* `--storage-account-name` ou `-s` é opcional. Se não for especificado, é criada uma conta de armazenamento com um nome aleatório no grupo de recursos indicado.

O resultado é semelhante ao seguinte:

![az acr create output](./media/container-registry-get-started-azure-cli/acr_create.png)


Tome especial atenção:

* `id` - é identificador do registo na sua subscrição, do qual precisa se quiser atribuir um principal de serviço. 
* `loginServer` - o nome completamente qualificado que especificou para [iniciar sessão no registo](container-registry-authentication.md). Neste exemplo, o nome é `myregistry-contoso.exp.azurecr.io` (em minúsculas).

## <a name="assign-a-service-principal"></a>Atribuir um principal de serviço
Utilize os comandos da CLI 2.0 para atribuir um principal de serviço do Azure Active Directory a um registo. É atribuída a função Proprietário ao principal de serviço destes exemplos, mas pode atribuir [outras funções](../active-directory/role-based-access-control-configure.md), se assim entender.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Criar um principal de serviço e atribuir acesso ao registo
No comando seguinte, é atribuído a um principal de serviço novo o acesso de função Proprietário ao identificador do registo transmitido com o parâmetro `--scopes`. Especifique uma palavra-passe forte com o parâmetro `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Atribuir um principal de serviço existente
Se já tiver um principal de serviço e quiser atribuir-lhe o acesso de função Proprietário ao registo, execute um comando semelhante ao exemplo seguinte. O ID de aplicação do principal de serviço é transmitido com o parâmetro `--assignee`:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Gerir credenciais de administrador
É criada automaticamente uma conta de administrador para cada registo de contentores, que está desativada por predefinição. Os exemplos seguintes mostram comandos da CLI `az acr` para gerir as credenciais de administrador do registo de contentores.

### <a name="obtain-admin-user-credentials"></a>Obter as credenciais de utilizador administrador
```azurecli
az acr credential show -n myRegistry
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Ativar o utilizador administrador num registo existente
```azurecli
az acr update -n myRegistry --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Desativar o utilizador administrador num registo existente
```azurecli
az acr update -n myRegistry --admin-enabled false
```

## <a name="list-images-and-tags"></a>Listar imagens e etiquetas
Utilize os comandos da CLI `az acr` para consultar as imagens e etiquetas num repositório. 

> [!NOTE]
> Atualmente, o Registo de Contentores não suporta o comando `docker search` para consultar imagens e etiquetas.


### <a name="list-repositories"></a>Listar repositórios
Os exemplos seguintes listam os repositórios num registo, no formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myRegistry -o json
```

### <a name="list-tags"></a>Listar etiquetas
O exemplo seguinte lista as etiquetas no repositório **samples/nginx**, no formato JSON:

```azurecli
az acr repository show-tags -n myRegistry --repository samples/nginx -o json
```

## <a name="next-steps"></a>Passos seguintes
* [Push your first image using the Docker CLI (Enviar a sua primeira imagem com a CLI do Docker)](container-registry-get-started-docker-cli.md)


