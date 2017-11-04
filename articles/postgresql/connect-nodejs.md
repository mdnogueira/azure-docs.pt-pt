---
title: "Ligar-se à Base de Dados do Azure para PostgreSQL a partir de Node.js | Microsoft Docs"
description: "Este guia de início rápido proporciona um código de exemplo de Node.js que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2017
ms.openlocfilehash: c16705c89c36452a28ab0547dc6b5277f57dd6d5
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data"></a>Base de Dados do Azure para PostgreSQL: utilizar o Node.js para se ligar e consultar dados
Este guia de introdução demonstra como estabelecer ligação a uma base de dados do Azure para PostgreSQL utilizando um [Node.js](https://nodejs.org/) aplicação. Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Os passos neste artigo partem do princípio de que está familiarizado com o desenvolvimento com o Node.js e estiver a trabalhar com a base de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um destes guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI](quickstart-create-server-database-azure-cli.md)

Também tem de:
- Instalar o [Node. js](https://nodejs.org)

## <a name="install-pg-client"></a>Instalar o cliente pg
Instalar [pg](https://www.npmjs.com/package/pg), que é um cliente PostgreSQL para Node.js.

Para isso, execute o gestor de pacotes de nós (npm) para JavaScript a partir da linha de comandos para instalar o cliente pg.
```bash
npm install pg
```

Verifique a instalação através de uma lista de pacotes instalados.
```bash
npm list
```

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu da esquerda no portal do Azure, clique em **todos os recursos** e procure o servidor que acabou de criar.
3. Clique no nome do servidor.
4. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.
 ![Base de Dados do Azure para o PostgreSQL – Início de sessão de administrador do servidor](./media/connect-nodejs/1-connection-string.png)
5. Caso se tenha esquecido das informações de início de sessão do seu servidor, navegue até à página **Descrição geral** para visualizar o nome de início de sessão de administrador do servidor e, se necessário, repor a palavra-passe.

## <a name="running-the-javascript-code-in-nodejs"></a>Executar o código JavaScript no Node.js
Podem iniciar Node.js a partir da shell Bash, Terminal ou linha de comandos do Windows, escrevendo `node`, em seguida, execute o exemplo de código JavaScript interativamente pela cópia-los e colá para a linha de comandos. Como alternativa, pode guardar o código JavaScript num ficheiro de texto e iniciar `node filename.js` com o nome do ficheiro como um parâmetro para o executar.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para se ligar e carregar os dados com as instruções SQL **CREATE TABLE** e **INSERT INTO**.
O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Ler dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Atualizar dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **UPDATE**. O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e ler os dados com a instrução SQL **DELETE**. O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
