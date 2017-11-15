---
title: "Modelação de dados de documento para uma base de dados NoSQL | Microsoft Docs"
description: "Saiba mais acerca da modelação de dados para bases de dados NoSQL"
keywords: "dados de modelação"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig1
documentationcenter: 
ms.assetid: 69521eb9-590b-403c-9b36-98253a4c88b5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2016
ms.author: arramac
ms.openlocfilehash: 041982f251ddee7570619c0e7e61d07906aaed2f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modelação de dados de documento para bases de dados NoSQL
Enquanto sem esquema bases de dados, como a base de dados do Azure Cosmos torná-lo super fácil adotar alterações ao seu modelo de dados deve ainda demora alguns pensar tempo sobre os dados. 

Como dados vai ser armazenadas? Como a aplicação vai obter e consultar dados? É a sua aplicação heavy de leitura ou escrita pesada? 

Depois de ler este artigo, poderá responder às seguintes questões:

* Como necessário pensar sobre um documento numa base de dados de documento?
* O que é modelação de dados e por que motivo deve posso mais importantes para si? 
* Como é que os dados de modelação numa base de dados de documento diferente da base de dados relacional?
* Como posso express relações de dados numa base de dados não relacionais?
* Quando incorporar dados e quando ligar aos dados?

## <a name="embedding-data"></a>Ao incorporar dados
Quando inicia a modelação de dados num arquivo de documentos, tais como a base de dados do Azure Cosmos, tente tratar as entidades como **documentos autónomo** representados no JSON.

Antes que explore demasiado muito além disso, informe-nos reponha alguns passos e ver como podemos poderá modelo algo na base de dados relacional, um assunto muitos dos EUA já estejam familiarizados com. O exemplo seguinte mostra como uma pessoa poderá ser armazenada na base de dados relacional. 

![Modelo de base de dados relacional](./media/documentdb-modeling-data/relational-data-model.png)

Ao trabalhar com bases de dados relacionais, iremos tiver sido taught anos normalizar, normalizar, normalizar.

Normalizing, normalmente, os dados envolve a uma entidade, tal como uma pessoa e danificando-a para baixo para peças discretas de dados. No exemplo acima, uma pessoa pode ter vários registos de detalhes de contacto, bem como vários registos de endereço. Ainda estamos aceda um passo adicional e dividir os detalhes de contacto extraindo mais comuns campos como um tipo. Mesmo para o endereço, cada registo aqui tem um tipo como *home page* ou *negócio* 

Ao servir no local quando normalizing dados **evitar armazenar dados redundantes** em cada registo e em vez disso, consulte a dados. Neste exemplo, ler uma pessoa, com todos os respetivos detalhes de contacto e os endereços, tem de utilizar as associações para agregar eficazmente os dados em tempo de execução.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Atualizar uma única pessoa com os respetivos detalhes de contacto e endereços necessita de operações de escrita em várias tabelas individuais. 

Agora vamos ver como podemos seria modelar os mesmos dados como uma entidade na base de dados de documento autónomo.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {            
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ] 
    }

Utilizar a abordagem acima agora temos **denormalized** a pessoa que registe onde iremos **incorporados** todas as informações relacionadas com esta pessoa, tais como os respetivos detalhes de contacto endereços e, num único JSON documento.
Além disso, porque é não a limitados a um esquema fixo temos a flexibilidade para fazer coisas como tendo os detalhes de contacto de diferentes formas completamente. 

Obter um registo de pessoa completa da base de dados está agora numa única operação em relação a uma única coleção e de um único documento de leitura. A atualização do registo de uma pessoa, com os respetivos detalhes de contacto e os endereços, também é uma operação de escrita única em relação a um único documento.

Por denormalizing dados, a aplicação poderá ter de emitir menos consultas e atualizações para concluir as operações comuns. 

### <a name="when-to-embed"></a>Quando a incorporação
Em geral, utilize os dados incorporados modelos quando:

* Existem **contém** relações entre entidades.
* Existem **um-para-algumas** relações entre entidades.
* Não há dados incorporados que **alterações raramente**.
* É incorporado não aumentam dados **sem limite**.
* Não há dados incorporados, que é **integral** aos dados num documento.

> [!NOTE]
> Normalmente desnormalizadas dados modelos fornecem melhor **ler** desempenho.
> 
> 

### <a name="when-not-to-embed"></a>Quando não incorporar
Enquanto a regra prática numa base de dados de documento é denormalize tudo e incorporar todos os dados para um único documento, isto pode levar a algumas situações que devem ser evitadas.

Colocar este fragmento JSON.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Isto pode dever uma entidade de post com comentários incorporados seria aspeto se podemos foram modelação um blogue típico ou CMS, sistema. O problema com este exemplo está a matriz de comentários **unbounded**, que significa que não há nenhum limite (prática) para o número de comentários pode ter qualquer post único. Isto irá tornar-se um problema conforme o tamanho do documento foi aumentar significativamente.

Conforme o tamanho do documento que aumenta a capacidade para transmitir dados através de transmissão, bem como ler e atualizar o documento, à escala, será afetada.

