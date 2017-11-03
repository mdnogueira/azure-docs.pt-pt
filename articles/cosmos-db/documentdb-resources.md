---
title: Conceitos e modelo de recursos do Azure do Cosmos DB | Microsoft Docs
description: "Saiba mais sobre o modelo de hierárquico da BD do Azure Cosmos de bases de dados, coleções, a função definida pelo utilizador (UDF), documentos, permissões para gerir recursos e muito mais."
keywords: "Modelo hierárquico cosmosdb, do azure, Microsoft azure"
services: cosmos-db
documentationcenter: 
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: anhoh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 53059c09d454dedbd65ef188a0dd91d497b0e502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Conceitos-chave e modelo de recursos hierárquicos do Azure Cosmos DB
As entidades de base de dados que gere a base de dados do Azure Cosmos são referidas como **recursos**. Cada recurso é identificado de forma exclusiva por um URI lógico. Pode interagir com os recursos utilizando verbos HTTP padrão, cabeçalhos de pedido/resposta e códigos de estado. 

Ao ler este artigo, poderá responder às seguintes questões:

* O que é o modelo de recursos de BD Cosmos do Azure?
* Quais são sistema definido recursos, por oposição a recursos definidos pelo utilizador?
* Como posso resolver um recurso?
* Como funcionam com coleções
* Como funcionam com procedimentos armazenados, acionadores e funções definidas pelo utilizador (UDFs)?

## <a name="hierarchical-resource-model"></a>Modelo de recursos hierárquica
Como o diagrama seguinte ilustra, a BD do Cosmos Azure hierárquica **modelo de recursos** consiste em conjuntos de recursos numa conta de base de dados, cada endereçável através de um URI lógico e estável. Um conjunto de recursos vai ser referido como um **feed** neste artigo. 

> [!NOTE]
> BD do Azure do Cosmos oferece um protocolo TCP altamente eficiente que também seja RESTful no seu modelo de comunicação, disponível através de [cliente do DocumentDB .NET API](documentdb-sdk-dotnet.md).
> 
> 

![Modelo de recursos hierárquica de Cosmos BD do Azure][1]  
**Modelo de recursos hierárquica**   

Para começar a trabalhar com recursos, terá [criar uma conta de base de dados](create-documentdb-dotnet.md) através da sua subscrição do Azure. Uma conta de base de dados pode ser composto por um conjunto de **bases de dados**, cada um possuindo vários **coleções**, cada do qual por sua vez, conter **procedimentos armazenados, aciona, UDFs, documentos**relacionados e **anexos**. Uma base de dados está também associado **utilizadores**, cada um com um conjunto de **permissões** para aceder a coleções, procedimentos armazenados, acionadores, UDFs, documentos ou anexos. Enquanto as bases de dados, os utilizadores, permissões e as coleções são recursos definidos pelo sistema com esquemas bem conhecidos, documentos e anexos contêm arbitrários, conteúdos JSON definidos pelo utilizador.  

| Recurso | Descrição |
| --- | --- |
| Conta de base de dados |Uma conta de base de dados está associada um conjunto de bases de dados e uma quantidade fixa de armazenamento de BLOBs para anexos. Pode criar uma ou mais contas de base de dados através da sua subscrição do Azure. Para obter mais informações, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Base de Dados |Uma base de dados é um contentor lógico do armazenamento de documentos particionado em coleções. Também é um contentor de utilizadores. |
| Utilizador |O espaço de nomes lógico para o âmbito de permissões. |
| Permissão |Um token de autorização associado a um utilizador para aceder a um recurso específico. |
| Coleção |Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript. Uma coleção é uma entidade faturável, onde o [custo](performance-levels.md) é determinado pelo nível de desempenho associado à coleção. As coleções podem abranger uma ou mais partições/servidores e podem dimensionar para processar volumes praticamente ilimitados de armazenamento ou débito. |
| Procedimento Armazenado |Lógica da aplicação escrita em JavaScript, que é registado com uma coleção e uma forma executado dentro do motor de base de dados. |
| Acionador |Lógica da aplicação escrita em JavaScript executado antes ou depois de um insert, substituir ou eliminar a operação. |
| UDF |Lógica da aplicação escrita em JavaScript. UDFs permitem-lhe modelar um operador de consulta personalizada e, deste modo, expandir as principais a linguagem de consulta do DocumentDB API. |
| Documento |Conteúdos (arbitrários) JSON definidos pelo utilizador. Por predefinição, nenhum esquema tem de ser definido nem índices secundários precisam de ser fornecido para todos os documentos adicionados a uma coleção. |
| Anexo |Um anexo é um documento especial que contém referências e os metadados associados para o blob/suporte de dados externo. O programador pode optar por ter o blob gerido pela base de dados do Cosmos ou armazene-o com um fornecedor de serviço blob externo, como o OneDrive, Dropbox, etc. |

