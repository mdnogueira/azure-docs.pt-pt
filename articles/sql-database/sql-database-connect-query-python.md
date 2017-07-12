---
title: "Ligar-se à Base de Dados SQL do Azure com o Python | Microsoft Docs"
description: "Apresenta um exemplo de código Python que pode utilizar para se ligar e consultar a Base de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 99195b43a1577f978562864bac5fa12cdeb95d63
ms.contentlocale: pt-pt
ms.lasthandoff: 06/17/2017

---
<a id="azure-sql-database-use-python-to-connect-and-query-data" class="xliff"></a>

# Base de Dados SQL do Azure: utilizar o Python para se ligar e consultar dados

 Este guia de início rápido explica como utilizar o [Python](https://python.org) para se ligar a uma Base de Dados SQL do Azure; em seguida, utilize as instruções Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas Windows, Mac OS e Ubuntu Linux.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)

<a id="install-the-python-and-database-communication-libraries" class="xliff"></a>

## Instalar as bibliotecas de comunicação Python e a base de dados

Os passos nesta secção pressupõem que está familiarizado com programação que utiliza Python e que nunca trabalhou com a Base de Dados SQL do Azure. Se não estiver familiarizado com a programação que utiliza Python, aceda a [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Criar uma aplicação com o SQL Server), selecione **Python** e, em seguida, selecione o seu sistema operativo.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu script Python. Introduza os seguintes comandos para instalar **brew**, **Microsoft ODBC Driver para Mac** e **pyodbc**. O pyodbc utiliza o Microsoft ODBC Driver no Linux para se ligar a Bases de Dados SQL.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu script Python. Introduza os seguintes comandos para instalar o **Microsoft ODBC Driver para Linux** e **pyodbc**. O pyodbc utiliza o Microsoft ODBC Driver no Linux para se ligar a Bases de Dados SQL.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

<a id="windows" class="xliff"></a>

### **Windows**
Instale o [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) (atualize o controlador se solicitado). O pyodbc utiliza o Microsoft ODBC Driver no Linux para se ligar a Bases de Dados SQL. 

Em seguida, instale **pyodbc** através de pip.

```cmd
pip install pyodbc==3.1.1
```

As instruções para ativar a utilização de pip estão disponíveis [aqui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

<a id="get-connection-information" class="xliff"></a>

## Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem abaixo. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se esqueceu das informações de início de sessão do seu servidor, navegue para a página de servidor da Dase de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe.     
   
<a id="select-data" class="xliff"></a>

## Selecionar dados

Utilize o seguinte código para consultar os 20 melhores produtos por categoria ao utilizar uma função [pyodbc.connect](https://github.com/mkleehammer/pyodbc/wiki) com a instrução Transact SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). A função [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) é utilizada para recuperar um conjunto de resultados de uma consulta na Base de Dados SQL. Esta função aceita uma consulta e devolve um conjunto de resultados que pode ser iterado com a utilização de **cursor.fetchone()**. Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

<a id="insert-data" class="xliff"></a>

## Inserir dados
Utilize o seguinte código para inserir um novo produto na tabela SalesLT.Product com a função [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) e a instrução Transact SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

<a id="update-data" class="xliff"></a>

## Atualizar dados
Utilize o seguinte código para atualizar o produto novo que foi adicionado anteriormente com a função [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) e a instrução Transact SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

<a id="delete-data" class="xliff"></a>

## Eliminar dados
Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com a função [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) e a instrução Transact SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

<a id="next-steps" class="xliff"></a>

## Passos seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Controladores Python da Microsoft para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/?v=17.23h)


