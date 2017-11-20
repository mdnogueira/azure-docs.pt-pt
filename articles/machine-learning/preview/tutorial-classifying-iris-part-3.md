---
title: "Implementar um modelo dos serviços do Azure Machine Learning (pré-visualização) | Microsoft Docs"
description: "Este tutorial completo mostra como utilizar os serviços do Azure Machine Learning (pré-visualização) ponto a ponto. Esta é a parte três e aborda o modelo de implementação."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 11/14/2017
ms.openlocfilehash: 7fa7eb53876746f1934af8ca3428cfdacb56382d
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="classify-iris-part-3-deploy-a-model"></a>Classificar Íris, parte 3: implementar um modelo
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados completa e integrada para os cientistas de dados profissionais. Os cientistas de dados podem utilizá-los para preparar dados, desenvolver experimentações e implementar modelos à escala da cloud.

Este tutorial é a parte três de uma série de três partes. Nesta parte do tutorial, vai utilizar os serviços do Azure Machine Learning (pré-visualização) para:

> [!div class="checklist"]
> * Localizar o ficheiro de modelo.
> * Gerar um script de classificação e um ficheiro de esquema.
> * Preparar o ambiente.
> * Criar um serviço Web em tempo real.
> * Executar o serviço Web em tempo real.
> * Examinar os dados de blob de saída. 

 Este tutorial utiliza o [conjunto de dados flor de Iris](https://en.wikipedia.org/wiki/iris_flower_data_set) intemporal. As capturas de ecrã são específicas do Windows, mas a experiência em macOS é praticamente idêntica.

## <a name="prerequisites"></a>Pré-requisitos
Conclua as duas primeiras partes desta série de tutoriais.

   * Siga o [tutorial Prepare data](tutorial-classifying-iris-part-1.md) (Preparar dados) para criar recursos do Machine Learning e instalar a aplicação Azure Machine Learning Workbench.

   * Siga o [Tutorial Build a model](tutorial-classifying-iris-part-2.md) (Criar um modelo) para criar um modelo de regressão logística no Azure Machine Learning.

É necessário um motor do Docker instalado e em execução localmente. Em alternativa, pode implementar um cluster do Azure Container Service no Azure.

## <a name="download-the-model-pickle-file"></a>Transferir o ficheiro pickle do modelo
Na parte anterior do tutorial, o script **iris_sklearn.py** foi executado no Machine Learning Workbench localmente. Essa ação serializou o modelo de regressão logística mediante a utilização do popular pacote de serialização de objetos do Python, o [pickle](https://docs.python.org/2/library/pickle.html). 

1. Abra a aplicação Machine Learning Workbench e abra o projeto **myIris** que criou na parte anterior da série do tutorial.

2. Quando o projeto estiver aberto, selecione o botão **Ficheiros** (ícone de pasta), no painel do lado esquerdo, para abrir a lista de ficheiros na pasta do projeto.

3. Selecione o ficheiro **iris_sklearn.py**. O código de Python abre-se num novo separador do editor de texto dentro do Workbench.

4. Reveja o ficheiro **iris_sklearn.py** para ver onde o ficheiro pickle foi gerado. Selecione Control+F para abrir a caixa de diálogo **Localizar** e localize a palavra **pickle** no código de Python.

   Este fragmento de código mostra como o ficheiro de saída de pickle foi gerado. O ficheiro pickle de saída é denominado **model.pkl** no disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Localize o ficheiro pickle do modelo nos ficheiros de saída de uma execução anterior.
   
   Quando executou o script **iris_sklearn.py**, o ficheiro de modelo foi escrito na pasta **saídas** com o nome **model.pkl**. Esta pasta reside no ambiente de execução no qual escolheu executar o script e não na pasta do projeto local. 
   
   - Para localizar o ficheiro, selecione o botão **Execuções** (ícone de relógio), no painel do lado esquerdo, para abrir a lista de **Todas as Execuções**.  
   - O separador **Todas as Execuções** abre-se. Na tabela de execuções, selecione uma das execuções recentes em que o destino era **local** e o nome do script **iris_sklearn.py**. 
   - Abre-se o painel **Propriedades da Execução**. No canto superior direito do painel, repare na secção **Saídas**. 
   - Para transferir o ficheiro pickle, selecione a caixa de verificação junto ao ficheiro **model.pkl** e selecione o botão **Transferir**. Guarde-o na raiz da pasta do projeto. O ficheiro é necessário nos passos seguintes.

   ![Transferir o ficheiro pickle](media/tutorial-classifying-iris/download_model.png)

   Leia mais sobre a pasta `outputs` no artigo [How to read and write large data files](how-to-read-write-files.md) (Como ler e escrever ficheiros de dados grandes).

## <a name="get-the-scoring-script-and-schema-files"></a>Obter os ficheiros de script de classificação e de esquema
Para implementar o serviço Web, juntamente com o ficheiro do modelo, também precisa de um script de classificação e, opcionalmente, de um esquema para os dados de entrada do serviço Web. O script de classificação carrega o ficheiro **model.pkl** a partir da pasta atual e utiliza-o para produzir uma classe de Íris recentemente prevista.  

1. Abra a aplicação Azure Machine Learning Workbench e abra o projeto **myIris** que criou na parte anterior da série do tutorial.

2. Quando o projeto estiver aberto, selecione o botão **Ficheiros** (ícone de pasta), no painel do lado esquerdo, para abrir a lista de ficheiros na pasta do projeto.

3. Selecione o ficheiro **score_iris.py**. É aberto o script de Python. Este ficheiro é utilizado como o ficheiro de classificação.

   ![Ficheiro de classificação](media/tutorial-classifying-iris/model_data_collection.png)

4. Para obter o ficheiro de esquema, execute o script. Selecione o ambiente **local** e o script **score_iris.py** na barra de comandos e selecione o botão **Executar**. 

5. Este script cria um ficheiro JSON na secção **Saídas**, que captura o esquema de dados de entrada de que o modelo precisa.

6. Repare no painel **Tarefas**, no lado direito do painel **Dashboard do Projeto**. Aguarde até que o último trabalho de **score_iris.py** apresente o estado verde **Concluído**. Em seguida, selecione a hiperligação **score_iris.py [1]** relativa à última execução de tarefa para ver os detalhes da execução de **score_iris.py**. 

7. No painel **Propriedades da Execução**, na secção **Saídas**, selecione o ficheiro recém-criado **service_schema.json**.  Selecione a caixa de verificação junto ao nome do ficheiro e selecione **Transferir**. Guarde o ficheiro na pasta de raiz do projeto.

8. Regresse ao separador anterior, onde abriu o script **score_iris.py**. Mediante a utilização da recolha de dados, pode capturar as entradas e as previsões do modelo a partir do serviço Web. Os passos seguintes são particularmente interessantes para a recolha de dados:

9. Reveja o código na parte superior da classe de importação de ficheiros **ModelDataCollector**, porque este contém a funcionalidade de recolha de dados do modelo:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Reveja as linhas de código seguintes na função **init()** que instancia **ModelDataCollector:**

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Reveja as linhas de código seguintes na função **run(input_df)**, que recolhe os dados de entrada e de predição:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Agora, está pronto para preparar o ambiente para operacionalizar o modelo.



## <a name="prepare-to-operationalize-locally"></a>Preparar para operacionalizar localmente
Utilize a implementação de _modo local_ para executar em contentores do Docker no seu computador local.

Pode utilizar o _modo local_ para desenvolvimento e testes. O motor do Docker tem de estar em execução localmente para concluir os passos seguintes de modo a operacionalizar o modelo. Pode utilizar o sinalizador `-h` no fim dos comandos para obter a Ajuda dos mesmos.

>[!NOTE]
>Se não tiver o motor do Docker localmente, pode, mesmo assim, continuar, mediante a criação de um cluster no Azure para a implementação. Certifique-se apenas de que elimina o cluster a seguir ao tutorial, para que não incorra em custos contínuos.

1. Abra a interface de linha de comandos (CLI).
   Na aplicação Azure Machine Learning Workbench, no menu **Ficheiro**, selecione **Abrir Linha de Comandos**.

   A linha de comandos abre-se na localização da pasta do seu projeto atual, **c:\temp\myIris>**.

2. Certifique-se de que o fornecedor de recursos do Azure **Microsoft.ContainerRegistry** está registado na sua subscrição. Tem de registar este fornecedor de recursos antes de poder criar um ambiente no passo 3. Pode utilizar o comando seguinte para confirmar se já está registado:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Deverá ver um resultado como este: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Se **Microsoft.ContainerRegistry** não estiver registado, pode registá-lo com o comando abaixo:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   O registo pode demorar alguns minutos. Pode verificar o estado do mesmo com o comando **az provider list** anterior ou com o seguinte:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   A terceira linha da saída apresenta **"registrationState": "Registering"**. Aguarde uns momentos e repita o comando **show**, até que a saída apresente **"registrationState": "Registered"**.

3. Crie o ambiente. Tem de executar este passo uma vez por ambiente. Por exemplo, execute-o uma vez para o ambiente de desenvolvimento e outra para produção. Utilize o _modo local_ para o primeiro. Pode experimentar o comutador `-c` ou `--cluster` no comando seguinte para configurar um ambiente no _modo de cluster_ mais tarde.

Tenha em conta que o comando de configuração seguinte requer que tenha o acesso Contribuidor à subscrição. Se não o tiver, precisa, pelo menos, de acesso Contribuidor ao grupo de recursos no qual está a implementar. Neste último caso, tem de especificar o nome do grupo de recursos como parte do comando de configuração através do sinalizador `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Siga as instruções no ecrã para aprovisionar uma conta de armazenamento para armazenar as imagens do Docker, um registo do Azure Container Registry que lista as imagens do Docker e uma conta do AppInsight que recolhe telemetria. Se tiver utilizado o comutador `-c`, este também cria um cluster do Azure Container Service.
   
   O nome do cluster é uma forma de identificar o ambiente. A localização deve ser a mesma da localização da conta de Gestão de Modelos que criou no portal do Azure.

4. Crie uma conta de Gestão de Modelos. (Esta é uma configuração única.)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Defina a conta de Gestão de Modelos.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Defina o ambiente.

   Depois de concluída a configuração, utilize o comando seguinte para definir as variáveis de ambiente necessárias para operacionalizar o ambiente. Utilize o mesmo nome de ambiente que utilizou anteriormente no passo 4. Utilize o mesmo nome de grupo de recursos que foi produzido na janela do comando quando o processo de configuração foi concluído.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. Para verificar se configurou corretamente o ambiente operacionalizado para a implementação do serviço Web local, introduza o comando abaixo:

   ```azurecli
   az ml env show
   ```

Agora, está pronto para criar o serviço Web em tempo real.

>[!NOTE]
>Pode reutilizar a conta de Gestão de Modelos e o ambiente para implementações subsequentes de serviços Web. Não terá de criá-los para cada serviço Web. Uma conta ou um ambiente pode ter vários serviços Web associados.

## <a name="create-a-real-time-web-service-in-one-command"></a>Criar um serviço Web em tempo real com um único comando
1. Para criar um serviço Web em tempo real, utilize o comando seguinte:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Este comando gera um ID de serviço Web que pode utilizar mais tarde.

   Os comutadores seguintes são utilizados com o comando **az ml service create realtime**:
   * `-n`: o nome da aplicação, tem de estar todo em minúsculas.
   * `-f`: o nome do ficheiro de script de classificação.
   * `--model-file`: o ficheiro de modelo. Neste caso, é o ficheiro model.pkl de pickle.
   * `-r`: o tipo de modelo. Neste caso, é um modelo de Python.
   * `--collect-model-data true`: permite a recolha de dados.

   >[!IMPORTANT]
   >O nome do serviço, que também é o nome da imagem do Docker nova, tem de estar em minúsculas. Caso contrário, recebe um erro. 

2. Quando executa o comando, o modelo e o ficheiro de classificação são carregados para a conta de armazenamento que criou como parte da configuração do ambiente. O processo de implementação cria uma imagem do Docker com o modelo, o esquema e o ficheiro de classificação no mesmo e envia-o para o registo do Azure Container Registry: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   O comando extrai a imagem localmente para o computador e inicia um contentor do Docker com base na mesma. Se o seu ambiente estiver configurado no modo de cluster, o contentor do Docker é implementado no cluster do Kubernetes dos Serviços Cloud do Azure.

   Como parte da implementação, é criado um ponto final REST HTTP para o serviço Web no seu computador local. Após alguns minutos, o comando deve ser concluído com uma mensagem de êxito e o serviço Web está pronto para a ação.

3. Para ver o contentor do Docker que está em execução, utilize o comando **docker ps**:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Criar um serviço Web em tempo real com comandos separados
Como alternativa ao comando **az ml service create realtime** mostrado anteriormente, também pode realizar os passos em separado. 

Em primeiro lugar, registe o modelo. Em seguida, gere o manifesto, crie a imagem do Docker e crie o serviço Web. Esta abordagem passo a passo dá-lhe mais flexibilidade a cada passo. Além disso, pode reutilizar as entidades geradas no passo anterior e recriá-las apenas quando necessário.

1. Indique o nome do ficheiro pickle para registar o modelo.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Este comando gera um ID de modelo.

2. Crie um manifesto.

   Para criar um manifesto, utilize o comando seguinte e indique a saída do ID de modelo do passo anterior:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Este comando gera um ID de manifesto.

3. Crie uma imagem do Docker.

   Para criar uma imagem do Docker, utilize o comando seguinte e indique a saída do valor de ID de manifesto do passo anterior:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Este comando gera um ID de imagem do Docker.
   
4. Crie o serviço.

   Para criar um serviço, utilize o comando seguinte e indique a saída do ID de manifesto do passo anterior:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Este comando gera um ID de serviço Web.

Está agora pronto para executar o serviço Web.

## <a name="run-the-real-time-web-service"></a>Executar o serviço Web em tempo real

Para testar o serviço Web **irisapp** que está em execução, utilize um registo codificado JSON que contenha uma matriz de quatro números aleatórios:

1. O serviço Web inclui os dados de exemplo. Quando em execução no modo local, pode chamar o comando **az ml service show realtime**. Esta chamada obtém um comando de execução de exemplo que é útil para testar o serviço. Também obtém o url de classificação que pode utilizar para incorporar o serviço na sua própria aplicação personalizada:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. Para testar o serviço, execute o comando de execução de serviço devolvido.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   O resultado é **"2",**, que é a classe prevista. (O seu resultado poderá ser diferente.) 

3. Para executar o serviço fora da CLI, tem de obter as chaves para a autenticação:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Ver os dados recolhidos no armazenamento de Blobs do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Localize as suas contas de armazenamento. Para tal, selecione **Mais Serviços**.

3. Na caixa de pesquisa, introduza **Contas de armazenamento** e selecione **Introduzir**.

4. Na caixa de pesquisa das **Contas de armazenamento**, selecione o recurso **Conta de armazenamento** que corresponde ao seu ambiente. 

   > [!TIP]
   > Para determinar que conta de armazenamento está a ser utilizada:
   > 1. Abra o Azure Machine Learning Workbench.
   > 2. Selecione o projeto em que está a trabalhar.
   > 3. Abra uma linha de comandos a partir do menu **Ficheiro**.
   > 4. Na linha de comandos, introduza `az ml env show -v` e verifique o valor *storage_account*. Este é o nome da sua conta de armazenamento.

5. Depois de aberto o painel **Conta de armazenamento**, selecione **Contentores**, na lista do lado esquerdo. Localizar o contentor com o nome **modeldata**. 
 
   Se não vir quaisquer dados, poderá ter de aguardar até dez minutos após o primeiro pedido de serviço Web para ver os dados serem propagados para a conta de armazenamento.

   Os dados fluem para os blobs com o caminho de contentor seguinte:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Pode consumir estes dados a partir do armazenamento de Blobs do Azure. Existem diversas ferramentas que utilizam, tanto o software Microsoft, como ferramentas de código aberto, como:

   - Azure Machine Learning: abra o ficheiro CSV, adicionando-o como uma origem de dados. 
   - Excel: abra os ficheiros CSV diários como uma folha de cálculo.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): crie gráficos com os dados extraídos a partir dos dados do CSV nos blobs.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): carregue os dados do CSV para uma tabela do Hive e execute consultas SQL diretamente nos blobs.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): crie um dataframe com uma grande quantidade de dados do CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Passos seguintes
Nesta terceira parte da série do tutorial de três partes, aprendeu a utilizar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Localizar o ficheiro de modelo.
> * Gerar um script de classificação e um ficheiro de esquema.
> * Preparar o ambiente.
> * Criar um serviço Web em tempo real.
> * Executar o serviço Web em tempo real.
> * Examinar os dados de blob de saída. 

Executou com êxito um script de preparação em vários ambientes de computação, criou um modelo, serializou-o e operacioanalizou-o através de um serviço Web baseado no Docker. 

Está agora preparado para fazer preparação de dados avançada:
> [!div class="nextstepaction"]
> [Preparação de dados avançada](tutorial-bikeshare-dataprep.md)
