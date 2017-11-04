---
title: "Previsão da carga de trabalho de servidor no terabytes de dados - Azure | Microsoft Docs"
description: Como preparar um modelo de machine learning macrodados utilizando o Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: b962ad3da6d5daff2c8b2524828a9450da702abb
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Previsão da carga de trabalho dos servidores em terabytes de dados

Este artigo abrange como cientistas de dados podem utilizar o Azure Machine Learning Workbench para desenvolver soluções que requerem a utilização de macrodados. Pode iniciar a partir de uma amostra de um grande conjunto de dados, itere através de preparação de dados, de engenharia da funcionalidade e de aprendizagem e, em seguida, expanda o processo para o conjunto completo de dados grande. 

Irá aprender as seguintes capacidades de chaves do Workbench do Machine Learning:
* Fácil de alternar entre computação destinos. Pode configurar destinos de computação diferentes e utilizá-los na experimentação. Neste exemplo, utilize um DSVM Ubuntu e um cluster do Azure HDInsight como destinos de computação. Também pode configurar os destinos de computação, consoante a disponibilidade dos recursos. Em particular, depois de aumentar horizontalmente o cluster do Spark com mais nós de trabalho, pode utilizar os recursos através do Workbench do Machine Learning para acelerar a execução da experimentação.
* Execute o controlo de histórico. Pode utilizar o Machine Learning Workbench para monitorizar o desempenho do machine learning modelos e outras métricas de interesse.
* Operationalization de modelo do machine learning. Pode utilizar as ferramentas incorporadas Workbench Machine Learning para implementar o treinado do machine learning modelo como um serviço web no serviço de contentor do Azure. Também pode utilizar o serviço web para obter predições de mini-batch através de chamadas da REST API. 
* Suporte para terabytes de dados.

