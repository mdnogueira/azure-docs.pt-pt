---
title: "Imagem classificação utilizando CNTK no interior do Workbench do Azure Machine Learning | Microsoft Docs"
description: "Preparar, avaliar e implementar um modelo de classificação de imagem personalizada através do Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: PatrickBue
ms.author: pabuehle
ms.reviewer: mawah, marhamil, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 2f8b2d9d2396c1f9c9e509257f3cd031a816729f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Classificação de imagem com o Azure Machine Learning Workbench

Abordagens de classificação de imagem podem ser utilizadas para resolver um grande número de problemas de visão do computador.
Estes incluem a criação de modelos, que responda às perguntas, tais como: *é um OBJETO presente na imagem?* onde por exemplo foi possível o OBJETO *preguiçoso*, *carro*, ou  *são enviados*. Ou perguntas mais complexas como: *que classe gravidade de disease olho é evinced pela análise retinal este patient?*.

Este tutorial aborda a resolver esses problemas. Vamos mostrar como preparar, avaliar e implementar a sua própria imagem classificação modelo com o [Microsoft cognitivos Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) de aprendizagem profunda.
Imagens de exemplo são fornecidas, mas o leitor também pode colocar o seu próprio conjunto de dados e preparar os seus próprios modelos personalizados.

Visão do computador soluções tradicionalmente necessários conhecimentos especialista para identificar e implementar so-called manualmente *funcionalidades*, que destacar informações pretendidas nas imagens.
Esta abordagem manual alteradas no 2012 com o famosa [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] avançada Learning documento e no presente, redes neuronal profunda (DNN) são utilizadas para localizar automaticamente estas funcionalidades.
DNNs gerou uma enorme melhoria no campo, não apenas para classificação de imagem, mas também para outros problemas de visão do computador, tais como a deteção de objeto e semelhança de imagem.


## <a name="link-to-the-gallery-github-repository"></a>Ligação para o repositório do GitHub da Galeria
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Descrição geral

Neste tutorial está dividido em três partes:

- Parte 1 mostra como preparar, avaliar e implementar um sistema de classificação de imagem utilizando uma DNN previamente treinado como featurizer e um SVM no respetivo resultado de preparação.
- Em seguida, parte 2 mostra como melhorar a precisão ao, por exemplo, limitar o DNN em vez de utilizá-lo como um featurizer fixo.
- Parte 3 abrange como utilizar o seu próprio conjunto de dados em vez das imagens de exemplo fornecidos e, se necessário, como produzir o seu próprio conjunto de dados por scraping imagens do net.

Apesar de experiência anterior com machine learning e CNTK não é necessária, é útil para compreender os princípios subjacentes. Números de precisão, formação de tempo, etc. comunicado no tutorial são apenas para referência, e os valores reais ao executar o código de quase certamente diferem.


## <a name="prerequisites"></a>Pré-requisitos

As pré-requisitos para executar este exemplo são os seguintes:

