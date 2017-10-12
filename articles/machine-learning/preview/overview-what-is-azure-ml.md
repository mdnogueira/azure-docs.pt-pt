---
title: "O que é o Azure Machine Learning? | Microsoft Docs"
description: "Descrição geral da Experimentação e da Gestão de Modelos do Azure Machine , uma solução de ciência de dados completa e integrada para cientistas de dados profissionais, que lhes permite desenvolver, experimentar e implementar aplicações de análise avançada à escala da cloud."
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 3bf9227a7ee432f036c57dd9d1c3807c7a867f3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

O Azure Machine Learning é uma solução de análise avançada e de ciência de dados integrada e completa. Permite que os cientistas de dados prepararem dados, desenvolvam experimentações e implementem modelos à escala da cloud.

Os componentes principais do Azure Machine Learning são:
- Azure Machine Learning Workbench
- Serviço de Experimentação do Azure Machine Learning
- Serviço de Gestão de Modelos do Azure Machine Learning
- Bibliotecas do Microsoft Machine Learning para Apache Spark (biblioteca MMLSpark)
- Visual Studio Code Tools para IA

Em conjunto, estes serviços e aplicações ajudam a acelerar significativamente o desenvolvimento e a implementação do seu projeto de ciência de dados. 

![Conceitos do Azure Machine Learning](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>Compatível com código aberto

O Azure Machine Learning suporta totalmente as tecnologias de código aberto. Pode utilizar dezenas de milhares de pacotes Python de código aberto, como as arquiteturas de machine learning seguintes:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Pode executar as experimentações em ambientes geridos, como contentores do Docker e clusters do Spark. Também pode utilizar hardware avançado, como [máquinas virtuais ativadas para GPU no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu), para acelerar a execução.

O Azure Machine Learning foi concebido sobre as tecnologias de código aberto seguintes:

- [Bloco de Notas do Jupyter](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Também inclui as tecnologias de código aberto da própria Microsoft, tais como a [Biblioteca do Microsoft Machine Learning para Apache Spark](https://github.com/Azure/mmlspark) e o Cognitive Toolkit.

Além disso, também pode tirar partido de algumas das mais avançadas e comprovadas tecnologias de machine learning desenvolvidas pela Microsoft para resolver problemas em grande escala. Estas tecnologias são testadas em muitos produtos Microsoft, entre os quais:

- Windows
- Bing
- Xbox
- Office
- SQL Server

As tecnologias de machine learning da Microsoft abaixo são algumas das que estão incluídas no Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (PrOgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
O Azure Machine Learning Workbench é uma aplicação de computador, que inclui ferramentas de linha de comandos, suportada em Windows e em macOS. Permite-lhe gerir soluções de machine learning ao longo de todo o ciclo de vida da ciência de dados:

- Ingestão e preparação de dados
- Desenvolvimento de modelos e gestão de experimentações
- Implementação de modelos em vários ambientes de destino

Seguem-se as principais funcionalidades que o Azure Machine Learning Workbench oferece:

- Ferramenta de preparação de dados , que pode aprender a lógica da transformação de dados através de exemplos.
- Abstração de origens de dados, acessível através de UX e código Python.
- SDK Python para invocar pacotes de preparação de dados construídos visualmente.
- Serviço Jupyter Notebook incorporado e UX cliente.
- Monitorização e gestão de experimentações mediante vistas de histórico de execuções.
- Controlo de acesso baseado em funções que permite a partilha e a colaboração através do Azure Active Directory.
- Instantâneos de projetos automáticos para cada execução e controlo de versões explícito, permitido pela integração de Git nativa. 
- Integração com IDEs de Python populares.

Para obter mais informações, veja os artigos seguintes:
- [Data Preparation User Guide](data-prep-user-guide.md) (Guia de Utilizador da Preparação de Dados)
- [Using Git with Azure Machine Learning](using-git-ml-project.md) (Utilizar o Git com o Azure Machine Learning)
- [Using Jupyter Notebook in Azure Machine Learning](how-to-use-jupyter-notebooks.md) (Utilizar o Jupyter Notebook no Azure Machine Learning)
- Roaming and Sharing (Roaming e Partilha)
- [Run History Guide](how-to-use-run-history-model-metrics.md) (Guia do Histórico de Execuções)
- [IDE Integration](how-to-configure-your-IDE.md) (Integração de IDEs)

## <a name="azure-machine-learning-experimentation-service"></a>Serviço de Experimentação do Azure Machine Learning
O Serviço de Experimentação processa a execução de experimentações de machine learning. Também suporta o Workbench, ao proporcionar gestão de projetos, integração do Git, controlo de acesso, roaming e partilha. 

Através de uma configuração fácil, pode executar as suas experiências numa gama de opções de ambientes de computação:

- Nativo local
- Contentor do Docker local
- Contentor do Docker numa VM remota
- Cluster do Spark no Azure de aumento horizontal

O Serviço de Experimentação constrói ambientes virtuais para garantir que o seu script pode ser executado em isolamento com resultados reproduzíveis. Regista as informações do histórico de execuções e apresenta-o visualmente. Pode facilmente selecionar o melhor modelo de entre as execuções da experimentação. 

Para obter mais informações, veja [Experimentation Execution Configuration](experiment-execution-configuration.md) (Configuração da Execução de Experimentações).

## <a name="azure-machine-learning-model-management-service"></a>Serviço de Gestão de Modelos do Azure Machine Learning

O Serviço de Gestão de Modelos permite aos cientistas de dados e às equipas de dev-ops implementar modelos preditivos numa grande variedade de ambientes. As versões e a linhagem dos modelos são monitorizados das execuções de preparação às implementações. Os modelos são armazenados, registados e geridos na cloud. 

Ao utilizar comandos da CLI simples, pode contentorizar o seu modelo, os scripts de classificação e as dependências em imagens do Docker. Estas imagens são registadas no seu próprio registo do Docker alojado no Azure (Azure Container Registry). Podem ser implementadas de forma fiável nos destinos seguintes:

- Máquinas locais
- Servidores no local
- Cloud
- Dispositivos IoT de periferia

É utilizado o Kubernetes em execução no Azure Container Service (ACS) para a implementação de aumento horizontal na cloud. A telemetria da execução do modelo é capturada no AppInsights, para análise visual. 

Para obter mais informações sobre o Serviço de Gestão de Modelos, veja [Model Management Overview](model-management-overview.md) (Descrição Geral do Modelo de Gestão).


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Biblioteca do Microsoft Machine Learning para Apache Spark

A [MMLSpark](https://github.com/Azure/mmlspark)(Biblioteca do Microsoft Machine Learning para Apache Spark) é um pacote Spark de código aberto que disponibiliza aprendizagem profunda e ferramentas de ciência de dados para Apache Spark. Integra o [Machine Learning Pipelines do Spark](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) no [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) e na biblioteca [OpenCV](http://opencv.org/). Permite-lhe criar rapidamente modelos preditivos e analíticos poderosos e altamente dimensionáveis para conjuntos de imagens e textos de grandes dimensões. Alguns destaques incluem:

- Ingestão de imagens fácil do HDFS no Spark DataFrame
- Pré-processamento de dados de imagens através de transformações a partir de OpenCV
- Caracterização de imagens mediante a utilização de redes neurais profundas pré-preparadas com o Microsoft Cognitive Toolkit 
- Utilização de LSTMs bidirecionais pré-preparadas a partir da Keras para extração de entidades médicas
- Preparação de modelos de classificação de imagens baseadas em DNN em VMs GPU da Série N no Azure
- Caracterização de dados de textos livres mediante a utilização de APIs práticas com base em primitivos no SparkML através de um único transformador
- Preparação fácil de modelos de classificação e regressão mediante a caracterização implícita de dados
- Calcular um conjunto rico de métricas de avaliação, incluindo métricas por instância individual

Para obter mais informações, veja [Using MMLSpark in Azure Machine Learning](how-to-use-mmlspark.md) (Utilizar o MMLSspark no Azure Machine Learning)

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools para IA
As Ferramentas do Visual Studio Code para AI são uma extensão do Visual Studio Code para criar, testar e implementar soluções de Aprendizagem Profunda e AI. Conta com muitos pontos de integração no Azure Machine Learning, incluindo:
- Uma vista do histórico de execuções que apresenta o desempenho das execuções de preparação e as métricas registadas.
- Uma vista de galeria, que permite aos utilizadores procurar e iniciar projetos novos com o Microsoft Cognitive Toolkit, o TensorFlow e muitas outras arquiteturas de aprendizagem profunda. 
- Uma vista de explorador para selecionar destinos de computação para os seus scripts executarem.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Quais são as opções de machine learning da Microsoft?
Para além do Azure Machine Learning, existe uma ampla gama de opções no Azure para criar, implementar e gerir modelos de machine learning. 
* Microsoft Machine Learning Services no SQL Server
* Microsoft Machine Learning Server
* Máquina Virtual de Ciência de Dados
* Spark MLLib no HDInsight
* Serviço Batch AI Training
* Microsoft Cognitive Toolkit
* Serviços Cognitivos da Microsoft


### <a name="microsoft-machine-learning-services-in-sql-server"></a>Microsoft Machine Learning Services no SQL Server
O [Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) permitem-lhe executar, preparar e implementar modelos de machine learning com R ou Python. Pode utilizar os dados localizados no local e em bases de dados do SQL Server. 

Utilize o Microsoft Machine Learning Services quando tiver de preparar ou implementar modelos no local ou no Microsoft SQL Server. Os modelos criados com o Machine Learning Services podem ser implementados com a Gestão de Modelos do Azure Machine Learning. 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
O [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) é um servidor empresarial para alojar e gerir cargas de trabalho paralelas e distribuídas de processos de R e Python. O Microsoft Machine Learning Server funciona em Linux, Windows, Hadoop e Apache Spark. Também está disponível no [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Fornece um motor de execução para as soluções que utilizam [pacotes do Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) e expande o R e o Python de código aberto com suporte para os cenários seguintes:

- análises de elevado desempenho
- análises estatísticas
- machine learning
- conjuntos de dados extremamente grandes

Os pacotes proprietários, que se instalam com o servidor, constituem uma funcionalidade de valor acrescentado. Para desenvolvimento, pode utilizar IDEs como o [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) e o [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Utilize o Microsoft Machine Learning Server quando precisar de:

- Criar e implementar modelos criados com R e Python num servidor
- Distribuir preparações em R e Python em escala num cluster do Hadoop ou do Spark

### <a name="data-science-virtual-machine"></a>Máquina Virtual de Ciência de Dados
A [Máquina Virtual de Ciência de Dados (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) é uma imagem de VM personalizada na cloud do Microsoft Azure, concebida especificamente para fazer ciência de dados. Tem muitas ferramentas populares de ciência de dados e outras pré-instaladas e pré-configuradas para permitir a rápida criação de aplicações inteligentes para análises avançadas. Está disponível no Windows Server e no Linux. Oferecemos a edição Windows da DSVM no Server 2016 e no Server 2012. Oferecemos a edição Linux da DSVM no Ubuntu 16.04 LTS e em distribuições do Linux baseadas no OpenLogic 7.2 CentOS. 

Utilize a Máquina Virtual de Ciência de Dados quando precisar de executar ou alojar os seus trabalhos num único nó. Ou se tiver de aumentar verticalmente de forma remota o processamento numa máquina individual. A Máquina Virtual de Ciência de Dados é suportada como destino para a Experimentação do Azure Machine Learning e a Gestão de Modelos do Azure Machine Learning. 

### <a name="spark-mllib-in-hdinsight"></a>Spark MLLib no HDInsight
O [Spark MLLib no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) permite-lhe criar modelos como parte de trabalhos do Spark que estão a ser executados em macrodados. Com o Spark, pode facilmente transformar e preparar dados e, depois, aumentar horizontalmente a criação de modelos num único trabalho. Os modelos criados através do Spark MLLib podem ser implementados, geridos e monitorizados com a Gestão de Modelos do Azure Machine Learning. As execuções de preparações podem ser enviadas e geridas com a Experimentação do Azure Machine Learning. O Spark também pode ser utilizado para aumentar horizontalmente trabalhos de preparação de dados criados no Machine Learning Workbench. 

Utilize o Spark quando precisar de aumentar horizontalmente o processamento de dados e criar modelos como parte de um pipeline de dados. Pode criar trabalhos do Spark em Scala, Java, Python ou R. 

### <a name="batch-ai-training"></a>Batch AI Training 
O [Azure Batch AI Training](https://aka.ms/batchaitraining) ajuda-o a fazer experimentações em paralelo com os seus modelos de AI através de qualquer arquitetura e, depois, prepara-os em escala em GPUs em clusters.  Descreva os requisitos do seu trabalho e a configuração a executar e nós tratamos do resto. 

O Batch AI Training permite-lhe aumentar horizontalmente trabalhos de aprendizagem profunda em GPUs em clusters mediante a utilização de arquiteturas como:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

A Gestão de Modelos do Azure Machine Learning pode ser utilizada para pegar em modelos do Batch AI Training para implementá-los, geri-los e monitorizá-los.  O Batch AI Training vai ser integrado na Experimentação do Azure Machine Learning no futuro. 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
O [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) é um conjunto de ferramentas unificadas de aprendizagem profunda que descreve as redes neurais como fases computacionais num gráfico direcionado. Neste gráfico direcionado, os nós de folha representam os valores de entrada ou os parâmetros de rede, ao passo que os outros nós representam operações de matriz após as respetivas entradas. O Cognitive Toolkit permite-lhe ver e combinar facilmente tipos de modelos populares, como DNNs (Deep Neural Networks, Redes Neurais Profundas) feed-forward, redes convolucionais (CNNs) e redes recorrentes (RNNs/LSTMs). Implementa a aprendizagem de gradiente descendente estocástico (SGD [stochastic gradient descent], propagação de erros retroativa) com diferenciação e paralelização automática em várias GPUs e servidores.

Utilize o Cognitive Toolkit quando quiser utilizar a aprendizagem profunda para criar um modelo.  O Cognitive Toolkit pode ser utilizado em qualquer um dos serviços anteriores.

### <a name="microsoft-cognitive-services"></a>Serviços Cognitivos da Microsoft
Os Serviços Cognitivos da Microsoft são um conjunto de 30 APIs com as quais pode criar aplicações que utilizam métodos neurais de comunicação. Estas APIs permitem que as suas aplicações vejam, escutem, falem, compreendam e interpretem as nossas necessidades com algumas linhas de código apenas. Adicione facilmente funcionalidades inteligentes às suas aplicações, tais como: 

- Deteção de emoções e sentimentos
- Reconhecimento de voz e visão
- Compreensão de idiomas
- Conhecimentos e investigação

Os Serviços Cognitivos da Microsoft podem ser utilizados para desenvolver aplicações em vários dispositivos e plataformas. As APIs são melhoradas constantemente e são fáceis de configurar. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Install and create Azure Machine Learning](quickstart-installation.md) (Instalar e criar o Azure Machine Learning)
