---
title: "Carregar dados para o Azure SQL Data Warehouse – Data Factory | Microsoft Docs"
description: Este tutorial carrega dados para o Azure SQL Data Warehouse, utilizando o Azure Data Factory e utiliza uma base de dados do SQL Server como origem de dados.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Carregar dados para o SQL Data Warehouse com o Data Factory

Pode utilizar o Azure Data Factory para carregar dados para o Azure SQL Data Warehouse a partir de qualquer o [arquivos de dados de origem suportados](../data-factory/copy-activity-overview.md). Por exemplo, pode carregar dados de uma base de dados SQL do Azure ou uma base de dados Oracle para um SQL data warehouse, utilizando o Data Factory. Tutorial neste artigo mostra como carregar dados a partir de uma base de dados do SQL Server no local para um SQL data warehouse.

**Estimativa de tempo**: neste tutorial demora cerca de 10 a 15 minutos a concluir depois dos pré-requisitos são cumpridos.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário um **base de dados do SQL Server** com tabelas que contêm os dados a ser copiadas para o armazém de dados do SQL Server.  

- É necessário um online **SQL Data Warehouse**. Se ainda não tiver um armazém de dados, saiba como [criar um Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md).

- É necessário um **conta do Storage do Azure**. Se ainda não tiver uma conta de armazenamento, saiba como [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). Para melhor desempenho, localize a conta de armazenamento e o armazém de dados na mesma região do Azure.

