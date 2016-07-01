<properties
   pageTitle="O que é o Azure SQL Data Warehouse | Microsoft Azure"
   description="Base de dados distribuída de nível empresarial capaz de processar volumes de petabytes de dados relacionais e não relacionais. É o primeiro armazém de dados em nuvem da indústria que pode aumentar, diminuir e ser colocado em pausa em segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# O que é o Azure SQL Data Warehouse?

O Azure SQL Data Warehouse é uma base de dados de escalabilidade horizontal, baseada na nuvem, capaz de processar grandes volumes de dados, tanto relacionais como não relacionais. Baseado na arquitetura de processamento paralelo em grande escala (MPP), o SQL Data Warehouse consegue processar a carga de trabalho da sua empresa. 

SQL Data Warehouse:

- Combina a base de dados relacional comprovada do SQL Server com as capacidades de dimensionamento horizontal em nuvem do Azure. Pode aumentar, diminuir, colocar em pausa ou retomar a computação em segundos.  Isto permite-lhe reduzir os custos ao aumentar horizontalmente a CPU quando precisar e ao reduzir a utilização durante as horas fora de pico.
- Tira partido da plataforma do Azure. É fácil de implementar, a manutenção é totalmente integrada e é totalmente tolerante a falhas graças às cópias de segurança automáticas. 
- Complementa o ecossistema do SQL Server.  Pode ser desenvolvido com as ferramentas e o T-SQL familiares do SQL Server.

Continue a ler para saber mais sobre as principais funcionalidades do SQL Data Warehouse.

## Otimizado

### Arquitetura de processamento paralelo em grande escala (MPP)

O SQL Data Warehouse utiliza a arquitetura de processamento paralelo em grande escala (MPP), concebida para executar alguns dos maiores armazéns de dados locais do mundo.

Atualmente, a arquitetura MPP espalha os dados por 60 unidades de processamento e de armazenamento sem partilha. Os dados são armazenados nos Blobs de Armazenamento do Azure dentro do Premium Storage e ligados a nós de computação para a execução de consultas. Com esta arquitetura, podemos adotar uma abordagem de divisão e conquista para executar consultas complexas de T-SQL. Durante o processamento, o nó de controlo analisa a consulta e, em seguida, cada nó de computação "conquista" a sua parte dos dados em paralelo. 

Ao combinar a arquitetura MPP e as capacidades de armazenamento do Azure, o SQL Data Warehouse pode:

- Aumentar ou diminuir o armazenamento, independentemente da computação.
- Aumentar ou diminuir a computação sem mover dados. 
- Colocar em pausa a capacidade de computação, mantendo os dados intactos.
- Retomar de imediato a capacidade de computação.

A arquitetura é descrita detalhadamente abaixo. 

![Arquitetura do SQL Data Warehouse][1]


- **Nó de controlo:** o nó de controlo "controla" o sistema. É o front-end que interage com todas as ligações e aplicações. No SQL Data Warehouse, o nó de controlo utiliza a tecnologia da Base de Dados SQL e a ligação ao mesmo é semelhante. Sob a superfície, o nó de controlo coordena todos os movimentos dos dados e a computação necessária para executar consultas paralelas nos seus dados distribuídos. Quando submete uma consulta TSQL ao SQL Data Warehouse, o nó de controlo transforma-a em consultas separadas que serão executadas em cada nó de computação em paralelo.

- **Nós de computação:** os nós de computação servem como o poder por trás do SQL Data Warehouse. São Bases de Dados SQL que processam os passos da consulta e gerem os seus dados. Quando adiciona dados, o SQL Data Warehouse distribui as linhas com os nós de computação. Os nós de computação também são os trabalhadores que executam as consultas paralelas nos seus dados. Após o processamento, passam os resultados novamente para o nó de controlo. Para concluir a consulta, o nó de controlo agrega os resultados e devolve o resultado final.


