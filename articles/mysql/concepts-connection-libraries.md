---
title: "Bibliotecas de ligação para base de dados do Azure para MySQL | Microsoft Docs"
description: "Este artigo apresenta uma lista de cada biblioteca ou controlador programas de cliente podem utilizar ao ligar à base de dados do Azure para MySQL."
services: mysql
author: mswutao
ms.author: wutao
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/20/2017
ms.openlocfilehash: 759fa290cff94b04e29edd818b985b11267caab7
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliotecas de ligação para base de dados do Azure para MySQL
Este artigo apresenta uma lista de cada biblioteca ou controlador programas de cliente podem utilizar ao ligar à base de dados do Azure para MySQL.

## <a name="client-interfaces"></a>Interfaces de cliente
MySQL oferece a conectividade de controlador de base de dados standard para utilizar o MySQL com as ferramentas que são compatíveis com as normas do sector ODBC e JDBC e aplicações. Qualquer sistema que trabalha com ODBC ou JDBC pode utilizar o MySQL.

| **Idioma** | **Plataforma** | **Recursos adicionais** | **Transferência** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Controlador nativo MySQL para PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Transferência](http://php.net/downloads.php) |
| ODBC | Plataformas Windows, Linux, Mac OS X e Unix | [Guia para programadores de MySQL conector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Transferência](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guia para programadores de MySQL conector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Transferência](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Independente da plataforma | [Guia para programadores de MySQL conector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Transferência](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [nó/sidorares-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Transferência](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guia para programadores de MySQL conector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guia para programadores de MySQL conector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guia para programadores de MySQL conector/C](https://dev.mysql.com/doc/connector-c/en/) | [Transferência](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plataformas Windows, Linux, Mac OS X e Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Transferência](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passos seguintes
Estes inícios rápidos sobre como ligar e consultar base de dados do Azure para MySQL, utilizando o seu idioma à escolha de leitura:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (c#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [aceda](./connect-go.md)

