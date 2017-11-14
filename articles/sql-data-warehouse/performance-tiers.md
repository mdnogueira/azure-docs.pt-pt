---
title: "Camadas de desempenho do armazém de dados SQL do Azure | Microsoft Docs"
description: "Introdução ao elasticidade e os escalões de desempenho otimizado de computação disponíveis no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: c403a73d03fd5152e2c0617b3e3784926c28f5c3
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Escalões de desempenho do armazém de dados SQL do Azure (pré-visualização)
O SQL Data Warehouse oferece dois escalões de desempenho que estão otimizados para cargas de trabalho analíticas. Este artigo explica os conceitos das camadas de desempenho para o ajudar a escolher o escalão de desempenho mais adequado para a carga de trabalho. 


## <a name="what-is-a-performance-tier"></a>O que é uma camada de desempenho?
Um escalão de desempenho é uma opção que determina a configuração do seu armazém de dados. Esta opção é uma das opções de primeiro que efetuar durante a criação de um armazém de dados.  

- O **escalão de desempenho Otimizado para Elasticidade** separa as camadas de armazenamento e computação na arquitetura. Esta opção é ideal para as cargas de trabalho que podem tirar partido da separação entre a computação e o armazenamento ao dimensionar frequentemente para suportar curtos períodos de atividade de pico. Este escalão de computação tem o nível de preço de entrada mais baixo e é dimensionado para suportar a maioria das cargas de trabalho do cliente.

- O **escalão de desempenho Otimizado para Computação** utiliza o hardware do Azure mais recente para introduzir uma nova cache do Disco de Estado Sólido NVMe que mantém os dados acedidos mais frequentemente próximos das CPUs, que é exatamente onde os quer. Ao escalar automaticamente o armazenamento, este escalão de desempenho é a opção ideal para consultas complexas, uma vez que a E/S é mantida no local para a camada de computação. Além disso, o columnstore foi melhorado para armazenar uma quantidade ilimitada de dados no SQL Data Warehouse. O escalão de desempenho Otimizado para Computação fornece o maior nível de escalabilidade, ao permitir dimensionar até 30.000 Unidades do Data Warehouse (cDWU). Escolha este escalão para cargas de trabalho que requerem um desempenho contínuo e incrivelmente rápido.

## <a name="service-levels"></a>Níveis de serviço
O objetivo de nível de serviço (SLO) é a definição de escalabilidade que determina o nível de desempenho e custo do seu armazém de dados. Os níveis de serviço para Optimized de computação de dimensionamento de camada de desempenho são medidos em computação unidades do data warehouse (cDWU), por exemplo DW2000c. Optimized para níveis de serviço de elasticidade é medido em DWUs, por exemplo, DW2000. Para obter mais informações, consulte [o que é uma unidade do armazém de dados?](what-is-a-data-warehouse-unit-dwu-cdwu.md)

Em T-SQL a definição de SERVICE_OBJECTIVE determina o nível de serviço e a camada de desempenho para o seu armazém de dados.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>Valores máximos de memória
Os escalões de desempenho tem os perfis de memória diferente, traduz-se em período diferentes de memória por consulta. Optimized para a camada de desempenho de computação fornece 2,5 x mais memória por consulta que Optimized para a camada de desempenho de elasticidade. Esta memória adicional ajuda Optimized, para a camada de desempenho de computação, fornecer o desempenho dele incrivelmente rápido. Memória adicional por consulta também permite-lhe executar consultas mais em simultâneo, uma vez que podem utilizar consultas inferior [classes de recurso](resource-classes-for-workload-management.md). 

### <a name="optimized-for-elasticity"></a>Otimizado para Elasticidade

Os níveis de serviço para Optimized para o intervalo de camada de desempenho do elasticidade entre DW100 a DW6000. 

