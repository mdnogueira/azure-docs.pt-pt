---
title: "Ciência de dados com a dados de ciência de Máquina Virtual com Linux no Azure | Microsoft Docs"
description: "Como efetuar várias tarefas de ciência de dados comuns com a VM de ciência de dados do Linux."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev;paulsh
ms.openlocfilehash: 650b11d66f3ca32266b9842af77c909e125b4e4d
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Ciência de dados com um dados ciência de Máquina Virtual com Linux no Azure
Estas instruções mostram como efetuar várias tarefas de ciência de dados comuns com a VM de ciência de dados do Linux. A Máquina Virtual de ciência de dados de Linux (DSVM) é uma imagem de máquina virtual disponível no Azure que seja pré-instaladas com uma coleção de ferramentas normalmente utilizado para análise de dados e de aprendizagem. Os componentes de chaves de software estão descritos a [aprovisionar a Máquina Virtual de ciência de dados de Linux](linux-dsvm-intro.md) tópico. A imagem VM torna mais fácil começar a utilizar ao executar a ciência de dados em minutos, sem ter de instalar e configurar cada uma das ferramentas individualmente. Pode facilmente aumentar verticalmente a VM, se for necessário e pare-quando não está em utilização. Por isso este recurso é elástico e económico.

As tarefas de ciência de dados demonstradas nestas instruções siga os passos descritos no [o processo de ciência de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Este processo fornece uma abordagem systematic a ciência de dados que lhe permite equipas de cientistas de dados de forma eficaz colaborar ao longo do ciclo de vida de criação de aplicações inteligentes. O processo de ciência de dados também fornece uma arquitetura iterativa para ciência de dados que pode ser seguida por uma pessoa.

Vamos analisar o [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) conjunto de dados nestas instruções. Este é um conjunto de mensagens de correio eletrónico que estão marcados como spam ou ham (que significa que não são spam), e também contém algumas estatísticas sobre o conteúdo dos e-mails. As estatísticas incluídas são abordadas na próxima mas uma secção.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar uma Máquina Virtual de ciência de dados de Linux, tem de ter o seguinte:

* Um **subscrição do Azure**. Se já tiver uma, consulte [hoje a criar a conta do Azure gratuita](https://azure.microsoft.com/free/).
* A [ **ciência de dados de Linux VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Para obter informações sobre o aprovisionamento de VM, consulte [aprovisionar a Máquina Virtual de ciência de dados de Linux](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) instalado no seu computador e abrir uma sessão XFCE. Para obter informações sobre como instalar e configurar um **X2Go cliente**, consulte [instalar e configurar o cliente X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Para uma experiência de deslocamento smoother, ative o sinalizador de gfx.xrender.enabled no sobre: configuração num browser de VMs FireFox. [Ver mais aqui. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Considere também a ativando ou desativando *mousewheel.enable_pixel_scrolling* como False. [Instruções aqui.](https://support.mozilla.org/en-US/questions/981140)
* Um **AzureML conta**. Se ainda não tiver um, inscreva-se um novo no [home page do AzureML](https://studio.azureml.net/). Há uma camada de utilização livre para o ajudar a começar a utilizar.

## <a name="download-the-spambase-dataset"></a>Transferir o conjunto de dados spambase
O [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) conjunto de dados é um relativamente pequeno conjunto de dados que contém apenas 4601 exemplos. Este é um tamanho a utilizar quando demonstrar que algumas das principais funcionalidades da VM de ciência de dados que mantém os requisitos de recursos modest conveniente.

> [!NOTE]
> Estas instruções foi criada num D2 tamanho v2 dados ciência de Máquina Virtual com Linux. Este tamanho DSVM é capaz de processar os procedimentos nestas instruções.
>
>

Se precisar de mais espaço de armazenamento, pode criar discos adicionais e anexe-os à VM. Estes discos utilizam persistente storage do Azure, para que os seus dados são preservados, mesmo quando o servidor é reaprovisionado devido ao redimensionamento ou é encerrado. Para adicionar um disco e anexe-o à sua VM, siga as instruções em [adicionar um disco a uma VM com Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Estes passos utilizam a Interface de linha de comandos do Azure (CLI do Azure), que já está instalado o DSVM. Por isso, estes procedimentos podem ser realizados completamente da própria VM. Outra opção para aumentar o armazenamento está a utilizar [ficheiros do Azure](../../storage/files/storage-how-to-use-files-linux.md).

Para transferir os dados, abra uma janela de terminal e execute este comando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

O ficheiro transferido não tem uma linha de cabeçalho, por isso, vamos criar outro ficheiro, que têm um cabeçalho. Execute este comando para criar um ficheiro com os cabeçalhos adequados:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Em seguida, concatenar os dois ficheiros, juntamente com o comando:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

O conjunto de dados tem vários tipos de estatísticas sobre cada correio eletrónico:

* Colunas como ***word\_freq\_WORD*** indica a percentagem de palavras do e-mail que corresponde ao *WORD*. Por exemplo, se *word\_freq\_tornar* é 1, em seguida, 1 a % de todas as palavras no e-mail de foram *tornar*.
* Colunas como ***char\_freq\_CHAR*** indica a percentagem de todos os carateres de e-mail que foram *CHAR*.
* ***capital\_executar\_comprimento\_mais longo*** é o período mais longo de uma sequência de letras em maiúsculas.
* ***capital\_executar\_comprimento\_médio*** é a duração média de todas as sequências de letras em maiúsculas.
* ***capital\_executar\_comprimento\_total*** é o comprimento total de todas as sequências de letras em maiúsculas.
* ***spam*** indica se o e-mail foi considerado spam ou não (1 = spam, 0 = não spam).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorar o conjunto de dados com o Microsoft R aberta
Vamos examinar os dados e efetue algumas básico do machine learning com R. A VM de ciência de dados fornecida com [Microsoft R abra](https://mran.revolutionanalytics.com/open/) pré-instaladas. As bibliotecas de bibliotecas multithread nesta versão do R oferecem um melhor desempenho que várias versões single-threaded. Microsoft R abra igualmente reproducibility através da utilização de um instantâneo do repositório de pacote CRAN.

Para obter cópias dos exemplos de código utilizados nestas instruções, clone o **Azure-Machine-Learning--ciência de dados** repositório utilizando o git, que é previamente instalado na VM. A partir da linha de comandos de git, execute:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Abra uma janela de terminal e iniciar uma nova sessão de R com a consola interativa do R.

> [!NOTE]
> Também pode utilizar RStudio para os seguintes procedimentos. Para instalar RStudio, execute este comando num terminal:`./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Para importar os dados e configurar o ambiente, execute:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Para ver o resumidas estatísticas sobre cada coluna:

    summary(data)

Para obter uma vista diferente dos dados:

    str(data)

Isto mostra-lhe o tipo de cada variável e os valores de alguns primeiro no conjunto de dados.

O *spam* coluna foi lido como um número inteiro, mas é, na verdade, um categórico variável (ou fator). Para definir o tipo:

    data$spam <- as.factor(data$spam)

Para fazer algumas exploratórias análise, utilize o [ggplot2](http://ggplot2.org/) pacote, uma biblioteca graphing popular para R que já está instalado na VM. Tenha em atenção, os dados de resumo apresentada anteriormente, temos estatística de resumo sobre a frequência do caráter de ponto de exclamação. Vamos desenhar esses frequências com os seguintes comandos:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Uma vez que a barra de zero é skewing o desenho, vamos removê-lo:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Há uma densidade não trivial acima 1 procura interessante. Vamos ver apenas os dados:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Em seguida, divida por ham vs de spam:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Estes exemplos deverá ativar a fazer rastreia semelhante de outras colunas para explorar os dados contidos nos mesmos.

## <a name="train-and-test-an-ml-model"></a>Dar formação e testar um modelo de ML
Agora vamos dar formação sobre alguns modelos de machine learning para classificar as mensagens de correio eletrónico no conjunto de dados como contendo span ou ham. Vamos dar formação sobre um modelo de árvore de decisões e um modelo de floresta aleatório nesta secção e, em seguida, testar os respetivos precisão do seus predições.

> [!NOTE]
> O pacote de rpart (recursiva a criação de partições e árvores de regressão) utilizado no seguinte código já está instalado na VM de ciência de dados.
>
>

Primeiro, vamos dividido o conjunto de dados em conjuntos de formação e teste:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

E, em seguida, crie uma árvore de decisão para classificar as mensagens de correio eletrónico.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Eis o resultado:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar o quão bem executa no conjunto de preparação, utilize o seguinte código:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Para determinar o quão bem executa no conjunto de teste:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Vamos tentar também um modelo de floresta aleatório. Florestas aleatórias dar formação sobre um sem-número de árvores de decisões e de saída de uma classe que é o modo das classificações de todas as árvores de decisões individuais. Fornecem um mais poderoso do machine learning abordagem como estes corrigir para a tendência de um modelo de árvore de decisão para overfit um conjunto de dados de formação.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Implementar um modelo do Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) é um serviço em nuvem que torna mais fácil criar e implementar modelos de Análise Preditiva. Uma das funcionalidades nice do AzureML é a capacidade de publicar qualquer função de R como um serviço web. O pacote de AzureML R com a implementação mais fácil fazer à direita da nossa sessão de R o DSVM.

Para implementar o código de árvore de decisão da secção anterior, terá de iniciar sessão no Azure Machine Learning Studio. Terá do ID da área de trabalho e um token de autorização para iniciar sessão. Para localizar estes valores e inicializar as variáveis do AzureML com os mesmos:

Selecione **definições** no menu da esquerda. Tenha em atenção o **ID da área de trabalho**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Selecione **Tokens de autorização** do menu gerais e tenha em atenção o **Token de autorização primário**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Carga de **AzureML** do pacote e, em seguida, definir os valores das variáveis com o seu ID de token e área de trabalho na sua sessão de R no DSVM:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Vamos simplificar o modelo para facilitar esta demonstração a implementar. Escolha as três variáveis na árvore de decisões mais próxima para a raiz e criar uma nova árvore de utilizando apenas essas três variáveis:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

É preciso uma função de predição que utiliza as funcionalidades como entrada e devolve os valores previstos:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publicar a função de predictSpam AzureML utilizando o **publishWebService** função:

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Esta função aceita o **predictSpam** funcionar, cria um serviço web com o nome **spamWebService** com definidos entradas e saídas e devolve informações sobre o novo ponto final.

Ver os detalhes do serviço web publicada, incluindo o respetivo ponto final de API e chaves com o comando de acesso:

    spamWebService[[2]]

Para experimentar no primeiro conjunto de 10 linhas do teste:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Utilizar outras ferramentas disponíveis
As secções restantes mostram como utilizar algumas das ferramentas instaladas na VM de ciência de dados de Linux. Eis a lista de ferramentas abordados:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* Armazém de dados do SQL Server

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) é uma ferramenta que fornece uma implementação de árvore rápido e preciso elevada.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost também podem chamar do python ou uma linha de comandos.

## <a name="python"></a>Python
Para o desenvolvimento com o Python, foram instaladas as distribuições de Anaconda Python 2.7 e 3.5 o DSVM.

> [!NOTE]
> A distribuição de Anaconda inclui [Condas](http://conda.pydata.org/docs/index.html), que pode ser utilizado para criar ambientes personalizados para o Python que têm versões diferentes e/ou pacotes instalados nos mesmos.
>
>

Vamos lida alguns do conjunto de dados spambase e classificar os e-mails com máquinas de vetor de suporte no scikit-Saiba mais:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para tornar as predições:

    clf.predict(X.ix[0:20, :])

Para mostrar como publicar um ponto de final do AzureML, vamos certificar-num modelo mais simples de três variáveis como foi feito quando é publicado anteriormente o modelo de R.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para publicar o modelo para AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> Isto só está disponível para o python 2.7 e ainda não é suportado no 3.5. Executar com **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
A distribuição de Anaconda no DSVM inclui um bloco de notas do Jupyter, num ambiente de várias plataformas para partilhar código Python, R ou Leonor e análise. O bloco de notas do Jupyter é acedido através do JupyterHub. Iniciar sessão utilizando o seu nome de utilizador de Linux local e a palavra-passe no ***https://\<nome DNS de VM ou o endereço IP\>: 8000 /***. Todos os ficheiros de configuração para JupyterHub encontram-se no diretório **etc/jupyterhub**.

Vários blocos de notas do exemplo já se encontram instalados na VM:

* Consulte o [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) para um bloco de notas do Python de exemplo.
* Consulte [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) para um exemplo **R** bloco de notas.
* Consulte o [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) para outro exemplo **Python** bloco de notas.

> [!NOTE]
> O idioma de Leonor também está disponível na linha de comandos na VM de ciência de dados do Linux.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (o R analíticos ferramenta para saber mais facilmente) é uma ferramenta gráfica do R para extração de dados. Tem uma interface intuitiva que torna mais fácil carregar, explore e transformar dados e criar e avaliar os modelos.  O artigo [Rattle: GUI de extração de dados de um para R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece uma explicação passo a passo que demonstra as respetivas funcionalidades.

Instalar e iniciar Rattle com os seguintes comandos:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Não é necessária a instalação no DSVM. Mas Rattle pode solicitar-lhe instalar pacotes adicionais quando carrega-lo.
>
>

Rattle utiliza uma interface baseada no separador. A maioria dos separadores corresponde aos passos de [o processo de ciência de dados](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), como o carregamento de dados ou explorá-lo. O processo de ciência de dados flui da esquerda para a direita nos separadores. Mas o último separador contém um registo dos comandos R executado por Rattle.

Para carregar e configurar o conjunto de dados:

* Para carregar o ficheiro, selecione o **dados** separador, em seguida,
* Escolha o seletor junto a **Filename** e escolha **spambaseHeaders.data**.
* Para carregar o ficheiro. Selecione **executar** na linha superior dos botões. Deverá ver um resumo de cada coluna, incluindo o respetivo tipo de dados estejam identificados, se se trata de uma entrada, um destino ou outro tipo de variável e o número de valores exclusivos.
* Rattle corretamente identificou o **spam** coluna como destino. Selecione a coluna de spam, em seguida, definir o **tipo de dados de destino** para **Categoric**.

Para explorar os dados:

* Selecione o **explorar** separador.
* Clique em **resumo**, em seguida, **executar**, para ver algumas informações sobre os tipos de variável e algumas estatísticas de resumidas.
* Para ver outros tipos de estatísticas sobre cada variável, selecione outras opções como **Describe** ou **Noções básicas**.

O **explorar** separador também permite-lhe gerar rastreia insightful muitos. Para desenhar um histograma dos dados:

* Selecione **distribuições**.
* Verifique **histograma** para **word_freq_remove** e **word_freq_you**.
* Selecione **executar**. Deverá ver a ambos os rastreia densidade numa janela de gráfico único, onde é claro que a palavra "si" aparece muito mais frequentemente nos e-mails que "Remover".

A correlação rastreia também é interessante. Para criar um:

* Escolha **correlação** como o **tipo**, em seguida,
* Selecione **executar**.
* Rattle avisa que recomenda um máximo de 40 variáveis. Selecione **Sim** para ver o desenho.

Existem algumas correlações interessantes que pensar: "tecnologia" vivamente é correlacionada com "HP" e "laboratórios", por exemplo. É também adequada correlacionada com o "650", porque o código de área de donors o conjunto de dados for 650.

Os valores numéricos de correlações entre as palavras estão disponíveis na janela explorar. É interessante para nota, por exemplo, se "tecnologia" negativamente está correlacionada com "UR" e "dinheiro".

Rattle pode transformar o conjunto de dados para processar alguns problemas comuns. Por exemplo, permite-lhe rescale funcionalidades, impute valores em falta, processar valores atípicos e remover variáveis ou as observações com dados em falta. Rattle também pode identificar as regras de associação entre as observações e/ou variáveis. Estes separadores estão fora do âmbito para estas instruções introdutórias.

Rattle também pode executar uma análise do cluster. Vamos excluir algumas funcionalidades para o facilitar a leitura de saída. No **dados** separador, escolha **ignorar** junto a cada uma das variáveis, exceto estes dez itens:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Em seguida, volte à **Cluster** separador, escolha **KMeans**e defina o *número de clusters* para 4. Em seguida, **executar**. Os resultados são apresentados na janela de saída. Um cluster tem de alta frequência de "george" e "hp" e é provavelmente um e-mail de legítima de negócios.

Para criar um modelo de decisão simples árvore machine learning:

* Selecione o **modelo** separador,
* Escolha **árvore** como o **tipo**.
* Selecione **executar** para apresentar a árvore de formato de texto na janela de saída.
* Selecione o **desenhar** botão para ver uma versão de gráfica. Isto semelhante bastante à árvore que obteve anteriormente utilizando *rpart*.

Uma das funcionalidades nice do Rattle é a capacidade de executar vários métodos do machine learning e avaliar rapidamente-los. Eis o procedimento:

* Escolha **todos os** para o **tipo**.
* Selecione **executar**.
* Depois de concluído, pode clicar em qualquer único **tipo**, como **SVM**e ver os resultados.
* Também pode comparar o desempenho dos modelos em validação definido utilizando o **Evaluate** separador. Por exemplo, o **erro matriz** seleção mostra a matriz de confusão, o erro geral e o erro de classe de média para cada modelo no conjunto de validação.
* Também pode desenhar curvas ROC, executar uma análise de sensibilidade e fazer outros tipos de avaliações do modelo.

Quando tiver terminado de criação de modelos, selecione o **registo** separador para ver o código de R executado por Rattle durante a sessão. Pode selecionar o **exportar** botão para guardá-lo.

> [!NOTE]
> Há um erro na versão atual do Rattle. Para modificar o script ou utilizá-lo mais tarde a repetir os passos, tem de inserir um caráter de # à frente do * exportar este registo... * texto do registo.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
O DSVM inclui PostgreSQL instalado. PostgreSQL é uma base de dados relacional sofisticada, open source. Esta secção mostra como carregar o nosso conjunto de dados de spam para PostgreSQL e, em seguida, consultá-lo.

Pode carregar os dados, terá de permitir a autenticação de palavra-passe do localhost. Numa linha de comandos:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Perto do fim do ficheiro de configuração são várias linhas de detalhe de ligações permitido:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Altere a linha de "Ligações de locais de IPv4" possa utilizar md5 em vez de ident, pode iniciar sessão utilizando um nome de utilizador e palavra-passe:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

E reinicie o serviço de postgres:

    sudo systemctl restart postgresql

Para iniciar psql, um terminal interativa para PostgreSQL, como o utilizador postgres incorporada, execute o seguinte comando a partir de uma linha:

    sudo -u postgres psql

Crie uma nova conta de utilizador, utilizando o mesmo nome de utilizador como a conta de Linux está atualmente sessão iniciada como e atribua-lhe uma palavra-passe:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Em seguida, inicie sessão no psql como o utilizador:

    psql

E importar os dados para uma nova base de dados:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Agora, vamos explorar os dados e executar algumas consultas com **Squirrel SQL**, uma ferramenta gráfica que lhe permite interagir com as bases de dados através de um controlador JDBC.

Para começar, inicie o Squirrel SQL no menu de aplicações. Para configurar o controlador:

* Selecione **Windows**, em seguida, **ver controladores**.
* Clique com o botão direito no **PostgreSQL** e selecione **modificar controlador**.
* Selecione **Extra classe caminho**, em seguida, **adicionar**.
* Introduza ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** para o **nome de ficheiro** e
* Selecione **abra**.
* Escolha a lista de controladores, em seguida, selecione **org.postgresql.Driver** no **nome da classe**e selecione **OK**.

Para configurar a ligação ao servidor local:

* Selecione **Windows**, em seguida, **ver Aliases.**
* Escolha o  **+**  botão para tornar um novo alias.
* Nome *base de dados de Spam*, escolha **PostgreSQL** no **controlador** pendente.
* Definir o URL *jdbc:postgresql://localhost/spam*.
* Introduza o *username* e *palavra-passe*.
* Clique em **OK**.
* Para abrir o **ligação** janela, faça duplo clique o ***base de dados de Spam*** alias.
* Selecione **Ligar**.

Para executar algumas consultas:

* Selecione o **SQL** separador.
* Introduza uma consulta simples como `SELECT * from data;` na caixa de texto de consulta na parte superior do separador do SQL Server.
* Prima **Ctrl-introduza** executá-la. Por predefinição o Squirrel SQL devolve os primeiros 100 linhas da sua consulta.

Existem demasiadas consultas mais que poderia executar para explorar estes dados. Por exemplo, como funciona a frequência da palavra *tornar* diferem entre spam e ham?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou quais são as características de e-mails que contêm frequentemente *3d*?

    SELECT * from data order by word_freq_3d desc;

A maioria dos e-mails que tenham uma ocorrência elevada *3d* são parecer lhe enviar spam, pelo que pode ser uma funcionalidade útil para a criação de um modelo preditivo para classificar as mensagens de correio eletrónico.

Se pretendesse executar aprendizagem com dados armazenados numa base de dados PostgreSQL, considere utilizar [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Armazém de dados do SQL Server
O Azure SQL Data Warehouse é uma base de dados de escalabilidade horizontal, baseada na nuvem, capaz de processar grandes volumes de dados, tanto relacionais como não relacionais. Para obter mais informações, consulte [que é o Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para ligar ao armazém de dados e criar a tabela, execute o seguinte comando numa linha de comandos:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Em seguida, na linha de comandos sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Dados de cópia com o bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Os endings de linha no ficheiro descarregado são estilo do Windows, mas bcp espera UNIX-estilo de, pelo que precisamos de dizer que bcp com o sinalizador - r.
>
>

E consultar com sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Também pode consultar com Squirrel SQL. Seguir passos semelhantes para PostgreSQL, utilizando o controlador JDBC de servidor do Microsoft MSSQL, que pode ser encontrado na ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Passos seguintes
Para obter uma descrição geral de tópicos que descrevem as tarefas que compõem o processo de ciência de dados no Azure, consulte [o processo de ciência de dados de equipa](http://aka.ms/datascienceprocess).

Para obter uma descrição de outras instruções ponto-a-ponto que demonstram os passos no processo de ciência de dados de agrupamento para cenários específicos, consulte [instruções do processo de ciência de dados de equipa](../team-data-science-process/walkthroughs.md). As instruções também mostram como combinar as ferramentas de nuvem e no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente.
