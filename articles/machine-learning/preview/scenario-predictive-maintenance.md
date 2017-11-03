---
title: "Cenário do mundo Real de manutenção preditiva | Microsoft Docs"
description: "Preditiva manutenção Real cenário do mundo utilizando PySpark"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: dc73c052ad9e0fe12af5042289f304a0e48ae413
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>Cenário do mundo real de manutenção preditiva.

O impacto dos períodos de indisponibilidade do equipamento agendada pode ser detrimental para as empresas. É fundamental para, por conseguinte, o equipamento de campo manter em execução para maximizar o desempenho e de utilização e pela minimizar o período de indisponibilidade dispendioso, agendado. Antecipado identificação de problemas pode ajudar a alocar recursos de manutenção limitado de forma económica e melhorar a qualidade e fornecer os processos de cadeia. 

Para este cenário, utilizamos uma relativamente [dados em grande escala](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) a guiá-lo do utilizador através dos passos principais da ingestão de dados, funcionalidade engenharia, criação de modelo e, em seguida, por fim operationalization de modelo e a implementação. O código para todo o processo é escrito em PySpark e implementadas utilizando os blocos de notas do Jupyter Workbench do Azure ML. O modelo de melhor é finally operacionalizado utilizando a gestão de modelo do Azure Machine Learning para utilização num ambiente de produção para efetuar em tempo real predições de falha.   

## <a name="link-to-the-gallery-github-repository"></a>Ligação para o repositório do GitHub da Galeria

Segue-se a ligação para o repositório do GitHub pública: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Descrição geral de cenário de utilização

Um problema principais deparam por empresas na asset pesado indústrias é os custos significativos que estão associados a atrasos mechanical problemas. A maioria das empresas estão interessadas nas prever quando estes problemas surgem para proativamente impedi-los antes de ocorrem. O objetivo consiste em reduzir os custos ao reduzir o período de indisponibilidade e, possivelmente, aumentar a segurança. Consulte o [manual de comunicação social de manutenção preditiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance) para uma explicação detalhada de comum utilizar casos, bem como a abordagem de modelação utilizado para manutenção preditiva.

Este cenário tira partido ideias do manual de comunicação social com o objetivo de fornecer os passos para implementar um modelo preditivo para um cenário, o que se baseia num synthesis de vários problemas de empresas do mundo real. Neste exemplo reúne casos de utilização de elementos de dados comuns observados entre muitos manutenção preditiva.

