---
title: "Ligar-se à Base de Dados SQL do Azure com o Node.js | Microsoft Docs"
description: "Apresenta um exemplo de código Node.js que pode utilizar para ligar e consultar a Base de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: a7b1a504c6ae7850e5f986895adcf4d92fd5adc1
ms.contentlocale: pt-pt
ms.lasthandoff: 06/15/2017


---
<a id="azure-sql-database-use-nodejs-to-connect-and-query-data" class="xliff"></a>

# Base de Dados SQL do Azure: utilize o Node.js para ligar e consultar dados

Este guia de início rápido explica como utilizar o [Node.js](https://nodejs.org/en/) para se ligar a uma Base de Dados SQL do Azure; em seguida, utilize as instruções Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas Windows, Mac OS e Ubuntu Linux.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)

<a id="install-nodejs" class="xliff"></a>

## Instalar o Node.js 

Os passos nesta secção partem do princípio de que está familiarizado com programação com Node.js e nunca trabalhou com a Base de Dados SQL do Azure. Se não estiver familiarizado com a programação com Node.js, aceda a [Criar uma aplicação com o SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) e selecione **Node.js** e, em seguida, selecione o sistema operativo.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Introduza os seguintes comandos para instalar **brew**, um gestor de pacotes de fácil utilização para Mac OS X e **Node.js**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Introduza os seguintes comandos para instalar **Node.js** e **npm**, um gestor de pacotes para Node.js.

```bash
sudo apt-get install -y nodejs npm
```

<a id="windows" class="xliff"></a>

### **Windows**
Visite a [página de transferências do Node. js](https://nodejs.org/en/download/) e selecione a opção de instalador Windows que pretende.


<a id="install-the-tedious-sql-server-driver-for-nodejs" class="xliff"></a>

## Instalar o controlador do SQL Server Tedious para Node.js
O controlador recomendado para Node. js é  **[tedious](https://github.com/tediousjs/tedious)**. O Tedious é uma iniciativa de código-fonte aberto que a Microsoft está utiliza para aplicações Node. js em qualquer plataforma. Para este tutorial, é necessário um diretório vazio para incluir o seu código e as dependências `npm` que iremos instalar.

Para instalar o controlador **tedious**, execute o seguinte comando no seu diretório:

```cmd
npm install tedious
```

<a id="get-connection-information" class="xliff"></a>

## Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem abaixo. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se tiver esquecido das informações de início de sessão do seu servidor da Base de Dados SQL do Azure, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe.
    
<a id="select-data" class="xliff"></a>

## Selecionar dados

Use o seguinte código para consultar o banco de dados SQL do Azure para 20 principais produtos por categoria. Primeiro importe as classes Connect e Request do controlador a partir da biblioteca do controlador Tedious. Depois criar o objeto de configuração e substituir as variáveis do **nome de utilziador**, **palavra-passe**, **servidor** e **base de dados** pelos valores que especificou quando criou a base de dados com os dados de exemplo AdventureWorksLT. Crie um `Connection` objeto usando o objeto especificado`config`. Depois disso, defina a chamada de retorno para o `connect` evento do `connection` objeto para executar a função `queryDatabase()`.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

<a id="insert-data-into-the-database" class="xliff"></a>

## Inserir dados na base de dados
Utilize o seguinte código para inserir um novo produto na tabela SalesLT.Product com a função `insertIntoDatabase()` e a instrução Transact SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Substitua as variáveis do **nome de utilizador**, **palavra-passe**, **servidor** e **base de dados** pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

<a id="update-data-in-the-database" class="xliff"></a>

## Atualizar dados na base de dados
Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com a função `updateInDatabase()` e a instrução Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Substitua as variáveis do **nome de utilizador**, **palavra-passe**, **servidor** e **base de dados** pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. Este exemplo usa o nome do produto inserido no exemplo anterior.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

<a id="delete-data-from-the-database" class="xliff"></a>

## Eliminar dados da base de dados
Use o código a seguir para eliminar dados da base de dados. Substitua as variáveis do **nome de utilizador**, **palavra-passe**, **servidor** e **base de dados** pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. Desta vez, use uma **instrução DELETE** na função `deleteFromDatabase()`. Este exemplo também usa o nome do produto inserido no exemplo anterior.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


<a id="next-steps" class="xliff"></a>

## Passos Seguintes
- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Controlador Microsoft Node.js para SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- [Ligar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).



