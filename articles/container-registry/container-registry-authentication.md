---
title: Autenticar com um registo de contentor do Azure
description: "Opções de autenticação para um registo de contentor do Azure, incluindo o Azure Active Directory do serviço de início de sessão de principais, direta e registo."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 37514e7b90afe1162aa4bbd2869326a691f75c4e
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticar com um registo de contentor do Docker privado

Existem várias formas de autenticar com um registo de contentor do Azure, cada um dos quais se aplica a um ou vários cenários de utilização do registo.

Pode iniciar sessão para um registo diretamente através do [início de sessão individuais](#individual-login-with-azure-ad), e as suas aplicações e orchestrators contentor podem efetuar a autenticação autónoma ou "sem interface", utilizando um Azure Active Directory (Azure AD) [ principal de serviço](#service-principal).

Registo de contentor do Azure não suporta operações de Docker não autenticadas ou acesso anónimo. Para imagens públicas, pode utilizar [Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Início de sessão individuais com o Azure AD

Ao trabalhar com o registo diretamente, tais como extrair imagens para e enviar imagens a partir da sua estação de trabalho de desenvolvimento, autenticar utilizando o [início de sessão do az acr](/cli/azure/acr?view=azure-cli-latest#az_acr_login) no [CLI do Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Quando iniciar sessão com `az acr login`, a CLI utiliza o token criado quando é executada `az login` perfeitamente autenticar a sua sessão com o seu registo. Assim que tiver a sessão iniciada desta forma, as suas credenciais estão em cache e subsequentes `docker` comandos não necessitam de um nome de utilizador ou palavra-passe. Se o seu token expira, pode atualizá-lo utilizando o `az acr login` comando novamente para voltar. Utilizar `az acr login` com identidades do Azure fornece [acesso baseado em funções](../active-directory/role-based-access-control-configure.md).

## <a name="service-principal"></a>Principal de serviço

Pode atribuir um [principal de serviço](../active-directory/develop/active-directory-application-objects.md) ao seu registo, e a aplicação ou serviço pode utilizá-lo para a autenticação sem interface. Permitem principais de serviço [acesso baseado em funções](../active-directory/role-based-access-control-configure.md) um registo, e pode atribuir vários principais de serviço para um registo. Vários principais de serviço permitem-lhe definir acesso diferentes para diferentes aplicações.

As funções disponíveis são:

  * **Leitor**: solicitação
  * **Contribuidor**: push e pull
  * **Proprietário**: solicitar, emitir e atribuir funções a outros utilizadores

Principais de serviço ativar a conetividade sem interface para um registo em push e pull cenários como o seguinte:

  * *Leitor*: implementações de contentores de um registo para sistemas de orquestração, incluindo Kubernetes, DC/OS e Docker Swarm. Pode também solicitar a partir os registos do contentor para os serviços do Azure relacionados, tais como [AKS](../aks/index.yml), [do serviço de aplicações](../app-service/index.yml), [Batch](../batch/index.md), [Service Fabric](/azure/service-fabric/), e outras pessoas.

  * *Contribuidor*: soluções de integração e a implementação contínua, como os serviços de equipa do Visual Studio (VSTS) ou Jenkins que criar imagens de contentor e emiti-las para um registo.

> [!TIP]
> Pode voltar a gerar a palavra-passe de um principal de serviço, executando o [az sp reposição-as credenciais de ad](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials) comando.
>

Também pode iniciar sessão diretamente com um principal de serviço. Forneça o ID da aplicação e a palavra-passe do serviço principal para o `docker login` comando:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Assim que a sessão iniciada, Docker coloca em cache as credenciais, por isso não terá de memorizar o ID de aplicação.

Dependendo da versão do Docker tiver instalado, poderá ver um aviso de segurança, recomendamos a utilização do `--password-stdin` parâmetro. Enquanto a sua utilização está fora do âmbito deste artigo, recomendamos a seguir esta melhor prática. Para obter mais informações, consulte o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) referência de comandos.

## <a name="admin-account"></a>Conta de administrador

Cada registo de contentor inclui uma conta de utilizador administrador, que está desativada por predefinição. Pode permitir que o utilizador de admin e gerir as suas credenciais no [portal do Azure](container-registry-get-started-portal.md#create-a-container-registry), ou utilizando a CLI do Azure.

> [!IMPORTANT]
> A conta de administrador foi concebida para um único utilizador aceder ao registo, principalmente para fins de teste. Recomendamos que não as credenciais da conta de administrador de partilha com vários utilizadores. Todos os utilizadores a autenticação com a conta de administrador são apresentados como um único utilizador com acesso de push e pull no registo. Alterar ou desativar esta conta desativa para todos os utilizadores que utilizam as respetivas credenciais de acesso ao registo. Identidade individuais é recomendada para utilizadores e de principais de serviço para cenários sem interface.
>

A conta de administrador é fornecida com duas palavras-passe, que podem ser novamente geradas. Duas palavras-passe permitem-lhe manter a ligação ao registo, utilizando uma palavra-passe enquanto volta a gerar a outra. Se estiver ativada a conta de administrador, pode passar o nome de utilizador e a palavra-passe para o `docker login` comando para a autenticação básica para o registo. Por exemplo:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Novamente, Docker recomenda que utilize o `--password-stdin` parâmetro em vez de indicando-a na linha de comandos para maior segurança. Também pode especificar apenas o nome de utilizador, sem `-p`e introduza a palavra-passe quando lhe for pedido.

Para permitir que o utilizador de administrador para um registo existente, pode utilizar o `--admin-enabled` parâmetro o [atualização de acr az](/cli/azure/acr?view=azure-cli-latest#az_acr_update) comando na CLI do Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pode ativar o utilizador de administrador no portal do Azure, navegando o registo, selecionar **chaves de acesso** em **definições**, em seguida, **ativar** em **Admin utilizador**.

![Permitir que o utilizador de admin da IU no portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Passos seguintes

* [Push primeira imagem utilizando a CLI do Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
