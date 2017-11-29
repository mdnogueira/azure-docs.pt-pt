---
title: "Registar dados do Data Lake Store no catálogo de dados do Azure | Microsoft Docs"
description: "Registar dados do Data Lake Store no catálogo de dados do Azure"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 93de6a574b306e3fd8959454709e84a57ee4fc10
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registar dados do Data Lake Store no catálogo de dados do Azure
Neste artigo, ficará a saber como integrar o Azure Data Lake Store com o catálogo de dados do Azure para tornar os dados detetável dentro de uma organização, como o integrar com o catálogo de dados. Para obter mais informações sobre cataloging dados, consulte [catálogo de dados do Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Para compreender os cenários em que pode utilizar o catálogo de dados, consulte [cenários comuns do catálogo de dados do Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ativar a sua subscrição do Azure** para pré-visualização pública do Data Lake Store. Veja as [instruções](data-lake-store-get-started-portal.md).
* **Conta do Azure Data Lake Store**. Siga as instruções em [Introdução ao Azure Data Lake Store com o Portal do Azure](data-lake-store-get-started-portal.md). Para este tutorial, informe-na criar uma conta de Data Lake Store denominada **datacatalogstore**.

    Uma vez que criou a conta, carregar um conjunto de dados de exemplo para a mesma. Para este tutorial, informe-na carregar todos os ficheiros. csv sob o **AmbulanceData** pasta o [repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Pode utilizar vários clientes, tais como [Explorador de armazenamento do Azure](http://storageexplorer.com/), carregar dados para um contentor do blob.
* **Catálogo de dados do Azure**. A organização já tem de ter um catálogo de dados do Azure criado para a sua organização. Apenas um catálogo é permitido para cada organização.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Register Data Lake Store, como uma origem para o catálogo de dados

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Aceda a `https://azure.microsoft.com/services/data-catalog`e clique em **começar**.
2. Inicie sessão no portal do catálogo de dados do Azure e clique em **publicar dados**.

    ![Registar uma origem de dados](./media/data-lake-store-with-data-catalog/register-data-source.png "registar uma origem de dados")
3. Na página seguinte, clique em **iniciar aplicação**. Isto irá transferir o ficheiro de manifesto da aplicação no seu computador. Faça duplo clique o ficheiro de manifesto para iniciar a aplicação.
4. Na página de boas-vindas, clique em **sessão**e introduza as suas credenciais.

    ![Ecrã de boas-vindas](./media/data-lake-store-with-data-catalog/welcome.screen.png "ecrã de boas-vindas")
5. Em selecionar uma página de origem de dados, selecione **do Azure Data Lake**e, em seguida, clique em **seguinte**.

    ![Origem de dados selecione](./media/data-lake-store-with-data-catalog/select-source.png "origem de dados selecione")
6. Na página seguinte, forneça o nome de conta do Data Lake Store que pretende registar no catálogo de dados. Deixe as outras opções como a predefinição e, em seguida, clique em **Connect**.

    ![Ligar à origem de dados](./media/data-lake-store-with-data-catalog/connect-to-source.png "ligar à origem de dados")
7. A página seguinte pode ser dividida em segmentos seguintes.

    a. O **hierarquia de servidores** caixa representa a estrutura de pastas de conta do Data Lake Store. **$Root** representa a raiz da conta de Data Lake Store, e **AmbulanceData** representa a pasta que criou na raiz da conta do Data Lake Store.

    b. O **objetos disponíveis** caixa apresenta uma lista de ficheiros e pastas sob a **AmbulanceData** pasta.

    c. **Objetos que devem ser registada caixa** apresenta uma lista de ficheiros e pastas que pretende registar no catálogo de dados do Azure.

    ![Ver a estrutura de dados](./media/data-lake-store-with-data-catalog/view-data-structure.png "ver a estrutura de dados")
8. Para este tutorial, deve registar todos os ficheiros no diretório. Para tal, clique em de (![mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "mover objetos")) botão para mover todos os ficheiros para **objetos a registar** caixa.

    Uma vez que os dados serão registados no catálogo de dados de toda a organização, é uma abordagem de recomendados para adicionar alguns metadados que pode utilizar mais tarde para localizar rapidamente os dados. Por exemplo, pode adicionar um endereço de correio eletrónico para o proprietário dos dados (por exemplo, um que está a carregar os dados) ou adicionar uma etiqueta para identificar os dados. Captura de ecrã abaixo mostra uma etiqueta, que iremos adicionar aos dados.

    ![Ver a estrutura de dados](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "ver a estrutura de dados")

    Clique em **registar**.
9. Captura de ecrã seguinte indica que os dados com êxito são registados no catálogo de dados.

    ![Registo completo](./media/data-lake-store-with-data-catalog/registration-complete.png "ver a estrutura de dados")
10. Clique em **ver Portal** para regressar ao portal do catálogo de dados e certifique-se de que agora pode aceder os dados registados do portal. Para procurar os dados, pode utilizar a tag utilizado ao registar os dados.

     ![Dados de pesquisa no catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "dados de pesquisa no catálogo")
11. Agora pode efetuar operações como adicionar anotações e a documentação para os dados. Para obter mais informações, consulte as hiperligações seguintes.

    * [Anotar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
    * [Origens de dados de documento no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Consultar também
* [Anotar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Origens de dados de documento no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar o Data Lake Store com outros serviços do Azure](data-lake-store-integrate-with-other-services.md)
