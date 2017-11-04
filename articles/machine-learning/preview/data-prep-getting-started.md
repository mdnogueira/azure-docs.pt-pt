---
title: "Introdução aos dados de preparação do Azure Machine Learning | Microsoft Docs"
description: "Este é o guia de introdução para a secção de preparação de dados do AML workbench"
author: cforbe
ms.author: cforbe
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: faf6e557914a33454229b5830419b9ef6f200a8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-data-preparation"></a>Introdução à preparação de dados

Bem-vindo ao dados preparação introdução ao guia de introdução. 

Preparação de dados fornece um conjunto de ferramentas para explorar de forma eficiente, compreender e corrigir os problemas nos dados. Permite-lhe consumir dados de várias formas e transformar dados em dados cleansed que melhor são adequados para utilização a jusante.

Preparação de dados é instalada como parte da experiência do Azure Machine Learning Workbench.  Utilize a preparação de dados localmente ou implementar um cluster de destino e na nuvem como um ambiente de tempo de execução ou de execução.

O tempo de execução do tempo de design extensibilidade utiliza o Python e depende de vários bibliotecas de Python, tais como Pandas. Como com outros componentes do Azure ML Workbench não é necessário para instalar o Python, é instalado por si. No entanto se precisar de instalar as bibliotecas adicionais estas bibliotecas têm de ser instalada no diretório do Azure ML Workbench Python não habitual Python diretório. Podem encontrar mais detalhes sobre como instalar pacotes [aqui](data-prep-python-extensibility-overview.md).

Um projeto é necessário antes de poder utilizar preparação de dados, uma vez que projeto ser criado, pode preparar dados. 

