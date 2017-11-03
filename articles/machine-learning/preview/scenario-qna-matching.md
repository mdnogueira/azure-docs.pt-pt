---
title: "As perguntas e respostas uma correspondência com o Azure Machine Learning Workbench | Microsoft Docs"
description: "Como utilizar vários métodos de aprendizagem eficaz para que corresponda ao abrir terminou consultas para pré-existente FAQ respostas à pergunta/pares."
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
ms.manager: tihazen
ms.openlocfilehash: 8edc21fb8f42ee5897c4e938045cc1f42aedb3ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>As perguntas e respostas uma correspondência com o workbench do Azure Machine Learning
As respostas a questões terminadas abrir é difícil e requer frequentemente um esforço manual de especialistas (SMEs). Para ajudar a reduzir a necessidade de SMEs internos, as empresas criar frequentemente apresenta uma lista de perguntas mais frequentes (FAQ) como um meio de utilizadores a prestar assistência. Neste exemplo showcases vários métodos de aprendizagem máquina eficaz para que corresponda ao abrir consultas terminadas para pré-existente FAQ/respostas a perguntas pares. Este exemplo demonstra um processo de desenvolvimento simples para construir uma solução de utilizar o Workbench do Azure Machine Learning. 

## <a name="link-to-the-gallery-github-repository"></a>Ligação para o repositório do GitHub da Galeria
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Descrição geral

