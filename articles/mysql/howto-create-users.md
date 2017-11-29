---
title: Criar utilizadores na base de dados do Azure para o servidor de MySQL | Microsoft Docs
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma base de dados do Azure para o servidor de MySQL.
services: mysql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 8adb74e11570ac60ad3b898b737cff4699f2bbf1
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Criar utilizadores na base de dados do Azure para o servidor de MySQL 
Este artigo descreve como pode criar utilizadores numa base de dados do Azure para o servidor de MySQL.

Quando criou a sua base de dados do Azure pela primeira vez para MySQL, que forneceu um nome de utilizador de início de sessão de administrador de servidor e a palavra-passe. Para obter mais informações, pode seguir o [início rápido](quickstart-create-mysql-server-database-using-azure-portal.md). Pode localizar o nome de utilizador do início de sessão do administrador do servidor do portal do Azure.

O utilizador de administrador do servidor obtém determinados privilégios para o servidor, conforme listado: SELECIONAR, inserir, ATUALIZAR, eliminar, criar, remover, recarregar, processo, as referências, índice, ALTER, Mostrar bases de dados, criar tabelas temporárias, tabelas de bloqueio, executar, replicação MULTISSERVIDOR, replicação CLIENTE, CRIAR VISTA, MOSTRAR VISTA, CRIE ROTINA, ALTER ROTINA, CRIAR O ACIONADOR DE UTILIZADOR, EVENTO,

Assim que a base de dados do Azure para o servidor de MySQL for criado, pode utilizar a primeira conta de utilizador do administrador do servidor para criar utilizadores adicionais e conceder acesso de administrador aos mesmos. Além disso, a conta de administrador do servidor pode ser utilizada para criar com menos privilégios de utilizadores que têm acesso para esquemas de base de dados individuais.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Como criar utilizadores administradores adicionais na base de dados do Azure para MySQL
1. Obter o nome de utilizador de admin e informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode localizar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a ferramenta de cliente preferencial, tais como o Workbench do MySQL, mysql.exe, HeidiSQL ou outros. 
   Se não souber como ligar, consulte [utilize MySQL Workbench para ligar e consultar dados](./connect-workbench.md)

3. Edite e execute o seguinte código do SQL Server. Substitua o novo nome de utilizador para o valor do marcador de posição `new_master_user`. Esta sintaxe concede os privilégios listados em todos os esquemas de base de dados (*.*) para o nome de utilizador (new_master_user neste exemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Certifique-se a concede 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Como criar utilizadores de base de dados na base de dados do Azure para MySQL

1. Obter o nome de utilizador de admin e informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode localizar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a ferramenta de cliente preferencial, tais como o Workbench do MySQL, mysql.exe, HeidiSQL ou outros. 
   Se não souber como ligar, consulte [utilize MySQL Workbench para ligar e consultar dados](./connect-workbench.md)

3. Edite e execute o seguinte código do SQL Server. Substitua o valor do marcador de posição `db_user` com os seus pretendido novo nome de utilizador e o valor do marcador de posição `testdb` com o seu próprio nome de base de dados.

   Esta sintaxe de código do SQL Server cria uma nova base de dados com o nome testdb para fins de exemplo. Em seguida, cria um novo utilizador no serviço de MySQL e concede todos os privilégios para o novo esquema de base de dados (testdb.\*) para esse utilizador. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Certifique-se a concede na base de dados.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Inicie sessão no servidor, especificar a base de dados designado, utilizando o novo nome de utilizador e palavra-passe. Este exemplo mostra a linha de comandos mysql. Com este comando, é-lhe pedida a palavra-passe para o nome de utilizador. Substitua o seu próprio nome do servidor, o nome de base de dados e o nome de utilizador.

   ```azurecli-interactive
   mysql --host myserver4demo.mysql.database.azure.com --database testdb --user db_user@myserver4demo -p
   ```

## <a name="next-steps"></a>Passos seguintes
Abra a firewall para os endereços IP de máquinas de novos utilizadores para permitir que os mesmos se liguem: [criar e gerir o Azure base de dados MySQL das regras de firewall com o portal do Azure](howto-manage-firewall-using-portal.md) ou [CLI do Azure](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre a gestão de contas de utilizador, consulte a documentação do produto MySQL para [gestão de contas de utilizador](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [conceder sintaxe](https://dev.mysql.com/doc/refman/5.7/en/grant.html), e [privilégios](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
