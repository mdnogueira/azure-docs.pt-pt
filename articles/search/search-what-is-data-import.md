---
title: Carregamento de dados na Azure Search | Documentos do Microsoft
description: "Saiba como carregar dados para um índice da Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search"></a>Carregar dados para a Azure Search
> [!div class="op_single_selector"]
> * [Descrição geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Existem duas formas de preencher índices com os seus dados. A primeira opção consiste no envio manual dos seus dados para o índice através da [API REST](search-import-data-rest-api.md) ou do [SDK .NET](search-import-data-dotnet.md) da Azure Search. A segunda é [apontar uma origem de dados suportada](search-indexer-overview.md) para o índice e permitir que o Azure Search extraia automaticamente os dados.

## <a name="push-data-to-an-index"></a>Enviar dados para um índice
Esta abordagem diz respeito ao envio através de programas dos seus dados para a Azure Search para os tornar disponíveis para pesquisa. Para aplicações com requisitos de latência muito baixos (por exemplo, se quiser que as operações de pesquisa estejam sincronizadas com bases de dados de inventário dinâmicas), o modelo de envio é a única opção.

Pode utilizar a [API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou o [SDK .NET](search-import-data-dotnet.md) para enviar dados para um índice. Atualmente não existe qualquer suporte de ferramentas para o envio de dados através do portal.

Esta abordagem é mais flexível do que o modelo de extração, pois pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, consoante o limite que ocorrer primeiro). O modelo de envio também permite carregar documentos para a Azure Search independentemente do local de armazenamento dos dados.

O formato de dados que o Azure Search compreende é o JSON e todos os documentos do conjunto de dados têm de ter campos que mapeiem para os campos definidos no esquema do índice. 

## <a name="pull-data-into-an-index"></a>Extrair dados para um índice
O modelo de extração pesquisa uma origem de dados suportada e carrega automaticamente os dados para o seu índice. No Azure Search, esta função é implementada através de *indexadores*, atualmente disponíveis para [Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md), [Armazenamento de Tabelas](search-howto-indexing-azure-tables.md), [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer), [Base de Dados SQL do Azure e SQL Server em VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 

Os indexadores ligam índices a uma origem de dados (geralmente, uma tabela, vista ou estrutura equivalente) e mapeiam os campos da origem para os campos equivalentes nos índices. Durante a execução, o conjunto de linhas é automaticamente transformado em JSON e carregado para o índice especificado. Todos os indexadores suportam o agendamento, de modo a que possa especificar a frequência com que os dados devem ser atualizados. A maioria dos indexadores disponibilizam o registo de alterações, se as origens de dados o suportarem. Os indexadores, através do registo de alterações e eliminações aos documentos existentes, além do reconhecimento de novos documentos, suprimem a gestão ativa dos dados no índice. 

A funcionalidade de indexador está exposta no [portal do Azure](search-import-data-portal.md), na [API REST](/rest/api/searchservice/Indexer-operations) e no [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations). 

Uma vantagem de utilizar o portal é que, geralmente, o Azure Search consegue gerar um esquema de índice predefinido por si, ao ler os metadados do conjunto de dados de origem. Pode modificar o índice gerado até o índice ser processado, após o qual as únicas edições ao esquema permitidas são as que não requerem nova indexação. Se as alterações que quiser fazer influenciarem o esquema diretamente, terá de recriar o índice. 

Depois de o índice ser preenchido, pode utilizar o **Explorador de Pesquisas** na barra de comandos do portal como passo de verificação.

## <a name="query-an-index-using-search-explorer"></a>Consultar índices com o Explorador de Pesquisas

Uma forma rápida de fazer uma verificação preliminar no carregamento do documento é utilizar o **Explorador de Pesquisas** no portal. O explorador permite-lhe consultar índices sem ter de escrever qualquer código. A experiência de pesquisa baseia-se em predefinições, como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e o [parâmetro de consulta searchMode](/rest/api/searchservice/search-documents) predefinido. Os resultados são devolvidos em JSON, de modo a que possa inspecionar todo o documento.

> [!TIP]
> Vários [exemplos de código do Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de dados incorporados ou disponíveis a pronto, o que lhe dá uma forma fácil de começar. O portal também disponibiliza um indexador e uma origem de dados de exemplo, que consiste num pequeno conjunto de dados de imobiliário (com o nome “realestate-us-sample"). Quando executar o indexador pré-configurado na origem de dados de exemplo, é criado um índice e carregado com documentos que podem, depois, ser consultados no Explorador de Pesquisas ou através de códigos que escrever.