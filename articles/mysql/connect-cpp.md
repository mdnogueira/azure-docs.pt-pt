---
title: "Ligar à Base de Dados do Azure para MySQL a partir de C++ | Microsoft Docs"
description: "Este guia de início rápido disponibiliza um código de exemplo de C++ que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL."
services: mysql
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: C++
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 92620c8081b1f0f5c96cc3ae09465b3526e74042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-connectorc-to-connect-and-query-data"></a>Base de Dados do Azure para MySQL: utilizar o Connector/C++ para se ligar e consultar dados
Este guia de introdução demonstra como estabelecer ligação a uma base de dados do Azure para MySQL utilizando uma aplicação de C++. Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este tópico assume que está familiarizado com o desenvolvimento utilizando C++ e que estiver a trabalhar com a base de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de introdução utiliza os recursos criados dos seguintes guias como um ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

Também tem de:
- Instalar o [.NET Framework](https://www.microsoft.com/net/download)
- Instalar o [Visual Studio](https://www.visualstudio.com/downloads/)
- Instalar o [MySQL Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Instalar o [Boost](http://www.boost.org/)

## <a name="install-visual-studio-and-net"></a>Instalar o Visual Studio e .NET
Os passos nesta secção partem do princípio que está familiarizado com a programação com .NET.

### <a name="windows"></a>**Windows**
- Instale o Visual Studio 2017 Community, que é um completo em destaque, extensível, livre IDE para a criação de aplicações modernas para Android, iOS, Windows, bem como web e aplicações de base de dados e serviços em nuvem. Pode instalar o .NET Framework completo ou apenas .NET Core: os fragmentos de código no início rápido funcionam com qualquer um. Se já tiver o Visual Studio instalado no seu computador, ignore as dois passos seguintes.
   1. Transferir o [instalador do Visual Studio 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Execute o instalador e siga as instruções de instalação, para concluí-la.

### <a name="configure-visual-studio"></a>**Configurar o Visual Studio**
1. No Visual Studio, project property > configuration properties > C/C++ > linker > general > additional library directories, adicione o diretório lib\opt (ou seja: C:\Programas (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt) do conector C++.
2. Propriedade do projeto do Visual Studio, > propriedades de configuração > C/C++ > geral > adicionais incluir diretórios:
   - Adicionar incluem / diretório de c + + conector (ou seja,: C:\Program Files (x86) \MySQL\MySQL conector C++ 1.1.9\include\).
   - Adicione o diretório de raiz da biblioteca de aumento (ou seja,: C:\boost_1_64_0\).
3. Propriedade do projeto do Visual Studio, > propriedades de configuração > C/C++ > linker > entrada > dependências adicionais, adicione mysqlcppconn.lib para o campo de texto.
4. Copiar mysqlcppconn.dll da pasta de biblioteca do conector C++ no passo 3 para o mesmo diretório que o executável da aplicação ou adicione-o para a variável de ambiente para a aplicação possa encontrar.

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu da esquerda no portal do Azure, clique em **todos os recursos**e a pesquisa para o servidor que criou (tais como **myserver4demo**).
3. Clique no nome do servidor.
4. Selecione o servidor **propriedades** página e, em seguida, anote o **nome do servidor** e **nome de início de sessão de administração do servidor**.
 ![Nome do servidor da Base de Dados do Azure para o MySQL](./media/connect-cpp/1_server-properties-name-login.png)
5. Se se esquecer da sua informações de início de sessão do servidor, navegue para o **descrição geral** página para ver o nome de início de sessão de administração do servidor e, se necessário repor a palavra-passe.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para estabelecer ligação e carregar os dados utilizando **CREATE TABLE** e **INSERT INTO** instruções SQL. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método createStatement() e o método execute() para executar os comandos da base de dados. 

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    stmt = con>createStatement();
    stmt>execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt>execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con>prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt>setString(1, "banana");
    pstmt>setInt(2, 150);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "orange");
    pstmt>setInt(2, 154);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "apple");
    pstmt>setInt(2, 100);
    pstmt>execute();
    cout << "One row inserted." << endl;
    
    delete pstmt;   
    delete con;
    system("pause");
    return 0;

```

## <a name="read-data"></a>Ler dados

Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **SELECIONE** instrução SQL. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método prepareStatement() e executeQuery() para executar os comandos select. Em seguida, o código utiliza next() para avançar para os registos nos resultados. Por fim, o código utiliza getInt() e GetString () para analisar os valores no registo.

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

//  select  
    pstmt = con>prepareStatement("SELECT * FROM inventory;");
    result = pstmt>executeQuery();  
    
    while (result>next())
        printf("Reading from table=(%d, %s, %d)\n", result>getInt(1), result>getString(2).c_str(), result>getInt(3));   
    
    delete result;
    delete pstmt;   
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Atualizar dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **ATUALIZAÇÃO** instrução SQL. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método prepareStatement() e executeQuery() para executar os comandos update. 

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

    //update
    pstmt = con>prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt>setInt(1, 200);
    pstmt>setString(2, "banana");
    pstmt>executeQuery();
    printf("Row updated\n");
    
    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Eliminar dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **eliminar** instrução SQL. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método prepareStatement() e executeQuery() para executar os comandos delete.

Substitua os parâmetros Anfitrião, DBName, Utilizador e Palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
        
    //delete
    pstmt = con>prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt>setString(1, "orange");
    result = pstmt>executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a sua base de dados MySQL para a Dase de Dados do Azure para MySQL através da funcionalidade de captura e restauro](concepts-migrate-dump-restore.md)
