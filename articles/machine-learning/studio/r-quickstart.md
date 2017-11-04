---
title: "Tutorial de início rápido para o idioma de R para o Machine Learning | Microsoft Docs"
description: "Utilize este tutorial de programação do R para começar a trabalhar rapidamente utilizando a linguagem de R com o Azure Machine Learning Studio para criar uma solução de previsão."
keywords: "Guia de introdução, idioma de r, linguagem de programação de r, tutorial de programação de r"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 99a3a0fd-b359-481a-b236-66868deccd96
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: garye
ms.openlocfilehash: 40cc3728d1361b9304896bf0cc4ceed439291d45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Tutorial de início rápido para a linguagem de programação R para o Azure Machine Learning

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introdução
Este tutorial do guia de introdução ajuda-o a começar rapidamente a expansão do Azure Machine Learning, utilizando a linguagem de programação do R. Siga este tutorial de programação do R para criar, testar e executar código do R no Azure Machine Learning. À medida que trabalha através do tutorial, irá criar uma solução completa de previsão utilizando a linguagem de R no Azure Machine Learning.  

Microsoft Azure Machine Learning contém muitos poderosas machine learning e dados manipulação módulos. O idioma de R poderoso tem foi descrito como o franca lingua de análise. Happily, manipulação de dados e de análise no Azure Machine Learning pode ser expandida através da utilização de R. Esta combinação fornece a escalabilidade e a facilidade de implementação do Azure Machine Learning com a flexibilidade e a análise profunda do R.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="forecasting-and-the-dataset"></a>Previsão e o conjunto de dados
Previsão é um método de analítico bastante úteis e amplamente utilizado. Comuns utiliza o intervalo de prever vendas de itens sazonais, determinar os níveis de inventário ideal, ao prever macroeconomic variáveis. Previsão é geralmente feita com os modelos de série de tempo.

Dados de séries de tempo são os dados na qual os valores tem um índice de tempo. O índice de tempo pode ser normal, por exemplo, a todos os meses ou a cada minuto, ou dados. Um modelo de série de tempo é baseado em dados de série de tempo. A linguagem de programação de R contém uma arquitetura flexível e análise de um vasto conjunto de dados de séries de tempo.

Neste guia de início rápido será possível trabalhar com produção dairy Califórnia e dados de preço. Estes dados incluem informações mensais sobre a produção de vários produtos dairy e o preço de fat milk, um produto benchmark.

Os dados utilizados neste artigo, juntamente com os scripts de R, podem ser [transferida aqui][download]. Estes dados foi originalmente synthesized da informação disponível University de Wisconsin em http://future.aae.wisc.edu/tab/production.html.

### <a name="organization"></a>Organização
Iremos irá percorre vários passos, irá aprender a criar, testar e executar o código de manipulação R de análise e os dados no ambiente do Azure Machine Learning.  

* Primeiro, vamos explorar as noções básicas do utilizando a linguagem de R no ambiente do Azure Machine Learning Studio.
* Em seguida, iremos avança para debater diferentes aspetos de e/s de dados, o código do R e gráficos no ambiente do Azure Machine Learning.
* Em seguida, iremos irá construir a primeira parte da nossa solução previsão através da criação de código para a limpeza de dados e a transformação.
* Com os nossos dados preparados, iremos efetuar uma análise de correlações entre várias as variáveis no nosso conjunto de dados.
* Por fim, vamos criar um modelo de previsão de série de tempo sazonais para produção milk.

## <a id="mlstudio"></a>Interagir com o idioma de R no Machine Learning Studio
Esta secção orienta-algumas noções básicas da interação com a linguagem de programação de R no ambiente de Machine Learning Studio. O idioma de R fornece uma ferramenta poderosa para criar os módulos de manipulação de dados no ambiente do Azure Machine Learning e de análise personalizada.

Utilizarei RStudio para desenvolver, testar e depurar código do R numa pequena escala. Este código é, em seguida, cortar e colar para um [executar Script do R] [ execute-r-script] módulo no Machine Learning Studio pronto para ser executado.  

### <a name="the-execute-r-script-module"></a>O módulo de executar o Script do R
No Machine Learning Studio, scripts de R são executados dentro do [executar Script do R] [ execute-r-script] módulo. Um exemplo do [executar Script do R] [ execute-r-script] módulo no Machine Learning Studio é mostrado na figura 1.

 ![R linguagem de programação: módulo a executar o Script do R selecionado no Machine Learning Studio][1]

*Figura 1. O ambiente de Machine Learning Studio que mostra o módulo de executar o Script do R selecionado.*

Referência a figura 1, vamos ver alguns das peças chaves do ambiente de Machine Learning Studio para trabalhar com o [executar Script do R] [ execute-r-script] módulo.

* Os módulos a experimentação são apresentados no painel central.
* A parte superior do painel da direita contém uma janela para ver e editar os scripts de R.  
* A parte inferior do painel direito apresenta algumas propriedades do [executar Script do R][execute-r-script]. Pode ver os registos de erros e de saída ao clicar nas oportunidades adequadas neste painel.

Iremos obviamente, será, debater o [executar Script do R] [ execute-r-script] mais detalhadamente o resto deste documento.

Ao trabalhar com funções de R complexas, posso recomendado editar, testar e depurar no RStudio. Tal como acontece com quaisquer desenvolvimento de software, expandir o seu código de forma incremental e testá-lo num pequenos casos de teste simples. Em seguida, corte e cole as suas funções para a janela de script do R do [executar Script do R] [ execute-r-script] módulo. Esta abordagem permite-lhe harness o ambiente de desenvolvimento integrado do RStudio (IDE) e a capacidade do Azure Machine Learning.  

#### <a name="execute-r-code"></a>Executar código do R
Qualquer código de R no [executar Script do R] [ execute-r-script] módulo será executado quando executar a experimentação ao clicar no **executar** botão. Quando a execução foi concluída, aparece uma marca de verificação no [executar Script do R] [ execute-r-script] ícone.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>R defesas codificação do Azure Machine Learning
Se estiver a desenvolver o código do R para dizer, um serviço web utilizando o Azure Machine Learning, deve sem dúvida planeia como o seu código irá lidar com uma entrada de dados inesperados e exceções. Para manter os efeitos de clareza, não posso ter incluídas muito in the way of a verificação ou a excepção a processar a maioria dos exemplos de código mostrados. No entanto, à medida que avança posso irá dar-lhe são vários os exemplos de funções através da utilização de exceção do R capacidade de processamento.  

