---
title: Utilizar a funcionalidade de recolha de dados do modelo no Azure Machine Learning Workbench | Microsoft Docs
description: Este artigo aborda como utilizar a funcionalidade de recolha de dados do modelo no Azure Machine Learning Workbench
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 6a40a85426d2be72fa688548f7ab30e7e5f92146
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="collect-model-data-by-using-data-collection"></a>Recolher dados do modelo através da utilização de recolha de dados

Pode utilizar a funcionalidade de recolha de dados do modelo no Azure Machine Learning Workbench para arquivar entradas de modelo e predições de um serviço web.

## <a name="install-the-data-collection-package"></a>Instalar o pacote de recolha de dados
Pode instalar a biblioteca de recolha de dados nativamente no Linux e Windows.

### <a name="windows"></a>Windows
No Windows, instale o módulo de recoletor de dados utilizando o seguinte comando:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
No Linux, instale primeiro a biblioteca libxml + +. Execute o seguinte comando, tem de ser emitido em sudo:

    sudo apt-get install libxml++2.6-2v5

Em seguida, execute o seguinte comando:

    pip install azureml.datacollector

## <a name="collect-data"></a>Recolher dados

Para utilizar a recolha de dados do modelo, efetue as seguintes alterações ao seu ficheiro de classificação:

1. Adicione o seguinte código na parte superior do ficheiro:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Adicione as seguintes linhas de código para o `init()` função:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Adicione as seguintes linhas de código para o `run(input_df)` função:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Certifique-se de que as variáveis `input_df` e `pred` (valor de predição de `model.predict()`) são inicializados antes de chamar o `collect()` função nos mesmos.

4. Utilize o `az ml service create realtime` comando com o `--collect-model-data true` comutador para criar um serviço web em tempo real. Este passo certifica-se de que os dados de modelo são recolhidos quando o serviço está a ser executado.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Para testar a recolha de dados, execute o `az ml service run realtime` comando:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Ver os dados recolhidos
Para ver os dados recolhidos no armazenamento de BLOBs:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **mais serviços**.
3. Na caixa de pesquisa, escreva **contas do Storage** e selecione a tecla Enter.
4. Do **contas do Storage** painel pesquisa, selecione o **conta de armazenamento** recursos. Para determinar a sua conta do storage, utilize os seguintes passos:

    a. Vá para o Azure Machine Learning Workbench, selecione o projeto a trabalhar e, abra uma linha de comandos a **ficheiro** menu.
    
    b. Introduza `az ml env show -v` e verifique o *storage_account* valor. Este é o nome da conta de armazenamento.

5. Selecione **contentores** no recurso de menu do painel e, em seguida, o contentor denominado **modeldata**. Para ver dados iniciar propagar-se à conta de armazenamento, poderá ter de aguardar até 10 minutos após o primeiro pedido de serviço web. Os dados fluem para os blobs com o caminho de contentor seguinte:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Podem ser consumidos dados de blobs do Azure de diversas formas, através do Microsoft software e ferramentas open source. Eis alguns exemplos:
- Azure Workbench de aprendizagem máquina: Abra o ficheiro. csv no Azure Machine Learning Workbench adicionando o ficheiro. csv como uma origem de dados.
- Excel: Abra os ficheiros. csv diária como uma folha de cálculo.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): criem gráficos com os dados solicitados a partir dos dados. csv em blobs.
- [O Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview): criar um intervalo de dados com uma grande parte dos dados. csv.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Ramo de registo](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): dados de carregamento de. csv para um ramo de registo de tabela e executar consultas SQL diretamente no blob.

