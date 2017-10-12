---
title: Importar dados para o Azure Search no portal do | Microsoft Docs
description: "Utilize o Assistente de Dados de Importação de Azure Search no Portal do Azure para pesquisar dados do Azure no NoSQL do Azure Cosmos DB, no armazenamento de Blobs do Azure, no armazenamento de tabelas, Base de Dados SQL e no SQL Server em VMs do Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: Azure Portal
ms.assetid: f40fe07a-0536-485d-8dfa-8226eb72e2cd
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: a3e6dd66197a17bfdc80c04130e198b787692a58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="import-data-to-azure-search-using-the-portal"></a>Importar dados para a Pesquisa do Azure através do portal
O Portal do Azure fornece um assistente de **Importação de dados** no dashboard do Azure Search para carregar dados para um índice. 

  ![Importação de Dados na barra de comandos][1]

Internamente, o assistente configura e invoca um *indexador*, automatizando vários passos do processo de indexação: 

* Ligar a uma origem de dados externos na mesma subscrição do Azure
* Gerar um esquema de índice modificável com base na estrutura dos dados de origem
* Carregue documentos JSON para um índice com um conjunto de linhas obtido a partir da origem de dados

Pode tentar terminar este fluxo de trabalho com dados de exemplo no Azure Cosmos DB. Visite [Introdução ao Azure Search no Portal do Azure](search-get-started-portal.md) para obter instruções.

> [!NOTE]
> Pode iniciar o assistente **Importar dados** a partir do dashboard do Azure Cosmos DB, para simplificar a indexação dessa origem de dados. Na navegação à esquerda, aceda a **Coleções** > **Adicionar Azure Search** para começar.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Origens de dados suportadas pelo Assistente de Importação de Dados
O Assistente para Importar Dados suporta as seguintes origens de dados: 

* Base de Dados SQL do Azure
* Dados relacionais do SQL Server numa VM do Azure
* Azure Cosmos DB
* Armazenamento de Blobs do Azure
* Armazenamento de Tabelas do Azure

Um conjunto de dados bidimensional é uma entrada necessária. Só é possível importar a partir de uma única tabela, de uma vista de base de dados ou de uma estrutura de dados equivalente. Deve criar esta estrutura de dados antes de executar o assistente.

## <a name="connect-to-your-data"></a>Ligar aos dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com) e abra o dashboard de serviço. Pode clicar em **Mais serviços** na barra de atalhos para pesquisar os "serviços de pesquisa" existentes na subscrição atual. 
2. Clique em **Importar Dados** na barra de comandos para abrir gradualmente o painel Importar Dados.  
3. Clique em **Ligar aos seus dados** para especificar uma definição de origem de dados utilizada por um indexador. Para origens de dados de subscrição de dimensionamento, o assistente, normalmente, pode detetar e ler informações de ligação, minimizando os requisitos de configuração geral.

