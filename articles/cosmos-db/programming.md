---
title: "Programação de JavaScript do lado do servidor de base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como utilizar a base de dados do Azure Cosmos para escrever e procedimentos armazenados, acionadores de base de dados, funções definidas pelo utilizador (UDFs) em JavaScript. Obter sugestões de programing de base de dados e muito mais."
keywords: Acionadores de base de dados, procedimento armazenado, procedimentos armazenados, o programa de base de dados, sproc, documentdb, azure, do Microsoft azure
services: cosmos-db
documentationcenter: 
author: aliuy
manager: jhubbard
editor: mimig
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: andrl
ms.openlocfilehash: ef191c3c8d85afa389859956d30b5ac0275053d2
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programação do lado do servidor de base de dados do Cosmos do Azure: e procedimentos armazenados, acionadores de base de dados, UDFs
Saiba como idioma da BD do Azure Cosmos integrados, execução transacional do JavaScript permite aos programadores escrever **procedimentos armazenados**, **acionadores** e **(UDFs)defunçõesdefinidaspeloutilizador** nativamente num [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) JavaScript. Isto permite-lhe escrever a lógica de aplicação de programa de base de dados que pode ser fornecida e executada diretamente nas partições de armazenamento de base de dados. 

Recomendamos que comece por ver o vídeo seguinte, onde Andrew Liu fornece uma breve introdução ao modelo de programação de base de dados do lado do servidor da BD do Cosmos. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Demo-A-Quick-Intro-to-Azure-DocumentDBs-Server-Side-Javascript/player]
> 
> 

Em seguida, regresse a este artigo, onde poderá aprender as respostas às seguintes perguntas:  

* Como escrever um um procedimento armazenado, acionador ou UDF através de JavaScript?
* Como a base de dados do Cosmos garantir ACID?
* Como funcionam as transações na base de dados do Cosmos?
* Quais são previamente aciona e aciona pós-cópia e como escrever um?
* Como registar e executar um procedimento armazenado, acionador ou UDF de forma RESTful através de HTTP
* Que estão disponíveis para criar e executar SDKs de BD do Cosmos e procedimentos armazenados, acionadores, UDFs?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introdução ao procedimento armazenado e UDF programação
Esta abordagem de *"JavaScript sendo um dia moderno T-SQL"* liberta os programadores de aplicações do complexidades de tipos incompatíveis de sistema e as tecnologias de mapeamento de objetos relacionais. Também tem várias vantagens intrínsecos que pode ser utilizado para criar aplicações avançadas:  

* **Lógica de procedimento:** JavaScript como uma linguagem de programação alto nível, fornece uma interface avançada e familiar para expressar a lógica de negócio. Pode efetuar complexas sequências de operações próximo aos dados.
* **Transações atómicas:** garantias Cosmos DB que operações executadas dentro de um procedimento armazenado único ou acionador de base de dados são atómicas. Isto permite que uma aplicação combinar relacionadas com operações num único lote para que todos eles concluída com êxito ou nenhum com êxito. 
* **Desempenho:** o facto de que JSON intrinsecamente está mapeado para o sistema de tipo de linguagem Javascript e também é a unidade básica de armazenamento na base de dados do Cosmos permite a um número de otimizações como materialization lento de documentos JSON no conjunto de memória intermédia e tornar disponível a pedido para o código em execução. Existem mais benefícios de desempenho associados envio lógica de negócio para a base de dados:
  
  * Criação de batches – os programadores podem operações como inserções de grupo e submetê-las em massa. A latência de tráfego de rede de custos e a sobrecarga de arquivo para criar transações separadas são significativamente reduzidas. 
  * Pré-compilação – Cosmos DB precompiles procedimentos armazenados, acionadores e funções definidas pelo utilizador (UDFs) para evitar o custo de compilação do JavaScript para cada invocação. A sobrecarga de compilar o código de byte para a lógica de procedimento é amortized para um valor mínimo.
  * Sequenciação – muitas necessidade de operações-efeito ("acionador") que potencialmente envolve fazer uma ou várias operações de arquivo secundário. Para além de atomicity, este é mais performant quando movida para o servidor. 
