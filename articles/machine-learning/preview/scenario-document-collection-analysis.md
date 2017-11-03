---
title: "Documente a análise de coleção - Azure | Microsoft Docs"
description: "Como resumir e analisar uma grande coleção de documentos, incluindo técnicas, como a análise de modelo de tópico através do Azure ML Workbench, tópico modelação e learning frase."
services: machine-learning
documentationcenter: 
author: kehuan
ms.author: kehuan
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 5ef1589e28c01d750641873d3c8482f61d90a887
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="document-collection-analysis"></a>Análise de coleção de documentos

Este cenário demonstra como resumir e analisar uma grande coleção de documentos, incluindo técnicas, como a análise de modelo de tópico através do Azure ML Workbench, tópico modelação e learning frase. Workbench de aprendizagem máquina do Azure fornece para fácil escala para a coleção de documentos muito grandes e fornece mecanismos para preparar e otimizar modelos dentro de uma variedade de contextos de computação, vão de computação local para dados de ciência de máquinas virtuais para um Cluster do Spark. Desenvolvimento fácil é fornecido através de notas do Jupyter no Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Ligação para o repositório do GitHub da Galeria

O repositório do GitHub público para este cenário do mundo real contém todos os materiais, incluindo exemplos de código, necessários para este exemplo:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Descrição geral

Com uma grande quantidade de dados (data de texto especialmente não estruturados) recolhida diariamente, um significativo desafio é organizar, procurar e compreender vasta quantidades destas textos. Neste cenário de análise de coleção de documentos demonstra um eficiente e automatizado ponto-a-ponto fluxo de trabalho para analisar a coleção de documentos grande e ativar as tarefas NLP a jusante.

Os principais elementos fornecidos por neste cenário são:

1. Learning salientes palavras multi frase de acesso de documentos.

1. Detetar subjacentes tópicos apresentados na coleção de documentos.

1. Que representam os documentos pela distribuição topical.

1. Apresentação de métodos para organizar, procurar e resumir documentos com base no conteúdo topical.

Os métodos apresentados neste cenário foi possível ativar uma variedade de crítico industriais as cargas de trabalho, tais como a deteção de anomalias de tendências de tópico, resumo de coleção de documentos e pesquisa de documento semelhante. Podem ser aplicada para vários tipos de análise do documento, tais como government legislation, stories de notícias, revisões de produto, comentários do cliente e artigos científicos research.

Os técnicas/algoritmos do machine learning utilizados neste cenário incluem:

1. Processamento de texto e a limpeza

1. Aprendizagem de expressão

1. Modelação de tópico

1. Resumo de corpus

1. Tendências topical e deteção de anomalias

## <a name="prerequisites"></a>Pré-requisitos

As pré-requisitos para executar este exemplo são os seguintes:

* Certifique-se de que tem instalado corretamente [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [instalar e criar o guia de introdução](quickstart-installation.md).

* Neste exemplo pode ser executado em qualquer contexto de computação. No entanto, recomenda-se a executá-la numa máquina com vários núcleo com, pelo menos, de 16GB de memória e espaço em disco de 5GB.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto do Workbench

Crie um novo projeto com este exemplo como um modelo:
1.  Abra máquina do Azure Learning Workbench
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "Documento coleção Analysis Services" e selecione o modelo
5.  Clique em **Criar**.

## <a name="data-description"></a>Descrição de dados

O conjunto de dados utilizado neste cenário contém resumos de texto e os dados de metadados associados para cada ação legislative demorar por Congress-nos. Os dados são recolhidos a partir [GovTrack.us](https://www.govtrack.us/), que regista as atividades dos Estados Unidos Congress e ajuda a Americans participar no respetivo processo legislative national. Os dados em massa podem ser transferidos através de [esta ligação](https://www.govtrack.us/data/congress/) através de um script manual, o que não está incluído neste cenário. Foi possível encontrar os detalhes sobre como transferir os dados no [documentação da API de GovTrack](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Origem de dados

Neste cenário, os dados não processados recolhidos são uma série de ações legislative que foi introduzida pelo nos Congress (faturas propostas e as resoluções) de 1973 para Junho de 2017 (93rd para os Congresses 115th). Os dados recolhidos estão no formato JSON e contém um conjunto avançado de informações sobre as ações legislative. Consulte [esta ligação de GitHub](https://github.com/unitedstates/congress/wiki/bills) para uma descrição detalhada dos campos de dados. Para fins de demonstração dentro neste cenário, apenas um subconjunto dos campos de dados foram extraiu dos ficheiros do JSON. Um ficheiro TSV previamente compilado `CongressionalDataAll_Jun_2017.tsv` com registos dessas ações legislative é fornecido neste cenário. O ficheiro TSV pode ser transferido automaticamente pelos blocos de notas `1_Preprocess_Text.ipynb` sob a pasta de bloco de notas ou `preprocessText.py` no pacote do Python.

### <a name="data-structure"></a>Estrutura dos dados

Existem nove campos de dados no ficheiro de dados. Os nomes de campo de dados e as descrições são listadas da seguinte forma.

| Nome do campo | Tipo | Descrição | Contém um valor em falta |
|------------|------|-------------|---------------|
| `ID` | Cadeia | O ID da fatura/resolução. O formato deste campo é [bill_type] [número]-[congress]. Por exemplo, "hconres1 93" significa que o tipo de fatura "hconres" (consegue para resolução de próxima em simultâneo, consulte [neste documento](https://github.com/unitedstates/congress/wiki/bills#basic-information)), o número de fatura é ' 1 'e o número de congress é ' 93'. | Não |
| `Text` | Cadeia | O conteúdo da fatura/resolução. | Não |
| `Date` | Cadeia | A data da fatura/resolução inicialmente proposta. Num formato 'aaaa-mm-dd'. | Não |
| `SponsorName` | Cadeia | O nome das equipas de patrocinador primário propostas a fatura/resolução. | Sim |
| `Type` | Cadeia | O tipo de título dos principais equipas de patrocinador, 'rep' (representante) ou 'reme' (senator). | Sim |
| `State` | Cadeia | O estado das equipas de patrocinador primário. | Sim |
| `District` | Número inteiro | O número de regional de equipas de patrocinador primário se o título das equipas de patrocinador for um representante. | Sim |
| `Party` | Cadeia | A entidade de equipas de patrocinador primária. | Sim |
| `Subjects` | Cadeia | Os termos do requerente ao cumulativamente e pela biblioteca de Congress factura. Os termos são concatenados por vírgulas. Estes termos são escritos por um humanos na biblioteca de Congress e não existem, normalmente, quando as informações na factura são publicadas pela primeira vez. Estes podem ser adicionados em qualquer altura. Deste modo, no final de vida de uma fatura, algum assunto pode não ser relevante já. | Sim |

## <a name="scenario-structure"></a>Estrutura do cenário

O exemplo de análise de coleção do documento está organizado por dois tipos de materiais a entregar. O primeiro tipo é uma série de iPython blocos de notas que mostram as descrições passo a passo do fluxo de trabalho completo. O segundo tipo é um pacote do Python, bem como alguns exemplos de código de como utilizar esse pacote. O pacote do Python é genérico para servir vários casos de utilização.

Os ficheiros neste exemplo estão organizados de forma.

| Nome de ficheiro | Tipo | Descrição |
|-----------|------|-------------|
| `aml_config` | Pasta | Pasta de configuração do Azure Machine Learning Workbench, consulte [esta documentação](./experimentation-service-configuration-reference.md) para configuração de execução da experimentação de detalhado |
| `Code` | Pasta | A pasta de código utilizada para guardar o pacote do Python e Python scripts |
| `Data` | Pasta | A pasta de dados utilizada para guardar ficheiros intermédios |
| `notebooks` | Pasta | A pasta de blocos de notas do Jupyter |
| `Code/documentAnalysis/__init__.py` | Ficheiro do Python | O ficheiro de init de pacote do Python |
| `Code/documentAnalysis/configs.py` | Ficheiro do Python | O ficheiro de configuração utilizado pelo pacote do Python documento Analysis Services, incluindo constantes predefinidas |
| `Code/documentAnalysis/preprocessText.py` | Ficheiro do Python | O ficheiro de Python utilizado para processar previamente os dados para tarefas a jusante |
| `Code/documentAnalysis/phraseLearning.py` | Ficheiro do Python | O ficheiro de Python utilizado para saber mais expressões a partir dos dados e transformar os dados não processados |
| `Code/documentAnalysis/topicModeling.py` | Ficheiro do Python | O ficheiro de Python utilizado para preparar um modelo de tópico latentes Dirichlet alocação (LDA) |
| `Code/step1.py` | Ficheiro do Python | Passo 1 da análise de coleção de documentos: processar previamente os texto |
| `Code/step2.py` | Ficheiro do Python | Passo 2 da análise de coleção de documentos: frase learning |
| `Code/step3.py` | Ficheiro do Python | Passo 3 da análise de coleção de documentos: dar formação e avaliar modelo de tópico LDA |
| `Code/runme.py` | Ficheiro do Python | Exemplo de executar todos os passos num ficheiro |
| `notebooks/1_Preprocess_Text.ipynb` | iPython bloco de notas | Processar previamente os texto e transformar os dados não processados |
| `notebooks/2_Phrase_Learning.ipynb` | iPython bloco de notas | Saiba mais expressões de dados de texto (depois de transformação de dados) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython bloco de notas | Preparar modelo de tópico LDA |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython bloco de notas | Resumir os conteúdos da coleção de documentos com base num modelo de tópico LDA preparado |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython bloco de notas | Analisar o conteúdo de uma coleção de documentos de texto topical e correlacionar topical informações em relação a outros dados de metadados associados à coleção de documentos |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython bloco de notas | Visualização interativa do modelo de tópico adquirido |
| `notebooks/winprocess.py` | Ficheiro do Python | O script do python para multiprocessamento utilizado por blocos de notas |
| `README.md` | Ficheiro de markdown | O ficheiro de markdown Leia-me |

### <a name="data-ingestion-and-transformation"></a>Ingestão de dados e a transformação

O conjunto de dados compilado `CongressionalDataAll_Jun_2017.tsv` são guardados no armazenamento de BLOBs e está acessível a ambos de dentro de blocos de notas e os scripts Python. Existem dois passos para ingestão de dados e a transformação: pré-processamentos os dados de texto e o frase learning.

#### <a name="preprocess-text-data"></a>Processar previamente os dados de texto

O passo preprocessing aplica-se técnicas de processamento de linguagem natural para limpar e preparar os dados de texto não processados. Funciona como um precursor para a modelação de tópico latentes e aprendizagem de expressão supervisionados. O bloco de notas pode encontrar a descrição passo a passo detalhada `1_Preprocess_Text.ipynb`. Há também um script de Python `step1.py` corresponde a este bloco de notas.

Neste passo, o ficheiro de dados TSV é transferido a partir do armazenamento de Blobs do Azure e importado como um DataFrame Pandas. Cada elemento de linha de DataFrame é uma cadeia de comprimento coesa única de texto ou um 'documento'. Cada documento, em seguida, é dividido em segmentos de texto que probabilidades de frases, expressões ou subphrases. Divisão foi concebido para proibir o frase processo do word entre frase ou frase entre cadeias quando a aprendizagem expressões de aprendizagem.

Existem várias funções definidas para o passo preprocessing no bloco de notas e o pacote do Python. A maioria da tarefa pode ser alcançada ao chamar essas duas linhas de códigos.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Aprendizagem de expressão

O passo de aprendizagem frase implementa uma estrutura básica para saber mais expressões chaves entre uma coleção grande de documentos. Descrito no documento designada por "[modelação expressões Multiword com restrita a árvore de expressões para melhorada tópico modelação de Conversational voz](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", que foi originalmente apresentada no Workshop de IEEE 2012 na linguagem de autenticação ditas Tecnologia. A implementação detalhada passo de aprendizagem da expressão é apresentada no bloco de notas do iPython `2_Phrase_Learning.ipynb` e o script de Python `step2.py`.

Este passo demora o texto apagado como entrada e aprende as expressões mais salientes presentes numa coleção grande de documentos. Learning o frase de acesso é um processo iterativo que pode ser dividido em três tarefas: contagem de n-grams, classificar que potenciais expressões por ponderado Pointwise mútua informações do respetivas palavras constituintes e reescrever o frase em texto. Essas três tarefas são executadas sequencialmente no múltiplas interações até tem foi aprendeu as expressões especificados.

O pacote Python do documento Analysis Services, uma classe de Python `PhraseLearner` está definido no `phraseLearning.py` ficheiro. Segue-se o fragmento de código utilizado para saber mais expressões.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> O passo de aprendizagem da expressão é implementado com multiprocessamento. No entanto, fazer mais núcleos de CPU **não** significar um tempo de execução mais rápido. No nossos testes, o desempenho não foi melhorado com mais de oito núcleos devido a sobrecarga de multiprocessamento. Demorou cerca de duas e um meio horas para saber mais de 25.000 expressões numa máquina com oito núcleos (3,6 GHz).
>

### <a name="topic-modeling"></a>Modelação de tópico

Learning uma utilização de modelo de tópico latentes LDA é o terceiro passo deste cenário. O [gensim](https://radimrehurek.com/gensim/) pacote do Python é necessária neste passo para saber mais um [modelo de tópico LDA](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). O bloco de notas correspondente para este passo é `3_Topic_Model_Training.ipynb`. Também pode consultar `step3.py` como utilizar o pacote de análise do documento.

Neste passo, a tarefa principal é obter um modelo de tópico LDA e otimizar os parâmetros hyper. Existem vários parâmetros têm de ser otimizados quando preparar um modelo LDA, mas o parâmetro mais importante é o número de tópicos. Tópicos de poucos podem não ter informações aprofundadas sobre a coleção de documentos; ao escolher demasiado muitas fará o "excessiva clustering" de um corpus para vários tópicos de pequenos e altamente semelhantes. O objetivo deste cenário é mostrar como ajustar o número de tópicos. Workbench de aprendizagem máquina do Azure fornece a liberdade para executar experimentações com configuração de parâmetro diferentes nos contextos de computação diferentes.

No pacote do Python documento Analysis Services, alguns funções foram definidas para ajudar os utilizadores Descubra o número de procedimento dos tópicos. A primeira abordagem é através da avaliação de coerência do modelo de tópico. Existem quatro matrizes de avaliação suportados: `u_mass`, `c_v`, `c_uci`, e `c_npmi`. Os detalhes das quatro métricas são abordados na [neste documento](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). A abordagem segundo consiste em avaliar perplexity num corpus Escalamento contido.

Para a avaliação de perplexity, é esperada uma curva de forma 'U' para determinar o número de procedimento dos tópicos. E a posição de elbow é a melhor opção. Recomenda-se que avalie várias vezes com diferentes seed aleatório e obter a média. A coerência avaliar deve ser um 'n' formam, que significa que aumenta a coerência com o aumento do número de tópicos e, em seguida, diminuir. Um desenho de exemplo de perplexity e `c_v` coerência está a ser mostrada da seguinte forma.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v coerência](./media/scenario-document-collection-analysis/c_v_Coherence.png)

Neste cenário, o perplexity aumenta significativamente após 200 tópicos, o valor de coerência diminui significativamente após 200 tópicos bem. Com base nesses gráficos e a intenção dos tópicos mais gerais versus através de tópicos em cluster, escolha 200 tópicos devem ser uma boa opção.

Pode preparar um modelo de tópico LDA uma experimentação executar, ou dar formação e avaliar vários modelos LDA com configurações número tópico diferente de uma experimentação único executar. Recomenda-se a executar várias vezes para uma configuração e, em seguida, obter as avaliações de coerência e/ou perplexity médias. Os detalhes sobre como utilizar o pacote de análise do documento podem ser encontrados na `step3.py` ficheiro. Segue-se um fragmento de código de exemplo.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> O tempo de execução para preparar um modelo de tópico LDA depende de vários fatores, tais como o tamanho de corpus, configuração de parâmetro hyper, bem como o número de núcleos na máquina. Utilizar vários núcleos de CPU trains um modelo mais rápido. No entanto, com o mesmo parâmetro hyper definição mais núcleos significa que menos atualizações durante a preparação. Recomenda-se ter **, pelo menos, 100 atualizações para preparar um modelo de LDA convergida**. A relação entre o número de atualizações e parâmetros hyper é abordada em [este post](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) e [este post](http://miningthedetails.com/blog/python/lda/GensimLDA/). No nossos testes, que demorou cerca de 3 horas para preparar um modelo LDA com 200 tópicos utilizando a configuração de `workers=15`, `passes=10`, `chunksize=1000` numa máquina com 16 núcleos (de 2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Resumo de tópico e análise

O resumo de tópico e a análise é composta por dois blocos de notas, enquanto existirem não funciona correspondente no pacote de análise do documento.

No `4_Topic_Model_Summarization.ipynb`, mostra como resumir os conteúdos do documento com base num modelo de tópico LDA preparado. O resumo é aplicado a um modelo de tópico LDA aprendido no passo 3. Mostra como medir a importância ou qualidade de um tópico com tópico a medida de purity do documento. Esta medida purity parte do princípio de tópicos latentes dominam os documentos na qual aparece, são mais semanticamente importantes que os tópicos latentes weakly distribuídos por vários documentos. Este conceito foi introduzido no documento "[latentes modelação de tópico de áudio Corpus resumo](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)."

Bloco de notas `5_Topic_Model_Analysis.ipynb` mostra como analisar o conteúdo de uma coleção de documentos topical e correlacionar topical informações em relação a outros dados de metadados associados à coleção de documentos. Alguns rastreia foram introduzida neste bloco de notas para ajudar os utilizadores a compreender melhor o tópico adquirido e a coleção de documentos.

Bloco de notas `6_Interactive_Visualization.ipynb` mostra como interativamente visualizar o modelo de tópico adquiridas. Inclui quatro tarefas de visualização interativo.

## <a name="conclusion"></a>Conclusão

Este cenário do mundo real realça como utilize técnicas de análise de known text (neste caso, frase de acesso e de aprendizagem modelação de tópico LDA) para produzir um modelo robusto e como do Azure Machine Learning Workbench pode ajudar a controlar o desempenho do modelo e executar de forma totalmente integrada learners na escala superior. Mais detalhadamente:

* Utilize learning frase e modelação de tópico para processar uma coleção de documentos e criar um modelo robusto. Se a coleção de documentos é grande, do Azure Machine Learning Workbench pode dimensionar facilmente-configurar e out. Além disso, os utilizadores têm de ter a liberdade de o executar experimentações no contexto de computação diferentes facilmente no Azure Machine Learning Workbench.

* Workbench de aprendizagem máquina do Azure fornece ambas as opções para executar os blocos de notas numa forma passo a passo e o script do Python para executar uma experimentação de toda.

* Utilizando o Azure Machine Learning Workbench para determinar o número de procedimento dos tópicos de otimização de Hyper-parâmetro necessário para saber mais. Workbench de aprendizagem máquina do Azure pode ajudar a monitorizar o desempenho do modelo e totalmente integrada executar learners diferentes na escala superior.

* Workbench de aprendizagem máquina do Azure podem gerir o histórico de execução e modelos adquiridos. Permite que os cientistas de dados para identificar rapidamente o melhor desempenho modelos e para localizar os scripts e dados utilizados para gerá-los.

## <a name="references"></a>Referências

* **Timothy J. Hazen, Pedro Richardson**, [ _modelação Multiword expressões com a árvore de expressões restrita para melhorada modelação de tópico de reconhecimento de voz Conversational_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Idioma ditas tecnologia Workshop (SLT), IEEE de 2012. IEEE, 2012.

* **Timothy J. Hazen**, [ _tópico latentes modelação de resumo de áudio Corpus_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). Conferência anual ao 12º da associação de comunicação de voz internacional. 2011.

* **Miguel Roder, Andreas ambos, Alexander Hinneburg**, [ _explorar o espaço de tópico coerência medidas_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Proceedings da oitavo conferência internacional ACM na pesquisa na Web e de extração de dados. ACM, 2015.