| Nível de serviço | Consultas em simultâneo máx. | Nós de computação | Distribuições por nó de computação | Memória máxima por distribuição (MB) | Memória máxima do armazém de dados (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1,600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2,000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2,400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4,000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4,800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6,000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8,000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1440                               |

### <a name="optimized-for-compute"></a>Otimizado para computação

Os níveis de serviço para Optimized para o intervalo de camada de desempenho do processamento de DW1000c a DW30000c. 

| Nível de serviço | Consultas em simultâneo máx. | Nós de computação | Distribuições por nó de computação | Memória máxima por distribuição (GB) | Memória máxima do armazém de dados (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

O máximo cDWU é DW30000c, que tem 60 nós de computação e de uma distribuição por nó de computação. Por exemplo, um armazém de dados a 600 TB no DW30000c processa aproximadamente 10 TB por nó de computação.

## <a name="concurrency-maximums"></a>Valores máximos de concorrência
Armazém de dados do SQL Server fornece simultaneidade líder da indústria num armazém de dados individual. Para garantir que cada consulta tem recursos suficientes para executar o sistema de forma eficiente, controla computação utilização de recursos através da atribuição de ranhuras de concorrência para cada consulta. O sistema coloca consultas para uma fila onde Aguarde até que o suficiente ranhuras de concorrência estão disponíveis. 

Ranhuras de concorrência também determinam a atribuição de prioridades de CPU. Para obter mais informações, consulte [analisar a carga de trabalho](analyze-your-workload.md)

### <a name="concurrency-slots"></a>Ranhuras de concorrência
Ranhuras de concorrência são uma maneira conveniente para controlar os recursos disponíveis para execução da consulta. São, como pedidos de suporte que comprar para reservar utilizadores individuais num concert porque seating está limitado. Da mesma forma, o armazém de dados do SQL Server tem um número finito de recursos de computação. Consultas reservar os recursos de computação por adquirir ranhuras de concorrência. Antes de iniciar uma consulta em execução, tem de ser capaz de reserva suficiente ranhuras de concorrência. Quando uma consulta estiver concluída, versões respetivas ranhuras de concorrência. 

* O otimizado para a camada de desempenho de elasticidade dimensiona 240 ranhuras de concorrência.
* O otimizado para escalas de camada de desempenho de computação para as ranhuras de simultaneidade de 1200.

Cada consulta irá consumir zero, um ou mais ranhuras de concorrência. Consultas de sistema e algumas consultas Triviais não consumir quaisquer ranhuras. Por predefinição, as consultas que são reguladas pelas classes de recurso requerem um bloco de concorrência. Consultas mais complexas podem necessitar de ranhuras de concorrência adicionais.  

- Uma consulta com 10 ranhuras de concorrência pode aceder a recursos de computação mais de 5 vezes que uma consulta com 2 ranhuras de concorrência.
- Se cada consulta requer 10 ranhuras de simultaneidade e existem 40 ranhuras de simultaneidade, 4 apenas consultas podem executar em simultâneo.
 
Apenas as consultas de recursos regida consumam ranhuras de concorrência. O número exato de ranhuras de concorrência consumido é determinado pela consulta [classe de recursos](resource-classes-for-workload-management.md).

### <a name="optimized-for-compute"></a>Otimizado para computação
A tabela seguinte mostra as consultas em simultâneo máximas e ranhuras de concorrência para cada [classe de recursos dinâmicos](resource-classes-for-workload-management.md).  Estas aplicam-se para Optimized para a camada de desempenho de computação.

**Classes de recursos dinâmicos**
| Nível de serviço | Consultas em simultâneo máximas | Ranhuras de concorrência disponíveis | Ranhuras utilizadas pelo smallrc | Ranhuras utilizadas pelo mediumrc | Ranhuras utilizadas pelo largerc | Ranhuras utilizadas pelo xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**Classes de recurso estático**

A tabela seguinte mostra as consultas em simultâneo máximas e ranhuras de concorrência para cada [classe de recursos estático](resource-classes-for-workload-management.md).  

| Nível de serviço | Consultas em simultâneo máximas | Ranhuras de concorrência disponíveis |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Otimizado para Elasticidade
A tabela seguinte mostra as consultas em simultâneo máximas e ranhuras de concorrência para cada [classe de recursos dinâmicos](resource-classes-for-workload-management.md).  Estas aplicam-se para Optimized para a camada de desempenho de elasticidade.

**Classes de recursos dinâmicos**

| Nível de serviço | Consultas em simultâneo máximas | Ranhuras de concorrência disponíveis | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  32                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  32                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  32                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  48                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         |  64                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 128                         | 1       | 32       | 64      | 128      |

**Classes de recurso estático** a tabela seguinte mostra as consultas em simultâneo máximas e ranhuras de concorrência para cada [classe de recursos estático](resource-classes-for-workload-management.md).  Estas aplicam-se para Optimized para a camada de desempenho de elasticidade.

| Nível de serviço | Consultas em simultâneo máximas | Ranhuras de concorrência máximo |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Quando for cumprida uma destes limiares, consultas novas são colocados em fila e executadas numa base first in, First Out.  Como um consultas concluída e o número de consultas e de ranhuras descerem abaixo os limites, o SQL Data Warehouse versões consultas em fila. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como tirar partido das classes de recursos para otimizar ainda mais a carga de trabalho, reveja os artigos seguintes:
* [Classes de recursos para a gestão de carga de trabalho](resource-classes-for-workload-management.md)
* [Analisar a carga de trabalho](analyze-your-workload.md)

