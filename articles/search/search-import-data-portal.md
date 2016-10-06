<properties
    pageTitle="Importar dados para a Pesquisa do Azure Search utilizando indexadores no Portal do Azure | Microsoft Azure | Serviço de pesquisa em nuvem alojado"
    description="Utilize o Assistente de Dados de Importação de Azure Search no Portal do Azure para pesquisar dados no armazenamento de Blobs do Azure, no armazenamento de tabelas, Base de Dados SQL e no SQL Server em VMs do Azure."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>


# Importar dados para a Pesquisa do Azure através do portal

O Portal do Azure fornece um assistente de **Importação de Dados** no dashboard do Azure Search para carregar dados para um índice. 

  ![Importação de Dados na barra de comandos][1]

Internamente, o assistente configura e invoca um *indexador*, automatizando vários passos do processo de indexação: 

- Ligar a uma origem de dados externos na subscrição atual do Azure
- Gerar automaticamente um esquema de índice com base na estrutura dos dados de origem
- Criar documentos com base num conjunto de linhas obtido a partir da origem de dados
- Carregar documentos para o índice do serviço de pesquisa

Pode tentar terminar este fluxo de trabalho com dados de exemplo no DocumentDB. Visite [Introdução ao Azure Search no Portal do Azure](search-get-started-portal.md) para obter instruções.

## Origens de dados suportadas pelo Assistente de Importação de Dados

O Assistente para Importar Dados suporta as seguintes origens de dados: 

- Base de Dados SQL do Azure
- Dados relacionais do SQL Server numa VM do Azure
- Azure DocumentDB
- Armazenamento de Blobs do Azure (pré-visualização)
- Armazenamento de Tabelas do Azure (pré-visualização)

Um conjunto de dados bidimensional é uma entrada necessária. Só é possível importar a partir de uma única tabela, de uma vista de base de dados ou de uma estrutura de dados equivalente. Deve criar esta estrutura de dados antes de executar o assistente.

Tenha em atenção que alguns dos indexadores ainda se encontram em pré-visualização, o que significa que a definição do indexador está protegida pela versão de pré-visualização da API. Consulte [Descrição geral do indexador](search-indexer-overview.md) para obter mais informações e ligações.

## Ligar aos dados

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e abra o dashboard de serviço. Pode clicar em **Procurar serviços** na barra de atalhos para mostrar os serviços existentes na subscrição atual. 

2. Clique em **Importar Dados** na barra de comandos para abrir gradualmente o painel Importar Dados.  

3. Clique em **Ligar aos seus dados** para especificar uma definição de origem de dados utilizada por um indexador. Para origens de dados de subscrição de dimensionamento, o assistente, normalmente, pode detetar e ler informações de ligação, minimizando os requisitos de configuração geral.

