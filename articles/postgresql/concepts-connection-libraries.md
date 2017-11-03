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
ms.openlocfilehash: 7d6f13336b3913299433e25c69a41f8c94f598b2
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de ligação para base de dados do Azure para PostgreSQL
Este tópico apresenta uma lista de bibliotecas e controladores que os programadores podem utilizar para programar aplicações para ligar e consultar a base de dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de idioma utilizadas para ligar ao servidor PostgreSQL são projetos externos e são distribuídas de forma independente. Estas são suportadas nas plataformas Windows, Linux e Mac. Alguns dos controladores cliente mais populares estão listados na seguinte tabela:

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
