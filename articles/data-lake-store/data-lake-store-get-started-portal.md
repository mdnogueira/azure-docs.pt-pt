<properties 
   pageTitle="Introdução ao Data Lake Store | Azure" 
   description="Utilizar o portal para criar uma conta do Data Lake Store e executar operações básicas no Data Lake Store" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# Introdução ao Azure Data Lake Store utilizando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a utilizar o Portal do Azure para criar uma conta do Azure Data Lake Store e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake Store, veja [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md).

## Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte:

- **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## Aprende depressa com vídeos?

Veja os vídeos seguintes para começar a utilizar o Data Lake Store.

* [Criar uma conta do Data Lake Store](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gerir dados no Data Lake Store com o Explorador de Dados](https://mix.office.com/watch/icletrxrh6pc)

## Criar uma conta do Azure Data Lake Store

1. Inicie sessão no novo [Portal do Azure](https://portal.azure.com).

2. Clique em **NOVO**, **Dados + Armazenamento** e, em seguida, em **Azure Data Lake Store**. Leia as informações no painel **Azure Data Lake Store** e, em seguida, clique em **Criar** no canto inferior esquerdo do painel.

3. No painel **Novo Data Lake Store**, forneça os valores, conforme mostrado na captura de ecrã abaixo:

    ![Criar uma nova conta do Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Create a new Azure Data Lake account")

    - **Subscrição**. Selecione a subscrição sob a qual pretende criar uma nova conta do Data Lake Store.
    - **Grupo de Recursos**. Selecione um grupo de recursos existente ou clique em **Criar um grupo de recursos** para criar um. Um grupo de recursos é um contentor que retém recursos relacionados para uma aplicação. Para obter mais informações, veja [Grupos de Recursos no Azure](resource-group-overview.md#resource-groups).
    - **Localização**: selecione uma localização onde pretende criar a conta do Data Lake Store.

4. Selecione **Afixar ao Startboard** se pretender que a conta do Data Lake Store esteja acessível a partir do Startboard.

5. Clique em **Criar**. Se optar por afixar a conta ao startboard, é direcionado novamente para o startboard e poderá ver o progresso do aprovisionamento da conta do Data Lake Store. Depois de a conta do Data Lake Store estar aprovisionada, é apresentado o respetivo painel.

6. Expanda o menu pendente **Essentials** para ver as informações sobre a sua conta do Data Lake Store, como o grupo de recursos de que faz parte, a localização, etc. Clique no ícone **Início Rápido** para ver as ligações para outros recursos relacionados com o Data Lake Store.

    ![A sua conta do Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Your Azure Data Lake account")

## <a name="createfolder"></a>Criar pastas na conta do Azure Data Lake Store

Pode criar pastas na sua conta do Data Lake Store para gerir e armazenar dados.

1. Abra a conta do Data Lake Store que acabou de criar. No painel esquerdo, clique em **Procurar**, clique em **Data Lake Store** e, em seguida, no painel Data Lake Store, clique no nome da conta sob o qual pretende criar as pastas. Se afixou a conta ao startboard, clique no mosaico dessa conta.

2. No painel da conta do Data Lake Store, clique em **Explorador de Dados**.

    ![Criar pastas na conta do Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Create folders in Data Lake Store account")

3. No painel da conta do Data Lake Store, clique em **Nova Pasta**, introduza um nome para a nova pasta e, em seguida, clique em **OK**.
    
    ![Criar pastas na conta do Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Create folders in Data Lake Store account")
    
    A pasta recém-criada será listada no painel **Explorador de Dados**. Pode criar pastas aninhadas até qualquer nível.

    ![Criar pastas na conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Create folders in Data Lake account")


## <a name="uploaddata"></a>Carregar dados para a conta do Azure Data Lake Store

Pode carregar os dados para a conta do Azure Data Lake Store diretamente no nível de raiz ou para uma pasta que criou na conta. Na captura de ecrã abaixo, siga os passos para carregar um ficheiro para uma sub-pasta a partir do painel **Explorador de Dados**. Nesta captura de ecrã, o ficheiro é carregado para uma sub-pasta mostrada na estrutura (marcado numa caixa vermelha).

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Upload data")


## <a name="properties"></a>Propriedades e ações disponíveis nos dados armazenados

Clique no ficheiro recém-adicionado para abrir o painel **Propriedades**. As propriedades associadas ao ficheiro e as ações que pode efetuar no ficheiro estão disponíveis neste painel. Também pode copiar o caminho completo para o ficheiro na sua conta do Azure Data Lake Store, realçada na caixa vermelha na captura de ecrã abaixo.

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Properties on the data")

* Clique em **Pré-visualizar** para ver uma pré-visualização do ficheiro, diretamente a partir do browser. Também pode especificar o formato da pré-visualização. Clique em **Pré-visualizar**, clique em **Formato** no painel **Pré-visualização do Ficheiro** e, no painel **Formato de Pré-visualização do Ficheiro** especifique as opções, como o número de linhas a apresentar, a codificação a utilizar, o delimitador a utilizar, etc.

  ![Formato de pré-visualização do ficheiro](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "File preview format")

* Clique em **Transferir** para transferir o ficheiro para o seu computador.

* Clique em **Mudar o nome do ficheiro** para mudar o nome do ficheiro.

* Clique em **Eliminar ficheiro** para eliminar o ficheiro.


## Proteger os dados

Pode proteger os dados armazenados na sua conta do Azure Data Lake Store utilizando o Azure Active Directory e o controlo de acesso (ACLs). Para obter instruções sobre como fazê-lo, veja [Proteger dados no Azure Data Lake Store](data-lake-store-secure-data.md).


## Eliminar conta do Azure Data Lake Store

Para eliminar uma conta do Azure Data Lake Store, a partir do painel Data Lake Store, clique em **Eliminar**. Para confirmar a ação, ser-lhe-á pedido para introduzir o nome da conta que pretende eliminar. Introduza o nome da conta e, em seguida, clique em **Eliminar**.

![Eliminar conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Delete Data Lake account")


## Passos seguintes

- [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)
- [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Registos de diagnóstico de acesso para o Data Lake Store](data-lake-store-diagnostic-logs.md)



<!--HONumber=sep16_HO2-->


