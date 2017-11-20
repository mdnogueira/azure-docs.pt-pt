---
title: "Preparar dados para classificar o tutorial Iris em serviços do Azure Machine Learning (pré-visualização) | Microsoft Docs"
description: "Este tutorial completo mostra como utilizar os serviços do Azure Machine Learning (pré-visualização) ponto a ponto. Esta é a parte um e fala sobre a preparação dos dados."
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
ms.openlocfilehash: fcef59d38cce5f8860508ebeeda0b1b97f5e83f1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="classify-iris-part-1-prepare-the-data"></a>Classificar Íris, parte 1 : preparar os dados
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados ponto a ponto integrada para os cientistas de dados profissionais prepararem dados, desenvolverem experimentações e implementarem modelos à escala da cloud.

Este tutorial é a primeira parte de uma série de três partes. Neste tutorial, vamos percorrer as noções básicas dos serviços do Azure Machine Learning (pré-visualização). Saiba como:
> [!div class="checklist"]
> * Criar um projeto no Azure Machine Learning Workbench.
> * Criar um pacote de preparação de dados.
> * Gerar código Python/PySpark para invocar um pacote de preparação de dados.

Este tutorial utiliza o [conjunto de dados flor de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporal. As capturas de ecrã são específicas do Windows, mas a experiência em macOS é praticamente idêntica.

## <a name="prerequisites"></a>Pré-requisitos
- Criar uma conta de Experimentação do Azure Machine Learning.
- Instalar o Azure Machine Learning Workbench.

Pode seguir as instruções do artigo [Início rápido de instalação e criação](quickstart-installation.md) para instalar a aplicação Azure Machine Learning Workbench. Esta instalação também inclui a ferramenta de linha de comandos multiplataforma do Azure, também denominada CLI do Azure.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Criar um projeto novo no Azure Machine Learning Workbench
1. Abra a aplicação Azure Machine Learning Workbench e inicie sessão, se necessário. No painel **PROJETOS**, selecione o sinal de mais (**+**) para criar um **Projeto Novo**.

   ![Nova área de trabalho](media/tutorial-classifying-iris/new_ws.png)

2. Preencha os detalhes em **Criar Novo Projeto**: 

   ![Novo projeto](media/tutorial-classifying-iris/new_project.png)

   - Preencha a caixa **Nome do projeto** com um nome para o projeto. Por exemplo, utilize o valor **myIris**.
   - Selecione o **Diretório do projeto** no qual é criado o projeto. Por exemplo, utilize o valor `C:\Temp\`. 
   - Introduza a **Descrição do projeto**, que é opcional. 
   - O campo **Repositório do Git** também é opcional e pode ficar em branco. Pode indicar um repositório do Git vazio existente (um repositório sem ramo principal) no Visual Studio Team Services. Se utilizar um repositório do Git que já exista, pode ativar os cenários de roaming e partilha mais tarde. Para obter mais informações, veja [Use Git repo](using-git-ml-project.md) (Utilizar o repositório do Git). 
   - Selecione uma **Área de Trabalho**. Por exemplo, este tutorial utiliza **IrisGarden**. 
   - Selecione o modelo **Classifying Iris** na lista de modelos de projeto. 

3. Selecione o botão **Criar**. O projeto é agora criado e aberto para si.

## <a name="create-a-data-preparation-package"></a>Criar um pacote de preparação de dados
1. Abra o ficheiro **iris.csv** a partir da **Vista de Ficheiros**. O ficheiro é uma tabela com cinco colunas e 150 linhas. Tem quatro colunas de funcionalidades numéricas e uma coluna de destino de cadeia. Não tem cabeçalhos de coluna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Não inclua ficheiros de dados na pasta do projeto, especialmente quando o tamanho do ficheiro é grande. Incluímos o ficheiro **iris.csv** neste modelo para fins de demonstração, porque é muito pequeno. Para obter mais informações, veja [How to read and write large data files](how-to-read-write-files.md) (Como ler e escrever ficheiros de dados grandes).

2. Na **Vista de Dados**, selecione o sinal de mais (**+**) para adicionar uma origem de dados nova. É aberta a página **Adicionar Origem de Dados**. 

   ![Vista de dados](media/tutorial-classifying-iris/data_view.png)

3. Deixe os valores predefinidos e, em seguida, selecione o botão **Seguinte**.  
 
   ![Selecionar iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Certifique-se de que seleciona o ficheiro **iris.csv** no diretório do projeto atual para este exercício. Caso contrário, os passos posteriores poderão falhar.
   
4. Depois de selecionar o ficheiro, selecione o botão **Concluir**.

4. É criado um ficheiro novo com o nome **iris-1.dsource**. É atribuído um nome exclusivo ao ficheiro com um traço-um, uma vez que o projeto de exemplo já vem com um ficheiro **iris.dsource** não numerado.  

   O ficheiro abre-se e os dados são apresentados. É automaticamente adicionada uma série de cabeçalhos de coluna, de **Column1** a **Column5**, a este conjunto de dados. Desloque o cursor até à parte inferior e repare que a última linha do conjunto de dados está vazia. A linha está vazia porque há uma quebra de linha adicional no ficheiro CSV.

   ![Vista de dados de iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Selecione o botão **Métricas**. Observe os histogramas. Foi calculado para cada coluna um conjunto completo de estatísticas. Também pode selecionar o botão **Dados** para ver os dados novamente. 

   ![Vista de dados de iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Selecione o botão **Preparar**. É aberta a caixa de diálogo **Preparar**. 

   O projeto de exemplo inclui um ficheiro **iris.dprep**. Por predefinição, pede-lhe para criar um novo fluxo de dados no pacote de preparação de dados **iris.dprep** que já existe. 

   Selecione **+ Novo Pacote de Preparação de Dados** no menu pendente, introduza um valor novo para o nome do pacote, utilize **iris-1** e, em seguida, selecione **OK**.

   ![Vista de dados de iris](media/tutorial-classifying-iris/new_dprep.png)

   É criado e aberto no editor de preparação de dados um pacote de preparação de dados novo com o nome **iris-1.dprep**.

7. Agora, vamos fazer uma preparação de dados simples. Mude os nomes das colunas. Selecione cada cabeçalho de coluna para tornar o texto do cabeçalho editável. 

   Introduza **Sepal Lenght** (Comprimento da Sépala), **Sepal Width** (Largura da Sépala), **Petal Length** (Comprimento da Pétala), **Petal Width** (Largura da Pétada) e **Species** (Espécies) nas cinco colunas, respetivamente.

   ![Mudar o nome das colunas](media/tutorial-classifying-iris/rename_column.png)

8. Para contar valores distintos, selecione a coluna **Species** e clique com o botão direito do rato para selecioná-la. Selecione **Contagens de Valores** no menu pendente. 

   ![Selecione Contagens de Valores](media/tutorial-classifying-iris/value_count.png)

   Esta ação abre o painel **Inspetores** e apresenta um histograma com quatro barras. A coluna de destino tem três valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** e um valor **(null)** (nulo).

9. Para filtrar os nulos, selecione a barra do gráfico que representa o valor nulo. Há uma linha com um valor **(null)**. Para remover esta linha, selecione o sinal de subtração (**-**).

   ![Histograma da contagem de valores](media/tutorial-classifying-iris/filter_out.png)

10. Tenha em conta os passos individuais detalhados no painel **PASSOS**. Quando mudou o nome das colunas e filtrou as linhas de valores nulos, cada ação foi registada como um passo de preparação de dados. Pode editar os passos individuais para ajustar as definições, reordenar os passos e remover os passos.

   ![Passos](media/tutorial-classifying-iris/steps.png)

11. Feche o editor de preparação de dados. Selecione **Fechar** (x) no separador **iris-1** com o ícone de gráfico. O seu trabalho é guardado automaticamente no ficheiro **iris-1.dprep** que é apresentado no cabeçalho **Preparações de Dados**.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Gerar código Python/PySpark para invocar um pacote de preparação de dados

1. Clique com o botão direito do rato no ficheiro **iris-1.dprep** para apresentar o menu de contexto e selecione **Gerar Ficheiro de Código de Acesso a Dados**. 

   ![Gerar código](media/tutorial-classifying-iris/generate_code.png)

2. É aberto um ficheiro novo com o nome **iris-1.py** com as linhas de código abaixo:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   Este fragmento de código invoca a lógica que criou como um pacote de preparação de dados. Consoante o contexto em que este código é executado, `df` representa os vários tipos de pacotes de dados. É utilizado um [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) quando é executado num runtime de Python ou um [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) quando é executado num contexto do Spark. 

   Para obter mais informações sobre como preparar dados no Azure Machine Learning Workbench, veja o guia [Get started with data preparation](data-prep-getting-started.md) (Introdução à preparação de dados).

## <a name="next-steps"></a>Passos seguintes
Nesta primeira parte da série do tutorial de três partes, utilizou o Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Criar um projeto novo. 
> * Criar um pacote de preparação de dados.
> * Gerar código Python/PySpark para invocar um pacote de preparação de dados.

Está pronto para avançar para a próxima parte da série, em que vai aprender a criar um modelo do Azure Machine Learning.
> [!div class="nextstepaction"]
> [Criar um modelo](tutorial-classifying-iris-part-2.md)
