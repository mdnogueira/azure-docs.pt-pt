<properties 
    pageTitle="Introdução ao DocumentDB, uma base de dados JSON | Microsoft Azure" 
    description="Saiba mais sobre o Azure DocumentDB, uma base de dados NoSQL JSON. Esta base de dados de documentos é criada para macrodados, escalabilidade elástica e elevada disponibilidade." 
    keywords="json database, document database"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>


# Introdução ao DocumentDB: uma Base de dados NoSQL JSON

##O que é o DocumentDB?

O DocumentDB é um serviço de base de dados NoSQL completamente gerido criado para um desempenho previsível e rápido, elevada disponibilidade, dimensionamento elástico, distribuição global e facilidade de desenvolvimento. Como uma base de dados NoSQL sem esquema, o DocumentDB fornece capacidades de consulta SQL avançadas e familiares com latências baixas consistentes em dados JSON, garantindo que 99% das suas leituras são servidas em menos de 10 milissegundos e 99% das suas escritas são servidas em menos de 15 milissegundos. Estas vantagens exclusivas fazem do DocumentDB a opção perfeita para aplicações Web, para telemóveis, jogos e IoT, bem como para muitas outras aplicações que necessitam de dimensionamento totalmente integrado e replicação global.

## Como posso saber mais sobre o DocumentDB? 

Uma forma rápida de saber mais sobre o DocumentDB e vê-lo em ação passa por seguir estes três passos: 

