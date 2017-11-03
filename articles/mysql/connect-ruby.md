---
title: "Ligar-se à Base de Dados do Azure para MySQL através de Ruby | Microsoft Docs"
description: "Este guia de início rápido proporciona vários exemplos de código Ruby que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 10f774262015cb19e158a687138b4618ce50063b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-ruby-to-connect-and-query-data"></a>Base de Dados do Azure para MySQL: utilizar Ruby para se ligar e consultar dados
Este guia de início rápido explica como se pode ligar a uma Base de Dados do Azure para MySQL através de uma aplicação [Ruby](https://www.ruby-lang.org) e o gem [mysql2](https://rubygems.org/gems/mysql2) a partir de plataformas Windows, Ubuntu Linux e Mac. Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este tópico assume que está familiarizado com o desenvolvimento com Ruby e que estiver a trabalhar com a base de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-ruby"></a>Instalar o Ruby
Instale Ruby, Gem e a biblioteca de MySQL2 no seu computador. 

### <a name="windows"></a>Windows
1. Transfira e instale a versão 2.3 do [Ruby](http://rubyinstaller.org/downloads/).
2. Inicie uma nova linha de comandos (cmd) a partir do menu Iniciar.
3. Altere o diretório para o diretório do Ruby para a versão 2.3. `cd c:\Ruby23-x64\bin`
4. Teste a instalação do Ruby ao executar o comando `ruby -v` para ver a versão instalada.
5. Teste a instalação do Gem ao executar o comando `gem -v` para ver a versão instalada.
6. Crie o módulo Mysql2 para Ruby com Gem ao executar o comando `gem install mysql2`.

### <a name="macos"></a>MacOS
1. Instale o Ruby com Homebrew ao executar o comando `brew install ruby`. Para obter mais opções de instalação, veja a [documentação de instalação](https://www.ruby-lang.org/en/documentation/installation/#homebrew) do Ruby.
2. Teste a instalação do Ruby ao executar o comando `ruby -v` para ver a versão instalada.
3. Teste a instalação do Gem ao executar o comando `gem -v` para ver a versão instalada.
4. Crie o módulo Mysql2 para Ruby com Gem ao executar o comando `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Instale o Ruby ao executar o comando `sudo apt-get install ruby-full`. Para obter mais opções de instalação, veja a [documentação de instalação](https://www.ruby-lang.org/en/documentation/installation/) do Ruby.
2. Teste a instalação do Ruby ao executar o comando `ruby -v` para ver a versão instalada.
3. Instale as atualizações mais recentes para Gem ao executar o comando `sudo gem update --system`.
4. Teste a instalação do Gem ao executar o comando `gem -v` para ver a versão instalada.
5. Instale o gcc, make e outras ferramentas de compilação ao executar o comando `sudo apt-get install build-essential`.
6. Instale as bibliotecas de programador de cliente MySQL ao executar o comando `sudo apt-get install libmysqlclient-dev`.
7. Crie o módulo mysql2 para Ruby com Gem ao executar o comando `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu da esquerda no portal do Azure, clique em **todos os recursos**e, em seguida, procure o servidor ter creased (tais como **myserver4demo**).
3. Clique no nome do servidor, **myserver4demo**.
4. Selecione o servidor **propriedades** página e, em seguida, anote o **nome do servidor** e **nome de início de sessão de administração do servidor**.
 ![Base de Dados do Azure para o MySQL – Início de sessão de administrador do servidor](./media/connect-ruby/1_server-properties-name-login.png)
5. Se se esquecer da sua informações de início de sessão do servidor, navegue para o **descrição geral** página para ver o nome de início de sessão de administração do servidor e, se necessário repor a palavra-passe.

## <a name="run-ruby-code"></a>Executar código Ruby 
1. Cole o código de Ruby as secções abaixo ficheiros de texto e, em seguida, guarde os ficheiros para uma pasta do projeto com .rb de extensão de ficheiro (tal como `C:\rubymysql\createtable.rb` ou `/home/username/rubymysql/createtable.rb`).
2. Para executar o código, inicie a linha de comandos ou Bash shell. Altere o diretório para a pasta do projeto, `cd rubymysql`.
3. Em seguida, escreva o comando Ruby, seguido do nome de ficheiro, tal como `ruby createtable.rb` para executar a aplicação.
4. No sistema operativo Windows, a aplicação Ruby não esteja na sua variável de ambiente path, poderá ter de utilizar o caminho completo para iniciar a aplicação de nó, tais como`"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Ligar-se e criar uma tabela
Utilize o seguinte código para ligar e criar uma tabela utilizando **CREATE TABLE** instrução de SQL, seguida de **INSERT INTO** instruções SQL para adicionar linhas na tabela.

O código utiliza um método [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class .new() para ligar à Base de Dados do Azure para MySQL. Em seguida, chama várias vezes o método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para executar os comandos DROP, CREATE TABLE e INSERT INTO. Em seguida, chama o método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a ligação antes de terminar.

Substitua as cadeias `host`, `database`, `username` e `password` pelos seus próprios valores. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Ler dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **SELECIONE** instrução SQL. 

O código utiliza um [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class.new() método para ligar à base de dados do Azure para MySQL. Em seguida, chama o método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para executar os comandos SELECT. Em seguida, chama o método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a ligação antes de terminar.

Substitua as cadeias `host`, `database`, `username` e `password` pelos seus próprios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Atualizar dados
Utilize o seguinte código para estabelecer ligação e atualizar os dados utilizando um **ATUALIZAR** instrução SQL.

O código utiliza um método [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class .new() para ligar à Base de Dados do Azure para MySQL. Em seguida, chama o método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para executar os comandos UPDATE. Em seguida, chama o método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a ligação antes de terminar.

Substitua as cadeias `host`, `database`, `username` e `password` pelos seus próprios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Eliminar dados
Utilize o seguinte código para estabelecer ligação e ler os dados utilizando um **eliminar** instrução SQL. 

O código utiliza um método [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class .new() para ligar à Base de Dados do Azure para MySQL. Em seguida, chama o método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para executar os comandos DELETE. Em seguida, chama o método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para fechar a ligação antes de terminar.

Substitua as cadeias `host`, `database`, `username` e `password` pelos seus próprios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./concepts-migrate-import-export.md)
