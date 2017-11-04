---
title: "Serviço de base de dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre os escalões de serviço para único e bases de dados de conjunto para fornecer os níveis de desempenho e tamanhos de armazenamento."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 55f59fddee008eb42b7252d6368a56873a6abd16
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Quais são os escalões de serviço do SQL Database do Azure

[Base de dados SQL do Azure](sql-database-technical-overview.md) oferece **básico**, **padrão**, **Premium**, e **Premium RS** camadas para ambos os doserviço[único bases de dados](sql-database-single-database-resources.md) e [conjuntos elásticos](sql-database-elastic-pool.md). Escalões de serviço são principalmente diferenciadas por um intervalo de nível de desempenho e opções de tamanho de armazenamento e preços.  Todos os escalões de serviço fornecem flexibilidade na alteração do tamanho de armazenamento e de nível de desempenho.  Bases de dados individuais e conjuntos elásticos são faturados por hora com base na camada de serviço, o nível de desempenho e o tamanho de armazenamento.   

## <a name="choosing-a-service-tier"></a>Escolher uma camada de serviços

Escolher uma camada de serviço depende principalmente continuidade do negócio, armazenamento e requisitos de desempenho.
| | **Básica** | **Standard** |**Premium** |**Premium RS** |
| :-- | --: |--:| --:| --:| 
|Carga de trabalho de destino|Desenvolvimento e de produção|Desenvolvimento e de produção|Desenvolvimento e de produção|Carga de trabalho que pode tolerar a perda de dados até 5 minutos devido a falhas de serviço|
|SLA de Tempo de Atividade|99,99%|99,99%|99,99%|N/d enquanto em pré-visualização|
|Retenção da cópia de segurança|7 dias|35 dias|35 dias|35 dias|
|CPU|Baixo|Baixa, média, alta|Média, alta|Médio|
|Débito de e/s|Baixo  | Médio | Ordem de grandeza superior ao padrão|Mesmo que Premium|
|Latência de e/s|Superior ao Premium|Superior ao Premium|Inferior ao básico e Standard|Mesmo que Premium|
|A indexação Columnstore e OLTP na memória|N/D|N/D|Suportado|Suportado|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Limites de tamanho de armazenamento e de nível de desempenho

Níveis de desempenho são expressas em termos de unidades de transação de base de dados (DTUs) para bases de dados individuais e unidades de transação da base de dados elásticas (eDTUs) para conjuntos elásticos. Para mais informações sobre as DTUs e eDTUs, consulte [quais são as DTUs e eDTUs?](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Bases de dados individuais

|  | **Básica** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Armazenamento máximo tamanho * | 2GB | 1 TB | 4 TB  | 1 TB  |
| DTUs máximas | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>Conjuntos elásticos

| | **Básica** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento por base de dados *  | 2GB | 1 TB | 1 TB | 1 TB |
| Tamanho máximo de armazenamento por agrupamento * | 156 GB | 4 TB | 4 TB | 1 TB |
| EDTUs máximo por base de dados | 5 | 3000 | 4000 | 1000 |
| Máximo eDTUs por conjunto | 1600 | 3000 | 4000 | 1000 |
| Número máximo de bases de dados por agrupamento | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \*Tamanhos de armazenamento maiores do que a quantidade de armazenamento incluído estão em pré-visualização e são aplicados custos adicionais. Para detalhes, ver os preços da [Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* No escalão Premium, mais de 1 TB de armazenamento estão atualmente disponível nas seguintes regiões: E.U.A. Leste 2, E.U.A. Oeste, Gov (US) - Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Ver [Limitações Atuais P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Para obter detalhes sobre as opções de tamanho de armazenamento disponíveis e níveis de desempenho específicos, consulte [dos limites de recursos de base de dados SQL](sql-database-resource-limits.md).


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [único recursos de base de dados](sql-database-single-database-resources.md).
- Saiba mais sobre conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).
- Saiba mais sobre [subscrição do Azure e limites de serviço, Quotas e restrições](../azure-subscription-service-limits.md)
* Saiba mais sobre [DTUs e eDTUs](sql-database-what-is-a-dtu.md).
* Saiba mais sobre a monitorização de utilização, consulte [monitorização e a otimização de desempenho](sql-database-troubleshoot-performance.md).