Este exemplo aborda o problema de mapeamento de perguntas de utilizador para pré-existente pares (Q & A) pergunta e resposta que normalmente é fornecida uma lista de perguntas mais frequentes (ou seja, FAQ) ou o Q & um pares presente em Web sites, como [pilha Capacidade excedida](https://stackoverflow.com/). Existem várias abordagens para corresponder a uma pergunta a respetiva resposta correta, tais como encontrar a resposta mais semelhante à pergunta. No entanto, neste exemplo abrir perguntas terminadas são correspondidas às perguntas mais anteriormente por partindo do princípio de que cada resposta nas FAQ do pode responder a várias perguntas semanticamente equivalentes.

Os passos de chaves necessários para fornecer esta solução são os seguintes:

1. Limpar e processar os dados de texto.
2. Saiba mais expressões informativos, que são word multi sequências que fornecem mais informações, quando são visualizados na sequência que quando tratados de forma independente.
3. Extrair as funcionalidades de dados de texto.
4. Os modelos de classificação de texto de formação e avaliar o desempenho do modelo.


## <a name="prerequisites"></a>Pré-requisitos

As pré-requisitos para executar este exemplo são os seguintes:

1. Um [conta do Azure](https://azure.microsoft.com/free/) (gratuitas estão disponíveis).
2. Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguintes o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar uma área de trabalho.
3. Neste exemplo pode ser executado em qualquer contexto de computação. No entanto, recomenda-se a executá-la numa máquina com vários núcleo com, pelo menos, de 16GB de memória e espaço em disco de 5GB.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto do workbench

Crie um novo projeto com este exemplo como um modelo:
1.  Abra máquina do Azure Learning Workbench
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "Q e A correspondência de" e selecione o modelo
5.  Clique em **Criar**.

## <a name="data-description"></a>Descrição de dados

O conjunto de dados utilizado neste exemplo é uma pilha Exchange dados informação do estado armazenado em [archive.org](https://archive.org/details/stackexchange). Estes dados são uma captura anónimos de todo o conteúdo contribuídos por utilizador no [rede pilha Exchange](https://stackexchange.com/). Cada site da rede é formatado como um arquivo separado consiste em ficheiros XML zipado através de 7-zip utilizando bzip2 compressão. Arquivo de cada site inclui publicações, os utilizadores, votos, comentários, PostHistory e PostLinks. 

### <a name="data-source"></a>Origem de dados

Este exemplo utiliza o [mensagens de dados (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) e [PostLinks dados (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) do site Stack Overflow. Para informações de esquema completa, consulte o [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

O `PostTypeId` campo de dados de publicações indica se a um pedido post é um `Question` ou um `Answer`. O `PostLinkTypeId` campo de dados PostLinks indica se duas mensagens estão ligadas ou duplicado. Mensagens de pergunta incluem, geralmente, algumas etiquetas, que são palavras-chave que categorizam uma pergunta com outras perguntas semelhante/duplicado. Existem algumas etiquetas com frequência alta, tais como `javascript`, `java`, `c#`, `php` etc., composto por um grande número de mensagens de pergunta. Neste exemplo, um subconjunto de Q & um pares com o `javascript` etiquetas é extraída.

Additionionally, um pedido post pergunta pode estar relacionado com várias mensagens de resposta ou mensagens duplicadas pergunta. Para construir uma lista de perguntas mais frequentes sobre a partir destes dois conjuntos de dados, são considerados alguns critérios de recolha de dados. Os três conjuntos de dados compilados são selecionados através de um script do SQL Server, que não está incluído neste exemplo. A descrição de dados resultante é o seguinte:

- `Original Questions (Q)`: Pergunta cronologia é mais frequentes e respondida na Stack Overflow site.
- `Duplications (D)`: Pergunta mensagens duplicadas outras perguntas pré-existente (`PostLinkTypeId = 3`), que são as perguntas originais. Duplications são considerados como ponto de vista semântico equivalentes às perguntas na medida em que a resposta fornecida à pergunta original também responde à pergunta duplicada novo originais.
- `Answers (A)`: Cada pergunta original e o respetivos duplications podem ligar a mais do que uma resposta publicações. Neste exemplo seleciona apenas a publicação da resposta é aceite pelo autor original (filtrado por `AcceptedAnswerId`) ou com a classificação mais elevada (classificadas por `Score`) em perguntas originais. 

A combinação destes três conjuntos de dados cria as perguntas e respostas pares onde uma resposta (A) está mapeada para uma pergunta original (Q) e a várias perguntas duplicadas (D) conforme ilustrado o seguinte diagrama de dados:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Estrutura dos dados

O esquema de dados e ligações de transferência direta dos três conjuntos de dados podem ser encontradas na tabela seguinte:

| Conjunto de dados | Campo | Tipo | Descrição
| ----------|------------|------------|--------
| [Perguntas](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Id | Cadeia | O ID exclusivo pergunta (chave primária)
|  | AnswerId | Cadeia | O ID exclusivo de resposta por pergunta
|  | Text0 | Cadeia | Os dados de texto não processados, incluindo o título e um corpo à pergunta
|  | CreationDate | Timestamp | O carimbo de quando a pergunta foi pedida
| [dupes](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Id | Cadeia | O ID exclusivo duplicação (chave primária)
|  | AnswerId | Cadeia | O ID de resposta associado a duplicação
|  | Text0 | Cadeia | Os dados de texto não processados, incluindo o título e um corpo de duplicação
|  | CreationDate | Timestamp | O carimbo de quando foi pedida de duplicação
| [respostas](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Id | Cadeia | O ID exclusivo de resposta (chave primária)
|  | text0 | Cadeia | Os dados não processados do texto da resposta


## <a name="scenario-structure"></a>Estrutura do cenário

É apresentado o exemplo correspondente as perguntas e respostas através de três tipos de ficheiros. O primeiro tipo é uma série de blocos de notas Jupyter que mostram as descrições passo a passo do fluxo de trabalho completo. O segundo tipo é um conjunto de ficheiros de Python contêm módulos personalizados do Python para extração de aprendizagem e a funcionalidade de expressão. Estes módulos do Python são genéricos não só servir neste exemplo, mas também outros casos de utilização. O tipo de terceiro é um conjunto de ficheiros do Python para otimizar parâmetros hyper e controlar o desempenho de modelo utilizando o Workbench do Azure Machine Learning.

Os ficheiros neste exemplo estão organizados de forma.

| Nome de ficheiro | Tipo | Descrição
| ----------|------------|--------
| `Image` | Pasta | A pasta utilizada para guardar as imagens para o ficheiro Leia-me
| `notebooks` | Pasta | A pasta de blocos de notas do Jupyter
| `modules` | Pasta | A pasta de módulos do Python
| `scripts` | Pasta | A pasta de ficheiros do Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Bloco de Notas do Jupyter | Aceder os dados de exemplo, pré-processar o texto e preparar a formação e testar a conjuntos de dados
| `notebooks/Part_2_Phrase_Learning.ipynb` | Bloco de Notas do Jupyter | Saiba mais expressões informativos e tokenize texto em representações de matriz de palavras (BOWs)
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Bloco de Notas do Jupyter | Extrair funcionalidades, preparar os modelos de classificação de texto e o desempenho do modelo de avaliação
| `modules/__init__.py` | Ficheiro do Python | O ficheiro de init de pacote do Python
| `modules/phrase_learning.py` | Ficheiro do Python | Os módulos do Python utilizados para transformar os dados não processados e saiba expressões informativos
| `modules/feature_extractor.py` | Ficheiro do Python | Os módulos do Python extrair funcionalidades para formação de modelo
| `scripts/naive_bayes.py` | Ficheiro do Python | O Python para otimizar hyper parâmetros no modelo de Bayes da
| `scripts/random_forest.py` | Ficheiro do Python | O Python para otimizar hyper parâmetros no modelo de floresta aleatório
| `README.md` | Ficheiro de markdown | O ficheiro de markdown Leia-me

> [!NOTE]
> A série de blocos de notas baseia-se em Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Ingestão de dados e a transformação

Os três conjuntos de dados compilados são armazenados no armazenamento de BLOBs e são obtidos em `Part_1_Data_Preparation.ipynb` bloco de notas.  

Antes dos modelos de classificação de texto de preparação, o texto no perguntas é limpa e pré-processado para excluir os fragmentos de código. Um learning frase supervisionados é aplicada durante o material de formação para saber mais informativos word multi sequências. Estas expressões são representados como unidades de uma única palavra composta pelo featurization a jusante de matriz de palavras (BOWs) utilizado pelos modelos de classificação de texto.

As descrições detalhadas passo a passo pré-processamentos de texto e aprendizagem de expressão podem ser encontradas nos blocos de notas `Part_1_Data_Preparation.ipynb` e `Part_2_Phrase_Learning.ipynb`, respetivamente.

### <a name="modeling"></a>Modelação

Este exemplo foi concebido para pontuar novas perguntas contra o pré-existente respostas um pares pelo texto de formação em que cada pré-existente Q & um par é uma classe exclusivos e um subconjunto das perguntas duplicados para cada par de perguntas e respostas estão disponíveis como os modelos de classificação materiais de formação. No exemplo, as perguntas e 75% das perguntas duplicadas original são mantidos para formação e o mais recentemente publicados 25% das perguntas duplicadas são retidos Escalamento como dados de avaliação.

O modelo de classificação utiliza um método ensemble para agregar três classificadores base, incluindo **Bayes da**, **máquina de Vetor com suporte**, e **aleatório floresta**. Em cada classificador de base, o `AnswerId` é utilizado como a etiqueta de classe e representações BOWs é utilizado como as funcionalidades.

O processo de preparação do modelo é ilustrado no `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Avaliação

Duas métricas de avaliação diferentes são utilizadas para avaliar o desempenho. 
1. `Average Rank (AR)`: indica a posição média em que a resposta correta se encontra na lista de pares de perguntas e respostas obtidas (sem o conjunto completo de 103 classes de resposta). 
2. `Top 3 Percentage`: indica a percentagem de teste questões que a resposta correta pode ser obtida no opções de topo três no devolvido classificado lista. 

A avaliação é demonstrada nos `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Conclusão

Este exemplo destaca como utilizar técnicas de análise de texto bem conhecidos, tais como o frase learning e o texto a classificação, para produzir um modelo robusto. Também showcases como o Azure Machine Learning Workbench pode ajudar com interativa de desenvolvimento e controlar modelo o desempenho da solução. 

Alguns destaques chaves deste exemplo são:

- A pergunta e o problema de correspondência de resposta possam ser resolvidos eficazmente com os modelos de classificação de expressão learning e texto.
- Demonstrar o desenvolvimento de modelo interativa com o Azure Machine Learning Workbench e o bloco de notas do Jupyter.
- Workbench de aprendizagem máquina do Azure gere o histórico de execução e modelos adquiridos com o registo as métricas de avaliação para fins de comparação. Estas funcionalidades permite a otimização de parâmetro hyper rápido e ajuda a identificar o melhor desempenho modelos.


## <a name="references"></a>Referências

Timothy J. Hazen, Pedro Richardson, [ _modelação Multiword expressões com a árvore de expressões restrita para melhorada modelação de tópico de reconhecimento de voz Conversational_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Idioma ditas tecnologia Workshop (SLT), IEEE de 2012. IEEE, 2012.

Timothy J. Hazen, [ _MCE técnicas de formação para fins de identificação de tópico de documentos de áudio ditas_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) IEEE transações no áudio, de reconhecimento de voz e de idioma de processamento, vol. 19, não. 8, pp. 2451-2460, Nov. de 2011.
