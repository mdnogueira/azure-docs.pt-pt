---
title: "Ligar à Base de Dados do Azure para PostgreSQL com a linguagem Go | Microsoft Docs"
description: "Este guia de início rápido fornece um exemplo de linguagem de programação Go que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 11/03/2017
ms.openlocfilehash: 8b52aeaadf7ba94d6b79ef447600cd7b57e70dfa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-postgresql-use-go-language-to-connect-and-query-data"></a>Base de Dados do Azure para PostgreSQL: utilize a linguagem Go para ligar e consultar dados
Este início rápido explica como se pode ligar a uma Base de Dados do Azure para PostgreSQL através de código escrito na linguagem [Go](https://golang.org/) (golang). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados na base de dados. Este artigo pressupõe que esteja familiarizado com o desenvolvimento que utiliza o Go, mas que nunca trabalhou com a base de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um destes guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI do Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Instalar o Go e o conector pq
Instale o [Go](https://golang.org/doc/install) e o [controlador Pure Go Postgres (pq)](https://github.com/lib/pq) no seu computador. Dependendo da sua plataforma, siga os passos adequados de:

### <a name="windows"></a>Windows
1. [Transfira](https://golang.org/dl/) e instale o Go para o Microsoft Windows, de acordo com as [instruções de instalação](https://golang.org/doc/install).
2. Inicie a linha de comandos a partir do menu Iniciar.
3. Crie uma pasta para o seu projeto, tais como `mkdir  %USERPROFILE%\go\src\postgresqlgo`.
4. Altere o diretório para a pasta do projeto, como `cd %USERPROFILE%\go\src\postgresqlgo`.
5. Defina a variável de ambiente de GOPATH para apontar para o diretório do código de origem. `set GOPATH=%USERPROFILE%\go`.
6. Execute o comando `go get github.com/lib/pq` para instalar o [controlador Pure Go Postgres (pq)](https://github.com/lib/pq).

   Para resumir, instale o Go e, em seguida, execute estes comandos na linha de comandos:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Inicie a shell de Bash. 
2. Execute `sudo apt-get install golang-go` para instalar o Go.
3. Crie uma pasta para o projeto no seu diretório raiz, como `mkdir -p ~/go/src/postgresqlgo/`.
4. Altere o diretório para a pasta, como `cd ~/go/src/postgresqlgo/`.
5. Defina a variável de ambiente GOPATH para apontar para um diretório de origem válido, como a pasta do go do diretório raiz atual. Na shell do bash, execute `export GOPATH=~/go` para adicionar o diretório do go como o GOPATH da sessão de shell atual.
6. Execute o comando `go get github.com/lib/pq` para instalar o [controlador Pure Go Postgres (pq)](https://github.com/lib/pq).

   Em resumo, execute estes comandos do bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Transfira e instale o Go em conformidade com as [instruções de instalação](https://golang.org/doc/install) da sua plataforma. 
2. Inicie a shell de Bash. 
3. Crie uma pasta para o projeto no seu diretório raiz, como `mkdir -p ~/go/src/postgresqlgo/`.
4. Altere o diretório para a pasta, como `cd ~/go/src/postgresqlgo/`.
5. Defina a variável de ambiente GOPATH para apontar para um diretório de origem válido, como a pasta do go do diretório raiz atual. Na shell do bash, execute `export GOPATH=~/go` para adicionar o diretório do go como o GOPATH da sessão de shell atual.
6. Execute o comando `go get github.com/lib/pq` para instalar o [controlador Pure Go Postgres (pq)](https://github.com/lib/pq).

   Em resumo, instale o Go e execute estes comandos do bash:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e procure o servidor que acabou de criar, por exemplo, **mypgserver-20170401**.
3. Clique no nome do servidor **mypgserver 20170401**.
4. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.
 ![Base de Dados do Azure para o PostgreSQL – Início de Sessão de Administrador do Servidor](./media/connect-go/1-connection-string.png)
5. Caso se tenha esquecido das informações de início de sessão do seu servidor, navegue até à página **Descrição geral** e visualize o nome de início de sessão de administrador do Servidor. Se necessário, reponha a palavra-passe.

## <a name="build-and-run-go-code"></a>Criar e executar código Go 
1. Escrever código Golang, pode utilizar um editor de texto simples, como o bloco de notas no Microsoft Windows, [vi](http://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) ou [Nano](https://www.nano-editor.org/) no Ubuntu ou TextEdit no macOS. Se preferir um Ambiente de Desenvolvimento Interativo (IDE) rico, experimente o [Gogland](https://www.jetbrains.com/go/), da Jetbrains, o [Visual Studio Code](https://code.visualstudio.com/), da Microsoft, ou o [Atom](https://atom.io/).
2. Cole o código de Golang as secções seguintes ficheiros de texto e guarde no pasta do projeto com a extensão de ficheiro \*.go, tais como o caminho do Windows `%USERPROFILE%\go\src\postgresqlgo\createtable.go` ou caminho de Linux `~/go/src/postgresqlgo/createtable.go`.
3. Localize as constantes `HOST`, `DATABASE`, `USER` e `PASSWORD` no código e substitua os valores de exemplo pelos seus próprios valores.  
4. Inicie a linha de comandos ou a shell de bash. Altere o diretório para a pasta do projeto . Por exemplo, no Windows `cd %USERPROFILE%\go\src\postgresqlgo\`. No Linux, `cd ~/go/src/postgresqlgo/`. Alguns dos ambientes IDE mencionados oferecem capacidades de depuração e runtime sem que sejam necessários comandos da shell.
5. Execute o código com o comando `go run createtable.go`, para compilar a aplicação e executá-la. 
6. Em alternativa, para criar o código numa aplicação nativa, `go build createtable.go`, e inicie `createtable.exe` para executar a aplicação.

## <a name="connect-and-create-a-table"></a>Ligar-se e criar uma tabela
Utilize o código seguinte para ligar e criar uma tabela com a instrução SQL **CREATE TABLE**, seguida das instruções SQL **INSERT INTO** para adicionar linhas à tabela.

O código importa três pacotes: o [pacote SQL Server](https://golang.org/pkg/database/sql/), a [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor de PostgreSQL e o [fmt pacote](https://golang.org/pkg/fmt/) para a entrada impressos e saída na linha de comandos.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para ligar à base de dados do Azure para a base de dados PostgreSQL e verifica a ligação utilizando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método[Exec()](https://golang.org/pkg/database/sql/#DB.Exec) várias vezes para executar vários comandos SQL. Sempre que um método personalizado checkError() verifica se Ocorreu um erro e panic para sair se ocorrer um erro.

Substitua os parâmetros `HOST`, `DATABASE`, `USER` e `PASSWORD` pelos seus próprios valores. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Ler dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

O código importa três pacotes: o [pacote SQL Server](https://golang.org/pkg/database/sql/), a [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor de PostgreSQL e o [fmt pacote](https://golang.org/pkg/fmt/) para a entrada impressos e saída na linha de comandos.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para ligar à base de dados do Azure para a base de dados PostgreSQL e verifica a ligação utilizando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. A consulta select é executada chamando o método [db. Query ()](https://golang.org/pkg/database/sql/#DB.Query), e as linhas resultantes são mantidas numa variável do tipo [linhas](https://golang.org/pkg/database/sql/#Rows). O código lê os valores de dados de coluna na linha atual através do método [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) e cria ciclos nas linhas através do iterador [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) até não existirem mais linhas. Os valores de coluna de cada linha são impressos na consola. Sempre que um método checkError() personalizada é utilizado para verificar se Ocorreu um erro e panic para sair se ocorrer um erro.

Substitua os parâmetros `HOST`, `DATABASE`, `USER` e `PASSWORD` pelos seus próprios valores. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Atualizar dados
Utilize o seguinte código para estabelecer ligação e atualizar os dados utilizando um **ATUALIZAR** instrução SQL.

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor Postgres e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para ligar à base de dados do Azure para a base de dados PostgreSQL e verifica a ligação utilizando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar a instrução SQL que atualiza a tabela. Um método checkError() personalizada é utilizado para verificar se Ocorreu um erro e panic para sair se ocorrer um erro.

Substitua os parâmetros `HOST`, `DATABASE`, `USER` e `PASSWORD` pelos seus próprios valores. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e eliminar os dados com a instrução SQL **DELETE**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor Postgres e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para ligar à base de dados do Azure para a base de dados PostgreSQL e verifica a ligação utilizando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. As chamadas de código a [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) método a executar a instrução de SQL que elimina uma linha da tabela. Um método checkError() personalizada é utilizado para verificar se Ocorreu um erro e panic para sair se ocorrer um erro.

Substitua os parâmetros `HOST`, `DATABASE`, `USER` e `PASSWORD` pelos seus próprios valores. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
