---
title: "Classificação de receitas - equipa o processo de ciência de dados - Azure Machine Learning | Microsoft Docs"
description: "Como utilizar o modelo de processo de ciência de dados de agrupamento para criar um projeto no Azure Machine Learning que classifica incomes E.U.A.."
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
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: 3e92687657b4e80e75fd869da454970622f7178c
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Classificação de receitas com o projeto de equipa dados ciência processo (TDSP)

## <a name="introduction"></a>Introdução

A uniformização da estrutura e a documentação de ciência de dados projetos, que é ancorada um estabelecida [ciclo de vida de ciência de dados](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), é importante para facilitar o início efetiva colaboração no equipas de ciência de dados. Criação de projetos do Azure Machine Learning com o [processo de ciência de dados de equipa (TDSP)](https://github.com/Azure/Microsoft-TDSP) modelo fornece uma estrutura para essa uniformização.

Iremos tinha anteriormente lançadas um [repositório do GitHub para a estrutura de projeto TDSP e modelos](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Mas não foi possível, até agora instanciar a estrutura TDSP e modelos dentro de uma ferramenta de ciência de dados. Iremos agora tiver ativado a criação de projetos do Azure Machine Learning que são instanciado com [modelos de estrutura e a documentação de TDSP do Azure Machine Learning](https://github.com/amlsamples/tdsp). São fornecidas instruções sobre como utilizar a estrutura TDSP e modelos no Azure Machine Learning [aqui](https://aka.ms/how-to-use-tdsp-in-aml). Aqui fornecemos um exemplo de como um projeto de aprendizagem real pode ser criado utilizando a estrutura TDSP, preenchido com o código de projeto específico, artefactos e a documentos e executado no Azure Machine Learning.

## <a name="link-to-github-repository"></a>Associar ao repositório do GitHub
Fornecemos documentação resumo [aqui](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) sobre a amostra. Pode encontrar documentação mais extensa no site do GitHub.

### <a name="purpose"></a>Objetivo
O objetivo principal deste exemplo é para mostrar como instanciar e executar um machine learning projeto com o [processo de ciência de dados de equipa (TDSP)](https://github.com/Azure/Microsoft-TDSP) estrutura e modelos no Azure Machine Learning. Para esta finalidade, utilizamos o conhecidos [1994, feitas dados de Census-do repositório UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/adult). A tarefa de modelação é prever E.U.A. anuais receitas as classes de informações nos Census (por exemplo, duração provável de antecipação, nível de educação, país de origem, etc.)

### <a name="scope"></a>Âmbito
 * Exploração de dados, formação e implementação de um modelo de machine learning que abordar o problema de predição descrito na descrição geral de cenário de utilização. 
 * Execução do projeto no Azure Machine Learning utilizando o modelo de processo de ciência de dados de equipa (TDSP) do Azure Machine Learning para este projeto. Para execução do projeto e relatórios, vamos utilizar o ciclo de vida TDSP.
 * Operationalization da solução diretamente a partir do Azure Machine Learning nos serviços de contentor do Azure.

 O projeto destaca várias funcionalidades do Azure Machine Learning, como TDSP estrutura Instanciação do objeto e a utilização, a execução de código em blocos de notas do Jupyter, bem como os ficheiros de Python e fácil operationalization nos serviços de contentor Azure utilizando o Docker e Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Ciclo de vida de processo de ciência de dados (TDSP) de equipa
Consulte [equipa ciclo de vida de processo (TDSP) de ciência de dados](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Pré-requisitos
### <a name="required-subscription-hardware-software"></a>Obrigatório: subscrição, hardware, software
1. Um Azure [subscrição](https://azure.microsoft.com). Pode obter um [livre subscrição](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) para executar este exemplo também.
2. Um [Máquina Virtual de ciência de dados do Azure (DSVM) do Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (tamanho da VM: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), com 4 CPUs virtuais e 14 Gb de RAM). Embora testado um DSVM do Azure, é provável que funcionam em qualquer máquina Windows 10.
3. Reveja a documentação no Azure Machine Learning e o respetivo relacionados serviços (consulte abaixo as ligações).
4. Certifique-se de que tem instalado corretamente Azure Machine Learning, o [guia de instalação de início rápido](quickstart-installation.md).

O conjunto de dados para este exemplo é a partir do repositório de ML UCI [[ligação]](https://archive.ics.uci.edu/ml/datasets/adult). Este é obtida a partir da base de dados de nos Census 1994, feitas e contém informações de cerca de 50 000 indivíduos census e receitas. Este é o conjunto de dados estruturado ter numéricos e funcionalidades categórico e um categórico destino consiste em duas categorias de receitas ('> 50 K' ou ' < = 50 K'). 

### <a name="optional-version-control-repository"></a>Opcional: Repositório de controlo de versão
Se gostaria de guardar e versão do seu projeto e o respetivo conteúdo, tem de ter um repositório de controlo de versão onde isto pode ser feito. Pode introduzir a localização do repositório de Git ao criar o novo projeto utilizando o modelo TDSP no Azure Machine Learning. Consulte [como utilizar o Git no Azure Machine Learning](using-git-ml-project.md) para obter mais detalhes.

### <a name="informational-about-azure-machine-learning"></a>Informativa: Sobre o Azure Machine Learning
* [FAQ – como começar](frequently-asked-questions.md)
* [Descrição geral](overview-what-is-azure-ml.md)
* [Instalação](quickstart-installation.md)
* [Execução](experimentation-service-configuration.md)
* [Utilizar TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Ler e escrever em ficheiros](how-to-read-write-files.md)
* [Using Git with Azure Machine Learning](using-git-ml-project.md) (Utilizar o Git com o Azure Machine Learning)
* [Implementar um modelo de ML como um serviço web](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Criar um novo projeto do workbench

Crie um novo projeto com este exemplo como um modelo:
1.  Abra máquina do Azure Learning Workbench
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "Incomes classificar E.U.A. - projeto TDSP" e selecione o modelo
5.  Clique em **Criar**.

Se fornecer uma localização de repositório de Git vazia durante a criação do projeto (na caixa de adequado), em seguida, desse repositório será preenchido com a estrutura de projeto e conteúdo após a criação do projeto.

## <a name="use-case-overview"></a>Descrição geral de cenário de utilização
O problema é compreender como socio económico dados capturados no Census nos podem ajudar a prever anuais receitas de indivíduos nos EUA. Com base nas funcionalidades Census, a tarefa do machine learning é prever as receitas de um indivíduo estiver acima $ 50 000 ou não (tarefa de classificação binária).

## <a name="data-description"></a>Descrição de dados
Para obter informações detalhadas sobre os dados, consulte o [Descrição](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) no repositório de UCI. 

Estes dados extraídos da base de dados Census Bureau encontrado em: https://www.census.gov/en.html. 


* Existem um total de 48,842 instâncias (antes de qualquer filtragem), a mistura de contínuos e discretos (preparar = 32, 561, testar = 16, 281)
* Probabilidade para a etiqueta ' > 50 K': % de 23.93 / % de 24.78 (sem unknowns)
* Probabilidade para a etiqueta ' < = 50 K': % de 76.07 / % de 75.22 (sem unknowns)  

* **DESTINO**: classe de receitas ' > 50 K', ' < = 50 K'. Estes são substituídos pelos 1 e 0 respetivamente na fase de preparação de dados.
* **FUNCIONALIDADES**: idade, classe de trabalho, o nível de educação, nível de educação, provável de antecipação, sex, horas de trabalho por semana, etc.


## <a name="project-structure-execution-and-reporting"></a>Estrutura do projeto, execução e de relatórios

### <a name="structure"></a>estrutura
Para este projeto, utilizamos os modelos de estrutura e a documentação de pasta TDSP (abaixo), que segue o [TDSP ciclo de vida](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Projeto ser criado com base nas instruções fornecidas [aqui](https://aka.ms/how-to-use-tdsp-in-aml). Depois de é preenchido com código e artefactos o projeto, a estrutura de procura da seguinte forma (consulte a estrutura de projeto boxed vermelho na figura abaixo).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Execução
Neste exemplo, vamos executar código no **ambiente de computação local**. Consulte a documentos do Azure Machine Learning para obter mais detalhes sobre [opções de execução](experimentation-service-configuration.md).

Executar um script de Python num tempo de execução local do Python é fácil:

    az ml experiment submit -c local my_script.py

Ficheiros de bloco de notas IPython podem ser duplo clique na estrutura do projeto no lado esquerdo da IU do Azure Machine Learning de e executar no servidor de bloco de notas Jypyter.


O fluxo de trabalho de ciências de dados passo a passo foi da seguinte forma:

* [**Aquisição de dados e a compreensão**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Dados foi transferidos no formato. csv de URLs no repositório de ML UCI [[ligação]](https://archive.ics.uci.edu/ml/datasets/adult). As funcionalidades, destino e os respetivos transformações são descritas em detalhe no ficheiro ProjectReport.md.

Código de aquisição de dados e a compreensão está localizado em: / código/01_data_acquisition_and_understanding.

Exploração de dados é efetuada utilizando o Python 3 [utilitário IDEAR (interativa de exploração de dados e relatórios)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) publicadas como parte de [suite TDSP das ferramentas de ciência de dados](https://github.com/Azure/Azure-TDSP-Utilities). Este utilitário ajuda-o para gerar relatórios de exploração de dados padronizado para dados que contém as funcionalidades categórico e numéricos e de destino. Detalhes da forma como o utilitário de Python 3 IDEAR foi utilizado é fornecida em baixo. 

A localização do relatório de exploração de dados final é [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). É apresentada uma vista do relatório IDEAR abaixo:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modelação**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Criámos dois modelos com 3-fold validação cruzada: floresta elástico Net aleatório. Utilizámos [amostragem de ponto de 59](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) para pesquisa de grelha aleatórias como uma estratégia para a otimização de parâmetro de modelo e de validação cruzada. A precisão dos modelos foram medida AUC (área em curva) a utilizar no conjunto de dados de teste. 

Código para a modelação está localizada em: / código/02_modeling.

AUC elástico Net e modelos de floresta aleatório foram > 0.85. Iremos guardar ambos os modelos no pickled.pkl ficheiros e o ROC rastreia para ambos os modelos de saída. Modelo de floresta AUC aleatório 0.92 e do modelo elástico Net era 0,90. Além disso, para interpretação de modelo, importância de funcionalidade para o modelo de floresta aleatório são num ficheiro. csv de saída e desenhados num pdf (primeiros 20 preditiva funcionalidades apenas).

Curva ROC de **modelo de floresta aleatório** no teste os dados são apresentados abaixo. Isto foi o modelo que tenha sido implementado:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

Funcionalidade importância (primeiros 20) do modelo de floresta aleatórias é mostrada abaixo. Mostra a quantidade de capital ganhos de funcionalidades, eduction, estado marital, tem de importância mais elevada de funcionalidade.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Implementação**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

O modelo de floresta aleatório foi implementada como um serviço web num cluster no [serviço de contentor do Azure (ACS)](https://azure.microsoft.com/services/container-service/). O ambiente de operationalization Aprovisiona Docker e Kubernetes do cluster para gerir a implementação de serviço web. Pode encontrar mais informações sobre o processo de operationalization [aqui](model-management-service-deploy.md). 

Código para a implementação está localizada em: / código/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Relatório de projeto final](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Os detalhes sobre cada uma das secções acima são fornecidos no relatório de projeto final compilado [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). O relatório de projeto também contém mais detalhes sobre o caso de utilização, métricas de desempenho do modelo, implementação e a infraestrutura em que o projeto foi desenvolvido e implementado.

O relatório de projeto, juntamente com o conteúdo da pasta do projeto todo e versão do repositório de controlo pode ser entregue ao cliente.


## <a name="conclusion"></a>Conclusão

Neste exemplo, vamos mostrou agora para utilizar a estrutura TDSP e modelos no Azure Machine Learning. Através de modelos de artefacto e a documentos, pode:
1. Definir corretamente objetivo e o âmbito de um projeto
2. Criar uma equipa de projeto com distribuída funções e responsabilidades
3. Estrutura e executar projetos, de acordo com as fases do ciclo de vida TDSP
4. Desenvolva relatórios padronizados através de utilitários de ciência de dados TDSP (por exemplo, o IDEAR exploração e visualização de relatório de dados).
5. Prepare um relatório de projeto de ciência de dados final que pode ser fornecido para um cliente

Esperamos que utilize esta funcionalidade do Azure Machine Learning para facilitar a uniformização e colaboração das equipas de ciência de dados.

## <a name="next-steps"></a>Passos Seguintes

Consulte referências abaixo para começar a utilizar:

[Como utilizar o processo de ciência de dados de equipa (TDSP) no Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Processo de ciência de dados de equipa (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Modelo de projeto TDSP do Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[E.u. a receitas de conjunto de dados de repositório UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)