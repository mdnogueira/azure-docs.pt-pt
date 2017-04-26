---
title: "Introdução ao Azure Data Lake Analytics com o portal do Azure | Microsoft Docs"
description: 'Saiba como utilizar o portal do Azure para criar uma conta do Data Lake Analytics, criar uma tarefa do Data Lake Analytics com U-SQL e submeter a tarefa. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 64c5869f3e66c249fefa9af228fe1b33974cf293
ms.lasthandoff: 04/25/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Tutorial: introdução ao Azure Data Lake Analytics com o portal do Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o portal do Azure para criar contas do Azure Data Lake Analytics, definir tarefas em [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter tarefas para o serviço Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

Neste tutorial, irá desenvolver uma tarefa que lê um ficheiro de valores separados por tabulações (TSV) e converte-o num ficheiro de valores separados por vírgulas (CSV). Para seguir o mesmo tutorial, utilizando outras ferramentas suportadas, clique nos separadores na parte superior desta secção. Assim que a sua primeira tarefa for bem sucedida, pode começar a escrever transformações de dados mais complexas com o U-SQL.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Criar conta de Data Lake Analytics
Tem de ter uma conta de Data Lake Analytics antes de poder executar quaisquer tarefas.

Cada conta de Data Lake Analytics tem uma dependência de conta do Azure Data Lake Store.  Esta conta é referida como a conta predefinida do Data Lake Store.  Pode criar a conta de Data Lake Store previamente ou quando criar a conta de Data Lake Analytics. Neste tutorial, irá criar a conta de Data Lake Store com a conta de Data Lake Analytics.

**Criar uma conta de Data Lake Analytics**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo**, clique em **Inteligência + análise** e, em seguida, clique em **Data Lake Analytics**.
3. Escreva ou selecione os seguintes valores:

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

   * **Nome**: nome da conta do Data Lake Analytics (Apenas são permitidas letras minúsculas e números).
   * **Subscrição**: selecione a subscrição do Azure utilizada para a conta da Análise.
   * **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo. O Azure Resource Manager permite trabalhar com os recursos na aplicação como um grupo. Para obter mais informações, consulte o artigo [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   * **Localização**. Selecione um centro de dados do Azure para a conta do Data Lake Analytics.
   * **Data Lake Store**: clique em *Configurar definições necessárias*. Siga as instruções para criar uma nova conta do Data Lake Store ou selecione uma conta existente. Cada conta do Data Lake Analytics tem uma conta do Data Lake Store dependente. A conta do Data Lake Analytics e a conta do Data Lake Store dependente têm de estar localizadas no centro de dados do Azure.
4. Selecione o Escalão de Preço  
5. Clique em **Criar**. Leva-o de volta para o ecrã principal do portal no qual é apresentado um novo mosaico, que mostra "Implementar Azure Data Lake Analytics". O processo de implementação demorará alguns minutos a criar uma conta do Data Lake Analytics. Quando a conta estiver criada, o portal abre a conta num novo painel.

Depois de uma conta de Data Lake Analytics ser criada, pode adicionar mais contas de Data Lake Store e contas de Armazenamento do Azure. Para obter instruções, consulte [Gerir origens de dados de contas de Data Lake Analytics](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

## <a name="prepare-source-data"></a>Preparar os dados de origem
Neste tutorial, irá processar registos de pesquisa.  O registo de pesquisa pode ser armazenado no Data Lake Store ou no Armazenamento de Blobs do Azure.

O portal do Azure fornece uma interface de utilizador para copiar ficheiros de dados de exemplo para a conta predefinida do Data Lake Store, que incluem um ficheiro de registo de pesquisa.

**Copiar ficheiros de dados de exemplo**

1. A partir do [portal do Azure](https://portal.azure.com), abra a sua conta do Data Lake Analytics.  Veja [Gerir contas do Data Lake Analytics](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account) para criar uma e abrir a conta no portal.
2. Expanda o painel **Princípios Básicos** e, em seguida, clique em **Explorar scripts de exemplo**. Esta ação abre outro painel com o nome **Scripts de Exemplo**.

    ![Script de exemplo do portal do Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-sample-scripts.png)
3. Clique em **Dados de Exemplo em Falta** para copiar os ficheiros de dados de exemplo. Quando estiver concluído, o portal mostra a mensagem **Dados de exemplo atualizados com êxito**.
4. No painel de conta de Data Lake Analytics, clique em **Explorador de Dados** na parte superior.

    ![Botão do explorador de dados da Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    São abertos dois painéis. Um é o **Explorador de Dados** e o outro é a conta predefinida do Data Lake Store.
5. No painel de conta predefinida do Data Lake Store, clique em **Exemplos** para expandir a pasta e clique em **Dados** para expandir a pasta. Deverá ver os seguintes ficheiros e pastas:

   * AmbulanceData/
   * AdsLog.tsv
   * SearchLog.tsv
   * version.txt
   * WebLog.log

     Neste tutorial, vai utilizar SearchLog.tsv.

Na prática, vai programar as suas aplicações para escreverem dados numa conta de armazenamento ligada ou para carregarem dados. Para carregar ficheiros, consulte [Carregar dados para o Data Lake Store](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) ou [Carregar dados para o Armazenamento de Blobs](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Criar e submeter tarefas de Data Lake Analytics
Depois de ter preparado os dados de origem, pode começar a desenvolver um script U-SQL.  

**Para submeter a uma tarefa**

1. A partir do painel de conta de Data Lake Analytics no portal, clique em **Nova Tarefa**.

    ![Botão de nova tarefa de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Se não vir o painel, consulte [Abrir uma conta de Data Lake Analytics a partir do portal](data-lake-analytics-manage-use-portal.md#access-adla-account).
2. Introduza o **Nome da Tarefa** e o seguinte script U-SQL:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    ![criar tarefas U-SQL de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

    Este script U-SQL lê o ficheiro de dados de origem, utilizando **Extractors.Tsv()**, e, em seguida, cria um ficheiro csv, utilizando **Outputters.Csv()**.

    Não modifique os dois caminhos, exceto se copiar o ficheiro de origem para uma localização diferente.  O Data Lake Analytics cria a pasta de saída se não existir.  Neste caso, estamos a utilizar caminhos relativos simples.  

    É mais simples utilizar caminhos relativos para ficheiros armazenados em contas do Data Lake predefinidas. Também pode utilizar caminhos absolutos.  Por exemplo

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Para saber mais sobre o U-SQL, consulte [Introdução à linguagem U-SQL de Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

1. Clique em **Submeter Tarefa** na parte superior.   
2. Aguarde até que o estado da tarefa seja alterado para **Com êxito**. Pode ver que a tarefa demorou cerca de um minuto para concluir.

    Caso a tarefa tenha falhado, consulte o artigo [Monitorizar e resolver problemas de tarefas de Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
3. Na parte inferior do painel, clique no separador **Saída** e, em seguida, clique em **SearchLog-from-Data-Lake.csv**. Pode pré-visualizar, transferir, mudar o nome e eliminar o ficheiro de saída.

    ![Propriedades do ficheiro de saída da tarefa de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)

## <a name="see-also"></a>Consultar também
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para começar a desenvolver aplicações U-SQL, consulte [Desenvolver scripts SQL-U, utilizando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).
* Para uma descrição geral da Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).
* Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)

