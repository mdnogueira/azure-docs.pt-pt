---
title: "Ligar à Base de Dados do Azure para PostgreSQL com a linguagem Go | Microsoft Docs"
description: "Este guia de início rápido fornece um exemplo de linguagem de programação Go que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: pt-pt
ms.lasthandoff: 07/04/2017

---

<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>

# Base de Dados do Azure para PostgreSQL: utilize a linguagem Go para ligar e consultar dados
Este início rápido explica como se pode ligar a uma Base de Dados do Azure para PostgreSQL através de código escrito na linguagem [Go](https://golang.org/). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este artigo pressupõe que esteja familiarizado com o desenvolvimento que utiliza o Go, mas que nunca trabalhou com a base de dados do Azure para PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos
Este início rápido utiliza os recursos criados em qualquer um desses guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI do Azure](quickstart-create-server-database-azure-cli.md)

<a id="install-go-and-pq" class="xliff"></a>

## Instalar Go e pq
- Transfira e instale o Go em conformidade com as [instruções de instalação](https://golang.org/doc/install) da sua plataforma.
- Crie uma pasta para o seu projeto, como C:\Postgresql\. Através da linha de comandos, altere o diretório para a pasta do projeto, como `cd C:\Postgres\`.
- Transfira o [controlador Pure Go Postgres (pq)](https://github.com/lib/pq) para a pasta do projeto ao escrever o comando `go get github.com/lib/pq` enquanto estiver no mesmo diretório.

<a id="build-and-run-go-code" class="xliff"></a>

## Criar e executar código Go 
- Guarde o código num ficheiro de texto com a extensão *.go e guarde-o na pasta do projeto, como `C:\postgres\read.go`.
- Para executar o código, altere o diretório para a pasta do projeto `cd postgres` e, em seguida, escreva o comando `go run read.go` para compilar a aplicação e executá-la.
- Para criar o código numa aplicação nativa, `go build read.go` e, em seguida, inicie read.exe para executar a aplicação.

<a id="get-connection-information" class="xliff"></a>

## Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Precisa das credenciais de nome de servidor totalmente qualificado e de início de sessão.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e procure o servidor que acabou de criar, por exemplo, **mypgserver-20170401**.
3. Clique no nome do servidor **mypgserver 20170401**.
4. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.
 ![Base de Dados do Azure para o PostgreSQL – Início de Sessão de Administrador do Servidor](./media/connect-go/1-connection-string.png)
5. Caso se tenha esquecido das informações de início de sessão do seu servidor, navegue até à página **Descrição geral** e visualize o nome de início de sessão de administrador do Servidor. Se necessário, reponha a palavra-passe.

<a id="connect-and-create-a-table" class="xliff"></a>

## Ligar e criar uma tabela
Utilize o código seguinte para ligar e criar uma tabela com a instrução SQL **CREATE TABLE**, seguida das instruções SQL **INSERT INTO** para adicionar linhas à tabela.

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor Postgres e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para ligar à Base de Dados do Azure para PostgreSQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método[Exec()](https://golang.org/pkg/database/sql/#DB.Exec) várias vezes para executar vários comandos SQL. Sempre que seja chamado um método checkError() personalizado para verificar se ocorreu um erro e pânico para sair se ocorrer um erro.


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

<a id="read-data" class="xliff"></a>

## Ler dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor Postgres e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para ligar à Base de Dados do Azure para PostgreSQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. A consulta select é executada ao chamar o método [db.Query()](https://golang.org/pkg/database/sql/#DB.Query) e as linhas resultantes são mantidas numa variável do tipo [rows](https://golang.org/pkg/database/sql/#Rows). O código lê os valores de dados de coluna na linha atual através do método [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) e cria ciclos nas linhas através do iterador [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) até não existirem mais linhas. Os valores de coluna de cada linha são impressos na consola. Sempre que seja chamado um método checkError() personalizado para verificar se ocorreu um erro e pânico para sair se ocorrer um erro.

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

<a id="update-data" class="xliff"></a>

## Atualizar dados
Utilize o código seguinte para se ligar e atualizar os dados com uma instrução SQL **UPDATE**.

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor Postgres e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para ligar à Base de Dados do Azure para PostgreSQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar a instrução SQL que atualiza a tabela. Método checkError() personalizado para verificar se ocorreu um erro e pânico para sair se ocorrer um erro.

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

<a id="delete-data" class="xliff"></a>

## Eliminar dados
Utilize o código seguinte para se ligar e ler os dados com uma instrução SQL **DELETE**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como um controlador para comunicar com o servidor Postgres e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para ligar à Base de Dados do Azure para PostgreSQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar a instrução SQL que atualiza a tabela. Método checkError() personalizado para verificar se ocorreu um erro e pânico para sair se ocorrer um erro.

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

<a id="next-steps" class="xliff"></a>

## Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com as opções Exportar e Importar](./howto-migrate-using-export-and-import.md)