- **Armazenamento:** os dados são armazenados em Blobs de Armazenamento do Azure. Quando os nós de computação interagem com os seus dados, escrevem e leem diretamente de e para o armazenamento de blobs. Uma vez que o armazenamento do Azure se expande de forma transparente e ilimitada, o SQL Data Warehouse poderá fazer o mesmo. Como a computação e o armazenamento são independentes, o SQL Data Warehouse pode, de forma independente, dimensionar o armazenamento e a computação e vice-versa.  O Armazenamento do Azure é também totalmente tolerante a falhas e simplifica o processo de cópia de segurança e restauro.
   

- **Serviço de Movimento de Dados:** o Serviço de Movimento de Dados (DMS) é a tecnologia que utilizamos para mover dados entre os nós. O DMS dá aos nós de computação acesso aos dados de que necessitam para associações e agregações. O DMS não é um serviço do Azure. É um serviço do Windows que é executado em conjunto com a Base de Dados SQL em todos os nós. Uma vez que o DMS é executado em segundo plano, não irá interagir com ele diretamente. No entanto, quando consultar planos de consulta, irá reparar que incluem algumas operações do DMS, uma vez que o movimento de dados é necessário de alguma forma para executar cada consulta em paralelo.


### Desempenho otimizado de consulta

Para além da estratégia de divisão e conquista, a abordagem MPP é auxiliada por algumas otimizações de desempenho específicas dos armazéns de dados, incluindo:

- Um otimizador de consultas distribuídas e um conjunto de estatísticas complexas em todos os dados. Utilizando informações sobre o tamanho e a distribuição dos dados, o serviço consegue otimizar as consultas ao avaliar o custo de operações de consulta distribuída específicas.

- Técnicas e algoritmos avançados integrados no processo de movimento de dados para mover os dados de forma eficiente entre recursos de computação, conforme for necessário para executar a consulta. Estas operações de movimento de dados são incorporadas e todas as otimizações ao Serviço de Movimento de Dados ocorrem automaticamente.

- Índices columnstore em cluster por predefinição. Utilizando o armazenamento baseado em colunas, o SQL Data Warehouse obtém até 5x mais ganhos de compressão em relação ao armazenamento orientado por linhas tradicional e até 10x mais ganhos de desempenho de consulta. As consultas de análises que precisam de analisar um grande número de linhas funcionam muito bem em índices columnstore. 

## Escalável

A arquitetura do SQL Data Warehouse introduz armazenamento e computação independentes, permitindo o dimensionamento independente de cada. A estrutura de implementação rápida e simples da Base de Dados SQL permite que computação adicional fique disponível de imediato. A utilização de Blobs de Armazenamento do Azure complementa este aspeto. A utilização de Blobs não só proporciona armazenamento replicado e estável, como também permite a fácil expansão da infraestrutura a um custo reduzido.  Com esta combinação de armazenamento à escala da nuvem e computação do Azure, o SQL Data Warehouse permite que pague pelo desempenho de consulta apenas quando necessitar dele. Alterar a quantidade de computação é tão simples como mover um controlo de deslize no portal do Azure para a esquerda ou para a direita, mas também pode ser agendada com o T-SQL e o PowerShell.

Juntamente com a capacidade de controlar totalmente a quantidade de computação independentemente do armazenamento, o SQL Data Warehouse permite-lhe colocar totalmente em pausa o seu armazém de dados. Enquanto mantém o armazenamento no devido lugar, toda a computação é libertada para o conjunto principal do Azure, poupando em custos de imediato. Quando for necessário, basta retomar a computação e terá os seus dados e computação disponíveis para a sua carga de trabalho.

A utilização de computação no SQL Data Warehouse é medida com Unidades do SQL Data Warehouse (DWUs). As DWUs são uma medida de potência subjacente que o armazém de dados tem e foram concebidas para garantir que tem uma quantidade padrão de desempenho associado ao seu armazém em qualquer altura específica.  Mais especificamente, utilizamos DWUs para garantir que:

- Consegue dimensionar o seu armazém de dados de forma eficaz, sem se preocupar com o hardware ou software subjacente.

- Consegue compreender o desempenho que verá ao nível de uma DWU antes de alterar o tamanho do armazém de dados.

