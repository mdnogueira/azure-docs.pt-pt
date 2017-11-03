---
title: "Introdução à máquina de Virtual de ciência de dados do Azure para Linux e Windows | Microsoft Docs"
description: "Cenários de análise de chave e componentes de máquinas de virtuais de ciência de dados de Linux e Windows."
keywords: "ferramentas de ciência de dados, a máquina de virtual de ciência de dados, as ferramentas de ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: a8b9efffd6373ee33026e915b0a14e15d41295b3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Introdução à máquina de Virtual de ciência de dados do Azure para Linux e Windows

A Máquina Virtual de ciência de dados (DSVM) é uma imagem personalizada de VM na nuvem do Azure da Microsoft criada especificamente para fazer a ciência de dados. Tem muitas ferramentas populares de ciência de dados e outras pré-instaladas e pré-configuradas para permitir a rápida criação de aplicações inteligentes para análises avançadas. Está disponível no Windows Server e no Linux. Oferecemos a edição Windows da DSVM no Server 2016 e no Server 2012. Oferecemos a edição Linux da DSVM no Ubuntu 16.04 LTS e em distribuições do Linux baseadas no OpenLogic 7.2 CentOS. 

Este tópico descreve o que pode fazer com que a VM de ciência de dados, descreve alguns dos principais cenários para utilizar a VM, itemizes as principais funcionalidades disponíveis nas versões do Windows e Linux e fornece instruções sobre como começar a utilizá-los.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>O que posso fazer com a máquina de Virtual de ciência de dados?
O objetivo da máquina de Virtual de ciência de dados consiste em fornecer profissionais de dados em todas as funções e níveis de skill com um ambiente de ciência de dados sem friction. Esta VM guarda um tempo considerável que seria gastam se que tinha implementado num ambiente comparável por si. Em vez disso, inicie imediatamente o projeto de ciência de dados numa instância de VM recentemente criada. 

A VM de ciência de dados está concebida e configurada para trabalhar com uma vasta gama de cenários de utilização. Pode dimensionar o seu ambiente ou reduzir verticalmente como o projeto tem de alteração. É possível utilizar a linguagem preferencial a tarefas de ciência de dados do programa. Pode instalar outras ferramentas e personalizar o sistema para as suas exatas necessidades.

## <a name="key-scenarios"></a>Cenários principais
Esta secção sugere alguns cenários-chave para o qual a VM de ciência de dados pode ser implementada.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Ambiente de trabalho de análise pré-configurada na nuvem
A VM de ciência de dados fornece uma configuração de linha de base para as equipas de ciência de dados à procura de substituir os seus ambientes de trabalho locais com um ambiente de trabalho de nuvem gerido. Esta linha de base garante que todos os cientistas de dados num agrupamento têm uma configuração consistente com a qual pretende verificar as experimentações e promover colaboração. Também reduz os custos ao reduzir a carga de administrador do sistema e a guardar o tempo necessário para avaliar, instalar e manter os vários pacotes de software necessários para efetuar análises avançadas.  

### <a name="data-science-training-and-education"></a>Formação e educação em ciência de dados
Enterprise trainers e educators que ensinam ciência de dados classes, normalmente, fornecem uma imagem de máquina virtual para garantir que os respetivos estudantes têm uma configuração consistente e que os exemplos funcionam de forma previsível. A VM de ciência de dados cria um ambiente a pedido com uma configuração consistente que facilita os desafios de suporte e de incompatibilidade. Casos onde estes ambientes têm de ser criada com frequência, especialmente para classes de formação mais curtas, beneficiam substancialmente.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>A pedido flexível para projetos em grande escala
Ciência de dados hackathons/competitions ou modelação de dados em grande escala e exploração requerem ampliar a capacidade de hardware, normalmente de curta duração. A VM de ciência de dados pode ajudá-lo a replicar o ambiente de ciência de dados rapidamente a pedido, nos servidores expandidos terminar que permitem que as experimentações que requerem high-powered recursos informáticos para ser executado.

