---
title: "Preparar Dados para classificar o tutorial Iris em serviços do Machine Learning (pré-visualização) | Microsoft Docs"
description: "Este tutorial completo mostra como utilizar os serviços do Azure Machine Learning (pré-visualização) ponto a ponto. Esta é a parte 1 sobre a preparação de dados."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>Classificar Iris parte 1: preparar os dados
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados ponto a ponto integrada para os cientistas de dados profissionais prepararem dados, desenvolverem experimentações e implementarem modelos à escala da cloud.

Este tutorial é a primeira parte de uma série de três partes. Neste tutorial, vamos percorrer as noções básicas dos serviços do Azure Machine Learning (pré-visualização). Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um projeto no Azure Machine Learning Workbench
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar o pacote de preparação de dados

Para simplificar, este tutorial utiliza o intemporal [conjunto de dados flor de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). As capturas de ecrã são específicas do Windows, mas a experiência de macOS é praticamente idêntica.

## <a name="prerequisites"></a>Pré-requisitos
- Crie uma Conta de Experimentação do Azure Machine Learning
- Instalar o Azure Machine Learning Workbench

Pode seguir o [Início rápido de instalação e criação](quickstart-installation.md) para instalar a aplicação Azure Machine Learning Workbench. Esta instalação também inclui a interface de linha de comandos (CLI).

## <a name="create-a-new-project-in-azure-ml-workbench"></a>Criar um novo projeto no Azure ML Workbench
1. Inicie a aplicação Azure Machine Learning Workbench e inicie sessão, se necessário. No painel **PROJETOS**, clique no ícone **+** para criar um **Novo Projeto**.

   ![nova área de trabalho](media/tutorial-classifying-iris/new_ws.png)

2. Preencha os detalhes em **Criar Novo Projeto**. 

   ![Novo Projeto](media/tutorial-classifying-iris/new_project.png)

   - Preencha o campo **Nome do projeto** com um nome para o projeto. Por exemplo, utilize o valor **myIris**.
   - Escolha o **Diretório de projeto** no qual é criado o projeto. Por exemplo, utilize o valor **C:\Temp**. 
   - Introduza a **Descrição do projeto**. 
   - O campo **Repositório de Git** é opcional e pode ficar em branco. Pode indicar um repositório de Git vazio existente (um repositório sem um ramo principal) no VSTS. Ao fazê-lo, poderá ativar cenários de roaming e partilha mais rapidamente. Para obter mais informações, consulte o artigo [Utilizar o repositório de Git](using-git-ml-project.md). 
   - Escolha uma **Área de Trabalho**. Por exemplo, este tutorial utiliza **IrisGarden**. 
   - Selecione o modelo **Classifying Iris** na lista de modelos de projeto. 

3. Clique no botão **Criar** para criar o projeto. O projeto é agora criado e aberto para si.

## <a name="create-a-data-preparation-package"></a>Criar um pacote de preparação de dados
1. Abra o ficheiro **iris.csv** a partir da **Vista de Ficheiros**. O ficheiro é uma tabela simples com 5 colunas e 150 linhas. Tem quatro colunas de funcionalidades numéricas e uma coluna de destino de cadeia. Não tem cabeçalhos de coluna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Não é recomendado incluir ficheiros de dados na pasta do projeto, especialmente quando o tamanho do ficheiro é grande. Incluímos o ficheiro **iris.csv** neste modelo para fins de demonstração porque é muito pequeno. Para obter mais informações, consulte o artigo [Como ler e escrever ficheiros de dados grandes](how-to-read-write-files.md).

2. Na **Vista de Dados**, clique no ícone **+** para adicionar uma nova origem de dados. O assistente **Adicionar Origem de Dados** é iniciado. 

   ![vista de dados](media/tutorial-classifying-iris/data_view.png)