Se precisar de um tratamento mais completo de processamento de exceções de R, posso Recomendamos que leia as secções aplicáveis do livro por Wickham listado no [apêndice B - leitura mais](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Depuração e o teste R no Machine Learning Studio
Para reiterate, posso recomendável testar e depurar o código do R numa escala no RStudio pequena. No entanto, há casos em que tem de identificar problemas de código do R no [executar Script do R] [ execute-r-script] próprio. Além disso, é recomendável verificar os resultados no Machine Learning Studio.

Resultado da execução do seu código de R e na plataforma do Azure Machine Learning encontra-se principalmente na output.log. Algumas informações adicionais serão apresentadas no error.log.  

Se ocorrer um erro ao executar o código do R no Machine Learning Studio, deve ser o primeiro método de ação ver error.log. Este ficheiro pode conter mensagens de erro útil para ajudar a compreender e corrigir o erro. Para ver error.log, clique em **registo de erros do vista** no **painel Propriedades** para o [executar Script do R] [ execute-r-script] que contém o erro.

Por exemplo, posso executou o seguinte código do R, com um indefinido y variável, num [executar Script do R] [ execute-r-script] módulo:

    x <- 1.0
    z <- x + y

Este código não consegue executar, causando uma condição de erro. Clicar no **registo de erros do vista** no **painel Propriedades** produz a apresentação mostrada na figura 2.

  ![Mensagem de erro pop-up][2]

*Figura 2. Pop-up de mensagem de erro.*

Parece que precisamos de procurar no output.log para ver a mensagem de erro do R. Clique em de [executar Script do R] [ execute-r-script] e, em seguida, clique no **ver output.log** item no **painel Propriedades** à direita. Abre uma nova janela do browser e posso ver o seguinte.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Esta mensagem de erro contém não surpresas e identifica o problema.

Para inspecionar o valor de qualquer objeto no R, pode imprimir estes valores para o ficheiro output.log. As regras para examinar os valores de objeto são essencialmente o mesmo que uma sessão interativa de R. Por exemplo, se escrever um nome de variável de uma linha, o valor do objeto serão impressos para o ficheiro output.log.  

#### <a name="packages-in-machine-learning-studio"></a>Pacotes no Machine Learning Studio
O Azure Machine Learning é fornecido com mais 350 pacotes de idiomas de R pré-instaladas. Pode utilizar o seguinte código a [executar Script do R] [ execute-r-script] módulo para obter uma lista dos pacotes pré-instaladas.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Se não compreender a última linha deste código neste momento, continue a ler. O resto deste documento, vamos extensivamente abordar utilizar R no ambiente do Azure Machine Learning.

### <a name="introduction-to-rstudio"></a>Introdução ao RStudio
RStudio é um IDE amplamente utilizado para R. Utilizarei RStudio para editar, teste e depuração algumas do código de R utilizado neste guia de início rápido. Depois de código do R é testada e pronto, basta cortar e colar a partir do editor de RStudio num Machine Learning Studio [executar Script do R] [ execute-r-script] módulo.  

Se não tiver a linguagem de programação de R instalada no seu computador de secretária, recomendamos posso que fazê-lo agora. Transferências gratuitas de idioma de open source para R estão disponíveis no abrangente R arquivo rede (CRAN) em [http://www.r-project.org/](http://www.r-project.org/). As transferências estão disponíveis para o Windows, SO de Mac e Linux/UNIX. Escolha um espelho próximos em e siga as instruções de transferência. Além disso, CRAN contém vários pacotes de manipulação de dados e de análise útil.

Se estiver familiarizado com RStudio, deve transferir e instalar a versão de ambiente de trabalho. Pode encontrar que o RStudio transfere para o Windows, SO de Mac e Linux/UNIX http://www.rstudio.com/products/RStudio/. Siga as instruções fornecidas para instalar RStudio no seu computador de secretária.  

Uma introdução tutorial RStudio está disponível em https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Posso fornecem algumas informações adicionais sobre como utilizar RStudio no [anexo A][appendixa].  

## <a id="scriptmodule"></a>Obter dados e para o módulo de executar o Script do R
Nesta secção, vamos abordar como obter dados para dentro e fora do [executar Script do R] [ execute-r-script] módulo. Iremos irá Reveja como lidar com vários tipos de dados de leitura para dentro e fora do [executar Script do R] [ execute-r-script] módulo.

O código de completado para esta secção é no ficheiro zip que transferiu anteriormente.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Carregar e verificar dados no Machine Learning Studio
#### <a id="loading"></a>Carregar o conjunto de dados
Iremos será iniciado ao carregar o **csdairydata.csv** ficheiro para o Azure Machine Learning Studio.

* Inicie o seu ambiente do Azure Machine Learning Studio.
* Clique em **+ novo** na parte inferior esquerda do ecrã e selecione **Dataset**.
* Selecione **do ficheiro Local**e, em seguida, **procurar** para selecionar o ficheiro.
* Certifique-se de que selecionou **ficheiro CSV genérico com o cabeçalho (. csv)** como o tipo do conjunto de dados.
* Clique na marca de verificação.
* Depois do conjunto de dados terem sido carregado, deverá ver o novo conjunto de dados ao clicar no **conjuntos de dados** separador.  

#### <a name="create-an-experiment"></a>Criar uma experimentação
Agora que temos alguns dados no Machine Learning Studio, temos de criar uma experimentação para efetuar a análise.  

* Clique em **+ novo** em inferior à esquerda e selecione **experimentação**, em seguida, **experimentação em branco**.
* Pode atribuir o nome sua experimentação, selecionando e modificar, o **experimentação criado no...**  título na parte superior da página. Por exemplo, alterá-la para **AC Dairy Analysis**.
* No lado esquerdo da página de experimentação, expanda **conjuntos de dados guardado**e, em seguida, **conjuntos de dados os meus**. Deverá ver o **cadairydata.csv** que carregou anteriormente.
* Arrastar e largar o **csdairydata.csv dataset** para a experimentação.
* No **pesquisa experimentação itens** caixa no topo do painel esquerdo, tipo [executar Script do R][execute-r-script]. Verá o módulo de aparecer na lista de pesquisa.
* Arrastar e largar o [executar Script do R] [ execute-r-script] módulo no seu palete.  
* Ligar o resultado a **csdairydata.csv conjunto de dados** para a entrada mais à esquerda (**Dataset1**) da [executar Script do R][execute-r-script].
* **Não se esqueça de clicar em 'Guardar'!**  

Neste momento sua experimentação deve ter um aspeto semelhante a figura 3.

![Experimentar a análise de Dairy de AC com o conjunto de dados e o módulo de executar o Script do R][3]

*Figura 3. Experimentar a análise de Dairy de AC com o conjunto de dados e o módulo de executar o Script do R.*

#### <a name="check-on-the-data"></a>Verificar os dados
Vamos tem uma vista de olhos os dados que carregou na nossa experiência. Na experimentação, clique no resultado do **cadairydata.csv dataset** e selecione **visualizar**. Deverá ver algo semelhante a figura 4.  

![Resumo do conjunto de dados cadairydata.csv][4]

*Figura 4. Resumo do conjunto de dados cadairydata.csv.*

Nesta vista, Vemos muitas informações úteis. É possível ver as primeiras linhas vários desse conjunto de dados. Se selecionar é uma coluna, a secção de estatísticas mostra mais informações sobre a coluna. Por exemplo, a linha de tipo de funcionalidade mostra-nos de que tipos de dados do Azure Machine Learning Studio atribuído à coluna. Ter um aspeto rápido como esta é uma verificação de boa sanity antes de iniciar a podemos fazer qualquer trabalho grave.

### <a name="first-r-script"></a>Primeiro script R
Vamos criar um script do R primeiro simple para experimentar no Azure Machine Learning Studio. Posso ter criado e testado o seguinte script no RStudio.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    str(cadairydata)
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Agora necessário transferir este script para o Azure Machine Learning Studio. Posso pode simplesmente cortar e colar. No entanto, neste caso, posso irá transferir os meus script R através de um ficheiro zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrada de dados para o módulo de executar o Script do R
Vamos tem uma vista de olhos entradas para a [executar Script do R] [ execute-r-script] módulo. Neste exemplo iremos ler os dados dairy Califórnia para o [executar Script do R] [ execute-r-script] módulo.  

Existem três entradas possíveis para o [executar Script do R] [ execute-r-script] módulo. Pode utilizar qualquer uma ou todas estas entradas, dependendo da sua aplicação. Também é perfeitamente razoável utilizar um script do R que não demora a nenhuma entrada de todo.  

Vamos ver cada uma destas entradas, que vai da esquerda para a direita. Pode ver os nomes de cada uma das entradas ao colocar o cursor sobre a entrada e ao ler a descrição.  

#### <a name="script-bundle"></a>Pacote de script
O pacote de Script de entrada permite-lhe transferir os conteúdos de um ficheiro zip para [executar Script do R] [ execute-r-script] módulo. Pode utilizar um dos seguintes comandos para ler o conteúdo do ficheiro zip para o seu código do R.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> O Azure Machine Learning processa ficheiros zip, como se estão a ser a src / diretório, por isso terá de prefixo os nomes de ficheiro com este nome de diretório. Por exemplo, se zip contém os ficheiros `yourfile.R` e `yourData.rdata` na raiz da zip, teria de endereços como `src/yourfile.R` e `src/yourData.rdata` quando utilizar `source` e `load`.
> 
> 

Já discutimos conjuntos de dados de carregamento no [ao carregar o conjunto de dados](#loading). Depois de ter criado e testado o script do R mostrado na secção anterior, efetue o seguinte:

1. Guarde o script do R para um. Ficheiro de R. Chamar o meu ficheiro de script "simpleplot. R". Eis o conteúdo.
   
        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')
2. Crie um ficheiro zip e copie o script para este ficheiro zip. No Windows, clique com o botão direito no ficheiro e selecione **enviar para**e, em seguida, **Compressed pasta**. Esta ação irá criar um novo ficheiro zip que contém a "simpleplot. Ficheiro de R".
3. Adicione o ficheiro para o **conjuntos de dados** no Machine Learning Studio, especificar o tipo como **zip**. Deverá ver o ficheiro zip nos seus conjuntos de dados.
4. Arrastar e largar o ficheiro zip do **conjuntos de dados** para o **tela do ML Studio**.
5. Ligar o resultado a **zip dados** ícone para a **Script pacote** entrada do [executar Script do R] [ execute-r-script] módulo.
6. Tipo de `source()` função com o nome de ficheiro zip para a janela de código para o [executar Script do R] [ execute-r-script] módulo. No meu caso posso escreveu `source("src/simpleplot.R")`.  
7. Certifique-se de que clica **guardar**.

Assim que estes passos estiverem concluídos, o [executar Script do R] [ execute-r-script] módulo executará o script R no ficheiro zip ao executar a experimentação. Neste momento sua experimentação deve ter um aspeto semelhante a figura 5.

![Utilizando a experimentação zipado R script][6]

*Figura 5. Utilizando a experimentação zipado R script.*

#### <a name="dataset1"></a>Dataset1
Pode passar uma tabela de dados retangular ao código de R ao utilizar a entrada de Dataset1. No nosso scripts simples do `maml.mapInputPort(1)` função lê os dados da porta de 1. Estes dados, em seguida, são atribuídos a um nome de variável dataframe no seu código. No nosso scripts simples a primeira linha de código efetua a atribuição.

    cadairydata <- maml.mapInputPort(1)

Executar a experimentação ao clicar no **executar** botão. Quando a execução estiver concluída, clique em de [executar Script do R] [ execute-r-script] módulo e, em seguida, clique em **ver o registo de saída** no painel de propriedades. Uma nova página deve aparecer no seu browser, que mostra o conteúdo do ficheiro output.log. Se deslocar para baixo, deverá ver algo semelhante a seguinte.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Farther verticalmente na página é que informações mais detalhadas sobre as colunas, o que irá ter um aspeto semelhante a seguinte.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Estes resultados são principalmente conforme esperado, com as 228 observações e 9 colunas no dataframe. É possível ver os nomes das colunas, o tipo de dados de R e um exemplo de cada coluna.

> [!NOTE]
> Este resultado impressos mesmo é convenientemente disponível a partir da saída do dispositivo de R o [executar Script do R] [ execute-r-script] módulo. As saídas da, vamos abordar o [executar Script do R] [ execute-r-script] módulo na secção seguinte.  
> 
> 

#### <a name="dataset2"></a>Dataset2
O comportamento da entrada Dataset2 é idêntico ao que Dataset1. Utilizar esta entrada pode passar uma segunda tabela retangular dos dados para o seu código do R. A função `maml.mapInputPort(2)`, com o argumento 2, é utilizado para transferir estes dados.  

### <a name="execute-r-script-outputs"></a>Executar Script do R saídas
#### <a name="output-a-dataframe"></a>Um dataframe de saída
O utilizador pode apresentar os conteúdos de um dataframe R como uma tabela retangular através da porta de resultado Dataset1 utilizando o `maml.mapOutputPort()` função. No nosso script R simple esta é executada a seguinte linha.

    maml.mapOutputPort('cadairydata')

Depois de executar a experimentação, clique na porta de saída do resultado Dataset1 e, em seguida, clique em **visualizar**. Deverá ver algo semelhante a figura 6.

![A visualização de saída dos dados dairy Califórnia][7]

*Figura 6. A visualização de saída dos dados dairy Califórnia.*

Este resultado procura idêntico à entrada, tal como era esperado.  

### <a name="r-device-output"></a>Saída de dispositivo de R
A saída de dispositivo do [executar Script do R] [ execute-r-script] módulo contém gráficos e as mensagens de saída. Ambas as mensagens de saída e o erro padrão padrão de R são enviadas para a porta de saída do dispositivo de R.  

Para ver o resultado de R dispositivo, clique na porta e, em seguida, no **visualizar**. Vamos ver a saída padrão e o erro padrão do script R figura 7.

![Saída padrão e o erro padrão da porta do dispositivo de R][8]

*A figura 7. Saída padrão e o erro padrão da porta do dispositivo de R.*

Deslocar o ecrã para baixo, consulte a saída de gráficos do nosso script R na figura 8.  

![Saída de gráficos da porta do dispositivo de R][9]

*Figura 8. Gráficos de saída da porta do dispositivo de R.*  

## <a id="filtering"></a>Filtragem de dados e a transformação
Nesta secção, iremos efetuar alguns dados básicos filtragem e operações de transformação nos dados dairy Califórnia. No final desta secção temos dados num formato adequado para a criação de um modelo de análise.  

Mais especificamente, nesta secção, iremos efetuar alguns dados de limpeza e a transformação tarefas comuns: tipo de transformação, filtragem dataframes, adicionar novas colunas calculadas e o valor transformações. Este em segundo plano deve ajudar a lidar com muitas variações encontradas em problemas do mundo real.

O código do R completado para esta secção está disponível no ficheiro zip que transferiu anteriormente.

### <a name="type-transformations"></a>Transformações de tipo
Agora que iremos possa ler os dados de dairy de Califórnia para o código do R no [executar Script do R] [ execute-r-script] módulo, precisamos de garantir que os dados nas colunas tem o tipo pretendido e o formato.  

R é uma linguagem escrita de forma dinâmica, o que significa que os tipos de dados são forçados a partir de um para outro conforme necessário. Os tipos de R dados atómico incluem numérico, caráter e lógico. O tipo de fator é utilizado para armazenar compactly dados categóricos. Pode encontrar muito mais informações sobre tipos de dados nas referências na [apêndice B - ler mais](#appendixb).

Quando os dados em tabela é lido no R de uma origem externa, é sempre boa ideia Verifique os tipos resultantes nas colunas. Poderá pretender que a uma coluna de carácter de tipo, mas em muitos casos isto irá aparecer como fator ou vice-versa. Noutros casos, uma coluna que pensa que deve ser numérica é representada por dados de carateres, por exemplo número de pontos '1.23' em vez de 1.23 como uma vírgula flutuante.  

Felizmente, é fácil converter um tipo para outro, desde que o mapeamento está possíveis. Por exemplo, não é possível converter 'Nevada' para um valor numérico, mas pode convertê-la para um fator (variável categórico). Outro exemplo, pode converter um numérico 1 num caráter '1' ou um fator.  

A sintaxe para qualquer um destes conversões é simple: `as.datatype()`. Estas funções de conversão do tipo incluem o seguinte:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Observar os tipos de dados das colunas, introduzimos na secção anterior: todas as colunas são do tipo numérico, exceto para a coluna com a etiqueta 'Mês', que é de carácter de tipo. Vamos converter para um fator e testar os resultados.  

Posso ter eliminado a linha que criou a matriz de scatterplot e adicionar uma linha ao converter a coluna 'Mês' para um fator. Na minha experiência posso apenas será cortar e colar o código do R para a janela do código do [executar Script do R] [ execute-r-script] módulo. Também pode atualizar o ficheiro. zip e carregue-o no Azure Machine Learning Studio, mas esta ação demora vários passos.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(cadairydata$Month)
    str(cadairydata) # Check the result
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Vamos executar este código e observe o registo de saída para o script do R. Os dados relevantes do registo são mostrados na figura 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 9. Resumo de dataframe com uma variável de fator.*

Deverá agora indicar o tipo para o mês '**fator com 14 níveis**'. Este é um problema, uma vez que existem apenas de 12 meses no ano. Também pode verificar para ver que o tipo de **visualizar** do conjunto de dados de resultado da porta é '**Categorical**'.

O problema é que a coluna 'Mês' não foi codificada sistematicamente. Em alguns casos, um mês é chamado Abril e outros é abreviado como Apr. Iremos pode resolver este problema, trimming a cadeia de 3 carateres. A linha de código agora o seguinte aspeto:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Executar novamente a experimentação e ver o registo de saída. Os resultados esperados são apresentados na figura 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*A figura 10. Resumo de dataframe com o número correto de níveis de fator.*

A nossa variável fator tem agora os níveis de 12 pretendidos.

### <a name="basic-data-frame-filtering"></a>Filtragem de moldura de dados básicos
R dataframes suporta as capacidades de filtragem poderosas. Conjuntos de dados podem ser subsetted utilizando filtros lógicos em linhas ou colunas. Em muitos casos, os critérios de filtro complexa será necessários. As referências na [apêndice B - ler mais](#appendixb) contém um vasto conjunto exemplos de filtragem dataframes.  

Há um bit de filtragem deve fazer no nosso conjunto de dados. Se observar as colunas do dataframe cadairydata, verá duas colunas desnecessárias. A primeira coluna contém apenas um número de linha, que não é muito útil. A segunda coluna, Year.Month, contém informações redundantes. Iremos facilmente pode excluir estas colunas utilizando o seguinte código do R.

> [!NOTE]
> De agora nesta secção, posso apenas mostrará códigos adicionais estou a adicionar no [executar Script do R] [ execute-r-script] módulo. Posso adicionar cada nova linha **antes** o `str()` função. Posso utilizar esta função para verificar a minha resultados no Azure Machine Learning Studio.
> 
> 

Posso adicionar a seguinte linha de código my R no [executar Script do R] [ execute-r-script] módulo.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Execute este código na sua experimentação e verificar o resultado do registo de saída. Estes resultados são apresentados na figura 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 11. Resumo de dataframe com duas colunas removido.*

Boas notícias! Estamos a obter os resultados esperados.

### <a name="add-a-new-column"></a>Adicionar uma nova coluna
Para criar modelos de série de tempo será conveniente ter uma coluna que contém os meses desde o início da série de tempo. Iremos criar uma nova coluna 'Month.Count'.

Para ajudar a organizar o código que iremos criar nosso primeira função simple, `num.month()`. Em seguida, iremos aplicar esta função para criar uma nova coluna a dataframe. Segue-se o novo código.

    ## Create a new column with the month count
    ## Function to find the number of months from the first
    ## month of the time series
    num.month <- function(Year, Month) {
      ## Find the starting year
      min.year  <- min(Year)

      ## Compute the number of months from the start of the time series
      12 * (Year - min.year) + Month - 1
    }

    ## Compute the new column for the dataframe
    cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

Agora, execute a experimentação atualizada e utilizar o registo de saída para ver os resultados. Estes resultados são apresentados na figura 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 12. Resumo de dataframe com a coluna adicional.*

Parece que tudo está a funcionar. Temos a nova coluna com os valores esperados no nosso dataframe.

### <a name="value-transformations"></a>Transformações de valor
Nesta secção, iremos efetuar algumas transformações simples nos valores de algumas das colunas da nossa dataframe. A linguagem de R suporta transformações de valor quase arbitrário. As referências na [apêndice B - leitura mais](#appendixb) contém um vasto conjunto exemplos.

Se observar os valores existentes na resumos do nosso dataframe que deverá ver algo ímpar aqui. Mais de gelado que milk é produzido na Califórnia? Não, obviamente não, como isto faz nenhuma sentido, se como este facto podem ser a alguns dos EUA gelado lovers. As unidades são diferentes. O preço está em unidades de nos pounds, milk é em unidades de 1 milhão E.U.A. pounds, gelado é em unidades de 1.000 nos gallons e cottage cheese está em unidades de 1.000 E.U.A. pounds. Partindo do princípio de gelado weighs sobre 6.5 pounds por litro, pode facilmente fazemos multiplicação converter estes valores para que estejam todos na iguais unidades de pounds 1000.

Para o nosso modelo de previsão Utilizamos um modelo multiplicative de tendência e ajuste sazonais destes dados. Uma transformação de registo permite-nos utilizar um modelo de linear, simplificando a este processo. Pode aplicar a transformação de registo na mesma função onde o multiplicador é aplicado.

O código seguinte, posso definir uma nova função `log.transform()`e aplicá-la para as linhas que contém os valores de numérico. O R `Map()` função é utilizada para aplicar o `log.transform()` função para as colunas selecionadas do dataframe. `Map()`é semelhante à `apply()` , mas permite a mais do que uma lista de argumentos à função. Tenha em atenção que uma lista de multipliers fornece o segundo argumento para o `log.transform()` função. O `na.omit()` função é utilizada como um bit de limpeza para se certificar de que não têm valores em falta ou não definidos no dataframe.

    log.transform <- function(invec, multiplier = 1) {
      ## Function for the transformation, which is the log
      ## of the input value times a multiplier

      warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                           "ERROR: Arguments to function log.transform must be greate than zero",
                           "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                           "ERROR: Invalid time seies value encountered in function log.transform"
                           )

      ## Check the input arguments
      if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
      if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
      if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

      ## Wrap the multiplication in tryCatch
      ## If there is an exception, print the warningmessage to
      ## standard error and return NA
      tryCatch(log(multiplier * invec),
               error = function(e){warning(warningmessages[4]); NA})
    }


    ## Apply the transformation function to the 4 columns
    ## of the dataframe with production data
    multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
    cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

    ## Get rid of any rows with NA values
    cadairydata <- na.omit(cadairydata)  

Não há bastantes acontecer bits no `log.transform()` função. Na maioria deste código está a verificar potenciais problemas com os argumentos ou lidar com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas deste código, na verdade, efetue os cálculos.

O objetivo de programação defesas é impedir a falha de uma única função que impede o processamento de continuar. Uma falha de uma análise de execução longa abrupto pode ser bastante frustrating para os utilizadores. Para evitar esta situação, os valores de retorno de predefinido tem de escolher que irá limitar danos processamento a jusante. Uma mensagem também é produzida para alertar utilizadores que algo ficou errado.

Se não forem utilizados para programação defesas no R, todos os este código pode parecer um pouco complicado. Posso irá guiá-lo através dos passos principais:

1. Um vetor de quatro mensagens está definido. Estas mensagens são utilizadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com este código.
2. Devolve o valor para cada caso. Existem muitas outras possibilidades que poderão ter menos efeitos secundários. Posso foi possível devolver um vetor de zeros, ou o vetor de entrada original, por exemplo.
3. Verificações são executadas nos argumentos da função. Se for detetado um erro, é devolvido um valor predefinido e uma mensagem é produzida por cada caso, o `warning()` função. Estou a utilizar `warning()` vez `stop()` como a última opção irá terminar a execução, exatamente o que estou a tentar evitar. Tenha em atenção o que posso ter escrito este código num style técnicos, como no caso deste uma abordagem funcional seemed obscura e complexas.
4. Os cálculos de registo são moldados numa `tryCatch()` para que as exceções não irão causar uma halt abrupto para processamento. Sem `tryCatch()` maior parte dos erros gerados pelos resultados de funções de R num sinal de paragem, que é o mesmo.

Executar este código de R na sua experimentação e ter uma vista de olhos a saída impressos no ficheiro de output.log. Agora verá os valores transformados das quatro colunas no registo, conforme mostrado na figura 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 13. Resumo dos valores transformados no dataframe.*

Vamos ver que os valores de ter sido transformados. Agora a produção milk significativamente excede todos os outra produtos dairy de produção, recupera um que iremos está agora a visualizar uma escala de registo.

Neste momento é limpa nossos dados e estamos prontos para algumas modelação. Observar a visualização de resumo para a saída de conjunto de dados de resultados do nosso [executar Script do R] [ execute-r-script] módulo, verá a coluna 'Mês' for 'Categorical' com 12 valores exclusivos, novamente, tal como queremos.

## <a id="timeseries"></a>Análise de correlação e objetos de séries de tempo
Nesta secção, iremos explorar alguns objetos de séries de tempo do R básicos e analisar as correlações entre alguma das variáveis de. O nosso objetivo é um dataframe que contém as informações de correlação pairwise em várias lags de saída.

O código do R completado para esta secção é no ficheiro zip que transferiu anteriormente.

### <a name="time-series-objects-in-r"></a>Objetos de séries de tempo no R
Como mencionado já, tempo série é uma série de valores de dados indexado por hora. Objetos de séries de tempo de R são utilizados para criar e gerir o índice de tempo. Existem várias vantagens de utilizar objetos de séries de tempo. Objetos de séries de tempo libertação-lo a partir de muitos detalhes de gerir os valores de índice de séries de tempo que são encapsulados no objeto. Além disso, objetos de séries de tempo permitem-lhe utilizar vários métodos de séries de tempo para representar, imprimir, modelação, etc.

A classe de séries de tempo POSIXct é normalmente utilizada e é relativamente simple. Esta série de tempo de classe tempo medidas desde o início da época, 1 de Janeiro de 1970. Utilizaremos os objetos de séries de tempo POSIXct neste exemplo. Outras classes de objetos de séries de tempo do amplamente utilizados R incluem zoo e xts, série de tempo extensível.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Exemplo de objeto de série de tempo
Vamos começar com o nosso exemplo. Arrastar e largar uma **novo** [executar Script do R] [ execute-r-script] módulo na sua experiência. Ligue a porta de saída do resultado Dataset1 de existente [executar Script do R] [ execute-r-script] porta da nova de entrada do módulo para a Dataset1 [executar Script do R] [ execute-r-script] módulo.

Como para os exemplos primeiro, tal como estamos percorre o exemplo, em alguns pontos posso mostrará apenas as incrementais linhas adicionais de código do R cada passo.  

#### <a name="reading-the-dataframe"></a>Ler a dataframe
Como primeiro passo, vamos lida uma dataframe e certifique-se que obtemos os resultados esperados. O seguinte código deve efetuar a tarefa.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Agora, execute a experimentação. O registo da nova forma de executar o Script do R deverá ser semelhante figura 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Figura 14. Resumo de dataframe no módulo executar Script do R.*

Estes dados tem o formato e tipos previstos. Tenha em atenção que a coluna 'Mês' de fator de tipo e tem o número esperado de níveis.

#### <a name="creating-a-time-series-object"></a>Criação de um objeto de série de tempo
Precisamos de adicionar um objeto de série de tempo ao nosso dataframe. Substitua o código atual com o seguinte, que adiciona uma nova coluna da classe POSIXct.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Agora, verifique o registo. Este deve ter o seguinte aspeto figura 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 15. Resumo de dataframe com um objeto de série de tempo.*

É possível ver de que a nova coluna é na realidade da classe POSIXct resumo.

### <a name="exploring-and-transforming-the-data"></a>Explorar e transformar os dados
Vamos explorar algumas das variáveis este conjunto de dados. Uma matriz de scatterplot é uma boa forma para produzir um aspeto rápido. Estou a substituir o `str()` função com o código do R anterior com a seguinte linha.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Execute este código e ver o que acontece. O desenho produzido, a porta de dispositivo de R deverá ser semelhante figura 16.

![Matriz de Scatterplot das variáveis selecionadas][17]

*Figura 16. Matriz de Scatterplot das variáveis selecionadas.*

Há alguns estrutura odd-looking com as relações entre estas variáveis. Talvez este seja as tendências dos dados e do facto de que não podemos ter padronizado as variáveis.

### <a name="correlation-analysis"></a>Análise de correlação
Para executar uma análise de correlação é necessário anular de tendências e padronizar as variáveis. Podemos utilizar simplesmente o R `scale()` função, o que centros tanto dimensiona variáveis. Esta função também pode executar mais rapidamente. No entanto, pretender mostrar-lhe um exemplo de programing defesas em R.

O `ts.detrend()` função abaixo efetua ambas estas operações. As seguintes duas linhas de código anular os dados de tendências e, em seguida, padronizar os valores.

    ts.detrend <- function(ts, Time, min.length = 3){
      ## Function to de-trend and standardize a time series

      ## Define some messages if they are NULL  
      messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                    'ERROR: ts.detrend requires argument ts to be of type numeric',
                    paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                    'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                    'ERROR: Detrend regression has failed in ts.detrend',
                    'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
      )
      # Create a vector of zeros to return as a default in some cases
      zerovec  <- rep(length(ts), 0.0)

      # The input arguments are not of the same length, return ts and quit
      if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

      # If the ts is not numeric, just return a zero vector and quit
      if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

      # If the ts is too short, just return it and quit
      if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

      ## Check that the Time variable is of class POSIXct
      if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

      ## De-trend the time series by using a linear model
      ts.frame  <- data.frame(ts = ts, Time = Time)
      tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
               error = function(e){warning(messages[5]); zerovec})

      tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
                 ts <- ts/stdev},
                error = function(e){warning(messages[6]); zerovec})

      ts
    }  
    ## Apply the detrend.ts function to the variables of interest
    df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

    ## Plot the results to look at the relationships
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

Não há bastantes acontecer bits no `ts.detrend()` função. Na maioria deste código está a verificar potenciais problemas com os argumentos ou lidar com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas deste código, na verdade, efetue os cálculos.

Um exemplo de programação defesas no foi discutimos já [valor transformações](#valuetransformations). Ambos os blocos de cálculo são moldados numa `tryCatch()`. Para alguns erros faz sentido para devolver o vetor de entrada original e noutros casos, posso devolver um vetor de zeros.  

Tenha em atenção que a regressão linear utilizada para fins de tendência anular uma regressão de séries de tempo. A variável de previsão da receita é um objeto de série de tempo.  

Uma vez `ts.detrend()` está definido é aplicá-la para as variáveis de interesse no nosso dataframe. Iremos tem coerce a lista resultante criada pelo `lapply()` para dados dataframe utilizando `as.data.frame()`. Devido a defesas aspetos de `ts.detrend()`, Falha ao processar um das variáveis não impedirá o processamento correto dos outros.  

A linha de código final cria um scatterplot pairwise. Depois de executar o código do R, os resultados do scatterplot são apresentados na figura 17.

![Scatterplot Pairwise de série de tempo anular trended e padronizado][18]

*Figura 17. Pairwise scatterplot de série de tempo anular trended e padronizado.*

Pode comparar estes resultados às mostrado na figura 16. Com a tendência removido e as variáveis padronizadas, vemos muito menos estrutura em relações entre estas variáveis.

Segue-se o código de correlações como objetos de R ccf de computação.

    ## A function to compute pairwise correlations from a
    ## list of time series value vectors
    pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
      ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
    }

    ## A list of the pairwise indices
    corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

    ## Compute the list of ccf objects
    cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

    cadairycorrelations

Executar este código produz o registo mostrado na figura 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Figura 18. Lista de ccf objetos da análise de correlação pairwise.*

Não há um valor de correlação para cada desfasamento. Nenhum dos seguintes valores de correlação é suficientemente grande para ser significativo. Por conseguinte, pode concluir a que iremos pode modelo cada variável de forma independente.

### <a name="output-a-dataframe"></a>Um dataframe de saída
Podemos ter calculada de correlações pairwise como uma lista de objetos de R ccf. Isto apresenta um bits de um problema, como a porta de saída do conjunto de dados de resultado realmente requer um dataframe. Além disso, o objeto de ccf encontra-se uma lista e queremos apenas os valores do primeiro elemento desta lista, correlações nos lags vários.

O seguinte código extrai os valores do desfasamento da lista de objetos de ccf, que são próprias listas.

    df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

    c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
    r.names  <- c("Corr Cot Cheese - Ice Cream",
                  "Corr Cot Cheese - Milk Prod",
                  "Corr Cot Cheese - Fat Price",
                  "Corr Ice Cream - Mik Prod",
                  "Corr Ice Cream - Fat Price",
                  "Corr Milk Prod - Fat Price")

    ## Build a dataframe with the row names column and the
    ## correlation data frame and assign the column names
    outframe <- cbind(r.names, df.correlations)
    colnames(outframe) <- c.names
    outframe


    ## WARNING!
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

A primeira linha de código é um pouco tricky e algumas explicação pode ajudar a compreendê-lo. Trabalhar a partir de avesso temos o seguinte:

1. O '**[[**'operator com o argumento'**1**' seleciona o vetor de correlações nos lags o primeiro elemento da lista de objetos de ccf.
2. O `do.call()` função aplica-se a `rbind()` função através de elementos da lista devolve por `lapply()`.
3. O `data.frame()` função coerces o resultado produzido pelo `do.call()` para um dataframe.

Tenha em atenção que os nomes de linha numa coluna do dataframe. Se o fizer Sim preserva a linha nomes quando estes são o resultado do [executar Script do R][execute-r-script].

Executar o código produz a saída mostrada na figura 19 quando posso **visualizar** a saída na porta conjunto de dados de resultado. Os nomes de linha são na primeira coluna, conforme pretendido.

![Saída de resultados da análise de correlação][20]

*Figura 19. O resultado da análise de correlação de resultados.*

## <a id="seasonalforecasting"></a>Exemplo de séries de tempo: previsão sazonais
Nossos dados agora são um formato adequado para análise e Determinámos que não existem nenhum correlações significativas entre as variáveis. Vamos avançar e criar uma modelo de previsão de série de tempo. Utilizar este modelo, irá previsão Califórnia milk de produção durante 12 meses de 2013.

Tem dois componentes, um componente de tendência e um componente sazonais nosso modelo de previsão. A previsão de conclusão é o produto estes dois componentes. Este tipo de modelo é conhecido como um modelo multiplicative. A alternativa é um modelo semiaditivas. Iremos já tiver aplicado uma transformação de registo para as variáveis de interesse, o que torna esta análise tractable.

O código do R completado para esta secção é no ficheiro zip que transferiu anteriormente.

### <a name="creating-the-dataframe-for-analysis"></a>Criar dataframe para análise
Comece por adicionar um **novo** [executar Script do R] [ execute-r-script] módulo à sua experimentação. Ligar o **conjunto de dados de resultado** resultado existente [executar Script do R] [ execute-r-script] módulo para a **Dataset1** entrada do módulo novo. O resultado deve ser algo semelhante a figura 20.

![A experiência com o novo módulo executar Script do R adicionado][21]

*Figura 20. A experiência com o novo módulo executar Script do R adicionado.*

Como com a análise de correlação que acabou de concluir, é necessário adicionar uma coluna com um objeto de série de tempo POSIXct. O seguinte código vai fazê-lo imediatamente.

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Execute este código e observe o registo. O resultado deverá ser semelhante 21 figura.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 21. Um resumo do dataframe.*

Com este resultado, iremos estiver prontos para começar a nossa Analysis Services.

### <a name="create-a-training-dataset"></a>Criar um conjunto de dados de formação
Com o dataframe construído temos de criar um conjunto de dados de formação. Estes dados irão incluir todas as observações, exceto as últimas 12 do ano 2013, que é o nosso conjunto de dados de teste. O seguinte código subconjuntos de dataframe e cria rastreia das variáveis de produção e preços dairy. Em seguida, posso criar rastreia de produção de quatro e preços variáveis. Uma função anónima é utilizada para definir algumas aumenta de desenho e, em seguida, iterar sobre a lista dos outros dois argumentos com `Map()`. Se estiver a pensar que um de ciclo seria já trabalhou ajustar aqui, está corretas. No entanto, uma vez que o R é uma linguagem funcional posso estou mostrar uma abordagem funcional.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

Executar o código produz a série de tempo série rastreia da saída do dispositivo de R mostrada na figura 22. Tenha em atenção que o eixo de tempo em unidades de datas, uma vantagem nice da hora de série desenhar método.

![Primeiro rastreia de séries de tempo dos dados de produção e preços dairy no Califórnia](./media/r-quickstart/unnamed-chunk-161.png)

![Segundo de séries de tempo rastreia dos dados de produção e preços dairy no Califórnia](./media/r-quickstart/unnamed-chunk-162.png)

![Terço da série de tempo rastreia dos dados de produção e preços dairy no Califórnia](./media/r-quickstart/unnamed-chunk-163.png)

![Quarto de séries de tempo rastreia dos dados de produção e preços dairy no Califórnia](./media/r-quickstart/unnamed-chunk-164.png)

*Figura 22. Tempo série rastreia de produção dairy Califórnia e dados de preço.*

### <a name="a-trend-model"></a>Um modelo de tendência
Ter criado um objeto de série de tempo e ter tinha ver os dados, vamos começar a construir um modelo de tendência para os dados de produção de milk Califórnia. Podemos fazer isto com um regressão de séries de tempo. No entanto, é claro de desenho que iremos precisar de mais do que um declive e intercetar para modelar com precisão a tendência dos dados de formação observada.

Tendo em conta a pequena escala dos dados, posso irá criar o modelo para a tendência de RStudio e, em seguida, cortar e colar o modelo resultante para o Azure Machine Learning. RStudio fornece um ambiente interativo para este tipo de análises interativas.

Como uma primeira tentativa, irá tentar um regressão polynomial com powers até 3. Não há um risco real destes tipos de modelos de ajuste excessiva. Por conseguinte, é melhor evitar a termos de ordem elevada. O `I()` função inibe interpretação do conteúdo (interpreta os conteúdos 'porque é') e permite-lhe escrever uma função literalmente interpretada numa equação de regressão.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

Isto gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Entre os valores de P (Eli (> | t |)) neste resultado, é possível ver que o termo ao quadrado pode não ser significativo. Utilizarei a `update()` função para modificar este modelo para remover o termo ao quadrado absoluto.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

Isto gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Isto procura uma melhor. Todas as condições são significativas. No entanto, o valor de 2e 16 é um valor predefinido e não deve ser levado muito a sério demasiado.  

Como um teste de sanity, vamos certificar-num desenho de séries de tempo dos dados de produção dairy Califórnia com a curva de tendência mostrada. Posso ter adicionado o seguinte código no Azure Machine Learning [executar Script do R] [ execute-r-script] modelo (não RStudio) para criar o modelo e efetuar um desenho. O resultado é apresentado na figura 23.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Dados de produção milk Califórnia com o modelo de tendência apresentado](./media/r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dados de produção do milk Califórnia com o modelo de tendência apresentado.*

Parece que o modelo de tendência se adequa bastante também os dados. Além disso, não aparentemente, não existe uma prova do ajuste superior, como números ímpares wiggles na curva modelo.  

### <a name="seasonal-model"></a>Modelo sazonais
Com um modelo de tendência da mão, precisamos de push e incluir os efeitos sazonais. Utilizamos o mês do ano como uma variável fictício no modelo de linear para capturar o efeito de mês a mês. Tenha em atenção que quando apresentar variáveis fator para um modelo, o intercept tem não ser calculada. Se não fizer isto, a fórmula é localizado e R irá remover um dos fatores pretendidos, mas manter o termo intercept.

Uma vez que temos um modelo de tendência satisfatório podemos utilizar o `update()` função para adicionar os novos termos para o modelo existente. A -1 na fórmula de atualização ignora o termo intercept. Continuar em RStudio neste momento:

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

Isto gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vemos que o modelo já não tem um termo intercept de colunas e tem 12 fatores de mês significativas. Isto é exatamente o que queremos ver.

Certifiquemo-de outro desenho de séries de tempo dos dados de produção dairy Califórnia para ver quão bem o modelo sazonais está a funcionar. Posso ter adicionado o seguinte código no Azure Machine Learning [executar Script do R] [ execute-r-script] para criar o modelo e efetuar um desenho.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Este código a ser executado no Azure Machine Learning produz o desenho mostrado na figura 24.

![Produção de milk Califórnia com o modelo, incluindo efeitos sazonais](./media/r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produção de milk Califórnia com o modelo, incluindo sazonais efeitos.*

A opção para os dados apresentados na figura 24 é encouraging em vez disso. A tendência e o efeito sazonais (variação mensal) procure razoáveis.

Como outra verificação no nosso modelo, vamos tem uma vista de olhos as residuals. O seguinte código calcula os valores previstos do nosso dois modelos, calcula residuals para o modelo sazonais e, em seguida, rastreia estes residuals para os dados de preparação.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

O desenho residuais é apresentado na figura 25.

![Residuals do modelo sazonais para os dados de formação](./media/r-quickstart/unnamed-chunk-21.png)

*Figura 25. Residuals do modelo sazonais para os dados de preparação.*

Estes residuals parecer razoáveis. Não há nenhuma estrutura específica, exceto o efeito do recession 2008 2009, que o nosso modelo não conta particularmente bem.

O desenho mostrado na figura 25 é útil para detetar padrões qualquer dependentes da hora nos residuals. A abordagem explícita de computação e de desenho residuals utilizei coloca os residuals por ordem de tempo no desenho. Se, por outro lado, posso tinha desenhados `milk.lm$residuals`, não deverá ter sido o desenho por ordem de tempo.

Também pode utilizar `plot.lm()` para produzir uma série de rastreia diagnóstico.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Este código produz uma série de diagnóstico rastreia mostrado na figura 26.

![Primeiro rastreia diagnóstico para o modelo sazonais](./media/r-quickstart/unnamed-chunk-221.png)

![Segundo rastreia diagnóstico para o modelo sazonais](./media/r-quickstart/unnamed-chunk-222.png)

![Terço da rastreia diagnóstico para o modelo sazonais](./media/r-quickstart/unnamed-chunk-223.png)

![Quarto de diagnóstico rastreia para o modelo sazonais](./media/r-quickstart/unnamed-chunk-224.png)

*Figura 26. Diagnóstico rastreia para o modelo sazonais.*

Existem alguns pontos altamente influential identificados neste rastreia, mas nada a fazer com que uma grande preocupação. Além disso, é possível ver partir o desenho Normal Q-Q que o residuals são fechar normalmente distribuída, um pressuposto importante para modelos lineares.

### <a name="forecasting-and-model-evaluation"></a>Previsão e modelo de avaliação
Não há apenas uma coisa mais fazer para concluir o nosso exemplo. Temos de previsões de computação e medir o erro contra os dados reais. A nossa previsão será durante 12 meses de 2013. Iremos pode calcular uma medida de erro para este previsão aos dados reais que não faz parte do nosso conjunto de dados de formação. Além disso, vamos pode comparar o desempenho dos 18 anos de dados de formação para 12 meses de dados de teste.  

Um número de métricas é utilizado para medir o desempenho dos modelos de série de tempo. No nosso caso utilizamos o erro de média quadrática de raiz (RMS). A seguinte função calcula o erro de RMS entre duas séries.  

    RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
      ## Function to compute the RMS error or difference between two
      ## series or vectors

      messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                    "ERROR: Input vector to function RMS.error is too short",
                    "ERROR: Input vectors to function RMS.error must be of same length",
                    "WARNING: Funtion rms.error has received invald input time series.")

      ## Check the arguments
      if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
        warning(messages[1])
        return(NA)}

      if(length(series1) < min.length) {
        warning(messages[2])
        return(NA)}

      if((length(series1) != length(series2))) {
           warning(messages[3])
        return(NA)}

      ## If is.log is TRUE exponentiate the values, else just copy
      if(is.log) {
        tryCatch( {
          temp1 <- exp(series1)
          temp2 <- exp(series2) },
          error = function(e){warning(messages[4]); NA}
        )
      } else {
        temp1 <- series1
        temp2 <- series2
      }

     ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute the RMS error in a dataframe
      tryCatch( {
        sqrt(sum((temp1 - temp2)^2) / length(temp1))},
        error = function(e){warning(messages[4]); NA})
    }

