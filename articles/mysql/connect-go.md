---
title: "Ligar à Base de Dados do Azure para MySQL através de Go | Microsoft Docs"
description: "Este início rápido proporciona vários exemplos de código Go que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 1f18a35a3c22ecdc379bdffa1ecacb931c62a59d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-go-language-to-connect-and-query-data"></a>Base de Dados do Azure para MySQL: utilize a linguagem Go para ligar e consultar dados
Este guia de introdução demonstra como estabelecer uma ligação para uma base de dados do Azure para MySQL do Windows, Ubuntu Linux e Apple plataformas macOS através da utilização de código escrito o [aceda](https://golang.org/) idioma. Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este tópico assume que está familiarizado com o desenvolvimento, aceda a utilizar e que estiver a trabalhar com a base de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-go-and-mysql-connector"></a>Instalar o Go e o conector MySQL
Instalar [aceda](https://golang.org/doc/install) e [aceda-sql-controlador MySQL](https://github.com/go-sql-driver/mysql#installation) no seu computador. Dependendo da sua plataforma, siga os passos na secção adequada:

### <a name="windows"></a>Windows
1. [Transfira](https://golang.org/dl/) e instale o Go para o Microsoft Windows, de acordo com as [instruções de instalação](https://golang.org/doc/install).
2. Inicie a linha de comandos a partir do menu Iniciar.
3. Crie uma pasta para o seu projeto, como `mkdir  %USERPROFILE%\go\src\mysqlgo`.
4. Altere o diretório para a pasta do projeto, como `cd %USERPROFILE%\go\src\mysqlgo`.
5. Defina a variável de ambiente de GOPATH para apontar para o diretório do código de origem. `set GOPATH=%USERPROFILE%\go`.
6. Execute o comando `go get github.com/go-sql-driver/mysql` para instalar o [go-sql-driver para mysql](https://github.com/go-sql-driver/mysql#installation).

   Para resumir, instale o Go e, em seguida, execute estes comandos na linha de comandos:
   ```cmd
   mkdir  %USERPROFILE%\go\src\mysqlgo
   cd %USERPROFILE%\go\src\mysqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/go-sql-driver/mysql
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Inicie a shell de Bash. 
2. Execute `sudo apt-get install golang-go` para instalar o Go.
3. Crie uma pasta para o projeto no seu diretório raiz, como `mkdir -p ~/go/src/mysqlgo/`.
4. Altere o diretório para a pasta, como `cd ~/go/src/mysqlgo/`.
5. Defina a variável de ambiente GOPATH para apontar para um diretório de origem válido, como a pasta do go do diretório raiz atual. A shell de deteção, execute `export GOPATH=~/go` para adicionar o diretório acedo como o GOPATH para a sessão atual do shell.
6. Execute o comando `go get github.com/go-sql-driver/mysql` para instalar o [go-sql-driver para mysql](https://github.com/go-sql-driver/mysql#installation).

   Em resumo, execute estes comandos do bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

### <a name="apple-macos"></a>Apple macOS
1. Transfira e instale aceda de acordo com o [instruções de instalação](https://golang.org/doc/install) correspondentes à sua plataforma. 
2. Inicie a shell de Bash.
3. Crie uma pasta para o projeto no seu diretório raiz, como `mkdir -p ~/go/src/mysqlgo/`.
4. Altere o diretório para a pasta, como `cd ~/go/src/mysqlgo/`.
5. Defina a variável de ambiente GOPATH para apontar para um diretório de origem válido, como a pasta do go do diretório raiz atual. A shell de deteção, execute `export GOPATH=~/go` para adicionar o diretório acedo como o GOPATH para a sessão atual do shell.
6. Execute o comando `go get github.com/go-sql-driver/mysql` para instalar o [go-sql-driver para mysql](https://github.com/go-sql-driver/mysql#installation).

   Em resumo, instale o Go e execute estes comandos do bash:
   ```bash
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu da esquerda no portal do Azure, clique em **todos os recursos** e, em seguida, procure o servidor ter creased (tais como **myserver4demo**).
3. Clique no nome do servidor, **myserver4demo**.
4. Selecione o servidor **propriedades** página e, em seguida, anote o **nome do servidor** e **nome de início de sessão de administração do servidor**.
 ![Base de Dados do Azure para o MySQL – Início de sessão de administrador do servidor](./media/connect-go/1_server-properties-name-login.png)
5. Se se esquecer da sua informações de início de sessão do servidor, navegue para o **descrição geral** página para ver o nome de início de sessão de administração do servidor e, se necessário repor a palavra-passe.
   

## <a name="build-and-run-go-code"></a>Criar e executar código Go 
1. Para escrever o código Golang, pode utilizar um editor de textos simples, como o Blobo de Notas no Microsoft Windows, o [vi](http://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) ou o [Nano](https://www.nano-editor.org/) no Ubuntu ou o TextEdit em macOS. Se preferir um mais rico interativa desenvolvimento Ambiente IDE (), tente [Gogland](https://www.jetbrains.com/go/) por Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) pela Microsoft, ou [Atom](https://atom.io/).
2. Cole o código de aceda as secções abaixo para ficheiros de texto e, em seguida, guarde-los numa pasta do projeto com a extensão de ficheiro \*.go (por exemplo, o caminho do Windows `%USERPROFILE%\go\src\mysqlgo\createtable.go` ou caminho de Linux `~/go/src/mysqlgo/createtable.go`).
3. Localize o `HOST`, `DATABASE`, `USER`, e `PASSWORD` constantes no código e, em seguida, substitua os valores de exemplo com os seus próprios valores. 
4. Inicie a linha de comandos ou a shell de deteção. Altere o diretório para a pasta do projeto . Por exemplo, no Windows `cd %USERPROFILE%\go\src\mysqlgo\`. No Linux, `cd ~/go/src/mysqlgo/`.  Alguns dos editores de IDE mencionados oferecem capacidades de depuração e runtime sem que sejam necessários comandos da shell.
5. Execute o código com o comando `go run createtable.go`, para compilar a aplicação e executá-la. 
6. Em alternativa, para criar o código numa aplicação nativa, `go build createtable.go`, e inicie `createtable.exe` para executar a aplicação.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para ligar ao servidor, crie uma tabela e carregar os dados utilizando um **inserir** instrução SQL. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql. Open ()](http://go-database-sql.org/accessing.html) ligar à base de dados do Azure para o MySQL e verifica a ligação utilizando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método[Exec()](https://golang.org/pkg/database/sql/#DB.Exec) várias vezes para executar vários comandos DDL. O código também utiliza [Prepare()](http://go-database-sql.org/prepared.html) e Exec() para executar instruções preparadas com diferentes parâmetros para inserir três linhas. Sempre que for utilizado um método checkError() personalizado para verificar se Ocorreu um erro e panic para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed).")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table.")

    // Insert some data into table.
    sqlStatement, err := db.Prepare("INSERT INTO inventory (name, quantity) VALUES (?, ?);")
    res, err := sqlStatement.Exec("banana", 150)
    checkError(err)
    rowCount, err := res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("orange", 154)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("apple", 100)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}

```

## <a name="read-data"></a>Ler dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **SELECIONE** instrução SQL. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://go-database-sql.org/accessing.html) para ligar à Base de Dados do Azure para MySQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Query()](https://golang.org/pkg/database/sql/#DB.Query) para executar o comando de seleção. Em seguida, executa[Next()](https://golang.org/pkg/database/sql/#Rows.Next) para iterar o conjunto de resultados e [Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) para analisar os valores das colunas, guardando os valores em variáveis. De cada vez, é utilizado um método checkError() personalizado para verificar se ocorreu um erro e se deve ser chamado o método “panic”, para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Variables for printing column data when scanned.
    var (
        id       int
        name     string
        quantity int
    )

    // Read some data from the table.
    rows, err := db.Query("SELECT id, name, quantity from inventory;")
    checkError(err)
    defer rows.Close()
    fmt.Println("Reading data:")
    for rows.Next() {
        err := rows.Scan(&id, &name, &quantity)
        checkError(err)
        fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
    }
    err = rows.Err()
    checkError(err)
    fmt.Println("Done.")
}
```

## <a name="update-data"></a>Atualizar dados
Utilize o código seguinte para se ligar e atualizar os dados com a instrução SQL **UPDATE**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://go-database-sql.org/accessing.html) para ligar à Base de Dados do Azure para MySQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar o comando de atualização. De cada vez, é utilizado um método checkError() personalizado para verificar se ocorreu um erro e se deve ser chamado o método “panic”, para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("UPDATE inventory SET quantity = ? WHERE name = ?", 200, "banana")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e remover os dados com uma instrução SQL **DELETE**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://go-database-sql.org/accessing.html) para ligar à Base de Dados do Azure para MySQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar o comando de eliminação. De cada vez, é utilizado um método checkError() personalizado para verificar se ocorreu um erro e se deve ser chamado o método “panic”, para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("DELETE FROM inventory WHERE name = ?", "orange")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./concepts-migrate-import-export.md)
