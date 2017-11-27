---
title: "Criar um modelo para os serviços do Azure Machine Learning (pré-visualização) | Microsoft Docs"
description: "Este tutorial completo mostra como utilizar os serviços do Azure Machine Learning (pré-visualização) ponto a ponto. Esta é a parte dois e aborda a experimentação."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 11/06/2017
ms.openlocfilehash: b723cc23ccbda6c5d39627682116cc314dcf2c0e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="classify-iris-part-2-build-a-model"></a>Classificar Íris, parte 2: criar um modelo
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados ponto a ponto integrada para os cientistas de dados profissionais prepararem dados, desenvolverem experimentações e implementarem modelos à escala da cloud.

Este tutorial é a parte dois de uma série de três partes. Nesta parte do tutorial, vai utilizar os serviços do Azure Machine Learning (pré-visualização) para:

> [!div class="checklist"]
> * Utilizar o Azure Machine Learning Workbench.
> * Abrir scripts e rever código.
> * Executar scripts num ambiente local.
> * Rever o histórico de execuções.
> * Executar scripts num ambiente do Docker local.
> * Executar scripts numa janela local da CLI do Azure.
> * Executar scripts num ambiente do Docker remoto.
> * Executar scripts num ambiente do Azure HDInsight na cloud.

Este tutorial utiliza o [conjunto de dados flor de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporal. As capturas de ecrã são específicas do Windows, mas a experiência em macOS é praticamente idêntica.

## <a name="prerequisites"></a>Pré-requisitos
Concluir a primeira parte desta série de tutoriais. Siga o [tutorial “Prepare data”](tutorial-classifying-iris-part-1.md) (“Preparar dados”) para criar recursos do Azure Machine Learning e instalar a aplicação Azure Machine Learning Workbench antes de iniciar os passos deste tutorial.

Opcionalmente, pode experimentar a execução de scripts em relação a um contentor de Docker local. Para fazê-lo, precisa de um motor do Docker (a Edição Community é suficiente) instalado e iniciado localmente no seu computador Windows ou MacOS. Para obter mais informações sobre a instalação do Docker, veja [Docker installation instructions](https://docs.docker.com/engine/installation/) (Instruções de instalação do Docker).

Para experimentar a emissão de scripts para execução num contentor do Docker numa VM do Azure remota ou num cluster do Spark do HDInsight, pode seguir as [ instruções para criar uma Máquina Virtual de Ciência de Dados do Azure baseada em Ubuntu ou um cluster do HDInsight](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Reveja os ficheiros de configuração e iris_sklearn.py
1. Abra a aplicação Azure Machine Learning Workbench e abra o projeto **myIris** que criou na parte anterior da série do tutorial.

2. Quando o projeto estiver aberto, selecione o botão **Ficheiros** (o ícone de pasta), no painel mais à esquerda), para abrir a lista de ficheiros na pasta do projeto.

3. Selecione o ficheiro **iris_sklearn.py**. O código de Python abre-se num novo separador do editor de texto dentro do Workbench.

   ![Abrir um ficheiro](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >O código que vir pode não ser exatamente igual ao código anterior, pois este projeto de exemplo é atualizado frequentemente.

4. Reveja o código do script de Python para se familiarizar com o estilo de programação. O script realiza as tarefas seguintes:

   - Carrega o pacote de preparação de dados **iris.dprep** para criar um [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Utilize o pacote de preparação de dados `iris.dprep` que vem com o projeto de exemplo e que deve ser o mesmo que o ficheiro `iris-1.dprep` que criou na parte 1 do tutorial.

   - Adiciona funcionalidades aleatórias para tornar o problema mais difícil de resolver. A aleatoriedade é necessária porque o Íris é um conjunto de dados pequeno que pode ser facilmente classificado com uma precisão de quase 100%.

   - Utiliza a biblioteca de machine learning [scikit-learn](http://scikit-learn.org/stable/index.html) para criar um modelo de regressão logística simples. 

   - Serializa o modelo através da inserção da biblioteca [pickle](https://docs.python.org/2/library/pickle.html) num ficheiro na pasta `outputs`. Em seguida, o script carrega-o e anula a serialização de volta para a memória.

   - Utiliza o modelo com a serialização anulada para fazer uma predição num registo novo. 

   - Desenha dois gráficos, uma matriz de confusão e uma curva ROC (receiver operating characteristic) multiclasse mediante a utilização da biblioteca [matplotlib](https://matplotlib.org/) e guarda-os na pasta `outputs`.

   - O objeto `run_logger` é utilizado transversalmente para registar a taxa de regularização e para modelar a exatidão nos registos. Os registos são desenhados automaticamente no histórico de execuções.


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>Executar o script iris_sklearn.py num ambiente local

Vamos preparar-nos para executar o script **iris_sklearn.py** pela primeira vez. Este script requer os pacotes **scikit-learn** e **matplotlib**. O pacote **scikit-Saiba** já está instalado pelo Azure Machine Learning Workbench. No entanto, ainda é necessário instalar **matplotlib**. 

1. No Azure Machine Learning Workbench, selecione o menu **Ficheiro** e selecione **Abrir Linha de Comandos**, para abrir a linha de comandos. Referimo-nos a esta janela da interface de linha de comandos como *janela da CLI do Azure Machine Learning Workbench* ou *janela da CLI*, para abreviar.

2. Na janela da CLI, introduza o comando seguinte para instalar o pacote de Python **matplotlib**. A instalação deve demorar menos de um minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Se ignorar o comando `pip install` anterior, o código em `iris_sklearn.py` é executada com êxito. Se executar apenas `iris_sklearn.py`, o código não produz os gráficos da saída da matriz de confusão nem da curva COR multiclasse, conforme mostrado nas visualizações do histórico.

3. Regresse à janela da aplicação Workbench. 

4. Na barra de ferramentas, na parte superior do separador **iris_sklearn.py**, selecione para abrir o menu pendente junto ao ícone **Guardar** e selecione **Configuração da Execução**. Escolha **local** como o ambiente de execução e introduza `iris_sklearn.py` como o script a executar.

5. Em seguida, vá para o lado direito da barra de ferramentas e introduza `0.01` no campo **Argumentos**. 

   ![Controlo de execução](media/tutorial-classifying-iris/run_control.png)

6. Selecionar o botão **Executar**. É agendado imediatamente um trabalho. O trabalho é listado no painel **Trabalhos**, no lado direito da janela do Workbench. 

7. Ao fim de alguns instantes, o estado do trabalho passa de **A submeter** para **Em execução** e, por fim, para **Concluído**.

   ![Executar sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Selecione **Concluído** no texto do estado do trabalho, no painel **Trabalhos**. Abre-se uma janela de pop-up, que apresenta o texto de saída padrão (stdout) do script em execução. Para fechar o texto stdout, selecione o botão **Fechar** (**x**), no canto superior direito da janela do pop-up.

9. No mesmo estado de trabalho no painel **Trabalhos**, selecione o texto azul **iris_sklearn.py [n]** (_n_ é o número de execuções) imediatamente acima do estado **Concluído** e da hora de início. É aberta a janela **Propriedades da Execução**, que mostra as informações abaixo relativas a essa execução:
   - Informações das **Propriedades da Execução**
   - Ficheiros de **saídas**
   - **Visualizações**, se existentes
   - **Registos** 

   Quando a execução estiver concluída, a janela de pop-up mostra os resultados abaixo:

   >[!NOTE]
   >Uma vez que introduzimos alguma aleatoriedade no conjunto de preparação anteriormente, os resultados exatos podem ser ligeiramente diferentes dos apresentados aqui.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```

10. Feche o separador **Propriedades da Execução** e regresse ao separador **iris_sklearn.py**. 

11. Repita execuções adicionais. 

    Introduza uma série de valores numéricos diferentes no campo **Argumentos**, entre `0.001` e `10`. Selecione **Executar** para executar o código mais algumas vezes. O valor de argumento que alterar em cada execução é fornecido ao algoritmo de regressão logística no código, o que origina sempre conclusões diferentes.

## <a name="review-the-run-history-in-detail"></a>Rever o histórico de execuções em detalhe
No Azure Machine Learning Workbench, cada execução de script é capturada como um registo de histórico de execuções. Se abrir a vista **Execuções**, pode ver o histórico de execuções de scripts específicos.

1. Para abrir a lista de **Execuções**, selecione o botão **Execuções** (ícone de relógio), na barra de ferramentas do lado esquerdo. Em seguida, selecione **iris_sklearn.py** para mostrar o **Dashboard da Execução** de `iris_sklearn.py`.

   ![Vista de execução](media/tutorial-classifying-iris/run_view.png)

2. O separador **Dashboard da Execução** abre-se. Reveja as estatísticas capturadas nas várias execuções. Os gráficos são compostos na parte superior do separador. Cada execução tem um número consecutivo e os detalhes da execução estão listados na tabela na parte inferior do ecrã.

   ![Dashboard da execução](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtre a tabela e selecione um dos gráficos para ver o estado, a duração, a precisão e a taxa de regularização de cada execução. 

4. Selecione duas ou três execuções na tabela **Execuções** e selecione o botão **Comparar** para abrir um painel de comparação detalhada. Reveja a comparação lado a lado. Selecione o botão de retrocesso **Lista de Execuções**, no canto superior esquerdo do painel **Comparação**, para regressar ao **Dashboard da Execução**.

5. Selecione uma execução individual para ver a vista de detalhes da mesma. Repare que as estatísticas da execução selecionada são listadas na secção **Propriedades da Execução**. Os ficheiros escritos na pasta de saída são listados na secção **Saídas** e podem ser transferidos a partir daí.

   ![Detalhes da Execução](media/tutorial-classifying-iris/run_details.png)

   Os dois gráficos -- matriz de confusão e curva COR multiclasse -- são compostos na secção **Visualizações**. Também estão disponíveis na secção **Registos** todos os ficheiros de registos.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Executar scripts no ambiente Docker local

Com o Machine Learning, pode configurar facilmente ambientes de execução adicionais, como o Docker, e executar o script nos mesmos. 

>[!IMPORTANT]
>Para realizar este passo, tem de ter um motor do Docker instalado localmente e iniciado. Para obter mais informações, veja as instruções de instalação do Docker.

1. No painel do lado esquerdo, selecione o ícone de **Pasta** para abrir a lista **Ficheiros** do seu projeto. Expanda a pasta `aml_config`. 

2. Existem vários ambientes pré-configurados, como, por exemplo, **docker-python**, **docker-spark** e **local**. 

   Cada ambiente tem dois ficheiros, como `docker-python.compute` e `docker-python.runconfig`. Abra cada um dos ficheiros e verá que algumas opções podem ser configuradas no editor de texto.  

   Para limpar, selecione **Fechar** (**x**) nos separadores de qualquer editor aberto.

3. Execute o script **iris_sklearn.py** com o ambiente **docker-python**: 

   - Na barra de ferramentas do lado esquerdo, selecione o ícone de **Relógio** para abrir o painel **Execuções**. Selecione **Todas as Execuções**. 
   - Na parte superior do separador **Todas as Execuções**, selecione **docker-python** como o ambiente de destino em vez do ambiente **local** predefinido. 
   - Em seguida, vá para o lado direito e selecione **iris_sklearn.py** como o script a executar. 
   - Deixe o campo **Argumentos** em branco, pois o script especifica um valor predefinido. 
   - Selecionar o botão **Executar**.

4. Observe que é iniciado um trabalho novo. Aparece no painel **Trabalhos**, no lado direito da janela do Workbench.

   Quando executar no Docker pela primeira vez, a conclusão demora mais alguns minutos. 

   Nos bastidores, o Azure Machine Learning Workbench compila um novo ficheiro de docker. 
   O ficheiro novo referencia a imagem base do Docker especificada no ficheiro `docker.compute` e os pacotes de Python de dependência especificados no ficheiro `conda_dependencies.yml`. 
   
   O motor do Docker realiza as seguintes tarefas:

    - Transfere a imagem base a partir do Azure.
    - Instala os pacotes de Python especificados no ficheiro `conda_dependencies.yml`.
    - Inicia um contentor do Docker.
    - Copia ou referencia, dependendo da configuração da execução, a cópia local da pasta do projeto.      
    - Executa o script `iris_sklearn.py`.

   No final, deverá ver o mesmo resultado de quando **local** é o destino.

5. Agora, vamos experimentar o Spark. A imagem base do Docker contém uma instância do Spark previamente instalada e configurada. Devido a esta instância, pode executar um script PySpark na mesma. Esta é uma forma simples de desenvolver e testar o seu programa Spark sem ter de perder tempo a instalar e configurar o Spark. 

   Abra o ficheiro `iris_spark.py`. Este script carrega o ficheiro de dados `iris.csv` e utiliza o algoritmo de regressão logística da biblioteca de Spark do Machine Learning para classificar o conjunto de dados Íris. Agora, altere o ambiente de execução para **docker-spark** e o script para **iris_spark.py** e volte a executá-lo. Este processo demora um pouco mais, uma vez que tem de ser criada e iniciada uma sessão do Spark dentro do contentor do Docker. Também pode ver que o stdout é diferente do stdout de `iris_spark.py`.

6. Faça mais algumas execuções e experimente diferentes argumentos. 

7. Abra o ficheiro `iris_spark.py` para ver o modelo de regressão logística criado com a biblioteca de Spark do Machine Learning. 

8. Interaja com o painel **Trabalhos**, execute uma vista de lista do histórico de execuções e execute uma vista de detalhes das suas execuções em diferentes ambientes.

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Executar scripts na janela da CLI do Azure Machine Learning

1. No Azure Machine Learning Workbench, abra a janela da linha de comandos, selecione o menu **Ficheiro** e selecione **Abrir Linha de Comandos**. A linha de comandos inicia-se na pasta de projeto com o comando `C:\Temp\myIris\>`.

   >[!IMPORTANT]
   >Tem de utilizar a janela da linha de comandos (aberta no Workbench) para realizar os passos seguintes.

2. Utilize a linha de comandos para iniciar sessão no Azure. 

   A aplicação Workbench e a CLI utilizam caches de credenciais independentes na autenticação em recursos do Azure. Só tem de o fazer uma vez até que o token em cache expire. O comando **az account list** devolve a lista de subscrições disponíveis para o seu início de sessão. Se houver mais de uma, utilize o valor do ID da subscrição pretendida. Defina essa subscrição como a conta predefinida para utilização com o comando **az account set -s** e indique o valor do ID da mesma. Em seguida, utilize o comando account **show** para confirmar a definição.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Após a conclusão da autenticação e de o contexto da subscrição do Azure atual estar definido, introduza os comandos seguintes na janela da CLI para instalar **matplotlib** e submeta o script de Python como experimentação para execução.

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Reveja a saída. A saída e os resultados são iguais aos que obteve quando utilizou o Workbench para executar o script. 

5. Se tiver o Docker instalado no computador, execute o mesmo script novamente com o ambiente de execução do Docker.

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. No Workbench, selecione o ícone de **Pasta**, no painel do lado esquerdo, para listar os ficheiros do projeto e abra o script de Python com o nome **run.py**. 

   Este script é útil para realizar um ciclo através de vários taxas de regularização. Execute a experimentação múltiplas vezes com estas taxas. Este script inicia um trabalho `iris_sklearn.py` com uma taxa de regularização de `10.0` (um número extremamente grande). Depois, o script diminui a taxa para metade na execução seguinte, e assim sucessivamente, até que a taxa não seja inferior a `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Para abrir o script **run.py** a partir da linha de comandos, execute os comandos seguintes:

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Após a conclusão de `run.py`, é apresentado um gráfico na vista de lista do histórico de execuções no Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Executar num contentor do Docker num computador remoto
Para executar o script num contentor do Docker num computador Linux remoto, tem de ter acesso SSH (nome de utilizador e palavra-passe) a esse computador remoto. Além disso, este computador remoto tem de ter um motor do Docker instalado e em execução. A forma mais fácil de obter um computador Linux destes é criar uma Máquina Virtual de Ciência de Dados (DSVM) baseada em Ubuntu no Azure. Saiba [como criar um DSVM em Ubuntu para utilizar no Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>Tenha em conta que a DSVM baseada em CentOS *não* é suportada.

1. Quando a VM estiver criada, pode ligá-la como ambiente de execução se gerar um par de ficheiros `.runconfig` e `.compute`. Utilize o comando abaixo para criá-los. Vamos dar o nome `myvm` ao ambiente novo.
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >O endereço IP também pode ser um nome de domínio completamente qualificado (FQDN) endereçável publicamente, como `vm-name.southcentralus.cloudapp.azure.com`. É boa prática adicionar o FQDN à sua DSVM e utilizá-lo aqui em vez de um endereço IP. Esta prática é útil porque poderá desativar a VM a determinada altura para poupar custos. Além disso, da próxima vez que iniciar a VM, o endereço IP pode ter sido alterado.

   Em seguida, execute o comando seguinte para criar a imagem do Docker na VM, para prepará-la para executar os scripts:
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Também pode alterar o valor de `PrepareEnvironment` em `myvm.runconfig` da predefinição de `false` para `true`. Esta alteração prepara automaticamente o contentor do Docker na primeira execução.

2. Edite o ficheiro `myvm.runconfig` gerado em `aml_config` e altere a estrutura da predefinição de `PySpark` para `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Deixar a definição da estrutura como PySpark também deverá funcionar. Contudo, é menos ineficiente se não precisar verdadeiramente de uma sessão do Spark para executar o seu script de Python.

3. Emita o comando utilizado anteriormente na janela da CLI, mas desta vez vamos escolher _myvm_ como o destino:
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   O comando é executado como se estivesse a utilizar um ambiente `docker-python`, com a diferença de que a execução tem lugar na VM do Linux remota. A janela da CLI apresenta as mesmas informações de saída.

4. Vamos experimentar com o Spark no contentor. Abra o Explorador de Ficheiros. Também o pode fazer na janela da CLI se estiver familiarizado com os comandos básicos de manipulação de ficheiros. Faça uma cópia do ficheiro `myvm.runconfig` e dê-lhe o nome `myvm-spark.runconfig`. Edite o ficheiro novo para alterar a definição `Framework` de `Python` para `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Não faça nenhuma alteração ao ficheiro `myvm.compute`. Para a execução no Spark, é utilizada a mesma imagem do Docker na mesma VM. No `myvm-spark.runconfig` novo, o campo `target` aponta para o mesmo ficheiro `myvm.compute` através do respetivo nome `myvm`.

5. Escreva o comando seguinte para executá-lo na instância do Spark no contentor do Docker remoto:
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Executar o script num cluster do HDInsight
Também pode executar este script num cluster do HDInsight Spark. Saiba [como criar o Cluster do Spark HDInsight para utilizar no Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>![NOTA] O cluster do HDInsight tem de utilizar o Blob do Azure como armazenamento principal. A utilização do armazenamento do Azure Data Lake ainda não é suportada.

1. Se tiver acesso a um cluster do Spark para o Azure HDInsight, gere um comando de configuração de execução do HDInsight, conforme mostrado aqui. Indique como parâmetros o nome do cluster do HDInsight e o seu nome de utilizador e a sua palavra-passe do HDInsight. Utilize o seguinte comando:

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   Tipicamente, o FQDN do nó principal do cluster é `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` é o nome de utilizador de SSH do cluster. O valor predefinido é `sshuser`, se não o alterar durante a configuração do HDInsight. Não é `admin`, que é o outro utilizador criado durante a configuração e que permite o acesso ao Web site de administração do cluster. 

2. Execute o comando seguinte e o script é executado no cluster do HDInsight:

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Quando executa num cluster do HDInsight remoto, também pode ver os detalhes da execução do trabalho do Yet Another Resource Negotiator (YARN), em `https://<cluster_name>.azurehdinsight.net/yarnui`, com a conta de utilizador `admin`.


## <a name="next-steps"></a>Passos seguintes
Nesta segunda parte da série do tutorial de três partes, aprendeu a utilizar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Utilizar o Azure Machine Learning Workbench.
> * Abrir scripts e rever código.
> * Executar scripts num ambiente local.
> * Rever o histórico de execuções.
> * Executar scripts num ambiente do Docker local.
> * Executar scripts numa janela local da CLI do Azure.
> * Executar scripts num ambiente do Docker remoto.
> * Executar scripts num ambiente do HDInsight na cloud.

Está pronto para avançar para a terceira parte da série. Agora que criou o modelo de regressão logística, vamos implementá-lo como um serviço Web em tempo real.

> [!div class="nextstepaction"]
> [Implementar um modelo](tutorial-classifying-iris-part-3.md)
