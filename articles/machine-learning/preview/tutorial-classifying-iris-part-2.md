---
title: "Criar um Modelo para os serviços do Azure Machine Learning (pré-visualização) | Microsoft Docs"
description: "Este tutorial completo mostra como utilizar os serviços do Azure Machine Learning (pré-visualização) ponto a ponto. Esta é a parte 2 sobre a experimentação."
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
ms.openlocfilehash: 5d86f3bdf19603d2f92fc1a704376beefd7323c0
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="classifying-iris-part-2-build-a-model"></a>Classificar Íris, parte 2: criar um modelo
Os serviços do Azure Machine Learning (pré-visualização) são uma solução de análise avançada e ciência de dados ponto a ponto integrada para os cientistas de dados profissionais prepararem dados, desenvolverem experimentações e implementarem modelos à escala da cloud.

Este tutorial é a parte dois de uma série de três partes. Nesta parte do tutorial, vai utilizar os serviços do Azure Machine Learning (pré-visualização) para aprender a:

> [!div class="checklist"]
> * Trabalhar com o Azure Machine Learning Workbench
> * Abrir scripts e rever código
> * Executar scripts num ambiente local
> * Rever o histórico de execuções
> * Executar scripts num ambiente Docker local
> * Executar scripts numa janela local da CLI do Azure
> * Executar scripts num ambiente Docker remoto
> * Executar scripts num ambiente HDInsight na cloud

Para simplificar, este tutorial utiliza o intemporal [conjunto de dados flor de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). As capturas de ecrã são específicas do Windows, mas a experiência de macOS é praticamente idêntica.

## <a name="prerequisites"></a>Pré-requisitos
Deve concluir a primeira parte desta série de tutoriais. Siga o [tutorial “Prepare data”](tutorial-classifying-iris-part-1.md) (“Preparar dados”) para criar recursos do Azure Machine Learning e instalar a aplicação Azure Machine Learning Workbench antes de iniciar os passos deste tutorial.

