---
title: Executar scripts do Python machine learning | Microsoft Docs
description: "Destaca subjacente suporte para scripts Python no Azure Machine Learning e cenários de utilização básica, capacidades e limitações de princípios de design."
keywords: machine learning, pandas, pandas de python, python scripts, Python executar scripts do python
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: c25f31ca72417672298657c4585184ad72db6c99
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Executar scripts de machine learning em Python no Azure Machine Learning Studio

Este tópico descreve os princípios de design subjacente o suporte para scripts Python no Azure Machine Learning atual. As principais capacidades fornecidas também descritas, incluindo:

- cenários de utilização básica de execução
- uma experimentação de pontuação num serviço web
- suporte para importar o código existente
- visualizações de exportação
- efetuar a seleção de funcionalidades supervisionado
- compreender algumas limitações

[Python](https://www.python.org/) é uma ferramenta indispensable no chest a ferramenta de muitos cientistas de dados. Tem de:

* uma sintaxe elegante e concisa, 
* suporte em várias plataformas, 
* uma coleção de bibliotecas de elevado desempenho, vasta e 
* ferramentas de desenvolvimento madura. 

Python está a ser utilizado em todas as fases de um fluxo de trabalho normalmente utilizadas nas modelação do machine learning:

- ingestão de dados e processamento 
- construção de funcionalidade
- modelo de formação 
- validação do modelo
- implementação dos modelos

Azure Machine Learning Studio suporta scripts do Python incorporar em várias partes de uma máquina experimentação de aprendizagem e também perfeitamente publicando-os como serviços web no Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Princípios de conceção de scripts Python no Machine Learning

A interface primária para Python no Azure Machine Learning Studio é através de [executar Script de Python] [ execute-python-script] módulo mostrado na figura 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. O **executar Script de Python** módulo.

O [executar Script de Python] [ execute-python-script] módulo no Azure ML Studio aceita até três entradas e produz saídas até dois (abordadas na secção seguinte), como o respetivo analogue R, o [executar R Script] [ execute-r-script] módulo. O código do Python para ser executado é introduzido na caixa de parâmetro especial nomeado como ponto de entrada função chamada `azureml_main`. Seguem-se a princípios de design chave utilizados para implementar este módulo:

1. *Tem de ser idiomatic para utilizadores do Python.* A maioria dos utilizadores de Python fator respetivo código como funções dentro de módulos. Por isso, colocar uma grande quantidade de instruções executável de um módulo de nível superior é relativamente raro. Como resultado, a caixa de script também cria uma função de Python especial com nome, por oposição a apenas uma sequência de instruções. Os objetos expostos na função são tipos de biblioteca padrão do Python, tais como [Pandas](http://pandas.pydata.org/) pacotes de dados e [NumPy](http://www.numpy.org/) matrizes.
2. *Tem de ter alta-fidelidade, entre locais e execuções de nuvem.* O back-end utilizado para executar o código de Python baseia-se no [Anaconda](https://store.continuum.io/cshop/anaconda/), um amplamente utilizado distribuição do Python científicos várias plataformas. Vem com próximo 200 dos pacotes de Python mais comuns. Por conseguinte, os cientistas de dados podem depurar e qualificar o respetivo código no seu ambiente do Azure Machine Learning compatível Anaconda local. Em seguida, utilizar um ambiente de desenvolvimento existente, tal como [IPython](http://ipython.org/) bloco de notas ou [ferramentas do Python para Visual Studio](http://aka.ms/ptvs), executá-lo como parte de uma experimentação do Azure ML. O `azureml_main` ponto de entrada é uma função de Python clássica e por isso *** podem ser criadas sem código do Azure ML específico ou o SDK instalado.
3. *Tem de ser totalmente integrada composta com outros módulos do Azure Machine Learning.* O [executar Script de Python] [ execute-python-script] módulo aceita, como entradas e saídas, conjuntos de dados do Azure Machine Learning padrão. A estrutura subjacente transparente e eficiente bridges tempos de execução do Azure ML e Python. Por isso, o Python pode ser utilizado em conjunto com existentes do Azure ML fluxos de trabalho, incluindo os que chamam para o R e SQLite. Consequentemente, scientist de dados foi compor fluxos de trabalho que:
   * utilizar o Python e Pandas para dados pré-processadas e limpeza
   * Feed de dados para uma transformação de SQL, associar vários conjuntos de dados para funcionalidades do formulário
   * modelos de formação utilizando os algoritmos no Azure Machine Learning 
   * avaliar e pós-cópia processar os resultados utilizando o R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Cenários de utilização básica no ML de scripts do Python

Nesta secção, iremos encontrar algumas das utilizações básicas do [executar Script de Python] [ execute-python-script] módulo. Entradas para o módulo de Python são expostas como frames Jumbo Pandas dados. A função tem de devolver um intervalo de dados único Pandas empacotado dentro de um Python [sequência](https://docs.python.org/2/c-api/sequence.html) como uma cadeia de identificação, lista ou NumPy matriz. O primeiro elemento desta sequência, em seguida, é devolvido na primeira porta de saída do módulo. Este esquema é mostrada na figura 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figura 2. Mapeamento de portas para os parâmetros de entrada e devolver o valor de porta de saída.

Mais detalhadas semântica de como as portas de entrada obterem mapeadas para os parâmetros do `azureml_main` função são apresentadas na tabela 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabela 1. Mapeamento de portas de entrada para parâmetros de função.

O mapeamento entre as portas de entrada e de parâmetros de função é posicional:

- A primeira porta de entrada ligada está mapeada para o primeiro parâmetro da função. 
- A segunda entrada (se ligado) está mapeada para o segundo parâmetro da função.

Consulte *Python para análise de dados* (O'Reilly, 2012) da McKinney para obter mais informações no Python Pandas e como pode ser utilizado para manipular dados de forma eficaz e eficiente. 


## <a name="translation-of-input-and-output-types"></a>Conversão de tipos de entrada e de saída 
Conjuntos de dados de entrada no Azure ML são convertidos em pacotes de dados no Pandas. Pacotes de dados de saída são convertidos para conjuntos de dados do Azure ML. As conversões seguintes são efetuadas:

1. Colunas de cadeia e numérica são convertidas como-é e os valores em falta num conjunto de dados são convertidos em 'NA' valores existentes na Pandas. A conversão mesma acontece volta a caminho (NA valores existentes na Pandas são convertidos para os valores em falta no Azure ML).
2. Os vetores de índice Pandas não são suportados no Azure ML. Todos os pacotes de dados de entrada na função Python sempre dispor de um índice numérico de 64 bits de 0 para o número de linhas menos 1. 
3. Conjuntos de dados de ML do Azure não podem ter nomes de colunas duplicados e nomes de colunas que não são cadeias. Se um intervalo de dados de saída contém colunas não numéricos, a estrutura chama `str` nos nomes de coluna. Da mesma forma, os nomes de colunas duplicados são automaticamente mangled a assegurar que os nomes são exclusivos. O sufixo (2) é adicionado à primeira duplicadas, (3) para o segundo duplicado e assim sucessivamente.


## <a name="operationalizing-python-scripts"></a>Operacionalizar Python scripts

Qualquer [executar Script de Python] [ execute-python-script] módulos utilizados na experimentação de classificação são denominados quando publicado como um serviço web. Por exemplo, a figura 3 mostra uma experimentação de classificação que contém o código para avaliar uma única expressão de Python. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Serviço Web para avaliação de uma expressão de Python.

Um serviço web criado a partir desta fase experimental:

- demora como entrada uma expressão de Python (como uma cadeia)
- envia-a para o interpretador Python 
- Devolve uma tabela que contém a expressão e o resultado avaliado.


## <a name="importing-existing-python-script-modules"></a>Importar os módulos de script de Python existentes

Um caso de utilização comuns para muitos cientistas de dados é para incorporar os scripts existentes do Python experimentações do Azure ML. Em vez de exigir que todo o código ser concatenado e colar na caixa de um script único, o [executar Script de Python] [ execute-python-script] módulo aceita um ficheiro zip que contém os módulos do Python, a porta de entrada terceiro. O ficheiro é deszipado pela estrutura de execução no tempo de execução e os conteúdos são adicionados ao caminho de biblioteca do interpretador Python. O `azureml_main` função pode, em seguida, importar estes módulos diretamente o ponto de entrada.

Por exemplo, considere o ficheiro Hello.py que contém uma função de "Olá, mundo" simple.

![image6](./media/execute-python-scripts/figure4.png)

Figura 4. Função definida pelo utilizador no ficheiro Hello.py.

Em seguida, vamos criar um ficheiro Hello.zip contém Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Figura 5. Ficheiro zip que contém o código de Python definido pelo utilizador.

Carregue o ficheiro zip como um conjunto de dados para o Azure Machine Learning Studio. Em seguida, criar e executar uma experimentação que utiliza o código de Python no ficheiro Hello.zip ao ligá-la para a porta de entrada terceira do **executar Script de Python** módulo, conforme mostrado na figura que.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Figura 6. Experimentação de exemplo com o código de Python definido pelo utilizador carregado como um ficheiro zip.

O resultado do módulo mostra que o ficheiro zip foi descompactado e de que a função `print_hello` tiver sido executado.
 
![image10](./media/execute-python-scripts/figure7.png)

A figura 7. A função definida pelo utilizador em utilização no interior do [executar Script de Python] [ execute-python-script] módulo.


## <a name="working-with-visualizations"></a>Trabalhar com visualizações

Plots criadas utilizando MatplotLib que pode ser visualizado no browser podem ser devolvidos pelo [executar Script de Python][execute-python-script]. Mas o rastreia não automaticamente redirecionado para imagens conforme forem ao utilizar o R. Por isso, o utilizador tem de os guardar explicitamente qualquer rastreia para ficheiros PNG se estiverem a ser devolvido para o Azure Machine Learning. 

Para gerar imagens de MatplotLib, tem de concluir o procedimento seguinte:

* mudar o back-end para "AGG" do compositor de com base em Qt a predefinição 
* criar um novo objeto de figura 
* obter o eixo e gerar rastreia todos os para a mesma 
* guardar a imagem para um ficheiro PNG 

Este processo é ilustrado na figura 8 seguintes que cria uma matriz de desenho de gráfico de dispersão utilizando a função de scatter_matrix no Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Figura 8. Código para guardar MatplotLib figuras imagens.

A figura 9 mostra uma experimentação que utiliza o script apresentado anteriormente para devolver rastreia através da porta de saída segundo.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Figura 9. Visualizar rastreia gerado a partir do código Python.

É possível devolver vários figuras ao guardá-las imagens diferentes, o tempo de execução do Azure Machine Learning escolherá todas as imagens e concatena-los para visualização.


## <a name="advanced-examples"></a>Exemplos avançados

O ambiente de Anaconda instalado no Azure Machine Learning contém pacotes comuns, tais como NumPy, SciPy e saber o Scikits. Estes pacotes podem ser efetivamente utilizados para várias tarefas de processamento de dados num de machine learning pipeline. Por exemplo, o seguinte experimentação e o script mostram a utilização de learners ensemble no saiba Scikits para calcular as pontuações de importância da funcionalidade para um conjunto de dados. As pontuações podem ser utilizadas para efetuar a seleção de funcionalidades supervisionado antes de a ser sejam fornecidas outro modelo de ML.

Segue-se a função de Python utilizada para calcular as pontuações de importância e as funcionalidades com base nas pontuações de ordem:

![image11](./media/execute-python-scripts/figure8.png)

A figura 10. Funciona a classificação funcionalidades por pontuações.
 A experimentação seguinte, em seguida, calcula e devolve as pontuações de importância das funcionalidades no conjunto de dados "Pima britânico Diabetes" no Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Figura 11. Experimentar a classificação funcionalidades no conjunto de dados Pima britânico Diabetes.

## <a name="limitations"></a>Limitações
O [executar Script de Python] [ execute-python-script] atualmente tem as seguintes limitações:

1. *Uma execução.* O tempo de execução do Python é atualmente um e, consequentemente, não permitir o acesso à rede ou sistema de ficheiros local de forma persistente. Todos os ficheiros guardados localmente são isolados e eliminados depois do módulo de conclusão. O código de Python não é possível aceder a maioria dos diretórios no computador que é executada, a exceção que está a ser o diretório atual e nos seus subdiretórios.
2. *Falta de desenvolvimento sofisticado e suporte de depuração.* O módulo de Python não suporta atualmente funcionalidades IDE como intellisense e a depuração. Além disso, se o módulo falha no tempo de execução, o rastreio da pilha Python completo está disponível. Mas tem de ser visualizado no registo de saída para o módulo. Recomendamos atualmente desenvolver e depurar scripts Python num ambiente, tais como IPython e, em seguida, importar o código para o módulo.
3. *Saída de moldura de dados individual.* O ponto de entrada do Python só é permitido para devolver um intervalo de dados individual como saída. Não é atualmente possível devolver arbitrários Python objetos, tais como modelos de formação diretamente para o tempo de execução do Azure Machine Learning. Como [executar Script do R][execute-r-script], que tem o mesmo limite, é possível em muitos casos pickle objetos para uma matriz de bytes e, em seguida, devolver que dentro de um intervalo de dados.
4. *Impossibilidade de personalizar a instalação de Python*. Atualmente, a única forma de adicionar módulos do Python personalizados é através do mecanismo de ficheiro zip descrito anteriormente. Apesar de ser exequível para módulos pequenos, é complexa para módulos grande (especialmente dos com DLLs nativas) ou um grande número de módulos. 

## <a name="conclusions"></a>Conclusões
O [executar Script de Python] [ execute-python-script] módulo permite um scientist de dados incorporar código Python existente fluxos de trabalho de aprendizagem alojado na nuvem no Azure Machine Learning e a forma totalmente integrada operacionalize-los como parte de um serviço web. O módulo de script de Python interoperates naturalmente com outros módulos no Azure Machine Learning. O módulo pode ser utilizado para um intervalo de tarefas de exploração de dados para pré- processamento de e extração funcionalidade e, em seguida, a avaliação e pós-processamento dos resultados. O tempo de execução de back-end utilizado para execução baseia Anaconda, uma distribuição do Python bem testada e amplamente utilizada. Este back-end torna simples para si para uma ativos de código existente para a nuvem.

Esperamos fornecer funcionalidades adicionais para o [executar Script de Python] [ execute-python-script] módulo, tal como a capacidade para dar formação e operacionalizar modelos no Python e adicionar um melhor suporte para o desenvolvimento e código de depuração no Azure Machine Learning Studio.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
