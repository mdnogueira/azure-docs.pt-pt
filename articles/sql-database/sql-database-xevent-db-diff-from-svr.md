---
title: Expandido eventos na base de dados SQL | Microsoft Docs
description: "Descreve eventos expandidos (XEvents) na SQL Database do Azure e como sessões de evento diferirem ligeiramente do sessões de evento no Microsoft SQL Server."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 3b28cf15-f820-4b3c-8310-908d6d5b9d0c
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: f4e41d340b38a5f29387d75b8f65b68c5fb31eb9
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="extended-events-in-sql-database"></a>Eventos expandidos na base de dados SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Este tópico explica a forma como a implementação de eventos expandidos na SQL Database do Azure é ligeiramente diferente em comparação com eventos expandidos no Microsoft SQL Server.

- SQL Database V12 adquiridos a funcionalidade de eventos expandidos na segunda metade do calendário 2015.
- SQL Server tenha tido eventos expandidos desde 2008.
- O conjunto de funcionalidades de eventos expandidos na base de dados do SQL Server é um subconjunto robusto de funcionalidades no SQL Server.

*XEvents* é uma alcunha informal que por vezes, é utilizada para 'eventos expandidos' em blogues e outras localizações informal.

Informações adicionais sobre eventos expandidas, para a SQL Database do Azure e o Microsoft SQL Server, estão disponíveis em:

