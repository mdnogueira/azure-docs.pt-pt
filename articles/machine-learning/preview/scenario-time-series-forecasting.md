---
title: "Série de tempo de pedido de energia previsão | Microsoft Docs"
description: "Como aplicar o machine learning para previsão de série de tempo do pedido de energia no Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: anta
manager: ireiter
editor: anta
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: bd0ddfcffdb6f946f9a3786f3d0add1740be861b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="energy-demand-time-series-forecasting"></a>Série de tempo de pedido de energia previsão


Previsão de série de tempo é a tarefa de prever futuros valores numa sequência ordenada de tempo das observações. Este é um problema comum e tem aplicações em indústrias muitos. Por exemplo, as empresas de revenda necessitem para a previsão vendas produto futura, para que podem organizar eficazmente as cadeias de alimentação para satisfazer o pedido. Da mesma forma, as empresas de entrega de pacote necessitem estimar a necessidade dos respetivos serviços, pelo que podem planear requisitos de força de trabalho e rotas de entrega antecedência. Em muitos casos, os riscos financeiros das previsões incorretas podem ser significativos. Por conseguinte, a previsão, muitas vezes, é uma atividade de crítico de negócio.

Este exemplo mostra como previsão de série de tempo pode ser efetuada através de técnicas do machine learning. Será guiado através de cada passo da modelação do processo, incluindo:
- Preparação de dados para limpar e formatar os dados;
- Criação de funcionalidades para os modelos de machine learning a partir dos dados de séries de tempo em bruto;
- Preparação de modelos vários de machine learning;
- Avaliar os modelos ao comparar o seu desempenho num conjunto de dados fora contido teste; e,
- Operacionalizar o modelo de melhor, tornando-a disponível através de um serviço web para gerar previsões a pedido.

Azure Workbench de aprendizagem máquina aids o processo de modelação em cada passo: 
- O exemplo mostra como pode garantir o ambiente de bloco de notas do Jupyter - disponível diretamente a partir do através do Workbench - desenvolver código Python mais fácil. O processo de desenvolvimento de modelo pode ser explicado a outras pessoas mais claramente utilizando anotações de markdown e gráficos. Estes blocos de notas podem ser visualizados, editar e executados diretamente a partir do Workbench.
- Modelos de formação podem ser persistente e carregados para o blob storage. Isto ajuda a scientist de dados para monitorizar objetos de modelo treinado e certifique-se de que são mantidos e recuperável quando necessário.
- Métricas podem ter sessão iniciadas ao executar um script de Python, ativar o scientist de dados manter um registo de pontuações de desempenho do modelo.
- O workbench produz tabelas personalizáveis de métricas com sessão iniciada, permitindo que o scientist dados comparar facilmente as métricas de desempenho do modelo. Gráficos são apresentados automaticamente para que o melhor desempenho modelo pode ser prontamente identificados.
- Por fim, o exemplo mostra como um modelo preparado pode ser operationalized implementando-a num serviço web em tempo real.

## <a name="link-to-the-gallery-github-repository"></a>Ligação para o repositório do GitHub da Galeria
O repositório do GitHub público para este cenário do mundo real contém todos os materiais, incluindo exemplos de código, necessários para este exemplo:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Descrição geral de cenário de utilização

Este cenário centra-se a pedido de energia em que o objetivo é para prever a carga futura numa grelha de energia de previsão. É uma operação de negócio crítico para empresas no setor de energia como operadores têm de manter o equilíbrio bem entre consumida na grelha de energia e a energia fornecida à mesma. Demasiados energia fornecida à grelha pode resultar nas perdas de energia ou falhas técnicas. No entanto, se for fornecida demasiado pouca energia pode levar a blackouts, deixando os clientes sem energia. Normalmente, operadores de grelha podem tomar decisões de curto prazo para gerir o fornecimento de energia à grelha e manter a carga no equilíbrio. Uma previsão exata de curta duração de procura de energia, por conseguinte, é essencial para o operador para estas decisões com confiança.

