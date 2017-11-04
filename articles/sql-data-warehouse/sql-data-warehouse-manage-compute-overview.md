---
title: "Gerir a capacidade de computação no Azure SQL Data Warehouse (descrição geral) | Microsoft Docs"
description: "Desempenho ampliação capacidades no Azure SQL Data Warehouse. Aumentar horizontalmente ao ajustar as DWUs ou colocar em pausa e retomar a recursos de computação para reduzir os custos."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 3/23/2017
ms.author: elbutter
ms.openlocfilehash: 0d0d3b94fb50155ce0579d32e8ff78a47b9e3589
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gerir a capacidade de computação no Azure SQL Data Warehouse (descrição geral)
> [!div class="op_single_selector"]
> * [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

A arquitetura do SQL Data Warehouse separa o armazenamento e computação independentes, permitindo cada dimensionar de forma independente. Como resultado, computação pode ser escalada para satisfazer os pedidos de desempenho independentes da quantidade de dados. Um consequence natural colateral desta arquitetura é que [faturação] [ billed] de computação e de armazenamento está separado. 

Esta descrição geral descreve como aumentar horizontalmente funciona com o SQL Data Warehouse e como utilizar o pausar, retomar e aumentar as capacidades do SQL Data Warehouse. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>Como funcionam operações de gestão do armazém de dados do SQL Server de computação
A arquitetura de armazém de dados SQL é composta por um nó de controlo, nós de computação e a camada de armazenamento distribuídos por 60 distribuições. 

Durante uma sessão ativa normal no SQL Data Warehouse, o nó principal do seu sistema gere os metadados e contém o otimizador de consultas distribuídas. Abaixo deste nó head são os nós de computação e a camada de armazenamento. Para um 400 DWU, o sistema tem um nó principal, quatro nós de computação e a camada de armazenamento, constituída por 60 distribuições. 

Quando são submetidos a uma escala ou interromper a operação, o sistema inutilizam primeiro todas as consultas de entrada e, em seguida, reverte as transações para Certifique-se num estado consistente. Para as operações de dimensionamento, dimensionamento só ocorrerá depois de concluída a este reversão transacional. Para uma operação de dimensionamento, Aprovisiona o sistema a extra pretendido número de nós de computação e, em seguida, começa reattaching os nós de computação para a camada de armazenamento. Para uma operação de dimensionamento inferior, são lançados desnecessários nós e os restantes nós de computação reattach próprios para o número apropriado de distribuições. Para uma operação de colocação em pausa, computação todos os nós são lançados e o sistema vai sofrer uma variedade de operações de metadados para deixar o seu sistema final num estado estável.

| DWU  | \#de nós de computação | \#de distribuições por nó |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

Três funções principais para a gestão de computação são:

1. Colocar em pausa
2. Retomar
3. Escala

Cada uma destas operações poderá demorar vários minutos a concluir. Se estiver dimensionamento/colocar em pausa/retomar automaticamente, pode querer implementar a lógica para garantir que determinadas operações forma concluídas antes de continuar com a ação de outra. 

A verificar o estado da base de dados através de vários pontos finais irá permitir-lhe implementar corretamente a automatização de operações. O portal irá fornecer notificações após a conclusão de uma operação e bases de dados do estado atual, mas não permite para programático a verificação do Estado. 

>  [!NOTE]
>
>  A funcionalidade de gestão não existe em todos os pontos finais de computação.
>
>  

|              | Colocar em pausa/retomar | Escala | Verificar o estado da base de dados |
| ------------ | ------------ | ----- | -------------------- |
| Portal do Azure | Sim          | Sim   | **Não**               |
| PowerShell   | Sim          | Sim   | Sim                  |
| API REST     | Sim          | Sim   | Sim                  |
| T-SQL        | **Não**       | Sim   | Sim                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Dimensionar a computação

Desempenho no SQL Data Warehouse é medido em [unidades do data warehouse (DWUs)] [do armazém de dados unidades (DWUs)] que é uma medida abstracted de recursos de computação, tais como CPU, memória e e/s da largura de banda. Um utilizador que pretende dimensionar o desempenho do seu sistema pode fazer através de vários meios, tal como através do portal, T-SQL e REST APIs. 

### <a name="how-do-i-scale-compute"></a>Como dimensionar a computação?
Computação energia é gerida para que o SQL Data Warehouse alterando a definição de DWU. Desempenho forma linear aumenta à medida que adiciona mais DWU para determinadas operações.  Ofertas de DWU que certifique-se de que o seu desempenho será alterado intensas em termos quando dimensionar o seu sistema ou reduzir verticalmente que oferecemos. 

Para ajustar as DWUs, pode utilizar qualquer um destes métodos individuais.

* [Escala de computação energia com o portal do Azure][Scale compute power with Azure portal]
* [Escala de computação energia com o PowerShell][Scale compute power with PowerShell]
* [Capacidade de computação de escala com REST APIs][Scale compute power with REST APIs]
* [Escala de computação energia com TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>O número de DWUs devo utilizar?

Para compreender qual é o seu valor de DWU ideal, experimente aumentar e reduzir verticalmente o desempenho e executar algumas consultas depois de carregar os dados. Uma vez que o dimensionamento é rápido, pode tentar vários níveis de desempenho numa hora ou menos. 

> [!Note] 
> O SQL Data Warehouse foi concebido para processar grandes quantidades de dados. Para ver as suas verdadeiras capacidades de dimensionamento, especialmente em DWUs maiores, que pretende utilizar um conjunto de dados grande que atinja ou exceda 1 TB.

Recomendações para localizar o DWU melhor para a carga de trabalho:

1. Para um armazém de dados de desenvolvimento, comece por selecionar um nível de desempenho de DWU mais pequeno.  Um ponto de partida é DW400 ou DW200.
2. Monitorize o desempenho de aplicações, observar o número de DWUs selecionado em comparação comparado o desempenho que observar.
3. Determine a quantidade desempenho mais rápido ou mais lento deve ser por si alcançar o nível de desempenho ideais para os seus requisitos ao pressupondo escala linear.
4. Aumentar ou reduzir o número de DWUs in proportion to como muito mais rápido ou inferior pretende que a carga de trabalho para executar. 
5. Continue a efetuar ajustes até chegar um nível de desempenho ideais para os seus requisitos de negócio.

> [!NOTE]
>
> Desempenho de consulta apenas aumenta com mais parallelization se o trabalho pode ser dividido entre nós de computação. Se achar que dimensionamento é não alterar o desempenho, consulte os nosso artigos para verificar se os seus dados unevenly são distribuídos ou se está a introduzir uma grande quantidade de movimento de dados de otimização do desempenho. 

### <a name="when-should-i-scale-dwus"></a>Quando de escala DWUs?
Dimensionamento DWUs altera os seguintes cenários importantes:

1. Alterar o desempenho do sistema para análises, agregações e instruções de CTAS forma linear
2. Aumento do número de leitores e escritores ao carregar com o PolyBase
3. Número máximo de consultas em simultâneo e de ranhuras de concorrência

Recomendações para quando dimensionar as DWUs:

1. Antes de executar uma operação de transformação ou de carregamento de dados pesados, aumentar verticalmente as DWUs para que os seus dados estão disponíveis mais rapidamente.
2. Durante o horário de pico, dimensione para acomodar grandes quantidades de consultas em simultâneo. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Computação pausa
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para colocar em pausa uma base de dados, utilize qualquer um destes métodos individuais.

* [Colocar em pausa computação com o portal do Azure][Pause compute with Azure portal]
* [Colocar em pausa computação com o PowerShell][Pause compute with PowerShell]
* [Colocar em pausa computação com REST APIs][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Retomar de computação
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para retomar uma base de dados, utilize qualquer um destes métodos individuais.

* [Retomar de computação com o portal do Azure][Resume compute with Azure portal]
* [Retomar de computação com o PowerShell][Resume compute with PowerShell]
* [Retomar de computação com REST APIs][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>Verificar o estado da base de dados 

Para retomar uma base de dados, utilize qualquer um destes métodos individuais.

- [Verificar o estado da base de dados com o T-SQL][Check database state with T-SQL]
- [Verificar o estado da base de dados com o PowerShell][Check database state with PowerShell]
- [Verificar o estado da base de dados com REST APIs][Check database state with REST APIs]

## <a name="permissions"></a>Permissões

Dimensionar a base de dados requer que as permissões descritas [ALTER DATABASE][ALTER DATABASE].  Colocar em pausa e retomar requerem o [contribuinte da BD SQL] [ SQL DB Contributor] permissão, especificamente Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

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
[billed]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

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
