---
title: "Engenharia no ciência de dados da funcionalidade | Microsoft Docs"
description: "Explica o efeito de engenharia da funcionalidade e fornece exemplos da sua função no processo de melhoramento de dados de aprendizagem."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 3fde69e8-5e7b-49ad-b3fb-ab8ef6503a4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: 72a412c08e57491a306f405f400665e2b0d25a3c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="feature-engineering-in-data-science"></a>Engenharia da funcionalidade na ciência de dados
Este artigo explica o efeito de engenharia da funcionalidade e fornece exemplos da sua função no processo de melhoramento de dados de aprendizagem. Os exemplos utilizados para ilustrar este processo são desenhados a partir do Azure Machine Learning Studio. 

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Isto **menu** ligações para artigos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo de [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Funcionalidade engenharia tentativas para aumentar a potência preditiva de algoritmos de aprendizagem através da criação de funcionalidades de dados não processados que ajudam o facilitar o processo de aprendizagem. A seleção das funcionalidades e de engenharia é parte integrante TDSP descrito no [o que é o ciclo de vida do processo de ciência de dados de equipa?](overview.md) Engenharia da funcionalidade e seleção de são partes do **desenvolver funcionalidades** passo o TDSP. 

* **engenharia da funcionalidade**: Este processo tenta criar funcionalidades relevantes adicionais a partir as funcionalidades existentes não processadas nos dados e para aumentar a potência preditiva do algoritmo do learning.
* **seleção de funcionalidade**: Este processo seleciona o subconjunto das funcionalidades de dados original chave numa tentativa para reduzir dimensionalidade do problema formação.

Normalmente **engenharia da funcionalidade** é aplicado primeiro ao gerar funcionalidades adicionais e, em seguida, o **seleção de funcionalidade** passo é realizado para eliminar irrelevantes, redundantes ou altamente correlacionadas funcionalidades.

Os dados de preparação utilizados no machine learning, muitas vezes, podem ser melhorados pela extração das funcionalidades de dados não processados recolhidos. Um exemplo de uma funcionalidade engineered no contexto de aprender a classificar as imagens de carateres handwritten é a criação de um pouco a densidade mapa construída a partir de dados de distribuição de bits não processados. Este mapa pode ajudar a localizar as extremidades de carateres de forma mais eficiente do que simplesmente utilizar a distribuição não processada diretamente.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Criar as funcionalidades dos seus dados - engenharia da funcionalidade
Os dados de preparação é composta por uma matriz composta exemplos (registos ou as observações armazenadas nas linhas), cada um dos quais tem um conjunto de funcionalidades (os campos armazenados nas colunas ou variáveis). As funcionalidades especificadas na estrutura experimental espera caracterizam os padrões nos dados. Embora muitos dos campos de dados não processados podem ser diretamente incluídos no conjunto de funcionalidade selecionada utilizado para preparar um modelo, é frequentemente as maiúsculas e minúsculas que funcionalidades adicionais (foi desenvolvidas) tem de ser construídas a partir as funcionalidades dos dados não processados para gerar um conjunto de dados de formação avançada.

Que tipo de funcionalidades deve ser criado para melhorar o conjunto de dados quando se prepara um modelo? Funcionalidades foi desenvolvidas que melhoram a formação fornecem informações que melhor diferencia aos padrões nos dados. Espera-se as novas funcionalidades para fornecer informações adicionais que não é claramente capturada ou facilmente aparente no conjunto de funcionalidade original ou existente. Mas este processo é algo um art. Decisões de som e produtivas frequentemente requerem alguns conhecimentos de domínio.

Ao iniciar com o Azure Machine Learning, é mais fácil para grasp este processo concretely com exemplos fornecidos no Studio. Dois exemplos são apresentados aqui:

* Um exemplo de regressão [previsão do número de alugueres de bicicleta](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) numa experimentação supervisionada onde os valores de destino são conhecidos
* A utilizar um exemplo do texto extração classificação [funcionalidade hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exemplo 1: Adicionar funcionalidades temporais para um modelo de regressão
Vamos utilizar a experimentação "a pedido previsão de bikes" no Azure Machine Learning Studio para demonstrar como criar funcionalidades para uma tarefa de regressão. O objetivo desta fase experimental é prever a procura de bikes, ou seja, o número de alugueres de bicicleta dentro de uma específica mês/dia/hora. O conjunto de dados "bicicleta Rental UCI conjunto de dados" é utilizado como os dados de entrada não processados. Este conjunto de dados é baseado nos dados reais anónimos da empresa Capital Bikeshare que mantém uma rede de rental bicicleta no Washington D.C. nos Estados Unidos. O conjunto de dados representa o número de alugueres de bicicleta dentro de uma hora específica de um dia nos anos 2011 e ano de 2012 e contém 17379 linhas e colunas de 17. O conjunto de funcionalidades em bruto contém condições Meteorologia (humidade/temperatura/vento velocidade) e o tipo do dia (feriado/dia da semana). O campo para prever é o número de "cnt", que representa os alugueres de bicicleta dentro de uma hora específica e que intervalos de 1 977.

Com o objetivo de construir funcionalidades efeitas nos dados de formação, quatro regressão modelos são criados com o mesmo algoritmo, mas com quatro conjuntos de dados de formação diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada não processados, mas com um número crescente de funcionalidades definida. Estas funcionalidades estão agrupadas em quatro categorias:

1. A fim de semana funcionalidades para o dia previsto + de = Meteorologia + feriado + dia da semana
2. B = diversas bikes foram rented em cada 12 horas anteriores
3. C = diversas bikes foram rented em cada um dos dias anteriores 12 na mesma hora
4. D = diversas bikes foram rented em cada um das 12 semanas anteriores na mesma hora e o mesmo dia

Para além de um conjunto de funcionalidade, que já existe nos dados não processados originais, os outros três conjuntos de funcionalidades são criados através da funcionalidade processos de engenharia. Definir funcionalidade B capturas a pedido muito recente para os bikes. Funcionalidade definido C capturas a procura de bikes uma hora específica. Funcionalidade definido a pedido de captura de D para bikes determinada hora e dia específico da semana. Quatro formação conjuntos de dados cada incluem um conjunto de funcionalidades, D + B, D + B + C e D + B + C + D, respetivamente.

Na experimentação do Azure Machine Learning, estes quatro conjuntos de dados de formação são formados através de quatro ramos do conjunto de dados de entrada pré-processado. Exceto o ramo mais à esquerda, cada um destes ramos contém um [executar Script do R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) módulo, em que as funcionalidades derivadas (funcionalidade definido B, C e D) são respetivamente construídas e anexadas ao conjunto de dados importado. A figura seguinte demonstra o script do R utilizado para criar o conjunto de funcionalidades B no segundo ramo esquerdo.

![criar funcionalidades](./media/create-features/addFeature-Rscripts.png)

Uma comparação dos resultados de desempenho dos modelos de quatro está resumida na tabela seguinte: 

![comparação de resultado](./media/create-features/result1.png)

Obter os melhores resultados, são apresentados as funcionalidades de D + B + C. Tenha em atenção que a taxa de erros diminui quando o conjunto de funcionalidades adicionais estão incluídos os dados de preparação. Verifica o presumption que o conjunto de funcionalidades B, C fornece informações adicionais de relevantes para a tarefa de regressão. Mas, adicionar a funcionalidade de D aparentemente, não forneça quaisquer redução adicional da taxa de erros.

## <a name="example2"></a>Exemplo 2: Criar funcionalidades na extração de texto
Engenharia da funcionalidade é amplamente aplicada em tarefas relacionadas com a extração de texto, tal como a análise de classificação e dados de sentimento do documento. Por exemplo, quando pretende classificar documentos em várias categorias, um pressuposto típico é que o word/expressões incluídos numa categoria documento são menos provável é que ocorra noutra categoria do documento. Por outras palavras, a frequência da distribuição de palavras/expressões é capaz de caracterizam categorias de documento diferente. Em aplicações de extração de texto, porque partes individuais de conteúdos de texto normalmente servirem como os dados de entrada, a funcionalidade de processos de engenharia necessária para criar as funcionalidades que envolvam word/frase frequências.

Para alcançar esta tarefa, denominada uma técnica **funcionalidade hash** é aplicado a forma eficiente ativar funcionalidades de texto arbitrários para índices. Em vez de associação de cada funcionalidade de texto (palavras/expressões) para um índice específico, este funções de método por aplicar uma função de hash para as funcionalidades e utilizar os respetivos valores de hash como índices diretamente.

No Azure Machine Learning, há um [funcionalidade hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) módulo que cria estes word/frase funcionalidades comodamente. Figura seguinte mostra um exemplo de como utilizar este módulo. O conjunto de dados de entrada contém duas colunas: a classificação de livro entre 1 e 5 e o conteúdo de revisão real. O objetivo deste [funcionalidade hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) módulo é para obter um bunch das novas funcionalidades que mostram a frequência de ocorrência da word(s) correspondente / rever phrase(s) dentro do livro específico. Para utilizar este módulo, conclua os seguintes passos:

* Em primeiro lugar, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Segundo, como "bitsize de Hashing" 8, que significa a 2 ^ 8 = 256 funcionalidades serão criadas. O word fase em todo o texto será convertido para 256 índices. Os parâmetro "Hashing bitsize" intervalos entre 1 e 31. O word(s) phrase(s) são menor probabilidade de ser colocado em hash para o mesmo índice se defini-la de ser um número maior.
* Terceira, defina o parâmetro "N-grams" como 2. Este valor obtém a frequência de ocorrência de unigrams (uma funcionalidade para cada uma única palavra) e bigrams (uma funcionalidade para cada par de palavras adjacentes) a partir do texto de entrada. O parâmetro "N-grams" intervalos de 0 a 10, que indica o número máximo de palavras sequenciais a ser incluído numa funcionalidade.  

![Módulo "Funcionalidade Hashing"](./media/create-features/feature-Hashing1.png)

A figura seguinte mostra que estes nova funcionalidade aspeto.

![Exemplo de "Funcionalidade Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
Funcionalidades selecionadas foi desenvolvidas e aumentam a eficiência do processo de preparação, tenta extrair as informações chaves contidas nos dados. Também podem melhorar o power destes modelos de classificar os dados de entrada com precisão e prever resultados de interesse mais robustly. Engenharia da funcionalidade e a seleção também podem combinar para tornar a aprendizagem mais viáveis tractable. Isto é feito através da Otimização e, em seguida, reduzir o número de funcionalidades necessárias para calibrar os ou preparar um modelo. Matematicamente falando, as funcionalidades selecionadas para preparar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões de dados e, em seguida, prever resultados com êxito.

Não é sempre necessariamente para efetuar a seleção de engenharia ou funcionalidade de funcionalidades. É necessária ou não depende dos dados para manualmente ou recolhidos, o algoritmo selecionado e o objetivo da experimentação.