Opcionalmente, pode experimentar a execução de scripts em relação a um contentor de Docker local. Para fazê-lo, irá precisar de um motor do Docker (a Edição Community é suficiente) instalado e iniciado localmente no seu computador Windows ou macOS. Leia mais sobre [Docker installation instruction (Instruções de instalação do Docker)](https://docs.docker.com/engine/installation/).

Se pretender experimentar com emissão de script para executar num contentor do Docker numa VM remota do Azure ou um cluster Spark do HDInsight, pode seguir [ instruções para criar uma Máquina Virtual de Ciência de Dados do Azure baseada em Ubuntu ou cluster do HDI](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-configuration-files"></a>Reveja os ficheiros de configuração e iris_sklearn.py
1. Inicie a aplicação **Azure Machine Learning Workbench** e abra o projeto **myIris** que criou na parte anterior da série de tutoriais.

2. Quando o projeto estiver aberto, clique no botão **Ficheiros** (ícone de pasta), na barra de ferramentas do lado esquerdo no Azure Machine Learning Workbench, para abrir a lista de ficheiros na pasta do seu projeto.

3. Selecione o ficheiro **iris_sklearn.py** e o código de Python abre-se num novo separador do editor de texto dentro do Workbench.

   ![abrir ficheiro](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >O código que vir pode não ser exatamente igual ao código anterior, uma vez que este projeto de exemplo é atualizado frequentemente.

4. Reveja o código do script de Python para se familiarizar com o estilo de programação. Tenha em conta que o script realiza as tarefas seguintes:

   - Carrega o pacote de preparação de dados **iris.dprep** para criar um [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Estamos a utilizar o pacote de preparação de dados `iris.dprep` que vem com o projeto de exemplo e que deve ser o mesmo que o ficheiro `iris-1.dprep` que criou na parte 1 do tutorial.

   - Adiciona funcionalidades aleatórias para tornar o problema mais difícil de resolver. (A aleatoriedade é necessária porque o Íris é um conjunto de dados pequeno que pode ser facilmente classificado com uma precisão de 100%.)

   - Utiliza a biblioteca de machine learning [scikit-Saiba](http://scikit-learn.org/stable/index.html) para criar um modelo de Regressão Logística simples. 

   - Serializa o modelo através da utilização da biblioteca [pickle](https://docs.python.org/2/library/pickle.html) num ficheiro na pasta `outputs` e, em seguida, carrega-o e anula a serialização novamente para a memória.

   - Utiliza o modelo com a serialização anulada para fazer a predição num registo novo. 

   - Utiliza a biblioteca [matplotlib](https://matplotlib.org/) para desenhar dois gráficos -- matriz de confusão e curva COR multiclasse -- e guarda-os na pasta `outputs`.

   - O objeto `run_logger` é sempre utilizado para registar a taxa de regularização e a precisão do modelo em registos, os quais são desenhados automaticamente no histórico de utilizações.


## <a name="execute-irissklearnpy-script-in-local-environment"></a>Executar o script iris_sklearn.py num ambiente local

Vamos preparar-nos para executar o script **iris_sklearn.py** pela primeira vez. Este script requer os pacotes scikit-learn e matplotlib. **scikit-learn** já está instalado pelo Azure ML Workbench. No entanto, temos de instalar **matplotlib**. 

1. No Azure Machine Learning Workbench, clique no menu **Ficheiro** e escolha **Abrir Linha de Comandos** para iniciar a linha de comandos. Referimo-nos a esta janela da interface de linha de comandos como janela da CLI do Azure Machine Learning Workbench ou janela da CLI, para abreviar.

2. Na janela da CLI, escreva o comando seguinte para instalar o pacote de Python **matplotlib**. A instalação deve demorar menos de um minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Se ignorar o comando `pip install` anterior, o código em `iris_sklearn.py` é executado com êxito, mas não produz os gráficos da saída da matriz de confusão nem da curva COR multiclasse, conforme mostrado nas visualizações do histórico.

3. Regresse à janela da aplicação Workbench. 

4. No canto superior esquerdo do separador **iris_sklearn.py**, junto ao ícone de guardar, clique no menu pendente para escolher a seleção **Configuração de Execução**.  Escolha **local** como o ambiente de execução e `iris_sklearn.py` como o script a executar.

5. Em seguida, avance para o lado direito do mesmo separador e preencha o campo **Argumentos** com o valor `0.01`. 

   ![img](media/tutorial-classifying-iris/run_control.png)

6. Clique no botão **Executar**. É agendado imediatamente um trabalho. O trabalho é listado no painel **Trabalhos**, no lado direito da janela do Workbench. 

7. Ao fim de alguns instantes, o estado do trabalho passa de **A submeter** para **Em execução** e, por fim, para **Concluído**, ao fim de pouco tempo.

   ![executar sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Clique na palavra **Concluído** no texto do estado do trabalho no painel Trabalhos. Abre-se uma janela de pop-up, que apresenta o texto de saída padrão (stdout) do script em execução. Para fechar o texto stdout, clique no botão **X**, no canto superior direito do pop-up.

9. No mesmo estado de trabalho no painel Trabalhos, clique no texto azul **iris_sklearn.py [1]**, (_n_ é o número de execuções) imediatamente acima do estado **Concluído** e da hora de início. Abre-se a página **Propriedades da Execução**, que mostra as informações das Propriedades da Execução, os ficheiros de **Saída**, eventuais **Visualizações** e os **Registos** dessa execução específica. 

   Quando a execução estiver concluída, a janela de pop-up mostra os seguintes resultados:

   >[!NOTE]
   >Uma vez que introduzimos alguma aleatoriedade no conjunto de preparação anteriormente, os resultados exatos podem ser ligeiramente diferentes.

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
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. Feche o separador **Propriedades da Execução** e regresse ao separador **iris_sklearn.py**. 

11. Repita execuções adicionais. 

    Introduza uma série de valores numéricos diferentes no campo **Argumentos**, entre `0.001` e `10`. Clique em **Executar** para executar o código mais algumas vezes. O valor de argumento que alterar em cada execução é fornecido ao algoritmo de Regressão Logística no código, o que origina sempre resultados diferentes.

## <a name="review-run-history-in-detail"></a>Rever o Histórico de Execuções em detalhe
No Azure Machine Learning Workbench, cada execução de script é capturada como um registo de histórico de execuções. Pode abrir a vista **Execuções** para ver o histórico de execuções de scripts específicos.

1. Clique no botão **Execuções** (ícone de relógio), na barra de ferramentas do lado esquerdo, para abrir a lista de**Execuções**. Em seguida, clique em **iris_sklearn.py** para mostrar o **Dashboard da Execução** de `iris_sklearn.py`.

   ![img](media/tutorial-classifying-iris/run_view.png)

2. O separador **Dashboard da Execução** abre-se. Reveja as estatísticas capturadas nas várias execuções. São criados gráficos na parte superior do separador e todas as execuções numeradas com detalhes são listadas na tabela na parte inferior da página.

   ![img](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtre a tabela e clique nos gráficos interativamente para ver o estado, a duração, a precisão e a taxa de regularização de cada execução. 

4. Selecione duas ou três execuções na tabela **Execuções** e clique no botão **Comparar** para abrir uma página de comparação detalhada. Reveja a comparação lado a lado. Clique no botão de retrocesso **Lista de execuções**, no canto superior esquerdo da página de comparação, para regressar ao **Dashboard da Execução**.

5. Clique numa execução individual para ver a vista de detalhes da mesma. Repare que as estatísticas da execução selecionada são listadas na secção _Propriedades da Execução_. Os ficheiros escritos na pasta de saída são listados na secção **Saída** e podem ser transferidos.

   ![img](media/tutorial-classifying-iris/run_details.png)

   Os dois gráficos -- matriz de confusão e curva COR multiclasse -- são compostos na secção **Visualizações**. Também estão disponíveis na secção **Registos** todos os ficheiros de registos.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Executar scripts no ambiente Docker local

O Azure ML permite-lhe configurar facilmente ambientes de execução adicionais, como o Docker, e executar o script nos mesmos. 

>[!IMPORTANT]
>Para realizar este passo, tem de ter o motor do Docker instalado localmente e iniciado. Veja o guia de instalação para obter mais detalhes.

1. Na barra de ferramentas do lado esquerdo, selecione o ícone de pasta para abrir a lista de **Ficheiros** do seu projeto. Expanda a pasta `aml_config`. 

2. Repare que existem vários ambientes pré-configurados, como, por exemplo, **docker-python**, **docker-spark** e **local**. 

   Cada ambiente tem dois ficheiros, como `docker-python.compute` e `docker-python.runconfig`. Abra cada um dos ficheiros e verá que algumas opções podem ser configuradas no editor de texto.  

   Feche (X) os separadores de qualquer editor aberto para limpar.

3. Execute o script **iris_sklearn.py** com o ambiente **docker-python**. 

   - Na barra de ferramentas da esquerda, clique no ícone de relógio para abrir o painel **Execuções**. Clique em **Todas as Execuções**. 
   - Na parte superior do separador **Todas as Execuções**, escolha **docker-python** como o ambiente de destino em vez do ambiente **local** predefinido. 
   - Em seguida, no lado direito, escolha **iris_sklearn.py** como o script a executar. 
   - Deixe o campo **Argumentos** em branco, uma vez que o script especifica um valor predefinido. 
   - Clique no botão **Executar**.

4. Observe que é iniciado um trabalho novo, conforme mostrado no painel **Trabalhos**, à direita da janela do Workbench.

   Na primeira vez que estiver a executar no Docker, a conclusão demora mais alguns minutos. 

   Em segundo plano, o Azure Machine Learning Workbench cria um ficheiro do Docker novo que referencia a imagem base do Docker especificada no ficheiro `docker.compute` e os pacotes Python de dependência especificados no ficheiro `conda_dependencies.yml`. O motor do Docker transfere, então, a imagem base a partir do Azure, instala os pacotes Python especificados no ficheiro `conda_dependencies.yml` e inicia um contentor do Docker. Depois, copia (ou referencia, consoante a configuração da execução) a cópia local da pasta do projeto e executa o script `iris_sklearn.py`. No final, deverá ver o mesmo resultado de quando **local** é o destino.

5. Agora, vamos experimentar o Spark. A imagem base do Docker contém uma instância do Spark previamente instalada e configurada. Por este motivo, pode executar um script PySpark na mesma. Esta é uma forma simples de desenvolver e testar o seu programa Spark sem ter de perder tempo a instalar e configurar o Spark. 

   Abra o ficheiro `iris_pyspark.py`. Este script carrega o ficheiro de dados `iris.csv` e utiliza o algoritmo de Regressão Logística da biblioteca de Spark ML para classificar o conjunto de dados Íris. Agora, altere o ambiente de execução para **docker-spark** e o script para **iris_pyspark.py** e volte a executar. Isto demora um pouco mais, uma vez que tem de ser criada e iniciada uma sessão do Spark dentro do contentor do Docker. Também pode ver que o stdout é diferente do stdout de `iris_pyspark.py`.

6. Faça mais algumas execuções e experimente diferentes argumentos. 

7. Abra o ficheiro `iris_pyspark.py` para ver o modelo de Regressão Logística simples criado com a biblioteca de Spark ML. 

8. Interaja com o painel **Trabalhos**, com a vista de lista do histórico de execuções e com a vista de detalhes das suas execuções em diferentes ambientes.

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Executar Scripts na janela da CLI do Azure ML

1. Utilizando o Azure Machine Learning Workbench, inicie a janela de linha de comandos ao clicar no menu **Ficheiro** e, em seguida, em **Abrir Linha de Comandos**. A linha de comandos inicia-se na pasta de projeto com o comando `C:\Temp\myIris\>`.

   >[!Important]
   >Tem de utilizar a janela da linha de comandos (iniciada a partir do Workbench) para realizar os seguintes passos:

2. Utilize a linha de comandos (CLI) para iniciar sessão no Azure. 

   A aplicação Workbench e a CLI utilizam caches de credenciais independentes na autenticação em recursos do Azure. Só tem de o fazer uma vez, até que o token em cache expire. O comando **az account list** devolve a lista de subscrições disponíveis para o seu início de sessão. Se houver mais de uma, utilize o valor do ID da subscrição pretendida e defina-a como a conta predefinida a utilizar com o comando **az set account -s**, indicando o valor de ID da subscrição. Em seguida, utilize o comando account show para confirmar a definição.

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. Quando estiver autenticado e o contexto da subscrição do Azure atual estiver definido, escreva os comandos seguintes na janela da CLI para instalar matplotlib e submeta o script de Python como experimentação para execução.

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Reveja a saída. Repare na mesma saída e no mesmo resultado que executou anteriormente neste tutorial mediante a utilização do Workbench para executar o script. 

5. Se tiver o Docker instalado no computador, execute o mesmo script com o ambiente de execução do Docker.

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. No Azure Machine Learning Workbench, clique no ícone Pasta, na barra de ferramentas do lado esquerdo, para listar os projetos de ficheiros e abra o script de Python com o nome **run.py**. 

   Este script é útil para alternar entre várias taxas de regularização e executar a experimentação diversas vezes com esses taxas. Este script inicia um trabalho `iris_sklearn.py` com uma taxa de regularização de `10.0` (um número ridiculamente grande) e reduz a taxa para metade na próxima execução, e assim sucessivamente, até a taxa não ser mais pequena do que `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Para iniciar o script **run.py** na linha de comandos, execute os comandos seguintes:

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Após a conclusão de `run.py`, é apresentado um gráfico na vista de lista do histórico de execuções no Azure Machine Learning Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Executar num contentor do Docker num computador remoto
Para executar o script num contentor do Docker num computador Linux remoto, tem de ter acesso SSH (nome de utilizador e palavra-passe) a esse computador remoto. Este computador remoto tem de ter o motor do Docker instalado e em execução. A forma mais fácil de obter um computador Linux destes é criar uma [Máquina Virtual de Ciência de Dados baseada em Ubuntu (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) no Azure. (Tenha em conta que a DSVM baseada em CentOS NÃO é suportada.) 

1. Quando a VM estiver criado, pode ligá-la como ambiente de execução mediante a geração de um par de ficheiros `.runconfig` e `.compute` com o comando abaixo: Vamos dar o nome `myvm` ao ambiente novo.
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >A área IP Address (Endereço IP) também pode ser um FQDN (nome de domínio completamente qualificado) endereçável publicamente, como `vm-name.southcentralus.cloudapp.azure.com`. É boa prática adicionar o FQDN à DSVM e utilizá-lo aqui em vez do endereço IP, uma vez que poderá, mais tarde, desativar a VM, para poupar nos custos. Além disso, da próxima vez que iniciar a VM, o endereço IP pode ter sido alterado.

   Em seguida, execute o comando seguinte para criar a imagem do Docker na VM para prepará-la para executar os scripts.
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Também pode alterar o valor de `PrepareEnvironment` em `myvm.runconfig` da predefinição `false` para `true`. Desta forma, o contentor do Docker é preparado automaticamente na primeira execução.

2. Edite o ficheiro `myvm.runconfig` gerado em `aml_config` e altere Framework da predefinição `PySpark` para `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Deixar a definição Framework como PySpark também deve funcionar. Contudo, é ligeiramente ineficaz se não precisar verdadeiramente de uma sessão do Spark para executar o seu script de Python.

3. Emita o comando utilizado anteriormente na janela da CLI, mas desta vez vamos escolher _myvm_ como o destino:
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   O comando é executado como se estivesse a utilizar um ambiente `docker-python`, com a diferença de que a execução tem lugar na VM do Linux remota. A janela da CLI apresenta as mesmas informações de saída.

4. Vamos experimentar o Spark no contentor. Abra o Explorador de Ficheiros (também o pode fazer na janela da CLI se estiver familiarizado com os comandos básicos de manipulação de ficheiros). Faça uma cópia do ficheiro `myvm.runconfig` e dê-lhe o nome `myvm-spark.runconfig`. Edite o ficheiro novo para alterar a definição `Framework` de `Python` para `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Não faça nenhuma alteração ao ficheiro `myvm.compute`. Para a execução no Spark, é utilizada a mesma imagem do Docker na mesma VM. No `myvy-spark.runconfig` novo, o campo `target` aponta para o mesmo ficheiro `myvm.compute` através do respetivo nome `myvm`.

5. Escreva o comando abaixo para executá-lo na instância do Spark no contentor do Docker remoto:
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Executar o script num cluster do HDInsight
Também pode executar este script num cluster do Spark real. 

1. Se tiver acesso a um cluster HDInsight do Spark para o Azure, gere um comando de configuração de execução HDI, conforme mostrado. Indique como parâmetros o nome do cluster do HDInsight, o seu nome de utilizador do HDInsight e a palavra-passe. Utilize o seguinte comando:

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   Normalmente, o nome de domínio completamente qualificado (FQDN) do nó principal do cluster é `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` é o nome de utilizador de SSH do cluster. O valor predefinido é `sshuser`, se não o alterar durante o aprovisionamento de HDI. Não é `admin`, que é o outro utilizador criado durante o aprovisionamento e que permite o acesso ao Web site de administração do cluster. 

2. Execute o comando seguinte e o script é executado no cluster do HDInsight:

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >Quando executa num cluster do HDI remoto, também pode ver os detalhes da execução do trabalho do YARN, em `https://<cluster_name>.azurehdinsight.net/yarnui`, com a conta de utilizador `admin`.


## <a name="next-steps"></a>Passos Seguintes
Nesta segunda parte da série do tutorial de três partes, aprendeu a utilizar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Trabalhar com o Azure Machine Learning Workbench
> * Abrir scripts e rever código
> * Executar scripts num ambiente local
> * Rever o histórico de execuções
> * Executar scripts num ambiente Docker local
> * Executar scripts numa janela local da CLI do Azure
> * Executar scripts num ambiente Docker remoto
> * Executar scripts num ambiente HDInsight na cloud

Está pronto para avançar para a terceira parte da série. Agora que já criou o modelo de Regressão Logística, vamos implementá-lo como um serviço Web em tempo real.

> [!div class="nextstepaction"]
> [Implementar um modelo](tutorial-classifying-iris-part-3.md)
