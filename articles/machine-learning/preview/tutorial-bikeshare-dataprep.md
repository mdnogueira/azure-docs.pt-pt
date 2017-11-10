---
title: "Tutorial de partilha de bicicleta - preparação de avançada de dados com o Azure Machine Learning Workbench"
description: "E tutoriais de preparação de dados ponto-a-ponto utilizando o Azure Machine Learning Workbench"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: acd61e9980b143ebbb81d2d144bdac9134e20a11
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Tutorial de partilha de bicicleta: avançadas preparação de dados com o Azure Machine Learning Workbench
Serviços de Machine Learning do Azure (pré-visualização) é um ciência de dados ponto-a-ponto integrada e a solução de análise avançadas para cientistas de dados professional preparar os dados, experimentações de desenvolver e implementar modelos na escala da nuvem.

Neste tutorial, utilize os serviços do Azure Machine Learning (pré-visualização) para saber como:
> [!div class="checklist"]
> * Preparar os dados de forma interativa com a ferramenta de preparação de dados do Azure Machine Learning
> * Importar, transformar e criar um conjunto de dados de teste
> * Gerar um pacote de preparação de dados
> * Execute o pacote de preparação de dados com o Python
> * Gerar um conjunto de dados de formação por reutilizar o pacote de preparação de dados para os ficheiros de entrada adicionais

> [!IMPORTANT]
> Este tutorial prepara apenas os dados, não a criar o modelo de previsão.
>
> Pode utilizar os dados preparados para preparar os seus próprios modelos de previsão. Por exemplo, poderá criar um modelo para prever a pedido de bicicleta durante uma janela de 2 horas.

## <a name="prerequisites"></a>Pré-requisitos
* Workbench de aprendizagem máquina do Azure tem de ser instalada localmente. Para obter mais informações, siga o [instalação início rápido](quickstart-installation.md).
* Familiaridade com a criação de um novo projeto do Workbench.

