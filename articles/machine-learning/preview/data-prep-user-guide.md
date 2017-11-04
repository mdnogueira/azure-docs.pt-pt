---
title: Guia aprofundado sobre como utilizar o Azure Machine Learning dados preparativos | Microsoft Docs
description: "Este documento fornece detalhes sobre como resolver problemas de dados com o Azure Machine Learning dados preparativos e uma descrição geral"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 1a1e12dbb5e32f62266ee6a3cdca9e781569e58c
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="data-preparations-user-guide"></a>Guia do utilizador de preparativos de dados 
A experiência de preparativos de dados do Azure Machine Learning fornece muitas das funcionalidades avançadas. Este artigo documentos as partes da experiência mais profundo existente.

### <a name="step-execution-history-and-caching"></a>Execução do passo, histórico e colocação em cache 
Dados preparativos passo histórico mantém uma série de caches por motivos de desempenho. Se selecionar um passo e chegar a uma cache, não executar novamente. Se tiver um bloco de escrita no fim do histórico do passo e Inverte anterior e descritos nos passos, mas não efetuar alterações, a operação de escrita não é acionada após a primeira vez. Uma operação de escrita de nova ocorre e substitui um anterior, se a:

- Efetue as alterações para o bloco de escrita.
- Adicione um novo bloco de transformação e movê-la acima o bloco de escrita, o que gera uma invalidação de cache.
- Altere as propriedades de um bloco acima o bloco de escrita, o que gera uma invalidação de cache.
- Selecione a atualização de uma amostra (assim invalidar todas as caches).

### <a name="error-values"></a>Valores de erro

Transformações de dados poderão falhar para um valor de entrada, porque esse valor não pode ser processado corretamente. Por exemplo, no caso de operações de adesão do tipo, a adesão falha se o valor da cadeia de entrada não pode ser convertido no tipo de destino especificado. Uma operação de adesão do tipo pode ser uma coluna do tipo de cadeia a converter para um tipo numérico ou booleano ou tentar duplicado uma coluna que não existe. (Esta falha ocorre como o resultado de mover o *eliminar coluna X* antes do *colunas duplicados X* operação.)

Nestes casos, dados preparativos produz um valor de erro como resultado. Valores de erro indicam que uma operação anterior falhou para o valor indicado. Internamente, está a ser tratados como um tipo de valor de primeira classe, mas a presença dos mesmos não alterar o tipo subjacente de uma coluna, mesmo que uma coluna é composta por inteiramente valores de erro.

Valores de erro são fáceis de identificar. Serem realçadas a vermelho e ler "Error." Para determinar o motivo para o erro, coloque o cursor sobre um valor de erro para ver uma descrição de texto da falha.

Valores de erro se propagarem. Após um erro ocorre a valor, esta propaga na maioria dos casos, como um erro através da maioria das operações. Existem três formas para substituir ou removê-los:

* Substituir
    -  Uma coluna com o botão direito e selecione **substituir valores de erro**. Em seguida, pode escolher um valor de substituição para cada valor de erro encontrado na coluna.

* Remover
    - Dados preparativos inclui filtros interativos manter ou remover valores de erro.
    - Uma coluna com o botão direito e selecione **filtro coluna**. Para manter ou remover valores de erro, crie um condicional com a condição *"é o erro"* ou *"não está erro".*

