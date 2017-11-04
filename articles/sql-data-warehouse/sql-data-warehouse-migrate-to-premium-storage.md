---
title: "Migrar o seu armazém de dados do Azure existente para o premium storage | Microsoft Docs"
description: "Instruções para migrar um armazém de dados existente para o premium storage"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 860e50b532b4b0a21d3be54f087730070b0e56bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Migrar o seu armazém de dados para o premium storage
O Azure SQL Data Warehouse recentemente adicionadas [armazenamento premium para previsão de desempenho superior][premium storage for greater performance predictability]. Os armazéns de dados existentes atualmente no armazenamento standard agora podem ser migrados para o premium storage. Pode tirar partido da migração automática, ou se preferir controlar quando migrar (que does envolve algum período de indisponibilidade), pode efetuar a migração por si.

Se tiver mais do que um armazém de dados, utilize o [agenda migração automática] [ automatic migration schedule] para determinar quando será também ser migrada.

## <a name="determine-storage-type"></a>Determinar o tipo de armazenamento
Se tiver criado um armazém de dados antes das datas seguintes, está atualmente a utilizar armazenamento standard.

| **Região** | **Armazém de dados criado antes desta data** |
|:--- |:--- |
| Leste da Austrália |Armazenamento Premium ainda não está disponível |
| Leste da China |1 de Novembro de 2016 |
| China Norte |1 de Novembro de 2016 |
| Alemanha Central |1 de Novembro de 2016 |
| Alemanha Nordeste |1 de Novembro de 2016 |
| Índia Ocidental |Armazenamento Premium ainda não está disponível |
| Oeste do Japão |Armazenamento Premium ainda não está disponível |
| EUA Centro-Norte |10 de Novembro de 2016 |

## <a name="automatic-migration-details"></a>Detalhes de migração automática
Por predefinição, iremos migrar a base de dados para si entre as 18:00:00 e 6:00:00 na sua região local durante a [agenda migração automática][automatic migration schedule]. O armazém de dados existente ficará inutilizável durante a migração. A migração irá demorar cerca de uma hora por com vários terabytes de armazenamento do armazém de dados. Não será cobrada durante qualquer parte da migração automática.

> [!NOTE]
> Quando a migração estiver concluída, o armazém de dados será novamente online e utilizável.
>
>

Microsoft está a demorar os seguintes passos para concluir a migração (estes não requerem qualquer envolvimento da sua parte). Neste exemplo, imagine que o armazém de dados existente no armazenamento standard atualmente com o nome "MyDW."

1. Microsoft muda o nome "MyDW" para "MyDW_DO_NOT_USE_ [Timestamp]".
2. Microsoft interrompe "MyDW_DO_NOT_USE_ [Timestamp]". Durante este período, foi efetuada uma cópia de segurança. Poderá ver várias coloca em pausa e retoma se podemos encontre quaisquer problemas durante este processo.
3. Microsoft cria um novo armazém de dados com o nome "MyDW" no armazenamento premium da cópia de segurança efetuada no passo 2. "MyDW" não será apresentado até após o restauro foi concluído.
4. Após o restauro está concluído, "MyDW" devolve para o mesmo Estado e unidades do data warehouse (em pausa ou Active Directory) que foi antes da migração.
5. Após a migração estiver concluída, a Microsoft elimina "MyDW_DO_NOT_USE_ [Timestamp]".

> [!NOTE]
> As seguintes definições não passa como parte da migração:
>
> * Auditoria ao nível da base de dados tem de ser ativada novamente.
> * Regras de firewall ao nível da base de dados tem de ser adicionado novamente. Regras de firewall ao nível do servidor não são afetadas.
>
>

### <a name="automatic-migration-schedule"></a>Agenda de migração automática
Migrações automáticas ocorrerem entre as 18:00:00 e 6:00:00 (hora local por região) durante a seguinte agenda de indisponibilidade.

