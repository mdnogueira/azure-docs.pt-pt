---
title: "Utilizar o portal do Azure para começar a utilizar o Data Lake Store | Microsoft Docs"
description: "Utilizar o portal do Azure para criar uma conta do Data Lake Store e executar operações básicas no Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: fa13266993017374ba49709f8e22fbe6b03a28c7
ms.contentlocale: pt-pt
ms.lasthandoff: 08/07/2017

---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Introdução ao Azure Data Lake Store com o portal do Azure
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [python](data-lake-store-get-started-python.md)
>
> 

Aprenda a utilizar o portal do Azure para criar uma conta do Azure Data Lake Store e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações, veja [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md).

Os dois vídeos seguintes contêm as mesmas informações, conforme descrito neste artigo:

* [Criar uma conta do Data Lake Store](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gerir dados no Data Lake Store com o Data Explorer](https://mix.office.com/watch/icletrxrh6pc)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Azure Data Lake Store

1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, **Dados + Armazenamento** e, em seguida, em **Azure Data Lake Store**. Leia as informações no painel **Azure Data Lake Store** e, em seguida, clique em **Criar** no canto inferior esquerdo do painel.
3. No painel **Novo Data Lake Store**, forneça os valores, conforme mostrado na captura de ecrã seguinte:
   
    ![Criar uma nova conta do Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma nova conta do Azure Data Lake")
   
   * **Nome**. Introduza um nome exclusivo para a conta do Data Lake Store.
   * **Subscrição**. Selecione a subscrição sob a qual pretende criar uma nova conta do Data Lake Store.
   * **Grupo de Recursos**. Selecione um grupo de recursos existente ou selecione a opção **Criar novo** para criar um. Um grupo de recursos é um contentor que retém recursos relacionados para uma aplicação. Para obter mais informações, veja [Grupos de Recursos no Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Localização**: selecione uma localização onde pretende criar a conta do Data Lake Store.
   * **Definições de Encriptação**. Existem três opções:
     
     * **Não ativar a encriptação**.
     * **Utilizar chaves geridas pelo Azure Data Lake**.  se quiser que o Azure Data Lake Store efetue a gestão das chaves de encriptação.
     * **Escolha as chaves a partir do Azure Key Vault**. Pode selecionar um Azure Key Vault existente ou criar um novo. Para utilizar as teclas de um Key Vault, tem de atribuir permissões para a conta do Azure Data Lake Store para aceder ao Azure Key Vault. Para obter as instruções, veja [Atribuir permissões ao Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Encriptação do Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Encriptação do Data Lake Store")
       
        Clique em **OK** no painel **Definições de Encriptação**.

        Para obter mais informações, veja [Encriptação de dados no Azure Data Lake Store](./data-lake-store-encryption.md).

4. Clique em **Criar**. Se optar por afixar a conta no dashboard, é direcionado novamente para o dashboard e poderá ver o progresso do aprovisionamento da sua conta do Data Lake Store. Depois de a conta do Data Lake Store estar aprovisionada, é apresentado o respetivo painel.

Também pode criar uma conta do Data Lake Store com modelos do Azure Resource Manager. Estes modelos são acessíveis a partir dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):

- Sem encriptação de dados: [Implementar a conta do Azure Data Lake Store sem encriptação de dados](https://azure.microsoft.com/en-us/resources/templates/101-data-lake-store-no-encryption/).
- Com encriptação de dados através do Data Lake Store: [Implementar a conta do Data Lake Store com encriptação (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
- Com encriptação de dados através do Azure Key Vault: [Implementar a conta do Data Lake Store com encriptação (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).

### <a name="assign-permissions-to-azure-key-vault"></a>Atribuir permissões ao Azure Key Vault
Se utilizou chaves de um Cofre de Chaves do Azure para configurar a encriptação na conta do Data Lake Store, tem de configurar o acesso entre a conta do Data Lake Store e a conta do Cofre de Chaves do Azure. Para fazê-lo, execute os passos seguintes.

1. Se utilizou chaves do Cofre de Chaves do Azure, o painel da conta do Data Lake Store apresenta um aviso na parte superior. Clique no aviso para abrir o painel **Configurar permissões do Cofre de Chaves**.
   
    ![Encriptação do Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Encriptação do Data Lake Store")
2. O painel mostra duas opções para configurar o acesso.
   
   * Na primeira opção, clique em **Conceder Permissão** para configurar o acesso. A primeira opção só estará ativada se o utilizador que criou a conta do Data Lake Store também for administrador do Cofre de Chaves do Azure.
   * A outra opção consiste em executar o cmdlet do PowerShell apresentado no painel. Tem de ser o proprietário do Cofre de Chaves do Azure ou ter a capacidade de conceder permissões no Cofre de Chaves do Azure. Depois de executar o cmdlet, volte para o painel e clique em **Ativar** para configurar o acesso.

## <a name="createfolder"></a>Criar pastas na conta do Azure Data Lake Store
Pode criar pastas na sua conta do Data Lake Store para gerir e armazenar dados.

1. Abra a conta do Data Lake Store que criou. No painel esquerdo, clique em **Procurar**, clique em **Data Lake Store** e, em seguida, no painel Data Lake Store, clique no nome da conta sob o qual pretende criar as pastas. Se afixou a conta ao startboard, clique no mosaico dessa conta.
2. No painel da conta do Data Lake Store, clique em **Explorador de Dados**.
   
    ![Criar pastas numa conta do Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas numa conta do Data Lake Store")
3. No painel da conta do Data Lake Store, clique em **Nova Pasta**, introduza um nome para a nova pasta e, em seguida, clique em **OK**.
   
    ![Criar pastas numa conta do Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas numa conta do Data Lake Store")
   
    A pasta recém-criada é listada no painel **Data Explorer**. Pode criar pastas aninhadas até qualquer nível.
   
    ![Criar pastas numa conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Criar pastas numa conta do Data Lake")

## <a name="uploaddata"></a>Carregar dados para a conta do Azure Data Lake Store
Pode carregar os dados para a conta do Azure Data Lake Store diretamente no nível de raiz ou para uma pasta que criou na conta. Na captura de ecrã seguinte, siga os passos para carregar um ficheiro para uma sub-pasta a partir do painel **Data Explorer**. Nesta captura de ecrã, o ficheiro é carregado para uma sub-pasta mostrada na estrutura (marcado numa caixa vermelha).

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")

## <a name="properties"></a>Propriedades e ações disponíveis nos dados armazenados
Clique no ficheiro recém-adicionado para abrir o painel **Propriedades**. As propriedades associadas ao ficheiro e as ações que pode efetuar no ficheiro estão disponíveis neste painel. Também pode copiar o caminho completo para o ficheiro na sua conta do Azure Data Lake Store, realçada na caixa vermelha na seguinte captura de ecrã:

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados")

* Clique em **Pré-visualizar** para ver uma pré-visualização do ficheiro, diretamente a partir do browser. Também pode especificar o formato da pré-visualização. Clique em **Pré-visualizar**, clique em **Formato** no painel **Pré-visualização do Ficheiro** e, no painel **Formato de Pré-visualização do Ficheiro** especifique as opções, como o número de linhas a apresentar, a codificação a utilizar, o delimitador a utilizar, etc.
  
  ![Formato de pré-visualização do ficheiro](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato de pré-visualização do ficheiro")
* Clique em **Transferir** para transferir o ficheiro para o seu computador.
* Clique em **Mudar o nome do ficheiro** para mudar o nome do ficheiro.
* Clique em **Eliminar ficheiro** para eliminar o ficheiro.

## <a name="secure-your-data"></a>Proteger os dados
Pode proteger os dados armazenados na sua conta do Azure Data Lake Store utilizando o Azure Active Directory e o controlo de acesso (ACLs). Para obter instruções sobre como fazê-lo, veja [Proteger dados no Azure Data Lake Store](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Eliminar conta do Azure Data Lake Store
Para eliminar uma conta do Azure Data Lake Store, a partir do painel Data Lake Store, clique em **Eliminar**. Para confirmar a ação, ser-lhe-á pedido para introduzir o nome da conta que pretende eliminar. Introduza o nome da conta e, em seguida, clique em **Eliminar**.

![Eliminar conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminar conta do Data Lake")

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Access diagnostic logs for Data Lake Store (Registos de diagnóstico de acesso do Data Lake Store)](data-lake-store-diagnostic-logs.md)


