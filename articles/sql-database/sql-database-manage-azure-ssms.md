---
title: Gerir uma Base de Dados SQL com o SSMS | Microsoft Docs
description: Aprenda a utilizar o SQL Server Management Studio para gerir as bases de dados e os servidores da Base de Dados SQL.
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: d9ff74a49742fa77f5989b8b05e0567e3ca81dc5
ms.openlocfilehash: 89cb8827745b31b3a77b64d5cafd586957d60d30


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gerir a Base de Dados SQL do Azure utilizando o SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Pode utilizar o SQL Server Management Studio (SSMS) para administrar as bases de dados e os servidores da Base de Dados SQL do Azure. Este tópico orienta-o ao longo de tarefas comuns com o SSMS. Antes de começar, já deverá ter um servidor e uma base de dados criados na Base de Dados SQL do Azure. Veja [Criar a sua primeira Base de Dados SQL do Azure](sql-database-get-started.md) e [Ligar e Consultar com o SSMS](sql-database-connect-query-ssms.md) para obter mais informações.

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar uma firewall baseada no servidor, ver propriedades do servidor, ligar com o SQL Server Management Studio, consultar a base de dados mestra, criar uma base de dados de exemplo e uma base de dados em branco, consultar as propriedades da base de dados, ligar com o SQL Server Management Studio e consultar a base de dados de exemplo, veja [Tutorial de Introdução](sql-database-get-started.md).
>

Recomenda-se utilizar a versão mais recente do SSMS sempre que trabalhar com a Base de Dados SQL do Azure. 

