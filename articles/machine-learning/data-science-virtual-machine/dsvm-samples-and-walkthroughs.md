---
title: "Exemplos e instruções para os dados de ciência de Máquina Virtual - Azure | Microsoft Docs"
description: "Exemplos e instruções para a máquina de Virtual de ciência de dados."
keywords: "ferramentas de ciência de dados, a máquina de virtual de ciência de dados, as ferramentas de ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 89bb5d255db9ab266d04169a3101e2b694236029
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Amostras nas máquinas de virtuais de ciência de dados (DSVM)

Os DSVMs vêm incluídos exemplos de escalamento de trabalhado totalmente sob a forma de blocos de notas do Jupyter e alguns que não se baseiam em Jupyter. Pode aceder Jupyter ao clicar no `Jupyter` ícone no menu de ambiente de trabalho ou da aplicação.  
> [!NOTE]
> Consulte [acesso Jupyter](#access-jupyter) secção ativar Jupyter Notebooks no seu DSVM.

## <a name="quick-reference-of-samples"></a>Referência rápida de amostras
| Categoria de amostras | Descrição | Localizações |
| ------------- | ------------- | ------------- |
| **R** idioma  | Amostras no **R** explicar cenários, como estabelecer ligação com os dados em nuvem do Azure armazena comparar abra R de origem e Microsoft R & operacionalizar modelos no Microsoft R Server ou SQL Server. <br/> [Captura de ecrã](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** idioma  | Amostras no **Python** explicar cenários, como estabelecer ligação com os arquivos de dados em nuvem do Azure e a trabalhar com **Azure Machine Learning**.  <br/> [Captura de ecrã](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Leonor** idioma  | Exemplo no **Leonor** que detalhe Plotting no Leonor, profundo no Leonor, chamar C e o Python de Leonor etc. <br/> [Captura de ecrã](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Cognitivos Toolkit de)  | Ligação avançada learning amostras publicadas pela equipa do Toolkit cognitivos na Microsoft.  <br/> [Captura de ecrã](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXnet** blocos de notas  | Profundidade exemplos de utilização de aprendizagem **MXnet** com base em redes neurais. Existem vários blocos de notas entre beginner para cenários avançados.  <br/> [Captura de ecrã](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | Interagir com **Azure Machine Learning** Studio e a criação de pontos finais do serviço web a partir de modelos de formação localmente, para fluxos de trabalho classificação baseados na nuvem. <br/> [Captura de ecrã](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | Profundidade exemplos de utilização de aprendizagem **caffe2** com base em redes neurais. Existem vários blocos de notas concebidos para familiarizar-os utilizadores com caffe2 e como utilizá-lo de forma eficaz, incluindo exemplos como imagem previamente processamento, criação de conjunto de dados, regressão e utilizar previamente preparado modelos. <br/> [Captura de ecrã](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Exemplos de baseado no Python através das **H2O** inúmeros problemas de cenário do mundo real. <br/> [Captura de ecrã](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** idioma  | Exemplo de utilização de funcionalidades e capacidades do Spark **MLlib** toolkit através de **pySpark 2.0** no **Apache Spark 2.0**.  <br/> [Captura de ecrã](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** idioma  | Exemplos de utilização de diversas **MMLSpark - Microsoft Machine Learning para Apache Spark**, que é uma estrutura que fornece um número de ferramentas de ciência de dados para e de aprendizagem profunda **Apache Spark**. <br/> [Captura de ecrã](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Vários exemplos de rede neuronal diferente e técnicas implementadas utilizando o **TensorFlow** framework. <br/> [Captura de ecrã](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Amostras de Machine Learning padrão no **XGBoost** para cenários, como a classificação, regressão etc. <br/> [Captura de ecrã](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acesso Jupyter 

Visite Jupyter Home acedendo a  **`https://localhost:9999`**  no Windows ou  **`https://localhost:8000`**  no Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Ativar o acesso do Jupyter do Browser

**Windows DSVM**

Executar  **`Jupyter SetPassword`**  do atalho do ambiente de trabalho e siga o pedido para o conjunto/repor a palavra-passe para Jupyter e iniciar o Jupyter processar. 
<br/>![Ativar a exceção do Jupyter](./media/jupyter-setpassword.png)<br/>
Pode aceder a home page do Jupyter depois do processo de Jupyter foi iniciada com êxito na sua VM, visitando  **`https://localhost:9999`**  no seu browser. Consulte a captura de ecrã para adicionar a exceção e ativar o acesso do Jupyter através do browser
<br/>![Ativar a exceção do Jupyter](./media/windows-jupyter-exception.png)<br/>
Inicie sessão com a nova palavra-passe que apenas tinha sido definido.
<br/>
**DSVM do Linux**

Pode aceder à home page do Jupyter na sua VM, visitando  **`https://localhost:8000`**  no seu browser. Consulte a captura de ecrã para adicionar a exceção e ativar o acesso do Jupyter através do browser.
<br/>![Ativar a exceção do Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
Inicie sessão com a mesma palavra-passe como o início de sessão para o DSVM.
<br/>

**Home page do Jupyter**
<br/>![Home page do Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Idioma de R 
<br/>![Exemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Exemplos de Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Leonor idioma 
<br/>![Exemplos de Leonor](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![Exemplos CNTK](./media/cntk-samples2.png)<br/>
<br/>![Exemplos CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXnet
<br/>![Exemplos de MXnet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Exemplos de AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>Caffe2 
<br/>![Exemplos de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemplos de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemplos de TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemplos de XGBoost](./media/xgboost-samples.png)<br/>