| **Região** | **Data de início estimado** | **Data de fim estimado** |
|:--- |:--- |:--- |
| Leste da Austrália |Não determinar ainda |Não determinar ainda |
| Leste da China |9 de Janeiro de 2017 |13 de Janeiro de 2017 |
| China Norte |9 de Janeiro de 2017 |13 de Janeiro de 2017 |
| Alemanha Central |9 de Janeiro de 2017 |13 de Janeiro de 2017 |
| Alemanha Nordeste |9 de Janeiro de 2017 |13 de Janeiro de 2017 |
| Índia Ocidental |Não determinar ainda |Não determinar ainda |
| Oeste do Japão |Não determinar ainda |Não determinar ainda |
| EUA Centro-Norte |9 de Janeiro de 2017 |13 de Janeiro de 2017 |

## <a name="self-migration-to-premium-storage"></a>Migração automática para o premium storage
Se quiser controlar quando o período de indisponibilidade irá ocorrer, pode utilizar os seguintes passos para migrar um armazém de dados existente no armazenamento standard para armazenamento premium. Se escolher esta opção, tem de concluir a migração automática antes de iniciar a migração automática nessa região. Isto garante que evitar quaisquer risco da migração automática, provocando um conflito (consulte o [agenda migração automática][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Instruções de migração automática
Para migrar o armazém de dados por si, utilize a cópia de segurança e restaurar as funcionalidades. A parte do restauro da migração deverá demorar cerca de uma hora por com vários terabytes de armazenamento do armazém de dados. Se pretender manter o mesmo nome após a migração estiver concluída, siga o [passos para mudar o nome durante a migração][steps to rename during migration].

1. [Colocar em pausa] [ Pause] o armazém de dados. Isto leva uma cópia de segurança automática.
2. [Restaurar] [ Restore] do seu instantâneo mais recente.
3. Elimine o armazém de dados existente no armazenamento standard. **Se falhar efetuar este passo, será cobrada para ambos os armazéns de dados.**

> [!NOTE]
> As seguintes definições não passa como parte da migração:
>
> * Auditoria ao nível da base de dados tem de ser ativada novamente.
> * Regras de firewall ao nível da base de dados tem de ser adicionado novamente. Regras de firewall ao nível do servidor não são afetadas.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Mudar o nome do armazém de dados durante a migração (opcional)
Duas bases de dados no mesmo servidor lógico não podem ter o mesmo nome. Armazém de dados do SQL Server suporta agora a capacidade de mudar o nome de um armazém de dados.

Neste exemplo, imagine que o armazém de dados existente no armazenamento standard atualmente com o nome "MyDW."

1. Mudar o nome "MyDW" utilizando o seguinte comando ALTER DATABASE. (Neste exemplo, iremos irá mude o nome "MyDW_BeforeMigration.")  Este comando para todas as transações existentes e tem de ser efetuado na base de dados mestra seja bem sucedida.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Colocar em pausa] [ Pause] "MyDW_BeforeMigration." Isto leva uma cópia de segurança automática.
3. [Restaurar] [ Restore] o mais recente do instantâneo de uma nova base de dados com o nome é utilizado para ser (por exemplo, "MyDW").
4. Eliminar "MyDW_BeforeMigration." **Se falhar efetuar este passo, será cobrada para ambos os armazéns de dados.**


## <a name="next-steps"></a>Passos seguintes
Com a alteração para o premium storage, também tem um aumento do número de ficheiros de blob de base de dados numa arquitetura subjacente do seu armazém de dados. Para maximizar as vantagens de desempenho desta alteração, reconstrua os índices columnstore em cluster utilizando o seguinte script. O script funciona forçando alguns dos seus dados existentes para os blobs adicionais. Se não efetuar qualquer ação, os dados serão naturalmente redistribuir ao longo do tempo, como carregados mais dados para as tabelas.

**Pré-requisitos:**

- O armazém de dados deve ser executada com unidades de armazém de 1000 dados ou superior (consulte [poder de computação de escala][scale compute power]).
- O utilizador executar o script deve ter o [mediumrc função] [ mediumrc role] ou superior. Para adicionar um utilizador a esta função, execute o seguinte:````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create table to control index rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute index rebuilds. If script fails, the below can be re-run to restart where last left off.
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Clean up table created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Se tiver quaisquer problemas com o armazém de dados, [criar um pedido de suporte] [ create a support ticket] e referência "a migração para o armazenamento premium" como a causa.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