* **Encapsulamento:** os procedimentos armazenados podem ser utilizados para agrupar a lógica de negócio num único local. Isto tem duas vantagens:
  * Adiciona uma camada de abstração sobre os dados não processados, que permite a arquitetos de dados evoluir as aplicações de forma independente a partir dos dados. Isto é particularmente vantajoso quando os dados sem esquema, devido aos pressupostos brittle que poderão ter de ser integrada na aplicação, se tiverem que lidar com os dados diretamente.  
  * Esta abstração permite que as empresas a manter os seus dados segura, simplificando o acesso a partir de scripts.  

A criação e a execução de acionadores de base de dados, o procedimento armazenado e operadores de consulta personalizada é suportada através de [REST API](/rest/api/documentdb/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), e [cliente SDKs](documentdb-sdk-dotnet.md)em muitas plataformas, incluindo .NET, Node.js e JavaScript.

Este tutorial utiliza o [SDK Node.js com Q Promises](http://azure.github.io/azure-documentdb-node-q/) para ilustrar a sintaxe e a utilização de procedimentos armazenados, acionadores e UDFs.   

## <a name="stored-procedures"></a>Procedimentos armazenados
### <a name="example-write-a-simple-stored-procedure"></a>Exemplo: Escrever um procedimento armazenado simple
Vamos começar com um procedimento armazenado simple que devolve uma resposta "Olá mundo".

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Os procedimentos armazenados são registados por coleção e podem operar em qualquer documento e anexo presente nessa coleção. O fragmento seguinte mostra como registar o procedimento armazenado do helloWorld com uma coleção. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Depois do procedimento armazenado é registado, iremos pode executá-lo contra a coleção e ler os resultados de volta ao cliente. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


O objeto de contexto fornece acesso a todas as operações que podem ser executadas no armazenamento de base de dados do Cosmos, bem como acesso a objetos de pedido e resposta. Neste caso, utilizámos o objeto de resposta para definir o corpo da resposta que lhe foi enviado para o cliente. Para obter mais detalhes, consulte o [Azure Cosmos DB JavaScript servidor documentação do SDK](http://azure.github.io/azure-documentdb-js-server/).  

Informe-nos expanda neste exemplo e adicionar que mais funcionalidades relacionadas para o procedimento armazenado de base de dados. Procedimentos armazenados podem criar, atualizar, ler, consultar e eliminar documentos e anexos dentro da coleção.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exemplo: Escrever um procedimento armazenado para criar um documento
O fragmento seguinte mostra como utilizar o objeto de contexto para interagir com recursos de BD do Cosmos.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        serverScript: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Este procedimento armazenado aceita como entrada documentToCreate, o corpo de um documento a ser criado na colecção actual. Todas as operações são assíncronas e dependem de chamadas de retorno de função de JavaScript. A função de chamada de retorno tem dois parâmetros, um para o objeto erro caso que a operação falhar e outro para o objeto criado. Dentro de chamada de retorno, os utilizadores podem processar a excepção ou gerar um erro. No caso de uma chamada de retorno não é fornecida e não existe um erro, o tempo de execução da base de dados do Azure Cosmos emite um erro.   

No exemplo acima, a chamada de retorno emite um erro se a operação falhou. Caso contrário, define o id do documento criado como o corpo da resposta ao cliente. Eis como este procedimento armazenado é executado com parâmetros de entrada.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;

            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };

            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });


Tenha em atenção que este procedimento armazenado pode ser modificado para tomar uma matriz de corpos de documento como entrada e criá-los todos na mesma execução de procedimento armazenado em vez de vários pedidos de rede para criar cada um deles individualmente. Isto pode ser utilizado para implementar um importador eficiente em massa para DB Cosmos (abordado posteriormente neste tutorial).   

O exemplo descrito demonstrado como utilizar os procedimentos armazenados. Abordaremos acionadores e funções definidas pelo utilizador (UDFs) mais tarde no tutorial.

