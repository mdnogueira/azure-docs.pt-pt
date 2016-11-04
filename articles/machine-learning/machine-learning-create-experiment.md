---
title: Uma experimentação simples no Machine Learning Studio | Microsoft Docs
description: Este tutorial de machine learning apresenta-lhe uma experimentação de ciência de dados fácil. Vamos prever o preço de um carro através de um algoritmo de regressão.
keywords: experimentação, regressão linear, algoritmos de machine learning, tutorial de machine learning, técnicas de modelação preditiva, experiência de ciência de dados
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/14/2016
ms.author: garye

---
# Tutorial de Machine Learning: crie a sua primeira experimentação de ciência de dados no Azure Machine Learning Studio
Este tutorial de machine learning apresenta-lhe uma experimentação de ciência de dados fácil. Vamos criar um modelo de regressão linear que prevê o preço de um automóvel com base em diferentes variáveis, como a marca e as especificações técnicas. Para fazer isto, iremos utilizar o Azure Machine Learning Studio para desenvolver e repetir uma simples experimentação de análise preditiva.

A *análise preditiva* é um tipo de ciência de dados que utiliza dados atuais para prever futuros resultados. Para obter um exemplo muito simples de análise preditiva, veja Dados de Ciência para Principiantes, vídeo 4: [Prever uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (duração do vídeo: 7:42).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Como é que o Machine Learning Studio ajuda?
O Machine Learning Studio facilita a configuração de uma experimentação através de módulos de arrastar e largar pré-programados com técnicas de modelação preditiva. Para executar a sua experimentação e prever uma resposta, irá utilizar o Machine Learning Studio para *criar um modelo*, *treinar o modelo* e *pontuar e testar o modelo*.

Entre no Machine Learning Studio: [https://studio.azureml.net](https://studio.azureml.net). Se já tem sessão iniciada no Machine Learning Studio, clique em **Iniciar sessão aqui**. Caso contrário, clique em **Inscrever-se** e escolha a opção gratuita ou paga.

Para obter informações gerais sobre o Machine Learning Studio, veja [O que é o Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

## Cinco passos para criar uma experimentação
Neste tutorial do Machine Learning, terá de executar cinco passos básicos para criar uma experimentação no Machine Learning Studio para poder criar, dar formação e pontuar o seu modelo:

* Criar um modelo
  * [Passo 1: Obter os dados]
  * [Passo 2: Processar previamente os dados]
  * [Passo 3: Definir funcionalidades]
* Dar formação sobre o modelo
  * [Passo 4: Escolher e aplicar um algoritmo de aprendizagem]
* Pontuar e testar o modelo
  * [Passo 5: Prever novos preços para automóveis]

[Passo 1: Obter os dados]: #step-1-get-data
[Passo 2: Processar previamente os dados]: #step-2-preprocess-data
[Passo 3: Definir funcionalidades]: #step-3-define-features
[Passo 4: Escolher e aplicar um algoritmo de aprendizagem]: #step-4-choose-and-apply-a-learning-algorithm
[Passo 5: Prever novos preços para automóveis]: #step-5-predict-new-automobile-prices


## Passo 1: Obter os dados
Pode escolher entre vários conjuntos de dados de exemplo incluídos no Machine Learning Studio e pode importar dados a partir de várias origens. Para este exemplo, utilizamos o conjunto de dados de exemplo incluído, os **Dados de preço do automóvel (bruto)**.
Este conjunto de dados inclui entradas de vários automóveis individuais, incluindo informações como a marca, o modelo, as especificações técnicas e o preço.

1. Inicie uma nova experiência, clicando em **+NOVO** na parte inferior da janela Machine Learning Studio, selecione **EXPERIMENTAR** e, em seguida, selecione **Experimentar em branco**. Selecione o nome predefinido da experimentação na parte superior da tela e mude o nome para algo significativo, por exemplo, **Predição do preço automóvel**.
2. À esquerda da tela da experimentação existe uma paleta de conjuntos de dados e módulos. Digite **automóvel** na caixa de pesquisa na parte superior desta paleta para localizar o conjunto de dados denominado **Dados do preço automóvel (bruto)**.
   
    ![Pesquisa na paleta][screen1a]
3. Arraste o conjunto de dados para a tela de experimentação.
   
    ![Conjunto de dados][screen1]

Para ver a aparência destes dados, clique na porta exterior da parte inferior do conjunto de dados do automóvel e depois selecione **Visualizar**.

![Porta de saída do módulo][screen1c]

As variáveis no conjunto de dados aparecem como colunas e cada instância de um automóvel aparece como uma linha. A coluna mais à direita (coluna 26 e com título "preço") é o objetivo variável que vamos tentar prever.

![Visualização do conjunto de dados][screen1b]

Feche a janela de visualização, clicando no "**x**" no canto superior direito.

## Passo 2: Processar previamente os dados
Normalmente, um conjunto de dados requer alguns pré-processamentos antes de poder ser analisado. Poderá ter reparado nos valores em falta presentes nas colunas de várias linhas. Estes valores em falta têm de ser apagados para que o modelo possa analisar os dados corretamente. No nosso caso, iremos remover quaisquer linhas que tenham valores em falta. Além disso, a coluna das **perdas normalizadas** tem uma grande proporção de valores em falta, pelo que recomendamos que exclua totalmente essa coluna do modelo.

> [!TIP]
> Apagar os valores em falta a partir dos dados de entrada é um pré-requisito para utilizar a maioria dos módulos.
> 
> 

Primeiro removeremos a coluna das **perdas normalizados** e, em seguida, removeremos qualquer linha que tenha dados em falta.

1. Digite as **colunas selecionadas** na caixa de pesquisa na parte superior da paleta do módulo para localizar o módulo [Colunas selecionadas no conjunto de dados][selecionar colunas]; em seguida, arraste o módulo para a tela de experimentação e ligue-o à porta de saída do **Dados do preço automóvel (bruto)** conjunto de dados. Este módulo permite-nos selecionar quais as colunas de dados que pretendemos incluir ou excluir no modelo.
2. Selecione o módulo [Colunas selecionadas no conjunto de dados][selecionar colunas] e clique em **Seletor de coluna de iniciação** no painel **Propriedades**.
   
   * À esquerda, clique em **Com regras**
   * Em **Começar Com**, clique em **Todas as colunas**. Isto direciona a opção [Colunas selecionadas no conjunto de dados][selecionar colunas] para percorrer todas as colunas (exceto as que estamos prestes a excluir).
   * Nas listas pendentes, selecione **Excluir** e os **nomes das colunas** e, em seguida, clique dentro da caixa de texto. É apresentada uma lista de colunas. Selecione as **perdas normalizadas** e estas serão adicionadas à caixa de texto.
   * Clique no botão da marca de verificação (OK) para fechar o seletor da coluna.
     
     ![Selecionar colunas][screen3]
     
     O painel de propriedades de **Selecionar colunas no conjunto de dados** indica que este irá percorrer todas as colunas do conjunto de dados, exceto **perdas normalizadas**.
     
     ![Selecionar colunas nas propriedades do conjunto de dados][screen4]
     
     > [!TIP]
     > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Neste caso, faça duplo clique no módulo [Selecionar colunas no conjunto de dados][selecionar colunas] e escreva o comentário "Excluir perdas normalizadas".
     > 
     > 
3. Arraste o módulo [Apagar dados em falta][apagar dados em falta] para a tela de experimentação e conecte-o ao módulo [Selecionar colunas no conjunto de dados][selecionar colunas]. No painel **Propriedades**, selecione **Remover toda a linha** em **Modo de limpeza** para apagar os dados através da remoção das linhas que têm valores em falta. Clique duas vezes no módulo e escreva o comentário "Remover linhas de valor em falta".
   
    ![Apagar as propriedades de dados em falta][screen4a]
4. Execute a experimentação ao clicar em **EXECUTAR** na tela de experimentação.

Quando a experimentação estiver concluída, todos os módulos devem tem uma marca de verificação a verde para indicar que foi concluído com êxito. Repare também no estado **Execução terminada** no canto superior direito.

![Execução da primeira experimentação][screen5]

Tudo o que efetuou na experimentação até este ponto foi apagar os dados. Se pretender ver o conjunto de dados apagado, clique na porta saída à esquerda do módulo [Apagar dados em falta][apagar dados em falta] (“Conjunto de dados apagado”) e selecione **Visualizar**. Repare que a coluna **perdas normalizadas** já não está mais incluída e não existem valores em falta.

Agora que os dados foram apagados, estamos prontos para especificar quais as funcionalidades que vai utilizar no modelo preditivo.

## Passo 3: Definir funcionalidades
No Machine Learning, as *funcionalidades* são propriedades mensuráveis individuais de algo que lhe interessa. No nosso conjunto de dados, cada linha representa um automóvel e cada coluna é uma funcionalidade desse automóvel.

Encontrar um bom conjunto de funcionalidades para criar um modelo preditivo requer experimentação e conhecimentos sobre o problema que pretende resolver. Algumas funcionalidades são melhores para prever num destino do que outras. Além disso, algumas funcionalidades têm uma correlação forte com outras funcionalidades (por exemplo, cidade-mpg versus autoestrada-mpg) para que estas não adicionem muitas informações novas para o modelo e podem ser removidas.

Vamos criar um modelo que utiliza um subconjunto das funcionalidades no nosso conjunto de dados. Pode voltar atrás e selecionar funcionalidades diferentes, executar novamente a experimentação e ver se consegue obter melhores resultados. Contudo, para começar, vamos experimentar as seguintes funcionalidades:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Arraste outro módulo [Selecionar colunas no conjunto de dados][selecionar colunas] para a tela de experimentação e conecte-o à porta de saída à esquerda do módulo [Apagar dados em falta][apagar dados em falta]. Clique duas vezes no módulo e escreva "Selecionar as funcionalidades para predição".
2. Clique no painel **Iniciar seletor de colunas** no painel **Propriedades**.
3. Clique em **Com regras**.
4. Em **Começar Com**, clique em **Sem colunas** e, em seguida, selecione **Incluir** e os **nomes das colunas** na linha de filtro. Introduza a nossa lista de nomes de coluna. Isto direciona o módulo para percorrer as colunas que especificamos.
   
   > [!TIP]
   > Ao executar a experimentação, certificamo-nos de que as definições da coluna relativamente aos nossos dados passam do conjunto de dados através do módulo [Apagar Dados em Falta][apagar dados em falta]. Isto significa que os outros módulos aos quais se ligar também terão informações do conjunto de dados.
   > 
   > 
5. Clique no botão de marca de verificação (OK).

![Selecionar colunas][screen6]

Isto produz o conjunto de dados que vai ser utilizado no algoritmo de aprendizagem nos passos seguintes. Mais tarde, pode voltar e tentar novamente com uma seleção de funcionalidades diferente.

## Passo 4: Escolher e aplicar um algoritmo de aprendizagem
Agora que os dados estão prontos, construir um modelo preditivo consiste em formar e testar. Utilizaremos os nossos dados para preparar o modelo e, em seguida, testar o modelo para o comparar com os preços previstos. Por agora, não se preocupe com o motivo pelo qual é necessário treinar e, em seguida, testar um modelo.

*Classificação* e *regressão* são dois tipos de técnicas supervisionadas do Machine Learning. A classificação prevê uma resposta a partir de um conjunto definido de categorias, tais como uma cor (vermelho, azul ou verde). A regressão é utilizada para prever um número.

Uma vez que queremos prever o preço, que é um número, vamos utilizar um modelo de regressão. Para este exemplo, vamos dar formação sobre um modelo de *regressão linear* e, no próximo passo, vamos testá-lo.

1. Utilizamos os nossos dados de formação e teste ao dividi-los em conjuntos de formação e teste separados. Selecione e arraste o módulo [Dividir dados][dividir] para a tela de experimentação e conecte-o à saída do último módulo [Selecionar colunas no conjunto de dados][selecionar colunas]. Definir a **fração de linhas no primeiro conjunto de dados de saída** para 0,75. Desta forma, vamos utilizar 75% dos dados para preparar o modelo e retenha 25% para fins de teste.
   
   > [!TIP]
   > Ao alterar o parâmetro **Seed aleatório**, pode produzir diferentes amostras aleatórias para formação e testar. Este parâmetro controla a propagação do gerador de número pseudo-aleatório.
   > 
   > 
2. Execute a experimentação. Isto permite que os módulos [Selecionar colunas no conjunto de dados][selecionar colunas] e [Dividir dados][dividir] para passar definições da coluna para os módulos será adicionado em seguida.  
3. Para selecionar o algoritmo do Learning, expanda a categoria **Machine Learning** na paleta do módulo para a esquerda da tela e, em seguida, expanda **Inicializar modelo**. Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos do Machine Learning.
   
    Para esta fase experimental, selecione o módulo [Regressão linear][regressão linear] sob a categoria **Regressão** categoria (também pode encontrar o módulo escrevendo "regressão linear" na caixa de pesquisa de paleta) e arraste-o para a tela de experimentação.
4. Localize e arraste o módulo [Modelo de formação][modelo de formação] para a tela de experimentação. Ligue a porta de entrada à esquerda à saída do módulo [Regressão linear][regressão linear]. Ligue a porta de entrada à direita à saída de dados de formação (porta esquerda) do módulo [Dividir dados][dividir].
5. Selecione o módulo [Modelo de formação][modelo de formação], clique em **Seletor de coluna de iniciação** no painel **Propriedades** e, em seguida, selecione a coluna **Preço**. Este é o valor que o nosso modelo irá prever.
   
    ![Selecione a coluna "preço"][screen7]
6. Execute a experimentação.

O resultado é um modelo de regressão treinado que pode ser utilizado para pontuação amostras novo para tornar as predições.

![Aplicar o algoritmo do Machine Learning][screen8]

## Passo 5: Prever novos preços para automóveis
Agora que experimentámos o modelo, utilizando 75% dos nossos dados, podemos utilizá-lo para pontuar os outros 25% por cento dos dados para ver quão bem funciona o nosso modelo.

1. Localize e arraste o módulo [Modelo de pontuação][modelo de pontuação] para a tela de experimentação e ligue a porta de entrada à esquerda à saída do módulo [Modelo de formação][modelo de formação]. Ligue a porta de entrada à direita à saída de dados de teste (porta direita) do módulo [Dividir dados][dividir].  
   
    ![Módulo do modelo de pontuação][screen8a]
2. Para executar a experimentação e ver o resultado do módulo [Modelo de pontuação][modelo de pontuação], clique na porta de saída e, em seguida, selecione **Visualizar**. O resultado mostra os valores previstos para os preços e os valores conhecidos dos dados do teste.  
3. Finalmente, para testar a qualidade dos resultados, selecione e arraste o módulo [Modelo de avaliação][modelo de avaliação] para a tela de experimentação e ligue a porta de entrada à esquerda à saída do módulo [Modelo de pontuação][modelo de pontuação]. (Existem duas portas de entrada, porque o módulo [Modelo de avaliação][modelo de avaliação] pode ser utilizado para comparar dois modelos.)
4. Execute a experimentação.

Para ver o resultado do módulo [Modelo de avaliação][modelo de avaliação], clique na porta de saída e, em seguida, selecione **Visualizar**. As estatísticas seguintes são apresentadas para o nosso modelo:

* **Média dos erros absolutos** (MAE): A média dos erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
* **Raiz quadrada da média dos erros** (RMSE): A raiz quadrada da média dos erros ao quadrado das predições efetuadas no conjunto de dados de teste.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: Também conhecido como o **valor de R ao quadrado**, esta é uma métrica de estatística que indica o quão bem um modelo é adequado para os dados.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. Um valor mais pequeno indica que as predições mais detalhadas correspondem aos valores reais. No **Coeficiente de determinação**, quanto mais próximo for um valor de outro (1.0), melhores serão as predições.

![Resultados da avaliação][screen9]

A experimentação final deve ter este aspeto:

![Tutorial do Machine Learning: concluir a experimentação de regressão linear que utiliza técnicas de modelação preditiva.][screen10]

## Passos seguintes
Agora que concluiu o primeiro tutorial do Machine Learning e tem a sua experimentação definida, pode voltar a tentar melhorar o modelo. Por exemplo, pode alterar as funcionalidades que utiliza na sua predição. Ou pode modificar as propriedades do algoritmo [Regressão Linear][regressão linear] ou tentar um algoritmo totalmente diferente. Pode ainda adicionar, de uma só vez, vários algoritmos do Machine Learning à sua experimentação e comparar dois utilizando o módulo [Modelo de avaliação][modelo de avaliação].

> [!TIP]
> Utilize o botão **GUARDAR COMO** na tela de experimentação para copiar qualquer iteração da sua experimentação. Pode ver todas as iterações da sua experimentação, clicando em **VER HISTÓRICO DE EXECUÇÕES** por baixo da tela. Para obter mais informações, consulte [Gerir iterações de experimentação no Azure Machine Learning Studio][históricoexecuções].
> 
> 

[históricoexecuções]: machine-learning-manage-experiment-iterations.md

Quando estiver satisfeito com o seu modelo, pode implementá-lo como um serviço web para ser utilizado para prever preços de automóveis através da utilização de novos dados. Consulte o artigo [Implementar um serviço web do Azure Machine Learning][publicar] para obter mais detalhes.

[publicar]: machine-learning-publish-a-machine-learning-web-service.md

Para instruções mais extensas e detalhadas sobre as técnicas de modelação preditiva para criar, formar, pontuar e implementar um modelo, consulte o artigo [Desenvolver uma solução preditiva utilizando o Azure Machine Learning][instruções].

[instruções]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[ecrã1]:./media/machine-learning-create-experiment/screen1.png
[ecrã1a]:./media/machine-learning-create-experiment/screen1a.png
[ecrã1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[ecrã2]:./media/machine-learning-create-experiment/screen2.png
[ecrã3]:./media/machine-learning-create-experiment/screen3.png
[ecrã4]:./media/machine-learning-create-experiment/screen4.png
[ecrã4a]:./media/machine-learning-create-experiment/screen4a.png
[ecrã5]:./media/machine-learning-create-experiment/screen5.png
[ecrã6]:./media/machine-learning-create-experiment/screen6.png
[ecrã7]:./media/machine-learning-create-experiment/screen7.png
[ecrã8]:./media/machine-learning-create-experiment/screen8.png
[ecrã8a]:./media/machine-learning-create-experiment/screen8a.png
[ecrã9]:./media/machine-learning-create-experiment/screen9.png
[ecrã10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[modelo de avaliação]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[regressão linear]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[apagar dados em falta]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[selecionar colunas]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[modelo de pontuação]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[dividir]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[modelo de formação]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



<!--HONumber=Sep16_HO3-->