Tal como com a `log.transform()` função discutimos na secção "Transformações de valor", há determinadas uma grande quantidade de código de recuperação de verificação e de exceção de erro nesta função. Estes princípios utilizados são os mesmos. O trabalho é feito em dois locais moldadas numa `tryCatch()`. Em primeiro lugar, a série de tempo é exponentiated, uma vez que estamos com os registos de valores. Segundo, o erro de RMS real é calculado.  

Equipados com uma função para medir o erro de RMS, vamos criar e de saída um dataframe que contém os erros de RMS. Iremos incluirá os termos do modelo de tendência e o modelo completo com fatores sazonais. O seguinte código efetua a tarefa utilizando dois modelos lineares que podemos ter construído.

    ## Compute the RMS error in a dataframe
    ## Include the row names in the first column so they will
    ## appear in the output of the Execute R Script
    RMS.df  <-  data.frame(
    rowNames = c("Trend Model", "Seasonal Model"),
      Traing = c(
      RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
      RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
      Forecast = c(
        RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
        RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
    )
    RMS.df

    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('RMS.df')

Executar este código produz o resultado mostrado na figura 27 na porta de saída de resultado conjunto de dados.

![Comparação de erros de RMS para os modelos][26]

*Figura 27. Comparação de erros de RMS para os modelos.*

Destes resultados, Vemos que os fatores sazonais a adicionar ao modelo reduz o erro de RMS significativamente. Não demasiado surprisingly, o erro do RMS para os dados de preparação é um bit inferior para a previsão.

## <a id="appendixa"></a>Apêndice a: Guia RStudio
RStudio está bastante bem documento, pelo que este anexo consigo fornecerá algumas hiperligações para as secções de chaves da documentação RStudio para ajudar a começar.

1. Crie projetos
   
   Pode organizar e gerir o seu código do R para projetos utilizando RStudio. A documentação que utiliza projetos pode ser encontrada em https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Posso Recomendamos que siga estas instruções e criar um projeto para os exemplos de código do R neste documento.  
2. Editar e executar código do R
   
   RStudio fornece um ambiente integrado para editar e executar código do R. Pode encontrar documentação em https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Depuração
   
   RStudio inclui funcionalidades de depuração poderosas. Documentação para estas funcionalidades está em https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   As funcionalidades de resolução de problemas do ponto de interrupção estão documentadas em https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>Apêndice b: Ler mais
Este tutorial de programação de R abrange as noções básicas do que necessita para utilizar a linguagem de R com o Azure Machine Learning Studio. Se não estiver familiarizado com R, dois introduções estão disponíveis na CRAN:

* R para principiantes por Emmanuel Paradis é um bom local para iniciar o http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* Uma introdução ao R por W. e Venables ter. usados. passa para um pouco maior profundidade, http://cran.r-project.org/doc/manuals/R-intro.html.

Existem muitos books no R que pode ajudar a começar a utilizar. Seguem-se algumas que posso úteis:

* Os elementos de gráficos de programação de R: uma apresentação de análises Software estrutura por Norman Matloff é uma excelente introdução à programação em R.  
* R Cookbook por Paul Teetor fornece uma abordagem de problema e a solução para utilizar o R.  
* R na ação pelo Robert Kabacoff é outro livro introdutórias útil. O Web site do complementar R rápida é um recurso útil em http://www.statmethods.net/.
* R Inferno por Patrick Burns é um livro surprisingly humorous que lida com um número de tricky e difícil tópicos que pode ser encontrado ao programação em R. O livro está disponível gratuitamente em http://www.burns-stat.com/documents/books/the-r-inferno/.
* Se pretender uma descrição profunda em tópicos avançadas R, ter uma vista de olhos o livro avançadas R por Hadley Wickham. A versão online deste livro é disponível gratuitamente em http://adv-r.had.co.nz/.

Um catalogar dos pacotes de séries de tempo de R pode ser encontrada na vista de tarefas de CRAN para análise de séries de tempo: http://cran.r-project.org/web/views/TimeSeries.html. Para obter informações sobre a hora específica pacotes de objeto de série, devem consultar a documentação desse pacote.

O livro introdutórias séries de tempo com R Paul Cowpertwait e Andrew Metcalfe fornece uma introdução à utilização R para análise de séries de tempo. Muitos textos mais teórico fornecem exemplos de R.

Alguns recursos de internet excelente:

* DataCamp: DataCamp informa R no comfort do seu browser com lições vídeos e exercícios codificação. Existem tutoriais interativos na mais recentes técnicas de R e pacotes. Tirar o livre tutorial interativo do R no https://www.datacamp.com/courses/introduction-to-r
* Um guia no Getting começar a utilizar R de Programiz https://www.programiz.com/r-programming
* Um tutorial rápido do R por Cardoso negra de Clarkson University http://www.cyclismo.org/tutorial/R/
* Recursos-R de 60 + listados em http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/r-quickstart/fig1.png
[2]: ./media/r-quickstart/fig2.png
[3]: ./media/r-quickstart/fig3.png
[4]: ./media/r-quickstart/fig4.png
[5]: ./media/r-quickstart/fig5.png
[6]: ./media/r-quickstart/fig6.png
[7]: ./media/r-quickstart/fig7.png
[8]: ./media/r-quickstart/fig8.png
[9]: ./media/r-quickstart/fig9.png
[10]: ./media/r-quickstart/fig10.png
[11]: ./media/r-quickstart/fig11.png
[12]: ./media/r-quickstart/fig12.png
[13]: ./media/r-quickstart/fig13.png
[14]: ./media/r-quickstart/fig14.png
[15]: ./media/r-quickstart/fig15.png
[16]: ./media/r-quickstart/fig16.png
[17]: ./media/r-quickstart/fig17.png
[18]: ./media/r-quickstart/fig18.png
[19]: ./media/r-quickstart/fig19.png
[20]: ./media/r-quickstart/fig20.png
[21]: ./media/r-quickstart/fig21.png
[22]: ./media/r-quickstart/fig22.png

[26]: ./media/r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
