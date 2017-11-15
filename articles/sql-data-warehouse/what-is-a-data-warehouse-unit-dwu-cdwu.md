---
title: "Quais são as unidades do Data Warehouse (DWUs, cDWUs) no Azure SQL Data Warehouse? | Microsoft Docs"
description: "Desempenho ampliação capacidades no Azure SQL Data Warehouse. Aumentar horizontalmente ao ajustar as DWUs, cDWUs, ou colocar em pausa e retomar a recursos de computação para reduzir os custos."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 02998c48dcab5d3ed191b168665c9e47bbfbd232
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Unidades do Data Warehouse (DWUs) e computação unidades do Data Warehouse (cDWUs)
Explica as unidades do Data Warehouse (DWUs) e computação unidades do Data Warehouse (cDWUS) para o Azure SQL Data Warehouse. Inclua recomendações sobre como escolher o número ideal de unidades do data warehouse e como alterar o número. 

## <a name="what-are-data-warehouse-units"></a>Quais são as unidades do Data Warehouse?
Com o SQL Data Warehouse CPU, memória e e/s estão incluídas em unidades de escala de computação chamado unidades do Data Warehouse (DWUs). Uma DWU representa uma medida abstracta, normalizada de recursos de computação e o desempenho. Ao alterar o nível de serviço alterar o número de DWUs que estão atribuídas ao sistema, que por sua vez ajusta o desempenho e o custo, do seu sistema. 

Pagar para um desempenho superior, pode aumentar o número de unidades do data warehouse. Pagar para um desempenho inferior, reduza unidades do data warehouse. Os custos de armazenamento e computação são faturados separadamente, para que unidades de armazém de dados de alteração não afetam os custos de armazenamento.

Desempenho de unidades do data warehouse baseia-se nestas métricas de carga de trabalho do armazém de dados:

- A rapidez pode uma padrão a dados warehousing consulta analisar um grande número de linhas e de, em seguida, efetuar uma agregação complexa? Esta operação é intensivos em termos de e/s e CPU.
- A rapidez pode no armazém de dados ingestão de dados de Blobs de armazenamento do Azure ou do Azure Data Lake? Esta operação é intensivo da CPU e de rede. 
- A rapidez pode a [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) comando T-SQL copiar uma tabela? Esta operação envolve a leitura de dados do armazenamento, a distribuição dos mesmos por nós do dispositivo de e a escrita para o armazenamento novo. Esta operação é CPU, e/s e que consomem muita rede.

Aumentar as DWUs:
- Alterações de forma linear desempenho do sistema para análises, agregações e CTAS instruções
- Aumenta o número de leitores e escritores para operações de carga do PolyBase
- Aumenta o número máximo de consultas em simultâneo e de ranhuras de concorrência.

## <a name="performance-tiers-and-data-warehouse-units"></a>Os escalões de desempenho e unidades do Data Warehouse

Cada escalão de desempenho utiliza uma unidade de medida ligeiramente diferente para as unidades do data warehouse. Esta diferença é refletida na fatura como a unidade de escala traduz-se diretamente a faturação.

- O otimizado para a camada de desempenho de elasticidade é medida em unidades do Data Warehouse (DWUs).
- O otimizado para a camada de desempenho de computação é medida em computação unidades do Data Warehouse (cDWUs). 

As DWUs e cDWUs suporta computação aumentar ou reduzir verticalmente e colocar em pausa computação quando não precisa de utilizar o armazém de dados. Estas operações são todas as-a pedido. O otimizado para computação camada de desempenho também utiliza uma cache com base em disco local em nós de computação para melhorar o desempenho. Quando dimensionar ou colocar em pausa o sistema, a cache é invalidada e, por isso, durante um período de cache warming é necessário antes de um desempenho ideal é alcançado.  

Como aumentar unidades do data warehouse, forma linear estão aumentar recursos informáticos. O otimizado para computação camada de desempenho fornece o melhor desempenho das consultas e dimensionamento mais elevado, mas tem um preço de entrada superior. Foi concebido para as empresas que têm um pedido de constante de desempenho. Estes sistemas de efetuar a maioria dos utilização da cache. 

