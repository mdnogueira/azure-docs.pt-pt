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
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: pt-pt
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>Classificar Iris parte 1: preparar os dados
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados ponto a ponto integrada para os cientistas de dados profissionais prepararem dados, desenvolverem experimentações e implementarem modelos à escala da cloud.

Este tutorial é a primeira parte de uma série de três partes. Neste tutorial, vamos percorrer as noções básicas dos serviços do Azure Machine Learning (pré-visualização). Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um projeto no Azure Machine Learning Workbench
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar o pacote de preparação de dados

Para simplificar, este tutorial utiliza o intemporal [conjunto de dados flor de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). As capturas de ecrã são específicas do Windows, mas a experiência de macOS é praticamente idêntica.

## <a name="launch-azure-machine-learning-workbench"></a>Iniciar o Azure Machine Learning Workbench
Siga o [Manual de início rápido Instalar e criar](quickstart-installation.md) para instalar a aplicação Azure Machine Learning Workbench, que também inclui a interface de linha de comandos (CLI). Inicie a aplicação Azure Machine Learning Workbench e inicie sessão, se necessário.

## <a name="create-a-new-project"></a>Criar um novo projeto
1. No painel **PROJETOS**, clique no ícone **+** para criar um **Novo Projeto**.

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
1. Abra o ficheiro `iris.csv` a partir da **Vista de Ficheiros**. O ficheiro é uma tabela simples com 5 colunas e 150 linhas. Tem quatro colunas de funcionalidades numéricas e uma coluna de destino de cadeia. Não tem cabeçalhos de coluna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >Nota: não é recomendado incluir ficheiros de dados na pasta do projeto, especialmente quando o tamanho do ficheiro é grande. Incluímos o ficheiro `iris.csv` neste modelo para fins de demonstração porque é muito pequeno. Para obter mais informações, consulte o artigo [Como ler e escrever ficheiros de dados grandes](how-to-read-write-files.md).

2. Na **Vista de Dados**, clique no ícone **+** para adicionar uma nova origem de dados. O assistente **Adicionar Origem de Dados** é iniciado. 

   ![vista de dados](media/tutorial-classifying-iris/data_view.png)

3. Selecione a opção **Ficheiro(s)/Diretório** e escolha o ficheiro local `iris.csv`. Aceite as predefinições para cada ecrã e, por último, clique em **Concluir**. 

   ![selecionar iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Certifique-se de que seleciona o ficheiro `iris.csv` no diretório do projeto atual para este exercício. Caso contrário, os últimos passos poderão falhar. 

4. É criado um novo ficheiro `iris-1.dsource`. É atribuído um nome exclusivo ao ficheiro com um traço-um, uma vez que o projeto de exemplo já vem com um ficheiro `iris.dsource` não numerado.  O ficheiro abre e os dados são apresentados. É automaticamente adicionada uma série de cabeçalhos de coluna, de `Column1` a `Column5`, a este conjunto de dados. Desloque o cursor até à parte inferior e repare que a última linha do conjunto de dados está vazia. Isto deve-se a uma quebra de linha extra no ficheiro csv.

   ![vista de dados de iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Agora, clique no botão **Métrica**. Observe os histogramas e um conjunto completo de estatísticas que são calculados para si para cada coluna. Também pode clicar no botão **Dados** para ver os dados novamente. 

   ![vista de dados de iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Clique no botão **Preparar** junto ao botão **Métrica** (ou no botão **Dados** se estiver na vista de métrica). A caixa de diálogo **Preparar** abre. O projeto de exemplo já vem com um ficheiro `iris.dprep`, pelo que, por predefinição, pede-lhe para criar um novo fluxo de dados nesse pacote de preparação de dados **iris.dprep** existente. Altere o valor da lista pendente para **+New Data Preparation Package**, introduza um novo valor "iris-1" e, em seguida, clique em **OK**.

   ![vista de dados de iris](media/tutorial-classifying-iris/new_dprep.png)

Um novo pacote de preparação de dados com o nome `iris-1.dprep` é criado e aberto no editor de preparação de dados.

Agora, vamos fazer uma preparação de dados simples. Mude os nomes das colunas ao clicar no cabeçalho de cada coluna e torne o texto do cabeçalho editável. Introduza `Sepal Length`, `Sepal Width`, `Petal Length`, `Petal Width` e `Species` para as cinco colunas, respetivamente.

![mudar o nome das colunas](media/tutorial-classifying-iris/rename_column.png)

Selecione a coluna `Species` e clique com o botão direito do rato na mesma. Escolha **Contagens de Valores**. 

![contagem de valores](media/tutorial-classifying-iris/value_count.png)

Esta ação cria um histograma com quatro barras. Repare que a nossa coluna de destino tem três valores distintos, `Iris_virginica`, `Iris_versicolor`, `Iris-setosa`. Há também uma linha com um valor `(null)`. Vamos remover esta linha, selecionando a barra que representa o valor nulo e, para removê-la, clique no botão de filtro **-**. 

![contagem de valores](media/tutorial-classifying-iris/filter_out.png)

Enquanto está a mudar os nomes das colunas e a remover a linha com o valor nulo, cada ação que executa está a ser registada como um passo de preparação de dados no painel **PASSOS**. Pode editar os passos (para ajustar as definições dos mesmos), reordená-los ou até removê-los.

![passos](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Gerar código Python/PySpark para invocar o pacote de preparação de dados

Agora, feche o editor DataPrep. (Não se preocupe, é guardado automaticamente.) Clique com o botão direito do rato no ficheiro **iris-1.dprep** para apresentar o menu de contexto e escolha **Gerar Ficheiro de Código de Acesso de Dados**. 

![gerar código](media/tutorial-classifying-iris/generate_code.png)

Um ficheiro **iris-1.py** é criado com as duas linhas seguintes de código pré-preenchidas (juntamente com alguns comentários):

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
Este fragmento de código invoca a lógica que criou como um pacote de preparação de dados. Dependendo do contexto em que este código é executado, `df` pode ser um [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) se for executado no runtime do Python ou um [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) se for executado num contexto de Spark. Para obter mais informações sobre como preparar dados no Azure Machine Learning Workbench, consulte o guia [Introdução à Preparação de Dados](data-prep-getting-started.md).

## <a name="next-steps"></a>Passos seguintes
Nesta primeira parte da série do tutorial de três partes, utilizou o Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Criar um novo projeto 
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar o pacote de preparação de dados

Está pronto para avançar para a próxima parte da série, para criar um modelo de aprendizagem automática.
> [!div class="nextstepaction"]
> [Criar um modelo](tutorial-classifying-iris-part-2.md)

