---
title: Utilizar os conjuntos de dados de exemplo no Machine Learning Studio | Microsoft Docs
description: "Descrições dos conjuntos de dados utilizadas em modelos de exemplo incluídos no Machine Learning Studio. Pode utilizar estes conjuntos de dados de exemplo para das suas experimentações."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 03a0b844-e8a7-4896-996f-d3c7a0db7a50
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: 55cec0c711836360c173a67d575f042d0fe86bef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Utilizar os conjuntos de dados de exemplo no Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

Quando cria uma nova área de trabalho no Azure Machine Learning, um número de conjuntos de dados de exemplo e experimentações está incluído por predefinição. Muitos destes conjuntos de dados de exemplo são utilizados pelos modelos de exemplo no [Azure galeria do Cortana Intelligence](http://gallery.cortanaintelligence.com/). Outros recursos são incluídos como exemplos de vários tipos de dados normalmente utilizados no machine learning.

Alguns destes conjuntos de dados estão disponíveis no Blob storage do Azure. Para estes conjuntos de dados, a tabela seguinte fornece uma ligação direta. Pode utilizar estes conjuntos de dados das suas experimentações utilizando o [importar dados] [ import-data] módulo.

O resto destes conjuntos de dados de exemplo estão disponíveis na sua área de trabalho em **conjuntos de dados guardado** na paleta do módulo para a esquerda da experimentação da tela quando abrir ou criar uma nova experimentação no Machine Learning Studio.
Pode utilizar qualquer um destes conjuntos de dados na sua própria experiência, arrastando-lo para a tela de experimentação.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th align=left>Nome do conjunto de dados</th>
  <th align=left>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td valign=top>Conjunto de dados de classificação binária de receitas Census para adultos</td>
  <td valign=top>
Um subconjunto de 1994, feitas Census base de dados, utilizando os adultos de trabalho mais de 16 com um índice de receitas ajustada de > 100.<p> </p><b>Utilização:</b> classificar pessoas utilizando demografia ao prever se uma pessoa earns mais de 50 K um ano.<p> </p><b>Relacionados Research:</b> Kohavi, R., Becker, o B., (1996). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>O conjunto de dados do AIRport códigos</td>
  <td valign=top>
Códigos de airport E.U.A..<p> </p>Este conjunto de dados contém uma linha para cada airport E.U.A., fornecendo o número de ID de airport e o nome, juntamente com a cidade de localização e o estado.
  </td>
</tr>

<tr>
  <td valign=top>Dados do preço automóvel (bruto)</td>
  <td valign=top>
Informações sobre automóveis através da marca e modelo, incluindo o preço, funcionalidades, tais como o número de cilindros e MPG, bem como um modelo de pontuação de risco insurance.<p> </p>A classificação de risco inicialmente associada preços automática e, em seguida, ajustada para risco real num processo conhecido actuaries como symboling. Um valor de + 3 indica que o automática é duvidosos e um valor de -3 que é provavelmente seguro.<p> </p><b>Utilização:</b> prever a classificação de risco as funcionalidades, utilizando a classificação regressão ou multivariate. <p> </p><b>Relacionados Research:</b> Schlimmer, J.C. (1987). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>O conjunto de dados do bicicleta Rental UCI</td>
  <td valign=top>
UCI bicicleta Rental conjunto de dados baseada em dados reais anónimos da empresa de Capital Bikeshare que mantém uma rede de rental bicicleta no Washington D.C.<p> </p>O conjunto de dados tem uma linha para cada hora de cada dia no 2011 e 2012, para um total de 17,379 linhas. O intervalo de hora a hora alugueres de bicicleta é entre 1 e 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Uma vez fatura RGB imagem</td>
  <td valign=top>
Converter o ficheiro de imagem publicamente disponíveis para dados CSV.<p> </p>O código para converter a imagem é fornecido no <strong>cor a utilizar o clustering de meios de K de quantização</strong> página de detalhes do modelo.
  </td>
</tr>

<tr>
  <td valign=top>Dados de donation Blood</td>
  <td valign=top>
Um subconjunto dos dados da base de dados de donor blood do Centro de Hsin Chu de serviço a Blood Transfusion cidade, Taiwan.<p> </p>Dados donor incluem os meses desde o último donation) e frequência ou o número total de donations, tempo decorrido desde a última donation e a quantidade de blood doada.<p> </p><b>Utilização:</b> o objetivo é prever através de classificação, se o donor doada blood no de 2007 de Março, em que 1 indica um donor durante o período de destino e 0 um não-donor. <p> </p><b>Relacionados Research:</b> Yeh I.C., (2008). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador <p> </p>Yeh, posso-Cheng Yang, King-Jang e Ting, Tao-Ming "deteção de dados de conhecimento no modelo de MFR utilizando sequência Bernoulli," especialista sistemas com aplicações, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Revisões de livro da Amazon</td>
  <td valign=top>