Neste caso, seria uma melhor a ter em consideração o seguinte modelo.

    Post document:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment documents:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Este modelo tem três mais recente comentários incorporados no post, que é uma matriz com um fixo vinculado neste momento. Os outros comentários são agrupados para lotes de 100 comentários e armazenados nos documentos separados. O tamanho do lote foi escolhido como 100 porque a nossa aplicação fictícia permite ao utilizador a carregar 100 comentários durante um período de tempo.  

Outro cenário onde incorporar dados não são uma boa ideia é quando os dados incorporados são utilizados frequentemente entre documentos e serão alterado frequentemente. 

Colocar este fragmento JSON.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Isto pode representar portefólio as cotações de uma pessoa. Escolhemos incorporar as informações as cotações para cada documento portefólio. Num ambiente onde os dados relacionados mudam frequentemente, como um gráfico de cotações comerciais aplicações, ao incorporar dados que são alterados frequentemente vai significam que lhe está constantemente a atualizar cada documento portefólio sempre que um gráfico de cotações é transacionado.

Gráfico de cotações *zaza* poderá ser transacionadas várias centenas de vezes numa única dia e milhares de utilizadores pode ter *zaza* no respetivo portefólio. Com um modelo de dados como temos para atualizar vários milhares de documentos portefólio demasiadas vezes acima todos os dias à esquerda para um sistema que não dimensiona muito bem. 

## <a id="Refer"></a>Dados de referência
Por isso, ao incorporar dados funciona bem para muitos casos, mas é claro que existem cenários quando denormalizing os dados fará com que problemas mais do que é importante. Por isso, o que podemos fazer agora? 

Bases de dados relacionais não são o único local onde pode criar relações entre entidades. Numa base de dados de documento pode ter informações de um documento que, na verdade, está relacionada com a dados em outros documentos. Agora, posso estou não advocating para um minuto, mesmo que iremos criar sistemas que podem ser melhor adequados para uma base de dados relacional do BD Azure Cosmos ou outra base de dados de documento, mas as relações simples podem ser utilizadas e podem ser bastante útil. 

O JSON abaixo, escolhemos para utilizar o exemplo de um portefólio as cotações de anteriormente mas, desta vez, referem-se ao item as cotações no portefólio em vez de incorporá-lo. Desta forma, quando o item as cotações alterados frequentemente ao longo de dia único documento que tem de ser atualizado é o único documento as cotações. 

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }


Embora um downside imediata para esta abordagem é se a aplicação é necessária para mostrar as informações sobre cada gráfico de cotações mantida quando se apresenta o portefólio de uma pessoa; Neste caso, terá de efetuar várias viagens na base de dados para carregar as informações para cada documento as cotações. Aqui fizemos uma decisão para melhorar a eficiência das operações de escrita, que ocorrem frequentemente ao longo do dia, mas que por sua vez comprometida, as operações de leitura que poderão ter menos impacto no desempenho deste sistema específico.

> [!NOTE]
> Modelos de dados de perdas **pode necessitar de ida e volta mais** para o servidor.
> 
> 

### <a name="what-about-foreign-keys"></a>O que sobre chaves externas?
Porque não existe atualmente nenhum conceito de uma restrição, chave externa ou, caso contrário, quaisquer relações entre documentos que tenham documentos são eficazmente "ligações fracas" e não serão verificadas pela base de dados própria. Se pretender certificar-se de que os dados de que um documento faz referência a existe realmente, terá de fazê-lo na sua aplicação ou através da utilização de acionadores do lado do servidor ou procedimentos armazenados na base de dados do Azure Cosmos.

### <a name="when-to-reference"></a>Quando referenciar
Em geral, utilize dados normalizados modelos quando:

* Que representa **um-para-muitos** relações.
* Que representa **muitos-para-muitos** relações.
* Dados relacionados com **altera com frequência**.
* Pode ser referenciados dados **unbounded**.

> [!NOTE]
> Normalmente, normalizing fornece melhor **escrever** desempenho.
> 
> 

### <a name="where-do-i-put-the-relationship"></a>Onde posso colocar a relação?
O crescimento da relação irão ajudá-lo a determinar qual documento para armazenar a referência.

Se vamos ver JSON abaixo modelos editores e books.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive in to Azure Cosmos DB" }

Se o número de books por publicador for pequeno com o crescimento limitado, em seguida, armazenar a referência de livro existente no documento de fabricante pode ser úteis. No entanto, se o número de books por publicador é unbounded, em seguida, este modelo de dados iria originar matrizes mutável, crescentes, como o documento de publicador de exemplo acima. 

Mudar coisas em torno de um bit resultaria num modelo que representa ainda os mesmos dados, mas agora evita a estas coleções mutável de grandes dimensões.

    Publisher document: 
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents: 
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive in to Azure Cosmos DB", "pub-id": "mspress"}

No exemplo acima, podemos ter removido a coleção unbounded no documento publicador. Em vez disso, temos apenas uma referência para o publicador em cada documento no livro.

### <a name="how-do-i-model-manymany-relationships"></a>Como posso modelar relações muitos:?
Na base de dados relacional *: muitos* relações são, muitas vezes, modeladas com tabelas de associação, que apenas associar registos da outras tabelas em conjunto. 

