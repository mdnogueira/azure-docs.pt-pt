---
title: Utilizar Ruby para consultar a Base de Dados SQL do Azure | Microsoft Docs
description: "Este tópico mostra-lhe como utilizar Ruby para criar um programa que se liga a uma base de dados SQL do Azure e a consulta com instruções Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 07/14/2017
ms.author: carlrab
ms.openlocfilehash: b7617d8f4cfe09052204182b40f6e5f7d46087b8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Utilizar Ruby para consultar uma base de dados SQL do Azure

Este tutorial de início rápido demonstra como utilizar o [Ruby](https://www.ruby-lang.org) para criar um programa que se liga a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial de início rápido, certifique-se de que tem os seguintes pré-requisitos:

- Uma base de dados SQL do Azure. Este início rápido utiliza os recursos criados num dos inícios rápidos seguintes: 

   - [Criar BD - Portal](sql-database-get-started-portal.md)
   - [Criar BD - CLI](sql-database-get-started-cli.md)
   - [Criar BD - PowerShell](sql-database-get-started-powershell.md)

- Uma [regra de firewall ao nível do servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para o endereço IP público do computador que utilizar para este tutorial de início rápido.
- Ter instalado o Ruby e software relacionado para o seu sistema operativo.
    - **MacOS**: instale o Homebrew, instale o rbenv e o ruby-build, instale o Ruby e, em seguida, instale o FreeTDS. Veja os [Passos 1.2, 1.3, 1.4 e 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
    - **Ubuntu**: instale os pré-requisitos para Ruby, instale o rbenv e o ruby-build, instale o Ruby e, em seguida, instale o FreeTDS. Veja os [Passos 1.2, 1.3, 1.4 e 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).

## <a name="sql-server-connection-information"></a>Informações de ligação do servidor SQL

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. No painel **Descrição geral** da base de dados, reveja o nome do servidor completamente qualificado. Pode colocar o rato sobre o nome do servidor para aparecer a opção **Clique para copiar**, conforme mostrado na imagem seguinte:

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se tiver esquecido das informações de início de sessão do seu servidor da Base de Dados SQL do Azure, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe.

> [!IMPORTANT]
> Tem de ter implementada uma regra de firewall para o endereço IP público do computador no qual executar este tutorial. Se estiver num computador diferente ou tiver um endereço IP público diferente, crie uma [regra de firewall ao nível do servidor com o portal do Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar a base de dados do SQL

1. No seu editor de texto favorito, crie um ficheiro novo, **sqltest.py**.

2. Substitua os conteúdos pelo código seguinte e adicione os valores corretos para o seu servidor, a sua base de dados, o seu utilizador e a sua palavra-passe.

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

## <a name="run-the-code"></a>Executar o código

1. Na linha de comandos, execute os comandos seguintes:

   ```bash
   ruby sqltest.rb
   ```

2. Confirme que as 20 linhas superiores são devolvidas e, em seguida, feche a janela da aplicação.


## <a name="next-steps"></a>Passos Seguintes
- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)
- [Repositório do GitHub para TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Report issues or ask questions about TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues) (Comunicar problemas ou fazer perguntas sobre o TinyTDS)
- [Controladores Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
