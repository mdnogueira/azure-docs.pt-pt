---
title: "Bibliotecas de ligação para base de dados do Azure para PostgreSQL | Microsoft Docs"
description: "Este artigo descreve bibliotecas vários controladores de que os programadores podem utilizar quando programação de aplicações para ligar e consultar a base de dados do Azure para PostgreSQL e."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: f371d5cd4e20096d5101fadf9066e3a135218d0b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de ligação para base de dados do Azure para PostgreSQL
Este artigo apresenta uma lista de bibliotecas e controladores que os programadores podem utilizar para desenvolver aplicações para ligar e consultar a base de dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de idioma utilizadas para ligar ao servidor PostgreSQL são projetos externos e são distribuídas de forma independente. As bibliotecas listadas são suportadas nas plataformas Windows, Linux e Mac, para ligar à base de dados do Azure para PostgreSQL. Vários exemplos de início rápido estão listados na secção passos seguinte.

| **Idioma** | **Interface do cliente** | **Informações adicionais** | **Transferência** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | API de BD em conformidade 2.0 | [Transferência](http://initd.org/psycopg/download/) |
| PHP | [PHP pgsql](https://php.net/manual/en/book.pgsql.php) | Extensão de base de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pacote de npm do grupo de proteção](https://www.npmjs.com/package/pg) | Puro JavaScript não bloquear cliente | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Controlador JDBC de tipo 4 | [Transferência](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Gem de grupo de proteção](https://deveiate.org/code/pg/) | Ruby Interface | [Transferência](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Ir | [Pacote pq](https://godoc.org/github.com/lib/pq) | Controlador de postgres aceda puro | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Fornecedor de dados do ADO.NET | [Transferência](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Controlador ODBC | [Transferência](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface de linguagem C primária | Incluído |
| C++ | [libpqxx](http://pqxx.org/) | Interface do novo estilo C++ | [Transferência](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passos seguintes
Estes inícios rápidos sobre como ligar e consultar base de dados do Azure para PostgreSQL utilizando o seu idioma à escolha de leitura:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (c#)](./connect-csharp.md) | [aceda](./connect-go.md)