## <a name="database-program-transactions"></a>Transações de programa de base de dados
Transação numa base de dados típica pode ser definida como uma sequência de operações efetuadas como uma unidade lógica única de trabalho. Cada transação fornece **garantias ACID**. ACID é um acrónimo conhecido que representa quatro propriedades - Atomicity, consistência, isolamento e características de durabilidade.  

Resumidamente, atomicity garante que todo o trabalho feito dentro de uma transação é tratado como uma única unidade em que o todos está consolidada ou none. Consistência certifica-se de que os dados estão sempre em bom estado interno em transações. Isolamento garante que não existem dois transações interferir com outros – geralmente, mais comerciais sistemas fornecem vários níveis de isolamento que podem ser utilizados com base nas necessidades de aplicação. Características de durabilidade garante que qualquer alteração que está consolidada na base de dados será sempre presente.   

Na base de dados do Cosmos, JavaScript está alojado no mesmo espaço de memória como a base de dados. Por conseguinte, os pedidos efetuados dentro de procedimentos armazenados e acionadores ser executado no mesmo âmbito de uma sessão de base de dados. Isto permite que a BD do Cosmos garantir ACID para todas as operações que fazem parte de um único procedimento/acionador armazenado. Considere a seguinte definição de procedimento armazenado:

    // JavaScript source code
    var exchangeItemsSproc = {
        id: "exchangeItems",
        serverScript: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            var player1Document, player2Document;

            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);

                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];

                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });

            if (!accept) throw "Unable to read player details, abort ";

            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;

                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";

                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });

                        if (!accept2) throw "Unable to update player 2, abort";
                    });

                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }

    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Este procedimento armazenado utiliza transações dentro de uma aplicação de jogos a itens de compromissos entre duas jogadores numa única operação. O procedimento armazenado tenta ler dois documentos cada correspondentes aos player IDs transmitido como um argumento. Se ambos os documentos de leitor encontram-se, em seguida, o procedimento armazenado atualiza os documentos, troca os seus itens. Se forem encontrados erros ao longo do percurso, emite uma exceção de JavaScript implicitamente interrompe a transação.

Se está registada na coleção o procedimento armazenado contra é uma coleção de partição única, em seguida, a transação tem um âmbito para todos os documentos dentro da coleção. Se a coleção estiver particionada, procedimentos armazenados são executados no âmbito de transação de uma chave de partição única. Execução do procedimento, em seguida, tem de incluir um valor de chave de partição correspondente para o âmbito da transação tem de executar em cada armazenado. Para obter mais detalhes, consulte [Azure Cosmos DB a criação de partições](partition-data.md).

### <a name="commit-and-rollback"></a>Consolidação e a reversão
As transações são profundamente e nativamente integradas no modelo de programação do Cosmos DB JavaScript. Dentro de uma função de JavaScript, todas as operações são automaticamente encapsuladas sob uma única transação. Se o JavaScript concluída sem qualquer excepção, as operações para a base de dados são consolidadas. Em vigor, as instruções de "BEGIN TRANSACTION" e "CONSOLIDAR transação" em bases de dados relacionais são implícitas na base de dados do Cosmos.  

Se não houver qualquer exceção que é propagada do script, tempo de execução do JavaScript da BD do Cosmos irá reverter a transação de toda. Como é mostrado no exemplo anterior, gerar uma exceção é efetivamente equivalente para um "ROLLBACK TRANSACTION" na base de dados do Cosmos.

### <a name="data-consistency"></a>Consistência dos dados
Acionadores e procedimentos armazenados são sempre executados na réplica primária do contentor do Azure Cosmos DB. Isto assegura que lê a partir de dentro armazenadas consistência forte de oferta de procedimentos. Consultas de funções definidas pelo utilizador a utilizar podem ser executadas num primário ou de qualquer réplica secundária, mas podemos assegurar para satisfazer o nível de consistência pedida ao escolher a réplica adequada.

