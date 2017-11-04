---
title: "Ligar à Base de Dados do Azure para MySQL do PHP | Microsoft Docs"
description: "Este guia de início rápido proporciona vários exemplos de código PHP que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL."
services: mysql
author: mswutao
ms.author: wuta
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 2af5871e8bf67070c83b5faebc1f9e44b0de609e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-php-to-connect-and-query-data"></a>Base de Dados do Azure para MySQL: utilizar o PHP para se ligar e consultar dados
Este guia de início rápido explica como se pode ligar a uma Base de Dados do Azure para MySQL através de uma aplicação [PHP](http://php.net/manual/intro-whatis.php). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este tópico assume que está familiarizado com o desenvolvimento com o PHP e que estiver a trabalhar com a base de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-php"></a>Instalar o PHP
Instale o PHP no seu próprio servidor ou crie uma [aplicação Web](../app-service/app-service-web-overview.md) do Azure que inclua o PHP.

### <a name="macos"></a>MacOS
- Transferir [PHP 7.1.4 versão](http://php.net/downloads.php).
- Instalar o PHP e consulte o [PHP manual](http://php.net/manual/install.macosx.php) para configuração adicional.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Transferir [PHP 7.1.4 versão (x64) seguro não thread](http://php.net/downloads.php).
- Instalar o PHP e consulte o [PHP manual](http://php.net/manual/install.unix.php) para configuração adicional.

### <a name="windows"></a>Windows
- Transferir [PHP 7.1.4 versão (x64) seguro não thread](http://windows.php.net/download#php-7.1).
- Instalar o PHP e consulte o [PHP manual](http://php.net/manual/install.windows.php) para configuração adicional.

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No painel do lado esquerdo, clique em **Todos os recursos** e procure o servidor que criou (por exemplo, **myserver4demo**).
3. Clique no nome do servidor.
4. Selecione o servidor **propriedades** página e, em seguida, anote o **nome do servidor** e **nome de início de sessão de administração do servidor**.
 ![Nome do servidor da Base de Dados do Azure para o MySQL](./media/connect-php/1_server-properties-name-login.png)
5. Se se esquecer da sua informações de início de sessão do servidor, navegue para o **descrição geral** página para ver o nome de início de sessão de administração do servidor e, se necessário repor a palavra-passe.

## <a name="connect-and-create-a-table"></a>Ligar-se e criar uma tabela
Utilize o seguinte código para ligar e criar uma tabela utilizando **CREATE TABLE** instrução SQL. 

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código de métodos de chamadas [mysqli_init](http://php.net/manual/mysqli.init.php) e [mysqli_real_connect](http://php.net/manual/mysqli.real-connect.php) para ligar ao MySQL. Em seguida, chama o método [mysqli_query](http://php.net/manual/mysqli.query.php) para executar a consulta. Em seguida, chama o método [mysqli_close](http://php.net/manual/mysqli.close.php) para fechar a ligação.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Inserir dados
Utilize o seguinte código para ligar e inserir dados, utilizando um **inserir** instrução SQL.

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) para criar uma instrução de introdução preparada e, em seguida, une os parâmetros de cada valor introduzido na coluna através do método [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php). O código é executado a instrução utilizando o método [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php) e, posteriormente, fecha a instrução utilizando o método [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Ler dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **SELECIONE** instrução SQL.  O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_query](http://php.net/manual/mysqli.query.php) executar a consulta de sql e o método [mysqli_fetch_assoc](http://php.net/manual/mysqli-result.fetch-assoc.php) para obter as linhas resultantes.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Atualizar dados
Utilize o seguinte código para estabelecer ligação e atualizar os dados utilizando um **ATUALIZAR** instrução SQL.

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) para criar uma instrução de atualização preparada e, em seguida, une os parâmetros de cada valor introduzido na coluna através do método [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php). O código é executado a instrução utilizando o método [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php) e, posteriormente, fecha a instrução utilizando o método [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Eliminar dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **eliminar** instrução SQL. 

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) para criar uma instrução de eliminação preparada e, em seguida, une os parâmetros da cláusula «onde» na instrução através do método [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php). O código é executado a instrução utilizando o método [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php) e, posteriormente, fecha a instrução utilizando o método [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Criar uma aplicação Web PHP e MySQL no Azure](../app-service/app-service-web-tutorial-php-mysql.md?toc=%2fazure%2fmysql%2ftoc.json)
