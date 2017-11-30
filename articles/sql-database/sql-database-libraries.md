---
title: "Bibliotecas de ligação para base de dados SQL | Microsoft Docs"
description: "Fornece ligações para as transferências de módulos que permitem a ligação ao SQL Server e base de dados SQL a partir de uma grande variedade de linguagens de programação do cliente."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: 44c4491e7c198eec43a31480ef3df748626b7b75
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Bibliotecas de conectividade e estruturas para Microsoft SQL Server

Consulte a nossa [obter tutoriais iniciado](http://aka.ms/sqldev) introdução à programação idiomas, como c#, Java, Node.js, PHP e Python e criar uma aplicação utilizando o SQL Server no Linux ou Windows ou Docker no macOS rapidamente.

A tabela abaixo lista as bibliotecas de conetividade ou *controladores* que aplicações cliente podem utilizar a partir de uma variedade de idiomas para ligar a e utilizar o Microsoft SQL Server em execução no local ou na nuvem, no Linux, Windows ou Docker e também a SQL Database do Azure e Azure SQL Data Warehouse. 

| Idioma | Plataforma | Recursos adicionais | Transferência | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para o SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Transferência](https://www.microsoft.com/net/download/) | [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Controlador Microsoft JDBC para SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Transferência](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Controlador do SQL do PHP para SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Sistema operativo: <br/> \*[Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \*[Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \*[macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Controlador de node.js para o SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Instalar](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Controlador do SQL de Python](http://msdn.microsoft.com/library/mt652092.aspx) | Instale opções: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby controlador para o SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Instalar](https://msdn.microsoft.com/library/mt711041.aspx) | [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC Driver para SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [Transferência](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

A tabela abaixo lista alguns exemplos de estruturas de mapeamento relacional objeto (ORM) e estruturas web que aplicações cliente podem utilizar com o Microsoft SQL Server em execução no local ou na nuvem, no Linux, Windows ou Docker e também a SQL Database do Azure e Azure SQL Data Warehouse. 

| Idioma | Plataforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entidade Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernar ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby no Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Ligações relacionadas
- [SQL Server controladores](http://msdn.microsoft.com/library/mt654049.aspx) para ligar a partir de aplicações de cliente
- Ligar à base de dados do SQL Server:
    - [Ligar à Base de Dados SQL com o .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Ligar à Base de Dados SQL com PHP](sql-database-connect-query-php.md)
    - [Ligar à Base de Dados SQL com o Node.js](sql-database-connect-query-nodejs.md)
    - [Ligar à Base de Dados SQL com Java](sql-database-connect-query-java.md)
    - [Ligar à Base de Dados SQL com o Python](sql-database-connect-query-python.md)
    - [Ligar à Base de Dados SQL com Ruby](sql-database-connect-query-ruby.md)
- Repita os exemplos de código lógica:
    - [Resiliently se ligar ao SQL Server com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Resiliently se ligar ao SQL Server com o PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