## <a name="bounded-execution"></a>Execução vinculada
Tem de concluir todas as operações de base de dados do Cosmos no servidor especificado duração do tempo limite do pedido. Esta restrição aplica-se também a funções de JavaScript (procedimentos armazenados, acionadores e funções definidas pelo utilizador). Se uma operação não for concluída com esse limite de tempo, a transação é revertida. Funções JavaScript tem de concluir dentro do tempo limite ou implementar um modelo de continuação com base para a execução de lote/retomar.  

Para simplificar o desenvolvimento de procedimentos armazenados e acionadores para lidar com os limites de tempo, todas as funções sob o objecto de coleção (para criar, ler, substitua e eliminação de documentos e anexos) devolver um valor boleano valor que representa se essa operação irá concluir. Se este valor for FALSO, é uma indicação de que o limite de tempo está prestes a expirar e de que o procedimento tem de encapsular cópias de segurança execução.  Para concluir se o procedimento armazenado é concluída no tempo e não espera mais pedidos garantidos operações em fila antes da primeira operação de arquivo não aceite.  

Funções JavaScript são também tem um vínculo no consumo de recursos. BD do cosmos reserva débito por coleção com base no tamanho aprovisionado de uma conta de base de dados. Débito é expresso em termos de uma unidade normalizada de CPU, memória e unidades de pedido ou RUs de consumo de e/s. Funções JavaScript, potencialmente, podem utilizar um grande número de RUs num curto período de tempo de cópia de segurança e poderão obter taxa-limited caso seja atingido o limite da coleção. Procedimentos armazenados intensivas de recursos também podem ser colocados em quarentena para garantir a disponibilidade das operações de base de dados primitivos.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exemplo: Em massa importar dados para um programa de base de dados
Abaixo está um exemplo de um procedimento armazenado que é escrito importar em massa documentos para uma coleção. Tenha em atenção a como o procedimento armazenado processa execução vinculada ao verificar o valor de booleano devolver o valor de createDocument e, em seguida, utiliza a contagem de documentos inseridos em cada invocação do procedimento armazenado para controlar e retomar o progresso em lotes.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();

        // The count of imported docs, also used as current doc index.
        var count = 0;

        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");

        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }

        // Call the create API to create a document.
        tryCreate(docs[count], callback);

        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);

            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }

        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;

            // One more document has been inserted, increment the count.
            count++;

            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Acionadores de base de dados
### <a name="database-pre-triggers"></a>Pré-acionadores de base de dados
BD do cosmos fornece acionadores que são executadas ou acionados por uma operação num documento. Por exemplo, pode especificar um pré-acionador de quando estiver a criar um documento – este pré-acionador será executado antes do documento é criado. Segue-se um exemplo de como os acionadores de pré-lançamento podem ser utilizadas para validar as propriedades de um documento que está a ser criada:

    var validateDocumentContentsTrigger = {
        id: "validateDocumentContents",
        serverScript: function validate() {
            var context = getContext();
            var request = context.getRequest();

            // document to be created in the current operation
            var documentToCreate = request.getBody();

            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }

            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


E o código de registo do lado do cliente do Node.js correspondente para o acionador:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };

            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Pré-acionadores não podem ter quaisquer parâmetros de entrada. O objecto de pedido pode ser utilizado para manipular a mensagem de pedido associada com a operação. Aqui, o pré-acionador de está a ser executado com a criação de um documento e o corpo da mensagem de pedido contém o documento a ser criado no formato JSON.   

Quando acionadores estão registados, os utilizadores podem especificar as operações que pode ser executado com. Este acionador foi criada com TriggerOperation.Create, o que significa que não é permitida.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Pós-acionadores de base de dados
Pós-acionadores, como acionadores pré-lançamento, estão associados uma operação num documento e não ter quaisquer parâmetros de entrada. São executados **depois** a operação foi concluída e têm acesso para a mensagem de resposta que é enviado para o cliente.   

O exemplo seguinte mostra os acionadores de pós-implementação em ação:

    var updateMetadataTrigger = {
        id: "updateMetadata",
        serverScript: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            // document that was created
            var createdDocument = response.getBody();

            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";

            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';

                         var metadataDocument = documents[0];

                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                            
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


O acionador pode ser registado como mostrado no seguinte exemplo.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };

            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Este acionador consulta para o documento de metadados e atualiza-o com detalhes sobre o documento recentemente criado.  