|  |  |
| --- | --- |
| **Origem de dados existente** |Se já tiver indexadores definidos no seu serviço de pesquisa, pode selecionar uma definição de origem de dados para outra importação existente. |
| **Base de Dados SQL do Azure** |Podem ser especificados um nome do serviço, credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados na página ou através de uma cadeia de ligação do ADO.NET. Escolha a opção de cadeia de ligação para ver ou personalizar propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| **SQL Server numa VM do Azure** |Especifique um nome de serviço totalmente qualificado, um ID de utilizador e palavra-passe e uma base de dados como uma cadeia de ligação. Para utilizar esta origem de dados, deve ter instalado anteriormente um certificado no arquivo local que encripta a ligação. Para obter instruções, consulte [Ligação da VM do SQL para o Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| **Azure Cosmos DB** |Os requisitos incluem a conta, a base de dados e a coleção. Todos os documentos na coleção serão incluídos no índice remissivo. Pode definir uma consulta para nivelamento ou filtrar o conjunto de linhas ou para detetar documentos alterados para as operações de atualização de dados subsequentes. |
| **Armazenamento de Blobs do Azure** |Os requisitos incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar a porção do diretório virtual do nome como uma pasta no contentor. Consulte [Armazenamento de Blobs de Indexação](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
| **Armazenamento de Tabelas do Azure** |Os requisitos incluem a conta de armazenamento e um nome de tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto de tabelas. Consulte [Armazenamento de Tabelas de Indexação](search-howto-indexing-azure-tables.md) para obter mais informações. |

## <a name="customize-target-index"></a>Personalizar o índice de destino
Um índice preliminar, normalmente, é inferido do conjunto de dados. Adicionar, editar ou eliminar campos para concluir o esquema. Além disso, definir atributos ao nível de campo para determinar os respetivos comportamentos de pesquisa subsequentes.

1. Em **Personalizar o índice de destino**, especifique o nome e uma **Chave** utilizada para identificar exclusivamente cada documento. A chave tem de ser uma cadeia. Se os valores de campo incluem espaços ou traços não se esqueça de definir opções avançadas em **Importar dados** para suprimir a verificação de validação para estes carateres.
2. Reveja os campos restantes. O nome de campo e tipo são normalmente preenchidos para si. Pode alterar o tipo de dados até que o índice seja criado. Se o alterar posteriormente irá necessitar de uma reconstrução.
3. Defina o índice para cada campo:
   
   * Recuperável devolve o campo nos resultados da pesquisa.
   * Filtrável permite que o campo seja referenciado em expressões de filtro.
   * Ordenável permite que o campo seja utilizado numa consulta.
   * Facetável ativa o campo para navegação por facetas.
   * Pesquisável ativa a pesquisa de texto completo.
4. Clique no separador **Analisador** se pretende especificar um analisador de idioma ao nível do campo. Apenas podem ser especificados, de momento, analisadores de idiomas. Ao utilizar um analisador personalizado ou um analisador que não seja de idioma como Palavra-chave, Padrão e assim sucessivamente, necessitará um código.
   
   * Clique em **Pesquisável** para designar a pesquisa em texto completo no campo e ativar a lista pendente do Analisador.
   * Escolha o analisador que pretende. Veja [Create an index for documents in multiple languages (Criar um índice para documentos em vários idiomas)](search-language-support.md) para obter detalhes.
5. Clique em **Sugestor** par ativar as sugestões de consulta de escrita antecipada nos campos selecionados.

## <a name="import-your-data"></a>Importar dados
1. Em **Importar dados**, forneça um nome para o indexador. Tenha em consideração que a que o produto do assistente de Importação de Dados é um indexador. Mais tarde, se pretender ver ou editar, selecioná-lo-á a partir do portal, em vez de executar o assistente novamente. 
2. Especifique a agenda, que se baseia fuso horário regional em que o serviço é aprovisionado.
3. Defina opções avançadas para especificar os limiares de se a indexação pode continuar no caso de que um documento seja removido. Além disso, pode especificar se os campos de **Chave** têm autorização para conter espaços e barras.  
4. Clique em **OK** para criar o índice e importar os dados.

Pode monitorizar a indexação no portal. Enquanto os documentos são carregados, a contagem do documento irá aumentar para o índice que definiu. Por vezes, demora alguns minutos para a página do portal escolher as atualizações mais recentes.

O índice está pronto para consultar assim que todos os documentos são carregados.

## <a name="query-an-index-using-search-explorer"></a>Consultar índices com o Explorador de Pesquisas

O portal inclui o **Explorador de Pesquisas**, para que possa consultar índices sem ter de escrever código. Pode utilizar o [Explorador de Pesquisas](search-explorer.md) em qualquer índice.

A experiência de pesquisa baseia-se em predefinições, como a [sintaxe simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e a predefinição [parâmetro de consulta searchMode (https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Os resultados são devolvidos em JSON, num formato verboso, de modo a que possa inspecionar todo o documento.

## <a name="edit-an-existing-indexer"></a>Editar um indexador existente
Conforme indicado, o assistente de Importação de dados cria um **indexador**, que pode ser modificado como uma construção autónoma no portal.

No dashboard de serviço, faça duplo clique no mosaico Indexador para fazer deslizar uma lista de todos os indexadores criados para a sua subscrição. Faça duplo clique num dos indexadores para executar, editar ou eliminá-lo. Pode substituir o índice por outro já existente, alterar a origem de dados e definir as opções de limiares de erros durante a indexação.

## <a name="edit-an-existing-index"></a>Editar um índice existente
O assistente também criou um **índice**. No Azure Search, as atualizações estruturais de um índice irão necessitar de reconstruir desse índice. Uma reconstrução pressupõe a eliminação do índice, recriando o índice com um esquema revisto com as alterações pretendidas e recarregamento dos dados. As atualizações estruturais incluem a alteração de um tipo de dados e a mudança do nome ou eliminação de um campo.

As edições que não requerem uma reconstrução incluem a adição de um novo campo, alteração dos perfis de classificação, alteração dos sugestores ou alteração dos analisadores de idiomas. Para obter mais informações, veja [Update Index (Atualizar Índice)](https://msdn.microsoft.com/library/azure/dn800964.aspx).


## <a name="next-steps"></a>Passos seguintes
Reveja estas ligações para saber mais sobre indexadores:

* [Indexação da Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexação do Azure Cosmos DB](search-howto-index-documentdb.md)
* [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md)
* [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

