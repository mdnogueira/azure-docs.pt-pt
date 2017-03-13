---
title: Tutorial NoSQL Node.js para o DocumentDB | Microsoft Docs
description: "Um tutorial NoSQL Node.js que cria uma base de dados NoSQL e uma aplicação de consola com o SDK DocumentDB Node.js. O DocumentDB é uma base de dados NoSQL para JSON."
keywords: tutorial de node.js, base de dados de node
services: documentdb
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: e955366aa6fb8481fc8a76158b298dd927e855a2
ms.lasthandoff: 03/08/2017


---
# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>Tutorial NoSQL Node.js: aplicação da consola Node.js do DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bem-vindo ao tutorial de Node.js para o SDK Node.js do DocumentDB! Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do DocumentDB.

Iremos abranger:

* Criação e ligação a uma conta DocumentDB
* Configurar a sua aplicação
* Criar uma base de dados Node
* Criação de uma coleção
* Criação de documentos JSON
* Consulta da coleção
* Substituir um documento
* Eliminar um documento
* Eliminar uma base de dados Node

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte o artigo [Obter a solução completa](#GetSolution) para instruções rápidas.

Quando tiver concluído o tutorial Node.js, utilize os botões de voto na parte superior e inferior desta página para submeter os seus comentários. Se preferir que entremos diretamente em contacto consigo, não hesite em indicar o seu endereço de e-mail nos seus comentários.

Agora comecemos!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Pré-requisitos para o tutorial Node.js
Certifique-se de que tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
    * Em alternativa, pode utilizar o [Emulador do Azure DocumentDB](documentdb-nosql-local-emulator.md) para este tutorial.
* Versão [Node.js](https://nodejs.org/) v0.10.29 ou superior.

## <a name="step-1-create-a-documentdb-account"></a>Passo 1: Criar uma conta DocumentDB
Criemos uma conta DocumentDB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a sua aplicação Node.js](#SetupNode). Se estiver a utilizar o Emulador do DocumentDB, siga os passos em [Azure DocumentDB Emulator (Emulador do Azure DocumentDB)](documentdb-nosql-local-emulator.md) para configurar o emulador e avance para [Setup your Node.js application (Configurar a aplicação Node.js)](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>Passo 2: Configurar a sua aplicação Node.js
1. Abra o seu terminal favorito.
2. Localize a pasta ou o diretório onde pretende guardar a sua aplicação Node.js.
3. Crie dois ficheiros JavaScript vazios com os seguintes comandos:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Instalar o módulo documentdb através do npm. Utilize o seguinte comando:
   * ```npm install documentdb --save```

Ótimo! Agora que concluiu a configuração, comecemos a escrever certos códigos.

## <a id="Config"></a>Passo 3: Configurar as configurações da sua aplicação
Abra ```config.js``` no seu editor de texto favorito.

Em seguida, copie e cole o fragmento de código abaixo e defina as propriedades ```config.endpoint``` e ```config.primaryKey``` para o uri e chave primária do ponto final do seu DocumentDB. Pode encontrar ambas as configurações no [Portal do Azure](https://portal.azure.com).

![Tutorial Node.js - Captura de ecrã do Portal do Azure que mostra uma conta DocumentDB com o ACTIVE hub realçado, o botão CHAVES realçado no painel de conta DocumentDB e os valores URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados no painel Chaves - Base de dados Node][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copie e cole o ```database id```, ```collection id```, e ```JSON documents``` para seu objeto ```config``` por baixo do local onde configurou o seu ```config.endpoint``` e ```config.authKey``` Propriedades. Se já tiver dados que pretende armazenar na sua base de dados, pode utilizar a [ferramenta de Migração de Dados](documentdb-import-data.md) do DocumentDB em vez de adicionar as definições do documento.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


As definições da base de dados, da coleção e do documento irão funcionar como o seu DocumentDB ```database id```, ```collection id```, e dados dos documentos.

Por fim, exporte o seu objeto ```config``` para poder referenciá-lo no ficheiro ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a> Passo 4: Ligar a uma conta do DocumentDB
Abra o seu ficheiro ```app.js``` vazio no editor de texto. Copie e cole o código abaixo para importar o módulo ```documentdb``` e o módulo ```config``` que criou recentemente.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copie e cole o código a utilizar guardado anteriormente ```config.endpoint``` e ```config.primaryKey``` para criar um novo DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Agora que tem o código para inicializar o cliente do documentdb, vamos ver como utilizar os recursos do DocumentDB.

## <a name="step-5-create-a-node-database"></a>Passo 5: Criar uma base de dados Node
Copie e cole o código abaixo para definir o estado HTTP em Não Encontrado, o url da base de dados e o url da coleção. Estes urls irão permitir ao cliente do DocumentDB encontrar a base de dados e coleção corretas.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Pode criar uma [base de dados](documentdb-resources.md#databases) utilizando a função [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Uma base de dados é o contentor lógico do armazenamento de documentos particionados em coleções.

Copie e cole a função **getDatabase** para criar a sua nova base de dados no ficheiro app.js com ```id``` especificado no objeto ```config```. A função irá verificar se já existe uma base de dados com o mesmo id ```FamilyRegistry```. Se existir, iremos devolver essa base de dados em vez de criar uma nova.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie e cole o código por baixo do local onde o configurou a função **getDatabase** para adicionar a função de programa auxiliar **sair** que irá imprimir a mensagem de saída e a chamada para a função **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Criou uma base de dados DocumentDB com êxito.

## <a id="CreateColl"></a>Passo 6: Criar uma coleção
> [!WARNING]
> **CreateDocumentCollectionAsync** irá criar uma nova coleção, tendo repercussões sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Pode criar uma [coleção](documentdb-resources.md#collections) utilizando a função [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

Copie e cole a função **getCollection** por baixo da função **getDatabase** no ficheiro app.js para criar a sua nova coleção com ```id``` especificado no objeto ```config```. Mais uma vez, iremos certificar-nos de que não existe nenhuma coleção com o mesmo id ```FamilyCollection```. Se existir, iremos devolver essa coleção em vez de criar uma nova.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie e cole o código por baixo da chamada para **getDatabase** para executar a função **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Criou uma coleção DocumentDB com êxito.

## <a id="CreateDoc"></a>Passo 7: Criar um documento
Pode criar um [documento](documentdb-resources.md#documents) utilizando a função [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Os documentos são conteúdos (arbitrários) JSON definidos pelo utilizador. Pode agora inserir um documento no DocumentDB.

Copie e cole a função **getFamilyDocument** por baixo da função **getCollection** para criar documentos que contêm os dados JSON guardados no objeto ```config```. Mais uma vez, iremos certificar-nos de que não existe nenhum documento com o mesmo ID.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo da chamada para **getCollection** para executar a função **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Criou documentos DocumentDB com êxito.

![Tutorial Node.js - Diagrama que ilustra a relação hierárquica entre a conta, a base de dados, a coleção e os documentos - base de dados Node](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

## <a id="Query"></a>Passo 8: Consultar os recursos do DocumentDB
O DocumentDB suporta [consultas](documentdb-sql-query.md) extensas de documentos JSON armazenados em cada coleção. O seguinte código de exemplo mostra uma consulta que pode executar nos documentos da sua coleção.

Copie e cole a função **queryCollection** por baixo da função **getFamilyDocument** no ficheiro app.js. O DocumentDB suporta consultas de tipo SQL, conforme mostrado abaixo. Para obter mais informações sobre a criação de consultas complexas, consulte o artigo o [Query Playground](https://www.documentdb.com/sql/demo) e a [documentação sobre consultas](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


O seguinte diagrama ilustra como a sintaxe de consulta do DocumentDB SQL é chamada na coleção que criou.

![Tutorial Node.js - Diagrama que ilustra o âmbito e o significado da consulta - base de dados Node](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#FromClause) é opcional na consulta porque as consultas do DocumentDB já estão confinadas a uma única coleção. Por conseguinte, "FROM Families f" pode ser trocada por "FROM root r" ou qualquer outra variável de nome escolher. Por predefinição, o DocumentDB irá inferir essas Famílias, raiz ou o nome da variável que escolheu, referenciar a atual coleção.

Copie e cole o código por baixo da chamada para **getFamilyDocument** para executar a função **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Consultou documentos DocumentDB com êxito.

## <a id="ReplaceDocument"></a>Passo 9: Substituir um documento
O DocumentDB suporta a substituição de documentos JSON.

Copie e cole a função **replaceFamilyDocument** por baixo da função **queryCollection** no ficheiro app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo da chamada para **queryCollection** para executar a função **replaceDocument**. Além disso, adicione o código para chamar novamente **queryCollection** para confirmar que o documento foi alterado com êxito.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Substituiu um documento do DocumentDB com êxito.

## <a id="DeleteDocument"></a>Passo 10: Eliminar um documento
O DocumentDB suporta a eliminação de documentos JSON.

Copie e cole a função **deleteFamilyDocument** por baixo da função **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo da chamada da segunda **queryCollection** para executar a função **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Eliminou um documento do DocumentDB com êxito.

## <a id="DeleteDatabase"></a>Passo 11: Eliminar a base de dados Node
Eliminar a base de dados criada irá remover a base de dados e todos os recursos subordinados (coleções, documentos, etc.).

Copie e cole afunção **cleanup** baixo da função **deleteFamilyDocument** para remover a base de dados e todos os recursos subordinados.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copie e cole o código por baixo da chamada para **deleteFamilyDocument** para executar a função **limpeza**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Passo 12: Executar a sua aplicação Node.js em conjunto!
No seu conjunto, a sequência para chamar as suas funções deve ter o seguinte aspeto:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Deverá ver o resultado da sua aplicação Introdução. A saída deve corresponder ao texto de exemplo abaixo.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Parabéns! Criou e completou o tutorial Node.js e tem a sua primeira aplicação de consola do DocumentDB!

## <a id="GetSolution"></a>Obter a solução completa do tutorial Node.js
Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir o código, pode obtê-lo a partir do [Github](https://github.com/Azure-Samples/documentdb-node-getting-started).

Para executar a solução GetStarted que contém todos os exemplos deste artigo, deverá ter o seguinte:

* [Conta do DocumentDB][documentdb-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) está disponível no GitHub.

Instalar o módulo **documentdb** através do npm. Utilize o seguinte comando:

* ```npm install documentdb --save```

Em seguida, no ficheiro ```config.js```, atualize os valores config.endpoint e config.authKey, conforme descrito em [Passo 3: Definir as configurações da sua aplicação](#Config). 

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```.

Já está, basta criar e está pronto! 

## <a name="next-steps"></a>Passos seguintes
* Pretende um exemplo de Node.js mais complexo? Consulte o artigo [Criar uma aplicação Web Node.js utilizando o DocumentDB](documentdb-nodejs-application.md).
* Saiba como [monitorizar uma conta DocumentDB](documentdb-monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na secção Desenvolver da [página de documentação do DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