- O hardware e o software subjacentes da sua instância podem ser alterados ou movidos sem afetar o desempenho da sua carga de trabalho

- Podemos efetuar ajustes à arquitetura subjacente do serviço sem afetar o desempenho da sua carga de trabalho.

- Como melhoramos rapidamente o desempenho no SQL Data Warehouse, podemos garantir que o fazemos de uma forma escalável e que afete o sistema uniformemente.

### Unidades do Data Warehouse

Especificamente, vemos as Unidades do Data Warehouse como uma medida de três métricas precisas, que consideramos estarem altamente correlacionadas com o desempenho da carga de trabalho do armazém de dados. Para obtermos disponibilidade geral, pretendemos que estas métricas de carga de trabalho chave sejam dimensionadas de forma linear com as DWUs que escolheu para o seu armazém de dados.

**Análise/Agregação:** esta métrica de carga de trabalho utiliza uma consulta de armazém de dados padrão, que analisa um grande número de linhas e, em seguida, efetua uma agregação complexa. Esta operação é exigente em termos de E/S e CPU.

**Carga:** esta métrica mede a capacidade de ingerir dados no serviço. As cargas são completadas com o PolyBase que carrega um conjunto de dados representativo a partir de um Blob de Armazenamento do Azure. Esta métrica foi concebida para realçar aspetos da Rede e da CPU do serviço.

**CREATE TABLE AS SELECT (CTAS):** o CTAS mede a capacidade de criar uma cópia de uma tabela. Isto envolve a leitura de dados do armazenamento, a distribuição dos mesmos pelos nós da aplicação e a escrita dos dados novamente no armazenamento. É uma operação exigente em termos de CPU e da Rede.

### Quando dimensionar

Resumindo, queremos que as DWUs sejam simples. Quando precisar de resultados mais rápidos, aumente as suas DWUs e pague por um desempenho superior.  Quando precisar de menos poder de computação, diminua as DWUs e pague apenas pelo que precisar. Poderá pensar em alterar o número de DWUs nestes casos:

- Quando não precisar de executar consultas, talvez à noite ou ao fim de semana, coloque em pausa os recursos de computação para cancelar todas as consultas em execução e remova todas as DWUs atribuídas ao seu armazém de dados.

- Ao efetuar uma operação de transformação ou de carregamento de uma grande quantidade de dados, poderá querer aumentar verticalmente o desempenho, de modo a que os dados fiquem disponíveis mais rapidamente.

- Para compreender qual é o seu valor de DWU ideal, tente aumentar e reduzir verticalmente o desempenho e executar algumas consultas depois de carregar os dados. Uma vez que o dimensionamento é rápido, pode experimentar diferentes níveis de desempenho sem comprometer mais de uma hora.

> [AZURE.NOTE] Tenha em atenção que devido à arquitetura do SQL Data Warehouse, poderá não ver as capacidades de desempenho esperadas com volumes de dados inferiores.  Recomendamos que comece com volumes de dados iguais ou superiores a 1 TB para obter uma indicação real dos benefícios de desempenho.

## Integrado

O SQL Data Warehouse baseia-se no motor de base de dados relacional comprovado do SQL Server e inclui muitas das funcionalidades que se espera de um armazém de dados empresarial. Se já conhece o Transact-SQL, é fácil transferir o seu conhecimento para o SQL Data Warehouse. Quer seja um utilizador avançado ou esteja apenas a começar, os exemplos em toda a documentação irão ajudá-lo a começar. Em geral, pode pensar sobre a forma como construímos os elementos de linguagem do SQL Data Warehouse da seguinte forma:

- O SQL Data Warehouse utiliza a sintaxe Transact-SQL (TSQL) do SQL Server para muitas operações e suporta um conjunto amplo de construções tradicionais de SQL, tais como procedimentos armazenados, funções definidas pelo utilizador, criação de partições de tabela, índices e agrupamentos.

