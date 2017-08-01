---
title: "Ligar à Base de Dados do Azure para PostgreSQL com PHP | Microsoft Docs"
description: "Este guia de início rápido fornece um código de exemplo de PHP que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: php
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 009cc7941dc5d60153f1ab27adb9b86866b9a3f2
ms.contentlocale: pt-pt
ms.lasthandoff: 06/30/2017

---

# <a name="azure-database-for-postgresql-use-php-to-connect-and-query-data"></a>Base de Dados do Azure para PostgreSQL: utilize o PHP para ligar e consultar dados
Este início rápido explica como se pode ligar a uma Base de Dados do Azure para PostgreSQL através de uma aplicação [PHP](http://php.net/manual/intro-whatis.php). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este artigo pressupõe que esteja familiarizado com o desenvolvimento que utiliza o PHP, mas que nunca trabalhou com a Base de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este início rápido utiliza os recursos criados em qualquer um desses guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI do Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-php"></a>Instalar o PHP
Instale o PHP no seu próprio servidor ou crie uma [aplicação Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview) do Azure que inclua o PHP.

### <a name="windows"></a>Windows
- Transfira a [versão 7.1.4 do PHP (x64) não «thread safe»](http://windows.php.net/download#php-7.1)
- Instale o PHP e veja o [manual do PHP](http://php.net/manual/install.windows.php) para mais configurações
- O código utiliza a classe **pgsql** (ext/php_pgsql.dll) incluída na instalação do PHP. 
- Ative a extensão **pgsql** ao editar o ficheiro de configuração php.ini, geralmente localizado em `C:\Program Files\PHP\v7.1\php.ini`. O ficheiro de configuração deve conter uma linha com o texto `extension=php_pgsql.so`. Se não for apresentado, adicione o texto e guarde o ficheiro. Se o texto estiver presente, mas comentado com um prefixo de ponto e vírgula, remova os comentários do texto ao remover o ponto e vírgula.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Transfira a [versão 7.1.4 do PHP (x64) não «thread safe»](http://php.net/downloads.php) 
- Instale o PHP e veja o [manual do PHP](http://php.net/manual/install.unix.php) para mais configurações
- O código utiliza a classe **pgsql** (php_pgsql.so). Instale-o ao executar `sudo apt-get install php-pgsql`.
- Ative a extensão **pgsql** ao editar o ficheiro de configuração `/etc/php/7.0/mods-available/pgsql.ini`. O ficheiro de configuração deve conter uma linha com o texto `extension=php_pgsql.so`. Se não for apresentado, adicione o texto e guarde o ficheiro. Se o texto estiver presente, mas comentado com um prefixo de ponto e vírgula, remova os comentários do texto ao remover o ponto e vírgula.

### <a name="macos"></a>MacOS
- Transferir a [versão 7.1.4 do PHP](http://php.net/downloads.php)
- Instale o PHP e veja o [manual do PHP](http://php.net/manual/install.macosx.php) para mais configurações

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Precisa das credenciais de nome de servidor totalmente qualificado e de início de sessão.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e procure o servidor que acabou de criar, por exemplo, **mypgserver-20170401**.
3. Clique no nome do servidor **mypgserver 20170401**.
4. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.
 ![Base de Dados do Azure para o PostgreSQL – Início de Sessão de Administrador do Servidor](./media/connect-php/1-connection-string.png)
5. Caso se tenha esquecido das informações de início de sessão do seu servidor, navegue até à página **Descrição geral** para visualizar o nome de início de sessão de administrador do servidor e, se necessário, repor a palavra-passe.

## <a name="connect-and-create-a-table"></a>Ligar e criar uma tabela
Utilize o código seguinte para ligar e criar uma tabela com a instrução SQL **CREATE TABLE**, seguida das instruções SQL **INSERT INTO** para adicionar linhas à tabela.

O código chama o método [pg_connect()](http://php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](http://php.net/manual/en/function.pg-query.php) várias vezes para executar vários comandos e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) para verificar os detalhes, se tiver ocorrido um erro. Em seguida, chama o método [pg_close()](http://php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password") 
        or die("Failed to create connection to database: ". pg_last_error(). "<br/>");
    print "Successfully created connection to database.<br/>";

    // Drop previous table of same name if one exists.
    $query = "DROP TABLE IF EXISTS inventory;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished dropping table (if existed).<br/>";

    // Create table.
    $query = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished creating table.<br/>";

    // Insert some data into table.
    $name = '\'banana\'';
    $quantity = 150;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($1, $2);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'orange\'';
    $quantity = 154;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'apple\'';
    $quantity = 100;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error()). "<br/>";

    print "Inserted 3 rows of data.<br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="read-data"></a>Ler dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

 O código chama o método [pg_connect()](http://php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](http://php.net/manual/en/function.pg-query.php) para executar o comando SELECT ao manter os resultados num conjunto de resultados, e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) para verificar os detalhes se tiver ocorrido um erro.  Para ler o conjunto de resultados, é chamado o método [pg_fetch_row()](http://php.net/manual/en/function.pg-fetch-row.php) num ciclo, uma vez por linha, e os dados das linhas são obtidos numa matriz `$row`, com um valor de dados por coluna em cada posição de matriz.  Para libertar o conjunto de resultados, é chamado o método [pg_free_result()](http://php.net/manual/en/function.pg-free-result.php). Em seguida, chama o método [pg_close()](http://php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";
    
    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). "<br/>");

    print "Successfully created connection to database. <br/>";

    // Perform some SQL queries over the connection.
    $query = "SELECT * from inventory";
    $result_set = pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    while ($row = pg_fetch_row($result_set))
    {
        print "Data row = ($row[0], $row[1], $row[2]). <br/>";
    }

    // Free result_set
    pg_free_result($result_set);

    // Closing connection
    pg_close($connection);
?>
```

## <a name="update-data"></a>Atualizar dados
Utilize o código seguinte para se ligar e atualizar os dados com uma instrução SQL **UPDATE**.

O código chama o método [pg_connect()](http://php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](http://php.net/manual/en/function.pg-query.php) para executar um comando e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) para verificar os detalhes, se tiver ocorrido um erro. Em seguida, chama o método [pg_close()](http://php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). ".<br/>");

    print "Successfully created connection to database. <br/>";

    // Modify some data in table.
    $new_quantity = 200;
    $name = '\'banana\'';
    $query = "UPDATE inventory SET quantity = $new_quantity WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ".<br/>");
    print "Updated 1 row of data. </br>";

    // Closing connection
    pg_close($connection);
?>
```


## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e ler os dados com uma instrução SQL **DELETE**. 

 O código chama o método [pg_connect()](http://php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](http://php.net/manual/en/function.pg-query.php) para executar um comando e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) para verificar os detalhes, se tiver ocorrido um erro. Em seguida, chama o método [pg_close()](http://php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
            or die("Failed to create connection to database: ". pg_last_error(). ". </br>");

    print "Successfully created connection to database. <br/>";

    // Delete some data from table.
    $name = '\'orange\'';
    $query = "DELETE FROM inventory WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ". <br/>");
    print "Deleted 1 row of data. <br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com as opções Exportar e Importar](./howto-migrate-using-export-and-import.md)