### <a name="short-term-experimentation-and-evaluation"></a>Experiência de curto prazo e avaliação
A VM de ciência de dados pode ser utilizado para avaliar ou obter ferramentas como o Microsoft ML Server, SQL Server, ferramentas do Visual Studio, Jupyter, profundo learning / ML toolkits e novas ferramentas populares à Comunidade do mínima com a configuração de esforço. Uma vez que a VM de ciência de dados pode ser configurada rapidamente, pode ser aplicada noutros cenários de utilização curta duração, tais como replicar experimentações publicadas, executar demonstrações, guias seguintes em sessões online ou tutoriais de conferência.

### <a name="deep-learning"></a>Learning profunda
A VM de ciência de dados pode ser utilizada para o modelo de formação utilizando algoritmos de aprendizagem automática avançada no GPU (unidades de processamento de gráficos) baseada em hardware. Utilizar as capacidades de nuvem do Azure de dimensionamento de VM, DSVM ajuda-o a utilizar hardware GPU baseados na nuvem de acordo com necessidade. Um pode mudar para uma VM com base em GPU quando se prepara modelos grandes ou precisar de cálculos de alta velocidade, mantendo o mesmo disco de SO.  A edição do Windows Server 2016 do DSVM vem pré-instaladas com controladores GPU, estruturas e versão para a GPU de avançada algoritmos de aprendizagem. No Linux, avançada learning no GPU está ativada apenas nos [Máquina Virtual de ciência de dados para a edição de Linux (Ubuntu)](http://aka.ms/dsvm/ubuntu). Pode implementar a edição do Windows/Ubuntu-2016 da VM de ciência de dados não GPU baseado na máquina de virtual do Azure, nesse caso, todos os avançada learning estruturas será de contingência para o modo de CPU. Anteriormente, para o Windows Server 2012 é publicado um [profunda learning toolkit](http://aka.ms/dsvm/deeplearning) , mas agora Recomendamos a utilização do Windows Server 2016 para cargas de trabalho baseados em Windows learning profunda. O Linux baseados em CentOS edição do DSVM contém apenas a CPU baseia-se de algumas avançada learning ferramentas (Microsoft cognitivos Toolkit, TensorFlow, MXNet), mas não vêm pré-instaladas com os controladores de GPU e estruturas. 

## <a name="whats-included-in-the-data-science-vm"></a>O que está incluído na VM de ciência de dados?
As máquinas de ciência de dados tem muitas ciência de dados mais populares e avançada learning ferramentas já instalado e configurado. Também inclui as ferramentas que simplificam o trabalho com vários produtos de dados e análise do Azure. Pode explorar e criar modelos preditivos nos conjuntos de dados em grande escala utilizando o Microsoft ML Server (R, Python) ou SQL Server 2017. Um anfitrião de outras ferramentas da Comunidade open source para e da Microsoft também estão incluído, bem como exemplo de código e blocos de notas. A tabela seguinte itemizes e compara os componentes principais incluídos nas edições Windows e Linux da máquina de Virtual de ciência de dados.


| **Ferramenta**                                                           | **Edição do Windows** | **Edição do Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R abra](https://mran.microsoft.com/open/) com pacotes populares previamente instalados   |S                      | S             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) inclui a edição de programador, <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) framework de elevado desempenho paralela e distribuído (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) -novos algoritmos de ML-de-última geração da Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R e Python Operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |S                      | S |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) profissional para Plus com a ativação partilhada - Word, Excel e o PowerPoint   |S                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, do 3.5 com pacotes populares previamente instalados    |S                      |S              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) com populares pacotes para o idioma de Leonor previamente instalado                         |S                      |S              |
| Bases de Dados Relacionais                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Edição de programador| [PostgreSQL](https://www.postgresql.org/)(apenas CentOS) |
| Ferramentas de base de dados                                                       | * O SQL Server Management Studio <br/>* O SQL Server Integration Services<br/>* [BCP, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC controladores| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (consultar a ferramenta), <br /> * bcp, sqlcmd <br /> * ODBC/JDBC controladores|
| Análise de na base de dados dimensionável com os serviços do SQL Server ML (R, Python) | S     |N              |
| **[Servidor de bloco de notas do Jupyter](http://jupyter.org/) com kernels seguintes,**                                  | S     | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 & 3.5 | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Leonor | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (apenas Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | S |
| JupyterHub (servidor multiutilizador blocos de notas)| N | S |
| **Ferramentas de desenvolvimento, editores IDEs e código**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (edição Community)](https://www.visualstudio.com/community/) > com o plug-in do Git, o Azure HDInsight (Hadoop), o Data Lake, ferramentas de dados do SQL Server, [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs), e [R ferramentas para Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ambiente de trabalho do RStudio](https://www.rstudio.com/products/rstudio/#Desktop) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Servidor do RStudio](https://www.rstudio.com/products/rstudio/#Server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno Leonor IDE)](http://junolab.org/)| S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim e Emacs | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* O Git e GitBash | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* O .net framework | S | N |
| Power BI Desktop | S | N |
| SDKs para aceder ao Azure e dos serviços do Cortana Intelligence Suite | S | S |
| **Movimento de dados e ferramentas de gestão** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Explorador de armazenamento do azure | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CLI do Azure](https://docs.microsoft.com/cli/azure/overview) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* O azure Powershell | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (armazenamento do Azure Data Lake)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ferramenta de migração de dados de DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): mover dados entre OnPrem e na nuvem | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Utilitários da linha de comandos Unix/Linux | S | S |
| [Apache desagregação](http://drill.apache.org) de exploração de dados | S | S |
| **Ferramentas do Machine Learning** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integração com [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (apenas Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | Y (apenas Ubuntu) |
| **Com base em GPU avançada, ferramentas de aprendizagem** |Edição do Windows Server 2016  |Ubuntu edição |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Toolkit cognitivos Microsoft (anteriormente conhecida como CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | S | S|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia dígitos](https://github.com/NVIDIA/DIGITS) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, CUDNN, Nvidia controlador](https://developer.nvidia.com/cuda-toolkit) | S | S |
| **Plataforma de macrodados (Devtest apenas)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Local [Spark](http://spark.apache.org/) autónomo | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Local [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | N | S |



## <a name="get-started-with-the-windows-data-science-vm"></a>Começar com a VM de ciência de dados do Windows
* Criar uma instância da edição Windows DSVM pretendida, acedendo a
  * [Windows Server 2016 com base em DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  ou 
  * [Windows Server 2012 com base em DSVM](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* Clique em de **obter TI agora** botão.
* Inicie sessão VM do ambiente de trabalho remoto utilizando as credenciais que especificou quando criou a VM.
* Para detetar e iniciar as ferramentas disponíveis, clique o **iniciar** menu.

## <a name="get-started-with-the-linux-data-science-vm"></a>Começar com a VM de ciência de dados do Linux
* Criar uma instância da edição de Linux DSVM pretendida, acedendo a 
  * [Ubuntu com base em DSVM](http://aka.ms/dsvm/ubuntu)

  ou

  * [OpenLogic CentOS com base em DSVM](http://aka.ms/dsvm/centos)

  
* Clique em de **obtê-lo agora** botão.
* Inicie sessão a VM a partir de um cliente SSH, tais como o Putty ou comando SSH, utilizando as credenciais que especificou quando criou a VM.
* Na linha de comandos da shell, introduza informações de dsvm muito mais.
* Para um ambiente de trabalho gráfico, transfira o cliente de X2Go para a sua plataforma de cliente [aqui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) e siga as instruções no documento de VM de ciência de dados de Linux [aprovisionar a Máquina Virtual de ciência de dados de Linux](linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Passos seguintes
### <a name="for-the-windows-data-science-vm"></a>Para a ciência de dados do Windows VM
* Para obter mais informações sobre como executar ferramentas específicas disponíveis sobre a versão do Windows, consulte [aprovisionar a máquina de Virtual de ciência de dados do Microsoft](provision-vm.md) e
* Para obter mais informações sobre como efetuar várias tarefas de necessárias para o seu projeto de ciência de dados na VM do Windows, consulte [dez coisas que pode fazer na ciência de dados de Máquina Virtual](vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Para a VM de ciência de dados do Linux
* Para obter mais informações sobre como executar ferramentas específicas disponíveis sobre a versão do Linux, consulte [aprovisionar a Máquina Virtual de ciência de dados de Linux](linux-dsvm-intro.md).
* Para instruções que mostram como efetuar várias tarefas de ciência de dados comuns com a VM com Linux, consulte [ciência de dados na máquina Virtual com Linux dados ciência](linux-dsvm-walkthrough.md).

