<properties
    pageTitle="Importar dados para a Pesquisa do Azure Search utilizando indexadores no Portal do Azure | Microsoft Azure | Serviço de pesquisa em nuvem alojado"
    description="Como utilizar indexadores no Portal do Azure."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="heidist"/>

# Importar dados para a Pesquisa do Azure através do portal

O Portal do Azure inclui um comando **Importar Dados** no dashboard da Pesquisa do Azure que o conduz através da ingestão de dados na Pesquisa do Azure. O comando baseia-se na funcionalidade de indexadores incorporados que pesquisa uma origem de dados existente, criando e carregando documentos com base num conjunto de linhas encontrado na origem de dados.

Utilizando o assistente, a importação de dados é uma construção composta por 3 partes:

- uma ligação à origem de dados
- um índice de destino no qual são carregados dados (o assistente pode frequentemente gerar este por si)
- uma agenda que é executada agora ou em intervalos regulares

Para utilizar um indexador ou o comando **Importar Dados**, a sua origem de dados principal tem de ser uma das origens de dados suportadas: Base de Dados Azure SQL, bases de dados relacionais do SQL Server numa VM do Azure ou do Azure DocumentDB.

Só é possível importar a partir de uma única tabela, vista ou estrutura de dados equivalente. Poderá ter de criar primeiro esta estrutura de dados na origem de dados da aplicação para colocar as entradas de metadados e dados corretas num índice de pesquisa.

Pode tentar terminar este fluxo de trabalho com dados de exemplo. Visite [Introdução à Pesquisa do Azure no Portal do Azure](search-get-started-portal.md) para começar.

##Configurar a importação de dados

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Abra o dashboard de serviço do seu serviço de Pesquisa do Azure. Seguem-se algumas formas de localizar o dashboard.
    - No Jumpbar, clique em **Base**. A home page tem mosaicos para cada serviço na sua subscrição. Clique no mosaico para abrir o dashboard de serviço.
    - No Jumpbar, clique em **Procurar tudo** > **Filtrar por** > **Procurar serviços** para localizar o serviço de Pesquisa na lista.

3. No dashboard de serviço, verá uma barra de comando na parte superior, incluindo um para **Importar Dados**. Clique em **Importar Dados** para abrir gradualmente o painel Importar Dados.

4. Clique em **Ligar aos seus dados** para especificar uma definição de origem de dados utilizada por um indexador. As opções incluem:
    -   A origem de dados existente refere-se a uma definição de origem de dados previamente criada para um indexador. Se já tiver indexadores definidos no seu serviço de pesquisa, pode reobjetivar uma definição de origem de dados para outra importação.
    -   O SQL do Azure é utilizado para especificar uma ligação à origem de dados para uma base de dados SQL no Azure ou para uma base de dados SQL Server numa VM do Azure.
    -   O DocumentDB é utilizado para especificar uma ligação à origem de dados para esse tipo de origem de dados.

   Para o SQL do Azure e DocumentDB, a base de dados tem de existir na sua subscrição. Pode colar uma cadeia de ligação se a souber, ou escolher uma origem de dados criada anteriormente por alguém que tenha privilégios de escrita para a sua subscrição.

5. Clique em **Personalizar o índice de destino** para concluir o índice predefinido.
    - A **Chave** é necessária. O campo que selecionar para a chave tem de ser um campo de cadeia que contenha valores exclusivos.
    - O nome de campo e tipo são normalmente preenchidos para si. Pode alterar o tipo de dados.
    - Selecione os atributos para cada campo:
        - Recuperável devolve o campo nos resultados da pesquisa.
        - Filtrável permite que o campo seja referenciado em expressões de filtro.
        - Ordenável permite que o campo seja utilizado numa consulta.
        - Facetável ativa o campo para navegação por facetas.
        - Pesquisável ativa a pesquisa de texto completo.
    - Clique no separador **Analisador** se pretende especificar um analisador de idioma ao nível do campo. Consulte o artigo [Criar um índice para documentos em vários idiomas](search-language-support.md) para obter detalhes.
    - Clique no **Sugestor** se pretender ativar as sugestões de consulta de conclusão automática ou sugestão automática.

6. Clique em **Importar os seus dados** para executar a operação de importação utilizando a opção Executar Agora ou configurar numa agenda periódica.

A operação de importação de dados que acabou concluir criou um indexador em segundo plano. Agora pode editar o indexador diretamente para alterar qualquer uma das suas partes de componente.

##Editar um indexador existente

No dashboard de serviço, faça duplo clique no mosaico Indexador para fazer deslizar uma lista de todos os indexadores criados para a sua subscrição. Faça duplo clique num dos indexadores para executar, editar ou eliminá-lo.



<!--HONumber=Jun16_HO2-->


