---
title: "Recomendações do Advisor de desempenho do Azure | Microsoft Docs"
description: "Utilize o Advisor para otimizar o desempenho das implementações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 995a1f37a3fd68b39c14a95d46109c0f7814018d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-performance-recommendations"></a>Recomendações de desempenho do Assistente

Recomendações de desempenho do Assistente do Azure ajudam a melhorar a velocidade e a capacidade de resposta das suas aplicações empresariais vitais. Pode obter recomendações de desempenho do Advisor no **desempenho** separador do dashboard do Advisor.

![Separador de desempenho do Advisor](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhorar o desempenho de base de dados com o Assistente de base de dados SQL

Advisor disponibiliza uma vista consolidada consistente de recomendações para todos os seus recursos do Azure. É integrado com o Assistente de base de dados do SQL para assegurar as recomendações para melhorar o desempenho da base de dados SQL Azure. Assistente de base de dados SQL avalia o desempenho das bases de dados SQL Azure ao analisar o histórico de utilização. Em seguida, oferece recomendações que melhor se adequam para executar a carga de trabalho normal da base de dados. 

> [!NOTE]
> Para obter recomendações, uma base de dados tem de ter sobre uma semana de utilização e dentro dessa semana tem de existir alguma atividade consistente. Pode otimizar a SQL Database Advisor mais facilmente para padrões de consulta consistente que para bursts aleatórias de atividade.

Para obter mais informações sobre o Advisor de base de dados do SQL Server, consulte [SQL Database Advisor](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![Recomendações de base de dados do SQL Server](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="improve-redis-cache-performance-and-reliability"></a>Melhorar a fiabilidade e desempenho da Cache de Redis

Advisor identifica as instâncias de Cache de Redis onde desempenho poderá ser afetado negativamente por utilização elevada da memória, carga de servidor, a largura de banda de rede ou um grande número de ligações de cliente. O Advisor também fornece as melhores práticas recomendações para o ajudar a evitar potenciais problemas. Para obter mais informações sobre as recomendações de Cache de Redis, consulte [Advisor de Cache de Redis](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Melhorar a fiabilidade e desempenho do serviço de aplicações

Advisor do Azure integra-se as recomendações das melhores práticas para melhorar a sua experiência de serviços aplicacionais e deteção de funções de plataforma relevantes. Exemplos de recomendações de serviços aplicacionais são:
* Deteção de instâncias em que os recursos de CPU ou memória ficam esgotados por tempos de execução de aplicações com as opções de mitigação.
* Deteção de instâncias onde collocating recursos que as aplicações web e bases de dados pode melhorar o desempenho e custo mais baixo. 

Para obter mais informações sobre as recomendações de serviços de aplicações, consulte [melhores práticas para o App Service do Azure](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![Recomendações de serviços de aplicação](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como aceder recomendações de desempenho no Advisor

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **mais serviços**.

3. No painel de menu de serviço, em **monitorização e gestão**, clique em **Azure Advisor**.  
 É apresentado o dashboard do Advisor.

4. No dashboard do Advisor, clique em de **desempenho** separador.

5. Selecione a subscrição para o qual pretende receber recomendações e, em seguida, clique em **obter recomendações**.

> [!NOTE]
> Para obter recomendações do Assistente de acesso, primeiro tem *registar a sua subscrição* com o Advisor. Uma subscrição é registada quando um *subscrição proprietário* inicia o dashboard do Advisor e clica no **obter recomendações** botão. Este é um *operação única*. Depois da subscrição está registada, pode aceder recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* para uma subscrição, um grupo de recursos ou um recurso específico.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do assistente, consulte:

* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Assistente](advisor-performance-recommendations.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)

