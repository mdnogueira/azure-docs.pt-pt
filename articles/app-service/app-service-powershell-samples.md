---
title: "Exemplos do PowerShell do Azure - serviço de aplicações | Microsoft Docs"
description: "Exemplos do PowerShell do Azure - serviço de aplicações"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: b587f299e7b11effd05cbbc4b15976fee3f83ffd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-powershell-samples"></a>Exemplos do PowerShell do Azure

A tabela seguinte inclui ligações para bash scripts criadas com o Azure PowerShell.

| | |
|-|-|
|**Criar aplicação**||
| [Criar uma aplicação web com a implementação a partir do GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação web do Azure que obtém código a partir do GitHub. |
| [Criar uma aplicação Web com implementação contínua do GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação web do Azure que implementa continuamente código a partir do GitHub. |
| [Criar uma aplicação web e implementar código com FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um web do Azure ficheiros de aplicação e o carregamento de um diretório local a utilizar FTP. |
| [Criar uma aplicação Web e implementar código a partir de um repositório do Git local](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação web do Azure e configura o push de código do repositório de Git local. |
| [Criar uma aplicação Web e implementar código para um ambiente de teste](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação web do Azure com uma ranhura de implementação para alterações de código de teste. |
|**Configure a aplicação**||
| [Mapear domínios personalizados para uma aplicação Web](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação web do Azure e mapeia um nome de domínio personalizado ao mesmo. |
| [Vincular um certificado SSL personalizado para uma aplicação web](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação web do Azure e vincula o certificado SSL de um nome de domínio personalizado ao mesmo. |
|**Aplicação de escala**||
| [Dimensionar uma aplicação Web manualmente](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação web do Azure e dimensiona-lo em 2 instâncias. |
| [Dimensionar uma aplicação Web para todo o mundo com uma arquitetura de elevada disponibilidade](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria duas aplicações web do Azure em duas regiões geográficas diferentes e disponibiliza-los através de um único ponto final utilizando o Gestor de tráfego do Azure. |
|**Ligar a aplicação aos recursos**||
| [Ligar uma aplicação web numa base de dados do SQL Server](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação web do Azure e uma base de dados do SQL Server e, em seguida, adiciona a cadeia de ligação de base de dados para as definições da aplicação. |
| [Ligar uma aplicação Web a uma conta de armazenamento](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma aplicação web do Azure e uma conta de armazenamento, em seguida, adiciona a cadeia de ligação de armazenamento para as definições da aplicação. |
|**Cópia de segurança e restaurar a aplicação**||
| [Cópia de segurança de uma aplicação web](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação web do Azure e cria uma única cópia de segurança para o mesmo. |
| [Criar uma cópia de segurança agendada para uma aplicação web](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação web do Azure e cria uma cópia de segurança agendada para o mesmo. |
| [Eliminar uma cópia de segurança para uma aplicação web](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimina uma cópia de segurança existente para uma aplicação web. |
|**Monitorizar aplicação**||
| [Monitorizar uma aplicação Web com os registos do servidor Web](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma aplicação web do Azure, ativa o registo para a mesma e transfere os registos no seu computador local. |
| | |