> [!NOTE]
> Para exemplos de código e outros materiais relacionados com este exemplo, consulte [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Descrição geral de cenário de utilização

A carga de trabalho nos servidores de previsão é uma necessidade de negócio comuns para as empresas de tecnologia que gerem as seus próprios infraestrutura. Para reduzir o custo da infraestrutura, os serviços em execução nos servidores em utilizado devem ser agrupados em conjunto com um número mais pequeno de máquinas. Serviços em execução nos servidores overused devem receber mais máquinas para ser executada. 

Neste cenário, que se concentre em previsão da carga de trabalho para cada máquina (ou servidor). Em particular, pode utilizar os dados de sessão em cada servidor para prever a classe de carga de trabalho do servidor no futuro. Classificar a carga de cada servidor em baixa, média e altas classes utilizando o classificador de floresta aleatório no [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). O machine learning técnicas e fluxo de trabalho neste exemplo pode ser facilmente expandido para outros problemas semelhantes. 


## <a name="prerequisites"></a>Pré-requisitos

As pré-requisitos para executar este exemplo são os seguintes:

* Um [conta do Azure](https://azure.microsoft.com/free/) (gratuitas estão disponíveis).
* Uma cópia instalada do [Machine Learning Workbench](./overview-what-is-azure-ml.md). Para instalar o programa e criar uma área de trabalho, consulte o [guia de instalação de início rápido](./quickstart-installation.md).
* Windows 10 (as instruções neste exemplo são, geralmente, os mesmos para sistemas de macOS).
* Dados ciência Máquina Virtual (DSVM) para Linux (Ubuntu). Pode aprovisionar um DSVM Ubuntu seguindo [estas instruções](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Também pode ver [este guia de introdução](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Recomendamos que utilize uma máquina virtual pelo menos 8 núcleos e 32 GB de memória. Terá do endereço IP de DSVM, nome de utilizador e palavra-passe para experimentar neste exemplo. Guarde a tabela seguinte com as informações DSVM para os passos seguintes:

 Nome do campo| Valor |  
 |------------|------|
Endereço IP de DSVM | xxx|
 Nome de utilizador  | xxx|
 Palavra-passe   | xxx|

 Pode optar por utilizar qualquer VM com [motor de Docker](https://docs.docker.com/engine/) instalado.

* Um Cluster do Spark HDInsight com a versão de plataforma de dados do Hortonworks 3.6 e a versão do Spark 2.1.x. Visite [criar um cluster do Apache Spark no Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) para obter detalhes sobre como criar clusters do HDInsight. Recomendamos que utilize um cluster de trabalho de três, com cada trabalho ter 16 núcleos e 112 GB de memória. Ou apenas pode escolher tipo de VM `D12 V2` para o nó principal, e `D14 V2` para o nó de trabalho. A implementação do cluster demora cerca de 20 minutos. Tem o nome do cluster, o nome de utilizador SSH e a palavra-passe para experimentar neste exemplo. Guarde a tabela seguinte com as informações de cluster do Azure HDInsight para os passos seguintes:

 Nome do campo| Valor |  
 |------------|------|
 Nome do cluster| xxx|
 Nome de utilizador  | xxx (sshuser por predefinição)|
 Palavra-passe   | xxx|


* Uma conta de armazenamento do Azure. Pode seguir [estas instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar um. Além disso, criar contentores de BLOBs privada dois com os nomes de `fullmodel` e `onemonthmodel` nesta conta de armazenamento. A conta de armazenamento é utilizada para guardar resultados de computação intermédio e modelos de machine learning. Tem a chave de acesso e o nome conta de armazenamento para experimentar neste exemplo. Guarde a tabela seguinte com as informações de conta de armazenamento do Azure para os passos seguintes:

 Nome do campo| Valor |  
 |------------|------|
 Nome da conta de armazenamento| xxx|
 Chave de acesso  | xxx|


O DSVM Ubuntu e o cluster do Azure HDInsight criada na lista de pré-requisitos são destinos de computação. Computação destinos são os recursos de computação no contexto do Workbench do Machine Learning, que poderá ser diferente do computador onde é executado o Workbench.   

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto do Workbench

Crie um novo projeto ao utilizar este exemplo como um modelo:
1.  Abra do Machine Learning Workbench.
2.  No **projetos** página, selecione o  **+**  iniciar sessão e selecionar **novo projeto**.
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto.
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva **previsão de carga de trabalho nos dados de Terabytes**e selecione o modelo.
5.  Selecione **Criar**.

Pode criar um projeto do Workbench com um repositório de git previamente criada ao seguir [esta instrução](./tutorial-classifying-iris-part-1.md).  
Executar `git status` inspecionar o estado dos ficheiros de registo de versão.

## <a name="data-description"></a>Descrição de dados

Os dados utilizados neste exemplo são dados de carga de trabalho do servidor sintetizado. Estar alojado numa conta de armazenamento de Blobs do Azure que seja acessível publicamente. Podem encontrar as informações de conta de armazenamento específicas de `dataFile` campo [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json). Pode utilizar os dados diretamente a partir do armazenamento de Blobs. Se o armazenamento é utilizado por vários utilizadores em simultâneo, pode utilizar [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) para transferir os dados para o seu próprio armazenamento. 

O tamanho total dos dados é de aproximadamente 1 TB. Cada ficheiro é cerca de 1 a 3 GB e está no formato de ficheiro CSV, sem cabeçalho. Cada linha de dados representa a carga de uma transação de um determinado servidor. As informações detalhadas do esquema de dados são o seguinte:

Número de colunas | Nome do campo| Tipo | Descrição |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    Hora de início de sessão
2  |`SessionEnd`    | DateTime | Hora de fim de sessão
3 |`ConcurrentConnectionCounts` | Número inteiro | Número de ligações simultâneas
4 | `MbytesTransferred` | duplo | Normalizado dados transferidos em megabytes
5 | `ServiceGrade` | Número inteiro |  Nível de serviço para a sessão
6 | `HTTP1` | Número inteiro|  Sessão utiliza HTTP1 ou HTTP2
7 |`ServerType` | Número inteiro   |Tipo de servidor
8 |`SubService_1_Load` | duplo |   Carga subservice 1
9 | `SubService_1_Load` | duplo |  Carga subservice 2
10 | `SubService_1_Load` | duplo |     Carga subservice 3
11 |`SubService_1_Load` | duplo |  Carga subservice 4
12 | `SubService_1_Load`| duplo |      Carga subservice 5
13 |`SecureBytes_Load`  | duplo | Carga de bytes segura
14 |`TotalLoad` | duplo | Carga total no servidor
15 |`ClientIP` | Cadeia|    Endereço IP do cliente
16 |`ServerIP` | Cadeia|    Endereço IP do servidor



Tenha em atenção que os tipos de dados esperado estão listados na tabela anterior. Devido a valores em falta e problemas de dados incorreto, não há nenhuma garantia de que os tipos de dados, na verdade, estão como esperado. O processamento de dados deve ter em consideração esta. 


## <a name="scenario-structure"></a>Estrutura do cenário

Os ficheiros neste exemplo estão organizados de forma.

| Nome de ficheiro | Tipo | Descrição |
|-----------|------|-------------|
| `Code` | Pasta | A pasta contém todos os o código no exemplo. |
| `Config` | Pasta | A pasta contém os ficheiros de configuração. |
| `Image` | Pasta | A pasta utilizada para guardar as imagens para o ficheiro Leia-me. |
| `Model` | Pasta | A pasta utilizada para guardar os ficheiros de modelo transferidos do armazenamento de Blobs. |
| `Code/etl.py` | Ficheiro do Python | O ficheiro de Python utilizado para a preparação de dados e de engenharia da funcionalidade. |
| `Code/train.py` | Ficheiro do Python | O ficheiro de Python utilizado para preparar um modelo de classe de três várias-classfication.  |
| `Code/webservice.py` | Ficheiro do Python | O ficheiro de Python utilizado para operationalization.  |
| `Code/scoring_webservice.py` | Ficheiro do Python |  O ficheiro de Python utilizado para a transformação de dados e chamar o serviço web. |
| `Code/O16Npreprocessing.py` | Ficheiro do Python | O ficheiro de Python utilizado para processar previamente os dados para scoring_webservice.py.  |
| `Code/util.py` | Ficheiro do Python | O ficheiro de Python que contém o código para leitura e escrita de blobs do Azure.  
| `Config/storageconfig.json` | Ficheiro JSON | O ficheiro de configuração para o contentor de Blobs do Azure que armazena a resultados intermédios e o modelo para processamento e formação em dados de um mês. |
| `Config/fulldata_storageconfig.json` | Ficheiro JSON | O ficheiro de configuração para o contentor de Blobs do Azure que armazena a resultados intermédios e o modelo para processamento e formação num conjunto de dados completo.|
| `Config/webservice.json` | Ficheiro JSON | O ficheiro de configuração para scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Ficheiro YAML | O ficheiro de dependência Conda. |
| `Config/conda_dependencies_webservice.yml` | Ficheiro YAML | O ficheiro de dependência de Conda para o serviço web.|
| `Config/dsvm_spark_dependencies.yml` | Ficheiro YAML | O ficheiro de dependência do Spark para Ubuntu DSVM. |
| `Config/hdi_spark_dependencies.yml` | Ficheiro YAML | O ficheiro de dependência do Spark para o cluster do HDInsight Spark. |
| `README.md` | Ficheiro de markdown | O ficheiro Leia-me do markdown. |
| `Code/download_model.py` | Ficheiro do Python | O ficheiro de Python utilizado para transferir os ficheiros de modelo do Azure blob para um disco local. |
| `Docs/DownloadModelsFromBlob.md` | Ficheiro de markdown | O ficheiro de markdown que contém instruções sobre como executar `Code/download_model.py`. |



### <a name="data-flow"></a>Fluxo de dados

O código no [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carrega dados a partir do contentor de acessível publicamente (`dataFile` campo [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Inclui a preparação de dados e de engenharia da funcionalidade e guarda os resultados de computação intermédio e modelos para o seus próprios contentor privado. O código no [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) carrega os resultados de computação intermédio a partir do contentor de privada, trains o modelo de classificação de classe multi e escreve o modelo treinado do machine learning para o contentor privado. 

Deve utilizar um contentor para experimentação no conjunto de dados de um mês e outra para experimentação no conjunto de dados completa. Porque os dados e os modelos são guardados como ficheiros Parquet, cada ficheiro é realmente uma pasta no contentor, que contém vários blobs. O contentor resultante procura da seguinte forma:

| Nome de prefixo do blob | Tipo | Descrição |
|-----------|------|-------------|
| featureScaleModel | Parquet | Modelo de scaler padrão para funcionalidades numérico. |
| stringIndexModel | Parquet | Cadeia de modelo de indexador para funcionalidades não sejam numéricos.|
| oneHotEncoderModel|Parquet | Modelo de acesso frequente um codificador para funcionalidades categórico. |
| mlModel | Parquet | Modelo de formação de machine learning. |
| informações| Ficheiro de pickle do Python | Informações sobre os dados transformados, incluindo o início de formação, fim de formação, duração, timestamp para formação-teste de divisão e colunas para acesso frequente uma codificação e indexação.

Todos os ficheiros e os blobs na tabela anterior são utilizados para operationalization.


### <a name="model-development"></a>Desenvolvimento de modelo

#### <a name="architecture-diagram"></a>Diagrama da arquitetura


O diagrama seguinte mostra o fluxo de trabalho ponto-a-ponto da utilização do Workbench do Machine Learning para desenvolver o modelo: ![arquitetura](media/scenario-big-data/architecture.PNG)

Nas secções seguintes, vamos mostrar o desenvolvimento de modelo ao utilizar a funcionalidade de destino remoto de computação no Machine Learning Workbench. Vamos primeiro carregar uma pequena quantidade de dados de exemplo e execute o script [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) num DSVM Ubuntu para iteração rápida. Iremos pode limitar ainda mais o trabalho no [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) através da transmissão de um argumento adicional para iteração mais rápida. No final, podemos utilizar um cluster do HDInsight para preparar com dados completa.     

O [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) ficheiro carrega e prepara os dados e executa engenharia da funcionalidade. Aceita dois argumentos:
* Um ficheiro de configuração para o contentor de armazenamento de BLOBs, para armazenar os resultados de computação intermédio e modelos.
* Um argumento de configuração de depuração para iteração mais rápida.

O primeiro argumento `configFilename`, é um ficheiro de configuração local onde armazenar as informações do armazenamento de Blob e especifique onde pretende carregar os dados. Por predefinição, é [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), e vai ser utilizado nos dados de um mês executar. Podemos também incluir [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), que tem de utilizar no conjunto de dados completo executar. O conteúdo na configuração é o seguinte: 

| Campo | Tipo | Descrição |
|-----------|------|-------------|
| StorageAccount | Cadeia | Nome de conta do Storage do Azure |
| storageContainer | Cadeia | Contentor na conta de armazenamento do Azure para armazenar os resultados intermédios |
| storageKey | Cadeia |Chave de acesso da conta de armazenamento do Azure |
| DataFile|Cadeia | Ficheiros de origem de dados  |
| Duração| Cadeia | Duração de dados nos ficheiros de origem de dados|

Modificar ambos `Config/storageconfig.json` e `Config/fulldata_storageconfig.json` para configurar a conta de armazenamento, a chave de armazenamento e o contentor do blob para armazenar os resultados intermédios. Por predefinição, é o contentor de BLOBs para os dados de um mês execute `onemonthmodel`, e é o contentor do blob para o conjunto de dados completo executar `fullmodel`. Certifique-se a que criar estes dois contentores na sua conta de armazenamento. O `dataFile` campo no [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) configura os dados que serão carregados no [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). O `duration` campo configura o intervalo incluem os dados. Se a duração está definida como ONE_MONTH, os dados carregados devem ser apenas um ficheiro. csv entre os ficheiros de sete dos dados para Junho de 2016. Se a duração está completo, o conjunto completo de dados (1 TB) foi carregado. Não precisa de alterar `dataFile` e `duration` destes ficheiros de configuração de dois.

O segundo argumento é depuração. Defini-la como FILTER_IP permite uma rápido iteração. Utilize este parâmetro é útil quando pretende depurar o script.

> [!NOTE]
> Em todos os comandos seguintes, substitua qualquer argumento variável com o respetivo valor real.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Desenvolvimento de modelo no Docker do Ubuntu DSVM

#####  <a name="1-set-up-the-compute-target"></a>1. Configurar o destino de computação

Inicie a linha de comandos do Machine Learning Workbench selecionando **ficheiro** > **abra a linha de comandos**. Em seguida, execute: 

```az ml computetarget attach --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password --type remotedocker```

Os seguintes dois ficheiros são criados na pasta aml_config do seu projeto:

-  dockerdsvm.Compute: este ficheiro contém as informações de ligação e a configuração para um destino de execução remota.
-  dockerdsvm.runconfig: este ficheiro é um conjunto de opções de execução utilizada na aplicação Workbench.

Navegue até à dockerdsvm.runconfig e alterar a configuração destes campos para o seguinte:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Prepare o ambiente de projeto, executando a aplicação:

```az ml experiment prepare -c dockerdsvm```


Com `PrepareEnvironment` definido como true, o Machine Learning Workbench cria o ambiente de tempo de execução, sempre que submete uma tarefa. `Config/conda_dependencies.yml`e `Config/dsvm_spark_dependencies.yml` contêm a personalização do ambiente de tempo de execução. Pode modificar sempre o Conda dependências, configuração de Spark e dependências de Spark editando estes dois ficheiros YMAL. Neste exemplo, adicionámos `azure-storage` e `azure-ml-api-sdk` como pacotes de Python adicionais na `Config/conda_dependencies.yml`. Adicionámos também `spark.default.parallelism`, `spark.executor.instances`, e `spark.executor.cores` no `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Preparação de dados e de engenharia da funcionalidade no DSVM Docker

Execute o script `etl.py` no DSVM Docker. Utilize um parâmetro de depuração, que filtra os dados carregados com endereços IP do servidor específico:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Navegue para o painel do lado e selecione **executar** para ver o histórico de execução de `etl.py`. Tenha em atenção que o tempo de execução é cerca de dois minutos. Se pretender alterar o código para incluir novas funcionalidades, fornecer FILTER_IP como segundo argumento fornece uma iteração mais rápida. Poderá ter de executar este passo de várias vezes ao lidar com a sua própria do machine learning problemas, para explorar o conjunto de dados ou criar novas funcionalidades. 

Com a restrição personalizada nos quais os dados para o carregamento e mais filtragens dos quais os dados a processar, que pode acelerar o processo de iteração no seu desenvolvimento de modelo. Como a experimentação, periodicamente deve guardar as alterações no seu código para o repositório de Git. Tenha em atenção que utilizámos o seguinte código `etl.py` para ativar o acesso ao contentor privado:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Em seguida, execute o script `etl.py` no Docker DSVM sem o parâmetro de depuração FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Navegue para o painel do lado e selecione **executar** para ver o histórico de execução de `etl.py`. Tenha em atenção que o tempo de execução é cerca de quatro minutos. O resultado processado deste passo é guardado para o contentor e é carregado para formação em train.py. Além disso, os indexadores de cadeia, pipelines de codificador e scalers padrão são guardados no contentor privada. Estes são utilizados no operationalization. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Modelo de formação em DSVM Docker

Execute o script `train.py` no DSVM Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Este passo carrega os resultados de computação intermédios da execução da `etl.py`e trains um modelo de machine learning. Este passo demora cerca de dois minutos.

Quando tiver concluído com êxito a experimentação nos dados pequenos, pode continuar a executar a experimentação no conjunto de dados completo. Pode começar, utilizando o mesmo código e, em seguida, experimentar argumento e computação alterações de destino.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Desenvolvimento de modelo no cluster do HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Criar o destino de computação no Machine Learning Workbench para o cluster do HDInsight

```az ml computetarget attach --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password --type cluster```

Os seguintes dois ficheiros são criados na pasta aml_config:
    
-  myhdo.Compute: este ficheiro contém informações de ligação e a configuração para um destino de execução remota.
-  myhdi.runconfig: este ficheiro está definido das opções de execução utilizadas na aplicação Workbench.


Navegue até à myhdi.runconfig e alterar a configuração destes campos para o seguinte:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Prepare o ambiente de projeto, executando a aplicação:

```az ml experiment prepare -c myhdi```

Este passo pode demorar até sete minutos.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Preparação de dados e de engenharia da funcionalidade num cluster do HDInsight

Execute o script `etl.py`, com dados completa no cluster do HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Porque esta tarefa dura relativamente muito tempo (cerca de duas horas), pode utilizar `-a` para desativar a transmissão em fluxo de saída. Quando a tarefa é efetuada, além **histórico de execuções**, pode ver os registos de controlador e o controlador. Se tiver um cluster maior, pode reconfigurar as configurações na sempre `Config/hdi_spark_dependencies.yml` para utilizar mais instâncias ou núcleos. Por exemplo, se tiver um cluster de nó de trabalho de quatro, pode aumentar o valor de `spark.executor.instances` entre 5 a 7. Pode ver o resultado deste passo no **fullmodel** contentor na sua conta de armazenamento. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Modelo de formação num cluster do HDInsight

Execute o script `train.py` num cluster do HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Porque esta tarefa dura relativamente muito tempo (cerca de 30 minutos), pode utilizar `-a` para desativar a transmissão em fluxo de saída.

#### <a name="run-history-exploration"></a>Execute a exploração de histórico

Histórico de execução é uma funcionalidade que permite o controlo da sua experimentação no Machine Learning Workbench. Por predefinição, controla a durante a experimentação. No nosso exemplo específico, quando é mover para o conjunto completo de dados para `Code/etl.py` na experimentação, repararmos que duração aumenta significativamente. Também pode iniciar sessão métricas específicas para efeitos de controlo. Para ativar o controlo de métrico, adicione as seguintes linhas de código para o cabeçalho do ficheiro Python:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Eis um exemplo para controlar a métrica específica:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Na barra lateral à direita do Workbench, navegue até à **executa** para ver o histórico de execução para cada ficheiro de Python. Também pode aceder ao seu repositório do GitHub. Um novo ramo, com o nome começado por "AMLHistory", é criado para controlar a alteração que efetuou ao seu script em cada execução. 


### <a name="operationalize-the-model"></a>Operacionalizar o modelo

Nesta secção, operacionalizar o modelo que criou nos passos anteriores, como um serviço web. Também irá aprender a utilizar o serviço web para prever a carga de trabalho. Utilize interfaces de linha de comandos do idioma máquina operationalization (CLIs) para compactar o código e dependências como imagens de Docker e para publicar o modelo como um serviço web de. Para obter mais informações, consulte [esta descrição geral](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md).

Pode utilizar a linha de comandos da linha de comandos no Machine Learning Workbench para executar os CLIs.  Também pode executar os CLIs no Ubuntu Linux seguindo o [guia de instalação](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> Em todos os seguintes comandos, substitua qualquer argumento variável com o respetivo valor real. Demora cerca de 40 minutos para concluir esta secção.
> 

Escolha uma cadeia exclusiva como o ambiente para operationalization. Aqui, utilizamos a cadeia "[exclusiva]" para representar a cadeia que escolher.

1. Criar o ambiente para operationalization e criar o grupo de recursos.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Tenha em atenção que pode utilizar o serviço de contentor como o ambiente utilizando `--cluster` no `az ml env setup` comando. Pode operacionalizar o modelo de machine learning no [serviço de contentor Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Utiliza [Kubernetes](https://kubernetes.io/) para automatizar a implementação, dimensionamento e gestão de aplicações. Este comando demora cerca de 20 minutos para ser executada. Utilize o seguinte para verificar se a implementação foi concluída com êxito: 

        az ml env show -g [unique]rg -n [unique]

   Defina o ambiente de implementação que acabou de criar, executando o seguinte:

        az ml env set -g [unique]rg -n [unique]

2. Criar e utilizar uma conta de gestão de modelo. Para criar uma conta de gestão de modelo, execute o seguinte:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Utilize a gestão de modelo para operationalization executando o seguinte:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Uma conta de gestão de modelo é utilizada para gerir os serviços web e modelos. No portal do Azure, pode ver que uma nova conta de gestão do modelo foi criada. Pode ver os modelos, manifestos, imagens de Docker e serviços que são criados utilizando esta conta de gestão de modelo.

3. Transferir e registe os modelos.

   Transferir os modelos no **fullmodel** contentor no seu computador local no diretório do código. Não transferir o ficheiro de dados parquet com o nome "vmlSource.parquet." Não é um ficheiro de modelo; é um resultado de computação intermédio. Também pode reutilizar os ficheiros de modelo incluídos no repositório de Git. Para obter mais informações, consulte [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Vá para o `Model` pasta na CLI e registar os modelos como segue:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   O resultado de cada comando fornece um ID de modelo, que é necessário no próximo passo. Guardá-las num ficheiro de texto para utilização futura.

4. Crie um manifesto para o serviço web.

   Um manifesto inclui o código para o serviço web, modelos de todos os o machine learning e dependências de ambiente de tempo de execução. Vá para o `Code` pasta na CLI e execute o seguinte comando:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   A saída fornece um ID de manifesto para o passo seguinte. 

   Permanecer no `Code` diretório e, pode testar webservice.py, executando o seguinte: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Crie uma imagem de Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   A saída fornece um ID de imagem para o passo seguinte, esta imagem de docker é utilizada no serviço de contentor. 

6. Implemente o serviço web para o cluster do serviço de contentor.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   A saída fornece um ID de serviço. Terá de utilizá-la para obter a chave de autorização e URL de serviço.

7. Chame o serviço web no código do Python para pontuar em lotes de mini.

   Utilize o seguinte comando para obter a chave de autorização:

         az ml service keys realtime -i $ServiceID 

   Utilize o seguinte comando para obter o URL de pontuação de serviço:

        az ml service usage realtime -i $ServiceID

   Modificar o conteúdo `./Config/webservice.json` com o serviço direita URL e a autorização da chave de classificação. Manter "Portador" no ficheiro original e substitua a parte "xxx". 
   
   Vá para o diretório de raiz do projeto e testar o serviço web para mini-batch classificação utilizando o seguinte:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Dimensione o serviço web. 

   Para obter mais informações, consulte [como dimensionar operationalization no cluster do serviço de contentor Azure](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md).
 

## <a name="next-steps"></a>Passos seguintes

Este exemplo destaca como utilizar o Machine Learning Workbench para preparar um modelo de machine learning sobre macrodados e operacionalizar o modelo treinado. Em particular, aprendeu a configurar e utilizar destinos de computação diferentes e execute o histórico de controlar métricas e utilizar executa diferentes.

Pode expandir o código para explorar a otimização de validação cruzada e hyper parâmetro. Para saber mais sobre a otimização de validação cruzada e hyper parâmetro, consulte [este recurso de GitHub](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Para saber mais sobre a previsão de série de tempo, consulte o artigo [este recurso de GitHub](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
