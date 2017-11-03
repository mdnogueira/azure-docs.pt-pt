---
title: "Gerir dados históricos em tabelas temporais com a política de retenção | Microsoft Docs"
description: "Saiba como utilizar a política de retenção temporal para manter os dados históricos sob o seu controlo."
services: sql-database
documentationcenter: 
author: bonova
manager: drasumic
editor: 
ms.assetid: 76cfa06a-e758-453e-942c-9f1ed6a38c2a
ms.service: sql-database
ms.custom: develop databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 10/12/2016
ms.author: bonova
ms.openlocfilehash: b4e1524008837094b57a3df469439ceaebf9c166
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerir dados históricos em tabelas temporais com a política de retenção
As tabelas temporais podem aumentar o tamanho da base de dados com mais de tabelas regulares, especialmente se mantiver dados históricos para um período de tempo. Por conseguinte, a política de retenção de dados históricos é um aspeto importante de planeamento e a gerir o ciclo de vida de cada tabela temporal. As tabelas temporais na SQL Database do Azure são fornecidos com o mecanismo de retenção de fácil utilização que o ajuda a realizar esta tarefa.

Retenção de histórico temporais pode ser configurada a nível de tabela individuais, que permite aos utilizadores criar envelhecimento flexível políticas. Aplicar retenção temporal é simple: requer apenas um parâmetro a ser definido durante a alteração de criação ou de esquema de tabela.

Depois de definir a política de retenção, a base de dados do Azure SQL inicia a verificar regularmente se existem históricas linhas que são elegíveis para limpeza de dados automática. Identificação de linhas correspondentes e os respetivos remoção da tabela de histórico de ocorrer transparente, a tarefa em segundo plano que está agendada e executada pelo sistema. Condição de idade para as linhas da tabela de histórico é verificada com base na coluna que representa o fim do período SYSTEM_TIME. Se o período de retenção, por exemplo, é definido como seis meses, linhas de tabela qualificadas para a limpeza satisfazem a seguinte condição:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

No exemplo anterior, é assumido que **ValidTo** coluna corresponde ao fim do período SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção?
Antes de configurar a política de retenção para uma tabela temporal, verifique primeiro se a retenção de histórico temporal está ativada *ao nível da base de dados*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Sinalizador de base de dados **is_temporal_history_retention_enabled** está definido como ON, por predefinição, mas os utilizadores podem alterá-lo com a instrução ALTER DATABASE. Também automaticamente está definido como OFF após [ponto no restauro de tempo](sql-database-recovery-using-backups.md) operação. Para ativar a limpeza de retenção de histórico temporais para a base de dados, execute a seguinte instrução:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [!IMPORTANT]
> Pode configurar o período de retenção para o mesmo quando de tabelas temporais **is_temporal_history_retention_enabled** está OFF, mas a limpeza automática para linhas Desatualizadas não é acionada nesse caso.
> 
> 

Política de retenção é configurada durante a criação da tabela, especificando o valor do parâmetro HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Base de dados SQL do Azure permite-lhe especificar o período de retenção através da utilização de unidades de tempo diferente: dias, semanas, meses ou anos. Se HISTORY_RETENTION_PERIOD for omitido, é assumida retenção INFINITO. Também pode utilizar palavras-chave INFINITA explicitamente.

Em alguns cenários, poderá querer configurar a retenção após a criação da tabela ou alterar anteriormente configurado valor. Nesse caso utilize a instrução ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [!IMPORTANT]
> Definição de SYSTEM_VERSIONING como OFF *não preserva* valor de período de retenção. Definição de SYSTEM_VERSIONING como ON, sem HISTORY_RETENTION_PERIOD explicitamente especificado resultados no período de retenção INFINITO.
> 
> 

Para rever o estado atual da política de retenção, utilize a seguinte consulta que associa o sinalizador de ativação de retenção temporal ao nível da base de dados com períodos de retenção para tabelas individuais:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