- O SQL Data Warehouse contém também várias funcionalidades inovadoras do SQL Server, incluindo índices columnstore em cluster, integração do PolyBase e Auditoria de Dados (completa com Avaliação de Ameaças).

- Como o SQL Data Warehouse está ainda em desenvolvimento, determinados elementos de linguagem TSQL menos comuns para cargas de trabalho de armazém de dados ou mais recentes em relação ao SQL Server podem não estar disponíveis neste momento. Consulte a documentação de migração para obter mais informações relacionadas com estes elementos.

Com o Transact-SQL e a convergência de funcionalidades entre o SQL Server, o SQL Data Warehouse, a Base de Dados SQL e o Analytics Platform System, pode desenvolver uma solução que se adeque às suas necessidades de dados. Pode decidir onde pretende manter os seus dados, com base em requisitos de desempenho, segurança e escala, e, em seguida, transferir os dados conforme necessário entre sistemas diferentes.

Para além de adotar a área de superfície de TSQL do SQL Server, o SQL Data Warehouse também se integra com muitas das ferramentas com as quais os utilizadores do SQL Server podem estar familiarizados. Especificamente, concentramo-nos em integrar algumas categorias de ferramentas no SQL Data Warehouse, incluindo:

**Ferramentas tradicionais do SQL Server:** com o SQL Data Warehouse, está disponível integração total com o SQL Server Analysis Services, o Integration Services e o Reporting Services.

**Ferramentas baseadas na nuvem:** o SQL Data Warehouse pode ser utilizado em conjunto com várias novas ferramentas no Azure e funciona de forma totalmente integrada com o Data Factory, o Stream Analytics, o Machine Learning e o Power BI do Azure.

**Ferramentas de terceiros:** um grande número de fornecedores de ferramentas terceiros certificou a integração das respetivas ferramentas no SQL Data Warehouse. Veja a lista completa.

## Híbrido

Utilizar o SQL Data Warehouse com o PolyBase fornece aos utilizadores uma capacidade inédita para mover dados pelo seu ecossistema, revelando a capacidade de configurar cenários híbridos com origens de dados não relacionais e no local.

O Polybase é fácil de utilizar e permite tirar partido dos seus dados de diferentes origens, utilizando os mesmos comandos familiares do T-SQL. O Polybase permite fazer consultas em dados não relacionais mantidos no armazenamento de blobs do Azure como se se tratasse de uma tabela normal. Utilize o Polybase para consultar dados não relacionais ou para importar dados não relacionais para o SQL Data Warehouse.

- O PolyBase utiliza tabelas externas para aceder a dados não relacionais. As definições de tabela são armazenadas no SQL Data Warehouse e podem ser acedidas pelo SQL Server e por ferramentas, tal como acederia a dados relacionais normais.

- O Polybase é agnóstico em termos de integração. Expõe as mesmas funções e funcionalidades a todas as origens que suporta. Os dados lidos pelo Polybase podem ter uma variedade de formatos, incluindo ficheiros delimitados ou ficheiros ORC.

- O PolyBase pode ser utilizado para aceder ao armazenamento de blobs que também está a ser utilizado como armazenamento para um cluster do HD Insight, dando-lhe acesso inovador aos mesmos dados com ferramentas relacionais e não relacionais.

## Passos seguintes

Agora que já conhece um pouco sobre o SQL Data Warehouse, saiba mais sobre a [carga de trabalhos do armazém de dados], [como aprovisionar] um SQL Data Warehouse e [como carregar dados de exemplo].  Em alternativa, dê uma vista de olhos a alguns destes outros Recursos do SQL Data Warehouse.  

- [Blogues] 
- [Pedidos de Funcionalidades]
- [Vídeos]
- [Blogues da Equipa CAT]
- [Criar Pedido de Suporte]
- [Fórum MSDN]
- [Fórum Stack Overflow]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar Pedido de Suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[carga de trabalhos do armazém de dados]: ./sql-data-warehouse-overview-workload.md
[como carregar dados de exemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[como aprovisionar]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de Funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum MSDN]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse



<!--HONumber=Jun16_HO2-->