1. Um [conta do Azure](https://azure.microsoft.com/free/) (gratuitas estão disponíveis).
2. O [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguintes o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar uma área de trabalho.  
3. Um computador Windows. Sistema operativo Windows é necessário uma vez que o Workbench suporta apenas o Windows e o MacOS, ao Toolkit cognitivos da Microsoft (que utilizamos como biblioteca de aprendizagem profunda) só suporta o Windows e Linux.
4. Uma GPU dedicada não é necessária para executar a formação SVM na parte 1, no entanto, é necessário para limitar de DNN descrito na parte 2. Se não dispõem de uma GPU forte, pretende preparar em vários GPUs ou não dispõe de um computador Windows, considere, em seguida, a utilização profunda aprendizagem máquina do Azure com o sistema operativo Windows. Consulte [aqui](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) para obter um guia de implementação de 1-clique. Depois de implementada, ligar à VM através de uma ligação de ambiente de trabalho remota, instale o Workbench existe e execute o código localmente a partir da VM.
5. Vários bibliotecas de Python, tais como OpenCV tem de ser instalado. Clique em *abra a linha de comandos* do *ficheiro* menu no Workbench e execute os seguintes comandos para instalar estas dependências:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl`Após transferir a roda OpenCV do http://www.lfd.uci.edu/~gohlke/pythonlibs/ (o nome exato e versão podem alterar)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`

### <a name="troubleshooting--known-bugs"></a>Erros conhecidos / resolução de problemas
- É necessária uma GPU para parte 2 e, caso contrário, o erro "Batch formação de normalização na CPU ainda não está implementada" é emitida que ao tentar refinar a DNN.
- Erros de memória esgotada durante a preparação de DNN podem ser evitados, reduzir o tamanho de minibatch (variável `cntk_mb_size` no `PARAMETERS.py`).
- O código foi testado utilizando CNTK 2.0 e 2.1 e deve alterações também execução em versões mais recentes sem qualquer (ou apenas secundárias).
- No momento da escrita, o Workbench do Azure Machine Learning tiveram problemas que mostra os blocos de notas com mais de 5 em Mbytes. Blocos de notas deste tamanho grande podem acontecer se o bloco de notas é guardado com todas as células saída apresentada. Se encontrar este erro, abra a linha de comandos no menu de ficheiro no interior do Workbench, executar `jupyter notebook`, abra o bloco de notas, desmarque todos de saída e guardar o bloco de notas. Depois de efetuar estes passos, o bloco de notas abre-se corretamente no interior do Workbench do Azure Machine Learning novamente.


## <a name="create-a-new-workbench-project"></a>Criar um novo projeto do workbench

Para criar um novo projeto com este exemplo como um modelo:
1.  Abra o Azure Machine Learning Workbench.
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**.
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto.
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "Classificação de imagem" e selecione o modelo.
5.  Clique em **Criar**.

A executar estes passos cria a estrutura de projeto mostrada abaixo. Diretório do projeto está restrito a ser Mbytes menos de 25, uma vez que o Workbench do Azure Machine Learning cria uma cópia desta pasta após cada execução (para ativar o histórico de execução). Por conseguinte, todas as imagens e os ficheiros temporários são guardados do diretório *~/Desktop/imgClassificationUsingCntk_data* (referido como *DATA_DIR* neste documento).

  Pasta| Descrição
  ---|---
  aml_config /|                           Diretório que contém os ficheiros de configuração do Azure Machine Learning Workbench
  bibliotecas /|                              Diretório que contém todas as funções de programa auxiliar de Python e Jupyter
  blocos de notas /|                              Diretório que contém todos os blocos de notas
  recursos /|                              Diretório que contém todos os recursos (por exemplo o url de imagens de forma)
  scripts /|                              Diretório que contém todos os scripts
  PARAMETERS.py|                       Especificar todos os parâmetros de script de Python
  Readme.md|                           Este documento Leia-me


## <a name="data-description"></a>Descrição de dados

Este tutorial utiliza como executar. por exemplo um dataset texture do corpo superior clothing constituídas por até 428 imagens. Cada imagem está anotada como um dos três textures diferentes (leopard delimitada por pontos, repartido,). Vamos manter o número de imagens pequeno para que este tutorial pode ser executado rapidamente. No entanto, o código é bem testado e funciona com dezenas de milhares de imagens ou mais. Todas as imagens foram scraped utilizando a pesquisa do Bing imagem e mão-anotado como é explicado no [parte 3](#using-a-custom-dataset). A imagem de URLs com os respetivos atributos estão listados no */resources/fashionTextureUrls.tsv* ficheiro.

O script `0_downloadData.py` transfere todas as imagens para a *imagens/DATA_DIR/fashionTexture/* diretório. Alguns dos 428 URLs sejam provavelmente interrompidas. Não é um problema e tal significa que temos um pouco menos imagens para formação e testar.

A figura seguinte mostra exemplos para os atributos separada por pontos (esquerda), repartido (médio) e leopard (direita). Foram efetuadas anotações, de acordo com o item de clothing corpo superior.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Parte 1 – modelo de formação e avaliação

Na primeira parte deste tutorial, iremos são preparação um sistema que utiliza, mas não modificar, uma rede neuronal profundo previamente preparada. Este DNN previamente treinado é utilizado como um featurizer e um SVM linear está preparado para prever o atributo (separada por pontos, repartidos, ou leopard) de uma determinada imagem.

Iremos agora descrito esta abordagem em detalhe, passo a passo e mostrar quais os scripts têm de ser executado. Recomendamos que após cada passo para inspecionar os ficheiros são escritos e em que são escritos para.

Foram especificados todos os parâmetros importantes e uma explicação breve fornecido é um único local: o `PARAMETERS.py` ficheiro.




### <a name="step-1-data-preparation"></a>Passo 1: A preparação de dados
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

O bloco de notas `showImages.ipynb` podem ser utilizados para visualizar as imagens de e para corrigir os respetivos anotação conforme necessário. Para executar o bloco de notas, abra-o no Azure Machine Learning Workbench, clique em "Iniciar bloco de notas do servidor" se esta opção é apresentada e, em seguida, executar todas as células no bloco de notas. Se obtiver um erro complaining que o bloco de notas é demasiado grande para ser apresentada, consulte a secção de resolução de problemas neste documento.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Agora a executar o script com o nome `1_prepareData.py`, o que atribui todas as imagens para ou de preparação definido ou definir o teste. Esta atribuição é mutuamente exclusiva - nenhuma imagem de formação também é utilizada para fins de teste, ou vice versa. Por predefinição, um 75% aleatório das imagens de cada classe de atributo são atribuídos a formação e os restantes 25% são atribuídos para o teste. Todos os dados gerados pelo script são guardados no *proc/DATA_DIR/fashionTexture/* pasta.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Passo 2: Limitar a rede neuronal profundo
`Script: 2_refineDNN.py`

Como é explicado na parte 1 deste tutorial, a pré-treinado DNN é mantida fixo (ou seja, não é avançada). No entanto, o script com o nome `2_refineDNN.py` ainda é executada na parte 1, como carrega um previamente treinado [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] modelo e modifica-lo, por exemplo, para permitir a resolução da imagem de entrada superior. Este passo é rápido (segundos) e não necessita de uma GPU.

Parte 2 do tutorial, uma modificação para a PARAMETERS.py ficheiro faz com que o `2_refineDNN.py` script para também refinar a DNN previamente preparado. Por predefinição, iremos executar 45 epochs de formação durante refinement.

Em ambos os casos, o modelo final, em seguida, é escrito no ficheiro *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Passo 3: Avaliar DNN para todas as imagens
`Script: 3_runDNN.py`

Iremos agora pode utilizar o DNN (possivelmente avançada) no último passo para featurize nosso imagens. Tendo em conta uma imagem como entrada para o DNN, o resultado é o vector de 512 floats da camada penultimate do modelo. Este vetor é muito mais pequeno dimensional que a imagem em si. Contudo, deve conter (e até mesmo realce) todas as informações a imagem relevante para reconhecer o atributo da imagem, que é, se o item de clothing tem um delimitada por pontos repartido ou leopard texture.

Todos os representações de imagem DNN são guardados no ficheiro *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Passo 4: Formação da máquina de Vetor com suporte
`Script: 4_trainSVM.py`

Os floats 512 representações calculadas no último passo agora são utilizadas para preparar um classificador SVM: dada uma imagem como entrada, o SVM produz uma classificação para cada atributo estar presente. No nosso conjunto de dados de exemplo, isto significa que uma pontuação para 'repartidos' de 'delimitada por pontos' e 'leopard'.

Script `4_trainSVM.py` carrega as imagens de formação, trains um SVM para diferentes valores do parâmetro regularization (slack) C e mantém o SVM com maior precisão. A precisão da classificação é impresso na consola e desenhada no Workbench. Para os dados fornecidos texture estes valores devem ser aproximadamente 100% e 88% respetivamente. Por fim, o SVM treinado é escrito no ficheiro *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Passo 5: Avaliação e visualização
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

A precisão do classificador de imagem treinado pode ser medida ao utilizar o script `5_evaluate.py`. As pontuações de script utilizando o classificador SVM treinado todas as imagens de teste, atribui cada imagem o atributo com a classificação mais elevada e compara os atributos previstos com as anotações de truth zero.

O resultado do script `5_evaluate.py` é mostrado abaixo. A precisão da classificação de cada classe individuais é calculada, bem como a precisão para o conjunto de teste completo ('precisão global') e a média ao longo de accuracies individuais ('global uma média de classe precisão'). 100% corresponde a % de 0 para o pior e melhor exatidão possíveis. Deteção aleatórias em média a produzir uma precisão uma média de classe de 1 ao longo do número de atributos: no nosso caso, este precisão seria 33.33%. Estes resultados melhorar significativamente quando utilizar uma resolução de entrada superior como `rf_inputResoluton = 1000`, no entanto em detrimento de tempos de cálculo de DNN mais.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Para além de precisão, a curva ROC é representada com a respetiva área-em-curva (esquerda); e a matriz de confusão é apresentada (direita):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Por fim, o bloco de notas `showResults.py` é fornecida deslocar as imagens de teste e visualizar as pontuações da respetiva classificação:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Passo 6: implementação
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

O sistema treinado pode agora ser publicada como uma API REST. Implementação é explicada no bloco de notas `deploy.ipynb`e com base na funcionalidade dentro do Workbench do Azure Machine Learning. Consulte também a secção Implementação excelente o [IRIS tutorial](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-3).

Depois de implementada, o serviço web pode ser chamado utilizando o script `6_callWebservice.py`. Tenha em atenção que o endereço IP (local ou na nuvem) do serviço web tem de ser definida primeiro no script. O bloco de notas `deploy.ipynb` explica como encontrar este endereço IP.








## <a name="part-2---accuracy-improvements"></a>Parte 2 - os melhoramentos de precisão

Parte 1, iremos mostrou como classificar uma imagem por uma máquina de Vetor com suporte linear na saída 512 floats de uma rede neuronal profunda de preparação. Este DNN foi previamente treinado em milhões de imagens e a camada penultimate devolvido como vetor de funcionalidade. Esta abordagem é rápida, uma vez que é utilizado o DNN como-é mas, contudo, muitas vezes, fornece resultados boas.

Agora, iremos apresentar várias formas para melhorar a precisão do modelo do parte 1. Nomeadamente iremos refinar a DNN em vez de manter as coisas fixo.

### <a name="dnn-refinement"></a>DNN refinement

Em vez de um SVM, um pode fazer a classificação diretamente na rede neural. Isto é conseguido ao adicionar uma nova camada último para a pré-treinado DNN, que demora as floats 512 da camada penultimate como entrada. A vantagem de fazer a classificação no DNN encontra-se que agora total à rede pode ser retrained backpropagation a utilizar. Esta abordagem, muitas vezes, leva a muito accuracies classificação melhor comparação comparadas a utilização de DNN previamente treinado como-é, no entanto em detrimento muito mais formação tempo (mesmo com GPU).

A rede neuronal em vez de um SVM é feito ao alterar a variável `classifier` no `PARAMETERS.py` de `svm` para `dnn`. Em seguida, conforme descrito na parte 1, todos os scripts, exceto para formação SVM (passo 3) e de preparação de dados (passo 1) tem de ser executados novamente. DNN refinement necessita de uma GPU. Se não foi encontrado nenhum GPU ou se a GPU é bloqueada (por exemplo, uma execução anterior do CNTK), em seguida, o script `2_refineDNN.py` emitir um erro. Formação DNN pode gerar erros de memória esgotada no algumas GPUs, que podem ser evitadas, reduzir o tamanho de minibatch (variável `cntk_mb_size` no `PARAMETERS.py`).

Uma vez concluída a formação, o modelo avançado é guardado *DATA_DIR/proc/fashionTexture/cntk_refined.model*, e um desenho desenhada que mostra como os erros de classificação de formação e teste alteram durante a preparação. Tenha em atenção no que desenho que o erro no conjunto de preparação é muito menor do que o conjunto de teste. Este comportamento excessiva fitting so-called pode ser reduzido, por exemplo, utilizando um valor superior para a velocidade de dropout `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Como podem ser vistos no desenho abaixo, a precisão utilizando refinement DNN no conjunto de dados fornecido é 92.35% versus 88.92% antes (parte 1). Em particular, as imagens 'delimitada por pontos' melhoram significativamente, com um ROC área-em-curva de 0.98 com refinement vs. 0.94 antes. Estamos a utilizar um pequeno conjunto de dados e, por conseguinte, os accuracies reais com o código são diferentes. Este discrepância é devido a efeitos stochastic, tais como a divisão aleatório das imagens para formação e testar conjuntos.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Executar o controlo de histórico

Os arquivos do Azure Machine Learning Workbench o histórico de cada execução no Azure, para permitir a comparação das execuções de dois ou mais são mesmo apart semanas. Isto é explicado detalhadamente no [Iris tutorial](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-2). Também está a ser ilustrado nas capturas de ecrã seguintes, onde iremos comparar dois execuções do script `5_evaluate.py`, utilizando qualquer um dos refinement DNN ou seja, `classifier = "dnn"`(número de execução 148) ou SVM de preparação ou seja, `classifier = "svm"` (número de execução 150).

Captura de ecrã primeiro, o refinement DNN leva a melhor accuracies a formação SVM para todas as classes. A segunda captura de ecrã mostra as métricas que estão a ser controladas, incluindo qual era o classificador. Este registo é feito no script `5_evaluate.py` ao chamar o registo do Azure Machine Learning Workbench. Além disso, o script também guarda a matriz de curva e confusão ROC para o *produz* pasta. Isto *produz* pasta é especial em que o respetivo conteúdo também é controlado pela funcionalidade de histórico do Workbench e, por conseguinte, os ficheiros de saída podem ser acedidos em qualquer altura, independentemente se foram substituídas cópias locais.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>  
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Parâmetro de otimização
Como é verdadeiro para a maioria do machine learning projetos, obter resultados boas para um novo conjunto de dados requer parâmetro cuidado otimização, bem como para avaliar as decisões de conceção diferentes. Para ajudar a estas tarefas, são especificados todos os parâmetros importantes e uma explicação breve fornecido é um único local: o `PARAMETERS.py` ficheiro.

Alguns dos mais promising meios para melhoramentos são:

- Qualidade de dados: Certifique-se de que os conjuntos de formação e teste tem de alta qualidade. Ou seja, as imagens são anotadas corretamente, ambíguas imagens removidas (por exemplo itens clothing com reparte e pontos) e os atributos são mutuamente exclusivos (ou seja, escolhido, de modo a que cada imagem pertence a exatamente um atributo).
- Se o objeto de interesse for pequena na imagem, em seguida, abordagens de classificação de imagem se sabe não funciona bem. Nestes casos, considere utilizar uma abordagem de deteção de objeto, tal como descrito neste [tutorial](https://github.com/Azure/ObjectDetectionUsingCntk).
- DNN refinement: O parâmetro possivelmente mais importante para obter direita é o ritmo de aprendizagem `rf_lrPerMb`. Se a precisão em de preparação definido (primeiro figura na parte 2) não está próximo de 0 a 5%, mais provável é devido a um problema o ritmo de aprendizagem. Os outros parâmetros começadas `rf_` são menos importantes. Normalmente, o erro de formação deve exponencialmente diminuir e ser próximo de 0% depois de formação.
- Resolução de entrada: A resolução da imagem predefinida é 224 x 224 pixéis. Utilizar a resolução da imagem superior (parâmetro: `rf_inputResoluton`), por exemplo, 448 x 448 ou 896 x 896 pixels frequentemente significativas melhora a precisão, mas atrasar DNN refinement. **Utilizar superior resolução da imagem é praticamente livres lunch e quase sempre boosts precisão**.
- Ajuste superior DNN: evitar uma grande diferença entre a formação e teste precisão durante DNN refinement (primeiro figura na parte 2). Esta falha pode ser reduzida através de taxas dropout `rf_dropoutRate` de 0,5 ou mais e do aumento a ponderação regularizer `rf_l2RegWeight`. Utilizar uma taxa elevada de dropout pode ser especialmente útil se a resolução de entrada de imagem DNN é elevada.
- Tente utilizar DNNs mais profundo alterando `rf_pretrainedModelFilename` de `ResNet_18.model` quer `ResNet_34.model` ou `ResNet_50.model`. O modelo de Resnet 50 não é só mais aprofundado, mas o resultado da camada penultimate de floats tamanho 2048 (vs. 512 floats dos modelos ResNet-18 e ResNet 34). Este aumento de dimensão pode ser vantajoso especialmente quando se prepara um classificador SVM.

## <a name="part-3---custom-dataset"></a>Parte 3 - conjunto de dados personalizada

Parte 1 e 2, preparado e avaliar um modelo de classificação de imagem utilizando as imagens de textures clothing corpo superior fornecido. Agora, iremos mostram como utilizar um conjunto de dados personalizado do fornecidos pelo utilizador em vez disso. Ou, se não estiver disponível, como para gerar e anotar como um conjunto de dados com o Bing pesquisa de imagem.

### <a name="using-a-custom-dataset"></a>Utilizar um conjunto de dados personalizado

Em primeiro lugar, vamos tem uma vista de olhos a estrutura de pastas para os dados de texture clothing. Tenha em atenção, como todas as imagens para os atributos diferentes são nas respetivas subpastas *delimitada por pontos*, * leopard, e *repartidos* em *imagens/DATA_DIR/fashionTexture/*. Tenha em atenção também como o nome da pasta imagem também ocorre a `PARAMETERS.py` ficheiro:
```python
datasetName = "fashionTexture"
```

Utilizar um conjunto de dados personalizado é tão simple como reproducing esta estrutura de pasta onde todas as imagens estão em subpastas, de acordo com os respetivos atributos bem como para copiar estas subpastas para um novo diretório especificado para um utilizador *imagens/DATA_DIR/newDataSetName/*. É a única alteração de código necessárias definir o `datasetName` variável à *newDataSetName*. Scripts de 1 a 5, em seguida, podem ser executados por ordem e todos os ficheiros intermédios são escritos *proc/DATA_DIR/newDataSetName/*. Não existem outras alterações de código são necessárias.

É importante que cada imagem pode ser atribuída a exatamente um atributo. Por exemplo, seria errado têm atributos para 'preferido' e 'leopard', uma vez que uma imagem de 'leopard' também deverá pertencer a 'preferido'. Além disso, é melhor remover as imagens que são, por conseguinte, é difícil anotar e ambígua.



### <a name="image-scraping-and-annotation"></a>Imagem scraping e anotação

Recolher um suficientemente grande número de imagens anotados para formação e teste pode ser difícil. Uma forma de ultrapassar o problema é scrape imagens a partir da Internet. Por exemplo, consulte abaixo os resultados de pesquisa do Bing imagem para a consulta *t-shirt repartidos*. Conforme esperado, a maioria das imagens realmente estão repartidas t-shirts. As imagens incorretas ou é ambíguas alguns (por exemplo, a coluna 1, 1; de linha ou coluna 3, linha 2) podem ser identificadas e remover facilmente:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Para gerar um conjunto de dados de grande abrangente e diversificado, devem ser utilizadas várias consultas. Por exemplo, 7\*3 = 21 consultas podem synthesized utilizar automaticamente todas as combinações de itens de clothing {blouse hoodie, pullover, sweater, shirt, t-shirt, vest} e {repartido, delimitada por pontos, leopard} de atributos. Transferir as imagens principais 50 por consulta seria, em seguida, levar para um máximo de 21 * 50 = 1050 imagens.

Em vez de transferir manualmente as imagens a partir de pesquisa do Bing imagens, é muito mais fáceis de usar o [cognitivos API dos serviços do Bing imagem pesquisa](https://www.microsoft.com/cognitive-services/bing-image-search-api) que devolve um conjunto de URLs de imagem fornecida uma cadeia de consulta.

Algumas das imagens transferidas são exato ou perto duplicados (por exemplo, diferem apenas artefactos de resolução ou jpg imagem). Estes duplicados devem ser removidos, para que a divisão de formação e teste não contêm as imagens do mesmas. Remover imagens duplicadas pode ser conseguido através de uma abordagem de hash com base no que funciona em dois passos: (i) em primeiro lugar, a cadeia de hash é calculada para todas as imagens; (ii) na passagem segundo sobre as imagens, apenas essas imagens são mantidas com uma cadeia de hash, que ainda não foi encontrada. Todas as outras imagens são eliminadas. Encontrámos o `dhash` abordagem na biblioteca do Python `imagehash` e descritas neste [blogue](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) efetuar, com o parâmetro `hash_size` definido para 16. Há incorretamente remover algumas imagens não duplicado, desde que a maioria dos duplicados reais obter removida.





## <a name="conclusion"></a>Conclusão

Alguns destaques chaves deste exemplo são:
- O código de formação, avaliar e implementar modelos de classificação de imagem.
- Imagens de demonstração fornecidas, mas facilmente adaptable (uma linha alterar) para utilizar a imagem próprio conjunto de dados.
- Estado da última funcionalidades especialistas implementadas para preparar os modelos de precisão elevada com base na aprendizagem de transferência.
- Desenvolvimento de modelo interativa com o Azure Machine Learning Workbench e o bloco de notas do Jupyter.


## <a name="references"></a>Referências

[1] Alex Krizhevsky, Ilya Sutskever e Geoffrey E. Hinton, [ _ImageNet classificação com as redes Neurais profundo Convolutional_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming Putador, Xiangyu Zhang, Shaoqing Ren e Jian Sun, [ _profundo residuais de reconhecimento de imagem aprendizagem_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
