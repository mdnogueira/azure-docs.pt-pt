---
title: "Ligar-se à Base de Dados do Azure para PostgreSQL a partir de Python | Microsoft Docs"
description: "Proporciona um código de exemplo de Python que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 07/07/2017
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 3cd090b02887857a68271f021e3580e05660d1dc
ms.contentlocale: pt-pt
ms.lasthandoff: 07/13/2017

---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>Base de Dados do Azure para PostgreSQL: utilizar o Python para se ligar e consultar dados
Este guia de início rápido explica como utilizar o [Python](https://python.org) para se ligar a uma Base de Dados do Azure para PostgreSQL; em seguida, utilize as instruções SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas Windows, Mac OS e Ubuntu Linux. Os passos neste artigo pressupõem que está familiarizado com a programação com Python e que nunca trabalhou com a Base de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um destes guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI](quickstart-create-server-database-azure-cli.md)

Também tem de ter:
- O [python](https://www.python.org/downloads/) instalado
- O pacote [pip](https://pip.pypa.io/en/stable/installing/) instalado (o pip já está instalado se estiver a utilizar o Python 2 > = 2.7.9 ou Python 3 > = binários 3.4 transferidos a partir de [python.org](https://python.org), mas tem de atualizar a versão do pip.)

## <a name="install-the-python-connection-libraries-for-postgresql"></a>Instalar as bibliotecas de ligação ao Python para PostgreSQL
Instale o pacote [psycopg2](http://initd.org/psycopg/docs/install.html) que permite ligar-se e consultar a base de dados. O psycopg2 está [disponível em PyPI](https://pypi.python.org/pypi/psycopg2/) sob a forma de pacotes de [roda](http://pythonwheels.com/) para as plataformas mais comuns (Linux, OSX, Windows), portanto, pode utilizar a instalação do pip para obter a versão binária do módulo, incluindo todas as dependências:

```cmd
pip install psycopg2
```
Confirme que utiliza uma versão atualizada do pip (pode atualizar a versão ao utilizar algo como `pip install -U pip`)

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e procure o servidor que acabou de criar **mypgserver-20170401**.
3. Clique no nome do servidor **mypgserver 20170401**.
4. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.
 ![Base de Dados do Azure para o PostgreSQL – Início de sessão de administrador do servidor](./media/connect-python/1-connection-string.png)
5. Caso se tenha esquecido das informações de início de sessão do seu servidor, navegue até à página **Descrição geral** para visualizar o nome de início de sessão de administrador do servidor e, se necessário, repor a palavra-passe.

## <a name="how-to-run-python-code"></a>Como executar o código do Python
- Com o seu editor de texto favorito, crie um novo ficheiro com o nome postgres.py e guarde-o numa pasta do projeto. Copie e cole um dos exemplos de código abaixo para o ficheiro de texto e guarde-o. Certifique-se de que seleciona a codificação UTF-8 ao guardar o ficheiro no SO do Windows. 
- Para executar o código, inicie a linha de comandos ou a shell de bash. Altere o diretório para a pasta do projeto, como `cd postgresql`. Em seguida, escreva o comando do Python seguido do nome de ficheiro, como `python postgresql.py`.

> [!NOTE]
> A partir da versão 3 do Python, poderá ser apresentado o erro `SyntaxError: Missing parentheses in call to 'print'` ao executar os blocos de código abaixo. Se isso acontecer, substitua cada chamada para o comando `print "string"` por uma chamada de função utilizando parênteses, como `print("string")`.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o código seguinte para se ligar e carregar os dados com a função [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) e a instrução SQL **INSERT**. A função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) é utilizada para executar a consulta SQL na base de dados PostgreSQL. Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="read-data"></a>Ler dados
Utilize o seguinte código para ler os dados inseridos com a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) e a instrução SQL **SELECT**. Esta função aceita uma consulta e devolve um conjunto de resultados que pode ser iterado com a utilização de [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Atualizar dados
Utilize o código a seguir para atualizar a linha de inventário que inseriu anteriormente com a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) e a instrução SQL **UPDATE**. Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Eliminar dados
Utilize o código a seguir para eliminar o item de inventário que inseriu anteriormente com a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) e a instrução SQL **DELETE**. Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)