### <a name="capacity-limits"></a>Limites de capacidade
Cada SQL server (por exemplo, myserver.database.windows.net) tem um [unidade de transação de base de dados (DTU)](../sql-database/sql-database-what-is-a-dtu.md) quota que permite que um número específico de unidades do data warehouse. Para obter mais informações, consulte o [os limites de capacidade de gestão de carga de trabalho](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Como muitas unidades do data warehouse que preciso?
O número ideal de unidades do data warehouse depende muito a carga de trabalho e a quantidade de dados que carregou no sistema.

Passos para localizar o DWU melhor para a carga de trabalho:

1. Durante o desenvolvimento, comece por selecionar uma DWU mais pequeno, utilizando o otimizado para a camada de desempenho de elasticidade.  Uma vez que a preocupação nesta fase validação funcional, Optimized para a camada de desempenho de elasticidade é uma opção razoável. Um ponto de partida é DW200. 
2. Monitorize o desempenho de aplicações como testar as cargas de dados no sistema, observar o número de DWUs selecionado em comparação comparado o desempenho que observar.
3. Identifique quaisquer requisitos adicionais para periódicos períodos de atividade de pico. Se a carga de trabalho mostra picos significativos e troughs na atividade e existir um bom motivo para dimensionar com frequência, em seguida, favor o otimizado para a camada de desempenho de elasticidade.
4. Se precisar de mais de 1000 DWU, favor, em seguida, Optimized para a camada de desempenho de computação, uma vez que isto dá-o melhor desempenho.

O SQL Data Warehouse é um sistema de escalamento horizontal que pode aprovisionar grandes quantidades de quantidades que a computação e de consulta de dados. Para ver as suas verdadeiras capacidades de dimensionamento, especialmente em DWUs maiores, recomendamos que o conjunto de dados de dimensionamento, à medida que para se certificar de que tem dados suficientes para as CPUs do feed. Para testar a escala, recomendamos que utilize, pelo menos, 1 TB.

> [!NOTE]
>
> Desempenho de consulta apenas aumenta com mais parallelization se o trabalho pode ser dividido entre nós de computação. Se achar que dimensionamento é não alterar o desempenho, poderá ter de otimizar a estrutura da tabela e/ou as suas consultas. Para obter orientações sobre a otimização de consulta, consulte o seguinte [desempenho](sql-data-warehouse-overview-manage-user-queries.md) artigos. 

## <a name="permissions"></a>Permissões

Alterar as unidades de armazém de dados requer que as permissões descritas [ALTER DATABASE][ALTER DATABASE]. 

## <a name="view-current-dwu-settings"></a>Ver definições de DWU atuais

Para ver a definição de DWU atual:

1. Abra o SQL Server Object Explorer no Visual Studio.
2. Ligar à base de dados mestra associado o servidor lógico de base de dados SQL.
3. Selecione a vista de gestão dinâmica sys.database_service_objectives. Segue-se um exemplo: 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Unidades de armazém de dados de alteração

### <a name="azure-portal"></a>Portal do Azure
Para alterar as DWUs ou cDWUs:

1. Abra o [portal do Azure](https://portal.azure.com), abra a sua base de dados e clique em **escala**.

2. Em **escala**, mova o controlo de deslize à esquerda ou o botão direito do rato para alterar a definição de DWU.

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **Sim** para confirmar ou **não** para cancelar.

### <a name="powershell"></a>PowerShell
Para alterar as DWUs ou cDWUs, utilize o cmdlet do PowerShell [Set-AzureRmSqlDatabase] [Set-AzureRmSqlDatabase]. O exemplo a seguir define o objetivo de nível de serviço para DW1000 MySQLDW que está alojado no servidor MyServer da base de dados.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
Com o T-SQL pode ver as definições atuais de DWU ou cDWU, altere as definições e verificar o progresso. 

Para alterar as DWUs ou cDWUs:

1. Ligar à base de dados mestra associado o servidor lógico de base de dados SQL.
2. Utilize o [ALTER DATABASE] [ ALTER DATABASE] instrução TSQL. O exemplo a seguir define o objetivo de nível de serviço para DW1000 MySQLDW da base de dados. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>APIs REST

Para alterar as DWUs, utilize [criação ou atualização de base de dados] [criação ou atualização de base de dados] API REST. O exemplo a seguir define o objetivo de nível de serviço para DW1000 MySQLDW que está alojado no servidor MyServer da base de dados. O servidor está num grupo de recursos do Azure com o nome ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


## <a name="check-status-of-dwu-changes"></a>Verifique o estado das alterações DWU

Alterações DWU podem demorar vários minutos a concluir. Se estiver a dimensionar automaticamente, pondere implementar lógica para garantir que determinadas operações forma concluídas antes de continuar com a ação de outra. 

A verificar o estado da base de dados através de vários pontos finais permite-lhe implementar corretamente automatização. O portal fornece notificações após a conclusão de uma operação e o estado atual de bases de dados, mas não permite a programático a verificação do Estado. 

Não é possível verificar o estado da base de dados de operações de escalamento horizontal com o portal do Azure.

Para verificar o estado de DWU alterações:

1. Ligar à base de dados mestra associado o servidor lógico de base de dados SQL.
2. Submeta a consulta seguinte para verificar o estado da base de dados.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Submeter a consulta seguinte para verificar o estado da operação

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Este DMV devolve informações sobre a gestão de várias operações no SQL Data Warehouse, tais como a operação e o estado da operação, o que está ser IN_PROGRESS ou foi concluída.

## <a name="the-scaling-workflow"></a>O fluxo de trabalho de dimensionamento

Quando iniciar uma operação de dimensionamento, o sistema inutilizam primeiro abrir todas as sessões, a reverter as transações abertas para Certifique-se num estado consistente. As operações de dimensionamento, dimensionamento só ocorre uma vez concluída esta reversão transacional.  

- Para uma operação de dimensionamento, Aprovisiona o sistema adicionais de computação e, em seguida, reattaches para a camada de armazenamento. 
- Para uma operação de dimensionamento inferior, o desnecessária nós desligue do armazenamento e reattach para os nós restantes.

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes para ajudar a compreender alguns conceitos chave de desempenho adicionais:

* [Gestão de carga de trabalho e simultaneidade][Workload and concurrency management]
* [Descrição geral da estrutura da tabela][Table design overview]
* [Distribuição de tabela][Table distribution]
* [A indexação de tabela][Table indexing]
* [A criação de partições de tabela][Table partitioning]
* [Estatísticas de tabela][Table statistics]
* [Melhores práticas][Best practices]

<!--Image reference-->

<!--Article references-->

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