| | |
|--------|------------|
|**Origem de dados existente** | Se já tiver indexadores definidos no seu serviço de pesquisa, pode selecionar uma definição de origem de dados para outra importação existente.|
|**Base de Dados SQL do Azure** | Podem ser especificados um nome do serviço, credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados na página ou através de uma cadeia de ligação do ADO.NET. Escolha a opção de cadeia de ligação para ver ou personalizar propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção.|
|**SQL Server numa VM do Azure** | Especifique um nome de serviço totalmente qualificado, um ID de utilizador e palavra-passe e uma base de dados como uma cadeia de ligação. Para utilizar esta origem de dados, deve ter instalado anteriormente um certificado no arquivo local que encripta a ligação. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção.
|**DocumentDB** |Os requisitos incluem a conta, a base de dados e a coleção. Todos os documentos na coleção serão incluídos no índice remissivo. Pode definir uma consulta para nivelamento ou filtrar o conjunto de linhas ou para detetar documentos alterados para as operações de atualização de dados subsequentes.|
|**Armazenamento de Blobs do Azure** | Os requisitos incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar a porção do diretório virtual do nome como uma pasta no contentor. Consulte [Armazenamento de Blobs de Indexação (pré-visualização)](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
|**Table Storage do Azure** | Os requisitos incluem a conta de armazenamento e um nome de tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto de tabelas. Consulte [Armazenamento de Tabelas de Indexação (pré-visualização)](search-howto-indexing-azure-tables.md) para obter mais informações. |

## Personalizar o índice de destino

Um índice preliminar, normalmente, é inferido do conjunto de dados. Adicionar, editar ou eliminar campos para concluir o esquema. Além disso, definir atributos ao nível de campo para determinar os respetivos comportamentos de pesquisa subsequentes.

1. Em **Personalizar o índice de destino**, especifique o nome e uma **Chave** utilizada para identificar exclusivamente cada documento. A chave tem de ser uma cadeia. Se os valores de campo incluem espaços ou traços não se esqueça de definir opções avançadas em **Importar dados** para suprimir a verificação de validação para estes carateres.

2. Reveja os campos restantes. O nome de campo e tipo são normalmente preenchidos para si. Pode alterar o tipo de dados.

3. Defina o índice para cada campo:

 - Recuperável devolve o campo nos resultados da pesquisa.
 - Filtrável permite que o campo seja referenciado em expressões de filtro.
 - Ordenável permite que o campo seja utilizado numa consulta.
 - Facetável ativa o campo para navegação por facetas.
 - Pesquisável ativa a pesquisa de texto completo.
  
4. Clique no separador **Analisador** se pretende especificar um analisador de idioma ao nível do campo. Apenas podem ser especificados, de momento, analisadores de idiomas. Ao utilizar um analisador personalizado ou um analisador que não seja de idioma como Palavra-chave, Padrão e assim sucessivamente, necessitará um código.

 - Clique em **Pesquisável** para designar a pesquisa em texto completo no campo e ativar a lista pendente do Analisador.
 - Escolha o analisador que pretende. Consulte o artigo [Criar um índice para documentos em vários idiomas](search-language-support.md) para obter detalhes.

5. Clique em **Sugestor** par ativar as sugestões de consulta de escrita antecipada nos campos selecionados.


## Importar dados

1. Em **Importar dados**, forneça um nome para o indexador. Tenha em consideração que a que o produto do assistente de Importação de Dados é um indexador. Mais tarde, se pretender ver ou editar, selecioná-lo-á a partir do portal, em vez de executar o assistente novamente. 

2. Especifique a agenda, que se baseia fuso horário regional em que o serviço é aprovisionado.

3. Defina opções avançadas para especificar os limiares de se a indexação pode continuar no caso de que um documento seja removido. Além disso, pode especificar se os campos de **Chave** têm autorização para conter espaços e barras.  

## Editar um indexador existente

No dashboard de serviço, faça duplo clique no mosaico Indexador para fazer deslizar uma lista de todos os indexadores criados para a sua subscrição. Faça duplo clique num dos indexadores para executar, editar ou eliminá-lo. Pode substituir o índice por outro já existente, alterar a origem de dados e definir as opções de limiares de erros durante a indexação.

## Editar um índice existente

No Azure Search, as atualizações estruturais de um índice irão exigir uma reconstrução do mesmo, o que consiste na eliminação, recriação e recarregamento dos dados. As atualizações estruturais incluem a alteração de um tipo de dados e a mudança do nome ou eliminação de um campo.

As edições que não requerem uma reconstrução incluem a adição de um novo campo, alteração dos perfis de classificação, alteração dos sugestores ou alteração dos analisadores de idiomas. Para obter mais informações, veja [Update Index (Atualizar Índice)](https://msdn.microsoft.com/library/azure/dn800964.aspx).

## Passo seguinte

Reveja estas ligações para saber mais sobre indexadores:

- [Indexação da Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indexação do DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Indexação do Armazenamento de Blobs (pré-visualização)](search-howto-indexing-azure-blob-storage.md)
- [Indexação do Armazenamento de Tabelas (pré-visualização)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png




<!--HONumber=Sep16_HO4-->


