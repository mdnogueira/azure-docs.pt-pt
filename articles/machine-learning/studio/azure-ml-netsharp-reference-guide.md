---
title: "Guia para o Net # as redes Neurais especificação idioma | Microsoft Docs"
description: "Sintaxe para o Net # neuronal redes idioma especificação, juntamente com os exemplos de como criar um modelo de rede neuronal personalizada no Microsoft Azure ML com o Net #"
services: machine-learning
documentationcenter: 
author: jeannt
manager: jhubbard
editor: cgronlun
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeannt
ms.openlocfilehash: 54bef3e257363300ee1a13f7f45fc983e465ddbf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guia para idioma especificação rede neuronal Net # do Azure Machine Learning
## <a name="overview"></a>Descrição geral
NET # é um idioma desenvolvido pela Microsoft que é utilizado para definir arquiteturas de rede neuronal. Pode utilizar o Net # em módulos de rede neuronal no Microsoft Azure Machine Learning.

<!-- This function doesn't currentlyappear in the MicrosoftML documentation. If it is added in a future update, we can uncomment this text.

, or in the `rxNeuralNetwork()` function in [MicrosoftML](https://msdn.microsoft.com/microsoft-r/microsoftml/microsoftml). 

-->

Neste artigo, irá aprender os conceitos básicos necessários para desenvolver uma rede neuronal personalizada: 

* Requisitos de rede neuronal e como definir os componentes principais
* A sintaxe e palavras-chave da linguagem de especificação Net #
* Exemplos de redes neurais personalizadas criadas utilizando Net # 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>Noções básicas de rede neuronal
Uma estrutura de rede neuronal consiste ***nós*** que estão organizados por ***camadas***e ponderado ***ligações*** (ou ***contornos***) entre o nós. As ligações são direcional e de que cada ligação tem um ***origem*** nós e um ***destino*** nós.  

Cada ***camada trainable*** (um oculta ou uma camada de saída) tem um ou mais ***pacotes ligação***. Um pacote de ligação é constituída por uma camada de origem e uma especificação de ligações de camada dessa origem. Todas as ligações de um determinado grupo partilham o mesmo ***camada origem*** e o mesmo ***camada destino***. Em Net #, um pacote de ligação é considerado como pertencendo à camada de destino do pacote.  

NET # suporta vários tipos de ligação de pacotes, que lhe permite personalizar as entradas de forma estão mapeados para as camadas ocultas e mapeados para as saídas.   

A predefinição ou pacote padrão é um **pacote completo**, em que cada nó na camada de origem que está ligado a todos os nós na camada de destino.  

Além disso, Net # suporta os seguintes quatro tipos de pacotes de ligação avançada:  

* **Filtrar pacotes**. O utilizador pode definir um predicado utilizando as localizações de nó de camada de origem e o nó de camada de destino. Estão ligados nós sempre que o predicado é verdadeiro.
* **Os pacotes de convolutional**. O utilizador pode definir neighborhoods pequenos de nós na camada de origem. Cada nó na camada de destino está ligado a um neighborhood de nós na camada de origem.
* **Agrupamento de pacotes** e **os pacotes de normalização de resposta**. Estes são semelhantes a pacotes convolutional em que o utilizador define neighborhoods pequenos de nós na camada de origem. A diferença é que as ponderações de margens nestes pacotes não são trainable. Em vez disso, é aplicada uma função predefinida para os valores de nó de origem para determinar o valor do nó de destino.  

Com o Net # para definir a estrutura de uma rede neuronal possibilita definir estruturas complexas, tais como as redes neurais profundo ou convolutions de dimensões arbitrários, o que se sabe para melhorar a aprendizagem dados, tais como as vídeo, áudio ou imagem.  

## <a name="supported-customizations"></a>Personalizações suportadas
A arquitetura de modelos de rede neuronal que criar no Azure Machine Learning pode ser personalizada extensivamente utilizando Net #. Pode:  

* Criar camadas ocultas e controlar o número de nós em cada camada.
* Especifique como camadas estão a ser ligados entre si.
* Defina as estruturas de conectividade especiais, como convolutions e peso da partilha de pacotes.
* Especificar funções diferentes de ativação.  

Para obter detalhes sobre a sintaxe de linguagem especificação, consulte [especificação de estrutura](#Structure-specifications).  

Para obter exemplos de definir as redes neurais para algumas comuns do machine learning tarefas, a partir de simplex para complexa, consulte [exemplos](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Requisitos gerais
* Tem de existir camada exatamente uma saída, pelo menos uma camada de entrada e zero ou mais camadas ocultas. 
* Cada camada tem um número fixo de nós, concecionais dispostas numa matriz de dimensões arbitrários retangular. 
* Camadas de entrada não têm associados treinados parâmetros e representam o ponto onde os dados da instância entra na rede. 
* Trainable camadas (as camadas ocultas e de saída) associou treinados parâmetros, conhecidos como ponderações e preconceitos. 
* Os nós de origem e de destino tem de ser diferentes camadas. 
* Ligações tem de ser Acíclico; por outras palavras, não pode ser uma cadeia de ligações à esquerda novamente para o nó de origem inicial.
* A camada de saída não pode ser uma camada de origem de um pacote de ligação.  

## <a name="structure-specifications"></a>Especificações de estrutura
Uma especificação de estrutura de rede neuronal é composta por três secções: o **declaração de constantes**, a **camada declaração**, a **declaração de ligação**. Há também opcional **partilhar declaração** secção. As secções podem ser especificadas por qualquer ordem.  

## <a name="constant-declaration"></a>Declaração de constantes
Declaração de constante é opcional. Fornece um meio para definir valores utilizados noutro local na definição de rede neural. A instrução de declaração é composta por um identificador seguido por um sinal de igual e uma expressão de valor.   

Por exemplo, a seguinte instrução define uma constante **x**:  

    Const X = 28;  

Para definir dois ou mais constantes em simultâneo, coloque os valores e nomes de identificadores chavetas e separadas por ponto e vírgula. Por exemplo:  

    Const { X = 28; Y = 4; }  

O lado direito da expressão cada atribuição pode ser um número inteiro, um número real, um valor booleano (VERDADEIRO ou FALSO) ou uma expressão de matemática. Por exemplo:  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Declaração de camada
A declaração de camada é necessária. Define o tamanho e a origem da camada, incluindo respetivos pacotes de ligação e atributos. A instrução de declaração começa com o nome da camada (entrada, oculto ou saída), seguido de dimensões da camada (uma cadeia de identificação de números inteiros positivos). Por exemplo:  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

* O produto das dimensões é o número de nós na camada. Neste exemplo, existem duas dimensões [5,20], que significa que existem 100 nós na camada.
* As camadas podem ser declaradas por qualquer ordem, com uma exceção: se está definida mais do que uma camada de entrada, a ordem em que são declarados tem de corresponder à ordem das funcionalidades nos dados de entrada.  

Para especificar que o número de nós na camada de uma determinado automaticamente, utilize o **automática** palavra-chave. O **automática** palavra-chave tem efeitos diferentes, consoante a camada:  

* Uma declaração de camada de entrada, o número de nós é o número de funcionalidades nos dados de entrada.
* Numa declaração camada oculta, o número de nós é o número que é especificado pelo valor de parâmetro para **número de nós ocultos**. 
* Uma declaração de camada de saída, o número de nós é 2 para classificação de duas classes, 1 para regressão e igual ao número de nós de saída para a classificação de várias classes.   

Por exemplo, a seguinte definição de rede permite que o tamanho de todas as camadas para ser determinado automaticamente:  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Uma declaração de camada para uma camada trainable (as camadas ocultas ou de saída), opcionalmente, pode incluir a saída função (também denominada uma função de ativação), que está predefinida para **sigmoid** para modelos de classificação, e  **linear** para modelos de regressão. (Mesmo se utilizar a predefinição, a pode explicitamente de estado a função de ativação, se assim o desejar para efeitos de clareza.)

São suportadas as seguintes funções de saída:  

* sigmoid
* linear
* softmax
* rlinear
* parênteses
* sqrt
* srlinear
* Abs
* TANH 
* brlinear  

Por exemplo, a declaração seguinte utiliza o **softmax** função:  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Declaração de ligação
Imediatamente depois de definir a camada trainable, tem de declarar as ligações entre as camadas definidas. A declaração de pacote de ligação começa com a palavra-chave **de**, seguido do nome de camada de origem do pacote e o tipo de pacote de ligação para criar.   

Atualmente, são suportados os cinco tipos de pacotes de ligação:  

* **Completo** pacotes, indicados pela palavra-chave **todos os**
* **Filtrados** pacotes, indicados pela palavra-chave **onde**, seguido de uma expressão de predicado
* **Convolutional** pacotes, indicados pela palavra-chave **convolve**, seguido os atributos de convolution
* **Agrupamento** pacotes, indicados pelas palavras-chave **máximo conjunto** ou **significa agrupamento**
* **Normalização de resposta** pacotes, indicados pela palavra-chave **norma de resposta**      

## <a name="full-bundles"></a>Pacotes completos
Um pacote de ligação completo inclui uma ligação de cada nó na camada de origem para cada nó na camada de destino. Este é o tipo de ligação de rede predefinido.  

## <a name="filtered-bundles"></a>Pacotes filtrados
A especificação de um pacote de ligação filtrada inclui um predicado, indicado sintaticamente, tanto que, como c# expressão lambda. O exemplo seguinte define dois pacotes filtrados:  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

* No predicado de *ByRow*, **s** é um parâmetro que representa um índice para a matriz de nós de camada de entrada, retangular *Pixels*, e **d** é um parâmetro que representa um índice para a matriz de nós de camada oculta, *ByRow*. O tipo de ambos **s** e **d** é uma cadeia de identificação de números inteiros de comprimento dois. Concecionais, **s** intervalos através de todos os pares de números inteiros com *0 < = s [0] < 10* e *0 < = s[1] < 20*, e **d**  intervalos através de todos os pares de números inteiros, com *0 < = d [0] < 10* e *0 < = d[1] < 12*. 
* No lado direito da expressão de predicado, há uma condição. Neste exemplo, para cada valor de **s** e **d** forma a que a condição for True, não há um limite de nó de camada de origem para o nó de camada de destino. Assim, esta expressão de filtro indica que o pacote inclui uma ligação a partir do nó definida por **s** para o nó definido por **d** em todos os casos em que é igual a d [0] s [0].  

Opcionalmente, pode especificar um conjunto de peso de um pacote filtrado. O valor para o **ponderações** atributo tem de ser uma cadeia de identificação de valores de ponto com um comprimento que corresponde ao número de ligações definidos pelo pacote de vírgula flutuante. Por predefinição, as ponderações aleatoriamente são geradas.  

Valores de ponderação estão agrupados pelo índice de nó de destino. Ou seja, se o primeiro nó de destino está ligado a nós de origem K, o primeiro *K* elementos do **ponderações** cadeias de identificação são as ponderações para o primeiro nó de destino, por ordem de índice de origem. O mesmo se aplica para os restantes nós de destino.  

É possível especificar ponderações diretamente como valores constantes. Por exemplo, se o que aprendeu as ponderações anteriormente, pode especificá-las como constantes utilizando esta sintaxe:

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Pacotes de convolutional
Quando os dados de preparação tem uma estrutura homogénea, ligações convolutional são frequentemente utilizadas para saber mais funcionalidades de alto nível dos dados. Por exemplo, na imagem, dados de vídeos ou áudio, dimensionalidade geográfico ou temporal podem ser bastante uniforme.  

Os pacotes de convolutional recorrer retangular **kernels** que são slid através de dimensões. Essencialmente, cada kernel define um conjunto de ponderações aplicada no locais neighborhoods, referida como **aplicações de kernel**. Cada aplicação de kernel corresponde a um nó na camada de origem, é referido como o **central nó**. As ponderações de um kernel são partilhadas entre várias ligações. Um pacote convolutional, cada kernel está retangular em todas as aplicações de kernel são o mesmo tamanho.  

Os pacotes de convolutional suportam os seguintes atributos:

**InputShape** define dimensionalidade da camada de origem para efeitos deste pacote convolutional. O valor tem de ser uma cadeia de identificação de números inteiros positivos. O produto dos números de inteiros tem de ser igual o número de nós na camada de origem, mas, caso contrário, não precisa de corresponda à dimensionalidade declarada para a camada de origem. O comprimento de cadeia de identificação, este torna-se a **aridade** valor para o pacote convolutional. (Normalmente aridade refere-se ao número de argumentos ou operandos que pode efetuar uma função.)  

Para definir a forma e localizações dos kernels, utilize os atributos **KernelShape**, **Stride**, **preenchimento**, **LowerPad**e  **UpperPad**:   

* **KernelShape**: define o dimensionalidade do cada kernel para o pacote convolutional (obrigatório). O valor tem de ser uma cadeia de identificação de números inteiros positivos com um comprimento de é igual à aridade do pacote. Cada componente desta cadeia de identificação tem de ser maior do que o componente correspondente do **InputShape**. 
* **STRIDE**: (opcional) define os tamanhos de passo deslizante de convolution (tamanho de um passo para cada dimensão), que é a distância entre os nós centrais. O valor tem de ser uma cadeia de identificação de números inteiros positivos com um comprimento aridade do pacote. Cada componente desta cadeia de identificação tem de ser maior do que o componente correspondente do **KernelShape**. O valor predefinido é uma cadeia de identificação com todos os componentes igual a um. 
* **Partilha**: (opcional) define o peso da partilha para cada dimensão do convolution. O valor pode ser um valor booleano único ou uma cadeia de identificação dos valores booleanos com um comprimento aridade do pacote. Um valor booleano único é expandido para ser uma cadeia de identificação do comprimento correto com todos os componentes igual ao valor especificado. O valor predefinido é uma cadeia de identificação é composta por todos os valores verdadeiros. 
* **MapCount**: (opcional) define o número de funcionalidade mapeia para o pacote convolutional. O valor pode ser um número inteiro positivo único ou uma cadeia de identificação de números inteiros positivos com um comprimento aridade do pacote. Um só valor inteiro é expandido para ser uma cadeia de identificação do comprimento correto com os componentes primeiro igual ao valor especificado e os restantes componentes igual a um. O valor predefinido é um. O número total de mapas de funcionalidade é o produto dos componentes de cadeia de identificação. Factoring este número total entre os componentes determina a forma como os valores de mapa de funcionalidade são agrupados de nós de destino. 
* **Ponderações**: (opcional) define as ponderações iniciais para o pacote. O valor tem de ser uma cadeia de identificação de vírgula flutuante valores de ponto com um comprimento que é o número de vezes kernels o número de ponderações por kernel, tal como definido neste artigo. As ponderações predefinidas são geradas aleatoriamente.  

Existem dois conjuntos de propriedades que controlam o preenchimento, as propriedades que está a ser mutuamente exclusivos:

* **Preenchimento**: (opcional) determina se a entrada deve ser preenchido até ao utilizar um **esquema de preenchimento predefinido**. O valor pode ser um valor booleano único ou pode ser uma cadeia de identificação dos valores booleanos com um comprimento aridade do pacote. Um valor booleano único é expandido para ser uma cadeia de identificação do comprimento correto com todos os componentes igual ao valor especificado. Se o valor para uma dimensão for VERDADEIRO, a origem logicamente será preenchida nessa dimensão com o valor de zero células para suportar aplicações de kernel adicionais, que os nós dos primeiro e últimos kernels nessa dimensão centrais são os primeiro e últimos nós que dimensão na camada de origem. Assim, o número de nós "fictícias" em cada dimensão é determinado automaticamente, para se ajustar exatamente *(InputShape [d] - 1) / Stride [d] + 1* kernels para a camada de origem preenchido até. Se o valor para uma dimensão é False, os kernels são definidas para que o número de nós em cada lado que restam saída é o mesmo (até uma diferença de 1). O valor predefinido deste atributo é uma cadeia de identificação com todos os componentes igual como False.
* **UpperPad** e **LowerPad**: (opcional) forneça maior controlo sobre a quantidade de preenchimento para utilizar. **Importante:** estes atributos podem ser definido se e apenas se o **preenchimento** propriedade acima é ***não*** definido. Os valores devem ser cadeias de identificação valor de número inteiro com comprimentos são aridade do pacote. Quando estes atributos são especificados, são adicionados nós "fictícias" para as extremidades superiores e inferiores de cada dimensão da camada de entrada. O número de nós adicionados a terminar o inferior e superior em cada dimensão é determinado pelo **LowerPad**[i] e **UpperPad**[i], respetivamente. Para garantir que kernels correspondem apenas a nós "verdadeiro" e não a nós "fictícias", as condições seguintes têm de ser cumpridas:
  * Cada componente do **LowerPad** deve ser estritamente menor que KernelShape [d] / 2. 
  * Cada componente do **UpperPad** não pode ser superior a KernelShape [d] / 2. 
  * O valor predefinido destes atributos é uma cadeia de identificação com todos os componentes iguais a 0. 

A definição **preenchimento** = true permite a maior quantidade preenchimento conforme necessário para manter o "Centro" de kernel dentro "real" de entrada. Isto altera as contas um pouco para calcular o tamanho de saída. Geralmente, o tamanho de saída *D* é calculada como *D = (I - K) / S + 1*, onde *posso* é o tamanho de entrada, *K* é o tamanho de kernel, *S*  é stride, e  */*  é divisão de número inteiro (arredondar na direção de 0). Se definir UpperPad = [1, 1], o tamanho de entrada *posso* é, efetivamente, 29 e, consequentemente, *D = (29-5) / 2 + 1 = 13*. No entanto, quando **preenchimento** = true, essencialmente *posso* obtém bumped cópias de segurança por *K - 1*; por conseguinte, *D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14*. Ao especificar valores para **UpperPad** e **LowerPad** obter muito mais controlo sobre o preenchimento que o se basta definir **preenchimento** = true.

Para obter mais informações sobre redes convolutional e as respetivas aplicações, consulte estes artigos:  

* [http://deeplearning.NET/tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html)
* [http://Research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
* [http://people.csail.MIT.edu/jvb/Papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Agrupamento de pacotes
A **agrupamento pacote** aplica-se de geometria semelhante a conectividade convolutional, mas utiliza as funções predefinidas para os valores de nó de origem para derivar o valor do nó de destino. Por conseguinte, o agrupamento de pacotes tem sem estado trainable (ponderações ou preconceitos). Agrupamento de pacotes suportam todos os atributos convolutional exceto **partilha**, **MapCount**, e **ponderações**.  

Normalmente, kernels resumidos por unidades adjacentes de agrupamento não se podem sobrepor. Se Stride [d] for igual a KernelShape [d] na cada dimensão, a camada obtida é a tradicional camada pooling local, que é normalmente utilizada em redes neurais convolutional. Cada nó de destino calcula o número máximo ou a média das atividades do respetivo kernel na camada de origem.  

O exemplo a seguir ilustra um pacote pooling: 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

* A aridade do pacote é 3 (o comprimento das cadeias de identificação **InputShape**, **KernelShape**, e **Stride**). 
* O número de nós na camada de origem é *5 * 24 * 24 = 2880*. 
* Esta é uma camada de pooling local tradicional porque **KernelShape** e **Stride** são iguais. 
* O número de nós na camada de destino é *5 * 12 * 12 = 1440*.  

Para obter mais informações sobre as camadas de agrupamento, consulte estes artigos:  

* [http://www.cs.Toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (secção 3.4)
* [http://CS.Nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
* [http://CS.Nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pacotes de normalização de resposta
**Normalização de resposta** é um esquema de normalização local que foi introduzida pela primeira vez ao Geoffrey Hinton, usados de ter, no documento [ImageNet Classiﬁcation com as redes Neurais Convolutional profundas](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Normalização de resposta é utilizada para ajudar a generalização em redes neural. Quando um neuron é acionando um nível muito elevado de ativação, uma camada de normalização resposta local suprime o nível de ativação dos neurons surrounding. Isto é feito utilizando três parâmetros (***α***, ***β***, e ***k***) e uma estrutura convolutional (ou forma neighborhood). Cada neuron na camada de destino ***y*** corresponde a um neuron ***x*** na camada de origem. O nível de ativação de ***y*** é fornecido pela seguinte fórmula, onde ***f*** é o nível de ativação de um neuron e ***Nx*** é kernel (ou o conjunto que contém os neurons no Neighborhood de ***x***), tal como definido pela seguinte estrutura convolutional:  

![][1]  

Os pacotes de normalização de resposta suportam todos os atributos convolutional exceto **partilha**, **MapCount**, e **ponderações**.  

* Se o kernel contém neurons no mapa mesmo como ***x***, o esquema de normalização é referido como **mesmo mapear normalização**. Para definir a normalização de mapa mesmo, a primeira coordenada no **InputShape** tem de ter o valor de 1.
* Se o kernel contém neurons na mesma posição geográfico como ***x***, mas os neurons outros Maps, o esquema de normalização denomina **across mapeia normalização**. Este tipo de normalização de resposta implementa um formulário de inhibition lateral inspirado pelo tipo encontrado no neurons reais, criar disputa pelos níveis de ativação grande entre saídas neuron calculadas no maps diferentes. A coordenada primeiro tem de ser um número inteiro maior do que um e não é superior ao número de mapas para definir através de normalização de maps, e o resto das coordenadas tem de ter o valor de 1.  

Porque os pacotes de normalização de resposta aplicam uma função predefinida para os valores de nó de origem para determinar o valor do nó de destino, têm sem estado trainable (ponderações ou preconceitos).   

**Alerta**: os nós na camada de destino correspondem a neurons que são nós centrais dos kernels. Por exemplo, se KernelShape [d] ímpar, em seguida, é *KernelShape [d] / 2* corresponde ao nó de central kernel. Se *KernelShape [d]* é mesmo, o nó central em *KernelShape [d] / 1 de 2*. Por conseguinte, se **preenchimento**[d] é False, o primeiro e último *KernelShape [d] / 2* nós não tem nós correspondentes na camada de destino. Para evitar esta situação, definir **preenchimento** como [true, true,..., true].  

Para além dos atributos de quatro descritos anteriormente, os pacotes de normalização de resposta também suportam os seguintes atributos:  

* **Alpha**: (obrigatório) Especifica um valor de vírgula flutuante que corresponde ao ***α*** na fórmula de anterior. 
* **Beta**: (obrigatório) Especifica um valor de vírgula flutuante que corresponde ao ***β*** na fórmula de anterior. 
* **O desvio**: (opcional) Especifica um valor de vírgula flutuante que corresponde ao ***k*** na fórmula de anterior. Assume a 1.  

O exemplo seguinte define um pacote de normalização de resposta utilizando estes atributos:  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

* A camada de origem inclui cinco maps, cada um com aof dimensão de 12, 12, perfazendo em nós de 1440. 
* O valor de **KernelShape** indica que se trata de uma camada de normalização mesmo mapa, onde o neighborhood é um retângulo de 3, 3. 
* O valor predefinido de **preenchimento** é False, deste modo, a camada de destino tem apenas 10 nós em cada dimensão. Para incluir um nó na camada de destino que corresponde a todos os nós na camada de origem, adicione o preenchimento = [' true ', ' true ', ' true ']; e altere o tamanho de RN1 [5, 12, 12].  

## <a name="share-declaration"></a>Declaração de partilha
NET # opcionalmente suporta definir múltiplos pacotes com ponderações partilhados. As ponderações de quaisquer dois pacotes podem ser partilhadas se as estruturas são os mesmos. A seguinte sintaxe define os pacotes com ponderações partilhados:  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }

    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name

    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec

    bundle-spec:
       layer-name    =>     layer-name

    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec

    bias-spec:
        1    =>    layer-name

    layer-name:
        identifier  

Por exemplo, a seguinte declaração de partilha Especifica os nomes de camada, com a indicação de que tanto as ponderações preconceitos devem ser partilhados:  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

* As funcionalidades de entrada são divididos em partições para duas camadas de entrada tamanho iguais. 
* As camadas ocultas computação, em seguida, funcionalidades de nível superiores em duas camadas de entrada. 
* A declaração de partilha Especifica que *H1* e *H2* tem de ser calculada da mesma forma do respetivas entradas correspondentes.  

Em alternativa, isto pode ser especificado com dois declarações de partilha separadas da seguinte forma:  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

Pode utilizar o formato abreviado apenas quando as camadas de conter um único pacote. Em geral, partilha é possível apenas quando a estrutura relevante é idêntica, o que significa que têm o mesmo tamanho, mesma geometria convolutional e assim sucessivamente.  

## <a name="examples-of-net-usage"></a>Exemplos de utilização do Net #
Esta secção fornece alguns exemplos de como pode utilizar o Net # para adicionar camadas ocultas, definir a forma que camadas ocultas interagem com outras camadas e criar redes convolutional.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definir uma simple personalizada a rede neuronal: exemplo de "Olá mundo"
Neste exemplo simples demonstra como criar um modelo de rede neuronal que tem uma único camada oculta.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

O exemplo ilustra alguns comandos básicos da seguinte forma:  

* A primeira linha define a camada de entrada (com o nome *dados*). Quando utiliza o **automática** palavra-chave, a rede neuronal inclui automaticamente todas as colunas de funcionalidade nos exemplos a entrada. 
* A segunda linha cria camada oculta. O nome *H* está atribuído à camada oculta, que tem 200 nós. Esta camada totalmente está ligada à camada de entrada.
* A terceira linha define a camada de saída (com o nome *Nã*), que inclui 10 nós de saída. Se a rede neuronal é utilizada para classificação, é um nó de saída por classe. A palavra-chave **sigmoid** indica que a função de saída é aplicada à camada de saída.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definir várias camadas ocultas: exemplo de visão do computador
O exemplo seguinte demonstra como definir uma rede neuronal ligeiramente mais complexa, com várias camadas ocultas personalizadas.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];

    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }

    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

Este exemplo ilustra várias funcionalidades do idioma de especificação as redes neurais:  

* A estrutura tem duas camadas de entrada, *Pixels* e *metadados*.
* O *Pixels* camada é uma camada de origem para os pacotes de ligação de duas, com camadas de destino, *ByRow* e *ByCol*.
* As camadas *recolher* e *resultado* são as camadas de destino em múltiplos pacotes de ligação.
* A camada de saída, *resultado*, é uma camada de destino em dois pacotes de ligação; um com o segundo nível oculta (recolha) como uma camada de destino e outra com a camada de entrada (metadados) como uma camada de destino.
* As camadas ocultas, *ByRow* e *ByCol*, especifique filtrada conectividade através de expressões de predicado. De forma mais precisa, no nó do *ByRow* em [x, y] está ligado a nós do *Pixels* que tem o primeiro índice coordenar igual para o nó do x coordenada, primeiro. Da mesma forma, o nó na *ByCol em [x, y] está ligado a nós do _Pixels* que tem o índice segundo coordenar dentro do nó da y coordenada, segundo.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definir uma rede convolutional para classificação de várias classes: exemplo de reconhecimento de dígitos
A definição da seguinte rede foi concebida para reconhecer números e ilustra alguns técnicas avançadas para personalizar uma rede neural.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


* A estrutura tem uma camada única de entrada, *imagem*.
* A palavra-chave **convolve** indica se as camadas denominado *Conv1* e *Conv2* são convolutional camadas. Cada um destes declarações de camada é seguida por uma lista dos atributos convolution.
* O net tem uma terceira oculta a camada, *Hid3*, que é completamente ligado à segunda camada oculta, *Conv2*.
* A camada de saída, *dígito*, está ligado apenas à terceira camada oculta, *Hid3*. A palavra-chave **todos os** indica que a camada de saída está completamente ligada à *Hid3*.
* A aridade do convolution é três (o comprimento das cadeias de identificação **InputShape**, **KernelShape**, **Stride**, e **partilha**). 
* O número de ponderações por kernel é *1 + **KernelShape**\[0] * **KernelShape**\[1] * **KernelShape** \[ 2] = 1 + 1 * 5 * 5 = 26. Ou 26 * 50 = 1300*.
* Pode calcular os nós em cada camada oculta da seguinte forma:
  * **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
  * **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
  * **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
* É possível calcular o número total de nós utilizando dimensionalidade declarada da camada, [50, 5, 5], da seguinte forma:  ***MapCount** * **NodeCount**\[0] * **NodeCount**\[1] * **NodeCount**\[2] = 10 * 5 * 5 * 5*
* Porque **partilha**[d] for False apenas para *d = = 0*, é o número de kernels  ***MapCount** * **NodeCount** \[0] = 10 * 5 = 50*. 

## <a name="acknowledgements"></a>Confirmações
O idioma Net # para personalizar a arquitetura de redes neurais foi desenvolvido na Microsoft por Shon Katzenberger (arquiteto de sistemas, Machine Learning) e Alexey Kamenev (engenheiro de Software, Microsoft Research). É utilizada internamente para do machine learning projetos e aplicações que vão de deteção de imagem a análise de texto. Para obter mais informações, consulte [neuronal redes no Azure ML - introdução ao Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)

[1]:./media/azure-ml-netsharp-reference-guide/formula_large.gif

