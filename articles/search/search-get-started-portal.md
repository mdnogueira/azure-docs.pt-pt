<properties 
    pageTitle="Introdução à Azure Search | Microsoft Azure | Introdução à Azure Search | DocumentDB | Serviço de pesquisa na nuvem" 
    description="Crie a sua primeira solução da Azure Search através das instruções do tutorial. Saiba como criar um índice da Azure Search utilizando dados de DocumentDB. Este é um exercício baseado no portal sem código através do assistente para Importar Dados." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="paulettm" 
    editor=""
    tags="azure-portal"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="hero-article" 
    ms.tgt_pltfrm="na" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# Introdução à Azure Search no portal

Esta introdução sem código ajuda-o a trabalhar com a Pesquisa do Microsoft Azure utilizando as funcionalidades integradas diretamente no portal. 

O tutorial assume uma [base de dados de DocumentDB do Azure de exemplo](#apdx-sampledata) fácil de criar utilizando os nossos dados e instruções, mas também pode adaptar estes passos aos seus dados existentes no DocumentDB ou na Base de Dados SQL.

> [AZURE.NOTE] Este Tutorial de introdução requer uma [Subscrição do Azure](../../includes/free-trial-note.md) e um [Serviço Azure Search](search-create-service-portal.md). 
 
## Encontrar o seu serviço

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Abra o dashboard de serviço do seu serviço da Azure Search. Seguem-se algumas formas de localizar o dashboard.
    - Na barra de índice, clique em **Procurar serviços**. A barra lista todos os serviços aprovisionados na sua subscrição. Se tiver sido definido um serviço de pesquisa, verá **Procurar serviços** na lista.
    - Na barra de índice, clique em **Procurar** e, em seguida, digite "pesquisar" na caixa de pesquisa para produzir uma lista de todos os serviços de pesquisa criados nas suas subscrições.

## Verificar o espaço

Muitos clientes começam com o serviço gratuito. Esta versão está limitada a três índices, três origens de dados e três indexadores. Certifique-se de que tem espaço para itens adicionais antes de começar. Estas instruções irão criar um de cada objeto.

## Criar um índice e carregar dados

As consultas de pesquisas iteram sobre um *índice* que contém dados pesquisáveis, metadados e construções utilizadas para otimizar determinados comportamentos de pesquisa. Como primeiro passo, terá de definir e preencher um índice.

Existem várias formas de criar um índice. Se os seus dados estiverem num arquivo que a Azure Search possa pesquisar, tal como a Base de Dados SQL do Azure, o SQL Server num VM do Azure ou DocumentDB, pode criar e preencher um índice muito facilmente recorrendo a um *indexador*.

Para manter esta tarefa com base no portal, iremos assumir dados do DocumentDB que podem ser pesquisados utilizando um indexador através do assistente para **Importar dados**. 

Antes de continuar, crie uma [base de dados do DocumentDB de exemplo](#apdx-sampledata) para utilizar com este tutorial e, em seguida, regresse a esta secção para concluir os passos abaixo.

<a id="defineDS"></a>
#### Passo 1: Definir a origem de dados

1. No seu dashboard do serviço Azure Search, clique em **Importar dados** na barra de comando para iniciar um assistente que cria e preenche um índice.

  ![][7]

2. No assistente, clique em **Origem de Dados** > **DocumentDB** > **Nome**, digite um nome para a origem de dados. Uma origem de dados é um objeto de ligação na Azure Search que pode ser utilizado com outros indexadores. Uma vez criada, esta fica disponível como uma "origem de dados existente" no seu serviço.

3. Escolha a sua conta do DocumentDB existente, a base de dados e a coleção. Se estiver a utilizar os dados de exemplo que fornecemos, a sua definição da origem de dados terá este aspeto:

  ![][2]

Repare que estamos a ignorar a consulta. Isto deve-se ao facto de não estarmos a implementar um registo de alterações no nosso conjunto de dados. Se o seu conjunto de dados inclui um campo que monitoriza quando um registo é atualizado, pode configurar um indexador de Azure Search para utilizar o controlo de alterações para atualizações seletivas ao seu índice.

Clique em **OK** para concluir este passo do assistente.

#### Passo 2: Definir o índice

Ainda no assistente, clique em **Índice** e observe a superfície do design utilizada para criar um índice da Azure Search. No mínimo, um índice necessita de um nome e uma coleção de campos, com um campo marcado como a chave do documento. Uma vez que estamos a utilizar um conjunto de dados DocumentDB, os campos são automaticamente detetados pelo assistente e o índice é pré-carregado com campos e atribuições do tipo de dados. 

  ![][3]

Embora os campos e os tipos de dados estejam configurados, é preciso atribuir os atributos. As caixas de verificação na parte superior da lista de campos são *atributos de índice* que controlam a forma como o campo é utilizado. 

- **Recuperável** significa que aparece na lista de resultados da pesquisa. Pode marcar campos individuais como inacessíveis para os resultados da pesquisa ao desmarcar esta caixa de verificação, por exemplo, quando os campos são utilizados apenas em expressões de filtro. 
- **Filtrável**, **Ordenável** e **Facetável** determinam se um campo pode ser utilizado num filtro, uma ordenação ou uma estrutura de navegação de faceta. 
- **Pesquisável** significa que um campo está incluído na pesquisa de texto completo. Por norma, as cadeias são pesquisáveis. Os campos numéricos e booleanos são frequentemente marcados como não pesquisáveis. 

Antes de sair desta página, marque os campos no seu índice para utilizar as seguintes opções (Recuperável, Pesquisável e assim sucessivamente). A maioria dos campos são Recuperáveis. A maioria dos campos de cadeia são Pesquisáveis (não precisa de tornar a Chave pesquisável). Alguns campos, como género, orderableOnline, classificação e etiquetas, são também Filtráveis, Ordenáveis e Facetáveis. 
    
Campo | Tipo | Opções |
------|------|---------|
ID | Edm.String | |
albumTitle | Edm.String | Recuperável, Pesquisável |
albumUrl | Edm.String | Recuperável, Pesquisável |
género | Edm.String | Recuperável, Pesquisável, Filtrável, Ordenável, Facetável |
genreDescription | Edm.String | Recuperável, Pesquisável |
artistName | Edm.String | Recuperável, Pesquisável |
orderableOnline | Edm.Boolean | Recuperável, Filtrável, Ordenável, Facetável |
etiquetas | Coleção (Edm.String) | Recuperável, Filtrável, Facetável |
preço | Edm.Double | Recuperável, Filtrável, Facetável |
margem | Edm.Int32 | |
classificação | Edm.Int32 | Recuperável, Filtrável, Ordenável, Facetável |
inventário | Edm.Int32 | Recuperável |
lastUpdated | Edm.DateTimeOffset | |

Como ponto de comparação, a seguinte captura de ecrã é uma ilustração de um índice compilado conforme as especificações na tabela anterior.

 ![][4]

Clique em **OK** para concluir este passo do assistente.

#### Passo 3: Definir o indexador

Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome**, digite um nome para o indexador e utilize as predefinições para todos os outros valores. Este objeto define um processo executável. Depois de o ter criado, pode colocá-lo na agenda recorrente, mas, por agora, utilize a opção predefinida para executar o indexador uma vez, de imediato, quando clicar em **OK**. 

As suas entradas de dados importados devem estar todas preenchidas e prontas a utilizar.

  ![][5]

Para executar o assistente, clique em **OK** para começar a importação e fechar o assistente.

## Verificar progresso

Para verificar o progresso, aceda ao dashboard do serviço, desloque-se para baixo e faça duplo clique no mosaico **Indexadores** para abrir a lista de indexadores. Deve ver o indexador que acabou de criar na lista, assim como o estado que indica "em curso" ou com êxito, juntamente com o número de documentos indexados numa Azure Search.

  ![][6]

## Consultar o índice

Tem agora um índice de pesquisa pronto para consulta. 

O **Explorador de pesquisa** é uma ferramenta de consulta incorporada no portal. Esta fornece uma caixa de pesquisa para que possa verificar se uma introdução de pesquisa devolve os dados esperados. 

1. Clique em **Explorador de pesquisa** na barra de comando.
2. Tenha em atenção que o índice está ativo. Se não for aquele que acabou de criar, clique em **Alterar índice** na barra de comando para selecionar aquela que pretende.
2. Deixe a caixa de pesquisa em branco e, em seguida, clique no botão **Pesquisar** para executar uma pesquisa com carateres universais, a qual devolve todos os documentos.
3. Introduza algumas consultas de pesquisa em texto completo. Pode rever os resultados da sua pesquisa com carateres universais para se familiarizar com artistas, álbuns e géneros a consultar.
4. Experimente outra sintaxe de consulta utilizando os [exemplos fornecidos no final deste artigo](https://msdn.microsoft.com/library/azure/dn798927.aspx) para ideias, modificar a sua consulta para utilizar cadeias de pesquisa que provavelmente encontrará no seu índice.

## Passos seguintes

Depois de executar o assistente uma vez, pode voltar atrás e ver ou modificar componentes individuais: índice, indexador ou origem de dados. Algumas edições, tal como a alteração do tipo de dados de campo, não são permitidas no índice, mas a maioria das propriedades e das definições são modificáveis. Para ver componentes individuais, clique nos mosaicos **Índice**, **Indexador** ou **Origens de Dados** no seu dashboard para apresentar uma lista de objetos existentes.

Para obter mais informações sobre outras funcionalidades mencionadas neste artigo, visite estas ligações:

- [Indexadores](search-indexer-overview.md)
- [Criar Índice (inclui uma explicação detalhada dos atributos do índice)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Explorador de Procura](search-explorer.md)
- [Procurar nos Documentos (inclui exemplos da sintaxe de consulta)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

Pode tentar este mesmo fluxo de trabalho, utilizando o assistente para Importar dados para outras origens de dados, tal como a Base de Dados SQL do Azure ou do SQL Server em máquinas virtuais do Azure.

> [AZURE.NOTE] Foi recentemente comunicada a funcionalidade de suporte de indexadores para pesquisa do Blob Storage do Azure, contudo, esta função está em pré-visualização e ainda não é uma opção do portal. Para experimentar esse indexador, terá de escrever código. Consulte [Indexar o Blob Storage do Azure na Azure Search](search-howto-indexing-azure-blob-storage.md) para obter mais informações.
<a id="apdx-sampledata"></a>


## Apêndice: Criar dados de exemplo no DocumentDB

Esta secção cria uma base de dados pequena no DocumentDB que pode ser utilizada para concluir as tarefas neste tutorial.

As instruções seguintes dão-lhe orientações gerais, mas não são exaustivas. Se precisar de mais ajuda com as tarefas e navegação do portal DocumentDB, consulte a documentação do DocumentDB, mas a maioria dos comandos que precisará estão na barra de comando de serviço, na parte superior do dashboard, ou no painel da base de dados. 

  ![][1]

### Criar musicstoredb para este tutorial

1. [Clique aqui](https://github.com/HeidiSteen/azure-search-get-started-sample-data) para transferir um ficheiro ZIP com os ficheiros de dados JSON da loja de música. Fornecemos 246 documentos JSON para este conjunto de dados.
2. Adicione DocumentDB à sua subscrição e, em seguida, abra o dashboard de serviço.
2. Clique em **Adicionar Base de Dados** para criar uma nova base de dados com um ID de `musicstoredb`. Esta aparecerá no mosaico da base de dados mais baixo na página depois de criada.
2. Clique no nome da base de dados para abrir o painel da base de dados.
3. Clique em **Adicionar Coleção** para criar uma coleção com um ID de `musicstorecoll`.
3. Clique em **Explorador de Documentos**.
4. Clique em **Carregar**.
5. Em **Carregar Documento**, navegue até à pasta local que contém os ficheiros JSON que transferiu anteriormente. Selecione ficheiros JSON em lotes de 100 ou menos.
    - 386. json
    - 387.json
    - . . .
    - 486.json
6. Repita para obter o lote seguinte de ficheiros até carregar o último, 669.json.
7. Clique em **Explorador de Consulta** para verificar que os dados foram carregados de forma a cumprir os requisitos de carregamento do Explorador de Documentos.

Uma forma fácil de o fazer consiste em utilizar a consulta predefinida, mas também pode modificar a consulta predefinida para que esta seleciona os 300 primeiros (este conjunto de dados tem menos de 300 itens).

Deve regressar à saída JSON, começando pelo documento 386 e terminando no documento 669. Assim que os dados sejam carregados, pode [regressar aos passos destas instruções](#defineDS) para criar um índice utilizando o **Assistente para importar dados**.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png



<!--HONumber=sep16_HO1-->


