<properties
    pageTitle="Criar um índice da Azure Search | Microsoft Azure | Serviço de pesquisa em nuvem alojado"
    description="O que é um índice da Azure Search e como é utilizado?"
    services="search"
    documentationCenter=""
authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"/>

# Criar um índice da Azure Search
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

## O que é um índice?

Um *índice* é um arquivo persistente de *documentos* e outras construções utilizado por um serviço da Azure Search. Um documento é um único dado pesquisável sobre a unidade no índice. Por exemplo, um retalhista de comércio eletrónico pode ter um documento para cada artigo que vende, uma empresa jornalística pode ter um documento para cada artigo, etc. Mapeamento destes conceitos para equivalentes da base de dados mais familiares: um *índice* é, conceptualmente, semelhante a uma *tabela* e os *documentos* são quase equivalentes a *linhas* numa tabela.

Quando adiciona/carrega documentos e submete consultas de pesquisa para a Azure Search, submete pedidos para um índice específico no serviço de pesquisa. 

## Tipos de campo e atributos num índice da Azure Search

Na definição do esquema, tem de especificar o nome, o tipo e os atributos de cada campo do índice. O tipo de campo classifica os dados armazenados nesse campo. Os atributos são definidos em campos individuais para especificar o modo de utilização do campo. As tabelas seguintes enumeram os tipos e os atributos que pode especificar.


### Tipos de campo
|Tipo|Descrição|
|------------|-----------|
|*Edm.String*|Texto que pode, opcionalmente, ser atomizado para pesquisa em texto completo (separação de palavras, lematização, etc.).|
|*Coleção (Edm.String)*|Uma lista de cadeias que pode, opcionalmente, ser atomizada para pesquisa em texto completo. Não existe um limite superior teórico do número de itens numa coleção, contudo, o limite superior de 16 MB de tamanho de payload aplica-se às coleções.|
|*Edm.Boolean*|Contém valores verdadeiro/falso.|
|*Edm.Int32*|Valores inteiros de 32 bits.|
|*Edm.Int64*|Valores inteiros de 64 bits.|
|*Edm.Double*|Dados numéricos de dupla precisão.|
|*Edm.DateTimeOffset*|Valores de data/hora representados no formato OData V4 (por ex. `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Um ponto que representa uma localização geográfica no mundo.|

Pode encontrar mais informações detalhadas sobre os [tipos de dados suportados no MSDN](https://msdn.microsoft.com/library/azure/dn798938.aspx) da Azure Search.



### Atributos do campo
|Atributo|Descrição|
|------------|-----------|
|*Chave*|Uma cadeia que fornece o ID único de cada documento, utilizada para a procura de documentos. Todos os índices devem ter uma chave. Apenas um campo pode ser uma chave e o tipo deve ser definido para Edm.String.|
|*Recuperável*|Especifica se um campo pode ser devolvido num resultado da pesquisa.|
|*Filtrável*|Permite que o campo seja utilizado nas consultas de filtro. |
|*Ordenável*|Permite a uma consulta ordenar os resultados da pesquisa através deste campo.|
|*Facetável*|Permite a um campo ser utilizado numa estrutura de [navegação por facetas](search-faceted-navigation.md) para filtragem do utilizador auto-direcionada. Geralmente os campos com valores repetitivos que pode utilizar para agrupar vários documentos (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas.|
|*Pesquisável*|Marca o campo como pesquisável no texto completo.|

Pode encontrar mais informações detalhadas sobre os [atributos de índice no MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx) da Azure Search.



## Orientações para definir um esquema de índice

Na criação do índice, reserve tempo na fase de planeamento para analisar cada decisão. É importante ter em consideração as suas necessidades comerciais e experiência do utilizador de pesquisa quando estiver a criar o seu índice, uma vez que cada campo deve receber os [atributos adequados](https://msdn.microsoft.com/library/azure/dn798941.aspx). A alteração de um índice após a respetiva implementação implica reconstruir e recarregar os dados.


Caso os requisitos de armazenamento de dados alterem ao longo do tempo, pode aumentar ou diminuir a capacidade adicionando ou removendo partições. Para obter detalhes, consulte [Gerir o serviço de Pesquisa no Azure](search-manage.md) ou [Limites de Serviço](search-limits-quotas-capacity.md).



<!--HONumber=Jun16_HO2-->


