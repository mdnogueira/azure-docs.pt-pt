---
title: "Criar um novo conjunto elástico com o portal do Azure | Microsoft Docs"
description: "Como adicionar um conjunto de bases de dados elásticas dimensionável à sua configuração de base de dados SQL para uma administração e partilha de recursos mais fácil entre muitas bases de dados."
keywords: "base de dados dimensionável, configuração de base de dados"
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: 
ms.assetid: bf12594b-d258-40e6-a9fc-d8a8710c2d65
ms.service: sql-database
ms.custom: sharded databases pool
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: hero-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 1a0b8609acd99ec188f92a32ed4cb44a68edc3b2
ms.openlocfilehash: 54deca0518d82de59a58e02fc38693179c486b64


---
# <a name="create-a-new-elastic-database-pool-with-the-azure-portal"></a>Criar um novo conjunto de bases de dados elásticas com o portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
>

Este artigo mostra como criar um [conjunto elástico](sql-database-elastic-pool.md) dimensionável com o [portal do Azure](https://portal.azure.com/). Existem duas formas de criar um conjunto. Pode fazê-lo a partir do zero se souber qual é a configuração de conjunto que pretende ou começar com uma recomendação do serviço. A Base de Dados SQL é dotada de inteligência incorporada que recomenda uma configuração de conjunto se for mais rentável para si, com base na telemetria de utilização passada, para as suas bases de dados.

Pode adicionar vários conjuntos a um servidor, mas não pode adicionar bases de dados de diferentes servidores ao mesmo conjunto. Para criar um conjunto, é necessário, pelo menos, uma base de dados num servidor V12. Se não tiver uma, consulte o artigo [Criar a primeira base de dados SQL do Azure](sql-database-get-started.md). Pode criar um conjunto com apenas uma base de dados, mas os conjuntos só são rentáveis com várias bases de dados. Consulte o artigo [Considerações sobre preço e desempenho para um conjunto de bases de dados elásticas](sql-database-elastic-pool-guidance.md).

> [!NOTE]
> Os conjuntos elásticos estão em disponibilidade geral (GA) em todas as regiões do Azure, exceto na Índia Ocidental, onde se encontra, de momento, em pré-visualização.  A GA dos conjuntos elásticos nesta região vai ocorrer assim que possível.
>
>

## <a name="step-1-create-a-new-pool"></a>Passo 1: criar um novo conjunto

Este artigo mostra como criar um novo conjunto a partir de um painel do **servidor** existente no portal, que é a forma mais fácil de mover bases de dados existentes para um conjunto.

> [!NOTE]
> Também pode criar um novo conjunto procurando **agrupamento elástico de SQL** o **Marketplace** ou clicando em **+ Adicionar** non painel de pesquisa **conjuntos elásticos SQL**. Poderá especificar um servidor novo ou existente através deste conjunto de aprovisionamento de fluxo de trabalho.
>
>

1. No [Portal do Azure](http://portal.azure.com/), abaixo da lista do lado esquerdo, clique em **ais serviços** **>** **Servidores SQL** e, em seguida, clique no servidor que contém as bases de dados que pretende adicionar a um conjunto.
2. Clique em **Novo conjunto**.

    ![Adicionar um conjunto a um servidor](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-OU-**

    Poderá ver uma mensagem a indicar que existem conjuntos elásticos recomendados para o servidor (apenas para V12). Clique na mensagem para ver os conjuntos recomendados com base na telemetria do histórico de utilização de bases de dados e, em seguida, clique na camada para ver mais detalhes e personalizar o conjunto. Consulte a secção [Compreender as recomendações de conjunto](#understand-pool-recommendations) mais adiante neste tópico, para saber como a recomendação é feita.

    ![conjunto recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. O painel **Conjunto de bases de dados elásticas** é apresentado e é aqui que irá especificar as configurações para o conjunto. Se clicou em **Novo conjunto** no passo anterior, o escalão de preço será **Standard** por predefinição e ainda não haverão bases de dados selecionadas. Pode criar um conjunto vazio ou especificar um conjunto de bases de dados existente a partir desse servidor para mover para o conjunto. Se estiver a criar um conjunto recomendado, o escalão de preço recomendado, as definições de desempenho e a lista de bases de dados serão pré-povoados, mas ainda poderá alterá-los.

    ![Configurar o conjunto elástico](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. Especifique um nome para o conjunto elástico ou mantenha o nome predefinido.

## <a name="step-2-choose-a-pricing-tier"></a>Passo 2: escolher um escalão de preço

O escalão de preço do conjunto determina as funcionalidades disponíveis para as bases de dados elásticas no conjunto, o número máximo de eDTUs (eDTU MÁXIMA) e o armazenamento (GBs) disponível para cada base de dados. Para obter detalhes, consulte o Escalões de serviços.

Para alterar o escalão de preço do conjunto, clique em **Escalão de preço**, clique no escalão de preço pretendido e, em seguida, clique em **Selecionar**.

> [!IMPORTANT]
> Depois de escolher o escalão de preço e consolidar as alterações ao clicar em **OK** no último passo, não poderá alterar o escalão de preço do conjunto. Para alterar o escalão de preço de um conjunto elástico existente, crie um novo conjunto elástico no escalão de preço pretendido e migre as bases de dados elásticas para este novo conjunto.
>
>

![Selecionar um escalão de preço](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## <a name="step-3-configure-the-pool"></a>Passo 3: configurar o conjunto

Depois de definir o escalão de preço, clique em Configurar conjunto, onde pode adicionar bases de dados, definir as eDTUs e o armazenamento (GBs do conjunto) do conjunto, e definir o número máximo e mínimo de eDTUs para as bases de dados elásticas no conjunto.

1. Clique em **Configurar conjunto**
2. Selecione as bases de dados que pretende adicionar ao conjunto. Este passo é opcional ao criar o conjunto. As bases de dados podem ser adicionadas depois de o conjunto ter sido criado.
    Para adicionar bases de dados, clique em **Adicionar base de dados**, clique nas bases de dados que pretende adicionar e, em seguida, clique no botão **Selecionar**.

    ![Adicionar bases de dados](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Se as bases de dados com que está a trabalhar tiverem telemetria de histórico de utilização suficiente, o gráfico **Utilização de GB e eDTU estimada** e o gráfico de barras **Utilização de eDTU real** são atualizados para o ajudar a tomar decisões de configuração. Além disso, o serviço poderá apresentar uma mensagem de recomendação para o ajudar a dimensionar corretamente o conjunto. Consulte a secção [Recomendações Dinâmicas](#dynamic-recommendations).

3. Utilize os controlos na página **Configurar conjunto** para explorar as definições e configurar o conjunto. Consulte [Limites dos conjuntos elásticos](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) para obter mais detalhes sobre os limites de cada escalão de serviço e consulte [Considerações sobre preço e desempenho para conjuntos de bases de dados elásticas](sql-database-elastic-pool-guidance.md) para obter instruções detalhadas sobre como dimensionar corretamente um conjunto. Para obter mais detalhes sobre as definições do conjunto, consulte [Propriedades do conjunto elástico](sql-database-elastic-pool.md#elastic-pool-and-elastic-database-properties).

    ![Configurar o Conjunto Elástico](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Clique em **Selecionar** no painel **Configurar conjunto** depois de alterar as definições.
5. Clique em **OK** para criar o conjunto.


## <a name="understand-pool-recommendations"></a>Compreender as recomendações de conjunto

O serviço Base de Dados SQL avalia o histórico de utilização e recomenda um ou mais conjuntos quando é mais rentável do que utilizar bases de dados individuais. Cada recomendação está configurada com um subconjunto exclusivo das bases de dados do servidor que melhor se adequam ao conjunto.

![conjunto recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

A recomendação de conjunto é composta por:

- Um escalão de preço para o conjunto (Básico, Standard ou Premium)
- O número adequado de **eDTUs POR CONJUNTO** (também denominado Número máximo de eDTUs por conjunto)
- O **número máximo de eDTUs** e o **número mínimo de eDTUs** por base de dados
- A lista de bases de dados recomendadas para o conjunto

O serviço tem em conta os últimos 30 dias de telemetria quando recomenda conjuntos. Para uma base de dados ser considerada candidata para um conjunto de bases de dados elásticas, tem de existir durante, pelo menos, 7 dias. As bases de dados que já estão num conjunto de bases de dados elásticas não são consideradas candidatas para recomendações de conjunto de bases de dados elásticas.

O serviço avalia as necessidades de recursos e a relação custo-eficácia da movimentação das bases de dados individuais em cada escalão de serviço para conjuntos do mesmo escalão. Por exemplo, todas as bases de dados Standard num servidor são avaliadas para determinar a respetiva adequação a um Conjunto Elástico Standard. Isto significa que o serviço não faz recomendações entre escalões, tal como mover uma base de dados Standard para um conjunto Premium.

### <a name="dynamic-recommendations"></a>Recomendações dinâmicas

Depois de adicionar as bases de dados ao conjunto, as recomendações serão geradas dinamicamente com base no histórico de utilização das bases de dados que selecionou. Estas recomendações serão apresentadas no gráfico de utilização de eDTUs e GBs, bem como numa faixa de recomendação no topo do painel **Configurar conjunto**. Estas recomendações visam ajudá-lo a criar um conjunto otimizado para as suas bases de dados específicas.

![recomendações dinâmicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Gerir um conjunto elástico da Base de Dados SQL com o portal](sql-database-elastic-pool-manage-portal.md)
- [Gerir um conjunto elástico da Base de Dados SQL com o PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Gerir um conjunto elástico da Base de Dados SQL com C#](sql-database-elastic-pool-manage-csharp.md)
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)



<!--HONumber=Nov16_HO3-->


