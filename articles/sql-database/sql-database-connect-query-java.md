---
title: "Ligar-se à Base de Dados SQL do Azure com Java | Microsoft Docs"
description: "Apresenta um exemplo de código Java que pode utilizar para se ligar e consultar a Base de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 0df7bd56d3aac462a86e31b5512e48371918bbf2
ms.contentlocale: pt-pt
ms.lasthandoff: 07/04/2017


---
<a id="azure-sql-database-use-java-to-connect-and-query-data" class="xliff"></a>

# Base de Dados SQL do Azure: utilizar o Java para se ligar e consultar dados

Este guia de início rápido explica como utilizar o [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) para se ligar a uma Base de Dados SQL do Azure; em seguida, utilize as instruções Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas Windows, Mac OS e Ubuntu Linux.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)

<a id="install-java-software" class="xliff"></a>

## Instalar o software de Java

Os passos nesta secção assumem que está familiarizado com programação com Java e que nunca trabalhou com a Base de Dados SQL do Azure. Se não estiver familiarizado com a programação com Java, aceda a [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Criar uma aplicação com o SQL Server), selecione **Java** e, em seguida, selecione o seu sistema operativo.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu projeto Java. Instale **brew** e **Maven** ao introduzir os seguintes comandos: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu projeto Java. Instale **Maven** ao introduzir os seguintes comandos:

```bash
sudo apt-get install maven
```

<a id="windows" class="xliff"></a>

### **Windows**
Instale [Maven](https://maven.apache.org/download.cgi) ao utilizar o instalador oficial. Utilize Maven para ajudar a gerir as dependências, criar, testar e executar o seu projeto Java. 

<a id="get-connection-information" class="xliff"></a>

## Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem abaixo. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se esqueceu das informações de início de sessão do seu servidor, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe.
5. Clique em **Mostrar cadeias de ligação da base de dados**.

6. Reveja a cadeia de ligação **JDBC** completa.

    ![Cadeia de ligação JDBC](./media/sql-database-connect-query-jdbc/jdbc-connection-string.png)   

<a id="create-maven-project" class="xliff"></a>

### **Criar projeto Maven**
A partir do terminal, crie um novo projeto Maven. 
```bash
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

Adicione o **controlador Microsoft JDBC para SQL Server** às dependências em ***pom.xml***. 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.2.0.jre8</version>
</dependency>
```

<a id="select-data" class="xliff"></a>

## Selecionar dados

Utilize o seguinte código para consultar os 20 melhores produtos por categoria ao utilizar a classe de [ligação](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection) com a instrução Transact SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Substitua os parâmetros do servidor, da base de dados, do utilizador e da palavra-passe que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.Statement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                try (Statement statement = connection.createStatement();
                    ResultSet resultSet = statement.executeQuery(selectSql)) {

                        // Print results from select statement
                        System.out.println("Top 20 categories:");
                        while (resultSet.next())
                        {
                            System.out.println(resultSet.getString(1) + " "
                                + resultSet.getString(2));
                        }
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

<a id="insert-data" class="xliff"></a>

## Inserir dados

Utilize o seguinte código para inserir um novo produto na tabela SalesLT.Product com a classe [Instruções preparadas](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) e a instrução Transact SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Substitua os parâmetros do servidor, da base de dados, do utilizador e da palavra-passe que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, " 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                try (PreparedStatement prep = connection.prepareStatement(insertSql)) {
                        prep.setString(1, "BrandNewProduct");
                        prep.setInt(2, 200989);
                        prep.setString(3, "Blue");
                        prep.setDouble(4, 75);
                        prep.setDouble(5, 80);
                        prep.setTimestamp(6, sqlTimeStamp);

                        int count = prep.executeUpdate();
                        System.out.println("Inserted: " + count + " row(s)");
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
<a id="update-data" class="xliff"></a>

## Atualizar dados

Utilize o seguinte código para atualizar o produto novo que foi adicionado anteriormente com a classe [Instruções preparadas](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) e a instrução Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) para atualizar os dados na sua Base de Dados SQL do Azure. Substitua os parâmetros do servidor, da base de dados, do utilizador e da palavra-passe que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(updateSql)) {
                        prep.setString(1, "500");
                        prep.setString(2, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Updated: " + count + " row(s)")
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



<a id="delete-data" class="xliff"></a>

## Eliminar dados

Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com a classe [Instruções preparadas](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) e a instrução Transact SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Substitua os parâmetros do servidor, da base de dados, do utilizador e da palavra-passe que especificou ao criar a base de dados com os dados de exemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(deleteSql)) {
                        prep.setString(1, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Deleted: " + count + " row(s)");
                }
        }       
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

<a id="next-steps" class="xliff"></a>

## Passos seguintes
- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Controlador Microsoft JDBC para SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Comunicar problemas/fazer perguntas](https://github.com/microsoft/mssql-jdbc/issues)