Este cenário descreve em detalhe a construção de um de machine learning a pedido de energia solução de previsão. A solução está preparada num conjunto de dados público do [operador de sistema independentes da Nova Iorque (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), que funciona a grelha de energia para o estado da Nova Iorque. O conjunto de dados numa base horária inclui dados de pedido de energia de Nova Iorque cidade durante um período de cinco anos. Um conjunto de dados adicional com condições Meteorologia em Nova Iorque cidade de hora a hora durante o mesmo período de tempo foi retirado do [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Pré-requisitos

- Um [conta do Azure](https://azure.microsoft.com/free/) (gratuitas estão disponíveis).
- Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguintes o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar uma área de trabalho.
- Este exemplo assume que está a executar o Workbench do Azure ML no Windows 10 com [motor de Docker](https://www.docker.com/) instalada localmente. Se estiver a utilizar macOS, as instruções em grande medida são os mesmos.
- Azure Machine Learning Operationalization instalado com um ambiente de implementação de local de multimédia e uma conta de gestão de modelo criada como descrito neste [guia](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md).
- Este exemplo requer que Atualize a instalação de Pandas para versão 0.20.3 ou superior e instalar matplotlib. Clique em *abra a linha de comandos* do *ficheiro* menu no Workbench e execute os seguintes comandos para instalar estas dependências:

    ```
    conda install "pandas>=0.20.3"

    conda install matplotlib
    ```
    
## <a name="create-a-new-workbench-project"></a>Criar um novo projeto do Workbench

Crie um novo projeto com este exemplo como um modelo:
1.  Abra máquina do Azure Learning Workbench
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "De energia a pedido tempo série previsão" e selecione o modelo
5.  Clique em **Criar**.


## <a name="data-description"></a>Descrição de dados

Existem dois conjuntos de dados: `nyc_demand.csv` e `nyc_weather.csv`:

**nyc_demand.csv** hora a hora contém valores de pedido de energia para Nova Iorque cidade de anos 2012 2017. Os dados têm a seguinte estrutura simple:

| timeStamp | pedido |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Valores de pedido são nas megawatt-horas (MWh). Abaixo está um gráfico de procura de energia num período de 7 dias em Julho de 2017:

![Pedido de energia](./media/scenario-time-series-forecasting/energy_demand.png  "a pedido de energia")

**nyc_weather.csv** contém Meteorologia os valores de Nova Iorque cidade de hora a hora ao longo dos anos 2012 2017:

| timeStamp | precip | Temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* é uma medida de percentagem de nível de precipitation. *Temp* valores (temperatura) tem sido rescaled, de modo a que todos os valores enquadram-se no intervalo [0, 100].

## <a name="scenario-structure"></a>Estrutura do cenário

Quando abrir este projeto pela primeira vez no Azure Machine Learning Workbench, navegue para o *ficheiros* painel no lado esquerdo. Os seguintes ficheiros de código são fornecidos com este exemplo:
- `1-data-preparation.ipynb`-Este bloco de notas do Jupyter transfere e processa-os para o preparar para a modelação. Este é o primeiro bloco de notas que irá executar.
- `2-linear-regression.ipynb`-Este bloco de notas trains um modelo de regressão linear nos dados de preparação.
- `3-ridge.ipynb`-trains um modelo de regressão ridge.
- `4-ridge-poly2.ipynb`-trains um modelo de regressão ridge polynomial funcionalidades de nível 2.
- `5-mlp.ipynb`-trains uma rede neuronal perceptron multi camada.
- `6-dtree.ipynb`-trains um modelo de árvore de decisões.
- `7-gbm.ipynb`-trains um modelo de máquina de elevada gradação.
- `8-evaluate-model.py`-Este script carrega um modelo preparado e utiliza-o para fazer predições um conjunto de dados de teste Escalamento contido. Produz o métricas de avaliação do modelo, pelo que pode ser comparado com o desempenho dos modelos diferentes.
- `9-forecast-output-exploration.ipynb`-visualizações de previsões gerados pelos modelos de machine learning produz a este bloco de notas.
- `10-deploy-model.ipynb`-Este bloco de notas demonstra como um modelo de previsão preparado pode ser operationalized num serviço web em tempo real.
- `evaluate-all-models.py`-Este script avalia treinados todos os modelos. Fornece uma alternativa à execução `8-evaluate-model.py` para cada preparado modelo individualmente.

### <a name="1-data-preparation-and-cleaning"></a>1. Preparação de dados e a limpeza

Para iniciar a execução de amostra, primeiro clique no `1-data-preparation.ipynb` para abrir o bloco de notas no modo de pré-visualização. Clique em ***iniciar o bloco de notas do servidor*** para iniciar um servidor de bloco de notas do Jupyter e de kernel do Python no seu computador. Pode executar os blocos de notas do Workbench ou pode utilizar o seu browser, navegando até [http://localhost:8888](http://localhost:8888). Tenha em atenção que tem de alterar o kernel para *PROJECT_NAME local*. Pode fazê-do *Kernel* menu no bloco de notas em *kernel alteração*. Prima ***shift + introduza*** para executar o código em células de bloco de notas individuais, ou clique em *executar todos os* no *célula* menu para executar o bloco de notas completo.

O bloco de notas transfere primeiro os dados a partir de um contentor de armazenamento de BLOBs alojado no Azure. Os dados, em seguida, são armazenados no seu computador no `AZUREML_NATIVE_SHARE_DIRECTORY`. Esta localização é acessível a partir de qualquer blocos de notas ou scripts que executar a partir do Workbench, por isso, é um bom local para armazenar dados e modelos de formação.

Depois dos dados transferidos, o bloco de notas cleans os dados ao preencher lacunas na série de tempo e preencher os valores em falta por interpolação. Limpar os dados de séries de tempo desta forma, maximiza a quantidade de dados disponíveis para os modelos de preparação.

Várias funcionalidades de modelo são criadas a partir de dados não processados apagados. Estas funcionalidades podem ser classificadas em dois grupos:

- **Tempo orientadas por funcionalidades** são derivadas do *timestamp* variável por exemplo, *mês*, *dayofweek* e *hora*.
- **Lagged funcionalidades** são valores de hora desviado dos valores a pedido ou temperatura reais. Estas funcionalidades têm como objetivo para capturar as dependências condicionais entre os períodos de tempo consecutivos no modelo.

O conjunto de dados de funcionalidade resultantes pode ser utilizado quando desenvolver modelos de previsão.

### <a name="2-model-development"></a>2. Desenvolvimento de modelo

Uma abordagem de modelação primeiro pode ser um modelo de regressão linear simples. O `2-linear-regression.ipynb` bloco de notas mostra como preparar um modelo de previsão de regressão linear de pedido de energia futuras. Em particular, o modelo irá ser preparado para prever a pedido de energia uma hora (*t + 1*) antes do período de tempo atual (*t*). No entanto, iremos aplicar este recursivamente de modelo 'um passo' ao tempo de teste para gerar previsões para períodos de tempo futuros, *t + n*.

Para preparar um modelo, um pipeline preditivo é criado que envolve três passos distintos:

- **Uma transformação de dados**: os formatos de necessários para dados de entrada podem variar consoante o algoritmo do machine learning. Neste caso, o modelo de regressão linear requer variáveis categórico para ser frequente de um codificado.
- **A cruzada rotina de validação**:, muitas vezes, um modelo de machine learning tem uma ou mais de sintonização que precisam de ser otimizados através da experimentação. Validação cruzada pode ser utilizada para localizar o conjunto de valores de parâmetros ideal. O modelo repetidamente está preparado e avaliado em diferentes subconjuntos de validação do conjunto de dados. Os melhores parâmetros são aqueles que o modelo o melhor desempenho quando Considerando os subconjuntos de validação de validação cruzada. Este processo é explicado mais detalhadamente na `2-linear-regression.ipynb`.
- **Prepara o modelo final**: O modelo está preparado no conjunto de dados completo, utilizando o conjunto de sintonização melhor.

Incluímos uma série de 6 modelos diferentes em blocos de notas numerado 2-7. Cada bloco de notas trains um modelo diferente e armazena-na `AZUREML_NATIVE_SHARE_DIRECTORY` localização. Depois de ter preparado todos os modelos desenvolvidos para este cenário, iremos pode avaliar e compare-as como decribed na secção seguinte.

### <a name="3-model-evaluation-and-comparison"></a>3. Modelo avaliação e de comparação

Podemos utilizar um modelo preparado para fazer previsões para períodos de tempo futuros. É melhor avaliar destes modelos de um conjunto de dados de teste Escalamento contido. Através da avaliação de modelos diferentes no mesmo conjunto de dados unseen, iremos pode comparar o seu desempenho bastante e identificar o modelo de melhor. Neste cenário, iremos aplicar recursivamente o modelo treinado para prever vários passos de hora no futuro. Em particular, iremos gerar previsões de até seis horas, *t + 6* antes da hora atual, *t*. Estes predições são avaliadas em relação a procura real observada para cada período de tempo.

Para avaliar um modelo, abra o `8-evaluate-model.py` script do *ficheiros* painel no Workbench. Verifique se *configuração de Run* está definido como **local** e, em seguida, escreva o nome de modelo para o *argumentos* campo. O nome do modelo tem de corresponder exatamente a *model_name* variável definida no início do bloco de notas na qual o modelo está preparado. Por exemplo, introduza "linear_regression" para avaliar o modelo de regressão treinado. Em alternativa, depois de tem sido preparados todos os modelos, pode avaliá-los tudo com um comando executando `evaluate-all-models.py`. Para executar este script, abra uma linha de comandos do Workbench e execute o seguinte comando:

```
python evaluate-all-models.py
```
O `8-evaluate-model.py` script efetua as seguintes operações:

- Carrega um pipeline de modelo treinado do disco
- Predições do faz com que o conjunto de dados de teste
- Calcula métricas de desempenho do modelo e regista-las
- Guarda o teste predições do conjunto de dados para `AZUREML_NATIVE_SHARE_DIRECTORY` para posterior inspeção e análise
- Guarda o pipeline de modelo treinado para o *produz* pasta.

> [!Note]
> Guardar o modelo para o *produz* pasta copia automaticamente o objeto de modelo para a conta de armazenamento de Blobs do Azure associada à sua conta de experimentação. Isto significa que poderá sempre ao obter o objeto de modelo guardado a partir do blob, mesmo que altere máquina ou contexto de computação.

Navegue para o *histórico de execuções* painel e clique em `8-evaluate-model.py`. Irá ver gráficos que apresenta várias métricas de desempenho do modelo:

- **Enviar-ME**: significam que o erro de previsão. Isto pode ser interpretado como o bias médio de previsão.
- **MPE**: [significa que a percentagem de erro](https://en.wikipedia.org/wiki/Mean_percentage_error) (-ME expresso como uma percentagem da procura real)
- **MSE**: [significa erros](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: a média de raiz ao quadrado erro (a raiz quadrada de MSE)
- **MAPE**: [significa absoluto percentagem de erro](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [simétrica percentagem média de absoluto erro](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE de uma linha de base previsão no qual a predição é igual ao último valor conhecido a pedido.
- **MdRAE**: erro relativo absoluto mediana. O rácio de mediano do erro de previsão para a linha de base previsão erro. Um valor inferior a 1 significa que a previsão está a funcionar melhor do que a linha de base.

Consulte todas as métricas acima o *t + 1* previsão. Para além das métricas acima, verá também um conjunto de métricas correspondentes com o *_horizon* sufixo. Esta é a média de todos os pontos finais no intervalo de previsão do período de métrica *t + 1* para período *t + 6*.

Se não apresenta métricas na área de gráfico, clique no símbolo de equipamento no canto superior direito. Certifique-se de que as métricas de desempenho do modelo que está interessado estão marcadas. As métricas também serão apresentados na tabela abaixo os gráficos. Novamente, esta tabela é personalizável clicando no equipamento de símbolo. Ordena a tabela por uma métrica de desempenho para identificar o modelo de melhor. Se estiver interessado em ver como o desempenho de previsão varia de período *t + 1* para *t + 6*, clique na entrada para o modelo de tabela. Gráficos apresentar o MAPE, métricas MPE e MdRAE entre o período de previsão são mostradas em *visualizações*.

Quando avaliar os modelos de previsão, pode ser bastante útil visualizar as predições de saída. Isto ajuda a scientist de dados para determinar se a previsão produzido aparece realista. Também pode ajudar a identificar problemas na previsão se, por exemplo, a previsão efetua mal em determinados períodos de tempo. O `9-forecast-output-exploration.ipynb` bloco de notas irá produzir visualizações de previsões gerados para o conjunto de dados de teste.

### <a name="4-deployment"></a>4. Implementação

O modelo de melhor pode operationalized implementando-o como um serviço de web em tempo real. Este serviço web, em seguida, pode ser invocado para gerar previsões a pedido como novos dados ficarem disponíveis. Neste cenário, uma previsão novo tem de ser gerados a cada hora para prever a utilização de energia na hora subsequente. Para efetuar esta tarefa, pode ser um serviço web implementada que utiliza uma matriz de funcionalidades para um determinado período de tempo como entrada de hora e devolve a procura prevista como saída.

Neste exemplo, um serviço web está implementado uma máquina Windows 10. Certifique-se de que concluiu os passos de pré-requisitos para a implementação local definir deste [guia de introdução](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) para a CLI Operationalization. Depois de configurar o ambiente local e a conta de gestão de modelo, execute o `10-deploy-model.ipynb` bloco de notas para implementar o serviço web.

## <a name="conclusion"></a>Conclusão

Este exemplo demonstra como criar uma série de tempo de ponto-a-ponto da solução de previsão para fins de previsão pedido de energia. Muitos dos princípios de explorou neste exemplo podem ser expandidos para outros cenários e indústrias de previsão.

Este cenário mostra como Workbench do Azure Machine Learning podem ajudar um scientist dados desenvolver soluções do mundo real com funcionalidades úteis, tais como o ambiente de bloco de notas do Jupyter e capacidades de métrica de registo. O exemplo orienta também o leitor sobre como um modelo pode ser operationalized e implementado através da CLI do Azure Machine Learning Operationalization. Depois de implementada, a API do serviço web permite aos programadores ou engenheiros de dados integrar o modelo de previsão um vasta pipeline de dados.
