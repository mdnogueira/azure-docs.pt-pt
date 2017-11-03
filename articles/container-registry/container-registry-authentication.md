---
title: Autenticar com um registo de contentor do Azure | Microsoft Docs
description: "Como iniciar sessão para um registo de contentor do Azure através de um principal de serviço do Azure Active Directory ou uma conta de administrador"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d7d2ae0e9b1f7850332d151d78a4a5fdb013777
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticar com um registo de contentor do Docker privado
Para trabalhar com imagens do contentor num registo de contentor do Azure, inicie sessão com a `docker login` comando. Pode iniciar sessão utilizando um  **[principal de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md)**  específicas do registo ou **conta de administrador**. Este artigo fornece mais detalhes sobre estas identidades.

## <a name="service-principal"></a>Principal de serviço

Pode atribuir um principal de serviço para o registo e utilizá-lo para a autenticação básica do Docker. É recomendado utilizar um principal de serviço para a maioria dos cenários. Forneça o ID da aplicação e a palavra-passe do serviço principal para o `docker login` comando, conforme mostrado no exemplo seguinte:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Assim que a sessão iniciada, Docker coloca em cache as credenciais, por isso não terá de memorizar o ID de aplicação.

> [!TIP]
> Se quiser, pode voltar a gerar a palavra-passe de um principal de serviço, executando o `az ad sp reset-credentials` comando.
>

Permitem principais de serviço [acesso baseado em funções](../active-directory/role-based-access-control-configure.md) um registo. Funções disponíveis são:
  * Leitor (acesso só de solicitação).
  * Contribuidor (push e pull).
  * Proprietário (extração, push e atribuir funções a outros utilizadores).

Acesso anónimo não está disponível sobre os registos do contentor do Azure. Pode utilizar imagens públicas [Docker Hub](https://docs.docker.com/docker-hub/).

Pode atribuir vários principais de serviço para um registo, o que permite-lhe definir acesso para diferentes utilizadores ou aplicações. Principais de serviço também ativar a conetividade "sem interface" para um registo do programador ou cenários de DevOps, tais como os exemplos seguintes:

  * Implementações de contentores de um registo para sistemas de orquestração, incluindo DC/OS, Docker Swarm e Kubernetes. Também pode solicitar os registos do contentor para os serviços do Azure relacionados, tais como [serviço de contentor](../container-service/index.yml), [do serviço de aplicações](../app-service/index.yml), [Batch](../batch/index.md), [Service Fabric](/azure/service-fabric/)entre outros.

  * Contínuas integração e a implementação soluções (por exemplo, o Visual Studio Team Services ou Jenkins) que criar imagens de contentor e emiti-las para um registo.


## <a name="admin-account"></a>Conta de administrador
Com cada registo que criar, é criada automaticamente uma conta de administrador. Por predefinição a conta está desativada, mas pode ativá-la e gerir as credenciais, por exemplo através do [portal](container-registry-get-started-portal.md#create-a-container-registry) ou utilizando o [comandos de Azure CLI 2.0](container-registry-get-started-azure-cli.md#create-a-container-registry). Cada conta de administrador é fornecida com duas palavras-passe, que podem ser novamente geradas. As duas palavras-passe permitem-lhe manter as ligações ao registo, utilizando uma palavra-passe enquanto volta a gerar a outra palavra-passe. Se a conta estiver ativada, pode passar o nome de utilizador e a palavra-passe para o `docker login` comando para a autenticação básica para o registo. Por exemplo:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

> [!IMPORTANT]
> A conta de administrador foi concebida para um único utilizador aceder ao registo, principalmente para fins de teste. Não se recomenda para partilhar as credenciais da conta de administrador entre os outros utilizadores. Todos os utilizadores são apresentados como um único utilizador no registo. Alterar ou desativar esta conta desativa para todos os utilizadores que utilizam as credenciais de acesso ao registo.
>

### <a name="next-steps"></a>Passos seguintes
* [Push primeira imagem utilizando a CLI do Docker](container-registry-get-started-docker-cli.md).
* Para obter mais informações sobre a autenticação na pré-visualização de registo de contentor, consulte o [blogue](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/).