Navegue para a secção de dados do projeto, selecionando o ícone de dados ![ícone de origem de dados](media/data-prep-getting-started/data-source-icon.png) no lado esquerdo do ecrã.  Clique em "+" novamente ao **adicionar uma origem de dados**. O Assistente de origem de dados deve iniciar e adiciona uma **origem de dados** (.dsource) de ficheiro ao projeto depois de concluir o assistente. Por predefinição, a vista dos dados é a grelha. Acima grelha, também é possível selecionar a vista de métricas. Na vista de métricas, são apresentadas as estatísticas de resumidas.  Depois de rever as estatísticas de resumidas, clique em **preparar** na parte superior do ecrã. [Obter mais informações sobre o Assistente de origem de dados](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Blocos modulares de preparação de dados ##
### <a name="the-package"></a>O pacote ###

Um pacote é o contentor principal para o seu trabalho. Um pacote é o artefacto que é guardado carregado a partir do disco. Ao trabalhar no interior do cliente, o pacote está constantemente AutoSaved em segundo plano. 

Os saída resultados de um pacote pode ser explorou no Python ou através de um bloco de notas do Jupyter.

Um pacote pode ser executado em vários tempos de execução, incluindo local Python, Spark (incluindo no Docker) e o HDInsight.

Um pacote contém um ou mais Dataflows que são os passos e transformações aplicadas aos dados.

Um pacote pode utilizar outro pacote como uma origem de dados (referido como um fluxo de dados de referência).

### <a name="the-dataflow"></a>O fluxo de dados ###
Um fluxo de dados tem uma origem e transforma opcional, que são dispostos através de uma série de passos e destinos opcionais. Clicar num passo executa novamente as origens e transformações anteriores e incluindo, o passo selecionado.  Os dados transformados através esse passo são mostrados dentro da grelha. Podem ser adicionados, mover e eliminados dentro de um fluxo de dados através da lista de passo passos.

A lista de passo no lado direito do cliente pode ser aberta e fechada para fornecer mais espaço do ecrã.

Vários Dataflows pode existir na IU de cada vez, cada fluxo de dados é representado como um separador na IU.

### <a name="the-source"></a>A origem
É uma origem de onde vêm os dados e o formato está a ser. Um pacote de preparação de dados sempre origens dos dados a partir de outro dados Flow(Data Source). É este fluxo de dados que contém as informações de referência. Cada origem tem uma experiência de utilizador diferente para permitir que seja configurado. A origem produz uma vista tabular / "retangular" dos dados. Se a origem de dados foi originalmente "direito desalinhado", a estrutura com a em seguida, é normalizada ser "retangular". [O apêndice 2 fornece a lista atual de fontes suportadas](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>A transformação ###
Transformações consumam dados num formato especificado, execute algumas operações nos dados (como alterar o tipo de dados) e, em seguida, produzem os dados no novo formato. Cada transformação tem as suas próprias IU e behavior(s). Encadeamento várias transformações em conjunto através de passos no fluxo de dados é o núcleo da funcionalidade de preparação de dados. [Apêndice 3 fornece a lista atual de transformações suportadas](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>O inspector ###

Inspetores são visualizações de dados e estão disponíveis para melhorar a compreensão dos dados.  Compreender a dados e os dados qualidade emite ajuda a que decidir quais ações (transformações) deverão ser executadas. Alguns Inspetores suportam ações que geram transformações. Por exemplo, a contagem de valores Inspector permite-lhe selecionar um valor e, em seguida, aplicar um filtro para incluir esse valor ou para excluir esse valor. Inspetores também podem fornecer contexto para transformações. Por exemplo, se selecionar uma ou mais colunas altera as transformações possíveis que podem ser aplicadas.

Uma coluna pode ter vários Inspetores em qualquer ponto no tempo (por exemplo, estatísticas de coluna e um histograma). Também pode haver instâncias de um Inspector em várias colunas. Por exemplo, todas as colunas numéricas ter histogramas ao mesmo tempo.

Inspetores constar a criação de perfis bem na parte inferior do ecrã.  Maximize inspetores para vê-los maior dentro da área de conteúdo principal. Considere a grelha de dados como o inspector predefinido. Qualquer Inspector pode ser expandido para a área de conteúdo principal. Minimizar inspetores dentro da área de conteúdo principal para a criação de perfis bem. Personalize um Inspector clicando no ícone de lápis dentro de Inspector. Reordenação Inspetores dentro do utilizando bem arrastar e largar.

Alguns Inspetores suportam o modo de "Halo". Este modo mostra o estado ou um valor antes da transformação último foi aplicada. O valor antigo é apresentado no cinzento com o valor atual em primeiro plano e mostra o impacto de uma transformação. [Apêndice 4 fornece a lista atual de Inspetores suportados](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>O destino
 Um destino é onde a escrita/exportar os dados depois de preparar num fluxo de dados. Um fluxo de dados fornecido pode ter vários destinos. Cada destino tem uma experiência de utilizador diferente para permitir que seja configurado. O destino consome dados num formato "retangular" / tabela e escreve-algumas localização num formato especificado. [Apêndice 5 fornece a lista atual de destinos suportados](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Utilizar a preparação de dados ###
Preparação de dados assume uma básica quinto passo metodologia/abordagem a preparação de dados.

#### <a name="step-1-ingestion"></a>Passo 1: ingestão ####
Importar dados de preparação de dados utilizando o **Adicionar origem de dados** opção na vista de projeto.  Todos os ingestão inicial dos dados é processada através do Assistente de origem de dados.

#### <a name="step-2-understandprofile-the-data"></a>Passo 2: Compreender/perfil de dados ####

Em primeiro lugar, observe a barra de qualidade de dados na parte superior de cada coluna. Verde representa as linhas que têm valores. Cinzento representa linhas com um etc null, de valor em falta. Vermelho indica valores de erro. Coloque o cursor sobre a barra de obter uma descrição com os números exatos de linhas em cada um dos registos de três. Utiliza a barra de qualidade de dados uma escala logarítmica verifique sempre, por isso, os números reais para perceber aproximada para o volume de dados em falta.

![colunas](media/data-prep-getting-started/columns.png)

Em seguida, utilize uma combinação de outros Inspetores plus grelha para compreender melhor as características dos dados.  Inicie formulating hypotheses sobre a preparação de dados necessários para análise adicional. A maioria das inspetores funcionam em apenas uma coluna ou um pequeno número de colunas.  

É provável que vários Inspetores em várias colunas são necessárias para compreender os dados. Pode percorrer vários Inspetores na criação de perfis bem. Dentro do também, pode também mover Inspetores para o cabeçalho da lista para poder vê-los na área de imediatamente visível.

![inspetores](media/data-prep-getting-started/inspectors.PNG)

São fornecidos Inspetores diferentes para as variáveis de categórico vs contínua, as colunas. O menu de Inspector activa e desactiva opções consoante o tipo de variáveis/colunas que tiver.

Ao trabalhar com conjuntos de dados grande que tem várias colunas, uma abordagem de trabalhar com subconjuntos pragmatic é recomendada. Esta abordagem inclui concentrar-se num pequeno número de colunas (por exemplo, 5-10), prepará-los e, em seguida, resolver as restantes colunas. O inspector grelha suporta a criação de partições vertical das colunas e, se tiver mais do que 300 colunas, em seguida, terá de "página" através de-los.
 

#### <a name="step-3-transform-the-data"></a>Passo 3: Transformar os dados ####
Transformações alteram os dados e permitem a execução dos dados para suportar a hipótese de trabalho atual. Transformações são apresentados como passos na lista de passo no lado direito. É possível "levar tempo" através da lista de passo ao clicar em qualquer ponto arbitrário na lista de passo.

Um ícone verde à esquerda de um determinado passo indica que foi executada e os dados refletem a execução de transformação. Uma barra vertical à esquerda do passo indica o estado atual dos dados nos Inspetores.

![passos](media/data-prep-getting-started/steps.PNG)

Tente efetuar pequenas alterações frequentes aos dados e para validar (passo 4) após cada alteração à medida que evolui da hipótese.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Passo 4: Verificar o impacto da transformação. 
Decida se a hipótese estava correto. Se correto, desenvolver a hipótese de seguinte e repita os passos 2-3 para o novo. Se incorreto, em seguida, anular a última transformação e desenvolver um novo hipótese e repita os passos 2-3.

A forma como principal para determinar se a transformação tinha o impacto direita consiste em utilizar os Inspetores. Utilizar existentes. Utilize Inspetores com Halo efeito ativado ou iniciar vários Inspetores para ver os dados em indicado pontos no tempo.

![Halo inspector](media/data-prep-getting-started/halo1.PNG) ![Halo inspector](media/data-prep-getting-started/halo2.PNG)

Para anular uma transformação, aceda a lista de passos no lado direito da IU. (O painel de lista de passos pretender popped enviados de volta. Para o abrir, clique em apontar de divisa dupla esquerda). No painel, selecione a transformação que foi executada a que pretende anular. Selecione na lista pendente no lado direito de bloco de IU. Selecione **editar** para efetuar alterações ou **eliminar** para remover a transformação de lista de passos e o fluxo de dados.

#### <a name="step-5-output"></a>Passo 5: resultado 
Quando terminar com a preparação de dados, pode escrever o fluxo de dados para uma saída. Um fluxo de dados pode ter várias saídas. No menu de transformações, pode selecionar qual a saída de que pretende que o conjunto de dados escritos como. Também pode selecionar o destino de saída. 

## <a name="list-of-appendices"></a>Lista de Appendices 
[Apêndice 2 - origens de dados suportadas](data-prep-appendix2-supported-data-sources.md)  
[Transforma apêndice 3 - suportados](data-prep-appendix3-supported-transforms.md)  
[Apêndice 4 - Inspetores suportados](data-prep-appendix4-supported-inspectors.md)  
[Apêndice 5 - destinos suportados](data-prep-appendix5-supported-destinations.md)  
[Apêndice 6 - expressões de filtro de exemplo no Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Apêndice 7 - exemplo transformar expressões de fluxo de dados no Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Apêndice 8 - origens de dados de exemplo no Python](data-prep-appendix8-sample-source-connections-python.md)  
[Apêndice 9 - ligações de destino de exemplo no Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Apêndice 10 - exemplo coluna transforma no Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Veja Também

[Tutorial de preparação de dados avançadas](tutorial-bikeshare-dataprep.md)