## <a name="system-vs-user-defined-resources"></a>Sistema vs recursos definidos pelo utilizador
Recursos, tais como contas de base de dados, bases de dados, coleções, os utilizadores, as permissões, procedimentos armazenados, acionadores e UDFs - têm um esquema fixo e são denominados recursos do sistema. Em contrapartida, os recursos, tais como documentos e anexos tem sem restrições no esquema e são exemplos de recursos definida pelo utilizador. No Cosmos DB, sistema e o utilizador recursos definidos são representados e geridos como JSON padrão compatível. Todos os recursos, sistema ou utilizador definido, tem as seguintes propriedades comuns.

> [!NOTE]
> Tenha em atenção que o sistema de todos os gerado propriedades num recurso é adicionado o prefixo com um caráter de sublinhado (_) na respetiva representação JSON.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propriedade</strong></p></td>
            <td valign="top"><p><strong>Utilizador definível ou gerada pelo sistema?</strong></p></td>
            <td valign="top"><p><strong>Objetivo</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>Sistema gerado, o identificador exclusivo e hierárquica do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>ETag o recurso necessário para o controlo de simultaneidade otimista</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>Última timestamp atualizado do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Self</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>URI endereçável exclusivo do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ID</p></td>
            <td valign="top"><p>Qualquer um dos</p></td>
            <td valign="top"><p>Nome exclusivo do recurso (com o valor de chave de partição mesmo) definido pelo utilizador. Se o utilizador não especifica um id, um id será gerada pelo sistema</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Representação de transmissão de recursos
BD do cosmos não mandatar quaisquer extensões proprietários para as codificações de especiais ou padrão de JSON; funciona com o padrão de documentos JSON em conformidade.  

### <a name="addressing-a-resource"></a>Um recurso de endereçamento
Todos os recursos são URI endereçável. O valor da **Self** propriedade de um recurso representa o URI relativo do recurso. O formato do URI consiste no /\<feed\>/ {_rid} segmentos de caminho:  

| Valor da Self | Descrição |
| --- | --- |
| /DBS |Feed de bases de dados com uma conta de base de dados |
| /DBS/ {dbName} |Base de dados com um id de correspondência do valor {dbName} |
| /colls/ /DBS/ {dbName} |Feed de coleções de uma base de dados |
| /colls/ /DBS/ {dbName} {collName} |Coleção com um id de correspondência do valor {collName} |
| /colls/ /DBS/ {dbName} {collName} / docs |Feed de documentos numa coleção |
| /DBS/ {dbName} /colls/ {collName} /docs/ {docId} |O documento com um id de correspondência do valor {documento} |
| /users/ /DBS/ {dbName} |Feed de utilizadores numa base de dados |
| /users/ /DBS/ {dbName} {userId} |Utilizador com um id de correspondência do valor {utilizador} |
| /users/ /DBS/ {dbName} {userId} / permissões |Feed de permissões de um utilizador |
| /DBS/ {dbName} /users/ {userId} /permissions/ {permissionId} |Permissão com um id de correspondência do valor {permissão} |

Cada recurso tem um nome de utilizador exclusivo definido exposto através da propriedade de id. Nota: para documentos, se o utilizador não especificar um id nossos SDKs suportados irão gerar automaticamente um id exclusivo para o documento. O id é uma cadeia de definidas pelo utilizador, até 256 carateres que seja exclusivo dentro do contexto de um recurso principal específico. 

Cada recurso também tem um identificador de recurso hierárquica gerada pelo sistema (também referido como um RID), que está disponível através da propriedade _rid. O RID codifica toda a hierarquia de um determinado recurso e é uma representação interna conveniente utilizada para impor a integridade referencial de forma distribuída. O RID é exclusivo dentro de uma conta de base de dados e é utilizado internamente pelo Cosmos DB para encaminhamento eficiente, sem necessidade de pesquisas de partição cruzada. Os valores das propriedades _rid e o Self são representações alternativas e canónicos de um recurso. 

Suporta as APIs REST dos recursos de endereçamento e encaminhamento de pedidos pelo id e as propriedades de _rid.

