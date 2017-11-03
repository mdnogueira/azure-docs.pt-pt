---
title: "Amostras do CLI do Azure - serviço de aplicações | Microsoft Docs"
description: "Amostras do CLI do Azure - serviço de aplicações"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 6718694af487929d193dae54ecb2d85ece64887a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples"></a>Exemplos da CLI do Azure

A tabela seguinte inclui ligações para bash scripts compiladas com a CLI do Azure.

| | |
|-|-|
|**Criar aplicação**||
| [Criar uma aplicação Web e implementar código a partir do GitHub](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação web do Azure e implementa o código de um repositório do GitHub público. |
| [Criar uma aplicação Web com implementação contínua do GitHub](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação web do Azure com a publicação contínua de um repositório do GitHub que possui. |
| [Criar uma aplicação Web e implementar código a partir de um repositório do Git local](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação web do Azure e configura o push de código do repositório de Git local. |
| [Criar uma aplicação Web e implementar código para um ambiente de teste](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação web do Azure com uma ranhura de implementação para alterações de código de teste. |
| [Criar uma aplicação Web do Núcleo ASP.NET num contentor do Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação web do Azure no Linux e carrega uma imagem de Docker do Hub de Docker. |
|**Configure a aplicação**||
| [Mapear domínios personalizados para uma aplicação Web](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação web do Azure e mapeia um nome de domínio personalizado ao mesmo. |
| [Vincular um certificado SSL personalizado para uma aplicação web](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação web do Azure e vincula o certificado SSL de um nome de domínio personalizado ao mesmo. |
|**Aplicação de escala**||
| [Dimensionar uma aplicação Web manualmente](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação web do Azure e dimensiona-lo em 2 instâncias. |
| [Dimensionar uma aplicação Web para todo o mundo com uma arquitetura de elevada disponibilidade](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Cria duas aplicações web do Azure em duas regiões geográficas diferentes e disponibiliza-los através de um único ponto final utilizando o Gestor de tráfego do Azure. |
|**Ligar a aplicação aos recursos**||
| [Ligar uma aplicação web numa base de dados do SQL Server](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação web do Azure e uma base de dados do SQL Server e, em seguida, adiciona a cadeia de ligação de base de dados para as definições da aplicação. |
| [Ligar uma aplicação Web a uma conta de armazenamento](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma aplicação web do Azure e uma conta de armazenamento, em seguida, adiciona a cadeia de ligação de armazenamento para as definições da aplicação. |
| [Ligar uma aplicação web a uma cache de redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação web do Azure e uma cache de redis, em seguida, adiciona os detalhes de ligação redis para as definições da aplicação.) |
| [Ligar uma aplicação web à base de dados do Cosmos](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação web do Azure e uma base de dados do Cosmos e, em seguida, adiciona os detalhes da ligação de base de dados do Cosmos para as definições da aplicação. |
|**Monitorizar aplicação**||
| [Monitorizar uma aplicação Web com os registos do servidor Web](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma aplicação web do Azure, ativa o registo para a mesma e transfere os registos no seu computador local. |
| | |