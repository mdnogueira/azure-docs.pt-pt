---
title: "Ligar-se à Base de Dados SQL do Azure com o PHP | Microsoft Docs"
description: "Apresenta um exemplo de código PHP que pode utilizar para se ligar e consultar a Base de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 5b90c9b49448c6bc225f9d6ba7227782b81fc5ed
ms.contentlocale: pt-pt
ms.lasthandoff: 06/15/2017


---
<a id="azure-sql-database-use-php-to-connect-and-query-data" class="xliff"></a>

# Base de Dados SQL do Azure: utilizar o PHP para se ligar e consultar dados

Este guia de início rápido explica como utilizar o [PHP](http://php.net/manual/en/intro-whatis.php) para se ligar a uma Base de Dados SQL do Azure; em seguida, utilize as instruções Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas Windows, Mac OS e Ubuntu Linux.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)

<a id="install-php-and-database-communications-software" class="xliff"></a>

## Instalar o PHP e o software de comunicações com a base de dados

Os passos desta secção pressupõem que está familiarizado com a programação com PHP e que nunca trabalhou com a Base de Dados SQL do Azure. Se não estiver familiarizado com a programação com PHP, aceda a [Criar uma aplicação com o SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) e selecione **PHP** e, em seguida, selecione o seu sistema operativo.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Abra o seu terminal e insira os seguintes comandos para instalar **brew**, **Microsoft ODBC Driver para Mac** e **Controladores de PHP da Microsoft para SQL Server**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Insira os seguintes comandos para instalar **Controlador Microsoft ODBC para Mac** e **Controladores de PHP da Microsoft para SQL Server**.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

<a id="windows" class="xliff"></a>

### **Windows**
- Instalar o PHP 7.1.1 (x64) [do WebPlatform Installer](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 
- Instalar o [Controlador Microsoft ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339). 
- Transfira as dlls não seguras para o thread para o [Controlador Microsoft PHP para SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) e coloque os binários na pasta PHP\v7.x\ext.
- Em seguida, edite o ficheiro .ini (C:\Program Files\PHP\v7.1\php.ini) adicionando a referência para dll. Por exemplo:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

Neste ponto, deverá ter as dlls registradas com o PHP.

<a id="get-connection-information" class="xliff"></a>

## Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem abaixo. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se esqueceu das informações de início de sessão do seu servidor, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe.     
    
<a id="select-data" class="xliff"></a>

## Selecionar dados
Utilize o seguinte código para consultar os 20 melhores produtos por categoria, utilizando a função [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) com a instrução Transact SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). A função sqlsrv_query é usada para recuperar um conjunto de resultados de uma consulta na base de dados SQL. Esta função aceita uma consulta e devolve um conjunto de resultados que pode ser iterado com o uso de [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


<a id="insert-data" class="xliff"></a>

## Inserir dados
Utilize o seguinte código para inserir um novo produto na tabela SalesLT.Product com a função [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) instrução Transact SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

<a id="update-data" class="xliff"></a>

## Atualizar dados
Utilize o seguinte código para atualizar os dados na Base de Dados SQL do Azure com a função [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) e a instrução Transact SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

<a id="delete-data" class="xliff"></a>

## Eliminar dados
Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com a função [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) e a instrução Transact SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

<a id="next-steps" class="xliff"></a>

## Passos seguintes
- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Controlador Microsoft PHP para SQL Server](https://github.com/Microsoft/msphpsql/)
- [Relatório de problemas/perguntas](https://github.com/Microsoft/msphpsql/issues)


