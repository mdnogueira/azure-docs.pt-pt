---
title: "Distribuído de otimização de sintonização utilizando o Azure Machine Learning Workbench | Microsoft Docs"
description: "Este cenário mostra como efetuar a otimização de distribuídas de sintonização utilizando o Azure Machine Learning Workbench"
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.topic: article
ms.author: dmpechyo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 643cea5cc134a2eb25a0dec4abefd9edca726332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Distribuída a otimização de sintonização utilizando o Azure Machine Learning Workbench

Este cenário mostra como utilizar o Azure Machine Learning Workbench para ampliar a otimização de sintonização de algoritmos do machine learning que implementam scikit-saiba API. Vamos mostrar como configurar e utilizar um contentor de Docker remoto e o cluster do Spark como um back-end de execução de otimização de sintonização.

## <a name="link-of-the-gallery-github-repository"></a>Ligação do repositório GitHub da Galeria
Segue-se a ligação para o repositório do GitHub pública: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Descrição geral de cenário de utilização

Muitos algoritmos de machine learning tem uma ou mais botões, denominados de sintonização. Estes botões permitem a otimização de algoritmos para otimizar o seu desempenho sobre dados futuros, medidos de acordo com as métricas de utilizador especificado (por exemplo, exatidão, AUC, RMSE). Scientist de dados tem de fornecer valores de sintonização quando criar um modelo de dados de formação e antes de ver os dados de teste futuras. Com a pode de dados de formação conhecida como base configuramos os valores de sintonização, para que o modelo tem um bom desempenho sobre os dados de teste desconhecido? 

É uma técnica mais popular para otimização de sintonização um *pesquisa de grelha* combinado com *validação cruzada*. Validação cruzada é uma técnica que avalia o quão bem um modelo preparado no conjunto de preparação, prevê através do conjunto de teste. Com esta técnica, inicialmente iremos dividir o conjunto de dados em K subconjuntos de validação e, em seguida, preparar os tempos de algoritmo K em round robin, no, mas um dos subconjuntos de validação, denominados Escalamento contido subconjuntos de validação. Iremos computação o valor médio das métricas de modelos de K de através de subconjuntos de validação do K Escalamento contido. Este valor médio, denominado *estimativa de desempenho entre validados*, depende os valores de sintonização utilizado ao criar modelos de K. Quando a otimização de sintonização, iremos procurar o espaço de valores de hyperparameter candidato para localizar a estimar aqueles que otimizar o desempenho de validação cruzada. Pesquisa de grelha é uma técnica de pesquisa comuns, onde o espaço de valores de candidatos de sintonização vários é um produto cruzado de conjuntos de valores de candidatos de sintonização individuais. 

Pesquisa de grelha utilizando a validação cruzada pode ser morosa. Se um algoritmo tem de 5 sintonização, cada um com 5 valores candidato e utilizamos subconjuntos de validação K = 5, em seguida, para concluir uma pesquisa de grelha temos de formação 5<sup>6</sup>= 15625 modelos. Felizmente, pesquisa de grelha a utilização de validação cruzada é um procedimento constrangedoramente paralelo e todos os estes modelos podem ser preparados em paralelo.

## <a name="prerequisites"></a>Pré-requisitos