## <a name="data-acquisition"></a>Aquisição de dados
Este tutorial utiliza o [Boston Hubway dataset](https://s3.amazonaws.com/hubway-data/index.html) Boston Meteorologia dados e de [NOAA](http://www.noaa.gov/).

1. Transferir os ficheiros de dados das seguintes ligações para o ambiente de desenvolvimento local. 
   * [Dados de Meteorologia Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Dados de viagem Hubway a partir do site Hubway.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Deszipe a cada ficheiro. zip após a transferência.

## <a name="learn-about-the-datasets"></a>Saiba mais sobre os conjuntos de dados
1. O __Meteorologia Boston__ ficheiro contém os campos seguintes relacionados com Meteorologia comunicados numa base por hora:
   * DATA
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. O __Hubway__ dados são organizados em ficheiros por ano e mês. Por exemplo, o ficheiro denominado `201501-hubway-tripdata.zip` contém um ficheiro. csv que contém os dados de Janeiro de 2015. Os dados contêm os seguintes campos, cada linha que representa uma viagem de bicicleta:

   * Duração de viagem (em segundos)
   * Data e hora de início
   * Data e hora de paragem
   * Nome da estação início & ID
   * Nome da estação final & ID
   * ID de bicicleta
   * Tipo de utilizador (casuais = utilizador de passagem de 24 horas ou 72 horas; Membro = membro mensal ou anual)
   * Código postal (se o utilizador é membro)
   * Sexo (automática comunicado pelo membro)

## <a name="create-a-new-project"></a>Criar um novo projeto
1. Inicie o **Azure Machine Learning Workbench** do menu Iniciar ou iniciador.

2. Crie um novo projeto do Azure Machine Learning.  Clique em de  **+**  botão no **projetos** página, ou **ficheiro** > **novo**.
   - Utilize o **projeto em branco** modelo.
   - Nomeie o projeto **BikeShare**. 

## <a id="newdatasource"></a>Criar uma nova origem de dados

1. Crie uma nova origem de dados. Clique em de **dados** botão (ícone cilindro) na barra de ferramentas esquerdo. Esta ação apresenta os **vista de dados**.

   ![Imagem de separador da vista de dados](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Adicione uma origem de dados. Selecione o  **+**  ícone e, em seguida, selecione **Adicionar origem de dados**.

   ![Imagem da entrada de adicionar origem de dados](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Adicionar dados de Meteorologia

1. **Arquivo de dados**: selecione o arquivo de dados que contém os dados. Uma vez que estão a utilizar ficheiros, selecione **ficheiros / diretório**. Selecione **seguinte** para continuar.

   ![Imagem dos ficheiros / entrada de diretório](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Seleção de ficheiros**: adicionar os dados de Meteorologia. Procurar e selecionar o `BostonWeather.csv` ficheiro que transferiu anteriormente. Clique em **Seguinte**.

   ![Imagem da seleção de ficheiros com BostonWeater.csv selecionado](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **Detalhes de ficheiros**: Verifique se o esquema de ficheiro que é detetado. Workbench de aprendizagem máquina do Azure analisa os dados no ficheiro e infere o esquema a utilizar.

   ![Imagem dos detalhes de ficheiro](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > O Workbench pode não detetar o esquema correto em alguns casos. Deve sempre Certifique-se de que os parâmetros estão corretos para o conjunto de dados. Para os dados de Meteorologia, certifique-se de que estão definidas para os seguintes valores:
   >
   > * __Tipo de ficheiro__: ficheiro delimitado por (csv, tsv, txt, etc.)
   > * __Separador__: vírgulas [,]
   > * __Comente a linha caráter__: nenhum valor está definido.
   > * __Ignorar as linhas modo__: não salte
   > * __Codificação de ficheiro__: utf-8
   > * __Promover o modo de cabeçalhos__: utilizar cabeçalhos a partir do primeiro ficheiro

   A pré-visualização dos dados deverá apresentar as seguintes colunas:
   * **Caminho**
   * **DATA**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   Para continuar, selecione **seguinte**.

4. **Tipos de dados**: Reveja os tipos de dados que são detetados automaticamente. Workbench de aprendizagem máquina do Azure analisa os dados no ficheiro e infere os tipos de dados a utilizar.

   Para estes dados, alterar o `DATA TYPE` de todas as colunas para `String`.

   > [!NOTE]
   > `String`é utilizado para realçar as capacidades do Workbench mais tarde no tutorial. 

   ![Imagem dos tipos de dados](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   Para continuar, selecione __seguinte__. 

5. **Amostragem**: para criar um esquema de amostragem, selecione o **+ novo** botão. Selecione o novo __superior 10000__ linha está adicionada e, em seguida, selecione __editar__. Definir __estratégia de exemplo__ para **ficheiro completo**e, em seguida, selecione **aplicar**.

   ![Imagem da adição de uma nova estratégia de amostragem](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   Para utilizar o __ficheiro completo__ estratégia, selecione o __ficheiro completo__ entrada e, em seguida, selecione __definida como Active Directory__. Uma estrela é apresentado junto a __ficheiro completo__ para indicar que a estratégia de Active Directory.

   ![Imagem de ficheiro completo, como a estratégia de Active Directory](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Para continuar, selecione **seguinte**.

6. **Coluna caminho**: O __coluna caminho__ secção permite-lhe incluir o caminho de ficheiro completo como uma coluna de dados importados. Selecione __não incluir a coluna de caminho__.

   > [!TIP]
   > Incluindo o caminho como uma coluna é útil se estiver a importar uma pasta de muitos ficheiros com nomes de ficheiro diferente. Também é útil se os nomes de ficheiro contêm informações que pretende extrair mais tarde.

   ![Imagem da coluna caminho definido por não incluir](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Concluir**: para concluir a criação da origem de dados, selecione o **concluir** botão.

    Um novo separador de origem de dados com o nome __BostonWeather__ abre. É apresentado um exemplo dos dados numa vista de grelha. O exemplo baseia-se o esquema do Active Directory amostragem especificado anteriormente.

    Tenha em atenção o **passos** painel à direita do ecrã mostra as ações individuais executadas durante a criação desta origem de dados.

   ![Imagem que apresenta a origem de dados, de exemplo e os passos](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Veja as métricas origem de dados

Selecione o __métricas__ botão na parte superior esquerdo da vista de grelha no separador. Esta vista mostra a distribuição e outras estatísticas agregadas de amostras de dados.

![Imagem das métricas apresenta](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Para configurar a visibilidade das estatísticas, utilize o **escolha métrica** pendente. Verifique e desmarque as métricas existe para alterar a vista de grelha.

Para devolver o __vista de dados__, selecione __dados__ do canto superior esquerdo da página.

## <a name="add-data-source-to-data-preparation-package"></a>Adicionar origem de dados para o pacote de preparação de dados

1. Selecione __preparar__ para iniciar a preparação dos dados. 

2. Quando lhe for pedido, introduza um nome para o pacote de preparação de dados, tais como `BikeShare Data Prep`. 

3. Selecione __OK__ para continuar.

   ![Imagem da caixa de diálogo preparar](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Um novo pacote com o nome **BikeShare dados Prep** aparece por baixo __dados preparação__ secção o __dados__ separador. 

   Para apresentar o pacote, selecione esta entrada. 

5. Selecione o  **>>**  botão para expandir para apresentar o __Dataflows__ contidos no pacote. Neste exemplo, __BostonWeather__ é apenas o fluxo de dados.

   > [!IMPORTANT]
   > Um pacote pode conter vários Dataflows.

   ![Imagem de dataflows contidos no pacote](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Dados de filtro por valor
1. Para filtrar dados, faça duplo clique numa célula com um determinado valor e selecione __filtro__e, em seguida, o tipo de filtro.

2. Para este tutorial, selecione uma célula que contém o valor `FM-15` e, em seguida, defina o filtro para um filtro de **é igual a**.  Agora, os dados são filtrados para apenas devolve linhas em que o __REPORTTYPE__ é `FM-15`.

   ![Imagem da caixa de diálogo de filtro](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM 15 é um tipo de Meteorological Terminal Aviation rotina Meteorologia relatório (METAR). Os relatórios de FM 15 empirically são respeitados seja o mais completa, com dados em falta listtle.

## <a name="remove-a-column"></a>Remover uma coluna

Já não necessita de __REPORTTYPE__ coluna. Faça duplo clique no cabeçalho da coluna e selecione **Remover coluna**.

   ![Imagem da opção de coluna remover](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Alterar o tipos de dados e remover erros
1. Premir __Ctrl (Command ⌘ no Mac)__ enquanto a seleção de cabeçalhos de coluna poder selecionar várias colunas de uma só vez. Utilize esta opção para selecionar os seguintes cabeçalhos de coluna:
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Clique com botão direito** um dos cabeçalhos de coluna selecionada e selecione **converter o tipo de campo para numérica**. Isto converte o tipo de dados para as colunas numéricas.

   ![Converter várias colunas para um valor numérico](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtre os valores de erro. Algumas colunas tiverem problemas de conversão de tipo de dados. Este problema é indicado pela cor vermelha no __barra de qualidade de dados__ para a coluna.

   Para remover as linhas que tiverem erros, clique com botão direito no **HOURLYDRYBULBTEMPF** no cabeçalho de coluna. Selecione **filtrar a coluna**. Utilizar a predefinição **posso pretende para** como **manter linhas**. Alterar o **condições** pendente para selecionar **não é erro**. Selecione **OK** para aplicar o filtro.

4. Para eliminar as linhas de erro restantes nas outras colunas, repita este processo de filtro para **HOURLYRelativeHumidity** e **HOURLYWindSpeed** colunas.

## <a name="use-by-example-transformations"></a>Utilizar transformações de exemplo

Para utilizar os dados numa predição de blocos de tempo de duas horas, tem de computação as condições de Meteorologia média por períodos de duas horas. Para tal, pode utilizar os seguintes ações:

* Dividir a **data** coluna em separado **data** e **tempo** colunas. Consulte a secção seguinte para obter os passos detalhados.

* Derivar uma **Hour_Range** coluna o **tempo** coluna. Consulte a secção seguinte para obter os passos detalhados.

* Derivar uma **data\_hora\_intervalo** coluna o **data** e **Hour_Range** colunas. Consulte a secção seguinte para obter os passos detalhados.

### <a name="split-column-by-example"></a>Dividir coluna por exemplo

1. Dividir a **data** coluna Data separada e colunas de tempo. Clique com o botão direito no **data** cabeçalho da coluna e selecione **dividir coluna por exemplo**.

   ![Imagem da coluna divisão pela entrada de exemplo](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Workbench de aprendizagem máquina automaticamente identifica um delimitador significativo e cria duas colunas ao dividir os dados para os valores de data e hora. 

3. Selecione __OK__ para aceitar a operação resulta de divisão.

   ![Imagem das colunas da divisão DATE_1 e DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Derivar coluna por exemplo

1. Derivar de um intervalo de duas horas, clique com botão direito do __data\_2__ cabeçalho da coluna e selecione **derivar coluna, por exemplo**.

   ![Imagem da coluna derivada pela entrada de exemplo](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   É adicionada uma nova coluna vazia com valores nulos.

2. Clique na célula vazia primeiro na coluna novo. Fornecer um exemplo de intervalo de tempo pretendido, escrevendo `12AM-2AM` na nova coluna e, em seguida, prima introduza.

   ![Imagem da nova coluna com um valor de 12: 00 - 2 AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML Workbench synthesizes um programa com base nos exemplos fornecidos por si e aplica o mesmo programa em linhas restantes. Todas as outras linhas serão preenchidas automaticamente com base no exemplo que forneceu. Além disso, o Workbench analisa os dados e tenta identificar casos de limite. 

   > [!IMPORTANT]
   > Identificação de cenários de limite pode não funcionar no Mac na versão atual do Workbench. Ignorar o __passo 3__ e __passo 4__ abaixo no Mac. Em vez disso, prima __OK__ depois de todas as linhas obterem preenchidas com os valores derivados.
   
3. O texto **analisar dados** acima grelha indica que Workbench está a tentar detetar casos de limite. Quando terminar, o estado muda para **linha sugerida seguinte de revisão** ou **sem sugestões**. Neste exemplo, **linha sugerida seguinte de revisão** é devolvido.

4. Para rever as alterações sugeridas, selecione **linha sugerida seguinte de revisão**. A célula que deve rever e corrigir (se necessário) é realçada no ecrã do.

   ![Imagem da linha de revisão](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Selecione __OK__ para aceitar a transformação.
 
5. É encaminhado para a vista de grelha de dados para __BostonWeather__. Grelha contém agora três colunas adicionadas anteriormente.

   ![Imagem da vista de grelha com linhas adicionadas](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Todas as alterações efetuadas são mantidas no **passos** painel. Aceda ao passo que criou no **passos** painel, clique na seta para baixo e selecione **editar**. A janela avançada para **derivar coluna, por exemplo** é apresentado. Todos os seus exemplos são preservados aqui. Também pode adicionar exemplos manualmente, fazendo duplo clique de uma linha na grelha abaixo. Selecione **Cancelar** para regressar à grelha principal sem aplicar as alterações. Também pode aceder a esta vista, selecionando **modo avançado** ao efetuar uma **derivar coluna, por exemplo** transformação.

6. Para mudar o nome da coluna, faça duplo clique no cabeçalho da coluna e do tipo **intervalo de hora**. Prima **Enter** para guardar a alteração.

   ![Mudar o nome de coluna](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Derivar intervalo data e hora, selecionar vários o **data\_1** e **intervalo de hora** colunas, rato e, em seguida, selecione **coluna derivada, por exemplo**.

   ![Efetuar a derivação de coluna de exemplo](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Tipo `Jan 01, 2015 12AM-2AM` do exemplo contra a primeira linha e prima **Enter**.

   O Workbench determina a transformação com base no exemplo que fornecer. Neste exemplo, o resultado é que a data é o formato alterado concatenado com o período de duas horas.

   ![Imagem do exemplo ' 01 de Janeiro de 2015 12: 00 - 2 AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > No Mac, siga o passo seguinte, em vez de __passo 8__ abaixo.
   >
   > * Aceda a primeira célula que contém `Feb 01, 2015 12AM-2AM`. Deve ser o __linha 15__. Corrija o valor para `Jan 02, 2015 12AM-2AM`e prima __Enter__. 
   

8. Aguarde que o estado de alterar do **analisar dados** para **linha sugerida seguinte de revisão**. Esta operação pode demorar vários segundos. Selecione a ligação de estado para navegar para a linha sugerida. 

   ![Imagem da linha sugerida para rever](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   A linha tem um valor nulo porque o valor de data de origem pode ser de dd/mm/aaaa ou mm/dd/aaaa. Escreva o valor correto de `Jan 13, 2015 2AM-4AM` e prima **Enter**. O workbench utiliza dois exemplos para melhorar a derivação para as restantes linhas.

   ![Imagem de dados formatados corretamente](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Selecione **OK** para aceitar a transformação.

   ![Imagem da grelha de transformação concluída](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Pode utilizar o modo avançado de **coluna derivada, por exemplo** para este passo clicando na seta para baixo no **passos** painel. Grelha de dados, existem caixas de verificação junto aos nomes das colunas **data\_1** e **intervalo de hora** colunas. Desmarque a caixa de verificação junto a **intervalo de hora** coluna para ver como isto altera o resultado. Na ausência de **intervalo de hora** coluna como entrada, **12: 00 - 2 AM** é tratado como uma constante e é acrescentado para os valores derivados. Selecione **Cancelar** para regressar à grelha principal sem aplicar as alterações.

10. Para mudar o nome da coluna, faça duplo clique o cabeçalho. Altere o nome **data hora intervalo** e, em seguida, prima **Enter**.

11. Selecione o **data**, **data\_1**, **data\_2**, e **intervalo de hora** colunas. Clique com botão direito e, em seguida, selecione **Remover coluna**.

## <a name="summarize-data-mean"></a>Resumir os dados (média)

O passo seguinte é para resumir as condições de Meteorologia, efetuando a média dos valores, agrupados pelo intervalo de hora.

1. Selecione o **data hora intervalo** coluna e, em seguida, selecione **Summarize** do **transforma** menu.

    ![Transforma menu](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Para resumir os dados, arraste colunas da grelha na parte inferior da página para os esquerda e direito painéis na parte superior. O painel esquerdo contém o texto **arrastar colunas para aqui para agrupar dados**. Painel direito contém o texto **arrastar colunas para aqui para resumir os dados**. 

    Arraste o **data hora intervalo** coluna da grelha na parte inferior do painel esquerdo. Arraste **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity**, e **HOURLYWindSpeed** ao painel da direita. 

    No painel direito, selecione **significa** como o **agregado** medidas para cada coluna. Clique em **OK** para concluir o resumo.

   ![Imagem do ecrã de dados resumidos](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Transformação através de script de fluxo de dados

Alterar os dados nas colunas numéricas para um intervalo de 0-1 permite alguns modelos convergir rapidamente. Atualmente não há nenhuma transformação genericamente fazer esta transformação incorporada, mas um script de Python pode ser utilizado para efetuar esta operação.

1. Do **transformar** menu, selecione **transformar o fluxo de dados**.

2. Introduza o seguinte código na caixa de texto que aparece. Se tiver utilizado os nomes das colunas, o código deverão funcionar sem modificação. Estiver a escrever uma lógica simples de min-máx. de normalização no Python.

    > [!WARNING]
    > O script espera que os nomes de coluna utilizados anteriormente neste tutorial. Se tiver diferentes nomes de coluna, tem de alterar os nomes do script.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > O script de Python tem de devolver `df` no final. Este valor é utilizado para preencher a grelha.
    
    ![Caixa de diálogo de script de fluxo de dados de transformação](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Selecione __OK__ para utilizar o script. As colunas numérico na grelha agora contêm valores no intervalo 0-1.

    ![Grelha que contém os valores entre 0 e 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Tiver concluído a preparar os dados de Meteorologia. Em seguida, prepare os dados de viagem.

## <a name="load-trip-data"></a>Carregar dados viagem

1. Para importar o `201701-hubway-tripdata.csv` de ficheiros, utilize os passos a [criar uma nova origem de dados](#newdatasource) secção. Utilize as seguintes opções durante o processo de importação:

    * __Esquema de amostragem__: **ficheiro completo** amostragem esquema, tornar o exemplo ativa, e 
    * __Tipo de dados__: aceite as predefinições.

2. Depois de importar os dados, selecione o __preparar__ botão para iniciar a preparação dos dados. Selecione o existente **BikeShare dados Prep.dprep** do pacote e, em seguida, selecione __OK__.

    Este processo adiciona um **fluxo de dados** para existente **dados preparação** ficheiro em vez de criar um novo.

    ![Imagem da seleção de pacote existente](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Assim que a grelha foi carregadas, expanda __DATAFLOWS__. Existem agora dois dataflows: **BostonWeather** e **201701-hubway-tripdata**. Selecione o **201701-hubway-tripdata** entrada.

    ![Imagem da entrada de 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Utilizar inspector do mapa

Para a preparação de dados, são uma série de visualizações útil chamado **Inspetores** para dados de cadeia, numéricos e Geographical que ajudam a compreender melhor os dados e identificar valores atípicos. Utilize os seguintes passos para utilizar o inspector mapa:

1. Selecione o **iniciar latitude estação** e **iniciar longitude de estação** colunas. Faça duplo clique de uma das colunas e, em seguida, selecione **mapa**.

    > [!TIP]
    > Para ativar a seleção múltipla, mantenha premida __Ctrl (Command ⌘ no Mac)__ e selecione o cabeçalho para cada coluna.

    ![Imagem de visualização do mapa](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Para maximizar a visualização de mapa, selecione o **Maximizar** ícone. Para ajustar o mapa para a janela, selecione o **i** ícone no lado superior esquerdo da visualização.

    ![Maximizado imagem](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Clique em de **Minimizar** botão para regressar à vista de grelha.

## <a name="use-column-statistics-inspector"></a>Utilizar Inspector de estatísticas de coluna

Para utilizar o inspector de estatísticas de coluna, clique com botão direito no __tripduration__ coluna e selecione __estatísticas de coluna__.

![Entrada de estatísticas de coluna](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Este processo adiciona uma nova visualização intitulada __tripduration estatísticas__ no __INSPETORES__ painel.

![Imagem de inspector de estatísticas tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> O valor máximo da duração viagem é **minutos 961,814**, que está prestes a dois anos. Parece que existem alguns valores atípicos no conjunto de dados.

## <a name="use-histogram-inspector"></a>Utilizar inspector histograma

Para tentar identificar os valores atípicos, clique com botão direito do __tripduration__ coluna e selecione __histograma__.

![Inspector histograma](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

A histograma não é útil, como os valores atípicos são skewing gráfico.

## <a name="add-column-using-script"></a>Adicionar a coluna utilizando o script

1. Clique com o botão direito no **tripduration** coluna e selecione **Add Column (Script)**.

    ![Imagem do menu Adicionar a coluna (script)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. No __Add Column (Script)__ caixa de diálogo, utilize os seguintes valores:

    * __Novo nome de coluna__: logtripduration
    * __Inserir esta nova coluna depois__: tripduration
    * __Novo código de coluna__:`math.log(row.tripduration)`
    * __Tipo de bloco de código__: expressão

   ![A caixa de diálogo Adicionar coluna (Script)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Selecione __OK__ para adicionar o **logtripduration** coluna.

4. Faça duplo clique na coluna e selecione **histograma**.

    ![Histograma da coluna logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  Visualmente, parece que este histograma, como uma distribuição Normal com uma cauda anormal.

## <a name="use-advanced-filter"></a>Utilizar filtragem avançadas

Utilizando um filtro nos dados atualiza os inspetores com a distribuição de novo. Clique com o botão direito no **logtripduration** coluna e selecione **filtro coluna**. No __editar__ caixa de diálogo, utilize os seguintes valores:

* __Filtrar esta coluna de número__: logtripduration
* __Quero__: manter as linhas
* __Quando__: qualquer uma das condições abaixo são True (ou)
* __Se esta coluna__: inferior a
* __O valor__: 9

![Opções de filtragem](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Selecione __OK__ para aplicar o filtro.

![Histogramas atualizadas depois de filtro ser aplicado](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>O efeito de halo

1. Maximizar a **logtripduration** histograma. Não há um histograma azul overlaid num histograma cinzento. Este ecrã é denominado o **Halo efeito**:

    * O **histograma cinzenta** representa a distribuição antes da operação (neste caso, a operação de filtragem).
    * O **histograma azul** representa a histograma após a operação. 

   O efeito de halo ajuda-o com visualizar o efeito de uma operação nos dados.

   ![Imagem do efeito halo](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Tenha em atenção que a azul histograma aparece mais curta comparado com anterior. Isto acontece porque automática novamente bucketing dos dados no novo intervalo.

2. Para remover o halo, selecione __editar__ e desmarque __Mostrar halo__.

    ![Opções para a histograma](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Selecione **OK** para desativar o efeito de halo e, em seguida, minimizar a histograma.

### <a name="remove-columns"></a>Remover colunas

Dados viagem, cada linha representa um evento de recolha de bicicleta. Para este tutorial, só precisa do **starttime** e **iniciar id da estação** colunas. Remova outras colunas por selecionar vários estas duas colunas, faça duplo clique no cabeçalho da coluna e, em seguida, selecione **manter coluna**. Outras colunas são removidas.

![Imagem da opção de coluna keep](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Resumir os dados (número)

Para resumir bicicleta a pedido para um período de 2 horas, utilize colunas derivadas.

1. Clique com o botão direito no **starttime** coluna e selecione **derivar coluna, por exemplo**

    ![Imagem de derivar pela opção de exemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Por exemplo, introduza um valor de `Jan 01, 2017 12AM-2AM` para a primeira linha.

    > [!IMPORTANT]
    > No exemplo anterior de colunas derivadas, utilizou vários passos para derivar uma coluna que continha o período de data e hora. Neste exemplo, pode ver que esta operação pode ser executada como um único passo ao fornecer um exemplo do resultado final.

    > [!NOTE]
    > Pode dar um exemplo em relação a qualquer uma das linhas. Neste exemplo, o valor de `Jan 01, 2017 12AM-2AM` é válido para a primeira linha dos dados.

    ![Imagem dos dados de exemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > No Mac, siga o passo seguinte, em vez de __passo 3__ abaixo.
   >
   > * Aceda a primeira célula que contém `Jan 01, 2017 1AM-2AM`. Deve ser o __linha 14__. Corrija o valor para `Jan 01, 2017 12AM-2AM`e prima __Enter__. 

3. Aguarde até que a aplicação calcula os valores de todas as linhas. Poderá demorar vários segundos. Depois de concluída a análise, utilize o __linha sugerida seguinte de revisão__ ligação para verificar os dados.

   ![Imagem da análise foi concluída com ligação de revisão](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Certifique-se de que os valores calculados estão corretos. Caso contrário, atualize o valor com o valor esperado e prima enter. Em seguida, aguarde que a análise concluir. Concluir o **linha sugerida seguinte de revisão** processar até ver **sem sugestões**. Quando vir **sem sugestões**, a aplicação analisou os casos de limite e estiver satisfeita com o programa sintetizado. É uma melhor prática para efetuar uma inspeção visual dos dados transformados antes de aceitar a transformação. 

4. Selecione **OK** para aceitar a transformação. Mudar o nome da coluna recentemente criada para **data hora intervalo**.

    ![Imagem da coluna cujo nome foi alterada](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Clique com o botão direito no **starttime** cabeçalho da coluna e selecione **Remover coluna**.

6. Para resumir os dados, selecione __Summarize__ do __transformar__ menu. Para criar a transformação, execute as seguintes ações:

    * Arraste __data hora intervalo__ e __iniciar id da estação__ para o painel da esquerda (agrupar).
    * Arraste __iniciar id da estação__ à direita (resumir os dados) painel.

   ![Uma imagem das opções de resumo](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Selecione **OK** para aceitar o resultado de resumo.

## <a name="join-dataflows"></a>Associar dataflows

Para associar os dados de Meteorologia com os dados de viagem, utilize os seguintes passos:

1. Selecione __associar__ do __transforma__ menu.

2. __As tabelas__: selecione **BostonWeather** como o fluxo de dados à esquerda e **201701-hubway-tripdata** como o fluxo de dados à direita. Para continuar, selecione **seguinte**.

    ![Imagem das seleções de tabelas](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Colunas de chave__: selecione a **data hora intervalo** coluna em ambas as tabelas e, em seguida, selecione __seguinte__.

    ![Imagem da associação a um em colunas de chaves](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Tipo de associação__: selecione __que correspondam a linhas__ como o tipo de associação e, em seguida, selecione __concluir__.

    ![Tipo de associação de linhas correspondentes](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Este processo cria um novo fluxo de dados com o nome __associar resultado__.

## <a name="create-additional-features"></a>Criar funcionalidades adicionais

1. Para criar uma coluna que contém o dia da semana, clique com botão direito no **data hora intervalo** coluna e selecione **coluna derivada, por exemplo**. Utilize um valor de __Sun__ de uma data que ocorreu um Domingo. Por exemplo, **01 de Janeiro de 2017 12: 00 - 2 AM**. Prima **Enter** e, em seguida, selecione **OK**. Mudar o nome esta coluna para __dia da semana__.

    ![Imagem da criação de uma nova coluna que contém o dia da semana](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Para criar uma coluna que contém o período de tempo para uma linha, clique com botão direito no **data hora intervalo** coluna e selecione **coluna derivada, por exemplo**. Utilize um valor de **12: 00 - 2 AM** para a linha que contém **01 de Janeiro de 2017 12: 00 - 2 AM**. Prima **Enter** e, em seguida, selecione **OK**. Mudar o nome esta coluna para **período**.

    ![Imagem da coluna de período](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Para remover o **data hora intervalo** e **rDate intervalo de hora** colunas, prima **Ctrl (Command ⌘ no Mac)** e selecione cada cabeçalho da coluna. Clique com botão direito e, em seguida, selecione **Remover coluna**.

## <a name="read-data-from-python"></a>Ler dados a partir do Python

Pode executar um pacote de preparação de dados do Python ou PySpark e obter o resultado como uma **dados moldura**.

Para gerar um exemplo de script de Python, clique com botão direito no __BikeShare dados Prep__ e selecione __gerar ficheiro de código de acesso de dados__. O ficheiro de Python do exemplo é criado na sua **pasta do projeto**e também é carregado num separador do Workbench. O seguinte script de Python é um exemplo de código que é gerado:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

Para este tutorial, é o nome do ficheiro `BikeShare Data Prep.py`. Este ficheiro é utilizado mais tarde no tutorial.

## <a name="save-test-data-as-a-csv-file"></a>Guardar dados de teste como um ficheiro CSV

Para guardar o **associar resultado** fluxo de dados para um. Um ficheiro CSV, tem de alterar o `BikeShare Data Prep.py` script. Atualize o script de Python utilizando o seguinte código:

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

Selecione **executar** da parte superior do ecrã. O script é submetido como um **tarefa** no computador local. Depois de muda o estado da tarefa para __concluído__, o ficheiro foi escrito para a localização especificada.

## <a name="substitute-data-sources"></a>Substitua as origens de dados

Nos passos anteriores, utilizou o `201701-hubway-tripdata.csv` e `BostonWeather.csv` origens de dados para preparar os dados de teste. Para utilizar o pacote com os outros ficheiros de dados de viagem, utilize os seguintes passos:

1. Crie um novo **origem de dados** utilizando os passos fornecidos anteriormente, com as seguintes alterações ao processo de:

    * __Seleção de ficheiros__: ao selecionar um ficheiro, selecionar vários seis restantes tripdata viagem. Ficheiros CSV.

        ![Carregar os ficheiros restantes seis](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > O __+ 5__ entrada indica que existem cinco ficheiros adicionais para além de que está listado.

    * __Detalhes de ficheiros__: definir __promover o modo de cabeçalhos__ para **todos os ficheiros têm os mesmos cabeçalhos**. Este valor indica que contém todos os ficheiros de cabeçalho do mesmo.

        ![Seleção de detalhes do ficheiro](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Guarde o nome desta origem de dados, porque é utilizado em passos posteriores.

2. Selecione o ícone de pasta para ver os ficheiros no seu projeto. Expanda o __aml\_configuração__ diretório e, em seguida, selecione o `local.runconfig` ficheiro.

    ![Imagem da localização das local.runconfig](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. Adicione as seguintes linhas no final de `local.runconfig` de ficheiros e, em seguida, selecione o ícone de disco para guardar o ficheiro.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Esta alteração substitui a origem de dados original, pelo que contém os ficheiros de dados de seis viagem.

## <a name="save-training-data-as-a-csv-file"></a>Guardar dados de formação como um ficheiro CSV

Navegue para o ficheiro de Python `BikeShare Data Prep.py` que editado anteriormente e forneça um caminho de ficheiro diferentes para guardar os dados de preparação.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

Para submeter uma tarefa de novo, utilize o **executar** na parte superior da página. A **tarefa** for submetida com a nova configuração. O resultado da tarefa é os dados de formação. Estes dados são criados utilizando os mesmos passos de preparação de dados que criou anteriormente. Pode demorar alguns minutos para concluir a tarefa.

## <a name="next-steps"></a>Passos seguintes
Concluiu o tutorial de preparação de dados de partilha de bicicleta. Neste tutorial, utilizou serviços do Azure Machine Learning (pré-visualização) para saber como:
> [!div class="checklist"]
> * Preparar os dados de forma interativa com a ferramenta de preparação de dados do Azure Machine Learning
> * Importar, transformar e criar um conjunto de dados de teste
> * Gerar um pacote de preparação de dados
> * Execute o pacote de preparação de dados com o Python
> * Gerar um conjunto de dados de formação por reutilizar o pacote de preparação de dados para os ficheiros de entrada adicionais

Em seguida, saiba mais sobre a preparação de dados:
> [!div class="nextstepaction"]
> [Guia de utilizador de preparação de dados](data-prep-user-guide.md)
