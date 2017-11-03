---
title: Carregar terabytes de dados para o SQL Data Warehouse | Microsoft Docs
description: Demonstra como 1 TB de dados podem ser carregado para o Azure SQL Data Warehouse em 15 minutos com o Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8398a261ca33060b7709d818cb64bb1e8e2058b1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>1 TB no Azure SQL Data Warehouse em 15 minutos de carga com o Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [copiar dados de ou para o Azure SQL Data Warehouse, utilizando a versão 2 do Data Factory](../connector-azure-sql-data-warehouse.md).


[O Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é uma base de dados horizontal, baseada na nuvem, capaz de processar grandes volumes de dados tanto relacionais como não relacionais.  Baseado na arquitetura de processamento paralelo em grande escala (MPP), o SQL Data Warehouse está otimizado para cargas de trabalho de armazém de dados de empresa.  Oferece elasticidade da nuvem com a flexibilidade ao dimensionar o armazenamento e computação de forma independente.

Introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca utilizando **do Azure Data Factory**.  O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerido, que pode ser utilizado para preencher um SQL Data Warehouse com os dados do sistema existente e guardar a importantes tempo ao avaliar o SQL Data Warehouse e criar as suas soluções de análise. Seguem-se as principais vantagens de carregamento dos dados no Azure SQL Data Warehouse, utilizando o Azure Data Factory:

* **Fácil de configurar**: Assistente intuitivo do passo 5 com nenhuma scripting necessário.
* **Suporte de arquivo de dados avançados**: suporte incorporado para um conjunto avançado de arquivos de dados baseado na nuvem e no local.
* **Segura e compatível**: os dados são transferidos através de HTTPS ou ExpressRoute e presença global serviço assegura que os dados nunca sai do limite geográfico
* **Desempenho unparalleled utilizando o PolyBase** – utilizando o Polybase é a forma mais eficiente para mover dados para o Azure SQL Data Warehouse. Utilizar a funcionalidade de blob de teste, pode conseguir velocidades de carga elevada de todos os tipos de arquivos de dados para além do Blob storage do Azure, que o Polybase suporta por predefinição.

Este artigo mostra como utilizar o Assistente de cópia do Data Factory para carregar dados de 1 TB de armazenamento de Blobs do Azure para o Azure SQL Data Warehouse em 15 minutos, em mais 1,2 GBps débito.

Este artigo fornece instruções passo a passo para mover dados para o Azure SQL Data Warehouse, utilizando o Assistente para copiar.

> [!NOTE]
>  Para obter informações gerais sobre as capacidades da fábrica de dados de mover os dados da Azure SQL Data Warehouse, consulte [mover dados para e do armazém de dados SQL do Azure utilizando o Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artigo.
>
> Também pode criar pipelines com o portal do Azure, Visual Studio, PowerShell, etc. Consulte [Tutorial: copiar dados de Blobs do Azure para a SQL Database do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instruções rápidas com instruções passo a passo para utilizar a atividade de cópia no Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Pré-requisitos
* Armazenamento de Blobs do Azure: esta fase experimental utiliza o armazenamento de Blobs do Azure (GRS) para armazenar o conjunto de dados de teste TPC-H.  Se não tiver uma conta de armazenamento do Azure, saiba [como criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* [TPC-H](http://www.tpc.org/tpch/) dados:, vamos utilizar TPC-H como o conjunto de dados de teste.  Para tal, tem de utilizar `dbgen` do toolkit de TPC-H, que ajuda-o a gerar o conjunto de dados.  Ou pode transferir o código de origem para `dbgen` de [TPC ferramentas](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) e compilá-la pessoalmente ou transferir o binário compilado de [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Executar dbgen.exe com os seguintes comandos para gerar ficheiro simples de 1 TB para `lineitem` propagação de tabela em 10 ficheiros:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Agora copie os ficheiros gerados ao Blob do Azure.  Consulte [mover dados de e para um sistema de ficheiros no local utilizando o Azure Data Factory](data-factory-onprem-file-system-connector.md) para saber como fazê-lo utilizando a cópia do ADF.    
* O Azure SQL Data Warehouse: esta fase experimental carrega dados no Azure SQL Data Warehouse criada com as 6.000 DWUs

    Consulte [criar um Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para obter instruções detalhadas sobre como criar uma base de dados do armazém de dados do SQL Server.  Para obter o melhor desempenho de carga possível para o SQL Data Warehouse, utilizando o Polybase, vamos escolher o número máximo de unidades do Data Warehouse (DWUs) permitido na definição de desempenho, que é 6.000 DWUs.

  > [!NOTE]
  > Ao carregar a partir do Blob do Azure, os dados de desempenho de carregamento são diretamente proporcional ao número de DWUs configurar no armazém de dados SQL:
  >
  > Carregamento de 1 TB para 1.000 DWU SQL Data Warehouse assume 87 minutos (~ 200 MBps débito) carregar 1 TB para 2.000 DWU SQL Data Warehouse demora 46 minutos (~ 380 MBps débito) carregar 1 TB para 6.000 DWU SQL Data Warehouse demora 14 minutos (~1.2 GBps débito)
  >
  >

    Para criar um SQL Data Warehouse com 6.000 DWUs, mova o controlo de deslize de desempenho para a direita:

    ![Controlo de deslize de desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Para uma base de dados que não está configurado com as 6.000 DWUs, pode dimensioná-lo através do portal do Azure.  Navegue para a base de dados no portal do Azure e não existe um **escala** clique no botão no **descrição geral** painel mostrado na imagem seguinte:

    ![Botão de escala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique em de **escala** botão para abrir o painel seguinte, mova o controlo de deslize para o valor máximo e clique em **guardar** botão.

    ![Caixa de diálogo de escala](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Esta fase experimental carrega dados para utilizar o Azure SQL Data Warehouse `xlargerc` classe de recursos.

    Para alcançar melhor débito possíveis, tem de ser efetuada utilizando um utilizador do SQL Data Warehouse que pertencem a cópia `xlargerc` classe de recursos.  Saber como fazê-lo ao seguir [alterar um exemplo de classe de recursos de utilizador](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Crie o esquema da tabela de destino na base de dados do Azure SQL Data Warehouse, executando a seguinte instrução DDL:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
Com os passos de pré-requisitos foi concluídos, mas agora está prontas para configurar a atividade de cópia com o Assistente para copiar.

## <a name="launch-copy-wizard"></a>Inicie o Assistente de Cópia
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **+ novo** no canto superior esquerdo, clique em **Intelligence + análise**e clique em **Data Factory**.
3. No painel **Nova fábrica de dados**:

   1. Introduza **LoadIntoSQLDWDataFactory** para o **nome**.
       O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **nome da fábrica de dados "LoadIntoSQLDWDataFactory" não está disponível**, alterar o nome da fábrica de dados (por exemplo, yournameLoadIntoSQLDWDataFactory) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.  
   2. Selecione a sua **subscrição** do Azure.
   3. No Grupo de Recursos, siga um destes passos:
      1. Selecione **Utilizar existente** para selecionar um grupo de recursos já existente.
      2. Selecione **Criar novo** para introduzir um nome para um grupo de recursos.
   4. Selecione uma **localização** para a fábrica de dados.
   5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.  
   6. Clique em **Criar**.
4. Após concluir a criação, verá o painel **Data Factory**, conforme apresentado na imagem seguinte:

   ![Home page da fábrica de dados](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na home page do Data Factory, clique no mosaico **Copiar dados** para iniciar o **Assistente de Cópia**.

   > [!NOTE]
   > Se vir que o browser bloqueia enquanto estiver a "A autorizar…", desative/desmarque a definição **Bloquear cookies de terceiros e dados do site** (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar novamente o assistente.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Passo 1: Configurar a agenda do carregamento de dados
O primeiro passo é configurar os agendamento do carregamento de dados.  

Na página **Propriedades**:

1. Introduza **CopyFromBlobToAzureSqlDataWarehouse** para **nome da tarefa**
2. Selecione **executar agora uma vez** opção.   
3. Clique em **Seguinte**.  

    ![Assistente de cópia - página de propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Passo 2: Configurar a origem
Esta secção mostra-lhe os passos para configurar a origem: Blob do Azure que contém o TPC de 1 TB-os ficheiros de itens de linha de H.

1. Selecione o **Blob Storage do Azure** como os dados armazenar e clique em **seguinte**.

    ![Assistente de cópia - página selecionar origem](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Preencha as informações de ligação para a conta de armazenamento de Blobs do Azure e clique em **seguinte**.

    ![Assistente de cópia - informações de ligação de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Escolha o **pasta** TPC-H linha ficheiros de itens e clique em **seguinte**.

    ![Assistente para copiar - selecionar pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Após clicar em **seguinte**, as definições do formato de ficheiro são detetadas automaticamente.  Certifique-se de que delimitador de coluna é ' | 'em vez de vírgula predefinido','.  Clique em **seguinte** depois de ter pré-visualizado os dados.

    ![Assistente de cópia - definições do formato de ficheiro](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Passo 3: Configurar o destino
Esta secção mostra como configurar o destino: `lineitem` tabela na base de dados Azure SQL Data Warehouse.

1. Escolha **Azure SQL Data Warehouse** como destino de armazenar e clique em **seguinte**.

    ![Assistente de cópia - arquivo de dados de destino selecione](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Preencha as informações de ligação para o Azure SQL Data Warehouse.  Certifique-se de que especifica o utilizador que seja membro da função `xlargerc` (consulte o **pré-requisitos** secção para obter instruções detalhadas) e clique em **seguinte**.

    ![Assistente de cópia - informações de ligação de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Escolha a tabela de destino e clique em **seguinte**.

    ![Assistente de cópia - página de mapeamento de tabela](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na página de mapeamento de esquema, deixe opção "Aplicar mapeamento de colunas" desmarcada e clique em **seguinte**.

## <a name="step-4-performance-settings"></a>Passo 4: Definições de desempenho

**Permitir polybase** está selecionada por predefinição.  Clique em **Seguinte**.

![Assistente de cópia - página de mapeamento de esquema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Passo 5: Implementar e monitorizar os resultados de carga
1. Clique em **concluir** botão para implementar.

    ![Assistente de cópia - página de resumo](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Depois de concluída a implementação, clique em `Click here to monitor copy pipeline` para monitorizar a progresso de execução de cópia. Selecione o pipeline de cópia que criou no **atividade Windows** lista.

    ![Assistente de cópia - página de resumo](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Pode ver a cópia detalhes da execução **Explorador de janela de atividade** no painel à direita, incluindo o volume de dados de leitura de origem e escritos no destino, a duração e o débito médio para a execução.

    Como pode ver de captura de ecrã seguinte, copiar 1 TB de armazenamento de Blobs do Azure para o SQL Data Warehouse demorou 14 minutos, eficazmente alcançar o débito de GBps 1.22!

    ![Assistente de cópia - diálogo criada com êxito](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Melhores práticas
Seguem-se algumas melhores práticas para a base de dados do armazém de dados SQL do Azure a executar:

* Utilize uma classe de recurso superior ao carregar para um índice de COLUMNSTORE em cluster.
* Para associações mais eficientes, considere a utilização de distribuição hash, selecione uma coluna em vez da predefinição arredondar distribuição round robin.
* Para velocidades de carga, considere a utilização de área dinâmica para dados para dados transitórios.
* Crie estatísticas depois de concluir o carregamento do Azure SQL Data Warehouse.

Consulte [melhores práticas para o Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
* [Assistente de cópia do Data Factory](data-factory-copy-wizard.md) -este artigo fornece detalhes sobre o Assistente para copiar.
* [Copie o desempenho de atividade e o guia de otimização](data-factory-copy-activity-performance.md) -este artigo contém as medidas de desempenho de referência e o guia de otimização.
