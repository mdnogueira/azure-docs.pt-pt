<properties
    pageTitle="Criar um novo conjunto elástico com o portal do Azure | Microsoft Azure"
    description="Como adicionar um conjunto de bases de dados elásticas dimensionável à sua configuração de base de dados SQL para uma administração e partilha de recursos mais fácil entre muitas bases de dados."
    keywords="base de dados dimensionável, configuração de base de dados"
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="07/20/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"/>


# Criar um novo conjunto de bases de dados elásticas com o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

Este artigo mostra como criar um [conjunto de bases de dados elásticas](sql-database-elastic-pool.md) dimensionável com o [portal do Azure](https://portal.azure.com/). Existem duas formas de criar um conjunto. Pode fazê-lo a partir do zero se souber qual é a configuração de conjunto que pretende ou começar com uma recomendação do serviço. A Base de Dados SQL é dotada de inteligência incorporada que recomenda uma configuração de conjunto se for mais rentável para si, com base na telemetria de utilização passada, para as suas bases de dados.

Pode adicionar vários conjuntos a um servidor, mas não pode adicionar bases de dados de diferentes servidores ao mesmo conjunto. Para criar um conjunto, é necessário, pelo menos, uma base de dados num servidor V12. Se não tiver uma, consulte o artigo [Criar a primeira base de dados SQL do Azure](sql-database-get-started.md). Pode criar um conjunto com apenas uma base de dados, mas os conjuntos só são rentáveis com várias bases de dados. Consulte o artigo [Considerações sobre preço e desempenho para um conjunto de bases de dados elásticas](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] Os conjuntos só estão disponíveis com servidores V12 de Base de Dados SQL. Se tiver bases de dados num servidor V11, pode [utilizar um script do PowerShell para identificá-las como candidatas para um conjunto](sql-database-elastic-pool-database-assessment-powershell.md) num servidor V12 e, em seguida, [utilizar o PowerShell para atualizar a versão para V12 e criar um conjunto](sql-database-upgrade-server-powershell.md) num único passo.

## Passo 1: criar um novo conjunto

Este artigo mostra como criar um novo conjunto a partir de um painel do **servidor** existente no portal, que é a forma mais fácil de mover bases de dados existentes para um conjunto. 

> [AZURE.NOTE] Quer já tenha ou não um servidor, também pode criar um novo conjunto a partir do painel **Conjuntos elásticos SQL** (abaixo da lista do lado esquerdo do portal, clique em **Procurar** **>** **Conjuntos elásticos SQL**). Ao clicar em **+Adicionar** no painel **Conjuntos elásticos SQL**, verá os passos para criar um novo servidor durante o aprovisionamento de fluxo de trabalho do conjunto.

1. No [Portal do Azure](http://portal.azure.com/), abaixo da lista do lado esquerdo, clique em **Procurar** **>** **Servidores SQL** e, em seguida, clique no servidor que contém as bases de dados que pretende adicionar a um conjunto.
2. Clique em **Novo conjunto**.

    ![Adicionar um conjunto a um servidor](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-OU-**

    Poderá ver uma mensagem a indicar que existem conjuntos de bases de dados elásticas recomendados para o servidor (apenas para V12). Clique na mensagem para ver os conjuntos recomendados com base na telemetria do histórico de utilização de bases de dados e, em seguida, clique na camada para ver mais detalhes e personalizar o conjunto. Consulte a secção [Compreender as recomendações de conjunto](#understand-pool-recommendations) mais adiante neste tópico, para saber como a recomendação é feita.

    ![conjunto recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    O painel **Conjunto de bases de dados elásticas** é apresentado e é aqui que irá configurar o seu conjunto. Se clicou em **Novo conjunto** no passo anterior, o portal escolhe um **Conjunto padrão**, em **Escalão de preço**, um **Nome** exclusivo para o conjunto e uma configuração predefinida para o conjunto. Se optou por um conjunto de recomendado, a camada e a configuração recomendadas do conjunto que estão escolhidas, mas ainda pode alterá-las.

    ![Configurar o conjunto elástico](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Especifique um nome para o conjunto elástico ou mantenha o nome predefinido.

## Passo 2: escolher um escalão de preço

O escalão de preço do conjunto determina as funcionalidades disponíveis para as bases de dados elásticas no conjunto, o número máximo de eDTUs (eDTU MÁXIMA) e o armazenamento (GBs) disponível para cada base de dados. Para obter detalhes, consulte o Escalões de serviços.

Para alterar o escalão de preço do conjunto, clique em **Escalão de preço**, clique no escalão de preço pretendido e, em seguida, clique em **Selecionar**.

> [AZURE.IMPORTANT] Depois de escolher o escalão de preço e consolidar as alterações ao clicar em **OK** no último passo, não poderá alterar o escalão de preço do conjunto. Para alterar o escalão de preço de um conjunto elástico existente, crie um novo conjunto elástico no escalão de preço pretendido e migre as bases de dados elásticas para este novo conjunto.

![Selecionar um escalão de preço](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## Passo 3: configurar o conjunto

Depois de definir o escalão de preço, clique em Configurar conjunto, onde pode adicionar bases de dados, definir as eDTUs e o armazenamento (GBs do conjunto) do conjunto, e definir o número máximo e mínimo de eDTUs para as bases de dados elásticas no conjunto.

1. Clique em **Configurar conjunto**
2. Selecione as bases de dados que pretende adicionar ao conjunto. Este passo é opcional ao criar o conjunto. As bases de dados podem ser adicionadas depois de o conjunto ter sido criado.
    Para adicionar bases de dados, clique em **Adicionar base de dados**, clique nas bases de dados que pretende adicionar e, em seguida, clique no botão **Selecionar**.

    ![Adicionar bases de dados](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Se as bases de dados com que está a trabalhar tiverem telemetria de histórico de utilização suficiente, o gráfico **Utilização de GB e eDTU estimada** e o gráfico de barras **Utilização de eDTU real** são atualizados para o ajudar a tomar decisões de configuração. Além disso, o serviço poderá apresentar uma mensagem de recomendação para o ajudar a dimensionar corretamente o conjunto. Consulte a secção [Recomendações Dinâmicas](#dynamic-recommendations).

3. Utilize os controlos na página **Configurar conjunto** para explorar as definições e configurar o conjunto. Consulte [Limites dos conjuntos elásticos](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) para obter mais detalhes sobre os limites de cada escalão de serviço e consulte [Considerações sobre preço e desempenho para conjuntos de bases de dados elásticas](sql-database-elastic-pool-guidance.md) para obter instruções detalhadas sobre como dimensionar corretamente um conjunto. Para obter mais detalhes sobre as definições do conjunto, consulte [Propriedades do conjunto de bases de dados elásticas](sql-database-elastic-pool.md#elastic-database-pool-properties).

    ![Configurar o Conjunto Elástico](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Clique em **Selecionar** no painel **Configurar conjunto** depois de alterar as definições.
5. Clique em **OK** para criar o conjunto.


## Compreender as recomendações de conjunto

O serviço Base de Dados SQL avalia o histórico de utilização e recomenda um ou mais conjuntos quando é mais rentável do que utilizar bases de dados individuais. Cada recomendação está configurada com um subconjunto exclusivo das bases de dados do servidor que melhor se adequam ao conjunto.

![conjunto recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

A recomendação de conjunto é composta por:

- Um escalão de preço para o conjunto (Básico, Standard ou Premium)
- O número adequado de **eDTUs POR CONJUNTO** (também denominado Número máximo de eDTUs por conjunto)
- O **número máximo de eDTUs** e o **número mínimo de eDTUs** por base de dados
- A lista de bases de dados recomendadas para o conjunto

O serviço tem em conta os últimos 30 dias de telemetria quando recomenda conjuntos. Para uma base de dados ser considerada candidata para um conjunto de bases de dados elásticas, tem de existir durante, pelo menos, 7 dias. As bases de dados que já estão num conjunto de bases de dados elásticas não são consideradas candidatas para recomendações de conjunto de bases de dados elásticas.

O serviço avalia as necessidades de recursos e a relação custo-eficácia da movimentação das bases de dados individuais em cada escalão de serviço para conjuntos do mesmo escalão. Por exemplo, todas as bases de dados Standard num servidor são avaliadas para determinar a respetiva adequação a um Conjunto Elástico Standard. Isto significa que o serviço não faz recomendações entre escalões, tal como mover uma base de dados Standard para um conjunto Premium.

### Recomendações dinâmicas

Depois de adicionar as bases de dados ao conjunto, as recomendações serão geradas dinamicamente com base no histórico de utilização das bases de dados que selecionou. Estas recomendações serão apresentadas no gráfico de utilização de eDTUs e GBs, bem como numa faixa de recomendação no topo do painel **Configurar conjunto**. Estas recomendações visam ajudá-lo a criar um conjunto otimizado para as suas bases de dados específicas.

![recomendações dinâmicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## Recursos adicionais

- [Gerir um conjunto elástico da Base de Dados SQL com o portal](sql-database-elastic-pool-manage-portal.md)
- [Gerir um conjunto elástico da Base de Dados SQL com o PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Gerir um conjunto elástico da Base de Dados SQL com C#](sql-database-elastic-pool-manage-csharp.md)
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md) 




<!--HONumber=Aug16_HO1-->