3. Conclua o Assistente de Preparação de Dados. 
   - No primeiro ecrã, selecione a opção **Ficheiro(s)/Diretório** e clique em **Seguinte**.
   - No segundo ecrã, escolha o ficheiro local **iris.csv**, como, por exemplo "C:\Temp\myIris\iris.csv".
   - No terceiro ecrã, **Detalhes do Ficheiro**, aceite os valores predefinidos.
   - No quarto ecrã, **Tipos de Dados**, altere o **TIPO DE DADOS** de _Cadeia_ para _Numérico_ na _Column1_ à _Column4_, uma vez que estas colunas são valores numéricos. 
   - Nos quintos e sextos ecrãs, aceite as predefinições.
   - Clique no botão **Concluir**.

   ![selecionar iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Certifique-se de que seleciona o ficheiro **iris.csv** no diretório do projeto atual para este exercício. Caso contrário, os últimos passos poderão falhar. 

4. É criado um ficheiro **iris-1.dsource** novo. É atribuído um nome exclusivo ao ficheiro com um traço-um, uma vez que o projeto de exemplo já vem com um ficheiro **iris.dsource** não numerado.  

   O ficheiro abre e os dados são apresentados. É automaticamente adicionada uma série de cabeçalhos de coluna, de **Column1** a **Column5**, a este conjunto de dados. Desloque o cursor até à parte inferior e repare que a última linha do conjunto de dados está vazia. Isto deve-se a uma quebra de linha extra no ficheiro csv.

   ![vista de dados de iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Agora, clique no botão **Métrica**. Observe os histogramas e um conjunto completo de estatísticas que são calculados para si para cada coluna. Também pode clicar no botão **Dados** para ver os dados novamente. 

   ![vista de dados de iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Clique no botão **Preparar**. A caixa de diálogo **Preparar** abre. 

   O projeto de exemplo inclui um ficheiro **iris.dprep**, pelo que, por predefinição, pede-lhe para criar um novo fluxo de dados nesse pacote de preparação de dados **iris.dprep** já existente. 

   Altere o valor da lista pendente para **+New Data Preparation Package**, introduza um novo valor "iris-1" e, em seguida, clique em **OK**.

   ![Vista de dados de iris](media/tutorial-classifying-iris/new_dprep.png)

   Um novo pacote de preparação de dados com o nome **iris-1.dprep** é criado e aberto no editor de preparação de dados.

7. Agora, vamos fazer uma preparação de dados simples. Mude os nomes das colunas ao clicar no cabeçalho de cada coluna e torne o texto do cabeçalho editável. 

   Introduza **Sepal Lenght** (Comprimento da Sépala), **Sepal Width** (Largura da Sépala), **Petal Length** (Comprimento da Pétala), **Petal Width** (Largura da Pétada) e **Species** (Espécies) nas cinco colunas, respetivamente.

   ![Mudar o nome das colunas](media/tutorial-classifying-iris/rename_column.png)

8. Para contar valores distintos, selecione a coluna **Species** e clique com o botão direito do rato na mesma. Escolha **Contagens de Valores**. 

   ![Clique em Contagens de Valores](media/tutorial-classifying-iris/value_count.png)

   Esta ação abre o painel **Inspetores** e apresenta um histograma com quatro barras. Repare que a coluna de destino tem três valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** e um valor **(null)** (nulo).

9. Filtre os valores nulos ao selecionar a barra do gráfico que representa o valor nulo. Há uma linha com um valor **(null)**. Para eliminar esta linha, clique no botão do filtro **-**.

   ![Histograma da contagem de valores](media/tutorial-classifying-iris/filter_out.png)

10. Tenha em conta os passos individuais detalhados no painel **PASSOS**. Enquanto trabalhava na mudança do nome das colunas e na filtragem das linhas com valores nulos, cada ação foi registada como um passo de preparação de dados. Pode editar os passos individuais para ajustar as definições, reordenar os passos e inclusivamente remover passos.

   ![Passos](media/tutorial-classifying-iris/steps.png)

11. Agora, feche o editor de dados de preparação ao clicar no **X** no separador com o nome **iris-1** com o ícone de gráfico. O seu trabalho é guardado automaticamente no ficheiro **iris-1.dprep** que é apresentado no cabeçalho **Preparações de Dados**.

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Gerar código Python/PySpark para invocar o pacote de preparação de dados

1. Clique com o botão direito do rato no ficheiro **iris-1.dprep** para apresentar o menu de contexto e escolha **Gerar Ficheiro de Código de Acesso de Dados**. 

   ![gerar código](media/tutorial-classifying-iris/generate_code.png)

2. É aberto um ficheiro novo com o nome **iris-1.py** com as linhas de código abaixo:

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   Este fragmento de código invoca a lógica que criou como um pacote de preparação de dados. Consoante o contexto em que este código é executado, `df` representa diferentes tipos de pacotes de dados. É utilizado um [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) quando é executado num runtime de Python ou um [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) quando é executado num contexto do Spark. 

   Para obter mais informações sobre como preparar dados no Azure Machine Learning Workbench, consulte o guia [Introdução à Preparação de Dados](data-prep-getting-started.md).

## <a name="next-steps"></a>Passos seguintes
Nesta primeira parte da série do tutorial de três partes, utilizou o Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Criar um novo projeto 
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar o pacote de preparação de dados

Está pronto para avançar para a próxima parte da série, para criar um modelo de aprendizagem automática.
> [!div class="nextstepaction"]
> [Criar um modelo](tutorial-classifying-iris-part-2.md)
