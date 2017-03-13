---
title: Tutorial NoSQL C++ para o DocumentDB | Microsoft Docs
description: "Um tutorial NoSQL C++ que cria uma base de dados C++ e uma aplicação de consola com um SDK apoiado pelo DocumentDB para C++. O DocumentDB é um serviço de bases de dados NoSQL de escala planetária."
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 78c3da6fd83a6fca0351a90846d10acd82924be3
ms.lasthandoff: 03/08/2017


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>Tutorial NoSQL C++: aplicação de consola DocumentDB C++
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

Bem-vindo ao tutorial de C++ para o SDK apoiado pelo Azure DocumentDB para C++! Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do DocumentDB, incluindo uma base de dados C++.

Iremos abranger:

* Criação e ligação a uma conta DocumentDB
* Configurar a sua aplicação
* Criar uma base de dados C++ do DocumentDB
* Criação de uma coleção
* Criação de documentos JSON
* Consulta da coleção
* Substituir um documento
* Eliminar um documento
* Eliminar a base de dados C++ do DocumentDB

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/stalker314314/DocumentDBCpp). Consulte o artigo [Obter a solução completa](#GetSolution) para instruções rápidas.

Quando tiver concluído o tutorial C++, utilize os botões de voto na parte inferior desta página para nos enviar os seus comentários. 

Se preferir que entremos em contacto consigo diretamente, não hesite em indicar o seu endereço de e-mail nos seus comentários ou [contacte-nos aqui](https://www.research.net/r/8BKRJ3Z). 

Agora comecemos!

## <a name="prerequisites-for-the-c-tutorial"></a>Pré-requisitos do tutorial C++
Certifique-se de que tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/), com os componentes de linguagem C++ instalados.

## <a name="step-1-create-a-documentdb-account"></a>Passo 1: Criar uma conta DocumentDB
Criemos uma conta DocumentDB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a sua aplicação C++](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupC++"></a>Passo 2: Configurar a aplicação C++
1. Abra o Visual Studio e, no menu **Ficheiro**, clique em **Novo** e em **Projeto**. 
2. Na janela **Novo Projeto**, no painel **Instalado**, expanda **Visual C++**, clique em **Win32** e clique em **Aplicação de Consola Win32**. Dê o nome hellodocumentdb ao projeto e clique em **OK**. 
   
    ![Captura de ecrã do assistente Novo projeto](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. Quando o Assistente de Aplicação Win32 for iniciado, clique em **Concluir**.
4. Depois de o projeto ter sido criado, abra o gestor de pacotes NuGet, ao clicar com o botão direito do rato no projeto **hellodocumentdb**, no **Explorador de Soluções** e em **Gerir Pacotes NuGet**. 
   
    ![Captura de ecrã que mostra Gerir Pacote NuGet no menu do projeto](media/documentdb-cpp-get-started/nuget.png)
5. No separador **NuGet: hellodocumentdb**, clique em **Procurar** e procure *documentdbcpp*. Nos resultados, selecione DocumentDbCPP, conforme mostrado na captura de ecrã seguinte. Este pacote instala referências ao C++ REST SDK, que é uma dependência do DocumentDbCPP.  
   
    ![Captura de ecrã que mostra o pacote DocumentDbCPP realçado](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    Assim que os pacotes tiverem sido adicionados ao seu projeto, está pronto para começar a escrever código.   

## <a id="Config"></a>Passo 3: Copiar os detalhes da ligação do portal do Azure para a base de dados do DocumentDB
Abra o [portal do Azure](https://portal.azure.com) e aceda à conta de bases de dados NoSQL (DocumentDB) que criou. Vamos precisar do URI e da chave primária do portal do Azure no próximo passo, para estabelecer ligação a partir do fragmento de código C++. 

![URI e chave primária do DocumentDB no portal do Azure](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>Passo 4: Ligar a uma conta do DocumentDB
1. Adicione os cabeçalhos e espaços de nomes seguintes ao código de origem, a seguir a `#include "stdafx.h"`.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Em seguida, adicione o código seguinte à sua função principal e substitua a configuração da conta e a chave primária de modo a corresponderem às definições do DocumentDB do passo 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Agora que tem o código para inicializar o cliente do documentdb, vamos ver como utilizar os recursos do DocumentDB.

## <a id="CreateDBColl"></a>Passo 5: Criar uma base de dados C++ e uma coleção
Antes de realizar este passo, vamos ver de que forma é que as bases de dados, as coleções e os documentos interagem, para os utilizadores que estão agora a começar a utilizar o DocumentDB. Uma [base de dados](documentdb-resources.md#databases) é um contentor lógico de armazenamento de documentos particionado em coleções. Uma [coleção](documentdb-resources.md#collections) é um contentor de documentos JSON e a lógica da aplicação JavaScript associada. Pode saber mais sobre os conceitos e o modelo de recursos hierárquicos do DocumentDB em [DocumentDB hierarchical resource model and concepts (Conceitos e modelo de recursos hierárquicos do DocumentDB)](documentdb-resources.md).

Para criar uma base de dados e uma coleção correspondente, adicione o código seguinte ao fim da sua função principal. É criada uma base de dados com o nome “FamilyRegistry” e uma coleção com o nome “FamilyCollection”, através da utilização da configuração do cliente que declarou no passo anterior.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>Passo 6: Criar um documento
Os [documentos](documentdb-resources.md#documents) são conteúdos JSON definidos pelo utilizador (arbitrários). Pode agora inserir um documento no DocumentDB. Pode criar um documento ao copiar o código seguinte para o fim da função principal. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Para resumir, este código cria uma base de dados, uma coleção e documentos do DocumentDB, que pode consultar no Explorador de Documentos do portal do Azure. 

![Tutorial C++ - diagrama que ilustra a relação hierárquica entre a conta, a base de dados, a coleção e os documentos](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a id="QueryDB"></a>Passo 7: Consultar os recursos do DocumentDB
O DocumentDB suporta [consultas](documentdb-sql-query.md) extensas de documentos JSON armazenados em cada coleção. O código de exemplo seguinte mostra uma consulta criada com sintaxe SQL do DocumentDB, a qual pode ser executada nos documentos que criámos no passo anterior.

A função aceita como argumentos o identificador exclusivo ou o ID de recurso da base de dados e da coleção, juntamente com o cliente do documento. Adicione este código antes da função principal.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>Passo 8: Substituir um documento
O DocumentDB suporta a substituição de documentos JSON, conforme demonstrado no código seguinte. Adicione este código a seguir à função executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>Passo 9: Eliminar um documento
O DocumentDB suporta a eliminação de documentos JSON. Para tal, copie e cole o código seguinte a seguir à função replacedocument. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>Passo 10: Eliminar uma base de dados
Eliminar a base de dados criada remove a base de dados e todos os recursos subordinados (coleções, documentos, etc.).

Copie e cole o fragmento de código seguinte (limpeza de função) a seguir à função deletedocument para remover a base de dados e todos os recursos subordinados.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>Passo 11: Executar a aplicação de consola C++ de uma só vez!
Já adicionámos código para criar, consultar, modificar e eliminar diferentes recursos do DocumentDB.  Vamos agora ligar tudo ao adicionar chamadas para estas variadas funções a partir da função principal emhellodocumentdb.cpp, juntamente com algumas mensagens de diagnósticos.

Podemos fazê-lo ao substituir a função principal da aplicação pelo código seguinte, o que substitui o account_configuration_uri e a primary_key que copiou para o código no Passo 3, pelo que deve guardar essa linha ou copiar os valores novamente a partir do portal. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Deverá, agora, conseguir criar e executar o seu código no Visual Studio ao premir F5 ou, em alternativa, na janela do terminal, ao localizar a aplicação e executar o executável. 

Deverá ver o resultado da sua aplicação Introdução. A saída deve corresponder à captura de ecrã seguinte.

![Saída da aplicação C++ do DocumentDB](media/documentdb-cpp-get-started/docdbconsole.png)

Parabéns! Concluiu o tutorial C++ e tem a sua primeira aplicação de consola do DocumentDB!

## <a id="GetSolution"></a>Obter a solução completa do tutorial C++
Para criar a solução GetStarted que contém todos os exemplos deste artigo, precisa do seguinte:

* [Conta do DocumentDB][documentdb-create-account].
* A solução [GetStarted](https://github.com/stalker314314/DocumentDBCpp) está disponível no GitHub.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [monitorizar uma conta DocumentDB](documentdb-monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na secção Desenvolver da [página de documentação do DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md