É o problema de negócio para estes dados simulados prever problemas causado por falhas do componente. A pergunta de negócio, por conseguinte, é "*o que é a probabilidade de uma máquina fica inativo devido uma falha de um componente*?" Este problema é formatado como um problema de classificação de várias classes (vários componentes por máquina) e um algoritmo do machine learning é utilizado para criar o modelo preditivo. O modelo está preparado no histórico dados recolhidos a partir de computadores. Neste cenário, o utilizador passa através de vários passos para implementar a modelo de ambiente do Workbench do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Um [conta do Azure](https://azure.microsoft.com/en-us/free/) (gratuitas estão disponíveis).
* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguintes o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar uma área de trabalho.
* Azure Operationalization de aprendizagem máquina requer um ambiente de implementação de local e um [conta de gestão de modelo](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

Neste exemplo, pode ser executado em qualquer contexto de computação AML Workbench. No entanto, recomenda-se a executá-lo com, pelo menos, de 16 GB de memória. Este cenário foi criado e testado num computador Windows 10 com um padrão de DS4_V2 remoto [Máquina Virtual de ciência de dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Modelo operationalization foi feito utilizando 0.1.0a22 de versão da CLI do Azure ML.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto do Workbench

Crie um novo projeto com este exemplo como um modelo:
1.  Abra máquina do Azure Learning Workbench
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "Manutenção preditiva" e selecione o modelo
5.  Clique em **Criar**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparar o destino de cálculo de servidor do bloco de notas

Para executar no seu computador local, a partir do Workbench AML `File` menu, selecione o `Open Command Prompt` ou `Open PowerShell CLI`. Dentro da CLI do windows, execute os seguintes comandos:

`az ml experiment prepare --target docker --run-configuration docker`

Sugerimos que executar numa máquina de Virtual de ciência de dados para Linux (Ubuntu). Depois de ter configurado o DSVM, execute os seguintes dois comandos:

`az ml computetarget attach --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword] --type remotedocker`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

Com as imagens de docker preparadas, abra o servidor de bloco de notas do Jupyter no separador de blocos de notas do AML Workbench ou, para iniciar um servidor baseado em browser, execute: `az ml notebook start`.

Blocos de notas são armazenados no `Code` diretório no ambiente Jupyter. Iremos executar os blocos de notas sequencialmente, começando no primeiro (`Code\1_data_ingestion.ipynb`) bloco de notas. Quando abre cada bloco de notas, lhe for pedido para selecionar o kernel do computação. Escolha [Project_Name] modelos [Desired_Connection_Name] e clique em definir Kernel.

## <a name="data-description"></a>Descrição de dados

O [simulated dados](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) é composta por cinco ficheiros de valores separados por vírgulas (. csv). Siga as ligações para obter mais uma descrição mais detalhada dos conjuntos de dados.

* [Máquinas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): funcionalidades differentiating cada máquina. Por exemplo, a idade e o modelo.
* [Erro](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): O registo de erros contém erros de sem quebra acionados enquanto a máquina ainda está operacional. Estes erros não são considerados como falhas, mas poderá estar preditivos de um evento de falha de futuras. A data / hora do erro são arredondados a hora mais próxima, uma vez que os dados de telemetria são recolhidos a uma taxa por hora.
* [Manutenção](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): O registo de manutenção contém ambos os registos de agendados e manutenção. Manutenção agendada corresponde com regular inspeção dos componentes, manutenção agendada pode surgir na falha mechanical ou outra degradação do desempenho. A data / hora de manutenção são arredondados a hora mais próxima, uma vez que os dados de telemetria são recolhidos a uma taxa por hora.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): os dados de séries de tempo de telemetria é composta por uma tensão, rotação, pressão e vibration medidas de sensor recolhidas a partir de cada máquina em tempo real. Os dados são uma média ao longo de uma hora e armazenados nos registos de telemetria
* [Falhas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): falhas correspondem a substituições de componente no registo de manutenção. Cada registo contém o ID de máquina, a tipo de componente e a data de substituição e a hora. Estes registos são utilizados para criar a aprendizagem automática etiquetas que o modelo está a tentar prever.

Consulte o [ingestão de dados](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) cenário de notas do Jupyter na secção de código para transferir os conjuntos de dados não processados a partir do repositório do GitHub e criar os conjuntos de dados para esta análise PySpark.

## <a name="scenario-structure"></a>Estrutura do cenário
O conteúdo para o cenário está disponível na [repositório do GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

No repositório, há um [Leia-me](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) ficheiro, que descreve os processos de preparar os dados até que a criação de modelos alguns e, em seguida, operacionalizar um dos modelos melhor finally. Os blocos de notas do Jupyter quatro estão disponíveis no [código](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) pasta dentro do repositório.   

Em seguida Vamos descrever o fluxo de trabalho do cenário passo a passo. O cenário de ponto a ponto é escrito em PySpark e é dividido em quatro blocos de notas:

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Este bloco de notas transfere os cinco ficheiros de entrada. csv, efetua algumas preliminar limpeza e visualização. O bloco de notas converte os dados no formato de PySpark e armazena os resultados num contentor de Blobs do Azure para utilização na tarefa de engenharia da funcionalidade.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): Utilizando o conjunto de dados apagado do passo anterior, desfasamento e funcionalidades agregadas são criadas para as substituições de componente de sensores de telemetria e o número de erro, as informações da máquina estão associados aos dados de telemetria. As substituições relacionadas com a falha de componente são utilizadas para construir as etiquetas que descrevem o componente de sincronização falhou. Os funcionalidade identificados os dados são guardados num blob do Azure para o modelo de criação de tarefas.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): O conjunto de dados de funcionalidade com nome, a utilizar o bloco de notas modelação divide os dados em conjuntos de dados de formação e dev juntamente o carimbo de data / hora. O bloco de notas é a experiência de conjunto de configuração com `pyspark.ml.classification` modelos. Os dados de preparação é vectorized e o utilizador pode experimentar o um `DecisionTreeClassifier` ou um `RandomForestClassifier`, manipulação de sintonização para determinar o melhor desempenho modelo. O desempenho é determinado através da avaliação de estatísticas de medida no conjunto de dados do programador. Estas estatísticas são registadas no ecrã de tempo Workbench AML executar para o controlo. Em cada execução, o bloco de notas guarda o modelo resultante para o disco local com o kernel do bloco de notas Jupyter. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Utilizando o modelo de último guardado para disco (kernel de bloco de notas do Jupyter) local, este bloco de notas baseia-se os componentes para operacionalizar o modelo para um serviço web do Azure. Os ativos completa operacionais são compacted para o `o16n.zip` ficheiros armazenados no outro contentor de blob do Azure. O ficheiro zipped contém:

* `service_schema.json`O ficheiro de definição de esquema para a implementação. 
* `pdmscore.py`As funções de init() e run() requeridas pelo serviço web do Azure
* `pdmrfull.model`O diretório de definição do modelo.
    
 O bloco de notas testa as funções com a definição de modelo antes de empacotar os ativos de operationalization para implementação. Instruções de implementação são incluídas no fim do bloco de notas.

## <a name="conclusion"></a>Conclusão

Este cenário fornece o leitor de uma descrição geral de como construir uma solução de manutenção preditiva ponto a ponto utilizando PySpark dentro do ambiente de bloco de notas do Jupyter no Azure ML Workbench. O cenário também orienta o leitor sobre como o modelo de melhor pode ser facilmente operationalized e implementados utilizando o ambiente de gestão de modelo do Azure Machine Learning para utilização num ambiente de produção para efetuar em tempo real predições de falha. Em seguida, o leitor pode editar as partes relevantes de cenário para tapê-lo para as suas necessidades de negócio.  

## <a name="references"></a>Referências

Neste caso, utilize foi anteriormente desenvolveu em várias plataformas:

* [Modelo de solução da manutenção preditiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guia de modelação de manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Preditiva manutenção modelação Guia utilizando os serviços do SQL Server R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Bloco de notas do Python guia de modelação de manutenção preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Manutenção preditiva utilizando PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>Passos seguintes

Existem muitos cenários de exemplo disponíveis do Workbench do Azure Machine Learning que demonstram as funcionalidades adicionais do produto. 