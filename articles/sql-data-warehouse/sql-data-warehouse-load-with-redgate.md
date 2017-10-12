---
title: "Utilizar o Redgate para carregar dados para o armazém de dados do Azure | Microsoft Docs"
description: "Saiba como utilizar o Estúdio da Plataforma de Dados do Redgate para cenários de armazenamento de dados."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: a38b237d5bfc0450c1ca79b53a5784dbb9bf8602
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-with-redgate-data-platform-studio"></a>Carregar dados com o Estúdio da Plataforma de Dados do Redgate
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

Este tutorial mostra-lhe como utilizar o [Estúdio da Plataforma de Dados do Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) para mover dados de um SQL Server no local para o Azure SQL Data Warehouse. O Estúdio da Plataforma de Dados aplica as otimizações e correções de compatibilidade mais adequadas, para que seja a forma mais rápida de começar a utilizar o SQL Data Warehouse.

> [!NOTE]
> [Redgate](http://www.red-gate.com) é um parceiro de longa data da Microsoft que fornece várias ferramentas do SQL Server. Esta funcionalidade no Estúdio da Plataforma de Dados tem sido disponibilizado livremente para utilização comercial e não comercial.
> 
> 

## <a name="before-you-begin"></a>Antes de começar
### <a name="create-or-identify-resources"></a>Criar ou identificar recursos
Antes de iniciar este tutorial, é necessário ter:

* **Base de Dados do SQL Server no local**: os dados que pretende importar para o SQL Data Warehouse têm de ser provenientes de um SQL Server no local (versão 2008R2 ou acima). O Estúdio da Plataforma de Dados não pode importar dados diretamente de uma Base de Dados SQL do Azure ou a partir de ficheiros de texto.
* **Conta de Armazenamento do Azure**: o Estúdio da Plataforma de Dados prepara os dados no Armazenamento de Blobs do Azure antes de os carregar para o SQL Data Warehouse. A conta de armazenamento tem de utilizar o modelo de implementação do "Resource Manager" (o predefinido), em vez do modelo de implementação "Clássico". Se ainda não tem uma conta de armazenamento, saiba como Criar uma conta de armazenamento. 
* **SQL Data Warehouse**: este tutorial move os dados do servidor do SQL Server no local para o SQL Data Warehouse, por isso deve ter um armazém de dados online. Se ainda não tiver um armazém de dados, saiba como Criar um Azure SQL Data Warehouse.

> [!NOTE]
> O desempenho é melhorado se a conta de armazenamento e o armazém de dados forem criados na mesma região.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Passo 1: iniciar sessão no Estúdio da Plataforma de Dados com a sua conta do Azure
Abra o browser e navegue para o Web site [Estúdio da Plataforma de Dados](https://www.dataplatformstudio.com/). Inicie sessão com a mesma conta do Azure que utilizou para criar o armazém de dados e a conta de armazenamento. Se o seu endereço de e-mail estiver associado a uma conta profissional ou escolar e uma conta Microsoft, certifique-se de que escolhe a conta que tem acesso aos seus recursos.

> [!NOTE]
> Se esta for a primeira vez que utiliza o Estúdio da Plataforma de Dados, é-lhe pedido para conceder a permissão da aplicação para gerir os recursos do Azure.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>Passo 2: iniciar o Assistente de Importação
No ecrã principal do DPS, selecione a ligação Importar para o Azure SQL Data Warehouse, para iniciar o assistente de importação.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Passo 3: instalar o Gateway do Estúdio da Plataforma de Dados
Para ligar à sua base de dados do SQL Server no local, tem de instalar o Gateway DPS. O gateway é um agente de cliente que fornece acesso ao seu ambiente no local, extrai os dados e carrega-os para a sua conta de armazenamento. Nunca transmite os dados através de servidores do Redgate. Para instalar o Gateway:

1. Clique na ligação**Criar Gateway**
2. Transfira e instale o Gateway com o instalador fornecido

![][2]

> [!NOTE]
> O Gateway pode ser instalado em qualquer máquina com acesso à rede para a base de dados do SQL Server de origem. Acede à base de dados do SQL Server através da autenticação do Windows com as credenciais do utilizador atual.
> 
> 

Depois de instalado, o estado do Gateway é alterado para Ligado e pode selecionar Seguinte.

## <a name="step-4-identify-the-source-database"></a>Passo 4: identificar a base de dados de origem
Na caixa de texto *Introduzir Nome do Servidor*, introduza o nome do servidor que aloja a base de dados e selecione **Seguinte**. Em seguida, no menu pendente, selecione a base de dados de onde pretende importar os dados.

![][3]

O DPS inspeciona a base de dados selecionada para importar tabelas. Por predefinição, o DPS importa todas as tabelas na base de dados. Pode selecionar ou desmarcar tabelas ao expandir a ligação Todas as Tabelas. Selecione o botão Seguinte para continuar.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Passo 5: escolher uma conta de armazenamento para colocar os dados em posição armazém
O DPS pede-lhe uma localização para colocar os dados em posição armazém. Escolha uma conta de armazenamento existente a partir da sua subscrição e selecione **Seguinte**.

> [!NOTE]
> O DPS irá criar um novo contentor de blobs na conta de armazenamento escolhida e utilizar uma pasta diferente para cada importação.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Passo 6: selecionar um armazém de dados
Em seguida, selecione uma base de dados online do [Azure SQL Data Warehouse](http://aka.ms/sqldw), para importar os dados. Depois de selecionar a base de dados, tem de introduzir as credenciais para ligar à base de dados e selecionar **Seguinte**.

![][5]

> [!NOTE]
> O DPS intercala as tabelas de dados de origem no armazém de dados. O DPS avisa-o se o nome da tabela precisar que substitua as tabelas existentes no armazém de dados. Opcionalmente, pode eliminar quaisquer objetos existentes no armazém de dados ao selecionar Eliminar todos os objetos existentes antes de importar.
> 
> 

## <a name="step-7-import-the-data"></a>Passo 7: importar os dados
O DPS confirma que gostaria importar os dados. Basta clicar no botão Iniciar importação, para iniciar a importação dos dados.

![][6]

O DPS apresenta uma visualização que mostra o progresso de extração e carregamento dos dados do SQL Server no local e o progresso da importação no SQL Data Warehouse.

![][7]

Assim que a importação estiver concluída, o DPS apresenta um resumo da importação de dados e um relatório de alterações das correções de compatibilidade que foram efetuadas.

![][8]

## <a name="next-steps"></a>Passos seguintes
Para explorar os seus dados dentro do SQL Data Warehouse, comece por visualizar:

* [Consultar o Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Visualizar dados com o Power BI][Visualize data with Power BI]

Para saber mais acerca do Estúdio da Plataforma de Dados do Redgate:

* [Visitar a home page do DPS](http://www.dataplatformstudio.com/)
* [Ver uma demonstração do DPS no Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Para obter uma descrição geral de outras formas de migração e carregamento dos dados no SQL Data Warehouse, consulte:

* [Migre a sua solução para o SQL Data Warehouse][Migrate your solution to SQL Data Warehouse]
* [Carregar dados para o Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md)

Para mais sugestões de desenvolvimento, consulte a [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
