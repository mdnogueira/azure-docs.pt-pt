---
title: "O reconhecimento de entidade biomedical - equipa o processo de ciência de dados - Azure Machine Learning | Microsoft Docs"
description: "Um processo de ciência de dados de equipa projeto início rápido que utiliza a aprendizagem profunda de reconhecimento de entidade biomedical no Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 21f8f66d8b78c2b536792bc96e9233d5739fde81
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Reconhecimento de entidade biomedical utilizando o modelo de processo de ciência de dados de equipa (TDSP)

Extração de entidade é subtarefa de extração de informações (também conhecido como [reconhecimento denominado entidade (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), segmentação de entidade e identificação de entidade). O objetivo deste cenário do mundo real é para realçar como utilizar o Azure Machine Learning Workbench para resolver uma tarefa de processamento de linguagem Natural (NLP) complicadas, tais como a extração de entidade a partir de texto não estruturado:

1. A forma como para preparar uma palavra neuronal embeddings modelo no corpus texto de cerca de 18 milhões PubMed abstracts utilizando [Spark Word2Vec implementação](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Como criar um modelo de rede neuronal periódica de memória de curto prazo longo (LSTM) avançada para extração de entidade num preparados para a GPU dados ciência de Máquina Virtual do Azure (GPU DS VM) no Azure.
2. Demonstrar o que modelo de embeddings essa palavra específicas do domínio pode superam o desempenho dos modelos de embeddings word genérico na tarefa de reconhecimento de entidade. 
3. Demonstrar como preparar e operacionalizar modelos de aprendizagem profunda utilizando o Azure Machine Learning Workbench.

4. Demonstrar as seguintes capacidades dentro do Workbench do Azure Machine Learning:

    * Instanciação das [estrutura do processo de ciência de dados de equipa (TDSP) e modelos](how-to-use-tdsp-in-azure-ml.md).
    * Gestão automática das dependências de projeto, incluindo a transferência e a instalação
    * Execução de scripts Python no differetn ambientes de computação.
    * Execute o histórico de controlo para Python scripts.
    * Execução de tarefas do Spark remoto de computação contexto utilizando clusters do HDInsight Spark 2.1.
    * Execução de tarefas numa remoto GPU VMs no Azure.
    * Fácil operationalization learning profunda modelos como serviços web em serviços de contentor do Azure (ACS).

## <a name="use-case-overview"></a>Descrição geral de cenário de utilização
Reconhecimento biomedical entidade com o nome é um passo crítico para tarefas NLP biomedical complexas tais como: 
* Extrair os menciona suportadas de entidades com o nome deste tipo diseases, drugs, chemicals e sintomas de Eletrónicos registos médicas ou estado de funcionamento.
* Deteção de Drug
* Noções sobre as interações entre entidade diferentes tipos de como interação drug drug, relação drug disease e relação gene protein.

Nosso cenário de utilização está centrado nas como uma grande quantidade de corpus de dados não estruturados como Medline PubMed abstracts pode ser analisada para preparar uma palavra ao incorporar o modelo. Em seguida, os embeddings de saída são considerados funcionalidades geradas automaticamente para preparar um extrator neuronal entidade.

A nossa resultados mostram que a formação de modelo de extração de entidade biomedical na palavra incorporar funcionalidades específicas do domínio outperforms modelo preparado no tipo genérico de funcionalidade. O modelo de específicas do domínio pode detetar 7012 entidades corretamente (fora 9475) com F1-pontuação de 0.73 comparada 5274 entidades F1-pontuação de 0.61 para o modelo de genérico.

A figura seguinte mostra a arquitetura que foi utilizada para processar os dados e modelos de formação.

![Arquitetura](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Descrição de dados

### <a name="1-word2vec-model-training-data"></a>1. Dados de preparação do modelo de Word2Vec
Iremos transferir primeiro os dados abstratos MEDLINE não processados do [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Os dados estão disponíveis publicamente no formato de ficheiros XML no respetivo [servidor FTP](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Existem 892 ficheiros XML disponíveis no servidor e cada um dos ficheiros XML tem as informações de 30 000 artigos. Obter mais detalhes sobre o passo de recolha de dados são fornecidos na secção de estrutura de projeto. Os campos presentes em cada ficheiro são 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. Dados de preparação do modelo LSTM

O modelo de extração de entidade neuronal foi preparado e avaliado em publiclly conjuntos de dados disponíveis. Para obter uma descrição detalhada sobre estes conjuntos de dados, foi consulte as seguintes origens:
 * [Tarefa de reconhecimento de entidade bio no BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR corpus de tarefas](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 – tarefas 9.1 (Drug reconhecimento)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Ligação para o repositório do GitHub da galeria do Azure
Segue-se a ligação para o repositório do GitHub público do cenário do mundo real que contém o código e mais detalhadas Descrição: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Pré-requisitos 

* Um Azure [subscrição](https://azure.microsoft.com/free/)
* Azure Machine Learning Workbench. Consulte [guia de instalação](quickstart-installation.md). Atualmente, o Workbench do Azure Machine Learning pode ser instalado nos seguintes sistemas operativos apenas: 
    * Windows 10 ou Windows Server 2016
    * macOS Sierra (ou mais recentes)


### <a name="azure-services"></a>Serviços do Azure
* [Cluster do HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) versão 2.1 do Spark no Linux (HDI 3.6) para o cálculo de escalamento horizontal. Para processar a quantidade total de abstracts MEDLINE abordadas a seguir, terá da configuração mínima de: 
    * Nó principal: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) tamanho
    * Nós de trabalho: pelo menos 4 [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). No nosso trabalho, utilizámos o 11 nós de trabalho do tamanho de D12_V2.
* [Máquina Virtual de ciência de dados de NC6 (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) para o cálculo de dimensionamento.

### <a name="python-packages"></a>Pacotes de Python

Todas as dependências necessárias são definidas no ficheiro aml_config/conda_dependencies.yml sob a pasta do projeto de cenário. As dependências definidas neste ficheiro serão aprovisionadas automaticamente para execuções contra docker, VM e HDI destinos de cluster. Para obter detalhes sobre o formato de ficheiro Conda ambiente, consulte [aqui](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Instruções básicas para o workbench do Azure Machine Learning (AML)
* [Descrição geral](overview-what-is-azure-ml.md)
* [Instalação](quickstart-installation.md)
* [Utilizar TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Como ler e escrever ficheiros](how-to-read-write-files.md)
* [Como utilizar blocos de notas do Jupyter](how-to-use-jupyter-notebooks.md)
* [Como utilizar GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Estrutura do cenário
Para o cenário, utilizamos os TDSP estrutura e a documentação de modelos de projeto (figura 1), que se segue o [TDSP ciclo de vida](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Projeto ser criado com base nas instruções fornecidas [aqui](./how-to-use-tdsp-in-azure-ml.md).


![Preencha as informações do projeto](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

O fluxo de trabalho de ciências de dados passo a passo é o seguinte:

### <a name="1-data-acquisition-and-understanding"></a>1. Aquisição e compreensão de dados

Consulte [dados aquisição e a compreensão](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

O corpus MEDLINE em bruto tem um total de milhões de 27 abstracts onde os artigos de cerca de 10 milhões ter um campo abstrato vazio. Azure HDInsight Spark é utilizado para processar dados grandes que não podem ser carregados para a memória de uma única máquina como um [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Em primeiro lugar, os dados são transferidos para o cluster do Spark. Em seguida, os seguintes passos são executados no [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* analisar os ficheiros XML a utilizar o analisador de XML Medline
* processar previamente os o texto abstrato incluindo dividir o frase, atomização e normalização maiúsculas.
* excluir artigos onde abstrato campo está vazio ou tem texto abreviado 
* criar o vocabulário word a partir de abstracts a formação
* preparar o word incorporar neuronal modelo. Para obter mais detalhes, consulte [ligação de código do GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) para começar a utilizar.


Depois de analisar os ficheiros XML, dados, tem o seguinte formato: 

![Exemplo de dados](./media/scenario-tdsp-biomedical-recognition/datasample.png)

O modelo de extração de entidade neuronal foi preparado e avaliado em publiclly conjuntos de dados disponíveis. Para obter uma descrição detalhada sobre estes conjuntos de dados, foi consulte as seguintes origens:
 * [Tarefa de reconhecimento de entidade bio no BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR corpus de tarefas](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 – tarefas 9.1 (Drug reconhecimento)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modelação

Consulte [modelação](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modelação é a fase em que mostramos como pode utilizar os dados transferidos na secção anterior para a sua própria palavra ao incorporar o modelo de preparação e utilizá-lo para outras tarefas a jusante. Embora estamos a utilizar os dados de PubMed, o pipeline para gerar o embeddings é genérico e pode ser reutilizado para preparar o word embeddings para qualquer outro domínio. Para embeddings ser uma representação exata dos dados, é essencial que o word2vec está preparado numa grande quantidade de dados.
Assim que tivermos o embeddings word pronto, iremos pode dar formação sobre um modelo de rede neuronal profundo que utiliza o embeddings detetado ao inicializar a camada de Embedding. Iremos marcar a camada embedding como não trainable mas que não é obrigatório. A formação do word ao incorporar o modelo é supervisionada e, por conseguinte, é possível tirar partido de textos sem etiqueta. No entanto, a formação do modelo de reconhecimento de entidade é uma tarefa de learning supervisionado e a precisão depende da quantidade e a qualidade dos dados de anotado manualmente. 


#### <a name="21-feature-generation"></a>2.1. Geração de funcionalidade

Consulte [funcionalidade geração](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec é a palavra ao incorporar o algoritmo de aprendizagem supervisionados trains um modelo de rede neuronal de um corpus formação sem etiqueta. Produz um vetor de contínuo de cada palavra no corpus que representa as respetivas informações de semânticos. Estes modelos são as redes neurais simples com uma único camada oculta. Os word vetores/embeddings adquiridos por backpropagation e stochastic descendente gradação. Existem dois tipos de modelos de word2vec, nomeadamente, ignorar-grama e a contínua-a matriz de-de-palavras (verifique [aqui](https://arxiv.org/pdf/1301.3781.pdf) para obter mais detalhes). Uma vez que estamos a utilizar implementação o MLlib word2vec, que suporta o modelo de ignorar grama, iremos brevemente descrevem-lo aqui (imagem retirada do [ligação](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Ignorar grama modelo](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

O modelo utiliza Softmax hierárquica e amostragem negativa para otimizar o desempenho. SoftMax hierárquica (H-SoftMax) é uma aproximação do seu inspirada pelo árvores binárias. H SoftMax essencialmente substitui a camada de SoftMax simples com uma camada hierárquica que tenha as palavras que sai. Isto permite-nos decompor calcular a probabilidade de uma palavra para uma sequência de cálculos de probabilidade, que guarda-nos de ter que calcular a normalização dispendiosas ao longo de todas as palavras. Uma vez que uma árvore binária equilibrada tem uma profundidade de log2 (| V |) (V é o vocabulário), é necessário apenas avaliar, no máximo, log2 (| V |) nós a obter a probabilidade de uma palavra final. A probabilidade de um w word fornecido a c de contexto, em seguida, é simplesmente o produto de probabilidades de colocar à direita e esquerda respetivamente ativa essa antecedência para o nó de folha. Vamos pode construir uma árvore Huffman com base na frequência das palavras no conjunto de dados para se certificar de que as palavras mais frequentes obter representações mais curtas. Para obter mais informações, consulte [esta ligação](http://sebastianruder.com/word-embeddings-softmax/).
Imagem retirada do [aqui](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Visualização

Assim que tivermos embeddings o word, gostaríamos de visualizá-los e verificar a relação entre as palavras semanticamente semelhantes. 

![W2V semelhança](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Iremos têm mostrado duas formas diferentes de visualizar o embeddings. Primeiro utiliza um PCA para projetar o vetor de dimensional elevado para um espaço de vetor de 2-D. Isto leva a uma perda de informações importante e de visualização não é precisa como. O segundo consiste em utilizar PCA com [t SNE](https://distill.pub/2016/misread-tsne/). t SNE é uma técnica de redução de dimensionalidade nonlinear que é adequada para incorporar dados alta dimensional para um espaço de dimensões de duas ou três, que podem ser visualizados num desenho de gráfico de dispersão.  Cada objeto alta dimensional-modelos por um ponto dois ou three dimensional de forma a que os objetos semelhantes são modelados por pontos que e diferentes objetos são modelados por pontos distantes. Funciona em duas partes. Em primeiro lugar, cria uma distribuição da probabilidade pares no espaço de dimensional superior de uma forma com objetos semelhantes uma probabilidade elevada de que está a ser selecionado e os pontos de diferentes têm baixa probabilidade de obtenção selecionado. Segundo, define uma distribuição da probabilidade semelhante através de pontos num mapa dimensional baixo e minimiza o divergência de KL entre as duas distribuições relativamente à localização dos pontos no mapa. A localização dos pontos de na dimensão baixa é obtida pelo minimizando a divergência de KL descendente de gradação a utilizar. Mas t SNE poderá não ser sempre fiável. Podem ser encontrados detalhes de implementação [aqui](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Como é mostrado na figura seguinte, a visualização de t-SNE fornece separação mais de vetores de word e potenciais padrões de clusters. 


* Visualização com PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Visualização com t-SNE

![t SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Pontos de mais próximo da "Cancer" (são todos os subtipos de Cancer)

![Pontos de mais próximo Cancer](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Preparar o extrator de entidade neuronal

Consulte [preparar extrator de entidade neuronal](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

A arquitetura de rede neuronal feed direta afecta um problema que tratar cada entrada e de saída como independentes de outras entradas e saídas. Esta arquitetura não é possível modelo sequência de sequência de tarefas etiquetas, tais como a conversão de máquina e de extração de entidade. Modelos de rede neuronal periódica ultrapassar o problema como pode passam informações calculadas até agora para o próximo nó. Esta propriedade é chamada ter memória na rede, uma vez que é possível utilizar as informações anteriormente calculadas, conforme mostrado na figura seguinte:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

RNNs clássica, na verdade, sofrem do [Vanishing gradação problema](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) devido a que não são capazes de utilizar todas as informações que possam tem vistos anteriormente. O problema fica evidente apenas quando é necessária uma grande quantidade de contexto para efetuar uma predição. Mas modelos como LSTM não sofrem desse um problema, na verdade que foram concebidos para não se esqueça de dependências de longo prazo. Ao contrário de vanilla RNNs que tenham uma única rede neuronal, os LSTMs têm as interações entre as redes neurais quatro para cada célula. Para obter uma explicação detalhada do funcionamento LSTM, consulte [este post](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM célula](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Vamos tentar juntar nossa própria baseada no LSTM periódica a rede neuronal e tente ao extrair os tipos de entidade como drug, disease e sintoma menciona suportadas do PubMed dados. O primeiro passo é obter uma grande quantidade de dados com nome e como que poderia ter adivinhado, que não é fácil! A maioria dos dados médicas contém muitas informações confidenciais sobre a pessoa e, por conseguinte, não estão disponível publicamente. Iremos dependem de uma combinação de duas diferentes conjuntos de dados que estão disponíveis publicamente. O primeiro conjunto de dados é de Semeval 2013 – tarefas 9.1 (Drug reconhecimento) e o outro é da tarefa de BioCreative V CDR. Iremos está a combinar e automaticamente a etiquetagem estes dois conjuntos de dados, pelo que pode detetar drugs e diseases de textos médicas e avaliar a nossa embeddings word. Para obter detalhes de implementação, consulte [ligação de código do GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

A arquitetura de modelo que iremos utilizadas em todos os códigos de e para a comparação é apresentada abaixo. O parâmetro que é alterado para diferentes conjuntos de dados é o comprimento de máximo de sequência (613 aqui).

![Modelo LSTM](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Modelo de avaliação
Consulte [modelo avaliação](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Utilizamos o script de avaliação da tarefa partilhado [entidade biografia do reconhecimento tarefa biografia do NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) para avaliar a precisão, devolução de chamada e F1 classificação do modelo. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>No domínio versus word genérico incorporar modelos

Segue-se uma comparação entre a precisão dos dois tipos de funcionalidade: embeddings (1) word preparado no PubMed abstracts e (2) word embeddings preparado no Google notícias de última hora. Claramente, Vemos que o modelo no domínio outperforms o modelo de genérico. Por conseguinte, com uma palavra específica ao incorporar o modelo em vez de utilizar um método genérico é muito mais útil. 

* Tarefa #1: Drugs e Diseases deteção

![Comparação do modelo de 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Vamos efetuar a avaliação de embeddings o word nos outros conjuntos de dados de forma semelhante e ver que esse modelo no domínio é sempre melhor.

* Tarefa #2: Proteins, linha da célula, tipo de célula, DNA e RNA deteção

![Comparação do modelo de 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Tarefa #3: Chemicals e Diseases deteção

![Comparação do modelo de 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Tarefa #4: Deteção de Drugs

![Comparação do modelo de 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Tarefa #5: Genes deteção

![Comparação do modelo de 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow versus CNTK
O modelo comunicado são preparados com Keras TensorFlow como back-end. Keras com back-end CNTK não suporta "inversa" momento que este trabalho foi concluído. Por conseguinte, com vista à, comparação, iremos preparado um modelo LSTM unidirecional com o back-end CNTK e comparado com um modelo LSTM unidirecional com back-end de TensorFlow. Instalar CNTK 2.0 para Keras de [aqui](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Comparação do modelo de 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Iremos concluir que executa a CNTK como boa como Tensorflow, tanto em termos de tempo de formação decorrido por época (60 seg CNTK e seg 75 para Tensorflow) e o número de entidades de teste detetado. Estamos a utilizar as camadas Unidirecionais para avaliação.


### <a name="3-deployment"></a>3. Implementação

Consulte [implementação](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Iremos implementar um serviço web num cluster no [serviço de contentor do Azure (ACS)](https://azure.microsoft.com/services/container-service/). O ambiente de operationalization Aprovisiona Docker e Kubernetes do cluster para gerir a implementação de serviço web. Pode encontrar mais informações sobre o processo de operationalization [aqui](model-management-service-deploy.md ).


## <a name="conclusion"></a>Conclusão

Iremos correu sobre os detalhes da forma como foi possível preparar um modelo de embedding word utilizando o algoritmo de Word2Vec no Spark e, em seguida, utilizar o embeddings extraída como as funcionalidades para preparar uma rede neuronal profundidade para extração de entidade. Iremos ter aplicado o pipeline de formação biomedical domínio. No entanto, o pipeline é genérico que seja aplicada para detetar tipos de entidade personalizada de qualquer outro domínio. Basta dados suficientes e pode adaptar facilmente os o fluxo de trabalho aqui apresentado para um domínio diferente.

## <a name="references"></a>Referências

* Tomas Mikolov, Kai Chen, Greg Corrado e Jeffrey Dean. 2013a. Estimativa eficiente das representações palavra no espaço de vetor. No Proceedings de ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado e Jorge Dean. 2013b. Representações distribuídas palavras e expressões e as respetivas compositionality. No Proceedings de NIPS páginas 3111 – 3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen e Sampo Pyysalo. 2016. [Como preparar boa palavra Embeddings para Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), Proceedings do Workshop 15 de processamento de linguagem Natural Biomedical, páginas 166 – 174.
* [Representações vetor palavras](https://www.tensorflow.org/tutorials/word2vec)
* [Redes Neurais periódica](https://www.tensorflow.org/tutorials/recurrent)
* [Problemas encontrados com o Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [O Spark Word2Vec: lições aprendidas](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

