---
title: "Ligar à base de dados do Azure para MySQL do MySQL Workbench | Microsoft Docs"
description: "Este guia de Introdução fornece os passos para utilizar o MySQL Workbench para ligar e consultar dados a partir da base de dados do Azure para MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: seanli1988
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 024db86b8760c8edb8347679eec6c68ceab3cd35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-mysql-workbench-to-connect-and-query-data"></a>Base de dados do Azure para MySQL: Utilize MySQL Workbench para ligar e consultar dados
Este guia de introdução demonstra como estabelecer ligação a uma base de dados do Azure para MySQL utilizando a aplicação de MySQL Workbench. 

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Instalar o MySQL Workbench
Transfira e instale o MySQL Workbench no seu computador da [o Web site MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. No menu da esquerda no portal do Azure, clique em **todos os recursos**e, em seguida, procure o servidor que criou (tais como **myserver4demo**).

3. Clique no nome do servidor.

4. Selecione o servidor **propriedades** página e, em seguida, anote o **nome do servidor** e **nome de início de sessão de administração do servidor**.

 ![Base de dados do Azure para o nome do servidor MySQL](./media/connect-workbench/1-server-properties-name-login.png)
 
5. Se se esquecer da sua informações de início de sessão do servidor, navegue para o **descrição geral** página para ver o nome de início de sessão de administração do servidor e, se necessário repor a palavra-passe.

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Ligar ao servidor utilizando o MySQL Workbench 
Para ligar ao servidor de MySQL do Azure utilizando a ferramenta de GUI MySQL Workbench:

1.  Inicie a aplicação de MySQL Workbench no seu computador. 

2.  No **configuração nova ligação** caixa de diálogo, introduza as seguintes informações o **parâmetros** separador:

    ![configurar ligação nova](./media/connect-workbench/2-setup-new-connection.png)

    | **Definição** | **Valor sugerido** | **Descrição do campo** |
    |---|---|---|
    |   Nome da Ligação | Ligação de Demonstração | Especifique uma etiqueta para esta ligação. |
    | Método de Ligação | Standard (TCP/IP) | Standard (TCP/IP) é suficiente. |
    | Nome de anfitrião | *server name* | Especifique o valor de nome de servidor que foi utilizado quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso servidor de exemplo mostrado é myserver4demo.mysql.database.azure.com. Utilize o nome de domínio completamente qualificado (\*.mysql.database.azure.com), conforme mostrado no exemplo. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome do servidor.  |
    | Porta | 3306 | Utilize sempre a porta 3306 para se ligar à Base de Dados do Azure para MySQL. |
    | Nome de utilizador |  *nome de início de sessão de administrador do servidor* | Introduza o nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso nome de utilizador de exemplo é myadmin@myserver4demo. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome de utilizador. O formato é *username@servername*.
    | Palavra-passe | a sua palavra-passe | Clique em **arquivo no cofre...**  botão para guardar a palavra-passe. |

3.   Clique em **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente. 

4.   Clique em **OK** para guardar a ligação. 

5.   Numa listagem de **MySQL ligações**, clique no mosaico correspondente ao seu servidor e, em seguida, aguarde que a ligação ser estabelecida.

        É aberto um novo separador do SQL Server com um editor em branco, onde pode introduzir as suas consultas.
    
        > [!NOTE]
        > Por predefinição, a segurança de ligação SSL é necessário e imposta na sua base de dados do Azure para o servidor de MySQL. Normalmente, nenhuma configuração adicional com certificados SSL é necessária para MySQL Workbench ligar ao seu servidor. Para obter mais informações sobre SSL, consulte [conectividade de configurar o SSL na sua aplicação para ligar de forma segura à base de dados do Azure para MySQL](./howto-configure-ssl.md).  Se tiver de desativar o SSL, visitar o portal do Azure e clique na página de segurança de ligação para desativar o botão de alternar de ligação SSL impor.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Criar uma tabela, inserir dados, ler os dados, atualizar dados, eliminar dados
1. Copie e cole o código de SQL de exemplo de um separador de SQL Server em branco para ilustrar alguns dados de exemplo.

    Este código cria uma base de dados vazio com o nome quickstartdb e, em seguida, cria uma tabela de exemplo com o nome de inventário. Em seguida, insere-algumas linhas, lê as linhas. Altera os dados com uma instrução de atualização e lê as linhas novamente. Por fim, este elimina uma linha e, em seguida, lê as linhas novamente.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    A captura de ecrã mostra um exemplo do código de SQL no SQL Server Workbench e o resultado após tenha sido executado.
    
    ![MySQL Workbench SQL separador para executar o SQL. código de exemplo](media/connect-workbench/3-workbench-sql-tab.png)

2. Para executar o SQL. código de exemplo, clique no ícone de bolt lightening na barra de ferramentas do **ficheiro SQL** separador.
3. Tenha em atenção os três resultados separadores no **resultado grelha** secção no meio da página. 
4. Tenha em atenção o **saída** lista na parte inferior da página. É apresentado o estado de cada comando. 

Agora, tiver estabelecido ligação à base de dados do Azure para MySQL utilizando MySQL Workbench e terem consultado dados utilizando a linguagem do SQL Server.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./concepts-migrate-import-export.md)