![Associar tabelas](./media/documentdb-modeling-data/join-table.png)

Poderá ser tempted para replicar a mesma coisa utilizando documentos e produzir um modelo de dados, semelhante ao seguinte.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive in to Azure Cosmos DB" }

    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Isto funciona. No entanto, carregamento ou um autor com os respetivos books ou carregar um livro com o respetivo autor sempre precisaria de, pelo menos, duas consultas adicionais na base de dados. Uma consulta para o documento joining e, em seguida, outra consulta para obter o documento real que está a ser associado. 

Se tudo está a fazer esta tabela de associação é gluing, em conjunto, dois tipos de dados, em seguida, por que motivo não removê-la completamente?
Considere o seguinte.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents: 
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to Azure Cosmos DB", "authors": ["a2"]}

Agora, se posso tivesse um autor, sei imediatamente os livros que escrever e por outro lado se posso tinha um documento de livro carregado seria sei os ids do author(s). Isto poupa essa consulta intermediária contra a redução de tabela de associação viagens a aplicação tem de fazer de arredondar o número do servidor. 

## <a id="WrapUp"></a>Modelos de dados de híbridos
Iremos agora de analisar incorporar (ou denormalizing) e dados de referência (ou normalizing), cada uma tem os respetivos upsides e cada uma tem os compromissos visto que tem. 

Não tem sempre ser ou não ser scared combinar um pouco coisas cópias de segurança. 

Com base nos padrões de utilização específicos e cargas de trabalho poderão existir casos onde a combinação de incorporado da aplicação e dados referenciada faz sentido e foi antecedência para a lógica de aplicação mais simples com menos de servidor arredondar viagens mantendo ainda um bom nível de desempenho.

Considere o seguinte JSON. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",        
        "countOfBooks": 3,
         "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Aqui iremos (sobretudo) tiver seguido modelo incorporado, onde dados a partir de outras entidades estão incorporados no documento de nível superior, mas são referenciados outros dados. 

Se observar o documento do livro, é possível ver alguns interessantes campos quando vamos ver a matriz de autor. Não existe um *id* campo que é o campo que utilizamos para se referem novamente para um documento de autor, prática padrão de um modelo normalizado, e, em seguida, temos também *nome* e *thumbnailUrl*. Iremos foi tiver apenas bloqueada com *id* e à esquerda a aplicação para obter informações adicionais necessitava do documento respetivo autor através da "ligação", mas, dado que a nossa aplicação apresenta o nome do autor e uma imagem em miniatura com cada livro apresentado pode guardamos uma ida e volta ao servidor por livro numa lista por denormalizing **algumas** dados a partir do autor.

Certifique-se, se alterar o nome do autor ou pretendessem atualizar os respetivos fotografia que temos para aceder uma atualização cada livro se alguma vez publicados, mas para a nossa aplicação, com base no pressuposto de que os autores não altere os respetivos nomes muito frequentemente, esta é uma decisão de design aceitável.  

No exemplo são **previamente calculado agregados** valores para guardar dispendiosas processamento de uma operação de leitura. No exemplo, alguns dos dados incorporados no documento de autor são os dados que são calculados em tempo de execução. Sempre que um novo livro for publicado, é criado um documento de livro **e** o campo de countOfBooks está definido para um valor calculado com base no número de documentos do livro que existem para um determinado autor. Esta otimização seria boa nos sistemas de muita leitura onde iremos suportar fazer computações no escritas para otimizar operações de leitura.

É disponibilizada a capacidade de ter um modelo com campos calculados previamente porque a base de dados do Azure Cosmos suporta **transações de documentos múltiplos**. Vários arquivos de NoSQL não é possível fazer transações entre documentos e, por conseguinte, advocate decisões de conceção, como "sempre incorporar tudo" devido a esta limitação. Com o Azure Cosmos DB, pode utilizar acionadores do lado do servidor ou procedimentos armazenados que inserir books e atualizar os autores tudo dentro de uma transação ACID. Agora que não **ter** para incorporar tudo para um documento, mas apenas para Certifique-se de que os dados permanecem consistentes.

## <a name="NextSteps"></a>Passos seguintes
É os takeaways maiores do artigo para compreender que dados de modelação num mundo sem esquema são tão importantes como alguma vez. 

Tal como não é possível único para representar um conjunto de dados num ecrã, não há nenhuma forma única para modelar os seus dados. É necessário compreender a sua aplicação e a forma como irá produzir consumir e processar os dados. Em seguida, aplicando algumas diretrizes aqui apresentadas pode definir sobre como criar um modelo que satisfaz as necessidades de imediato da sua aplicação. Quando precisam de alterar as suas aplicações, pode tirar partido a flexibilidade de uma sem esquema base de dados para adotar a alterar e evoluir facilmente o seu modelo de dados. 

Para saber mais sobre a BD do Cosmos do Azure, consulte o serviço [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) página. 

Para compreender a forma como para partições horizontais seus dados através de várias partições, consulte [criação de partições de dados na base de dados do Azure Cosmos](documentdb-partition-data.md). 
