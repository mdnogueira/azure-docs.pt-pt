---
title: Tutorial de BI de energia para o conector Azure Cosmos DB | Microsoft Docs
description: "Utilize este tutorial do Power BI para importar JSON, criar relatórios insightful e visualizar dados utilizando o conector de BD do Cosmos do Azure e o Power BI."
keywords: Power bi tutorial, visualize os dados, o conector do power bi
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: mimig
ms.openlocfilehash: 03127c9d35b8dd0fe54310c84ff89ea087f175b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Tutorial de BI de energia de base de dados do Azure Cosmos: visualizar dados utilizando o conector do Power BI
[PowerBI.com](https://powerbi.microsoft.com/) é um serviço online, onde pode criar e partilhar os dashboards e relatórios com os dados que são importantes para si e a sua organização para.  Ambiente de trabalho do Power BI é um ferramenta que permite-lhe obter dados de várias origens de dados, intercalar e transformar os dados, criar relatórios poderosos e visualizações e publicar os relatórios no Power BI de criação de relatórios dedicado.  Com a versão mais recente do Power BI Desktop, pode agora ligar à sua conta de base de dados do Cosmos através do conector Cosmos DB para o Power BI.   

Neste tutorial do Power BI, vamos percorrer os passos para ligar a uma conta de base de dados do Cosmos no Power BI Desktop, navegue para uma coleção em que pretende extrair os dados utilizando o navegador, transformar dados JSON em formato tabular, utilizando o Editor de consultas de ambiente de trabalho do Power BI e criar e publicar um relatório no PowerBI.com.

Depois de concluir este tutorial do Power BI, poderá responder às seguintes questões:  

* Como pode incorporar relatórios com os dados da base de dados do Cosmos utilizando o ambiente de trabalho do Power BI?
* Como ligar a uma conta de base de dados do Cosmos no Power BI Desktop
* Como posso obter dados de uma coleção no Power BI Desktop?
* Como pode transformar os dados JSON aninhados no Power BI Desktop?
* Como publicar e partilhar os meus relatórios em PowerBI.com?

> [!NOTE]
> O conector do Power BI para a base de dados do Azure Cosmos estabelece ligação ao ambiente de trabalho do Power BI para extração e a transformação de dados. Relatórios criados no Power BI Desktop, em seguida, podem ser publicados para o site PowerBI.com. Extração direta e a transformação de dados de base de dados do Azure Cosmos não podem ser efetuadas no PowerBI.com. 

> [!NOTE]
> Para ligar a base de dados do Azure Cosmos ao Power BI através da API do MongoDB, tem de utilizar o [o controlador ODBC do Simba MongoDB](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial do Power BI, certifique-se de que tem acesso para os seguintes recursos:

* [A versão mais recente do Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Acesso a nossa conta de demonstração ou dados na sua conta de base de dados do Cosmos.
  * A conta de demonstração é preenchida com os dados de volcano mostrados neste tutorial. Esta conta de demonstração não está vinculada por qualquer SLA e destina-se apenas a fins de demonstração.  Estamos a reservar o direito de Efetue alterações para esta conta de demonstração, incluindo mas não se limitando a terminar a conta, alterar a chave, restringir o acesso, a alteração e eliminar os dados em qualquer altura, sem aviso prévio ou motivo.
    * URL: https://analytics.documents.azure.com
    * Chave apenas de leitura: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR + YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw = =
  * Ou, para criar a sua própria conta, consulte [criar uma conta de base de dados de base de dados do Azure Cosmos no portal do Azure](https://azure.microsoft.com/documentation/articles/create-account/). Em seguida, obter volcano exemplo dados que é semelhantes à que são utilizados neste tutorial (mas não contém os blocos de GeoJSON), consulte o [NOAA site](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) e, em seguida, importar os dados através de [ferramenta de migração de dados de base de dados do Azure Cosmos](import-data.md).

Para partilhar os seus relatórios em PowerBI.com, tem de ter uma conta no site PowerBI.com.  Para mais informações sobre o Power BI para gratuito e Power BI Pro, visite [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Vamos começar
Neste tutorial, vamos imagine que é um geologist studying princípios volcanoes em todo o mundo.  Os dados de volcano são armazenados numa conta de base de dados do Cosmos e os documentos JSON parecerem ser o documento de exemplo seguinte.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Pretende obter os dados de volcano da conta de base de dados do Cosmos e visualizar dados de um relatório do Power BI interativo como o relatório seguinte.

![Por concluir este tutorial do Power BI com o conector do Power BI, poderá visualizar dados com o relatório do Power BI Desktop volcano](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Está pronto para experimentá-la? Vamos começar.

1. Execute o ambiente de trabalho do Power BI na sua estação de trabalho.
2. Assim que o Power BI Desktop é iniciada, um *boas-vindas* é apresentado o ecrã.
   
    ![Ecrã de boas-vindas do BI ambiente de trabalho de energia - conector do Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Pode **obter dados**, consulte **origens recentes**, ou **abrir relatórios de outros** diretamente a partir de *boas-vindas* ecrã.  Clique no X no canto superior direito para fechar o ecrã. O **relatório** é apresentada a vista de ambiente de trabalho do Power BI.
   
    ![Power BI Desktop relatório vista - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Selecione o **home page** do Friso, em seguida, clique em **obter dados**.  O **obter dados** deve aparecer a janela.
5. Clique em **Azure**, selecione **(Beta) do Microsoft Azure DocumentDB**e, em seguida, clique em **Connect**. 

    ![Ambiente de trabalho do Power BI obter dados - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. No **pré-visualização do conector** página, clique em **continuar**. O **Microsoft Azure DocumentDB Connect** surge a janela.
7. Especifique o URL de ponto final de conta Cosmos DB teria de como obter os dados conforme mostrado abaixo e, em seguida, clique em **OK**. Para utilizar a sua própria conta, pode obter o URL na caixa URI no  **[chaves](manage-account.md#keys)**  painel do portal do Azure. Para utilizar a conta de demonstração, introduza `https://analytics.documents.azure.com` para o URL. 
   
    Deixe o nome de base de dados, o nome da coleção e a instrução SQL em branco como estes campos são opcionais.  Em vez disso, utilizamos o navegador para selecionar a base de dados e a coleção para identificar de onde vêm os dados.
   
    ![Tutorial do Power BI para o conector do Power BI do Azure Cosmos DB - janela de estabelecer a ligação de ambiente de trabalho](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Se estiver a ligar a este ponto final pela primeira vez, é-lhe pedida a chave de conta. Para a sua própria conta, obter a chave a partir de **chave primária** caixa o  **[chaves só de leitura](manage-account.md#keys)**  painel do portal do Azure. Para a conta de demonstração, a chave é `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Introduza a chave adequada e, em seguida, clique em **Connect**.
   
    Recomendamos que utilize a chave apenas de leitura durante a criação de relatórios.  Isto irá impedir que exposição desnecessária da chave mestra para potenciais riscos de segurança. A chave apenas de leitura está disponível a partir de [chaves](manage-account.md#keys) painel do portal do Azure ou pode utilizar as informações de conta de demonstração fornecidas acima.
   
    ![Tutorial de BI de energia para o conector do Power BI do Azure Cosmos DB - chave da conta](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Se obtiver um erro que indica "a base de dados especificada não encontrada." Veja a solução passos deste [problema do Power BI](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Quando a conta está ligada com êxito, o **navegador** será apresentada.  O **navegador** apresentará uma lista de bases de dados sob a conta.
10. Clique e expanda na base de dados onde os dados para o relatório serão provenientes, se estiver a utilizar a conta de demonstração, selecionar **volcanodb**.   
11. Agora, selecione uma coleção que irá obter os dados da. Se estiver a utilizar a conta de demonstração, selecione **volcano1**.
    
    O painel de pré-visualização mostra uma lista de **registo** itens.  Um documento é representado como uma **registo** tipo no Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento é também uma **registo**.
    
    ![Tutorial do Power BI para o conector do Power BI do Azure Cosmos DB - janela do navegador](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Clique em **editar** para iniciar o Editor de consultas numa nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Flattening e transformar documentos JSON
1. Mudar para a janela do Editor de consultas do Power BI, onde o **documento** coluna no painel central.
   ![Editor de consultas de ambiente de trabalho do Power BI](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Clique em expander no lado direito do **documento** cabeçalho da coluna.  Será apresentado o menu de contexto com uma lista de campos.  Selecione os campos para o relatório, é necessário para a instância, Volcano nome, país, região, localização, elevação, tipo, estado e última Eruption saber e, em seguida, clique em **OK**.
   
    ![Tutorial de BI para o conector Azure Cosmos DB Power BI de energia - expanda documentos](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. O painel de center irá apresentar uma versão de pré-visualização do resultado com os campos selecionados.
   
    ![Tutorial de BI para o conector Azure Cosmos DB Power BI de energia - aplanar resultados](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. No nosso exemplo, a propriedade de localização é um bloco de GeoJSON num documento.  Como pode ver, a localização é representada como uma **registo** tipo no Power BI Desktop.  
5. Clique em expander no lado direito de cabeçalho da coluna de localização.  O menu de contexto com tipo e as coordenadas campos serão apresentados.  Vamos, selecione o campo de coordenadas e clique em **OK**.
   
    ![Tutorial de BI de energia para o conector do Power BI do Azure Cosmos DB - registo de localização](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. O painel central mostra agora uma coluna de coordenadas do **lista** tipo.  Como é mostrado no início do tutorial, os dados de GeoJSON neste tutorial são do tipo de ponto com valores de Latitude e Longitude registados na matriz de coordenadas.
   
    O elemento de coordenadas [0] representa Longitude enquanto coordenadas [1] representa Latitude.
    ![Tutorial de BI de energia para o conector do Power BI do Azure Cosmos DB - lista de coordenadas](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Para aplanar a matriz de coordenadas, iremos criar uma **coluna personalizado** chamado LatLong.  Selecione o **Add Column** do Friso e clique em **Add Column personalizada**.  O **Add Column personalizada** deve aparecer a janela.
8. Forneça um nome para a nova coluna, por exemplo, LatLong.
9. Em seguida, especifique a fórmula personalizada para a nova coluna.  Para o nosso exemplo, iremos irá concatenar os valores de Latitude e Longitude separados por vírgulas, como mostrado abaixo utilizando a fórmula seguinte: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Clique em **OK**.
   
    Para obter mais informações em expressões de análise de dados (DAX), incluindo funções DAX, visite [DAX básico no Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Tutorial de BI de energia para o conector do Power BI do Azure Cosmos DB - Add Column personalizada](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)
10. Agora, o painel central irá mostrar a nova coluna LatLong preenchida com os valores de Latitude e Longitude separados por vírgulas.
    
    ![Tutorial de BI de energia para o conector Azure Cosmos DB Power BI - coluna LatLong personalizada](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se receber um erro na coluna nova, certifique-se de que os passos aplicados em definições de consulta correspondem a figura seguinte:
    
    ![Passos aplicados devem ser a origem de navegação, documento expandido, Document.Location expandido, adicionado personalizada](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se os passos são diferentes, elimine os passos adicionais e tente novamente a adicionar a coluna personalizada. 
11. Iremos agora foi concluído com flattening os dados em formato tabular.  Pode tirar partido de todas as funcionalidades disponíveis no Editor de consultas a forma e transforme dados na base de dados do Cosmos.  Se estiver a utilizar o exemplo, altere o tipo de dados para elevação a **número inteiro** alterando o **tipo de dados** no **home page** Friso.
    
    ![Tutorial do Power BI para o conector do Power BI do Azure Cosmos DB - alterar tipo de coluna](./media/powerbi-visualize/power_bi_connector_pbichangetype.png)
12. Clique em **fechar e aplicar** para guardar o modelo de dados.
    
    ![Tutorial do Power BI para o conector do Power BI do Azure Cosmos DB - feche & Aplicar](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Os relatórios de compilação
Vista do Power BI Desktop relatório é onde pode iniciar a criação de relatórios para visualizar dados.  Pode criar relatórios ao arrastar e largar os campos para o **relatório** tela.

![Power BI Desktop relatório vista - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Na vista de relatório, encontrará:

1. O **campos** painel, este é onde poderá ver uma lista de modelos de dados com campos que pode utilizar para os seus relatórios.
2. O **visualizações** painel. Um relatório pode conter um único ou vários visualizações.  Escolha os tipos de visual às suas necessidades de ajuste o **visualizações** painel.
3. O **relatório** tela, este é onde irá criar visuais para o relatório.
4. O **relatório** página. Pode adicionar várias páginas de relatório no Power BI Desktop.

O seguinte mostra os passos básicos de criação de um relatório de vista do mapa interativo simples.

1. Para o nosso exemplo, iremos criar uma vista de mapa que mostra a localização de cada volcano.  No **visualizações** painel, clique no tipo de visual de mapa conforme realçado na captura de ecrã acima.  Deverá ver o tipo de visual de mapa desenhado no **relatório** tela.  O **visualização** painel também deverá apresentar um conjunto de propriedades relacionadas com o tipo de visual do mapa.
2. Agora, arrastar e largar o campo de LatLong do **campos** painel para o **localização** propriedade no **visualizações** painel.
3. Em seguida, arrastar e largar o campo de nome de Volcano para o **legenda** propriedade.  
4. Em seguida, arrastar e largar o campo de elevação para o **tamanho** propriedade.  
5. Deverá ver o mapa que mostra um conjunto de bolhas que indica a localização de cada volcano com o tamanho de bolha correlacionando para a elevação do volcano visual.
6. Agora criou um relatório básico.  Pode personalizar ainda mais o relatório adicionando mais visualizações.  No nosso caso, vamos adicionar uma segmentação de dados do tipo Volcano para tornar o relatório interativo.  
   
    ![Captura de ecrã do relatório do Power BI Desktop final após a conclusão do tutorial do Power BI para a base de dados do Azure Cosmos](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Publicar e partilhar o relatório
Para partilhar o relatório, tem de ter uma conta no site PowerBI.com.

1. No Power BI Desktop, clique em de **home page** Friso.
2. Clique em **Publicar**.  Será solicitado para introduzir o nome de utilizador e palavra-passe para a sua conta de site PowerBI.com.
3. Depois de ter sido autenticada a credencial, o relatório é publicado para o destino selecionado.
4. Clique em **aberto 'PowerBITutorial.pbix' no Power BI** para ver e partilhar o relatório no PowerBI.com.
   
    ![Publicar o Power BI êxito! Tutorial abrir no Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Criar um dashboard no PowerBI.com
Agora que tem um relatório, permite partilhá-la no site PowerBI.com

Ao publicar o relatório do Power BI Desktop powerbi.com, será gerado um **relatório** e um **Dataset** no inquilino da sua PowerBI.com. Por exemplo, após publicou um relatório chamado **PowerBITutorial** powerbi.com, verá PowerBITutorial em ambos os **relatórios** e **conjuntos de dados** secções em PowerBI.com.

   ![Captura de ecrã do novo relatório e conjunto de dados no site PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Para criar um dashboard partilháveis, clique o **Pin em direto página** botão no relatório PowerBI.com.

   ![Captura de ecrã do novo relatório e conjunto de dados no site PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Em seguida, siga as instruções em [afixar um mosaico de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo dashboard. 

Também pode fazer alterações ad hoc ao relatório antes de criar um dashboard. No entanto, é recomendado que utilize o ambiente de trabalho do Power BI para efetuar as modificações e voltar a publicar o relatório em PowerBI.com.

## <a name="refresh-data-in-powerbicom"></a>Atualizar dados no site PowerBI.com
Existem duas formas de atualizar os dados, ad hoc e agendados.

Para uma atualização ad hoc, basta clicar em eclipses (…), pelo **Dataset**, por exemplo, PowerBITutorial. Deverá ver uma lista de ações, incluindo **atualizar agora**. Clique em **atualizar agora** para atualizar os dados.

![Captura de ecrã da atualização agora no site PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

Para uma atualização agendada, execute o seguinte:

1. Clique em **agendar atualização** na lista de ação. 

    ![Captura de ecrã da atualização agenda no PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. No **definições** página, expanda **as credenciais da origem de dados**. 
3. Clique em **editar credenciais**. 
   
    O pop-up de configurar aparece. 
4. Introduza a chave para ligar à conta de base de dados do Cosmos esse conjunto de dados, em seguida, clique em **sessão**. 
5. Expanda **agendar atualização** e configurar a agenda que pretende atualizar o conjunto de dados. 
6. Clique em **aplicar** e terminar de configurar a atualização agendada.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o Power BI, consulte [introdução ao Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para saber mais sobre a base de dados do Cosmos, consulte o [página de destino de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

