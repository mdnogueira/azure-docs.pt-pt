---
title: "Implementar um Modelo dos serviços do Azure Machine Learning (pré-visualização) | Microsoft Docs"
description: "Este tutorial completo mostra como utilizar os serviços do Azure Machine Learning (pré-visualização) ponto a ponto. Esta é a parte 3 sobre como implementar o modelo."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/27/2017
ms.openlocfilehash: 56a79906a0f43f06d35db703d641f547e7bdf868
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="classifying-iris-part-3-deploy-a-model"></a>Classificar Íris, parte 3: implementar um modelo
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados ponto a ponto integrada para os cientistas de dados profissionais prepararem dados, desenvolverem experimentações e implementarem modelos à escala da cloud.

Este tutorial é a parte três de uma série de três partes. Nesta parte do tutorial, vai utilizar os serviços do Azure Machine Learning (pré-visualização) para aprender a:

> [!div class="checklist"]
> * Localizar o ficheiro de modelo
> * Gerar um script de classificação e um ficheiro de esquema
> * Preparar o ambiente
> * Criar um serviço Web em tempo real
> * Executar o serviço Web em tempo real
> * Examinar os dados de blob de saída 

 Para simplificar, este tutorial utiliza o intemporal [conjunto de dados flor de Iris](https://en.wikipedia.org/wiki/iris_flower_data_set). As capturas de ecrã são específicas do Windows, mas a experiência de macOS é praticamente idêntica.

## <a name="prerequisites"></a>Pré-requisitos
Conclua as duas primeiras partes desta série de tutoriais.

1. Siga o [tutorial Prepare data (Preparar dados)](tutorial-classifying-iris-part-1.md) para criar recursos do Azure Machine Learning e instalar a aplicação Azure Machine Learning Workbench.

2. Siga o [Tutorial Build a model (Criar um modelo)](tutorial-classifying-iris-part-2.md) para criar um modelo de regressão logística no Azure Machine Learning.

3. É necessário o motor de Docker instalado e em execução localmente. Em alternativa, pode implementar um cluster do Azure Container Service no Azure.

## <a name="download-the-model-pickle-file"></a>Transferir o ficheiro pickle do modelo
Na parte anterior do tutorial, o script **iris_sklearn.py** foi executado no Azure Machine Learning Workbench localmente. Esta ação serializou o modelo de regressão logística ao utilizar o popular pacote de serialização de objetos do Python, o **[pickle](https://docs.python.org/2/library/pickle.html)**. 

1. Inicie a aplicação **Azure Machine Learning Workbench** e abra o projeto **myIris** que criou na parte anterior da série de tutoriais.

2. Quando o projeto estiver aberto, clique no botão **Ficheiros** (ícone de pasta), na barra de ferramentas do lado esquerdo no Azure Machine Learning Workbench, para abrir a lista de ficheiros na pasta do seu projeto.

3. Selecione o ficheiro **iris_sklearn.py** e o código de Python abre-se num novo separador do editor de texto dentro do Workbench.

4. Reveja o ficheiro **iris_sklearn.py** para ver onde o ficheiro pickle foi gerado. Utilize Control+F para abrir a caixa de diálogo Localizar e localize a palavra **pickle** no código de Python.

   Este fragmento de código mostra como o ficheiro de saída pickled foi gerado. Tenha em atenção que o ficheiro pickle de saída é denominado **model.pkl** no disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Localize o ficheiro pickle do modelo nos ficheiros de saída de uma execução anterior.
   
   Quando executou o script **iris_sklearn.py**, o ficheiro de modelo foi escrito na pasta **saídas** com o nome **model.pkl**. Esta pasta reside no ambiente de execução no qual escolheu executar o script e não na pasta do projeto local. 
   
   - Para localizar o ficheiro, utilize a aplicação Azure Machine Learning Workbench e clique no botão **Execuções** (ícone de relógio), na barra de ferramentas do lado esquerdo, para abrir a listagem de **Todas as Execuções**.  
   - O separador **Todas as Execuções** abre-se. Na tabela de Execuções, selecione uma das execuções recentes em que o destino era **local** e o nome do script **iris_sklearn.py**. 
   - A página **Propriedades da Execução** abre-se. No canto superior direito da página, repare na secção **Saídas**. 
   - Transfira o ficheiro pickle ao selecionar a caixa de verificação junto ao ficheiro **model.pkl** e clique no botão **Transferir**. Guarde-o na raiz da pasta do projeto. É necessário em passos subsequentes.

   ![Transferir o ficheiro pickle](media/tutorial-classifying-iris/download_model.png)

   Leia mais sobre a pasta **saídas** no artigo [How to read and write large data files (Como ler e escrever ficheiros de dados grandes)](how-to-read-write-files.md).

## <a name="get-scoring-and-schema-files"></a>Obter os ficheiros de classificação e de esquema
Para implementar o serviço Web, juntamente com o ficheiro do modelo, também precisa de um script de classificação e, opcionalmente, de um esquema para os dados de entrada do serviço Web. O script de classificação carrega o ficheiro **model.pkl** a partir da pasta atual e utiliza-o para produzir uma classe de Íris recentemente prevista.  

1. Inicie a aplicação **Azure Machine Learning Workbench** e abra o projeto **myIris** que criou na parte anterior da série de tutoriais.

2. Quando o projeto estiver aberto, clique no botão **Ficheiros** (ícone de pasta), na barra de ferramentas do lado esquerdo no Azure Machine Learning Workbench, para abrir a lista de ficheiros na pasta do seu projeto.

3. Selecione o ficheiro **iris_score.py**. É aberto o script de Python. Este ficheiro é utilizado como o ficheiro de classificação.

   ![Ficheiro de Classificação](media/tutorial-classifying-iris/model_data_collection.png)

4. Para obter o ficheiro de esquema, execute o script. Escolha o ambiente **local** e o script **iris_score.py** na barra de comandos e clique no botão **Executar**. 

5. Este script cria um ficheiro JSON na pasta **saídas**, que captura o esquema de dados de entrada de que o modelo precisa.

6. Repare no painel Trabalhos, no lado direito da janela do Machine Learning Workbench. Aguarde até que o último trabalho de **iris\_score.py** apresente o estado verde **Concluído**. Em seguida, clique na hiperligação **iris\_score.py [1]** relativa à última execução de trabalho para ver os detalhes da execução da execução **iris_score.py**. 

7. Na página Propriedades da Execução, na secção **Saídas**, selecione o ficheiro recém-criado **service_schema.json**. **Verifique** o ficheiro e clique em **Transferir**. Guarde o ficheiro na pasta de raiz do projeto.

8. Regresse ao separador anterior, onde abriu o script **iris_score.py**. 

   Aponte a utilização de recolha de dados que lhe permite capturar as entradas e as previsões do modelo a partir do serviço Web. Os pontos seguintes são particularmente interessantes para a recolha de dados:

9. Reveja o código na parte superior da classe de importação de ficheiros ModelDataCollector que contém a funcionalidade de recolha de dados do modelo:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Reveja as linhas de código seguintes na função **init()** que instancia ModelDataCollector:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Reveja as linhas de código seguintes na função **run(input_df)** que recolhe dados de entrada e de predição:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Agora, está pronto para preparar o ambiente para operacionalizar o modelo.

## <a name="prepare-to-operationalize-locally"></a>Preparar para operacionalizar localmente
Utilize a implementação de _modo local_ para executar em contentores do Docker no seu computador local.

Pode utilizar o _modo local_ para desenvolvimento e testes. O motor do Docker tem de estar em execução localmente para concluir os passos seguintes de modo a operacionalizar o modelo. Pode utilizar o sinalizador `-h` no fim dos comandos para obter ajuda do comando.

>[!NOTE]
>Se não tiver o motor do Docker localmente, pode, mesmo assim, continuar, mediante a criação de um cluster no Azure para a implementação. Certifique-se apenas de que elimina o cluster a seguir ao tutorial, para que não incorra em custos contínuos.

1. Abra a Interface de Linha de Comandos no Azure Machine Learning Workbench e, no menu Ficheiro, clique em **Abrir Linha de Comandos**.

   A linha de comandos abre-se na localização da pasta do seu projeto atual, **c:\temp\myIris>**.

2. Certifique-se de que o fornecedor de recursos do Azure **Microsoft.ContainerRegistry** está registado na sua subscrição. Registe este fornecedor de recursos antes de poder criar um ambiente no passo 3. Pode utilizar o comando seguinte para confirmar se já está registado:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Deverá ver uma saída semelhante a: 
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
   O registo pode demorar alguns minutos e pode verificar o estado do mesmo com o comando **az provider list**, anterior, ou com o seguinte:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   A terceira linha da saída apresenta **"registrationState": "Registering"**. Aguarde uns momentos e repita o comando show, até que a saída apresente **"registrationState": "Registered"**.

3. Crie o ambiente. Este passo tem de ser executado uma vez por ambiente (por exemplo, uma vez por ambiente de desenvolvimento e uma vez por ambiente de produção). Utilize o _modo local_ para o primeiro. (Pode experimentar o comutador `-c` ou `--cluster` no comando seguinte para configurar um ambiente no _modo de cluster_ mais tarde.)

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Siga as instruções no ecrã para aprovisionar uma conta de armazenamento para armazenar as imagens do Docker, um registo do ACR (Azure Container Registry) para listar as imagens do Docker e uma conta do AppInsight para recolher telemetria. Se tiver utilizado o comutador `-c`, este também cria um cluster do ACS (Azure Container Service).
   
   O nome do cluster é uma forma de identificar o ambiente. A localização deve ser a mesma da localização da conta de Gestão de Modelos que criou no portal do Azure.

4. Criar uma conta de Gestão de Modelos (esta é uma configuração única)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Defina a conta de Gestão de Modelos.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Defina o ambiente.
Depois de concluída a configuração, defina as variáveis de ambiente necessárias para operacionalizar através do comando seguinte. Utilize o mesmo nome de ambiente que utilizou anteriormente no passo 4. Utilize o mesmo nome de grupo de recursos que foi produzido na janela do comando quando o processo de configuração foi concluído.
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
1. Utilize o comando seguinte para criar um serviço Web em tempo real:

   ```azurecli
   az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   É gerado um ID de serviço Web que pode utilizar mais tarde.

   Os comutadores seguintes são utilizados com o comando **az ml service create realtime**:
   * -n: nome da aplicação, tem de estar em minúsculas.
   * -f: nome do ficheiro do script de classificação
   * --model-file: ficheiro do modelo; neste caso é o ficheiro model.pkl pickled
   * -r: tipo de modelo; neste caso, é um modelo de Python
   * --collect-model-data true: ativa a recolha de dados

   >[!IMPORTANT]
   >O nome do serviço (que também é o nome da imagem do Docker nova) tem de estar em minúsculas, caso contrário, é devolvido um erro. 

2. Quando executa o comando, o modelo e o ficheiro de classificação são carregados para a conta de armazenamento que criou como parte da configuração do ambiente. O processo de implementação cria uma imagem do Docker com o modelo, o esquema, o ficheiro de classificação no mesmo, e envia-o para o registo do ACR: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   Depois, extrai a imagem localmente para o computador e inicia um contentor do Docker com base na mesma. Se o seu ambiente estiver configurado no modo de cluster, o contentor do Docker é implementado no cluster Kubernetes do ACS.

   Como parte da implementação, é criado um ponto final REST HTTP para o serviço Web no seu computador local. Após alguns minutos, o comando deve ser concluído com uma mensagem de êxito e o serviço Web está pronto para a ação!

3. Pode utilizar o comando **docker ps** para ver o contentor do Docker em execução:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-using-separate-commands"></a>Criar um serviço Web em tempo real com comandos separados
Como alternativa ao comando **az ml service create realtime** mostrado acima, também pode realizar os passos em separado. Primeiro, registe o modelo, gere o manifesto, crie a imagem do Docker e crie o serviço Web. Esta abordagem passo a passo dá-lhe mais flexibilidade a cada passo. Além disso, pode reutilizar as entidades geradas no passo anterior e recriá-las apenas quando necessário.

1. Indique o nome do ficheiro pickle para registar o modelo.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Este comando gera um ID de modelo.

2. Crie o manifesto.

   Para criar um manifesto, utilize este comando e indique a saída do ID de modelo do passo anterior:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris_score.py -r python -i <model ID> -s service_schema.json
   ```
   Este comando gera um ID de manifesto.

3. Crie uma imagem do Docker.

   Para criar uma imagem do Docker, utilize este comando e indique a saída do valor de ID de manifesto do passo anterior:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Este comando gera um ID de imagem do Docker.
   
4. Criar o serviço

   Para criar um serviço, utilize o comando listado e indique a saída de ID de imagem do passo anterior:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Este comando gera um ID de serviço Web.

Está agora pronto para executar o serviço Web.

## <a name="run-the-real-time-web-service"></a>Executar o serviço Web em tempo real

Teste o serviço Web **irisapp** que está em execução. Para tal, alimente-o com um registo codificado JSON que contenha uma matriz de quatro números aleatórios.

1. A criação do serviço Web incluía dados de exemplo. Quando em execução no modo local, pode chamar o comando **az ml service show realtime**. Esta chamada obtém um comando de execução de exemplo que é útil para testar o serviço. Também obtém o url de classificação que pode utilizar para incorporar o serviço na sua própria aplicação personalizada:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. Para testar o serviço, execute o comando de execução de serviço devolvido.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   O resultado é **"2",**, que é a classe prevista. (O seu resultado poderá ser diferente.) 

3. Se quiser executar o serviço fora da CLI, tem de obter as chaves para a autenticação:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Ver os dados recolhidos no armazenamento de blobs do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Localize as suas Contas de Armazenamento. Para tal, clique em **Mais Serviços**.

3. Na caixa de pesquisa, escreva **Contas de armazenamento** e prima **Enter**.

4. Na página de pesquisa das **Contas de armazenamento**, selecione o recurso **Conta de armazenamento** que corresponde ao seu ambiente. 

   > [!TIP]
   > Para determinar que conta de armazenamento está a ser utilizada: abra o Azure Machine Learning Workbench, selecione o projeto em que está a trabalhar e abra a linha de comandos do menu **Ficheiro**. Na linha de comandos, escreva `az ml env show -v` e assinale o valor *storage_account*. Este é o nome da sua conta de armazenamento

5. Quando a página **Conta de armazenamento** se abrir, clique no item **Contentores**, na lista do lado esquerdo. Localizar o contentor com o nome **modeldata**. 
 
   Se não vir quaisquer dados, poderá ter de aguardar até dez minutos após o primeiro pedido de serviço Web para ver os dados começarem a ser propagados para a conta de armazenamento.

   Os dados fluem para os blobs com o caminho de contentor seguinte:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Pode consumir estes dados a partir dos blobs do Azure. Existem diversas ferramentas, quer na utilização de software Microsoft e de código aberto, como:

   - Azure ML Workbench: abra o ficheiro csv no Azure ML Workbench ao adicioná-lo como origem de dados. 
   - Excel: abra os ficheiros csv diários como uma folha de cálculo.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): crie gráficos com os dados extraídos a partir dos dados do csv nos blobs.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): carregue os dados do csv para uma tabela do Hive e execute consultas SQL diretamente no blob.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): crie um dataframe com uma grande quantidade de dados do csv.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Passos Seguintes
Nesta terceira parte da série do tutorial de três partes, aprendeu a utilizar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Localizar o ficheiro de modelo
> * Gerar um script de classificação e um ficheiro de esquema
> * Preparar o ambiente
> * Criar um serviço Web em tempo real
> * Executar o serviço Web em tempo real
> * Examinar os dados de blob de saída 

Executou com êxito um script de preparação em vários ambientes de computação, criou um modelo, serializou-o e operacioanalizou-o através de um serviço Web baseado no Docker. 

Está preparado para fazer preparação de dados avançada:
> [!div class="nextstepaction"]
> [Preparação de dados avançada](tutorial-bikeshare-dataprep.md)