* Um [conta do Azure](https://azure.microsoft.com/free/) (gratuitas estão disponíveis).
* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguintes o [instalar e criar o guia de introdução](./quickstart-installation.md) para instalar o Workbench e criar contas.
* Este cenário pressupõe que está a executar do Azure ML Workbench no Windows 10 ou MacOS com o motor de Docker instalada localmente. 
* Para executar o cenário com um contentor de Docker remoto, aprovisionar a Máquina Virtual de ciência de dados do Ubuntu (DSVM), seguindo o [instruções](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-provision-vm). Recomendamos que utilize uma máquina virtual pelo menos 8 núcleos e 28 Gb de memória. D4 instâncias de máquinas virtuais têm essa capacidade. 
* Para executar este cenário com um cluster do Spark, aprovisionar clusters do HDInsight ao seguir o [instruções](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Recomenda-se ter um cluster com o mínimo de seis nós de trabalho e pelo menos 8 núcleos e 28 Gb de memória em nós de cabeçalho e de trabalho. D4 instâncias de máquinas virtuais têm essa capacidade. Para maximizar o desempenho do cluster, recomendamos para alterar os parâmetros spark.executor.instances, spark.executor.cores e spark.executor.memory seguindo o [instruções](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-resource-manager) e editar as definições em "personalizado secção de spark predefinições".

     **Resolução de problemas**: subscrição do Azure o pode ter uma quota no número de núcleos que podem ser utilizadas. O portal do Azure permite a criação do cluster com o número total de núcleos exceder a quota. Para localizar a quota, aceda no portal do Azure para a secção de subscrições, clique na subscrição utilizada para implementar um cluster e, em seguida, clique em **utilização + quotas**. Normalmente, as quotas são definidas por região do Azure e pode optar por implementar o cluster do Spark numa região onde tem suficiente núcleos livres. 

* Crie uma conta de armazenamento do Azure que é utilizada para armazenar o conjunto de dados. Siga o [instruções](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account) para criar uma conta de armazenamento.

## <a name="data-description"></a>Descrição de dados

Utilizamos [TalkingData dataset](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Este conjunto de dados tem eventos das aplicações em telemóveis. O objetivo é para prever sexo e idade categoria de utilizador de telemóvel especificado o tipo de telefone e os eventos que o utilizador gerado recentemente.  

## <a name="scenario-structure"></a>Estrutura do cenário
Este cenário tem várias pastas no repositório do GitHub. Ficheiros de código e a configuração estão em **código** pasta, toda a documentação está ser **Docs** pasta e todas as imagens são **imagens** pasta. A pasta de raiz tem o ficheiro Leia-me que contém um breve resumo deste cenário.

### <a name="getting-started"></a>Introdução
Clique no ícone do Workbench do Azure Machine Learning para executar o Workbench do Azure Machine Learning e criar um projeto do modelo "Distribuído de otimização de sintonização". Pode encontrar instruções detalhadas sobre como criar um novo projeto na [instalar e criar o guia de introdução](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Configuração de ambientes de execução
Vamos mostrar como executar o nosso código num contentor Docker remoto e de um cluster do Spark. Iremos começar com a descrição das definições que são comuns a ambos os ambientes. 

Utilizamos [scikit-Saiba](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), e [storage do azure](https://pypi.python.org/pypi/azure-storage) pacotes que não são fornecidos no contentor de Docker predefinido do Azure Machine Learning Workbench. pacote de armazenamento do Azure requer a instalação da [criptografia](https://pypi.python.org/pypi/cryptography) e [azure](https://pypi.python.org/pypi/azure) pacotes. Para instalar estes pacotes no contentor do Docker e em nós de cluster do Spark, iremos modificar o ficheiro de conda_dependencies.yml:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

A modificação conda\_dependencies.yml ficheiro é armazenado no diretório de aml_config do tutorial. 

Nos passos, ligar o ambiente de execução à conta do Azure. Janela de linha de comandos aberta (CLI) ao clicar em menu de ficheiro no canto superior esquerdo do AML Workbench e escolher "Linha de comandos aberta." Em seguida, execute na CLI

    az login

Receber uma mensagem

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Vá para esta página web, introduza o código e iniciar sessão na sua conta do Azure. Após este passo, execute na CLI

    az account list -o table

e localize a subscrição de subscrição de ID do Azure com a sua conta da área de trabalho do AML Workbench. Por fim, execute na CLI

    az account set -s <subscription ID>

para concluir a ligação à sua subscrição do Azure.

Nas dois secções mostramos como concluir a configuração de docker remoto e o cluster do Spark.

#### <a name="configuration-of-remote-docker-container"></a>Configuração do contentor de Docker remoto

 Para configurar um contentor de Docker remoto, execute na CLI

    az ml computetarget attach --name dsvm --address <IP address> --username <username> --password <password> --type remotedocker

com o IP endereço, nome de utilizador e palavra-passe no DSVM. Endereço IP do DSVM pode ser encontrado na secção Descrição geral da sua página DSVM no portal do Azure:

![IP DA VM](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Configuração de cluster do Spark

Para configurar o ambiente do Spark, execute na CLI

    az ml computetarget attach --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> --type cluster

com o nome do cluster, o nome de utilizador do SSH do cluster e palavra-passe. O valor predefinido do nome de utilizador do SSH é `sshuser`, a menos que o alterado durante o aprovisionamento do cluster. O nome do cluster pode ser encontrado na secção de propriedades da sua página de cluster no portal do Azure:

![Nome do cluster](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Utilizamos o pacote de spark sklearn ter Spark como um ambiente de execução para a otimização de distribuídas de sintonização. Iremos modificou o ficheiro de spark_dependencies.yml para instalar este pacote quando é utilizado o ambiente de execução do Spark:

    configuration: {}
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

O spark modificado\_dependencies.yml ficheiro é armazenado no diretório de aml_config do tutorial. 

### <a name="data-ingestion"></a>Ingestão de dados
O código neste cenário pressupõe que os dados são armazenados no blob storage do Azure. Iremos mostrar inicialmente como transferir dados do Kaggle site para o seu computador e carregá-la para o armazenamento de Blobs. Em seguida, mostramos como ler os dados a partir do blob storage. 

Para transferir dados de Kaggle, visite [página do conjunto de dados](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) e clique no botão de transferência. Será pedido para iniciar sessão no Kaggle. Após iniciar sessão, será redirecionado novamente para a página de conjunto de dados. Em seguida, transferir os ficheiros primeiro sete na coluna esquerda, selecionando-los e clicando no botão de transferência. O tamanho total dos ficheiros transferidos é 289 Mb. Para carregar estes ficheiros para o armazenamento de BLOBs, crie contentor de blob storage 'conjunto de dados' na sua conta de armazenamento. Pode fazê-lo acedendo à página do Azure da sua conta de armazenamento, clicar em Blobs e, em seguida, clicando em + contentor. Introduza 'conjunto de dados' como nome e clique em OK. As capturas de ecrã seguintes ilustram estes passos:

![Abra blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![contentor aberta](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Depois disso selecione o contentor de conjunto de dados da lista e clique no botão de carregamento. Portal do Azure permite carregar em simultâneo vários ficheiros. Na secção "Carregar blob", clique no botão de pasta, selecione todos os ficheiros do conjunto de dados, clique em abrir e, em seguida, clique em carregamento. Captura de ecrã abaixo ilustra estes passos:

![Carregar blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Carregamento dos ficheiros demora vários minutos, consoante a ligação à Internet. 

No nosso código, utilizamos [SDK de armazenamento do Azure](https://azure-storage.readthedocs.io/en/latest/) para transferir o conjunto de dados do armazenamento de BLOBs para o ambiente de execução atual. A transferência é efetuada na carga\_função data () do ficheiro de load_data.py. Para utilizar este código, é necessário substituir < nome_conta > e < ACCOUNT_KEY > com o nome e a chave primária da conta de armazenamento que aloja o conjunto de dados. Nome da conta é apresentado no canto superior esquerdo da página do Azure da sua conta do storage. Para obter a conta de chaves de acesso da chave, selecione na página do Azure de armazenamento (consulte a captura de ecrã primeiro na secção de ingestão de dados) da conta e, em seguida, copie a cadeia de comprimento na primeira linha da coluna chave:
 
![Chave de acesso](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

O seguinte código da função de load_data() transfere um ficheiro único:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Tenha em atenção que não tem de executar load_data.py ficheiro manualmente. Mais tarde será chamado a partir de outros ficheiros.

### <a name="feature-engineering"></a>Com engenharia
O código para todas as funcionalidades de computação é na funcionalidade\_engineering.py ficheiro. Não é necessário executar o ficheiro de feature_engineering.py manualmente. Mais tarde será chamado a partir de outros ficheiros.

Iremos criar vários conjuntos de funcionalidades:
* Acesso frequente uma codificação de marca e modelo do telemóvel (um\_frequente\_brand_model função)
* Fração de eventos gerados pelo utilizador em cada dia da semana (dia da semana\_hour_features função)
* Fração de eventos gerados pelo utilizador em cada hora (dia da semana\_hour_features função)
* Fração de eventos gerados pelo utilizador em cada combinação de dia da semana e hora (dia da semana\_hour_features função)
* Fração de eventos gerados pelo utilizador em cada aplicação (um\_frequente\_app_labels função)
* Fração de eventos gerados pelo utilizador em cada etiqueta da aplicação (um\_frequente\_app_labels função)
* Fração de eventos gerados pelo utilizador em cada categoria de aplicação (texto\_category_features função)
* Funcionalidades de indicador de categorias de aplicações que foram utilizadas por utilizado para gerar eventos (um\_hot_category função)

Estas funcionalidades foram inspired pelo Kaggle kernel [um modelo linear em aplicações e as etiquetas](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

O cálculo destas funcionalidades requer uma quantidade significativa de memória. Inicialmente tentar computação funcionalidades no ambiente local com 16 Gb de RAM. Iremos foram capazes de calcular os primeiras quatro conjuntos de funcionalidades, mas foi recebido o erro 'fora de memória' quando o conjunto de funcionalidades quinto de computação. O cálculo dos conjuntos de quatro primeiras funcionalidade é no ficheiro singleVMsmall.py e pode ser executado no ambiente local através da execução 

     az ml experiment submit -c local .\singleVMsmall.py   

na janela do CLI.

Uma vez que o ambiente local é demasiado pequeno para computação que todos os conjuntos de funcionalidades, iremos mudar para DSVM remoto que tenha memória maior. A execução dentro DSVM é feita no interior do contentor de Docker que é gerido pelo AML Workbench. Com esta DSVM conseguem computação todas as funcionalidades e modelos de formação e otimizar de sintonização (consulte a secção seguinte). o ficheiro de singleVM.py tem a funcionalidade completa de cálculo e código de modelação. Na secção seguinte, mostramos como executar singleVM.py no DSVM remoto. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Otimização de sintonização utilizando DSVM remoto
Utilizamos [xgboost](https://anaconda.org/conda-forge/xgboost) implementação [1] de aumento de gradação da árvore. Utilizamos [scikit-Saiba](http://scikit-learn.org/) pacote para otimizar xgboost de sintonização. Embora xgboost não faz parte de scikit-saiba pacote, implementa scikit-saiba API e, por conseguinte, podem ser utilizadas em conjunto com as funções do scikit de otimização de hyperparameter-saber mais. 

Xgboost tem de oito sintonização:
* n_esitmators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* objetivo uma descrição de sintonização estes pode ser encontrada [aqui](http://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.sklearn) e [aqui](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md). Inicialmente, utilize DSVM remoto e otimizar a partir de uma pequena grelha dos valores de candidatos de sintonização:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objetivo': ['multi:softprob'] 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0,1], 'colsample_bylevel': [0,1,], 'subsample': [0,5]}]  

Esta grelha tem quatro combinações de valores de sintonização. Utilizamos 5-fold validação cruzada, 4, 5 = 20 do resultante é executado de xgboost. Para medir o desempenho dos modelos, utilizamos de métrica de perda de registo negativo. O código seguinte localiza os valores de sintonização da grelha que maximizar a perda de validados entre registo negativo. O código também utiliza estes valores para preparar o modelo final ao longo do conjunto de preparação completa:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Depois de criar o modelo, iremos guardar os resultados da otimização de hyperparameter. Utilizamos o registo de API do Workbench AML para guardar os valores de sintonização melhores e correspondente estimativa validados cruzada de perda de registo negativo:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Também podemos criar o ficheiro de sweeping_results.txt com perdas entre validados registo negativo de todas as combinações de valores de hyperparameter na grelha:

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Este ficheiro é armazenado numa oferta especial. / produz o diretório. Mais tarde mostramos como transferi-lo.  

 Antes de executar singleVM.py no DSVM remoto pela primeira vez, iremos criar um contentor de Docker existe executando 

    az ml experiment prepare -c dsvm

no CLI windows. Contentor de criação de Docker demora vários minutos. Depois disso executamos singleVM.py no DSVM:

    az ml experiment submit -c dsvm .\singleVM.py

Este comando terminar em 1 hora de 38 minutos quando DSVM tem 8 núcleos e 28 Gb de memória. Os valores com sessão iniciada podem ser visualizados na janela do histórico de execuções do AML Workbench:

![histórico de execução](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Por predefinição janela do histórico de execuções mostra os valores e gráficos do primeiro valores registados 1-2. Para obter uma lista completa dos valores escolhidos de sintonização, clique no ícone definições marcado com círculo vermelho na captura de ecrã anterior e selecione sintonização para ser mostrada na tabela. Além disso, para selecionar os gráficos que são apresentados na parte superior da janela do histórico de execução, clique no ícone de definição marcado com círculo azul e selecione os gráficos da lista. 

Os valores escolhidos de sintonização também podem ser examinados na janela de propriedades de execução: 

![Propriedades de execução](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

No canto superior direito da janela de propriedades executar há uma secção de ficheiros de saída com a lista de todos os ficheiros que foram criados no '. \output' pasta no ambiente de execução. varrimento\_results.txt pode ser transferido a partir daí, selecionando-e clicar no botão de transferência. sweeping_results.txt devem ter o seguinte resultado:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Otimização de sintonização utilizando o cluster do Spark
Utilizamos o cluster do Spark para ampliar a otimização de sintonização e utilize a grelha maior. É a nossa nova grelha

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Esta grelha tem 16 combinações de valores de sintonização. Uma vez que utilizamos 5-fold validação cruzada, iremos executar xgboost 16, 5 = 80 vezes.

scikit-saiba pacote não tem um suporte nativo de otimização de sintonização utilizando o cluster do Spark. Felizmente, [spark sklearn](https://spark-packages.org/package/databricks/spark-sklearn) pacote a partir da Databricks preenche neste intervalo. Este pacote fornece GridSearchCV função que tenha quase a mesma API como função GridSearchCV no scikit-saber mais. Para utilizar o spark sklearn e otimizar a utilização do Spark de sintonização é necessário ligar ao criar o contexto do Spark

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Em seguida, iremos substituir 

    from sklearn.model_selection import GridSearchCV

com o 

    from spark_sklearn import GridSearchCV

Substitua, também podemos chamar GridSearchCV de scikit-saiba do spark sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

É o código final para otimizar a utilização do Spark de regulação em distribuídas\_sweep.py ficheiro. É a diferença entre singleVM.py e distributed_sweep.py na definição da grelha e linhas de quatro adicionais de código. Repare também que devido a serviços AML Workbench, o código de registo não altera a alterar o ambiente de execução de DSVM remota para um cluster do Spark.

Antes de executar distributed_sweep.py no cluster do Spark pela primeira vez, é necessário instalar pacotes de Python não existe. Isto pode ser conseguido através da execução 

    az ml experiment prepare -c spark

no CLI windows. Esta instalação demora vários minutos. Depois disso executamos distributed_sweep.py no cluster do Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

Este comando terminar em 1 hora 6 minutos quando o cluster do Spark tem 6 nós de trabalho com 28 Gb de memória. Os resultados da otimização de sintonização no cluster do Spark, nomeadamente os registos, melhor valores do ficheiro de sintonização e sweeping_results.txt, podem ser acedidos no Azure Machine Learning Workbench da mesma forma que a execução de DSVM remota. 

### <a name="architecture-diagram"></a>Diagrama da arquitetura

O diagrama seguinte mostra o fluxo de trabalho ponto a ponto: ![arquitetura](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Conclusão 

Neste cenário, iremos mostrou como utilizar o Azure Machine Learning Workbench para efetuar a otimização de hyperparameter na máquina de virtual remota e no cluster do Spark remoto. Vimos que o Workbench do Azure Machine Learning fornece ferramentas para configuração fácil de ambientes de execução e mudar entre eles. 

## <a name="references"></a>Referências

[1] T. Chen e Guestrin C.. [XGBoost: Árvore dimensionável os aumentos sistema](https://arxiv.org/abs/1603.02754). KDD 2016.