É a única coisa que é importante ter em conta o **transacional** execução de acionadores na base de dados do Cosmos. Este acionador pós-implementação executado como parte da mesma transação como a criação do documento original. Por conseguinte, se podemos acionar uma excepção de pós-acionador (indiquem se estamos não é possível atualizar o documento de metadados), a transação toda falhará e ser revertida. Nenhum documento será criado e será devolvida uma exceção.  

## <a id="udf"></a>Funções definidas pelo utilizador
As funções definidas pelo utilizador (UDFs) são utilizadas para expandir a gramática de idioma de consulta do DocumentDB SQL de API e implementar a lógica de negócio personalizado. Só podem ser chamados de dentro de consultas. Estes não têm acesso ao objeto de contexto e se destinam a ser utilizado como só de computação JavaScript. Por conseguinte, UDFs podem ser executadas em réplicas secundárias do serviço base de dados do Cosmos.  

O exemplo seguinte cria um UDF para calcular a dedução dos impostos receitas com base nas tarifas para vários Retos de receitas e, em seguida, utiliza-o no interior de uma consulta para encontrar todas as pessoas que paga mais de 20.000 $ no taxas.

    var taxUdf = {
        id: "tax",
        serverScript: function tax(income) {

            if(income == undefined) 
                throw 'no input';

            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


UDF, subsequentemente, pode ser utilizado em consultas, como no seguinte exemplo:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);

            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>API de consulta de linguagem integrada de JavaScript
Para além de emitir consultas através de gramática do SQL do DocumentDB, o SDK do lado do servidor permite-lhe efetuar consultas otimizadas com uma interface de JavaScript fluent sem qualquer conhecimento do SQL Server. A consulta de JavaScript que API permite-lhe para programaticamente criar consultas através da transmissão de predicado funções para a função de chainable chama, com uma sintaxe familiar do ECMAScript5 built-ins de matriz e populares bibliotecas de JavaScript como lodash. As consultas são analisadas pelo runtime JavaScript para ser executada de forma eficiente com índices da BD do Cosmos do Azure.

> [!NOTE]
> `__`(double-caráter de sublinhado) é um alias para `getContext().getCollection()`.
> <br/>
> Por outras palavras, pode utilizar `__` ou `getContext().getCollection()` para aceder a API de consulta do JavaScript.
> 
> 

As funções suportadas incluem:

<ul>
<li>
<b>chain().... valor ([chamada de retorno] [, opções])</b>
<ul>
<li>
Inicia uma chamada em cadeia que tem de ser terminada com Value ().
</li>
</ul>
</li>
<li>
<b>filtro (predicateFunction [, opções] [, chamada de retorno])</b>
<ul>
<li>
Filtra a entrada através de uma função de predicado que devolve true/false para filtrar/out documentos de entrada para o conjunto resultante. Este comporta-se semelhante a uma cláusula WHERE no SQL Server.
</li>
</ul>
</li>
<li>
<b>mapa (transformationFunction [, opções] [, chamada de retorno])</b>
<ul>
<li>
Aplica-se uma projeção dada uma função de transformação que mapeia cada item de entrada para um valor ou o objeto de JavaScript. Este comporta-se semelhante a cláusula de SELEÇÃO no SQL Server.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Este é um atalho para um mapa que extrai o valor de uma propriedade de único de cada item de entrada.
</li>
</ul>
</li>
<li>
<b>aplanar ([isShallow] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Combina e flattens matrizes de cada item na entrada para uma matriz única. Este comporta-se semelhante ao SelectMany no LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predicate] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Produzir um novo conjunto de documentos pelo ordenar os documentos no fluxo de documento de entrada ascendentemente utilizando o predicado indicado. Este comporta-se semelhante a uma cláusula ORDER BY no SQL Server.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predicate] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Produzir um novo conjunto de documentos pelo ordenar os documentos no fluxo de documento de entrada por ordem descendente utilizando o predicado indicado. Este comporta-se semelhante a uma cláusula ORDER BY x DESC no SQL Server.
</li>
</ul>
</li>
</ul>


