---
title: "Código de memória intermédia de anel de XEvent para a base de dados SQL | Microsoft Docs"
description: "Fornece um exemplo de código de Transact-SQL que foi criado, fácil e rápido pela utilização de destino de memória intermédia de anel, na SQL Database do Azure."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 2510fb3f-c8f2-437a-8f49-9d5f6c96e75b
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: 61251eb9b125209ffd15adafdb0bace495e7cadd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Anel código de destino de memória intermédia de eventos expandidos na base de dados SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Pretende um exemplo de código completo para a forma mais fácil rápida informações de captura e o relatório para um evento expandido durante um teste. O destino mais fácil para os dados de evento expandido é o [destino de memória intermédia de anel](http://msdn.microsoft.com/library/ff878182.aspx).

Este tópico apresenta um exemplo de código de Transact-SQL que:

1. Cria uma tabela com dados para demonstrar com.
2. Cria uma sessão para um evento expandido existente, nomeadamente **sqlserver.sql_statement_starting**.
   
   * O evento está limitado a instruções SQL que contêm uma cadeia de atualização específica: **instrução LIKE '% ATUALIZAÇÃO tabEmployee %'**.
   * Optar por enviar o resultado do evento para um destino do tipo memória intermédia de anel, nomeadamente **package0.ring_buffer**.
3. Inicia a sessão do evento.
4. Problemas de duas instruções de ATUALIZAÇÃO SQL simples.
5. Problemas de uma instrução SQL SELECT para obter o resultado de eventos da memória intermédia de anel.
   
   * **sys.dm_xe_database_session_targets** e são associadas a outras vistas de gestão dinâmica (DMVs).
6. Interrompe a sessão do evento.
7. Ignora o destino de memória intermédia de anel, para libertar os respetivos recursos.
8. Ignora a sessão do evento e a tabela de demonstração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualquer base de dados que pode criar uma tabela.
  
  * Opcionalmente, pode [criar um **AdventureWorksLT** base de dados de demonstração](sql-database-get-started.md) em minutos.
* SQL Server Management Studio (ssms.exe), idealmente, a última versão atualização mensal. 
  Pode transferir o ssms.exe mais recente do:
  
  * Tópico intitulado [transferir SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [Uma ligação direta para a transferência.](http://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Exemplo de código

Com muito pequenas modificação, o seguinte exemplo de código de memória intermédia de anel pode ser executado na SQL Database do Azure ou Microsoft SQL Server. A diferença é a presença do nó '_database' nome algumas vistas de gestão dinâmica (DMVs), utilizados na cláusula FROM no passo 5. Por exemplo:

* sys.dm_xe**_database**_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;

## <a name="ring-buffer-contents"></a>Conteúdo de memória intermédia de anel

Utilizámos ssms.exe para executar o exemplo de código.

Para ver os resultados, iremos clica na célula sob o cabeçalho de coluna **target_data_XML**.

Em seguida, no painel de resultados clica na célula sob o cabeçalho de coluna **target_data_XML**. Isto, clique em criar noutro separador do ficheiro no ssms.exe na qual foi apresentado o conteúdo da célula resultado, como XML.

O resultado é apresentado no bloco de seguinte. Procura longo, mas é apenas dois  **<event>**  elementos.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Libertar recursos retidos, a memória intermédia de anel

Quando tiver terminado com a memória intermédia de anel, pode removê-lo e respetivos recursos de emissão da versão um **ALTER** com o seguinte:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


A definição da sua sessão do evento é atualizada, mas não foi removida. Mais tarde pode adicionar outra instância da memória intermédia de anel à sua sessão do evento:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Mais informações

O tópico principal expandida eventos na base de dados do Azure SQL é:

* [Expandido considerações de evento na base de dados do SQL Server](sql-database-xevent-db-diff-from-svr.md), que contrasta alguns aspetos dos eventos expandidos que diferem entre a base de dados do Azure SQL em comparação com o Microsoft SQL Server.

Outros tópicos de exemplo de código para expandida eventos estão disponíveis as seguintes ligações. No entanto, tem de verificar regularmente qualquer exemplo para ver se o exemplo está direcionada para Microsoft SQL Server em comparação com a SQL Database do Azure. Em seguida, pode decidir se a pequenas alterações são necessárias para executar o exemplo.

* Exemplo de código para a SQL Database do Azure: [código de destino de ficheiro de eventos para expandida eventos na base de dados SQL](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