## <a name="database-accounts"></a>Contas de base de dados
Pode aprovisionar uma ou mais Cosmos DB da base de dados contas através da sua subscrição do Azure.

Pode criar e gerir contas de base de dados de base de dados do Cosmos através do Portal do Azure em [http://portal.azure.com/](https://portal.azure.com/). Criar e gerir uma conta de base de dados exigir acesso administrativo e só podem ser efetuada na sua subscrição do Azure. 

### <a name="database-account-properties"></a>Propriedades da conta de base de dados
Como parte do aprovisionamento e gestão de uma conta de base de dados que pode configurar e ler as propriedades seguintes:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nome da propriedade</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Política de consistência</p></td>
            <td valign="top"><p>Defina esta propriedade para configurar o nível de consistência predefinida para todas as coleções na sua conta de base de dados. Pode substituir o nível de consistência numa base por pedido utilizando o cabeçalho de pedido [x-ms-consistência-nível]. <p><p>Tenha em atenção que esta propriedade aplica-se apenas a <i>recursos definido pelo utilizador</i>. Sistema de todos os recursos definidos estão configurados para suportar operações de leitura/consultas com consistência forte.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chaves de autorização</p></td>
            <td valign="top"><p>Estas são as chaves primária e secundárias só de leitura e mestre que fornecem acesso administrativo para todos os recursos sob a conta de base de dados.</p></td>
        </tr>
    </tbody>
</table>

Tenha em atenção que, além de aprovisionamento, configurar e gerir a sua conta de base de dados do Portal do Azure, pode também programaticamente criar e gerir contas de base de dados de base de dados do Cosmos utilizando o [APIs REST do Azure Cosmos DB](/rest/api/documentdb/) , bem como [SDKs de cliente](documentdb-sdk-dotnet.md).  

## <a name="databases"></a>Bases de Dados
Uma base de dados do Cosmos DB é um contentor lógico de uma ou mais coleções e os utilizadores, conforme mostrado no diagrama seguinte. Pode criar qualquer número de bases de dados com uma conta de base de dados de BD do Cosmos sujeitas a limites de oferta.  

![Modelo de hierárquico coleções e conta de base de dados][2]  
**Uma base de dados é um contentor lógico de utilizadores e de coleções**

Uma base de dados pode conter armazenamento virtualmente ilimitados documentos particionado em coleções.

### <a name="elastic-scale-of-a-azure-cosmos-db-database"></a>Escala elástica de uma base de dados de base de dados do Azure Cosmos
Uma base de dados do Cosmos DB é elástico por predefinição – entre a alguns GB e petabytes de armazenamento de documentos SSD de segurança e débito aprovisionado. 

Ao contrário de uma base de dados no RDBMS tradicionais, uma base de dados na base de dados do Cosmos não tem um âmbito para um único computador. Com base de dados do Cosmos, à medida que necessidades de dimensionamento da sua aplicação aumentar, pode criar mais coleções, as bases de dados ou ambos. Realmente, várias aplicações de terceiros primeiro na Microsoft utiliza o Azure Cosmos DB uma escala de consumidor através da criação de bases de dados de base de dados do Azure Cosmos extremamente grandes cada contentor milhares de coleções com terabytes de armazenamento de documentos. Pode aumentar ou diminuir o uma base de dados ao adicionar ou remover coleções para satisfazer os requisitos de escala da sua aplicação. 

Pode criar qualquer número de coleções dentro de uma base de dados sujeita a oferta. Cada coleção tem armazenamento SSD, segurança e débito aprovisionado para si, consoante a camada de desempenho selecionados.

Uma base de dados de base de dados do Azure Cosmos também é um contentor de utilizadores. Um utilizador, por sua vez, é um espaço de nomes de lógico de um conjunto de permissões que fornece acesso em coleções, documentos e anexos e autorização detalhada.  

Como com outros recursos no modelo de recursos do Azure Cosmos DB, bases de dados podem ser criados, substituídas, eliminar, ler ou enumerado facilmente utilizando o [REST APIs](/rest/api/documentdb/) ou qualquer uma do [SDKs de cliente](documentdb-sdk-dotnet.md). BD do Azure do Cosmos garante a consistência forte para ler ou consultar os metadados de um recurso de base de dados. Eliminar uma base de dados automaticamente assegura que não é possível aceder a qualquer um dos utilizadores contidos ou coleções.   

## <a name="collections"></a>Coleções
Uma coleção de BD do Cosmos é um contentor para os documentos JSON. 

### <a name="elastic-ssd-backed-document-storage"></a>Armazenamento de documentos de segurança de SSD elástico
Uma coleção é elástica intrinsecamente - automaticamente aumenta e diminui como adicionar ou remover documentos. As coleções são recursos lógicos e podem abranger uma ou mais partições físicas ou servidores. O número de partições dentro de uma coleção é determinado pelo DB Cosmos com base no tamanho de armazenamento e o débito aprovisionado da sua coleção. Cada partição na base de dados do Cosmos tem uma quantidade fixa de armazenamento SSD de segurança associada e é replicada para elevada disponibilidade. Gestão de partição é totalmente gerida por base de dados do Azure Cosmos e não precisa de escrever código complexo ou gerir as partições. As coleções do cosmos DB são **praticamente ilimitados** em termos de armazenamento e débito. 

### <a name="automatic-indexing-of-collections"></a>A indexação automática de coleções
BD do Cosmos do Azure é um sistema de verdadeiro sem esquema da base de dados. Não assume nem requer nenhum esquema para os documentos JSON. Como adicionar documentos numa coleção, base de dados do Azure Cosmos indexa automaticamente os mesmos e estão disponíveis para consulta. A indexação automática de documentos sem necessidade de esquema ou índices secundários é uma funcionalidade chave da base de dados do Azure Cosmos e está ativada por técnicas de manutenção do índice com otimização de escrita, liberte de bloqueio e estruturada em registos. BD do Azure do Cosmos suporta um volume de tempos de resposta extremamente rápidas escritas enquanto ainda processa consultas consistentes. Armazenamento de documentos e índice são utilizados para calcular o armazenamento consumido por cada coleção. Pode controlar os armazenamento e desempenho compromissos associados a indexação ao configurar a política de indexação de uma coleção. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configurar a política de indexação de uma coleção
A política de indexação de cada coleção permite-lhe efetuar o desempenho e armazenamento compromissos associados a indexação. As seguintes opções estão disponíveis como parte da configuração de indexação:  

* Escolha se a coleção indexa automaticamente os todos os documentos ou não. Por predefinição, todos os documentos são automaticamente indexados. Pode optar por desativar a indexação automática e adicionar seletivamente apenas os documentos específicos para o índice. Por outro lado, pode escolher seletivamente a excluir apenas a documentos específica. Pode fazê-lo através da definição da propriedade para ser VERDADEIRO ou FALSO em indexingPolicy de uma coleção automática e utilizar o cabeçalho de pedido [x-ms-indexingdirective] ao inserir, substituir ou eliminar um documento.  
* Escolha se pretende incluir ou excluir caminhos específicos ou padrões nos seus documentos do índice. Pode conseguir isto por definição includedPaths e excludedPaths no indexingPolicy de uma coleção, respetivamente. Também pode configurar os armazenamento e desempenho compromissos para consultas de intervalo e hash de padrões de caminho específico. 
* Escolha entre síncrona (consistente) e atualizações de índice (lento) assíncrona. Por predefinição, o índice é atualizado em sincronia cada inserir, substituir ou eliminar um documento para a coleção. Isto permite que as consultas que respeite o mesmo nível de consistência que lê o documento. Enquanto a base de dados do Azure Cosmos é escrita otimizada e suporta volumes constante de escritas documento juntamente com a manutenção de índice síncrona e que serve consultas consistentes, pode configurar determinadas coleções para atualizar o respetivo índice lenta. A indexação lento boosts ainda mais o desempenho de escrita e é ideal para cenários de ingestão em massa para principalmente leitura pesado coleções.

A política de indexação pode ser alterada, executando um PUT na coleção. Isto pode ser conseguido quer através do [cliente SDK](documentdb-sdk-dotnet.md), a [Portal do Azure](https://portal.azure.com) ou [REST APIs](/rest/api/documentdb/).

### <a name="querying-a-collection"></a>Consulta de uma coleção
Os documentos numa coleção podem ter esquemas arbitrários e podem consultar documentos numa coleção sem fornecer nenhum esquema ou compromisso índices secundários. Pode consultar a coleção a utilizar o [API de DocumentDB do Azure Cosmos DB: referência de sintaxe SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx), que fornece operadores avançados de geográficos, hierárquicos e relacionais e extensibilidade através de UDFs com base em JavaScript. A gramática JSON permite-lhe modelar documentos JSON como árvores com etiquetas como nós de árvore. Isto é forem explorado tanto por técnicas de indexação automática da API do DocumentDB, bem como dialeto SQL da API do DocumentDB. O idioma de consulta DocumetDB API consiste em três aspetos principais:   

1. Um pequeno conjunto de operações de consulta que mapeiam naturalmente para a estrutura de árvore, incluindo consultas hierárquicas e projeções. 
2. Um subconjunto de operações relacionais incluindo composição, filtro, projeções, agrega e associações automática. 
3. Com base em JavaScript pura UDFs que funcionam com (1) e (2).  

O modelo de consulta de base de dados do Azure Cosmos tenta trazem um equilíbrio entre funcionalidade, eficiência e na simplicidade. O motor de base de dados de base de dados do Azure Cosmos nativamente compila e executa as instruções de consulta SQL. Pode consultar uma coleção a utilizar o [REST APIs](/rest/api/documentdb/) ou qualquer uma do [SDKs de cliente](documentdb-sdk-dotnet.md). O SDK .NET é fornecido com um fornecedor LINQ.

> [!TIP]
> Pode experimentar a API do DocumentDB e executar consultas SQL no nosso conjunto de dados a [Query Playground](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Transações de documentos múltiplos
Transações da base de dados fornecem um modelo de programação seguro e previsível para lidar com alterações simultâneas nos dados. No RDBMS, a forma tradicional ao escrever a lógica de negócio é escrever **procedimentos armazenados** e/ou **acionadores** e envie-o para o servidor de base de dados para a execução transacional. No RDBMS, o Programador de aplicações é necessária para lidar com duas linguagens de programação diferentes: 

* A idioma (por exemplo, JavaScript, Python, c#, Java, etc.) de programação de aplicações (não transacional)
* T-SQL, a linguagem de programação transacional que é executada nativamente pela base de dados

Em virtude da respetivo compromisso profundo JavaScript e JSON diretamente dentro do motor de base de dados, base de dados do Azure Cosmos fornece um modelo de programação intuitivo para JavaScript em execução com base na lógica da aplicação diretamente nas coleções em termos de procedimentos armazenados e acionadores. Este procedimento permite que ambos os procedimentos seguintes:

* Controlar a implementação eficiente de simultaneidade, recuperação, a indexação de gráficos de objetos JSON diretamente no motor de base de dados automática
* Naturalmente expressa o fluxo de controlo, variável de âmbito, atribuição e a integração de excepção a processar primitivos com transações da base de dados diretamente em termos de JavaScript linguagem de programação

A lógica do JavaScript registada um nível de coleção, em seguida, pode emitir operações de base de dados em documentos de uma dada coleção. BD do Azure do Cosmos encapsula num wrapper implicitamente a com base em JavaScript acionadores dentro de um ambiente transações ACID com isolamento do instantâneo e de procedimentos armazenados em documentos numa coleção. Durante a sua execução, se o JavaScript emitir uma exceção, toda a transação será abortada. O modelo de programação resultante é muito simple ainda poderosas. Os programadores de JavaScript obter um modelo de programação "durável" ao ainda através do respetivo construções de idioma familiar e primitivos de biblioteca.   

Permite que a capacidade de executar o JavaScript diretamente dentro do motor da base de dados no mesmo espaço de endereços do conjunto de memória intermédia performant e a execução transacional de operações de base de dados contra os documentos de uma coleção. Além disso, um compromisso avançado para o JSON faz com que o motor de base de dados de base de dados do Cosmos e JavaScript elimina quaisquer impedance incompatibilidade entre os sistemas de tipo de aplicação e a base de dados.   

Depois de criar uma coleção, pode registar os procedimentos armazenados, acionadores e UDFs com uma coleção a utilizar o [REST APIs](/rest/api/documentdb/) ou qualquer uma do [SDKs de cliente](documentdb-sdk-dotnet.md). Após o registo, pode referenciar e execute-los. Considere o seguinte procedimento armazenado escrito inteiramente no JavaScript, o código abaixo aceita dois argumentos (nome do livro e nome do autor) e cria um novo documento, consulta para um documento e, em seguida, atualiza-lo-tudo numa transação ACID implícita. Em qualquer momento durante a execução, se é emitida uma exceção de JavaScript, toda a transação interrompe.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);

                        context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

O cliente pode "são enviados" a lógica do JavaScript acima para a base de dados para a execução transacional através de HTTP POST. Para obter mais informações sobre como utilizar métodos HTTP, consulte [RESTful interações com os recursos de base de dados do Azure Cosmos](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Tenha em atenção que porque a base de dados compreende nativamente JSON e JavaScript, não há nenhum incompatibilidade de tipo de sistema, nenhum "OR" ou um mapeamento da magic de geração de código necessárias.   

Acionadores e procedimentos armazenados interagem com uma coleção e os documentos numa coleção através de um modelo de objeto definida especificamente, o que expõe o contexto de coleção atual.  

Podem ser criadas coleções na DocumentDB API, eliminado, leitura ou enumerado facilmente utilizando o [REST APIs](/rest/api/documentdb/) ou qualquer uma do [SDKs de cliente](documentdb-sdk-dotnet.md). A API do DocumentDB fornece sempre consistência forte para ler ou consultar os metadados de uma coleção. Eliminar automaticamente uma coleção, assegura que não pode aceder a qualquer um dos documentos, os anexos, procedimentos armazenados, acionadores, UDFs contidos.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Os procedimentos armazenados, acionadores e funções definidas pelo utilizador (UDF)
Tal como descrito na secção anterior, pode escrever a lógica da aplicação para ser executado diretamente dentro de uma transação dentro do motor de base de dados. Lógica da aplicação pode ser escrita inteiramente no JavaScript e pode ser modelada como um procedimento armazenado, acionador ou um UDF. O código JavaScript dentro de um procedimento armazenado ou um acionador pode inserir, substituir, eliminar, ler ou consulta documentos numa coleção. Por outro lado, o JavaScript dentro de um UDF não é possível inserir, substituir ou eliminar documentos. UDFs enumerar os documentos do conjunto de resultados de uma consulta e produzem outro conjunto de resultados. Para vários inquilinos, a base de dados do Azure Cosmos impõe uma governação de recursos de reserva strict com base. Cada um procedimento armazenado, acionador ou de um UDF obtém um quantum fixo de recursos do sistema operativo para realizar o seu trabalho. Além disso, procedimentos armazenados, acionadores ou UDFs não é possível associar contra externas bibliotecas de JavaScript e são blacklisted excederem os orçamentos de recurso atribuídos aos mesmos. Pode registar, anular o registo de procedimentos armazenados, acionadores ou UDFs com uma coleção com as APIs REST.  Após o registo de um UDF, acionador ou um procedimento armazenado é pré-compilado e armazenado como código de byte que obtém executado mais tarde. A seguinte secção mostram como pode utilizar o Azure Cosmos DB JavaScript SDK para registar, executar e anular o registo de um procedimento armazenado, acionador e um UDF. O SDK de JavaScript é um wrapper simple através do [REST APIs](/rest/api/documentdb/). 

### <a name="registering-a-stored-procedure"></a>Registar um procedimento armazenado
Registo de um procedimento armazenado cria um novo recurso do procedimento armazenado numa coleção através de HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();

            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Executar um procedimento armazenado
Execução de um procedimento armazenado é feita através da emissão de um HTTP POST em relação a um recurso de procedimento armazenado existente através da transmissão de parâmetros para o procedimento no corpo do pedido.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>A anulação do registo um procedimento armazenado
A anulação do registo um procedimento armazenado simplesmente é feita ao emitir um eliminar HTTP em relação a um recurso de procedimento armazenado existente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registar um pré-acionador de
Registo de um acionador é feito através da criação de um novo recurso do acionador numa coleção através de HTTP POST. Pode especificar se o acionador não é um pré ou um acionador de post e o tipo de operação pode ser associado com (por exemplo, criar, substituir, eliminar ou todos).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Executar um pré-acionador de
Execução de um acionador é feita ao especificar o nome de um acionador existente no momento da emitir o pedido POST/PUT/eliminar de um recurso de documentos através de cabeçalho do pedido.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>A anulação do registo um pré-acionador de
A anulação do registo um acionador simplesmente é feita através de emitir um eliminar HTTP em relação a um recurso de Acionador existente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registar um UDF
Registo de um UDF é feito através da criação de um novo recurso UDF numa coleção através de HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Executar um UDF como parte da consulta
Um UDF pode ser especificado como parte da consulta SQL e é utilizado como uma forma para expandir as principais [linguagem de consulta SQL para a API do DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>A anulação do registo um UDF
A anulação do registo um UDF simplesmente é feita ao emitir um eliminar HTTP em relação a um recurso UDF existente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Embora os fragmentos acima mostrou o registo (POST), a anulação do registo (PUT), a leitura/lista (GET) e a execução (POST) através de [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), também pode utilizar o [REST APIs](/rest/api/documentdb/) ou outros [ cliente SDKs](documentdb-sdk-dotnet.md). 

## <a name="documents"></a>Documentos
Pode inserir, substituir, eliminar, ler, enumerar e consultar documentos JSON arbitrários numa coleção. BD do Azure do Cosmos não mandatar nenhum esquema e não necessita de índices secundários para suportar a consulta através de documentos numa coleção. O tamanho máximo para um documento é 2 MB.   

Ser um serviço de base de dados verdadeiramente aberta, base de dados do Azure Cosmos não invent quaisquer tipos de dados especializadas (por exemplo, tempo de data) ou codificações específicas para documentos JSON. Tenha em atenção que o Azure Cosmos DB não requerem qualquer especiais convenções JSON para codify as relações entre vários documentos; a sintaxe SQL da base de dados do Azure Cosmos fornece muito poderosa consulta hierárquica e relacional operadores a documentos de consulta e projeto sem quaisquer anotações especiais nem a necessidade de codify relações entre documentos utilizando único propriedades.  

Como todos os outros recursos, documentos podem ser criados, substituído, eliminar, ler, enumerar e consultado facilmente através de REST APIs ou de qualquer um do [SDKs de cliente](documentdb-sdk-dotnet.md). Eliminar um documento de forma instantânea liberta a quota de todos os anexos aninhados correspondente. O nível de consistência de leitura de documentos segue a política de consistência na conta de base de dados. Esta política pode ser substituída numa base por pedido, consoante os requisitos de consistência de dados da sua aplicação. Ao consultar documentos, a consistência de leitura segue o modo de indexação definido na coleção. "Consistente", para este acompanha política de consistência da conta. 

## <a name="attachments-and-media"></a>Anexos e o suporte de dados
BD do Cosmos do Azure permite-lhe armazenar blobs/suporte de dados binário com a base de dados do Azure Cosmos (o máximo de 2 GB por conta) ou para sua própria loja de multimédia remoto. Também permite que representam os metadados de um suporte de dados em termos de um documento especial, denominado anexo. Um anexo do BD Azure Cosmos é um documento (JSON) especial que referencia o suporte de dados/blob armazenado noutro local. Um anexo é simplesmente um documento especial que capture os metadados (por exemplo, localização, autor etc.) de um suporte de dados armazenado no armazenamento de suporte de dados remota. 

Considere uma aplicação de ler sociais que utiliza a base de dados do Azure Cosmos para armazenar anotações de tinta digital e realça os metadados, incluindo comentários, marcadores, as classificações, likes/dislikes etc. associados para um e-book transferível de um determinado utilizador.   

* O conteúdo do livro em si é armazenado no armazenamento do suporte de dados está disponível como parte da conta de base de dados de base de dados do Azure Cosmos ou um arquivo de suporte de dados remota. 
* Uma aplicação pode armazenar metadados de cada utilizador como um documento distinto – por exemplo, os metadados de Joe para book1 são armazenados num documento referenciado pelo /colls/joe/docs/book1. 
* Anexos de apontar para as páginas de conteúdo de um livro especificada de um utilizador são armazenados no documento correspondente /colls/joe/docs/book1/chapter1 por ex., /colls/joe/docs/book1/chapter2 etc. 

Tenha em atenção que os exemplos indicados acima utilizam ids amigáveis a transmitir a hierarquia de recursos. Os recursos são acedidos através de APIs REST através de ids exclusivos de recursos. 

O suporte de dados é gerida pelo Azure Cosmos DB, a propriedade _media o anexo fará referência o suporte de dados pelo seu respetivo URI. BD do Cosmos do Azure irá garantir a libertação da memória recolher o suporte de dados quando todas as referências pendentes são ignoradas. BD do Azure do Cosmos automaticamente gera o anexo ao carregar o novo suporte de dados e preenche o _media para apontar para o suporte de dados adicionados recentemente. Se optar por armazenar o suporte de dados num arquivo de blob remoto gerido por si (por exemplo, OneDrive, o Storage do Azure, DropBox, etc.), pode continuar a utilizar os anexos para referenciar o suporte de dados. Neste caso, irá criar o anexo por si e preencher a respetiva propriedade _media.   

Tal como acontece com todos os outros recursos, anexos podem ser criados, substituído, eliminaram, ler ou enumerado facilmente através de REST APIs ou de qualquer um dos SDKs de cliente. Tal como acontece com documentos, o nível de consistência de leitura de anexos segue a política de consistência na conta de base de dados. Esta política pode ser substituída numa base por pedido, consoante os requisitos de consistência de dados da sua aplicação. Quando consultar os anexos, a consistência de leitura segue o modo de indexação definido na coleção. "Consistente", para este acompanha política de consistência da conta. 
 

## <a name="users"></a>Utilizadores
Um utilizador de base de dados do Azure Cosmos representa um espaço de nomes lógico para agrupar as permissões. Um utilizador de base de dados do Azure Cosmos pode corresponder a um utilizador de um sistema de gestão de identidade ou uma função de aplicação predefinido. Para a base de dados do Azure Cosmos, um utilizador simplesmente representa uma abstração para agrupar um conjunto de permissões de uma base de dados.   

Para implementar vários inquilinos na sua aplicação, pode criar utilizadores na base de dados do Azure Cosmos que corresponde aos utilizadores reais ou os inquilinos da sua aplicação. Em seguida, pode criar permissões para um determinado utilizador que correspondem ao controlo de acesso através de várias coleções, documentos, os anexos, etc.   

Como as suas aplicações precisam de dimensionamento com o crescimento de utilizador, pode adotar várias formas de partições horizontais os dados. Pode modelo cada um dos seus utilizadores da seguinte forma:   

* Cada utilizador mapeia para uma base de dados.
* Cada utilizador mapeia para uma coleção. 
* Documentos correspondente a vários utilizadores aceda a uma coleção dedicada. 
* Documentos correspondente a vários utilizadores aceder a um conjunto de coleções.   

Independentemente da estratégia de fragmentação específico que escolher, pode modelar os seus utilizadores reais como os utilizadores na base de dados de base de dados do Azure Cosmos e associar permissões detalhadas para cada utilizador.  

![Coleções de utilizadores][3]  
**Estratégias de fragmentação e modelação de utilizadores**

Como todos os outros recursos, utilizadores do BD Azure Cosmos podem ser criados, substituídas, eliminaram, ler ou enumerado facilmente através de REST APIs ou de qualquer um dos SDKs de cliente. BD do Azure do Cosmos fornece sempre consistência forte para ler ou consultar os metadados de um recurso de utilizador. É importante, apontar que a eliminação de um utilizador automaticamente assegura que não é possível aceder a qualquer uma das permissões contidas. Apesar da base de dados do Azure Cosmos reclama a quota das permissões como parte do utilizador eliminado em segundo plano, as permissões eliminadas ficam disponíveis novamente para que possa utilizar.  

## <a name="permissions"></a>Permissões
De uma perspetiva de controlo de acesso, recursos, tais como contas de base de dados e permissão de bases de dados, os utilizadores são considerados *administrativo* recursos, uma vez que estes requerem permissões administrativas. Por outro lado, recursos, incluindo as coleções, documentos, anexos, procedimentos armazenados, acionadores, e UDFs são âmbito sob uma determinada base de dados e considerados *recursos de aplicação*. Correspondente para os dois tipos de recursos e as funções que aceder aos mesmos (nomeadamente o administrador e utilizador), o modelo de autorização define dois tipos de *chaves de acesso*: *chave mestra* e  *a chave de recurso*. A chave mestra é uma parte da conta de base de dados e é fornecida para o programador (ou o administrador) que é a conta de base de dados de aprovisionamento. Esta chave mestra tem semânticas de administrador, em que pode ser utilizado para autorizar o acesso aos recursos administrativos e de aplicações. Em contrapartida, uma chave de recurso é uma chave de acesso granulares que permite o acesso a um *específico* recurso de aplicação. Assim,-captura a relação entre o utilizador de uma base de dados e as permissões que o utilizador tem sobre um recurso específico (por exemplo, coleção, documentos, anexo, procedimento armazenado, acionador ou UDF).   

A única forma de obter uma chave de recurso é através da criação de um recurso de permissão sob um determinado utilizador. Tenha em atenção que para criar ou obter uma permissão, uma chave mestra tem de ser apresentada no cabeçalho de autorização. Um recurso de permissão vincula o recurso, o acesso e o utilizador. Depois de criar um recurso de permissão, o utilizador só tem de apresentar a chave de recursos associados para poder obter acesso ao recurso relevante. Por conseguinte, uma chave de recursos pode ser visualizada como uma representação lógica e compacta do recurso de permissão.  

Como com todos os outros recursos, permissões na base de dados do Azure Cosmos podem ser criadas, substituído, eliminaram, ler ou enumerado facilmente através de REST APIs ou de qualquer um dos SDKs de cliente. BD do Azure do Cosmos fornece sempre consistência forte para ler ou consultar os metadados de uma permissão. 

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como trabalhar com recursos utilizando comandos HTTP no [RESTful interações com os recursos de base de dados do Azure Cosmos](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