Quando incluídos no interior do predicado de e/ou o Seletor de funções, as construções de JavaScript seguintes obterem automaticamente otimizadas para executar diretamente em índices de base de dados do Azure Cosmos:

* Operadores simples: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* As literais, incluindo o objeto literal: {}
* var, retorno

As seguintes construções de JavaScript não obter otimizadas para índices de base de dados do Azure Cosmos:

* Controlar o fluxo (por exemplo, se, enquanto)
* Chamadas de função

Para obter mais informações, consulte a nossa [JSDocs do lado do servidor](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exemplo: Escrever um procedimento armazenado utilizando a API de consulta de JavaScript
O exemplo de código seguinte é um exemplo de como a API de consulta de JavaScript pode ser utilizada no contexto de um procedimento armazenado. O procedimento armazenado insere um documento, fornecido por um parâmetro de entrada e de atualizações de metadados de um documento, utilizando o `__.filter()` método, com minSize, maxSize e totalSize com base na propriedade de tamanho do documento de entrada.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL Server para cábula de Javascript
A tabela seguinte apresenta várias consultas SQL e as consultas de JavaScript correspondentes.

Conforme com as consultas SQL, chaves de propriedade de documento (por exemplo, `doc.id`) diferenciam maiúsculas de minúsculas.

|SQL| Consulta de JavaScript API|Descrição abaixo|
|---|---|---|
|SELECIONE *<br>DE documentos| __.Map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;devolver o documento;<br>});|1|
|SELECIONE docs.id, docs.message como tarifas de mensagens, docs.actions <br>DE documentos|__.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolver {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tarifas de mensagens: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|SELECIONE *<br>DE documentos<br>WHERE docs.id="X998_Y998"|__.Filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolver doc.id === "X998_Y998";<br>});|3|
|SELECIONE *<br>DE documentos<br>ONDE ARRAY_CONTAINS (docs. Etiquetas, 123)|__.Filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolver x.Tags & & x.Tags.indexOf(123) > -1;<br>});|4|
|SELECIONE docs.id, docs.message como tarifas de mensagens<br>DE documentos<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tarifas de mensagens: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|5|
|Etiqueta de valor SELECIONE<br>DE documentos<br>ASSOCIE docs de IN tag. Etiquetas<br>Docs._ts ORDER BY|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolva o documento. Etiquetas & & Array.isArray (documento. Etiquetas);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|6|

As seguintes descrições explicam cada consulta na tabela acima.
1. Resulta em todos os documentos (paginated com o token de continuação) como está.
2. Projetos a id, a mensagem (um alias para tarifas de mensagens) e a ação de todos os documentos.
3. As consultas para documentos com o predicado: id = "X998_Y998".
4. As consultas para documentos que tenham uma propriedade de etiquetas e etiquetas é uma matriz que contém o valor 123.
5. As consultas para documentos com um predicado, id = "X998_Y998" e, em seguida, projetos, o id e a mensagem (um alias para tarifas de mensagens).
6. Filtros para documentos que tem uma propriedade de matriz, etiquetas e ordena os documentos resultantes pela propriedade de sistema _ts timestamp e, em seguida, projetos + flattens a matriz de etiquetas.