* Utilize uma expressão de Python condicionalmente operar nos valores de erro. Para obter mais informações, consulte o [secção em extensões de Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Amostragem
Um ficheiro de origens de dados aceita dados não processados de uma ou mais origens, a partir do sistema de ficheiros local ou uma localização remota. O bloco de exemplo permite-lhe especificar se pretende trabalhar com um subconjunto dos dados através da geração de amostras. Muitas vezes, a utilizar uma amostra de dados em vez de um conjunto de dados grande leva a melhorar o desempenho quando realizar operações em passos posteriores.

Para cada ficheiro de origens de dados, vários exemplos podem ser gerados e armazenados. No entanto, apenas um exemplo pode ser definido como o exemplo de Active Directory. Pode criar, editar ou eliminar amostras no Assistente de origem de dados ou ao editar o bloco de exemplo. Quaisquer ficheiros de dados preparações que façam referência a uma origem de dados inerentemente utilizam o exemplo especificado no ficheiro de origens de dados.

Existem várias estratégias de amostragem disponível, cada um com diferentes parâmetros configuráveis.

#### <a name="top"></a>Parte superior
Esta estratégia pode ser aplicada aos ficheiros de locais ou remotos. Demora as primeiras linhas N (especificadas pela contagem) para a origem de dados.

#### <a name="random-n"></a>N aleatório 
Esta estratégia só pode ser aplicada ficheiros locais. Demora aleatórias linhas (especificadas pela contagem) para a origem de dados. Pode fornecer um seed específico para garantir que é gerado o mesmo exemplo, desde que a contagem é também o mesmo.

#### <a name="random-"></a>% Aleatório 
Esta estratégia pode ser aplicada aos ficheiros de locais ou remotos. Em ambos os casos, uma probabilidade e um seed tem de ser fornecido, semelhante a estratégia de N aleatório.

Exemplos de ficheiros remotos, parâmetros adicionais tem de ser fornecidos:

- Gerador de exemplo 
  - Selecione um cluster do Spark ou Docker remoto de destino a ser utilizada para a geração de exemplo de computação. O destino de computação têm de ser criado para o projeto previamente pela for apresentada nesta lista. Siga os passos na secção "Criar um novo destino de computação" no [como utilizar GPU no Azure Machine Learning](how-to-use-gpu.md) para criar os destinos de computação.
- Armazenamento de exemplo 
  - Forneça uma localização de armazenamento intermédio para armazenar o exemplo remoto. Este caminho tem de ser um diretório diferente da localização do ficheiro de entrada.

#### <a name="full-file"></a>Ficheiro completo 
Esta estratégia só pode ser aplicada ficheiros locais, colocar o ficheiro completo para a origem de dados. Se o ficheiro é demasiado grande, esta opção pode atrasar futuras operações na aplicação. Pode encontrar mais adequado para utilizar uma estratégia de amostragem diferentes.


### <a name="fork-merge-and-append"></a>Bifurcar, intercalar e de acréscimo

Ao aplicar um filtro ao longo de um conjunto de dados, a operação divide os dados em dois conjuntos de resultados: um conjunto representa registos com êxito no filtro e outro conjunto é para os registos que não obedeçam a. Em ambos os casos, o utilizador pode escolher qual resultado definido para apresentar. O utilizador pode eliminar o outro conjunto de dados ou colocá-lo um novo fluxo de dados. A última opção é denominada forking.

Para bifurcar: 
1. Selecione um coluna, rato e selecione o **filtro** coluna.

2. Em **posso pretende para**, selecione **manter linhas** para apresentar o conjunto de resultados que transmite o filtro.

3. Selecione **remover linhas** para apresentar o conjunto de falha.

4. Depois de **condições**, selecione **criar fluxo de dados que contém as filtrado saída linhas** para bifurcar o resultado de apresentação não definido para um novo fluxo de dados.


Esta prática é frequentemente utilizada para separar fora de um conjunto de dados que necessita de preparação adicional. Depois de wrangle o conjunto de dados forked, é comum para intercalar os dados com o resultado definido no fluxo de dados original. Para executar uma intercalação (inversa de uma operação de bifurcação), utilize uma das seguintes ações:

- **Acrescente linhas**. Intercalar duas ou mais fluxos de dados verticalmente (row-wise). 
- **Acrescentar colunas**. Intercalar duas ou mais fluxos de dados horizontalmente (column-wise).


>[!NOTE]
>Acrescente a falha de colunas se ocorrer uma colisão de coluna.


Após uma operação de intercalação, um ou mais fluxos de dados referenciados por um fluxo de dados de origem. Dados preparativos notifica-o com uma notificação no canto inferior direito da aplicação, por baixo da lista de passos.


Qualquer operação no fluxo de dados referenciada requer que o fluxo de dados principal para atualizar o exemplo utilizado a partir do fluxo de dados referenciada. Que eventos, uma caixa de diálogo de confirmação substitui a notificação de referência de fluxo de dados no canto inferior direito. Caixa de diálogo confirma que terá de atualizar o fluxo de dados para sincronizar com as alterações a qualquer fluxos de dados de dependência.

### <a name="list-of-appendices"></a>Lista de appendices 
* [Origens de dados suportadas](data-prep-appendix2-supported-data-sources.md)  
* [Transformações suportadas](data-prep-appendix3-supported-transforms.md)  
* [Inspetores suportados](data-prep-appendix4-supported-inspectors.md)  
* [Destinos suportados](data-prep-appendix5-supported-destinations.md)  
* [Expressões de filtro de exemplo no Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Expressões do fluxo de dados de transformação do exemplo no Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Origens de dados de exemplo no Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Ligações de destino de exemplo no Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Transforma colunas de exemplo no Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
