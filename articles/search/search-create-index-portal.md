---
title: "Criar um índice (portal - pesquisa do Azure) | Microsoft Docs"
description: "Crie um índice utilizando o Portal do Azure."
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Criar um índice da Azure Search utilizando o Portal do Azure
> [!div class="op_single_selector"]
> * [Descrição geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Utilizar o estruturador de índice incorporada no portal do Azure para criar protótipos ou crie um [índice de pesquisa](search-what-is-an-index.md) para ser executada no serviço da Azure Search. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe um [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e [serviço da Azure Search](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Localizar o serviço de pesquisa
1. Inicie sessão para a página de portal do Azure e rever o [procurar serviços para a sua subscrição](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecione o seu serviço do Azure Search.

## <a name="name-the-index"></a>Nome do índice

1. Clique em de **Adicionar índice** botão na barra de comando na parte superior da página.
2. Nome do seu índice da Azure Search. 
   * Começar com uma letra.
   * Utilize apenas letras minúsculas, dígitos ou traços ("-").
   * Limite o nome a 60 carateres.

  O nome do índice passa a fazer parte do URL do ponto final utilizado em ligações para o índice e para enviar pedidos HTTP na API de REST da Azure Search.

## <a name="define-the-fields-of-your-index"></a>Definir os campos do seu índice

Composição de índice inclui um *campos de coleção* que define os dados pesquisáveis no seu índice. Mais especificamente, especifica a estrutura de documentos que carregar separadamente. A coleção de campos inclui campos obrigatórios e opcionais, com o nome e escritas com atributos de índice para determinar a forma como o campo pode ser utilizado.

1. No **Adicionar índice** painel, clique em **campos >** para abrir gradualmente o painel de definição de campo. 

2. Aceite o gerado *chave* campo do tipo EDM. Por predefinição, o campo é denominado *id* , mas pode alterá-lo, desde que a cadeia satisfaça [regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Um campo de chave é obrigatório para todos os índices de pesquisa do Azure e tem de ser uma cadeia.

3. Adicione campos para especificar completamente os documentos que irá carregar. Se consistem em documentos um *id*, *nome átrios*, *endereço*, *Cidade*, e *região*, criar um campo correspondente para cada um no índice. Reveja o [conceber de orientações na secção abaixo](#design) para ajudar a definir os atributos.

4. Opcionalmente, adicione internamente quaisquer campos que são utilizados em expressões de filtro. Atributos em campo podem ser definidos para excluir os campos de operações de pesquisa.

5. Quando terminar, clique em **OK** para guardar e criar o índice.

## <a name="tips-for-adding-fields"></a>Sugestões para adicionar campos

Criar um índice no portal é exigente em termos de teclado. Minimiza passos seguindo este fluxo de trabalho:

1. Em primeiro lugar, crie a lista de campos ao introduzir os nomes e definir os tipos de dados.

2. Em seguida, utilize as caixas de verificação na parte superior de cada atributo para em massa ativar a definição para todos os campos e, em seguida, desmarque seletivamente caixas para o insuficiente de campos que não o requerem. Por exemplo, os campos de cadeia são pesquisáveis normalmente. Como tal, pode clicar em **recuperável** e **pesquisável** ao devolver os valores do campo nos resultados da pesquisa, bem como permitir a pesquisa em texto completo em campo. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Orientações de conceção para a definição de atributos

Embora possa adicionar novos campos em qualquer altura, definições de campo existente estão bloqueadas para a duração do índice. Por este motivo, os programadores normalmente utilizam o portal para criação de índices simples, testes ideias ou utilizando as páginas do portais para procurar uma definição. Iteração frequente através de um design do índice é mais eficiente se siga uma abordagem baseada no código de modo a que pode reconstrua o índice facilmente.

Analisadores e dos sugestores estão associados com campos antes do índice é guardado. Lembre-se de que clicar em cada página separadores para adicionar analisadores de idioma ou dos sugestores à sua definição de índice.

Campos de cadeia são frequentemente marcados como **pesquisável** e **recuperável**.

Os campos utilizados para restringir os resultados da pesquisa incluem **ordenável**, **Filterable**, e **Facetável**.

Atributos de campo determinam como um campo é utilizado, por exemplo, se é utilizada na pesquisa de texto completo, navegação por facetas, operações de ordenação e assim sucessivamente. A tabela seguinte descreve cada atributo.

|Atributo|Descrição|  
|---------------|-----------------|  
|**pesquisável**|Texto completo pesquisável, sujeita a análise lexical como quebra de palavra durante a indexação. Se definir um campo pesquisável para um valor como "sunny dia", internamente, será dividido em tokens individuais "sunny" e "dia". Para obter mais informações, consulte [funciona de pesquisa de texto completo como](search-lucene-query-architecture.md).|  
|**filtrável**|Referenciada na **$filter** consultas. Filtráveis campos do tipo `Edm.String` ou `Collection(Edm.String)` não são submetidos a quebra de palavra, pelo que é comparações para apenas corresponde exato. Por exemplo, se definir esse um campo f "sunny dia", `$filter=f eq 'sunny'` encontrará não corresponde, mas `$filter=f eq 'sunny day'` será. |  
|**ordenável**|Por predefinição o sistema ordena os resultados por pontuação, mas pode configurar a ordenação com base nos campos de documentos. Os campos do tipo `Collection(Edm.String)` não pode ser **ordenável**. |  
|**facetável**|Normalmente utilizado numa apresentação de resultados de pesquisa que inclui uma contagem de acessos por categoria (por exemplo, hotéis numa cidade específica). Esta opção não pode ser utilizada com campos do tipo `Edm.GeographyPoint`. Os campos do tipo `Edm.String` que são **filtrável**, **ordenável**, ou **facetável** pode ter um máximo de 32 kilobytes de comprimento. Para obter mais informações, consulte [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**chave**|Identificador exclusivo de documentos no índice. Deve ser selecionado exatamente um campo como o campo de chave e tem de ser do tipo `Edm.String`.|  
|**recuperável**|Determina se o campo pode ser devolvido num resultado da pesquisa. Isto é útil quando pretender utilizar um campo (tais como *margem de lucros*) como um filtro, ordenação ou a classificação de mecanismo, mas não pretende que o campo seja visível para o utilizador final. Este atributo tem de ser `true` para `key` campos.|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Crie o índice de hotéis utilizado nas secções de API de exemplo

Documentação da API de pesquisa do Azure inclui exemplos de código com um simples *hotéis* índice. As capturas de ecrã abaixo, pode ver a definição do índice, incluindo o analisador de idioma francês especificado durante a definição do índice, que pode recriá-lo como um exercício prática no portal.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Passos seguintes

Depois de criar um índice da Azure Search, pode mover para o próximo passo: [carregar dados pesquisáveis para o índice](search-what-is-data-import.md).

Em alternativa, também pode demorar mais profunda veja os índices. Para além da coleção de campos, um índice também especifica analisadores, dos sugestores, perfis de classificação e as definições CORS. O portal fornece páginas de separadores para definir os elementos mais comuns: campos, analisadores e dos sugestores. Para criar ou modificar a outros elementos, pode utilizar a REST API ou o .NET SDK.

## <a name="see-also"></a>Consultar também

 [Como funciona a pesquisa em texto completo](search-lucene-query-architecture.md)  
 [REST API do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

