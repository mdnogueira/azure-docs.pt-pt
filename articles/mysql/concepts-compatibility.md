---
title: "Compatibilidade de ferramentas de gestão e o MySQL controladores | Microsoft Docs"
description: "Controladores de MySQL e ferramentas de gestão compatíveis com a base de dados do Azure para MySQL"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 1042f7919b8761bdbc23ae19871703c53dff28f0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Controladores de MySQL e ferramentas de gestão compatíveis com a base de dados do Azure para MySQL
Este artigo descreve os controladores e ferramentas de gestão que são compatíveis com a base de dados do Azure para MySQL.

## <a name="mysql-drivers"></a>Controladores de MySQL
Base de dados do Azure para MySQL utiliza a edição community mais popular do mundo da base de dados MySQL. Por conseguinte, é compatível com uma ampla variedade de controladores e linguagens de programação. O objetivo é para suportar as versões mais recentes três controladores MySQL e esforços com autores da Comunidade open source para constantemente melhorar a funcionalidade e a facilidade de utilização de controladores de MySQL continuar. É fornecida uma lista de controladores que tenham sido testado e a ser compatível com a base de dados do Azure para MySQL 5.6 e 5.7 encontrado na tabela seguinte:

| **Controlador** | **Ligações** | **Versões compatíveis** | **Versões de uma** | **Notas** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://PHP.NET/downloads.php | 5.5 5.6 7. x | 5.3 | Para PHP 7.0 ligação com SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de ligação. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Conjunto PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opção para FALSO.|
| .Net | [MySqlConnector no Github]: https://github.com/mysql-net/MySqlConnector/releases <br> [Pacote de instalação do Nuget]:<br> https://www.nuget.org/Packages/MySqlConnector/ | 0.27 e após | 0.26.5 e antes | |
| Nodejs |  [MySQLjs no Github]:<br> https://github.com/mysqljs/MySQL/releases <br> [Pacote de instalação de NPM]:<br> Execute "npm instalar mysql" de NPM | 2.15 | 2.14.1 e antes | |
| ACEDA | https://github.com/go-SQL-Driver/MySQL/releases | 1.3 | 1.2 e antes | Utilizar allowNativePasswords = true na cadeia de ligação |
| Python | https://pypi.python.org/pypi/MySQL-Connector-Python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e antes | |
| Java | https://downloads.mariadb.org/Connector-Java/ | 2.1 2.0 1.6 | 1.5.5 e antes | |

## <a name="management-tools"></a>Ferramentas de Gestão
Expande a vantagem de compatibilidade para ferramentas de gestão de bases de dados bem. As ferramentas existentes devem continuar a trabalhar com a base de dados do Azure para MySQL, desde que funciona a manipulação de base de dados dentro do confines de permissões de utilizador. Três ferramentas de gestão da base de dados comuns que foram testadas e não for compatível com a base de dados do Azure para MySQL 5.6 e 5.7 estão listadas na seguinte tabela:

|                                     | **MySQL Workbench 6. x e até** | **Navicat 12** | **PHPMyAdmin 4. x e até** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Criar, atualizar, ler, escrever, eliminar | X | X | X |
| Ligação de SSL | X | X | X |
| Conclusão de automática de consulta SQL | X | X |  |
| Importar e exportar dados | X | X | X |
| Exportar para vários formatos | X | X | X |
| Cópia de Segurança e Restauro |  | X |  |
| Apresentar os parâmetros de servidor | X | X | X |
| Apresentar ligações de cliente | X | X | X |
