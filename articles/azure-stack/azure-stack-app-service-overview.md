---
title: "Descrição geral do serviço de aplicações: pilha do Azure | Microsoft Docs"
description: "Descrição geral do serviço de aplicações na pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 19b712d622276b6521317d79c68fc093dba547db
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="app-service-on-azure-stack-overview"></a>Descrição geral do Serviço de Aplicações no Azure Stack

App Service do Azure na pilha do Azure é uma oferta de plataforma-como-um-serviço (PaaS) do Microsoft Azure disponíveis para a pilha do Azure. O serviço permite que os seus clientes - internos ou externos - criar web, API e as funções do Azure aplicações para qualquer plataforma ou dispositivo. Podem integrar as suas aplicações com as aplicações no local e automatizar os processos empresariais. Os operadores da nuvem de pilha do Azure podem executar aplicações de cliente em máquinas de virtuais (VMs) completamente geridas com à escolha de recursos VM partilhados ou VMs dedicadas.

App Service do Azure inclui capacidades para automatizar processos empresariais e alojamento de APIs da nuvem. Como um serviço integrado único, App Service do Azure lhe compor vários componentes – sites, RESTful APIs e processos de negócio – numa solução única.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Por que motivo oferta de serviço de aplicações do Azure na pilha do Azure?

São a seguir apresentadas algumas funcionalidades-chave e capacidades do App Service:
- **Várias linguagens e arquiteturas**: App Service tem suporte de primeira classe para ASP.NET, Node.js, Java, PHP e Python. Pode também executar o Windows PowerShell e outros scripts ou executáveis em VMs do App Service.
- **Otimização de DevOps**: configurar a integração contínua e a implementação com o GitHub, o local Git ou o BitBucket. Promova atualizações através de ambientes de teste. Gerir as suas aplicações no App Service utilizando o Azure PowerShell ou a interface de linha de comandos (CLI) de várias plataformas.
- **Integração do Visual Studio**: as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar e implementar aplicações.

## <a name="app-types-in-app-service"></a>Tipos de aplicação no App Service

App Service oferece vários tipos de aplicação, cada um dos quais foi concebida para alojar uma carga de trabalho específica:

- [Aplicações Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview) para o alojamento de Web sites e aplicações web.
- [API Apps](https://docs.microsoft.com/en-us/azure/app-service-api/app-service-api-apps-why-best-platform) para alojar as APIs RESTful.
- Funções do Azure para alojar o evento orientadas por cargas de trabalho sem servidor.

A aplicação word aqui refere-se ao alojamento de recursos dedicado à execução de uma carga de trabalho. Tendo a “aplicação Web” como exemplo, está provavelmente habituado a pensar numa aplicação Web como os recursos de computação e o código de aplicação que em conjunto fornecem a funcionalidade a um browser. Mas no App Service, uma aplicação web se os recursos de computação que fornece pilha do Azure para alojar o código da aplicação.

A aplicação pode ser composta por várias aplicações do App Service de diferentes tipos. Por exemplo, se a aplicação é composta por um front-end da web e de fim de uma API RESTful anterior, pode:
- Implementar ambos (front-end e api) numa aplicação Web única
- Implementar o código de front-end numa aplicação Web e o código de back-end numa aplicação API.

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>O que é um plano de serviço de aplicações?

O fornecedor de recursos do serviço de aplicações utiliza o mesmo código que utiliza o App Service do Azure. Como resultado, alguns conceitos comuns merecem que descreve. No App Service, o contentor de preço para aplicações chama-se o plano de serviço de aplicações. Representa o conjunto de máquinas de virtuais dedicados utilizado para conter as suas aplicações. Dentro de uma determinada subscrição, pode ter vários planos de serviço de aplicações.

No Azure, existem trabalhadores partilhados e dedicados. Uma função de trabalho partilhada suporta que aloja a aplicação multi-inquilino high-density e houver apenas um conjunto de trabalhadores partilhados. Servidores dedicados são utilizados por apenas um inquilino e são fornecidos em três tamanhos: pequeno, médio e grande. Não é possível sempre descritas as necessidades dos clientes no local ao utilizar esses termos. No App Service na pilha do Azure, os administradores do fornecedor de recursos podem definir os escalões de trabalho que pretendem disponibilizar. Com base nas suas necessidades exclusivas de alojamento, pode definir vários conjuntos dos trabalhadores partilhados ou conjuntos diferentes de trabalhadores dedicados. Ao utilizar as definições de camada de trabalho, pode, em seguida, definirem as suas próprias de SKUs de preço.

## <a name="portal-features"></a>Funcionalidades do portais

Serviço de aplicações na pilha do Azure utiliza a mesma IU que utiliza do App Service do Azure, tal como acontece com o back-end. Algumas funcionalidades estão desativadas e não estão funcionais na pilha do Azure. As expectativas específicos do Azure ou serviços que exigem essas funcionalidades não são ainda disponíveis na pilha do Azure.

## <a name="next-steps"></a>Passos seguintes


- [Antes de começar com o serviço de aplicações na pilha do Azure](azure-stack-app-service-before-you-get-started.md)
- [Instalar o fornecedor de recursos do serviço de aplicações](azure-stack-app-service-deploy.md)

Também pode experimentar o outro [plataforma como dos serviços de serviço (PaaS)](azure-stack-tools-paas-services.md), como o [fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) e [fornecedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md).