## <a name="how-sql-database-deletes-aged-rows"></a>Como base de dados SQL elimina desatualizados linhas?
O processo de limpeza depende o esquema de índice da tabela de histórico. É importante notar que *apenas tabelas de histórico com um índice em cluster (columnstore ou de árvore B) podem ter a política de retenção finito configurada*. É criada uma tarefa em segundo plano para efetuar a limpeza de dados antigos para todas as tabelas temporais com período de retenção finito.
Lógica de limpeza para o índice em cluster rowstore (árvore B) elimina a linha desatualizada na segmentos mais pequenos (até 10 mil) que minimiza a pressão no registo de base de dados e um subsistema de e/s. Apesar de lógica de limpeza utiliza o índice de árvore B necessário, ordem de eliminação de linhas mais antigas do que o período de retenção não poderá ser garantido corretas. Por conseguinte, *não efetuar qualquer dependência na ordem limpeza nas suas aplicações*.

A tarefa de limpeza para o columnstore em cluster remove todo [grupos de linhas](https://msdn.microsoft.com/library/gg492088.aspx) em simultâneo (normalmente, contêm 1 milhões de linhas, cada), que é muito eficiente, especialmente quando os dados históricos são gerados durante a um ritmo elevado.

![Retenção columnstore em cluster](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Compressão de dados excelente e retenção eficiente limpeza torna columnstore um índice em cluster uma opção perfeita para cenários quando a carga de trabalho gera rapidamente elevada quantidade de dados históricos. Esse padrão é típica para intensiva [cargas de trabalho de processamento de transações que utilizam tabelas temporais](https://msdn.microsoft.com/library/mt631669.aspx) para registo de alterações e auditoria, análise de tendências ou ingestão de dados de IoT.

## <a name="index-considerations"></a>Considerações de índice
A tarefa de limpeza para tabelas com o índice em cluster rowstore requer índice a começar com a coluna correspondente ao fim do período SYSTEM_TIME. Se esses índice não existe, não é possível configurar um período de retenção finito:

*Tarifas de mensagens 13765, 16 nível, estado 1 <br> </br> a definição de período de retenção finito falhou na tabela temporal com versão do sistema 'temporalstagetestdb.dbo.WebsiteUserInfo' porque a tabela de histórico 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' não contém um índice em cluster necessário. Considere a criação de um columnstore em cluster ou o índice de árvore B, começando com a coluna correspondente ao fim do SYSTEM_TIME period, na tabela de histórico.*

É importante notar que a tabela de histórico predefinida criada pelo SQL Database do Azure já tem um índice em cluster, que está em conformidade com a política de retenção. Se tentar remover o índice numa tabela com o período de retenção finito, operação falha com o seguinte erro:

*Tarifas de mensagens 13766, 16 nível, estado 1 <br> </br> não é possível remover o índice em cluster 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' porque está a ser utilizado para a limpeza automática dos dados desatualizados. Pondere a definição HISTORY_RETENTION_PERIOD INFINITA na tabela temporal com versão do sistema correspondente para se precisar de remover este índice.*

Limpeza no índice columnstore em cluster executadas de forma ideal funciona se históricos linhas estão inseridas na ordem ascendente (ordenada no final de coluna de período), que é sempre o caso, quando a tabela de histórico é preenchida exclusivamente para o mecanismo SYSTEM_VERSIONIOING. Se as linhas na tabela de histórico não são ordenadas pela extremidade da coluna de período (que pode ser o caso, se tiver migrado os dados históricos existentes), deve voltar a criar o índice columnstore em cluster por cima do índice rowstore árvore B que é ordenado corretamente, para alcançar um desempenho ideal.

Evite recriar o índice columnstore em cluster na tabela de histórico com o período de retenção finito, porque pode alterar a ordenação nos grupos de linhas naturalmente impostos a operação de controlo de versões de sistema. Se precisar de reconstruir o índice columnstore em cluster na tabela de histórico, fazê-lo ao voltar a criar por cima do índice de árvore B em conformidade, preservando a ordenação no rowgroups necessário para a limpeza de dados normal. A mesma abordagem deverão ser executada se criar a tabela temporal com a tabela de histórico existente que tem um índice de coluna sem a ordem dos dados garantida em cluster:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Quando o período de retenção finito está configurado para a tabela de histórico com o índice columnstore em cluster, não é possível criar índices de árvore B agrupado adicionais nessa tabela:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Uma tentativa de execução acima da declaração de falha com o erro seguinte:

*Tarifas de mensagens 13772, 16 nível, estado 1 <br> </br> não é possível criar o índice não em cluster numa tabela de histórico temporais 'WebsiteUserInfoHistory' porque tem período de retenção finito e o índice columnstore em cluster definido.*

## <a name="querying-tables-with-retention-policy"></a>Consultar tabelas com a política de retenção
Todas as consultas em tabela temporal filtragem automaticamente históricas linhas correspondentes à política de retenção finito, para evitar resultados imprevisíveis e inconsistentes, uma vez que a tarefa de limpeza podem eliminar linhas Desatualizadas *em qualquer ponto no tempo e pela ordem arbitrários*.

A imagem seguinte mostra o plano de consulta para uma consulta simples:

````
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
````

O plano de consulta inclui adicional filtro aplicado ao fim da coluna de período (ValidTo) no operador de análise de índice em cluster na tabela de histórico (realçado). Este exemplo assume que esse período de retenção de um mês foi definido na tabela de WebsiteUserInfo.

![Filtro de consulta de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se consultar tabelas de histórico diretamente, poderá ver as linhas com mais de retenção especificado período, mas sem qualquer garantia para os resultados da consulta repeatable. A imagem seguinte mostra o plano de execução de consulta para a consulta da tabela de histórico sem filtros adicionais aplicados:

![Consultar o histórico sem filtro de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Não recorra apenas a lógica de negócio ler a tabela de histórico para além do período de retenção, pode obter resultados inconsistentes ou inesperados. Recomendamos que utilize consultas temporais com a cláusula FOR SYSTEM_TIME para analisar dados em tabelas temporais.

## <a name="point-in-time-restore-considerations"></a>O ponto no considerações de restauro de tempo
Quando cria uma nova base de dados por [restauro da base de dados existente para um ponto específico no tempo](sql-database-recovery-using-backups.md), tem de retenção temporal desativada ao nível da base de dados. (**is_temporal_history_retention_enabled** sinalizador definido como OFF). Esta funcionalidade permite-lhe examine todas as linhas históricas após o restauro, sem ter de se preocupar que desatualizados linhas são removidas antes de obter ao consultá-los. Pode utilizá-lo para *inspecionar os dados históricos, para além do período de retenção configurado*.

Imaginemos que uma tabela temporal tem período especificada de retenção de um mês. Se a base de dados foi criada na camada de serviço Premium, seria possível criar a cópia da base de dados com o estado da base de dados cópias de segurança para 35 dias no passado. Que efetivamente seria permitem-lhe analisar históricas as linhas até 65 dias consultando diretamente a tabela de histórico.

Se pretender ativar a limpeza de retenção temporais, execute a seguinte instrução de Transact-SQL após o ponto no restauro de tempo:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

## <a name="next-steps"></a>Passos seguintes
Para saber como utilizar as tabelas temporais nas suas aplicações, veja [introdução tabelas temporais na SQL Database do Azure](sql-database-temporal-tables.md).

Visite Channel 9 ouvi um [história de sucesso de implementação temporal clientes reais](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e veja um [em direto demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para obter informações detalhadas sobre as tabelas temporais, reveja [documentação MSDN](https://msdn.microsoft.com/library/dn935015.aspx).