> [!IMPORTANT]
> Utilize sempre a versão mais recente do SSMS, porque este é continuamente melhorado para funcionar com as últimas atualizações do Azure e da Base de Dados SQL. Para obter a versão mais recente, veja [Download SQL Server Management Studio (Transferir o SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Criar e gerir bases de dados SQL do Azure
Enquanto estiver ligado à base de dados **mestra**, pode criar bases de dados no servidor e modificar ou remover bases de dados existentes. Os passos seguintes descrevem como efetuar várias tarefas de gestão da base de dados comuns com o Management Studio. Para executar estas tarefas, certifique-se de que está ligado à base de dados **mestra** com o início de sessão principal ao nível do servidor que criou quando configurou o seu servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta Bases de dados, expanda a pasta **Bases de Dados do Sistema**, clique com o botão direito do rato em **mestra**e, em seguida, clique em **Nova Consulta**.

* Utilize a instrução **CREATE DATABASE** para criar uma base de dados. Para obter mais informações, veja [CREATE DATABASE (SQL Database) (CRIAR BASE DE DADOS [Base de Dados SQL])](https://msdn.microsoft.com/library/dn268335.aspx). A instrução seguinte cria uma base de dados denominada **myTestDB** e especifica que se trata de uma base de dados Standard S0 Edition com um tamanho máximo predefinido de 250 GB.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Clique em **Executar** para executar a consulta.

* Utilize a instrução **ALTER DATABASE** para modificar uma base de dados existente se, por exemplo, quiser alterar o nome e a edição da mesma. Para obter mais informações, veja [ALTER DATABASE (SQL Database) (ALTERAR BASE DE DADOS [Base de Dados SQL])](https://msdn.microsoft.com/library/ms174269.aspx). A instrução seguinte modifica a base de dados que criou no passo anterior para mudar a edição para Standard S1.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Utilize a instrução **DROP DATABASE** para eliminar uma base de dados existente. Para obter mais informações, veja [DROP DATABASE (SQL Database) (REMOVER BASE DE DADOS [Base de Dados SQL])](https://msdn.microsoft.com/library/ms178613.aspx). A seguinte instrução elimina a base de dados **myTestDB**, mas não a remova agora porque irá utilizá-la para criar inícios de sessão no próximo passo.
  
      DROP DATABASE myTestBase;
* A base de dados mestra tem a vista **sys.databases**, que pode utilizar para ver detalhes de todas as bases de dados. Para ver todas as bases de dados existentes, execute a seguinte instrução:
  
      SELECT * FROM sys.databases;
* Na Base de Dados SQL, a instrução **USE** não é suportada para alternar entre bases de dados. Em vez disso, tem de estabelecer uma ligação diretamente para a base de dados de destino.

> [!NOTE]
> Muitas das instruções de Transact-SQL que criam ou modificam uma base de dados têm de ser executadas nos seus próprios lotes e não podem ser agrupadas com outras instruções de Transact-SQL. Para obter mais informações, veja as informações específicas de cada instrução.
> 
> 

## <a name="create-and-manage-logins"></a>Criar e gerir inícios de sessão
A base de dados **mestra** contém inícios de sessão que têm permissão para criar bases de dados ou outros inícios de sessão. Para gerir os inícios de sessão, ligue à base de dados **mestra** com o início de sessão principal ao nível do servidor que criou quando configurou o seu servidor. Pode utilizar as instruções **CREATE LOGIN**, **ALTER LOGIN** ou **DROP LOGIN** para executar consultas na base de dados principal que gere os inícios de sessão em todo o servidor. Para obter mais informações, veja [Managing Databases and Logins in SQL Database (Gerir Bases de Dados e Inícios de Sessão na Base de Dados SQL)](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* Utilize a instrução **CREATE LOGIN** para criar um início de sessão ao nível do servidor. Para obter mais informações, veja [CREATE LOGIN (SQL Database) (CREATE LOGIN [Base de Dados SQL])](https://msdn.microsoft.com/library/ms189751.aspx). A instrução seguinte cria um início de sessão denominado **login1**. Substitua **password1** pela palavra-passe da sua preferência.
  
      CREATE LOGIN login1 WITH password='password1';
* Utilize a instrução **CREATE USER** para conceder permissões ao nível da base de dados. Todos os inícios de sessão têm de ser criados na base de dados **mestra**. Para que um início de sessão ligue a outra base de dados, tem de conceder permissões ao nível da base de dados com a instrução **CREATE USER** dessa base de dados. Para obter mais informações, veja [CREATE USER (SQL Database) (CRIAR UTILIZADOR [Base de Dados SQL])](https://msdn.microsoft.com/library/ms173463.aspx). 
* Para dar a login1 permissões para uma base de dados denominada **myTestDB**, execute os seguintes passos:
  
  1. Para atualizar o Object Explorer para ver a base de dados **myTestDB** que criou, clique com o botão direito do rato no nome do servidor no Object Explorer e, em seguida, clique em **Atualizar**.  
     
     Se fechou a ligação, pode restabelecê-la ao selecionar **Ligar Object Explorer**, no menu Ficheiro.
  2. Clique com o botão direito do rato em **myTestDB**, na base de dados, e selecione **Nova Consulta**.
  3. Execute a seguinte instrução na base de dados myTestDB para criar um utilizador de base de dados com o nome **login1User**, que corresponde ao início de sessão ao nível do servidor **login1**.
     
         CREATE USER login1User FROM LOGIN login1;
* Utilize o procedimento armazenado **sp\_addrolemember** para conceder à conta de utilizador o nível de permissões adequado na base de dados. Para obter mais informações, veja [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). A instrução seguinte dá permissões só de leitura a **login1User** à base de dados, ao adicionar**login1User** à função **db\_datareader**.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Utilize a instrução **ALTER LOGIN** para modificar um início de sessão existente se, por exemplo, pretender alterar a palavra-passe desse início de sessão. Para obter mais informações, veja [ALTER LOGIN (SQL Database) (ALTERAR INÍCIO DE SESSÃO [Base de Dados SQL])](https://msdn.microsoft.com/library/ms189828.aspx). A instrução **ALTER LOGIN** deve ser executada na base de dados **mestra**. Mude novamente para a janela de consulta que está ligada a essa base de dados. A instrução seguinte modifica o início de sessão **login1** para repor a palavra-passe. Substitua **newPassword** pela palavra-passe à sua escolha e **oldPassword** pela palavra-passe atual do início de sessão.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Utilize a instrução **DROP LOGIN** para eliminar um início de sessão existente. Eliminar um início de sessão ao nível do servidor também elimina as contas de utilizador da base de dados associadas. Para obter mais informações, veja [DROP DATABASE (SQL Database) (REMOVER BASE DE DADOS [Base de Dados SQL])](https://msdn.microsoft.com/library/ms178613.aspx). A instrução **DROP LOGIN** deve ser executada na base de dados **mestra**. A instrução elimina o início de sessão **login1**.
  
      DROP LOGIN login1;
* A base de dados mestra tem a vista **sys.sql\_inícios de sessão**, que pode utilizar para ver os inícios de sessão. Para ver todos os início de sessão existentes, execute a instrução seguinte:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Monitorizar a Base de Dados SQL com as Vistas de Gestão Dinâmica
A Base de Dados SQL suporta várias vistas de gestão dinâmica que pode utilizar para monitorizar bases de dados individuais. Abaixo, pode ver alguns exemplos dos tipos de dados de monitor que pode obter através destas vistas. Para obter detalhes completos e mais exemplos de utilização, veja [Monitoring SQL Database using Dynamic Management Views (Monitorizar a Base de Dados SQL com Vistas de Gestão Dinâmica)](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* Para consultar uma vista de gestão dinâmica, precisa das permissões **VER ESTADO DA BASE DE DADOS**. Para conceder a permissão **VER ESTADO DA BASE DE DADOS** a um utilizador de base de dados específico, ligue à base de dados e execute a seguinte instrução na mesma:
  
      GRANT VIEW DATABASE STATE TO login1User;
* Calcule o tamanho da base de dados com a vista **sys.dm\_db\_partition\_stats**. A vista **sys.dm\_db\_partition\_stats** devolve as informações de página e de contagem de linhas de cada partição na base de dados, que pode utilizar para calcular o tamanho da base de dados. A consulta seguinte devolve o tamanho da base de dados em megabytes:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* Utilize as vistas **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions** para obter informações sobre ligações de utilizador atuais e tarefas internas associadas à base de dados. A consulta seguinte devolve informações sobre a ligação atual:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* Utilize a vista **sys.dm\_exec\_query\_stats** para obter as estatísticas de desempenho agregado relativas a planos de consulta em cache. A consulta seguinte devolve informações sobre as cinco principais consultas classificadas por tempo médio de CPU.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Dec16_HO3-->


