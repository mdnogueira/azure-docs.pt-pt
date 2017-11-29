---
title: Analisar os dados no Data Lake Store utilizando o Power BI | Microsoft Docs
description: Utilizar o Power BI para analisar os dados armazenados no Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 0c77ccd29e3e22005c3339ed4e89dccfed725fa0
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analisar os dados no Data Lake Store utilizando o Power BI
Neste artigo, ficará a saber como utilizar o ambiente de trabalho do Power BI para analisar e visualizar dados armazenados no Azure Data Lake Store.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Conta do Azure Data Lake Store**. Siga as instruções em [Introdução ao Azure Data Lake Store com o Portal do Azure](data-lake-store-get-started-portal.md). Este artigo pressupõe que já criou uma conta de Data Lake Store, denominada **mybidatalakestore**e carregado um ficheiro de dados de exemplo (**Drivers.txt**) ao mesmo. Este ficheiro de exemplo está disponível para transferência a partir do [repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Pode transferir esta do [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
1. Inicie o ambiente de trabalho do Power BI no seu computador.
2. Do **home page** do Friso, clique em **obter dados**e, em seguida, clique em mais informações. No **obter dados** caixa de diálogo, clique em **Azure**, clique em **Azure Data Lake Store**e, em seguida, clique em **Connect**.
   
    ![Ligar ao Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "ligar ao Data Lake Store")
3. Se vir uma caixa de diálogo sobre o conector está a ser uma fase de desenvolvimento, optar por continuar.
4. No **Microsoft Azure Data Lake Store** caixa de diálogo, forneça o URL para a sua conta do Data Lake Store e, em seguida, clique em **OK**.
   
    ![URL para o Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL para o Data Lake Store")
5. Na caixa de diálogo seguinte, clique em **sessão** para iniciar sessão na conta do Data Lake Store. Será redirecionado para a página de início de sessão da sua organização. Siga as instruções para iniciar sessão na conta.
   
    ![Inicie sessão no arquivo Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "inicie sessão no arquivo Data Lake")
6. Após ter iniciado com êxito, clique em **Connect**.
   
    ![Ligar ao Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "ligar ao Data Lake Store")
7. A caixa de diálogo seguinte mostra o ficheiro que carregou para a sua conta do Data Lake Store. Verifique as informações e, em seguida, clique em **carga**.
   
    ![Carregar dados do Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "carregar dados do Data Lake Store")
8. Depois dos dados terem sido carregados com êxito para o Power BI, verá os campos seguintes no **campos** separador.
   
    ![Importar campos](./media/data-lake-store-power-bi/imported-fields.png "importados campos")
   
    No entanto, para visualizar e analisar os dados, vamos preferir a dados disponível pelos campos seguintes
   
    ![Pretender campos](./media/data-lake-store-power-bi/desired-fields.png "pretendido campos")
   
    Nos passos seguintes, iremos atualizar a consulta para converter os dados importados no formato pretendido.
9. Do **home page** do Friso, clique em **editar consultas**.
   
    ![Editar consultas](./media/data-lake-store-power-bi/edit-queries.png "editar consultas")
10. No Editor de consultas, sob o **conteúdo** coluna, clique em **binário**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query1.png "editar consultas")
11. Verá um ícone de ficheiro, que representa o **Drivers.txt** ficheiro que carregou. O ficheiro com o botão direito e clique em **CSV**.    
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query2.png "editar consultas")
12. Deverá ver um resultado como mostrado abaixo. Os dados estão agora disponíveis num formato que pode utilizar para criar visualizações.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query3.png "editar consultas")
13. Do **home page** do Friso, clique em **fechar e aplicar**e, em seguida, clique em **fechar e aplicar**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/load-edited-query.png "editar consultas")
14. Assim que a consulta for atualizada, o **campos** separador mostrará novos campos disponíveis para visualização.
    
    ![Actualizar campos](./media/data-lake-store-power-bi/updated-query-fields.png "atualizado campos")
15. Informe-na criar um gráfico circular para representar os controladores em cada cidade para um determinado país. Para tal, efetue as seleções seguintes.
    
    1. No separador de visualizações, clique o símbolo de um gráfico circular.
       
        ![Criar o gráfico circular](./media/data-lake-store-power-bi/create-pie-chart.png "criar o gráfico circular")
    2. As colunas que iremos utilizar são **coluna 4** (nome da cidade) e **coluna 7** (nome do país). Arraste estas colunas da **campos** separador para **visualizações** separador conforme mostrado abaixo.
       
        ![Criar visualizações](./media/data-lake-store-power-bi/create-visualizations.png "criar visualizações")
    3. O gráfico circular deve agora assemelhar-se, como o mostrado abaixo.
       
        ![Gráfico circular](./media/data-lake-store-power-bi/pie-chart.png "criar visualizações")
16. Ao selecionar um país específico dos filtros de nível de página, agora, pode ver o número de controladores em cada cidade do país/região selecionada. Por exemplo, sob o **visualizações** separador em **filtros de nível de página**, selecione **Brasil**.
    
    ![Selecione um país](./media/data-lake-store-power-bi/select-country.png "selecione um país")
17. O gráfico circular é atualizado automaticamente para apresentar os controladores no cidades de Brasil.
    
    ![Controladores de um país](./media/data-lake-store-power-bi/driver-per-country.png "controladores por país")
18. Do **ficheiro** menu, clique em **guardar** para guardar a visualização como um ficheiro do Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar o relatório no serviço Power BI
Assim que tiver criado visualizações no Power BI Desktop, poder partilhar com outras pessoas ao publicar no serviço Power BI. Para obter instruções sobre como fazê-lo, consulte [publicar do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Consultar também
* [Analisar dados no Data Lake Store com o Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