## <a name="configure-a-data-factory"></a>Configurar uma fábrica de dados
1. Inicie sessão no [Portal do Azure][].
2. Localize o seu armazém de dados e clique para abri-lo.
3. No painel principal, clique em **carga dados** > **do Azure Data Factory**.

    ![Iniciar o Assistente de dados de carga](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Se não tiver uma fábrica de dados na sua subscrição do Azure, verá um **nova fábrica de dados** caixa de diálogo no separador do browser. Preencha as informações pedidas e clique em **criar**. Depois de criar a fábrica de dados, o **nova fábrica de dados** fecha a caixa de diálogo e ver o **selecione Data Factory** caixa de diálogo.

    Se tiver uma ou mais dos data factories já existe na subscrição do Azure, consulte o **selecione Data Factory** caixa de diálogo. Na caixa de diálogo, pode selecionar uma fábrica de dados existente ou clique em **criar nova fábrica de dados** para criar um novo.

    ![Configurar fábrica de dados](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. No **selecione Data Factory** caixa de diálogo, o **carregar dados** opção está selecionada por predefinição. Clique em **seguinte** para começar a criar uma tarefa de carregamento de dados.

## <a name="configure-the-data-factory-properties"></a>Configurar as propriedades de fábrica de dados
Agora que criou uma fábrica de dados, o passo seguinte consiste em configurar os agenda do carregamento de dados.

1. Para **nome da tarefa**, introduza **DWLoadData fromSQLServer**.
2. Utilizar a predefinição **executar agora uma vez** opção, clique em **seguinte**.

    ![Configurar agenda de carga](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>Configurar o arquivo de dados de origem e o gateway
Agora, indique ao fábrica de dados sobre a base de dados no local do SQL Server que pretende carregar os dados.

1. Escolha **do SQL Server** os dados de origem suportada armazenar catálogo e clique em **seguinte**.

    ![Selecione a origem do SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. A **especificar a base de dados do SQL Server no local** é apresentada a caixa de diálogo. O primeiro **nome da ligação** campo é automaticamente preenchido. O campo segundo pede-lhe o nome do **Gateway**. Se estiver a utilizar uma fábrica de dados existente que já tem um gateway, pode reutilizar o gateway, selecionando-na lista pendente. Clique em de **criar Gateway** ligação para criar um Data Management Gateway.  

    > [!NOTE]
    > Se o arquivo de dados de origem for no local ou numa máquina virtual IaaS do Azure, é necessário um Gateway de gestão de dados. Um gateway tem uma relação de 1-1, com uma fábrica de dados. Não é possível utilizar a partir de outro fábrica de dados, mas pode ser utilizado por vários dados carregar tarefas com a mesma fábrica de dados. Um gateway pode ser utilizado para ligar a vários arquivos de dados ao executar tarefas de carregamento de dados.
    >
    > Para obter informações detalhadas sobre o gateway, consulte [Data Management Gateway](../data-factory/v1/data-factory-data-management-gateway.md) artigo.

3. A **criar Gateway** é apresentada a caixa de diálogo. Para nome, introduza **GatewayForDWLoading**e clique em **criar**.

4. A **configurar Gateway** é apresentada a caixa de diálogo. Clique em **lançar a configuração rápida neste computador** automaticamente transferir, instalar e registar o Gateway de gestão de dados no seu computador atual. O progresso é apresentado numa janela de pop-up. Se a máquina não é possível ligar ao arquivo de dados, pode manualmente [transferir e instalar o gateway](https://www.microsoft.com/download/details.aspx?id=39717) num computador que possam ligar aos dados armazenar e, em seguida, utilizar a chave para registar.
    > [!NOTE]
    > A configuração rápida nativamente funciona com o Microsoft Edge e do Internet Explorer. Se estiver a utilizar o Google Chrome, primeiro de instalar a extensão de ClickOnce da loja de web do Chrome.

    ![Inicie a configuração rápida](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Aguarde que a configuração do gateway concluir. Assim que o gateway está registado com êxito e estiver online, fecha a janela de pop-up e o novo gateway aparece no campo gateway. Em seguida, preencha o resto necessário campos da seguinte forma, em seguida, clique em **seguinte**.
    - **Nome do servidor**: nome do SQL Server no local.
    - **Nome da base de dados**: base de dados do SQL Server.
    - **Encriptação de credenciais**: utilizar a predefinição "pelo browser do web".
    - **Tipo de autenticação**: Escolha o tipo de autenticação que está a utilizar.
    - **Nome de utilizador** e **palavra-passe**: introduza o nome de utilizador e palavra-passe para um utilizador que tenha permissão para copiar os dados.

    ![Inicie a configuração rápida](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. O próximo passo é escolher as tabelas a partir dos quais pretende copiar os dados. Pode filtrar as tabelas através da utilização de palavras-chave. E pode pré-visualizar o esquema de dados e tabela, no painel inferior. Depois de concluir a seleção, clique em **seguinte**.

    ![Selecionar tabelas](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Configurar o destino, o SQL Data Warehouse

Agora, indique ao fábrica de dados sobre as informações de destino.

1. As informações de ligação do SQL Data Warehouse é automaticamente preenchidas. Introduza a palavra-passe para o nome de utilizador. e clique em **seguinte**.

    ![Configurar o destino](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. Um mapeamento de tabela inteligente aparece que mapas de origem com base nos nomes de tabela de tabelas de destino. Se a tabela não existe no destino, por predefinição ADF irá criar uma com o mesmo nome (Isto aplica-se para o SQL Server ou base de dados do Azure SQL Server como origem). Também pode optar por mapear para uma tabela existente. Reveja e clique em **seguinte**.

    ![Tabelas de mapa](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Reveja o mapeamento de esquema e procurar erros ou mensagens de aviso. Mapeamento inteligente é baseado no nome da coluna. Se houver uma conversão de tipo de dados não suportados entre a coluna de origem e de destino, verá uma mensagem de erro juntamente com a tabela correspondente. Se optar por permitir automática de fábrica de dados criar as tabelas, conversão de tipo de dados adequada pode ocorrer se for necessário para corrigir a incompatibilidade entre os arquivos de origem e destino.

    ![Esquema de mapa](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Clique em **Seguinte**.

## <a name="configure-the-performance-settings"></a>Configurar as definições de desempenho
As configurações de desempenho, pode configura uma conta de armazenamento do Azure utilizada para sincronizar os dados antes que carrega para utilização do SQL Data Warehouse performantly [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). Depois de terminar a cópia, os dados no armazenamento provisórias serão eliminados automaticamente.

Selecione uma conta de armazenamento do Azure existente e clique em **seguinte**.

![Configurar o teste de blob](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Reveja as informações de resumo e implemente o pipeline

Reveja a configuração e clique em **concluir** botão para implementar o pipeline.

![Implementar a fábrica de dados](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Progresso de carregamento de dados de monitor

Pode ver o progresso da implementação e os resultados no **implementação** página.

1. Após a conclusão da implementação, clique na ligação que indica que **clique aqui para monitorizar o pipeline de cópia** para monitorizar o progresso do carregamento de dados.

    ![Monitorizar o pipeline](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. Criado recentemente **DWLoadData fromSQLServer** pipeline de carregamento de dados é automaticamente selecionado o lado esquerdo **Explorador de recursos**.

    ![Pipeline de vista](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Clique no pipeline no painel do meio para ver o estado detalhado para cada tabela que mapeia para uma atividade.

    ![Atividade de tabela da vista](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. Mais clique para uma atividade e ver os dados ao carregar os detalhes no painel correto, incluindo o tamanho dos dados, linhas, débito, etc.

    ![Ver detalhes de atividade de tabela](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. Para iniciar esta vista de monitorização posterior, aceda ao seu armazém de dados do SQL Server, clique em **carga dados > Azure Data Factory**, selecione a fábrica e escolha **monitorizar existente a carregar tarefas**.

## <a name="next-steps"></a>Passos seguintes

Para migrar a base de dados para o SQL Data Warehouse, consulte [descrição geral da migração](sql-data-warehouse-overview-migrate.md).

Para mais informações sobre o Azure Data Factory e as respetivas capacidades de movimento de dados, consulte os artigos seguintes:

- [Introdução ao Azure Data Factory](../data-factory/introduction.md)
- [Mover dados utilizando a atividade de cópia](../data-factory/copy-activity-overview.md)
- [Mover dados para e do Azure SQL Data Warehouse, utilizando o Azure Data Factory](../data-factory/connector-azure-sql-data-warehouse.md)

Para explorar os dados no armazém de dados do SQL Server, consulte os artigos seguintes:

- [Ligar ao SQL Data Warehouse com o Visual Studio e SSDT](sql-data-warehouse-query-visual-studio.md)
- [Dados do elemento visual com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Portal do Azure]: https://portal.azure.com
