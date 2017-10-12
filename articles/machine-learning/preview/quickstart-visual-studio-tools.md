---
title: "Artigo de início rápido para Visual Studio Code Tools para Machine Learning no Azure | Microsoft Docs"
description: "Este artigo descreve como começar a utilizar o Visual Studio Code Tools para Machine Learning, desde criar uma experimentação a preparar um modelo e operar um serviço web."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: get-started-article
ms.date: 09/12/2017
ms.openlocfilehash: 680c1afab1af31cfef51b1c82d2db49f452ba6ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools para IA
O Visual Studio Code Tools para IA é uma extensão de desenvolvimento para criar, testar e implementar soluções de Aprendizagem Profunda e IA. Proporciona integração total com o Azure Machine Learning, nomeadamente uma vista de histórico de execução que detalha o desempenho de preparações anteriores e métricas personalizadas. Oferece uma vista de explorador de amostras que permite procurar e efetuar o arranque de sistema do novo projeto com o [Microsoft Cognitive Toolkit (anteriormente designado CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org)e outra arquitetura de aprendizagem profunda. Por fim, fornece um explorador de destinos de computação que permite submeter tarefas de preparação de modelos em ambientes remotos como as Máquinas Virtuais do Azure ou os servidores Linux com GPU. 
 
## <a name="getting-started"></a>Introdução 
Para começar, terá primeiro de transferir e instalar o [Visual Studio Code](https://code.visualstudio.com/Download). Assim que abrir o Visual Studio Code, efetue os seguintes passos:
1. Clique no ícone de extensão na barra de atividade. 
2. Pesquise por "Visual Studio Code Tools para IA". 
3. Clique no botão **Instalar**. 
4. Após a instalação, clique no botão **Recarregar**. 

Depois de recarregar o Visual Studio Code, a extensão é ativada. [Saiba mais sobre a instalação de extensões](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Explorar amostras de projeto
O Visual Studio Code Tools para IA inclui um explorador de amostras. O explorador de amostras torna mais fácil detetar amostras e experimentá-las com apenas alguns cliques. Para abrir o explorador, proceda da seguinte forma:   
1. Abra a paleta de comandos (Ver > **Paleta de Comandos** ou **Ctrl + Shift + P**).
2. Introduza "Amostra IA". 
3. Obtém uma recomendação para "IA: Abrir Explorador de Amostras do Azure ML". Selecione e prima enter. 

Em alternativa, pode clicar no ícone do explorador de amostras.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Criar um novo projeto a partir do explorador de amostras 
Pode procurar diferentes amostras e obter mais informações sobre as mesmas. Comece a procurar até encontrar a amostra "Classifying Iris". Para criar um novo projeto com base nesta amostra, faça o seguinte:
1. Clique no botão instalar na amostra de projeto e tenha em atenção os comandos apresentados que o irão guiar ao longo da criação de um projeto novo. 
2. Escolha um nome para o projeto; por exemplo, "Iris".
3. Escolha um caminho de pasta para criar o projeto e prima enter. 
4. Selecione uma área de trabalho existente e prima enter.

O projeto é criado.

> [!TIP]
> Tem de ter sessão iniciada para aceder aos seus recursos do Azure. No terminal incorporado, introduza "início de sessão az" e siga as instruções. 

## <a name="submitting-experiment-with-the-new-project"></a>Submeter a experimentação com o novo projeto
Para o novo projeto que está a ser aberto no Visual Studio Code, iremos submeter uma tarefa ao nosso destino de computação diferente (local e VM com docker).
O Visual Studio Code Tools para IA fornece várias formas de submeter uma experimentação. 
1. Menu de contexto (clique com o botão direito do rato) - **IA: Submeter Tarefa**.
2. Na paleta de comandos: "IA: Submeter Tarefa".
3. Em alternativa, pode executar o comando diretamente utilizando a CLI do Azure e comandos do Machine Learning num terminal incorporado.

Abra iris_sklearn.py, clique com o botão direito do rato e selecione **IA: Submeter Tarefa**.
1. Selecione a plataforma: "Azure Machine Learning".
2. Selecione a configuração de execução: "Docker-Python".

> [!NOTE]
> Se está a submeter uma tarefa pela primeira vez, receberá uma mensagem "Nenhuma configuração do Machine Learning encontrada, a criar...". É aberto um ficheiro JSON, guarde-o (**Ctrl + G**).

Depois de submeter a tarefa, o terminal incorporado mostra o progresso da execução. 

## <a name="view-list-of-jobs"></a>Ver a lista de tarefas
Depois de submeter as tarefas, pode listá-las a partir do histórico de execução.
1. Abra a paleta de comandos (Ver > **Paleta de Comandos** ou **Ctrl + Shift + P**).
2. Introduza "Lista de AI".
3. Obtém uma recomendação para "IA: Lista de Tarefas", selecione e prima enter.
4. Selecione a plataforma: "Azure Machine Learning".

É aberta a Vista de Lista de Tarefas que apresenta todas as execuções e algumas informações relacionadas.

## <a name="view-job-details"></a>Ver detalhes da tarefa
Com a Vista de Lista de Tarefas ainda aberta, clique na primeira execução na lista.
Para obter uma descrição profunda dos resultados de uma tarefa, clique no primeiro **ID da tarefa** para ver as informações detalhadas. 

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Como configurar o Azure Machine Learning para trabalhar com um IDE](./how-to-configure-your-IDE.md)
