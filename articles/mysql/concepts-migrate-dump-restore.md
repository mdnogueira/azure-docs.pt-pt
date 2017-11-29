---
title: Migrar a base de dados MySQL utilizando captura e restauro na base de dados do Azure para MySQL | Microsoft Docs
description: "Este artigo explica duas formas comuns de cópia de segurança e restaurar bases de dados na base de dados do Azure para MySQL, utilizando ferramentas como mysqldump, MySQL Workbench e PHPMyAdmin."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: e962fd65244ceebfc7544dc5a1d1956dad811fea
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar a base de dados MySQL à base de dados do Azure para utilizar a captura e restauro de MySQL
Este artigo explica duas formas comuns de cópia de segurança e restaurar bases de dados na base de dados do Azure para MySQL
- Captura e restauro a partir da linha de comandos (utilizando mysqldump) 
- Informação do Estado e o restauro utilizando PHPMyAdmin 

## <a name="before-you-begin"></a>Antes de começar
Para seguir este guia de procedimentos, tem de ter:
- [Criar base de dados do Azure para o servidor de MySQL - portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado num computador o utilitário da linha de comandos.
- MySQL Workbench [MySQL Workbench transferir](https://dev.mysql.com/downloads/workbench/), Toad, Navicat ou outra ferramenta de MySQL de terceiros para informação do Estado e restaurar os comandos.

## <a name="use-common-tools"></a>Utilizar ferramentas comuns
Utilize ferramentas como o Workbench do MySQL, mysqldump, Toad ou Navicat e utilitários comuns remotamente ligar e restaurar dados na base de dados do Azure para MySQL. Utilize estas ferramentas no seu computador cliente com uma ligação à internet para ligar à base de dados do Azure para MySQL. Utilizar uma ligação SSL encriptado para melhores práticas de segurança, consulte também [conectividade de configurar o SSL na base de dados do Azure para MySQL](concepts-ssl-connection-security.md). Não é necessário mover os ficheiros de informação para qualquer localização nuvem especiais ao migrar a base de dados do Azure para MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Utilizações comuns para captura e restauro
Pode utilizar utilitários de MySQL como mysqldump e mysqlpump às bases de dados de captura e carga para uma base de dados do Azure MySQL em vários cenários comuns. Noutros cenários, pode utilizar o [importar e exportar](concepts-migrate-import-export.md) abordagem em vez disso.

- Base de dados de utilização informações de estado quando estiver a migrar a base de dados. Esta recomendação detém ao mover uma grande quantidade de dados MySQL, ou quando pretender minimizar a interrupção do serviço para sites em direto ou aplicações. 
-  Certifique-se que todas as tabelas na base de dados utilizam o motor de armazenamento InnoDB quando carregar os dados na base de dados do Azure para MySQL. Base de dados do Azure para MySQL suporta apenas motor de armazenamento de InnoDB e, por conseguinte, não suporta os motores de armazenamento alternativo. Se as tabelas são configuradas com outros motores de armazenamento, convertê-los para o formato do motor de InnoDB antes da migração para a base de dados do Azure para MySQL.
   Por exemplo, se tiver um WordPress ou WebApp utilizando as tabelas de MyISAM, primeiro converta nessas tabelas ao migrar para o formato de InnoDB antes de restaurar a base de dados do Azure para MySQL. Utilize a cláusula `ENGINE=InnoDB` para definir o motor utilizado ao criar uma nova tabela, em seguida, transferir os dados na tabela compatível antes do restauro. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar quaisquer problemas de compatibilidade, certifique-se de que é utilizada a mesma versão do MySQL nos sistemas de origem e de destino, quando captura bases de dados. Por exemplo, se o servidor de MySQL existente é uma versão 5.7, em seguida, deve migrar a base de dados do Azure para MySQL configurado para executar a versão 5.7. O `mysql_upgrade` comando não funcionar numa base de dados do Azure para o servidor de MySQL e não é suportado. Se precisar de atualizar entre versões do MySQL, primeiro informação do Estado ou exportar a base de dados de versão inferior para uma versão superior do MySQL no seu próprio ambiente. Em seguida, execute `mysql_upgrade`, antes de tentar efetuar a migração para uma base de dados do Azure para MySQL.

## <a name="performance-considerations"></a>Considerações de desempenho
Para otimizar o desempenho, tome aviso destas considerações quando captura grande bases de dados:
-   Utilize o `exclude-triggers` opção na mysqldump quando as bases de dados de captura. Exclua os acionadores de ficheiros de informação para evitar os comandos de Acionador acionando durante o restauro de dados. 
-   Utilize o `single-transaction` opção para definir o modo de isolamento de transação para REPEATABLE READ e envia uma instrução SQL de início de transação para o servidor antes de captura de dados. Captura de várias tabelas dentro de uma transação única faz com que algumas armazenamento adicional ser consumido durante o restauro. O `single-transaction` opção e a `lock-tables` opção são mutuamente exclusivos porque as tabelas de bloqueio faz com que quaisquer pendentes transações ser consolidada implicitamente. A informação do Estado tabelas grandes, combinar o `single-transaction` opção com a `quick` opção. 
-   Utilize o `extended-insert` sintaxe de linha de vários que inclui várias listas de valor. Isto resulta num ficheiro mais pequeno de captura e acelera inserções quando o ficheiro é recarregar a pedido.
-  Utilize o `order-by-primary` opção na mysqldump quando captura bases de dados, para que os dados é colocado em script por ordem de chave primária.
-   Utilize o `disable-keys` opção na mysqldump quando captura dados, para desativar as restrições de chave externa antes de carga. Desativar verificações de chaves externas proporciona ganhos de desempenho. Ative as restrições e verificar os dados após o carregamento para garantir a integridade referencial.
-   Utilize as tabelas particionadas quando for adequado.
-   Carregar os dados em paralelo. Evite demasiada paralelismo que iria fazer com que atingiu o limite de um recurso e monitorizar recursos utilizando as métricas disponíveis no portal do Azure. 
-   Utilize o `defer-table-indexes` opção na mysqlpump quando captura bases de dados, para que a criação de índices ocorre após o carregamento de dados de tabelas.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um ficheiro de cópia de segurança a partir da linha de comandos utilizando mysqldump
Para fazer cópias de segurança base de dados MySQL existente no servidor local no local ou numa máquina virtual, execute o seguinte comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros para fornecer são:
- [uname] O nome de utilizador de base de dados 
- [passar] A palavra-passe para a base de dados (não observe não existe nenhum espaço entre -p e a palavra-passe) 
- [dbname] O nome da base de dados 
- [backupfile.sql] o nome de ficheiro para a cópia de segurança da base de dados 
- [-optar ativamente por participar] A opção de mysqldump 

Por exemplo, para fazer cópias de segurança de uma base de dados com o nome testdb no seu servidor MySQL com o nome de utilizador 'testuser' e com nenhuma palavra-passe para um ficheiro testdb_backup.sql, utilize o seguinte comando. O comando cria cópias de segurança a `testdb` base de dados para um ficheiro denominado `testdb_backup.sql`, que contém todas as declarações de SQL Server necessárias recriar a base de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas na base de dados para cópia de segurança, uma lista de nomes de tabela separados por espaços. Por exemplo, para cópia de segurança apenas tabelas de tabela1 e tabela2 do 'testdb', siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer uma cópia de segurança mais do que uma base de dados em simultâneo, utilize - base de dados de comutador e lista os nomes de base de dados, separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Para fazer uma cópia de segurança de todas as bases de dados no servidor em simultâneo, deverá utilizar o - opção de bases de dados de todos os.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Criar uma base de dados no destino da base de dados do Azure para o servidor de MySQL
Crie uma base de dados vazia no destino da base de dados do Azure para o servidor de MySQL onde pretende migrar os dados. Utilize uma ferramenta como o Workbench do MySQL, Toad ou Navicat para criar a base de dados. A base de dados pode ter o mesmo nome, como a base de dados que está contido os dados capturados ou pode criar uma base de dados com um nome diferente.

Para obter ligado, localize as informações de ligação na página de propriedades na base de dados do Azure para MySQL.
![Localizar as informações de ligação no portal do Azure](./media/concepts-migrate-dump-restore/1_server-properties-name-login.png)

Adicione as informações de ligação para o MySQL Workbench.
![Cadeia de ligação do MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar a base de dados MySQL através da linha de comandos ou MySQL Workbench
Assim que tiver criado a base de dados de destino, pode utilizar o comando de mysql ou MySQL Workbench para restaurar os dados para o específicos criado recentemente da base de dados a partir do ficheiro de informação.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados na base de dados recentemente criado no destino da base de dados do Azure para o servidor de MySQL.
```bash
$ mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportar utilizando PHPMyAdmin
Para exportar, pode utilizar o phpMyAdmin ferramenta comum, que lhe pode já ter instalado localmente no seu ambiente. Para exportar a base de dados MySQL PHPMyAdmin a utilizar:
- Abra phpMyAdmin.
- Selecione a base de dados. Clique no nome de base de dados na lista à esquerda. 
- Clique em de **exportar** ligação. É apresentada uma nova página Ver a informação da base de dados.
- Na área de exportação, clique em de **Selecionar tudo** ligação para escolher as tabelas na base de dados. 
- Na área de opções de SQL Server, clique nas opções apropriadas. 
- Clique em de **guarde como ficheiro** opção e a compressão correspondente opção e, em seguida, clique em de **aceda** botão. Uma caixa de diálogo deve aparecer que lhe pede para guardar o ficheiro localmente.

## <a name="import-using-phpmyadmin"></a>Importar com PHPMyAdmin
Importar a base de dados é semelhante a exportar. Efetue as seguintes ações:
- Abra phpMyAdmin. 
- Na página de configuração phpMyAdmin, clique em **adicionar** para adicionar a base de dados do Azure para o servidor de MySQL. Forneça as informações de início de sessão e os detalhes de ligação.
- Criar uma base de dados com o nome corretamente e selecione-a no lado esquerdo do ecrã. Para reescrever a base de dados existente, clique no nome de base de dados, selecione todas as caixas de verificação ao lado dos nomes de tabela e selecione **Drop** para eliminar as tabelas existentes. 
- Clique em de **SQL** ligação para mostrar a página onde podem introduzir os comandos do SQL Server ou carregar o ficheiro SQL. 
- Utilize o **procurar** botão Localizar o ficheiro de base de dados. 
- Clique em de **aceda** botão Exportar a cópia de segurança, execute os comandos SQL e voltar a criar a base de dados.

## <a name="next-steps"></a>Passos seguintes
[Ligar aplicações Azure base de dados MySQL](./howto-connection-string.md)