1. Veja o vídeo de dois minutos [O que é o DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/), que apresenta as vantagens da utilização do DocumentDB.
2. Veja o vídeo de três minutos [Criar DocumentDB no Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/), que realça como começar com o DocumentDB através do Portal do Azure.
3. Aceda ao [consulta Playground](http://www.documentdb.com/sql/demo), onde pode percorrer as diversas atividades para saber mais sobre a funcionalidade de consulta avançada disponível no DocumentDB. Em seguida, aceda ao separador Sandbox e execute as suas próprias consultas SQL personalizadas e experimentações com o DocumentDB.

Em seguida, volte a este artigo, onde iremos aprofundar mais o serviço.  

## Que capacidades e funcionalidades chave oferece o DocumentDB?  

O Azure DocumentDB oferece as principais capacidades e vantagens seguintes:

-   **Débito e armazenamento dimensionável:** Aumente ou reduza facilmente verticalmente a sua base de dados DocumentDB JSON para satisfazer as necessidades da sua aplicação. Os seus dados são armazenados em unidades de estado sólido (SSD) para baixas latências previsíveis. O DocumentDB suporta contentores para armazenar dados JSON designados coleções que podem ser ampliadas para tamanhos de armazenamento virtualmente ilimitados e débito. Pode dimensionar o DocumentDB com um desempenho previsível integrado, à medida que a sua aplicação aumentar. 

-   **Replicação de multirregião:** o DocumentDB replica de forma transparente os dados para todas as regiões que associou à conta do DocumentDB, permitindo-lhe desenvolver aplicações que necessitam acesso global aos dados ao fornecer as responsabilidades entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. O DocumentDB fornece ativação pós-falha regional transparente com APIs multi homing e a capacidade e dimensionar débito de forma elástica e armazenamento no globo. Saiba mais em [Distribuir dados globalmente com o DocumentDB](documentdb-distribute-data-globally.md).

-   **Consultas ad hoc com a sintaxe familiar do SQL:** Armazenar documentos JSON heterogéneos no DocumentDB e consultar estes documentos através de uma sintaxe familiar do SQL. O DocumentDB utiliza uma tecnologia de indexação estruturada em registos e sem bloqueio para indexar automaticamente todos os conteúdos do documento. Isto permite consultas em tempo real sem ter de especificar sugestões de esquema, índices secundários ou vistas. Saiba mais em [Consultar DocumentDB](documentdb-sql-query.md). 

-   **Execução de JavaScript na base de dados:** Expressar a lógica de aplicação como procedimentos armazenados, acionadores e funções definidas pelo utilizador (UDFs) com o JavaScript padrão. Isto permite que a lógica da sua aplicação funcione através de dados sem ter de se preocupar sobre o erro de correspondência entre a aplicação e o esquema da base de dados. O DocumentDB fornece a execução transacional completa da lógica de aplicação do JavaScript diretamente dentro do motor da base de dados. A integração profunda do JavaScript permite a execução das operações INSERIR, SUBSTITUIR, ELIMINAR e SELECIONAR de um programa de JavaScript como uma transação isolada. Saiba mais em [programação do lado do servidor do DocumentDB](documentdb-programming.md).

-   **Níveis de consistência sincronizáveis:** Selecione um dos quatro níveis de consistência bem definidos para alcançar compromissos ótimos entre a consistência e o desempenho. Para consultas e operações de leitura, o DocumentDB oferece quatro níveis de consistência distintos: forte, consistência vinculada, sessão e eventual. Estes níveis de consistência granular e bem definidos permitem-lhe efetuar compromissos sonoros entre a consistência, a disponibilidade e a latência. Saiba mais em [Utilizar níveis de consistência para maximizar a disponibilidade e desempenho no DocumentDB](documentdb-consistency-levels.md).

-   **Completamente geridos:** Eliminar a necessidade de gerir bases de dados e recursos de máquinas. O serviço Microsoft Azure sendo completamente gerido, não precisa de gerir máquinas virtuais, implementar e configurar o software, gerir o dimensionamento, ou lidar com atualizações de camada de dados complexas. É feita uma cópia de segurança automática de cada base de dados, sendo protegida contra falhas regionais. Pode facilmente adicionar uma conta DocumentDB e aprovisionar a capacidade conforme necessário, permitindo-lhe focar-se na sua aplicação em vez de explorar e gerir a sua base de dados. 

-   **Abrir por conceção:** Comece a trabalhar rapidamente utilizando as competências e ferramentas existentes. A programação do DocumentDB é simples, acessível e não requer a utilização de novas ferramentas ou a adesão a extensões personalizadas para o JSON ou o JavaScript. Pode aceder a todas as funcionalidades da base de dados, incluindo CRUD, consulta e processamento de JavaScript através de uma interface RESTful HTTP simples. O DocumentDB engloba formatos, idiomas e normas existentes, oferecendo capacidades de base de dados de elevado valor.

-   **Indexação automática:** por predefinição, o DocumentDB [indexa automaticamente](documentdb-indexing.md) todos os documentos na base de dados e não espera nem exige nenhum esquema ou criação de índices secundários. Não pretende indexar tudo? Não se preocupe, também pode [optar por excluir caminhos nos seus ficheiros JSON](documentdb-indexing-policies.md).

##<a name="data-management"></a>Como é que o DocumentDB gere dados?

O Azure DocumentDB gere dados JSON através de recursos de bases de dados bem definidos. Estes recursos são replicados para elevada disponibilidade, sendo exclusivamente endereçáveis pelo respetivo URI lógico. O DocumentDB oferece um modelo de programação HTTP simples com base na RESTful para todos os recursos. 

A conta da base de dados do DocumentDB é um espaço de nomes único que lhe dá acesso ao Azure DocumentDB. Antes de poder criar uma conta de base de dados, terá de ter uma subscrição do Azure, que lhe permitirá aceder a vários serviços do Azure. 

Todos os recursos do DocumentDB são modelados e armazenados como documentos JSON. Os recursos são geridos como itens, ou seja documentos JSON que contêm metadados e como feeds que são coleções de itens. Os conjuntos de itens estão contidos nos respetivos feeds.

A imagem abaixo mostra as relações entre os recursos do DocumentDB:

![A relação hierárquica entre os recursos no DocumentDB, uma base de dados NoSQL JSON][1] 

Uma conta de base de dados consiste num conjunto de bases de dados, em que cada uma contém várias coleções que, por sua vez, podem conter procedimentos armazenados, acionadores, UDFs, documentos e anexos relacionados. Uma base de dados também tem utilizadores associados, cada um com um conjunto de permissões para aceder a várias outras coleções, procedimentos armazenados, acionadores, UDFs, documentos ou anexos. Enquanto as bases de dados, os utilizadores, as permissões e as coleções são recursos definidos pelo sistema com esquemas bem conhecidos - os documentos, procedimentos armazenados, acionadores, UDFs e anexos contêm conteúdos JSON arbitrários definidos pelo utilizador.  

##<a name="develop"></a> Como posso programar aplicações com o DocumentDB?

O Azure DocumentDB expõe recursos através de uma API REST que pode ser chamada por qualquer idioma com capacidade de efetuar pedidos HTTP/HTTPS. Além disso, o DocumentDB oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam muitos aspetos do trabalho com o Azure DocumentDB, processando detalhes como detalhes como a colocação em cache do endereço, a gestão de exceções, tentativas automáticas e assim sucessivamente. Atualmente, as bibliotecas estão disponíveis para os seguintes idiomas e plataformas:  

Transferência | Documentação
--- | ---
[SDK .NET](http://go.microsoft.com/fwlink/?LinkID=402989) | [Biblioteca de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[SDK Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) | [Biblioteca Node. js](http://azure.github.io/azure-documentdb-node/)
[SDK Java](http://go.microsoft.com/fwlink/?LinkID=402380) | [Biblioteca Java](http://azure.github.io/azure-documentdb-java/)
[SDK JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Biblioteca JavaScript](http://azure.github.io/azure-documentdb-js/)
n/d | [SDK JavaScript do lado do servidor](http://azure.github.io/azure-documentdb-js-server/)
[SDK Python](https://pypi.python.org/pypi/pydocumentdb) | [Biblioteca Python](http://azure.github.io/azure-documentdb-python/)

Para além das operações básicas de criação, leitura, atualização e eliminação, o DocumentDB fornece uma interface de consulta avançada do SQL para obter documentos JSON e suporte do lado do servidor para a execução transacional da lógica de aplicação do JavaScript. As interfaces de execução de consulta e script estão disponíveis através de todas as bibliotecas de plataforma, bem como as APIs REST. 

### Consulta SQL
O Azure DocumentDB suporta a consulta de documentos através de uma linguagem SQL, enraizada no sistema de tipo JavaScript e expressões com suporte para consultas relacionais, hierárquicas e espaciais. O idioma de consulta do DocumentDB é uma interface simples, mas poderosa, para consultar documentos JSON. O idioma suporta um subconjunto de gramática ANSI SQL e adiciona uma integração profunda do objeto JavaScript, matrizes, construção de objeto e invocação de função. O DocumentDB fornece o modelo de consulta sem qualquer esquema explícito ou indexação de sugestões do programador.

As Funções Definidas pelo Utilizador (UDFs) podem ser registadas com o DocumentDB e referenciadas como parte de uma consulta do SQL, expandindo deste modo a gramática para suportar a lógica personalizada da aplicação. Estas UDFs são escritas como programas JavaScript e executadas na base de dados. 

Para programadores de .NET, o DocumentDB também oferece um fornecedor de consulta do LINQ como parte do [SDK do .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### Transações e execução de JavaScript
O DocumentDB permite-lhe escrever a lógica da aplicação como programas com nome escritos inteiramente no JavaScript. Estes programas estão registados para uma coleção e podem emitir operações de base de dados em documentos de uma dada coleção. O JavaScript pode ser registado para ser executado como um acionador, procedimento armazenado ou função definida pelo utilizador. Os acionadores e procedimentos armazenados podem criar, ler, atualizar e eliminar documentos enquanto as funções definidas pelo utilizador executam a lógica de execução da consulta sem acesso à escrita na coleção.

A execução do JavaScript no DocumentDB é modelada após os conceitos suportados pelos sistemas de base de dados relacional, o JavaScript sendo um substituto moderno para Transact-SQL. Toda a lógica do JavaScript é executada numa transação do ambiente ACID, com o isolamento do instantâneo. Durante a sua execução, se o JavaScript emitir uma exceção, toda a transação será abortada.

## Passos seguintes
Já tem uma conta do Azure? Então, pode começar a utilizar o DocumentDB no [Portal do Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB), [criando uma conta de base de dados DocumentDB](documentdb-create-account.md).

Não tem uma conta do Azure? Pode:

- Inscrever-se numa [avaliação gratuita do Azure](https://azure.microsoft.com/free/), que lhe oferece 30 dias e $200 para experimentar todos os serviços do Azure. 
- Se tiver uma subscrição do MSDN, poderá beneficiar de [$150 em créditos mensais gratuitos do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para utilizar em qualquer serviço Azure. 

Em seguida, quando estiver pronto para saber mais, aceda ao nosso [percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para percorrer todos os recursos de aprendizagem disponíveis para si. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 



<!--HONumber=Sep16_HO3-->


