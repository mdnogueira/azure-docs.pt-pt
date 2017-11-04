---
title: "Ligar à Base de Dados do Azure para MySQL a partir de C# | Microsoft Docs"
description: "Este guia de introdução disponibiliza um código de exemplo de C# (.NET) que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL."
services: MySQL
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: MySQL
ms.custom: mvc
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: f87c3c302ec25f33af4334c3753dfae0084e4393
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-net-c-to-connect-and-query-data"></a>Base de Dados do Azure para MySQL: utilizar .NET (C#) para se ligar e consultar dados
Este guia de introdução demonstra como estabelecer ligação a uma base de dados do Azure para MySQL utilizando uma aplicação c#. Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este tópico assume que está familiarizado com o desenvolvimento com c# e que estiver a trabalhar com a base de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

Também tem de:
- Instalar o [.NET](https://www.microsoft.com/net/download). Siga os passos no artigo ligado para instalar o .NET especificamente para a sua plataforma (Windows, Ubuntu Linux ou macOS). 
- Instalar o [Visual Studio](https://www.visualstudio.com/downloads/).
- Instalar o [Controlador ODBC para MySQL](https://dev.mysql.com/downloads/connector/odbc/).

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu da esquerda no portal do Azure, clique em **todos os recursos**e, em seguida, procure o servidor que criou (tais como **myserver4demo**).
3. Clique no nome do servidor.
4. Selecione o servidor **propriedades** página e, em seguida, anote o **nome do servidor** e **nome de início de sessão de administração do servidor**.
 ![Nome do servidor da Base de Dados do Azure para o MySQL](./media/connect-csharp/1_server-properties-name-login.png)
5. Se se esquecer da sua informações de início de sessão do servidor, navegue para o **descrição geral** página para ver o nome de início de sessão de administração do servidor e, se necessário repor a palavra-passe.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para estabelecer ligação e carregar os dados utilizando **CREATE TABLE** e **INSERT INTO** instruções SQL. O código utiliza a classe ODBC com o método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), define a propriedade CommandText e chama o método [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) para executar os comandos da base de dados. 

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;

namespace driver
{
    class MySQLCreate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "DROP TABLE IF EXISTS inventory;";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished dropping table (if existed)");

            command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished creating table");

            command.CommandText =
                String.Format(
                    @"INSERT INTO inventory (name, quantity) VALUES ({0}, {1});
                    INSERT INTO inventory (name, quantity) VALUES ({2}, {3});
                    INSERT INTO inventory (name, quantity) VALUES ({4}, {5});",
                    "\'banana\'", 150,
                    "\'orange\'", 154,
                    "\'apple\'", 100
                    );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }

    }
}

```

## <a name="read-data"></a>Ler dados

Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **SELECIONE** instrução SQL. O código utiliza a classe ODBC com o método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para estabelecer uma ligação ao MySQL. O código utiliza então o método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) e o método [ExecuteReader ()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executereader(v=vs.110).aspx) para executar os comandos da base de dados. Em seguida, o código utiliza [Read()](https://msdn.microsoft.com/library/system.data.odbc.odbcdatareader.read(v=vs.110).aspx) para avançar para os registos nos resultados. Em seguida, o código utiliza GetInt32 e GetString para analisar os valores do registo.

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;


namespace driver
{
    class MySQLRead
    {

        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "SELECT * FROM inventory;";

            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(
                    string.Format(
                        "Reading from table=({0}, {1}, {2})",
                        reader.GetInt32(0).ToString(),
                        reader.GetString(1),
                        reader.GetInt32(2).ToString()
                        )
                    );
            }

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

## <a name="update-data"></a>Atualizar dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **ATUALIZAÇÃO** instrução SQL. O código utiliza a classe ODBC com o método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), define a propriedade CommandText e chama o método [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) para executar os comandos da base de dados.

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLUpdate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("UPDATE inventory SET quantity = {0} WHERE name = {1};",
                200,
                "\'banana\'"
                );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}



```


## <a name="delete-data"></a>Eliminar dados
Utilize o seguinte código para estabelecer ligação e eliminar os dados utilizando um **eliminar** instrução SQL. 

O código utiliza a classe ODBC com o método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), define a propriedade CommandText e chama o método [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) para executar os comandos da base de dados.

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLDelete
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
                String.Format("DELETE FROM inventory WHERE name = {0};",
                    "\'orange\'");
            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}

```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a sua base de dados MySQL para a Dase de Dados do Azure para MySQL através da funcionalidade de captura e restauro](concepts-migrate-dump-restore.md)