## <a name="runtime-support"></a>Suporte de tempo de execução
[API de lado de servidor do DocumentDB JavaScript](http://azure.github.io/azure-documentdb-js-server/) fornece suporte para a maioria das funcionalidades de idioma base do JavaScript como padronizado por [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Segurança
Procedimentos armazenados do JavaScript e acionadores são um forma que os efeitos de um script não escapem para outro sem passar o isolamento de transação de instantâneo ao nível da base de dados. Os ambientes do tempo de execução são agrupados mas limpos do contexto após cada execução. Por conseguinte, são garantidos segurança de quaisquer efeitos secundários indesejados uns dos outros.

### <a name="pre-compilation"></a>Pré-compilação
Os procedimentos armazenados, acionadores e UDFs são implicitamente pré-compilados para o formato de código de byte para evitar o custo de compilação no momento da cada invocação de script. Isto garante invocações de procedimentos armazenados são rápidas e tem um reduzidos.

## <a name="client-sdk-support"></a>Suporte SDK do cliente
Para além da API do DocumentDB para [Node.js](documentdb-sdk-node.md) tem de cliente, base de dados do Azure Cosmos [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Java](documentdb-sdk-java.md), [ JavaScript](http://azure.github.io/azure-documentdb-js/), e [Python SDKs](documentdb-sdk-python.md) para o API do DocumentDB. Procedimentos armazenados, acionadores e UDFs podem ser criados e executado utilizando qualquer um destes SDKs bem. O exemplo seguinte mostra como criar e executar um procedimento armazenado com o cliente do .NET. Tenha em atenção a como os tipos .NET são transmitidos para o procedimento armazenado como JSON e ler novamente.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    

                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }

                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
             }"
    };

    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;

    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);


Este exemplo mostra como utilizar o [API .NET do DocumentDB](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) para criar um acionador de pré-lançamento e criar um documento com o acionador ativado. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };

    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


E o exemplo seguinte mostra como criar uma função definida pelo utilizador (UDF) e utilizá-la num [consulta SQL do DocumentDB API](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };

    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API REST
Todas as operações de base de dados do Azure Cosmos podem ser executadas de forma RESTful. Os procedimentos armazenados, acionadores e funções definidas pelo utilizador podem ser registadas sob uma coleção, utilizando o HTTP POST. Segue-se um exemplo de como registar um procedimento armazenado:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


O procedimento armazenado é registado, executando um pedido POST contra o URI dbs/testdb/colls/testColl/sprocs com o corpo que contém o procedimento armazenado para criar. Acionadores e UDFs podem ser registadas da mesma forma ao emitir um pedido POST contra /triggers e /udfs respetivamente.
Isto armazenados procedimento pode, em seguida, ser executado ao emitir um pedido POST em relação a respetiva ligação de recursos:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Aqui, a entrada para o procedimento armazenado é transmitida no corpo do pedido. Tenha em atenção que a entrada é transmitida como uma matriz JSON de parâmetros de entrada. O procedimento armazenado aceita a entrada primeiro como um documento que é um corpo de resposta. A resposta recebido é o seguinte:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Acionadores, ao contrário de procedimentos armazenados, não não possível executar diretamente. Em vez disso, são executados como parte de uma operação num documento. Iremos pode especificar os acionadores para ser executada com um pedido com os cabeçalhos de HTTP. Segue-se o pedido para criar um documento.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Aqui pré-acionar a ser executado com o pedido é especificado no cabeçalho x-ms-documentdb-pre-trigger-include. Proporcionalmente, acionadores pós-implementação são indicados no cabeçalho de x-ms-documentdb-post-trigger-include. Tenha em atenção que ambos pré e pós-implementação acionadores podem ser especificados para um determinado pedido.

## <a name="sample-code"></a>Código de exemplo
Pode encontrar mais exemplos de código do lado do servidor (incluindo [eliminação em massa](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js), e [atualizar](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) no nosso [repositório do GitHub](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Pretende partilhar o procedimento armazenado extraordinário? Envie-num pedido de solicitação! 

## <a name="next-steps"></a>Passos seguintes
Depois de ter um ou mais procedimentos armazenados, acionadores e funções definidas pelo utilizador criadas, pode carregá-los e visualizá-las no portal do Azure utilizando o Explorador de dados.

Pode também considerar as seguintes referências e recursos útil no seu caminho para saber mais sobre a programação do lado do servidor de base de dados de Cosmos do Azure:

* [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md)
* [O DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [262 ECMA de JavaScript](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Extensibilidade de base de dados seguro e portáteis](http://dl.acm.org/citation.cfm?id=276339) 
* [Serviço orientado para a arquitetura de base de dados](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [O tempo de execução de .NET no Microsoft SQL server de alojamento](http://dl.acm.org/citation.cfm?id=1007669)

