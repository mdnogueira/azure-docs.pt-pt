---
title: "Ligar-se à Base de Dados SQL do Azure com o Ruby | Microsoft Docs"
description: "Apresenta um exemplo de código do Ruby que pode utilizar para se ligar e consultar a Base de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: b25ef8333a2836f976a974d6ea6e7fdcea2745e3
ms.contentlocale: pt-pt
ms.lasthandoff: 07/08/2017

---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Base de Dados SQL do Azure: utilizar o Ruby para se ligar e consultar dados

Este guia de início rápido demonstra como utilizar o [Ruby](https://www.ruby-lang.org) para se ligar a uma Base de Dados SQL do Azure; em seguida, utilize as instruções Transact SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas Windows, Mac OS e Ubuntu Linux.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-ruby-and-database-communication-libraries"></a>Instalar as bibliotecas de comunicação e a base de dados Ruby

Os passos nesta secção pressupõem que está familiarizado com a programação com Ruby e que nunca trabalhou com a Base de Dados SQL do Azure. Se não estiver familiarizado com a programação com Ruby, aceda a [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Criar uma aplicação com o SQL Server), selecione **Ruby** e, em seguida, selecione o seu sistema operativo.

### <a name="mac-os"></a>**Mac OS**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu script Ruby. Introduza os comandos seguintes para instalar **brew**, **FreeTDS** e **TinyTDS**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu script Ruby. Introduza os comandos seguintes para instalar **FreeTDS** e **TinyTDS**.

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="sql-server-connection-information"></a>Informações de ligação do servidor SQL

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem abaixo. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se esqueceu das informações de início de sessão do seu servidor, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe.
    

## <a name="select-data"></a>Selecionar dados
Utilize o seguinte código para consultar os 20 melhores produtos por categoria ao utilizar a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) com a instrução Transact SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). A função TinyTDS::Client aceita uma consulta e devolve um conjunto de resultados. O conjunto de resultados é iterado através de [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>Inserir dados
Utilize o seguinte código para inserir um novo produto na tabela SalesLT.Product ao utilizar a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) com a instrução Transact SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

Este exemplo demonstra como executar uma instrução INSERT com segurança, aprovar parâmetros que protegem a sua aplicação contra a vulnerabilidade de [injeção de SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e recuperar o valor da [chave primária](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints) gerado automaticamente.    
  
Para utilizar TinyTDS com o Azure, é recomendável que execute várias instruções `SET` para alterar o modo como a sessão atual controla informações específicas. As instruções `SET` recomendadas são fornecidas no exemplo de código. Por exemplo, `SET ANSI_NULL_DFLT_ON` permitirá novas colunas criadas para permitir valores nulos, mesmo se o estado da permissão de valores null da coluna não for explicitamente declarado.  
  
Para estar alinhado com o formato [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) do Microsoft SQL Server, utilize a função [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) para converter para o formato de data e hora correspondente.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>Atualizar dados
Utilize o seguinte código para atualizar o produto novo que foi adicionado anteriormente com a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) e a instrução Transact SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>Eliminar dados
Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) e a instrução Transact SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Substitua os parâmetros do servidor, da base de dados, do nome de utilizador e da palavra-passe pelos valores que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>Passos Seguintes
- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Repositório do GitHub para TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Comunicar problemas/fazer perguntas](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Controladores Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)


