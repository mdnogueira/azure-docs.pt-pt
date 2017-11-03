---
title: Os limites de recursos de base de dados SQL do Azure | Microsoft Docs
description: "Esta página descreve alguns limites de recursos comuns para a SQL Database do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 2e0acc3cc09de4293dcc049c37bee6b899e6101a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-resource-limits"></a>Limites de recursos de base de dados SQL do Azure

## <a name="single-database-storage-sizes-and-performance-levels"></a>Base de dados individual: tamanhos de armazenamento e níveis de desempenho

Para bases de dados individuais, as tabelas seguintes mostram os recursos disponíveis para uma base de dados a cada nível de desempenho e o escalão de serviço. Pode definir a camada de serviço, o nível de desempenho e a quantidade de armazenamento para uma única base de dados utilizando o [portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), o [CLI do azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), ou o [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Base de dados individual: Altere o tamanho de armazenamento

- O preço DTU para uma base de dados inclui uma determinada quantidade de armazenamento sem custos adicionais. Armazenamento adicional para além da quantidade incluída pode ser aprovisionado um custo adicionais até ao limite de tamanho máximo em incrementos de 250 GB de cópia de segurança para 1 TB e, em seguida, em incrementos de 256 GB para além de 1 TB. Para quantidades incluídas do armazenamento e limites de tamanho máximo, consulte [única base de dados: tamanhos de armazenamento e níveis de desempenho](#single-database-storage-sizes-and-performance-levels).
- Armazenamento adicional para uma base de dados individual pode ser aprovisionado pelos aumentar o tamanho máximo utilizando o [portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure CLI](/cli/azure/sql/db#az_sql_db_update), ou o [REST API](/rest/api/sql/databases/update).
- O preço de armazenamento adicional para uma base de dados é a quantidade de armazenamento adicional multiplicada pelo preço de unidade de armazenamento adicional da camada de serviços. Para obter detalhes sobre os preços de armazenamento adicional, consulte [preços da SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Base de dados individual: Altere as DTUs

Após inicialmente escolher uma camada de serviço, o nível de desempenho e a quantidade de armazenamento, pode dimensionar uma base de dados ou reduzir verticalmente dinamicamente com base na experiência real utilizando o [portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az_sql_db_update), ou o [REST API](/rest/api/sql/databases/update). 

O vídeo seguinte mostra dinamicamente a alteração da camada de desempenho para aumentar as DTUs disponíveis para uma base de dados.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Alterar o escalão de serviço e/ou nível de desempenho de uma base de dados cria uma réplica da base de dados original ao nível do novo desempenho e, em seguida, troca ligações para a réplica. Não são perdidos dados durante este processo mas durante o breve momento em que trocamos para a réplica, as ligações à base de dados são desativadas, pelo que algumas transações em voo poderão ser revertidas. O período de tempo para o comutador a ativação pós-falha varia, mas é, geralmente, é de 4 segundos menos de 30 segundos 99% das vezes. Se existirem grande número de transações em trânsito, as ligações do momento em que está desativado, o período de tempo para o comutador a ativação pós-falha pode ser maior. 

A duração de todo o processo de aumento vertical depende do tamanho e do escalão de serviço da base de dados antes e após a alteração. Por exemplo, uma base de dados de 250 GB que está a mudar para ou dentro de uma camada de serviço Standard, deve ser concluído dentro de seis horas. Para uma base de dados do mesmo tamanho que está a alterar os níveis de desempenho na camada de serviço Premium, a escala cópia de segurança deve ser concluído dentro de três horas.

> [!TIP]
> Para verificar o estado de uma base de dados do SQL Server em curso operação de dimensionamento, pode utilizar a seguinte consulta: ```select * from sys.dm_operation_status```.
>

* Se estiver a atualizar para um nível de desempenho ou camada de serviço mais elevado, o tamanho máximo da base de dados não aumenta a menos que especifique explicitamente um tamanho maior (maxsize).
* Para mudar uma base de dados, o espaço de base de dados utilizada tem de ser menor que o tamanho máximo permitido do nível de desempenho e o escalão de serviço do destino. 
* Quando desatualização de **Premium** ou **Premium RS** para o **padrão** camada, um custo de armazenamento adicional se aplica se ambos os (1) o tamanho máximo da base de dados é suportado no destino nível de desempenho e (2) o tamanho máximo excede a quantidade de armazenamento incluída com o nível de desempenho de destino. Por exemplo, se uma base de dados P1 com um tamanho máximo de 500 GB é downsized para S3, em seguida, um custo de armazenamento adicional aplica-se porque S3 suporta um tamanho máximo de 500 GB e a quantidade de armazenamento incluída é apenas de 250 GB. Por isso, a quantidade de armazenamento adicional é 500 GB – 250 GB = 250 GB. Para obter preços de armazenamento adicional, consulte [preços da SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizado é inferior à quantidade incluída de armazenamento, em seguida, este custo extra pode ser evitada ao reduzir o tamanho máximo da base de dados para a quantidade incluída. 
* Ao atualizar a base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, atualizar suas bases de dados secundárias para a camada de desempenho pretendido antes de atualizar a base de dados primária (orientações gerais). Quando atualizar para o outro, atualizar a base de dados secundária primeiro é necessário.
* Quando a desatualização de uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, mudar o respetivos bases de dados primárias para a camada de desempenho pretendido antes desatualização de base de dados secundária (orientações gerais). Quando a mudança para uma edição diferente, desatualização de base de dados primária primeiro é necessário.
* As ofertas de serviço de restauro diferem entre os vários escalões de serviço. Se estão a mudança para o **básico** camada, há um período de retenção de cópias de segurança inferior - consulte [cópias de segurança do Azure SQL da base de dados](sql-database-automated-backups.md).
* As novas propriedades para a base de dados não são aplicadas até que as alterações estejam concluídas.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Base de dados individual: limitações de P11 e P15 quando o número máximo tamanho superior a 1 TB

Um tamanho máximo superior a 1 TB para base de dados P11 e P15 é suportada nas seguintes regiões: East2 nos, EUA oeste, Virginia us dos EUA, Europa Ocidental, Alemanha Central, Sul Oriental, leste do Japão, leste da Austrália, Canadá Central e Canadá Leste. As seguintes considerações e limitações aplicam-se às bases de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se escolher um tamanho máximo superior a 1 TB ao criar uma base de dados (utilizando um valor de 4096 GB ou de 4 TB), o comando create falha com um erro se a base de dados é aprovisionado numa região não suportada.
- Para existentes P11 P15 bases de dados e localizados das regiões suportadas, pode aumentar o máximo de armazenamento para além de 1 TB em incrementos de 256 GB até 4 TB. Para ver se um tamanho maior é suportado na sua região, utilize o [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funcionar ou Inspecione o tamanho de base de dados no portal do Azure. Atualizar um P11 existente ou P15 base de dados só pode ser efetuada por um início de sessão principal ao nível do servidor ou por membros da função de base de dados de dbmanager. 
- Se uma operação de atualização é executada numa região suportada a configuração está atualizada imediatamente. A base de dados permanece online durante o processo de atualização. No entanto, não é possível utilizar a quantidade total de armazenamento para além de 1 TB de armazenamento até que os ficheiros de base de dados real foram atualizados para o novo tamanho máximo. O período de tempo necessário depende após o tamanho da base de dados a ser atualizado. 
- Quando criar ou atualizar uma base de dados P11 ou P15, apenas pode escolher entre 1 TB e 4 TB o tamanho máximo em incrementos de 256 GB. Ao criar um P11/P15, a opção de armazenamento predefinida de 1 TB é pré-seleccionado. Para bases de dados localizados das regiões suportadas, pode aumentar o número máximo de armazenamento para até um máximo de 4 TB para uma base de dados nova ou existente. Para todas as outras regiões, não é possível aumentar o tamanho máximo superiores a 1 TB. O preço não se altera quando seleciona 4 TB de armazenamento incluído.
- Se o tamanho máximo de uma base de dados está definido como superior a 1 TB, em seguida, este não pode ser alterada para 1 TB, mesmo que o armazenamento real utilizado é inferior a 1 TB. Assim, a não é possível mudar um P11 ou P15 com um tamanho máximo superior a 1 TB para 1 TB P11 ou desempenho inferior a 1 TB P15 camada, tais como P1 P6). Esta restrição aplica-se também de restauro e cenários de cópia, incluindo o ponto no tempo, georrestauro, longo-prazo-cópia de segurança-retenção e a cópia da base de dados. Depois de uma base de dados está configurado com um tamanho máximo superior a 1 TB, todas as operações de restauro desta base de dados devem ser executadas para um P11/P15 com um tamanho máximo superior a 1 TB.
- Para cenários de replicação geográfica activa:
   - Configurar uma relação de replicação geográfica: se a base de dados primária estiver P11 ou P15, o secondary(ies) também tem de ser P11 ou P15; os escalões de desempenho inferiores são rejeitados como bases de dados secundárias, uma vez que não são capazes de oferecer suporte mais de 1 TB.
   - Atualizar a base de dados primária numa relação de replicação geográfica: alterar o tamanho máximo para mais de 1 TB numa base de dados primária aciona a mesma alteração na base de dados secundária. Ambas as atualizações tem de ser efetuada com êxito para a alteração principal tenha efeito. Aplicam limitações de região para a opção de mais de 1 TB. Se secundário está numa região que não suporta mais de 1 TB, o principal não está atualizado.
- Não é suportada a utilização do serviço de importação/exportação para carregar P11/P15 bases de dados com mais de 1 TB. Utilizar SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Agrupamento elástico: tamanhos de armazenamento e níveis de desempenho

Para conjuntos elásticos SQL Database, as tabelas seguintes mostram os recursos disponíveis em cada nível de desempenho e o escalão de serviço. Pode definir a camada de serviço, nível de desempenho e quantidade de armazenamento utilizando o [portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), a [CLI do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), ou o [API de REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Os limites de recursos de bases de dados individuais em conjuntos elásticos, geralmente, são iguais às bases de dados individuais fora conjuntos com base no DTUs e a camada de serviço. Por exemplo, os trabalhadores simultâneos máximas para uma base de dados de S2 é 120 workers. Por isso, os trabalhadores simultâneos máximas para uma base de dados num conjunto Standard também é 120 trabalhadores se o máximo de DTUS por base de dados no conjunto é 50 DTUs (que é equivalente ao S2).
>

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Se todas as DTUs de um conjunto elástico forem utilizadas, cada base de dados no conjunto recebe uma quantidade igual de recursos para processar consultas. O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. A equidade de partilha de recursos de um conjunto elástico é adicional a qualquer quantidade de recursos garantido de outro modo a cada base de dados quando o mínimo de DTUs por base de dados está definido como um valor diferente de zero.

### <a name="database-properties-for-pooled-databases"></a>Propriedades de base de dados para bases de dados agrupados

A tabela seguinte descreve as propriedades para bases de dados agrupados.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por base de dados |O número máximo de eDTUs que qualquer base de dados no conjunto pode utilizar, caso estejam disponíveis com base na utilização por outras bases de dados no conjunto. O número máximo de eDTUs por base de dados não é uma garantia de recurso para uma base de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Defina um número máximo de eDTUs suficientemente elevado para processar picos na utilização de base de dados. É esperado algum grau de consolidação excessiva, uma vez que, geralmente, o conjunto assume padrões de utilização a frio e a quente para bases de dados em que todas as bases de dados não atingem o pico em simultâneo. Por exemplo, suponha que o pico de utilização por base de dados é 20 eDTUs e apenas 20% das 100 bases de dados no conjunto atingem o pico ao mesmo tempo. Se o número máximo de eDTUs por base de dados estiver definido como 20 eDTUs, é razoável sobreconsolidar o conjunto em 5 vezes e definir o número de eDTUs por conjunto como 400. |
| Mínimo de eDTUs por base de dados |O número mínimo de eDTUs que é garantido a qualquer base de dados no conjunto. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O mínimo de eDTUs por base de dados pode ser definido como 0, que é também o valor predefinido. Esta propriedade é definida como qualquer valor entre 0 e a utilização média de eDTUs por base de dados. O produto do número de bases de dados no conjunto e o número mínimo de eDTUs por base de dados não pode exceder as eDTUs por conjunto. Por exemplo, se um conjunto tiver 20 bases de dados e o número mínimo de eDTUs por base de dados definido como 10 eDTUs, as eDTUs por conjunto têm de ser, pelo menos, 200 eDTUs. |
| Armazenamento máximo por base de dados |O armazenamento máximo para uma base de dados num conjunto. As bases de dados agrupadas partilham o armazenamento do conjunto, pelo que o armazenamento da base de dados é limitado ao armazenamento mais pequeno do conjunto restante e ao armazenamento máximo por base de dados. Armazenamento máximo por base de dados refere-se ao tamanho máximo dos ficheiros de dados e não inclui o espaço utilizado por ficheiros de registo. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Agrupamento elástico: Altere o tamanho de armazenamento

- O preço de eDTU de um conjunto elástico inclui uma determinada quantidade de armazenamento sem custos adicionais. Armazenamento adicional para além da quantidade incluída pode ser aprovisionado um custo adicionais até ao limite de tamanho máximo em incrementos de 250 GB de cópia de segurança para 1 TB e, em seguida, em incrementos de 256 GB para além de 1 TB. Para quantidades incluídas do armazenamento e limites de tamanho máximo, consulte [conjunto elástico: tamanhos de armazenamento e níveis de desempenho](#elastic-pool-storage-sizes-and-performance-levels).
- Armazenamento adicional de um conjunto elástico pode ser aprovisionado pelos aumentar o tamanho máximo utilizando o [portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), ou o [REST API ](/rest/api/sql/elasticpools/update).
- O preço de armazenamento adicional de um conjunto elástico é a quantidade de armazenamento adicional multiplicada pelo preço de unidade de armazenamento adicional da camada de serviços. Para obter detalhes sobre os preços de armazenamento adicional, consulte [preços da SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Agrupamento elástico: alterar eDTUs

Pode aumentar ou diminuir os recursos disponíveis para um conjunto elástico com base no recurso necessidades utilizando o [portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), ou o [ REST API](/rest/api/sql/elasticpools/update).

- Quando rescaling eDTUs de conjunto, as ligações de base de dados que são ignoradas. Este é o mesmo comportamento como ocorre quando rescaling DTUs para uma base de dados (não num agrupamento). Para obter detalhes sobre a duração e o impacto de ignorados ligações para uma base de dados durante as operações de rescaling, consulte [Rescaling DTUs para uma base de dados único](#single-database-change-storage-size). 
- A duração como rescale eDTUs de conjunto pode depender a quantidade total de espaço de armazenamento utilizada por todas as bases de dados no conjunto. Em geral, a latência rescaling averages 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total utilizado por todas as bases de dados no conjunto é 200 GB, então, a latência esperada para rescaling conjunto é 3 horas ou menos. Em alguns casos dentro o escalão Standard ou Basic, a latência rescaling pode estar em cinco minutos, independentemente da quantidade de espaço utilizado.
- Em geral, a duração para alterar as eDTUs Mín por base de dados ou o número máximo de eDTUs por base de dados é de cinco minutos ou menos.
- Quando downsizing eDTUs de conjunto, o espaço do agrupamento utilizado tem de ser menor que o tamanho máximo permitido das eDTUs de camada e um conjunto de serviço do destino.
- Quando rescaling eDTUs de conjunto, um custo de armazenamento adicional se aplica se (1) o tamanho máximo de armazenamento do conjunto é suportado pelo agrupamento de destino e o tamanho máximo (2) o armazenamento excede a quantidade de armazenamento incluídas de agrupamento de destino. Por exemplo, se um conjunto padrão com um tamanho máximo de 100 GB de eDTU 100 é downsized para um conjunto padrão de eDTU 50, em seguida, um custo de armazenamento adicional aplica-se, uma vez que o agrupamento de destino suporta um tamanho máximo de 100 GB e a quantidade de armazenamento incluída é apenas de 50 GB. Por isso, a quantidade de armazenamento adicional é 100 GB – 50 GB = 50 GB. Para obter preços de armazenamento adicional, consulte [preços da SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizado é inferior à quantidade incluída de armazenamento, em seguida, este custo extra pode ser evitada ao reduzir o tamanho máximo da base de dados para a quantidade incluída. 

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>O que acontece quando são atingidos e limites de recursos de conjunto elástico da base de dados?

### <a name="compute-dtus-and-edtus"></a>(As DTUs e eDTUs) de computação

Quando a utilização de computação de base de dados (medida DTUs e eDTUs) se tornar elevada, consultar aumentos de latência e pode, mesmo que o limite de tempo. Nas seguintes condições, as consultas podem ser colocados em fila pelo serviço e são fornecidas os recursos para execução como recurso ficar livres.
Quando encontrar a utilização de computação de elevado, as opções de mitigação incluem:

- Aumentar o nível de desempenho da base de dados ou do conjunto elástico para fornecer a base de dados com mais DTUs ou eDTUs. Consulte [única base de dados: alterar DTUs](#single-database-change-dtus) e [conjunto elástico: alterar eDTUs](#elastic-pool-change-edtus).
- Otimizar as consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, consulte [consulta Tuning/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando utilizado de espaço de base de dados atinge o limite de tamanho máximo, insere a base de dados e as atualizações que aumentam o tamanho de dados falharem e os clientes recebem um [mensagem de erro](sql-database-develop-error-messages.md). SELECIONA a base de dados e eliminações continuam a ter êxito.

Quando encontrar a utilização do espaço elevada, opções de mitigação incluem:

- Aumentar o tamanho máximo da base de dados ou agrupamento elástico ou altere o nível de desempenho para obter mais armazenamento incluído. Consulte [dos limites de recursos de base de dados do SQL Server](sql-database-resource-limits.md).
- Se a base de dados está num conjunto elástico, em seguida, em alternativa a base de dados pode ser movido fora do conjunto para que o espaço de armazenamento não está partilhado com outras bases de dados.

### <a name="sessions-and-workers-requests"></a>Sessões e funcionários (pedidos) 

O número máximo de sessões simultâneas e funcionários é determinado pelo desempenho e a camada de nível de serviço (DTUs e eDTUs).  Novos pedidos são rejeitados quando são atingidos os limites de sessão ou de trabalho e os clientes recebem uma mensagem de erro. Enquanto o número de ligações disponíveis pode ser controlado através da aplicação, o número de funcionários em simultâneo, muitas vezes, é mais difícil estimar e controlar. Isto é particularmente verdadeiro durante períodos de carga máxima quando os limites de recursos de base de dados são atingidos e trabalhadores pile cópias de segurança devido a tempo consultas em execução. 

Quando encontrar elevada utilização de sessão ou de trabalho, opções de mitigação incluem:
- Aumentar o nível de desempenho ou camada de serviço do agrupamento de base de dados ou elástico. Consulte [única base de dados: Altere o tamanho de armazenamento](#single-database-change-storage-size), [única base de dados: alterar DTUs](#single-database-change-dtus), [conjunto elástico: Altere o tamanho de armazenamento](#elastic-pool-change-storage-size), e [conjunto elástico: alterar eDTUs ](#elastic-pool-change-edtus).
- Otimizar as consultas para reduzir a utilização de recursos de cada consulta, se a causa da utilização de trabalho maior se dever a contenção de recursos de computação. Para obter mais informações, consulte [consulta Tuning/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre os escalões de serviço, consulte [escalões de serviço](sql-database-service-tiers.md).
- Para obter informações sobre bases de dados individuais, consulte [único recursos de base de dados](sql-database-resource-limits.md).
- Para obter informações sobre conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).
- Para obter informações sobre os limites do Azure gerais, consulte [subscrição do Azure e limites de serviço, quotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre as DTUs e eDTUs, consulte [DTUs e eDTUs](sql-database-what-is-a-dtu.md).