- [Início rápido: Eventos expandidos no SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Eventos expandidos](http://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Pré-requisitos

Este tópico assume que já tiver algum conhecimento de:

- [Serviço de base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/).
- [Expandido eventos](http://msdn.microsoft.com/library/bb630282.aspx) no Microsoft SQL Server.

- O volume da nossa documentação sobre eventos expandidas aplica-se ao SQL Server e base de dados SQL.

Exposição anterior para os seguintes itens é útil quando escolher o ficheiro de eventos como o [destino](#AzureXEventsTargets):

- [Serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Com o Azure PowerShell com o Storage do Azure](../storage/common/storage-powershell-guide-full.md) -fornece informações abrangentes sobre o PowerShell e o serviço de armazenamento do Azure.

## <a name="code-samples"></a>Exemplos de código

Tópicos relacionados fornecem dois exemplos de código:


- [Anel código de destino de memória intermédia de eventos expandidos na base de dados SQL](sql-database-xevent-code-ring-buffer.md)
    - Script de Transact-SQL simple curto.
    - Iremos destacar o tópico de exemplo de código que, quando tiver terminado com um destino de memória intermédia de anel, deve versão dos respetivos recursos, executando um largar alter `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instrução. Mais tarde, pode adicionar outra instância da memória intermédia de anel por `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Código de destino de ficheiro de evento expandido eventos na base de dados do SQL Server](sql-database-xevent-code-event-file.md)
    - Fase 1 é o PowerShell para criar um contentor de armazenamento do Azure.
    - Fase 2 é Transact-SQL que utiliza o contentor de armazenamento do Azure.

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL


- Quando executar o [eventos criar sessão](http://msdn.microsoft.com/library/bb677289.aspx) comando do SQL Server utiliza o **no servidor** cláusula. Mas na base de dados do SQL Server utiliza o **na base de dados** cláusula em vez disso.


- O **na base de dados** cláusula também se aplica ao [eventos de falha de ALTER sessão](http://msdn.microsoft.com/library/bb630368.aspx) e [remover eventos da sessão](http://msdn.microsoft.com/library/bb630257.aspx) comandos Transact-SQL.


- É uma melhor prática para incluir a opção de sessão do evento de **STARTUP_STATE = ON** no seu **eventos criar sessão** ou **eventos de falha de ALTER sessão** instruções.
    - O **= ON** valor suporta um reinício automático após uma reconfiguração da base de dados lógico devido a uma ativação pós-falha.

## <a name="new-catalog-views"></a>Novas vistas de catálogo

A funcionalidade de eventos expandidos é suportada por várias [vistas de catálogo](http://msdn.microsoft.com/library/ms174365.aspx). Vistas de catálogo conte *metadados ou com definições* de sessões de evento criados pelo utilizador na base de dados atual. As vistas devolve informações sobre as instâncias de sessões de evento Active Directory.

| Nome de<br/>Vista de catálogo | Descrição |
|:--- |:--- |
| **sys.database_event_session_actions** |Devolve uma linha para cada ação em cada evento de uma sessão do evento. |
| **sys.database_event_session_events** |Devolve uma linha para cada evento na sessão do evento. |
| **sys.database_event_session_fields** |Devolve uma linha para cada personalizar coluna que foi explicitamente definida em eventos e destinos. |
| **sys.database_event_session_targets** |Devolve uma linha para cada destino de eventos de uma sessão do evento. |
| **sys.database_event_sessions** |Devolve uma linha para cada sessão de evento na base de dados de base de dados SQL. |

No Microsoft SQL Server, vistas de catálogo semelhante têm nomes que incluem *.server\_*  em vez de *.database\_*. O padrão de nome é como **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Novas vistas de gestão dinâmica [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx)

Base de dados SQL do Azure tem [vistas de gestão dinâmica (DMVs)](http://msdn.microsoft.com/library/bb677293.aspx) que suportam eventos expandidos. DMVs conte *Active Directory* sessões de evento.

| Nome do DMV | Descrição |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Devolve informações sobre as ações de sessão do evento. |
| **sys.dm_xe_database_session_events** |Devolve informações sobre eventos da sessão. |
| **sys.dm_xe_database_session_object_columns** |Mostra os valores de configuração para objetos que estão vinculados a uma sessão. |
| **sys.dm_xe_database_session_targets** |Devolve informações sobre os destinos de sessão. |
| **xe_database_sessions** |Devolve uma linha para cada sessão de evento tem um âmbito de base de dados atual. |

No Microsoft SQL Server, as vistas de catálogo semelhantes são denominadas sem o  *\_base de dados* parte do nome, tal como:

- **sys.dm_xe_sessions**, em vez do nome<br/>**xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs comuns para ambos
Para eventos expandidos existem DMVs adicionais que são comuns a SQL Database do Azure e o Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Localizar disponíveis alargadas de eventos, ações e destinos

Pode executar um SQL simple **SELECIONE** para obter uma lista dos eventos disponíveis, ações e destino.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Destinos para as sessões de evento de base de dados SQL

Seguem-se destinos que podem capturar resultados a partir do seu sessões de evento na base de dados do SQL Server:

- [Destino de memória intermédia de anel](http://msdn.microsoft.com/library/ff878182.aspx) -brevemente contém dados de eventos na memória.
- [Destino de contador do evento](http://msdn.microsoft.com/library/ff878025.aspx) -contagens de todos os eventos que ocorrem durante uma sessão de eventos expandidos.
- [Destino do ficheiro de evento](http://msdn.microsoft.com/library/ff878115.aspx) -escreve memórias intermédias completas para um contentor de armazenamento do Azure.

O [rastreio de eventos para o Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) API não está disponível para eventos expandidos na base de dados do SQL Server.

## <a name="restrictions"></a>Restrições

Existem algumas diferenças relacionadas com segurança befitting o ambiente de nuvem de base de dados SQL:

- Eventos expandidos são founded no modelo de isolamento de inquilino único. Uma sessão do evento na base de dados de um não é possível aceder a dados ou eventos de outra base de dados.
- Não é possível emitir uma **eventos criar sessão** instrução no contexto do **mestre** base de dados.

## <a name="permission-model"></a>Modelo de permissão

Tem de ter **controlo** permissão na base de dados para emitir um **eventos criar sessão** instrução. O proprietário da base de dados (dbo) tem **controlo** permissão.

### <a name="storage-container-authorizations"></a>Autorizações de contentor de armazenamento

O token SAS gerar para o contentor de armazenamento do Azure tem de especificar **rwl** para conhecer as permissões. O **rwl** valor fornece as seguintes permissões:

- Leitura
- Escrita
- Lista

## <a name="performance-considerations"></a>Considerações de desempenho

Existem cenários onde utilização intensiva de eventos expandidos pode acumular ativa mais memória do que está em bom estado de sistema global. Por conseguinte o sistema de base de dados do Azure SQL dinamicamente define e ajusta limites a quantidade de memória de Active Directory que pode ser acumulada por uma sessão do evento. Vários fatores vá para o cálculo dinâmico.

Se receber uma mensagem de erro que indica que uma memória máxima tivesse sido imposta, algumas ações corretivas que pode tomar são:

- Execute menos sessões de evento em simultâneo.
- Através do seu **criar** e **ALTER** instruções para sessões de evento, reduzir a quantidade de memória que especificar no **máx.\_memória** cláusula.

### <a name="network-latency"></a>Latência de rede

O **ficheiro eventos** destino pode ocorrer a latência de rede ou falhas ao dados persistentes para blobs de armazenamento do Azure. Outros eventos na base de dados do SQL Server poderão ser atrasados enquanto que terão de aguardar a comunicação de rede concluir. Este atraso pode abrandar a sua carga de trabalho.

- Para mitigar este risco de desempenho, evite definir a **EVENT_RETENTION_MODE** opção para **NO_EVENT_LOSS** nas definições de sessão do evento.

## <a name="related-links"></a>Ligações relacionadas

- [Utilizar o Azure PowerShell com o Storage do Azure](../storage/common/storage-powershell-guide-full.md).
- [Cmdlets de armazenamento do Azure](http://msdn.microsoft.com/library/dn806401.aspx)
- [Com o Azure PowerShell com o Storage do Azure](../storage/common/storage-powershell-guide-full.md) -fornece informações abrangentes sobre o PowerShell e o serviço de armazenamento do Azure.
- [Como utilizar o Blob storage a partir do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CRIAR a sessão do evento (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)
- [Mensagens do blogue de Jonathan Kehayias sobre eventos expandidos no Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- O Azure *atualizações de serviço* página Web, narrowed pelo parâmetro a SQL Database do Azure:
    - [https://Azure.microsoft.com/Updates/?Service=SQL-Database](https://azure.microsoft.com/updates/?service=sql-database)


Outros tópicos de exemplo de código para expandida eventos estão disponíveis as seguintes ligações. No entanto, tem de verificar regularmente qualquer exemplo para ver se o exemplo está direcionada para Microsoft SQL Server em comparação com a SQL Database do Azure. Em seguida, pode decidir se a pequenas alterações são necessárias para executar o exemplo.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