Revisões Books no Amazon, obtidas a partir do Web site amazon.com pelos investigadores University de Pennsylvania (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">sentimento</a>). Consulte o documento de investigação, "Biographies, Bollywood, caixas de Boom e Blenders: Adaptation de domínio para classificação de dados de sentimento" João Blitzer, marca Dredze e Fernando Pereira; Associação de Linguistics computacional (ACL) de 2007.<p> </p>O conjunto de dados original foi 975K revisões com as classificações 1, 2, 3, 4 ou 5. As revisões foram escritos em inglês e são do período de tempo 1997 2007. Este conjunto de dados foi amostragem inferior a 10 mil revisões.
  </td>
</tr>

<tr>
  <td valign=top>Mama breast</td>
  <td valign=top>
Um dos três relacionadas com cancer conjuntos de dados fornecidos pelo Institute Oncology que aparece frequentemente no literature do machine learning. Combina informações de diagnóstico com funcionalidades de análise de laboratório de cerca de 300 amostras de tissue.<p> </p><b>Utilização:</b> classificar o tipo de cancer, com base nos 9 atributos, algumas das quais são lineares e alguns são categórico. <p> </p><b>Relacionados Research:</b> O.L. Wohlberg, W.H., rua, W.N. & Mangasarian, (1995). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Funcionalidades de Cancer breast <td valign=top>
O conjunto de dados contém informações sobre regiões suspeita de mil 102 (candidatos) de imagens X-ray, cada descrita através de 117 funcionalidades. As funcionalidades são proprietárias e os respetivos significado não seja revelado pelos criadores de conjunto de dados (Siemens Healthcare). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informações de Cancer breast</td>
  <td valign=top>
O conjunto de dados contém informações adicionais para cada região suspeita da imagem de X-ray. Cada exemplo fornece informações (por exemplo, etiqueta, patient ID, coordenadas das patch relativamente à imagem toda) sobre o número de linha correspondente no conjunto de dados Breast Cancer funcionalidades. Cada patient tem um número de exemplos. Para patients que tenham um cancer, alguns exemplos são positivos e alguns são negativos. Para patients que não tenham um cancer, todos os exemplos são negativos. O conjunto de dados tem 102K exemplos. O conjunto de dados é a totalmente direcionado, 0,6% dos pontos são positivo, o resto são negativo. O conjunto de dados foi disponibilizado pela Siemens Healthcare.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>CRM Appetency etiquetas partilhadas</td>
  <td valign=top>
As etiquetas do desafio de predição de relação de cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Etiquetas de volume de alterações CRM partilhadas</td>
  <td valign=top>
As etiquetas do desafio de predição de relação de cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Conjunto de dados do CRM partilhado</td>
  <td valign=top>
Estes dados provém do desafio de predição de relação de cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>O conjunto de dados contém K de 50 clientes da empresa francês Telecom laranja. Cada cliente tem 230 funcionalidades anónimos, 190 dos quais são numéricos e 40 são categórico. As funcionalidades são muito dispersas.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>CRM Upselling etiquetas partilhadas</td>
  <td valign=top>
As etiquetas do desafio de predição de relação de cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Dados de regressão eficiência de energia</td>
  <td valign=top>
Uma coleção de perfis de energia simulada, com base em 12 edifício diferentes formas. Os edifícios são diferenciados por 8 funcionalidades, como glazing área, distribuição de área glazing e orientação.<p> </p><b>Utilização:</b> utilizam regressão ou classificação para prever a eficiência energética classificação baseada em como uma das duas respostas valores reais. Para a classe multi classificação, é arredondar a variável de resposta para o número inteiro mais próximo. <p> </p><b>Relacionados Research:</b> Xifara, recomeço & Tsanas, A. (2012). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Trânsito atrasa dados</td>
  <td valign=top>
Trânsito Passenger no tempo de dados de desempenho obtidos a partir da recolha de dados TranStats dos E.U.A. Departamento de transportes (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">no tempo</a>).<p> </p>O conjunto de dados abrange o período de tempo Outubro de Abril de 2013. Antes de carregar para o Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte forma:<ul><li>O conjunto de dados foi filtrado para abranger apenas os 70 mais agitados aeroportos no continental E.U.A.</li><li>Flights canceladas foram denominados como um atraso por mais de 15 minutos</li><li>Foram filtrados diverted flights</li><li>Foram selecionadas as seguintes colunas: ano, mês, DayofMonth, DayOfWeek, operadora, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, cancelado</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Desempenho de tempo de voo (bruto)</td>
  <td valign=top>
Registos de arrivals de voo avião e departures no interior dos Estados Unidos de Outubro de 2011.<p> </p><b>Utilização:</b> prever atrasos dos voos. <p> </p><b>Relacionados Research:</b> de E.U.A. dodep. de transportes <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Incêndios florestais dados</td>
  <td valign=top>
Contém dados de Meteorologia, tais como índices de temperatura e humidade e a velocidade do vento, de uma área de Nordeste de Portugal combinado com registos de desencadeado de floresta.<p> </p><b>Utilização:</b> esta é uma tarefa difícil de regressão, onde o objetivo é para prever a área de burned do desencadeado de floresta. <p> </p><b>Relacionados Research:</b> Cortez, P. & Morais, A. (2008). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador <p> </p>[Cortez e Morais, 2007] Cortez P. e recomeço Morais. Uma abordagem de extração de dados para prever floresta é desencadeado Meteorological dados a utilizar. No J. Neves, M. f. Santos e Eds. J. Machado, tendências de novo no Artificial Intelligence, Proceedings do 2007 de EPIA 13 - conferência Portuguese no Artificial Intelligence, Dezembro, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN 13 978-989-95618-0-9. Disponível em: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Alemão cartão de crédito UCI conjunto de dados</td>
  <td valign=top>
O conjunto de dados UCI Statlog (alemão cartão de crédito) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + alemão + crédito + dados</a>), utilizando o ficheiro german.data.<p> </p>O conjunto de dados classifica pessoas, descritas por um conjunto de atributos, como os riscos de crédito baixa ou alta. Cada exemplo representa uma pessoa. Existem 20 funcionalidades, numéricos e categórico e uma etiqueta de binária (o valor de risco de crédito). Entradas de risco de crédito elevada tem etiqueta = 2, as entradas de risco de crédito baixa tem etiqueta = 1. O custo de misclassifying um exemplo de baixo risco tão elevado é 1, enquanto que o custo de misclassifying um exemplo de alto risco como baixa é 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de filmes IMDB</td>
  <td valign=top>
O conjunto de dados contém informações sobre filmes que foram classificados no Twitter tweets: IMDB filmes ID, o nome de filmes, o género e o ano de produção. Existem 17K filmes no conjunto de dados. O conjunto de dados foi introduzido no documento "s. Dooms, T. Alemanha Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes recolhidos do Twitter. Workshop em Crowdsourcing e a computação humana para sistemas de Recomendador, CrowdRec no RecSys 2013".
  </td>
</tr>

<tr>
  <td valign=top>Dados da classe de Iris dois</td>
  <td valign=top>
Talvez trata-se a base de dados melhor conhecido que deverá existir no literature de reconhecimento padrão. O conjunto de dados é relativamente pequeno, com 50 exemplos de valores de petal de três varieties de iris.<p> </p><b>Utilização:</b> prever o tipo de iris os valores.  <p> </p><b>Relacionados Research:</b> Fisher, R.A. (1988). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets filme</td>
  <td valign=top>
O conjunto de dados é uma versão expandida do conjunto de dados de filmes Tweetings. O conjunto de dados tem classificações de 170K de filmes, a extrair de tweets bem estruturados no Twitter. Cada instância representa um tweet e pode ser uma cadeia de identificação: ID de utilizador, ID de filmes IMDB, classificação, timestamp, numer de Favoritos para esta tweet e o número de retweets deste tweet. O conjunto de dados foi disponibilizado pelo recomeço consiga aceder tal, Dooms S., B. Loni e D. Tikk para sistemas de Recomendador desafio 2014.
  </td>
</tr>

<tr>
  <td valign=top>Dados MPG para vários automóveis</td>
  <td valign=top>
Este conjunto de dados é uma versão ligeiramente modificada do conjunto de dados fornecida pela biblioteca StatLib de Carnegie Mellon University. O conjunto de dados foi utilizado o 1983 American análises associação Exposition.<p> </p>Os dados apresenta uma lista de consumo fuel para vários automóveis em quilómetros por litro, juntamente com informações tal o número de cilindros, displacement motor, potência de cavalos, ponderação total e aceleração.<p> </p><b>Utilização:</b> prever economia fuel com base em 3 atributos discretos com múltiplos valores e 5 atributos contínuos. <p> </p><b>Relacionados Research:</b> StatLib Carnegie Mellon University, (1993). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador </td>
</tr>

<tr>
  <td valign=top>Conjunto de dados Pima Indians Diabetes binário classificação</td>
  <td valign=top>
Um subconjunto de dados do National Institute de Diabetes e Digestive e Kidney Diseases da base de dados. O conjunto de dados foi filtrado concentrar-se em patients female de Pima Indian heritage. Os dados incluem dados médicas, tais como glucose e níveis de insulin, bem como os fatores lifestyle.<p> </p><b>Utilização:</b> prever se o requerente tem diabetes (classificação binária). <p> </p><b>Relacionados Research:</b> Sigillito, V. (1990). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml "</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador </td>
</tr>

<tr>
  <td valign=top>Dados de cliente restaurante</td>
  <td valign=top>
Um conjunto de metadados sobre clientes, incluindo dados demográficos e preferências.<p> </p><b>Utilização:</b> utilizar este conjunto de dados, em combinação com os outros dois restaurante conjuntos de dados, formação e testar um sistema de recomendador. <p> </p><b>Relacionados Research:</b> Bache, k e Lichman, M. (2013). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador.
  </td>
</tr>

<tr>
  <td valign=top>Dados da funcionalidade restaurante</td>
  <td valign=top>
Um conjunto de metadados sobre restaurants e as respetivas funcionalidades, tais como prato tipo, estilo jantar e localização.<p> </p><b>Utilização:</b> utilizar este conjunto de dados, em combinação com os outros dois restaurante conjuntos de dados, formação e testar um sistema de recomendador. <p> </p><b>Relacionados Research:</b> Bache, k e Lichman, M. (2013). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador.
  </td>
</tr>

<tr>
  <td valign=top>Classificações de restaurante</td>
  <td valign=top>
Contém as classificações que forneceu aos utilizadores restaurants numa escala de 0 a 2.<p> </p><b>Utilização:</b> utilizar este conjunto de dados, em combinação com os outros dois restaurante conjuntos de dados, formação e testar um sistema de recomendador. <p> </p><b>Relacionados Research:</b> Bache, k e Lichman, M. (2013). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador.
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de dados de classe multi Steel Annealing</td>
  <td valign=top>
Este conjunto de dados contém uma série de registos da steel annealing avaliações com os atributos físicos (largura espessura, tipos de tipo (coil folha, etc.) de resultante steel.<p> </p><b>Utilização:</b> prever qualquer um dos dois atributos numéricos de classe; hardness ou força. Também poderá analisar correlações entre atributos.<p> </p>Steel grades siga um padrão de conjunto definidos por SAE e outras organizações. Procura específico 'nível' (a variável de classe) e pretende compreender os valores necessários. <p> </p><b>Relacionados Research:</b> Sterling, D. & Buntine, W. (NA). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia, profissional de informações e ciência de computador <p> </p>Um guia útil steel grades pode ser encontrado aqui: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Dados telescope</td>
  <td valign=top>
Registos de energia de alta gama partícula bursts juntamente com o ruído de fundo, ambos simulated através de um processo simulações Monte.<p> </p>A intenção de simulação foi para melhorar a precisão dos baseado em zero atmospheric Cherenkov gama telescopes, através de métodos de análises para diferenciar entre o sinal pretendida (Cherenkov radiation showers) e o ruído de fundo (hadronic showers iniciado pelo cosmic rays no atmosphere superior).<p> </p>Os dados tem sido previamente processados para criar um cluster elongated com a longa eixo está orientado para o Centro de câmara. As características deste reticências (frequentemente denominadas Hillas parâmetros) são entre os parâmetros de imagem que podem ser utilizados para discrimination.<p> </p><b>Utilização:</b> prever se a imagem de um shower representa ruído de sinal ou segundo plano.<p> </p><b>Notas:</b> precisão classificação simples não é significativo para estes dados, uma vez que classificar um evento de em segundo plano como sinal é um de classificar um evento de sinal como em segundo plano. Para comparação dos diferentes classificadores gráfico ROC deve ser utilizado. A probabilidade de aceitar um evento de em segundo plano como sinal deve ser inferior um dos seguintes limiares: 0.01, 0,02, 0,05, 0,1 ou 0,2.<p> </p>Além disso, tenha em atenção que o número de eventos em segundo plano (h, para hadronic showers) é underestimated, enquanto no medidas reais, a classe h ou ruído representa a maioria dos eventos. <p> </p><b>Relacionados Research:</b> Bock, R.K. (1995). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, AC: University da Califórnia do, profissional de informações </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Conjunto de dados de Meteorologia</td>
  <td valign=top>
Hora a hora com base no telefone Meteorologia as observações de NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">intercalar dados de 201304 para 201310</a>).<p> </p>Os dados de Meteorologia abrange as observações efetuadas a partir de estações de Meteorologia airport, que abrangem o período de tempo Outubro de Abril de 2013. Antes de carregar para o Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte forma:<ul><li>Os IDs de estação de Meteorologia foram mapeados para os IDs de airport correspondente</li><li>Foram filtradas estações Meteorologia não associadas os 70 aeroportos mais agitados</li><li>A coluna de data foi dividida em colunas de ano, mês e dia separadas</li><li>Foram selecionadas as seguintes colunas: AirportID, ano, mês, dia, hora, fuso horário, SkyCondition, visibilidade, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipedia SP 500 conjunto de dados</td>
  <td valign=top>
Dados são derivados de Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) com base nos artigos da cada empresa S & P 500, armazenados como dados XML.<p> </p>Antes de carregar para o Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte forma:<ul><li>Extrair conteúdo de texto para cada empresa específica</li><li>Remova a formatação da wiki</li><li>Remover os carateres não alfanuméricos</li><li>Converter a todo o texto em minúsculas</li><li>Foram adicionadas as categorias de empresa conhecidos</li></ul><p> </p>Tenha em atenção que, para algumas companies um artigo não foi possível encontrar, pelo que o número de registos é inferior a 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
O conjunto de dados contém indicações sobre a respetiva resposta para uma campanha de correio postal direta e dados de cliente. Cada linha representa um cliente. O conjunto de dados contém 9 funcionalidades sobre demografia de utilizador e passado comportamento e 3 colunas de etiqueta (visitar conversão e gaste).  Visite é uma coluna binária que indica que um cliente visitada após a campanha de marketing, conversão indica um cliente comprou algo e gaste é a quantidade que foi despendida.  O conjunto de dados foi disponibilizado pela Kevin Hillstrom para MineThatData correio electrónico análise de dados de extração de desafio e.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Funcionalidades de exemplos de teste no conjunto de dados de notícias Reuters RCV1 V2. O conjunto de dados tem 781K novos artigos, juntamente com os respetivos IDs (primeira coluna do conjunto de dados). Cada artigo é atomizado stopworded e stemmed. O conjunto de dados foi disponibilizado pela David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Funcionalidades de exemplos de formação no conjunto de dados de notícias Reuters RCV1 V2. O conjunto de dados tem 23K novos artigos, juntamente com os respetivos IDs (primeira coluna do conjunto de dados). Cada artigo é atomizado stopworded e stemmed. O conjunto de dados foi disponibilizado pela David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Conjunto de dados da deteção de dados de conhecimento KDD Cup 1999 e extração de dados das ferramentas de concorrência (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).<p> </p>O conjunto de dados foi transferido e armazenado no Blob storage do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e inclui tanto formação e testar conjuntos de dados. O conjunto de dados de formação tem aproximadamente mil 126 linhas e 43 colunas, incluindo as etiquetas. Três colunas fazem parte das informações de etiqueta e 40 colunas, consiste em funcionalidades numéricos e de cadeia categórico, estão disponíveis para o modelo de preparação. Os dados de teste tem, aproximadamente, 22.5K exemplos com as mesma 43 colunas como os dados de preparação de teste.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
Atribuições de tópico de novos artigos no conjunto de dados de notícias Reuters RCV1 V2. Um artigo de notícias de última hora pode ser atribuído a vários tópicos. O formato de cada linha é "&lt;o nome do tópico&gt; &lt;id do documento&gt; 1". O conjunto de dados contém atribuições de tópico 2.6M. O conjunto de dados foi disponibilizado pela David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Estes dados provém do desafio de avaliação de desempenho KDD Cup 2010 estudante (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">avaliação de desempenho do estudante</a>). Os dados utilizados são o conjunto de preparação de Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, recomeço, Ritter, s.., Gordon, G.J. & Koedinger, K.R. (2010). Algebra posso 2008-2009. Conjunto de dados do desafio de KDD Cup 2010 Educational Data Mining desafio. Encontrá-lo em <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> ou <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>O conjunto de dados foi transferido e armazenado no Blob storage do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) e contém ficheiros de registo de um estudante tutoring sistema. As funcionalidades fornecidas incluem o ID do problema e respetiva descrição breve, ID de estudante, timestamp e quantas tentativas estudante efetuada antes de resolver o problema da forma correta. O conjunto de dados original foi 8.9M registos; Este conjunto de dados foi amostragem pendentes para as primeiras linhas de 100K. O conjunto de dados tem 23 colunas separador por vírgulas de vários tipos: numéricos, categórico e timestamp.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
