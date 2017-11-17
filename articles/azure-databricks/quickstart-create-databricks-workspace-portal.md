---
title: "Início rápido: Execute uma tarefa de Spark primeiro Databricks do Azure através do portal do Azure | Microsoft Docs"
description: "O guia de introdução mostra como utilizar o portal do Azure para criar uma área de trabalho do Azure Databricks, um cluster do Apache Spark e executar uma tarefa de Spark."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: d384a1aef89941c2c9b547e5e0d05bb562578393
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Início rápido: Executar uma tarefa do Spark no Azure Databricks no portal do Azure

Este guia de introdução mostra como criar uma área de trabalho do Azure Databricks e um cluster do Apache Spark dentro da área de trabalho. Por fim, irá aprender a executar uma tarefa do Spark no Databricks cluster. Para obter mais informações sobre Databricks do Azure, consulte [Novidades Databricks do Azure?](what-is-azure-databricks.md)

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-databricks-workspace"></a>Criar uma área de trabalho Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks no portal do Azure. 

1. No portal do Azure, clique em  **+** , clique em **dados + análise**e, em seguida, clique em **Databricks do Azure (pré-visualização)**. 

    ![Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Databricks do Azure (pré-visualização)**, clique em **criar**.

    > [!NOTE]
    > Databricks do Azure está atualmente em pré-visualização limitada. Se pretender que a sua subscrição do Azure para ser considerada para adicionar à lista branca para a pré-visualização, deve preencher o [formulário de inscrição](https://databricks.azurewebsites.net/).

2. Em **Azure Databricks serviço**, forneça os valores seguintes:

    ![Criar uma área de trabalho do Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "criar uma área de trabalho do Azure Databricks")

    * Para **nome da área de trabalho**, forneça um nome para a sua área de trabalho Databricks.
    * Para **subscrição**, na lista pendente, selecione a sua subscrição do Azure.
    * Para **grupo de recursos**, especifique se pretende criar um novo grupo de recursos ou utilize uma já existente. Um grupo de recursos é um contentor que retém recursos relacionados para uma solução do Azure. Para obter mais informações, consulte [descrição geral do grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).
    * Para **localização**, selecione **EUA Leste 2**. Para outras regiões, consulte [serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

3. Clique em **Criar**.

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho Databricks que criou e, em seguida, clique em **inicializar a área de trabalho**.

2. São redirecionados para o portal do Azure Databricks. No portal, clique em **Cluster**.

    ![Databricks no Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks no Azure")

3. No **novo cluster** , indique os valores para criar um cluster.

    ![Criar um cluster do Spark de Databricks no Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "cluster do Spark de Databricks criar no Azure")

    * Introduza um nome para o cluster.
    * Certifique-se de que seleciona o **terminar após ___ minutos de atividade** caixa de verificação. Forneça uma duração (em minutos) para terminar o cluster, se o cluster não está a ser utilizado.
    * Aceite todos os outros valores predefinidos. 
    * Clique em **Criar cluster**. Depois do cluster está em execução, pode anexar blocos de notas para o cluster e executar tarefas de Spark.

Para obter mais informações sobre a criação de clusters, consulte [criar um cluster do Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Execute uma tarefa de Spark SQL

Antes de começar nesta secção, tem de concluir o seguinte:

* [Criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Transferir um ficheiro JSON de exemplo [a partir do Github](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json). 
* Carregue o ficheiro JSON de exemplo para a conta de armazenamento do Azure que criou. Pode utilizar [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar ficheiros.

Execute os seguintes passos para criar um bloco de notas no Databricks, configure o bloco de notas para ler dados a partir de uma conta de armazenamento de Blobs do Azure e, em seguida, execute uma tarefa de Spark SQL nos dados.

1. No painel esquerdo, clique em **área de trabalho**. Do **área de trabalho** pendente, clique **criar**e, em seguida, clique em **bloco de notas**.

    ![Criar Bloco de notas no Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "criar bloco de notas no Databricks")

2. No **bloco de notas criar** diálogo caixa, introduza um nome, selecione **Scala** como o idioma e selecione o cluster do Spark que criou anteriormente.

    ![Criar Bloco de notas no Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "criar bloco de notas no Databricks")

    Clique em **Criar**.

3. No seguinte fragmento, substitua `{YOUR STORAGE ACCOUNT NAME}` com o nome de conta de armazenamento do Azure que criou e `{YOUR STORAGE ACCOUNT ACCESS KEY}` com a chave de acesso da conta de armazenamento. Cole o fragmento numa célula vazia no bloco de notas e, em seguida, prima SHIFT + ENTER para executar a célula do código. Este fragmento configura o bloco de notas para ler dados a partir de um armazenamento de Blobs do Azure.

       spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")
    
    Para obter instruções sobre como obter a chave de conta do storage, consulte [gerir as chaves de acesso de armazenamento](../storage/common/storage-create-storage-account.md#manage-your-storage-account)

    > [!NOTE]
    > Também pode utilizar o Azure Data Lake Store com um cluster do Spark no Azure Databricks. Para obter instruções, consulte [utilize Data Lake Store com o Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store).

4. Executar uma instrução SQL para criar uma tabela temporária, utilizando os dados do ficheiro de dados JSON de exemplo, **small_radio_json.json**. No seguinte fragmento, substitua os valores de marcador de posição do nome do contentor e o nome da conta de armazenamento. Cole o fragmento numa célula código no bloco de notas e, em seguida, prima SHIFT + ENTER. No fragmento, `path` indica a localização do ficheiro JSON de exemplo que carregou para a sua conta do Storage do Azure.

    ```sql
    %sql 
    CREATE TEMPORARY TABLE radio_sample_data
    USING json
    OPTIONS (
     path "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/small_radio_json.json"
    )
    ```

    Depois do comando for concluído com êxito, terá de todos os dados do ficheiro JSON como uma tabela num cluster de Databricks.

    O `%sql` comando mágica do idioma permite-lhe executar um código SQL do bloco de notas, mesmo que o bloco de notas é de outro tipo. Para obter mais informações, consulte [mistura de idiomas de um bloco de notas](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

5. Vamos observar um instantâneo dos dados JSON de exemplo para compreender melhor a consulta que iremos executar. Cole o seguinte fragmento na célula do código e prima **SHIFT + ENTER**.

    ```sql
    %sql 
    SELECT * from radio_sample_data
    ```

6. Ver um resultado da tabela, como mostrado na captura de ecrã seguinte, (apenas algumas colunas são apresentadas):

    ![Dados JSON de exemplo](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "dados JSON de exemplo")

    Entre outras detalhes, os dados de exemplo captura género do público-alvo de um canal de botões de opção (nome de coluna, **sexo**) e se a sua subscrição é gratuita ou paga (nome de coluna, **nível**).

7. Agora, criar uma representação visual destes dados para mostrar para cada sexo, quantos utilizadores têm contas gratuitas e quantas estão paga subscritores. Na parte inferior do resultado tabela, clique em de **gráfico de barras** ícone e, em seguida, clique em **desenhar opções**.

    ![Criar o gráfico de barras](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "criar o gráfico de barras")

8. No **personalizar desenhar**, arrastar e largar os valores, conforme mostrado na captura de ecrã.

    ![Personalizar o gráfico de barras](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "personalizar o gráfico de barras")

    * Definir **chaves** para **sexo**.
    * Definir **agrupamentos de séries** para **nível**.
    * Definir **valores** para **nível**.
    * Definir **agregação** para **CONTAGEM**.

    Clique em **Aplicar**.

9. O resultado mostra a representação visual, conforme descrito na seguinte captura de ecrã:

     ![Personalizar o gráfico de barras](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "personalizar o gráfico de barras")

## <a name="clean-up-resources"></a>Limpar recursos

Ao criar o cluster do Spark, se tiver selecionado a caixa de verificação **terminar após ___ minutos de atividade**, o cluster terminará automaticamente se tenham estado inativo durante o período de tempo especificado.

Se não selecionou a caixa de verificação, tem de terminar manualmente o cluster. Para tal, a área de trabalho do Azure Databricks, no painel esquerdo, clique em **Clusters**. Para o cluster que pretende terminar, mova o cursor sobre o botão de reticências em **ações** coluna e clique em de **terminar** ícone.

![Terminar Databricks cluster](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "terminar Databricks cluster")

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um cluster do Spark no Azure Databricks e foi executada uma tarefa de Spark utilizando os dados no armazenamento do Azure. Também pode ver [origens de dados de Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) para saber como importar dados a partir de outras origens de dados para o Azure Databricks. Avançar para o artigo seguinte para saber como utilizar o Azure Data Lake Store com Databricks do Azure.

> [!div class="nextstepaction"]
>[Utilizar o Data Lake Store com Databricks do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store)