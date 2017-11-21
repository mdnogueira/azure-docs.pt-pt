---
title: Como utilizar os Jupyter Notebooks no Azure Machine Learning Workbench | Microsoft Docs
description: "Guia para a utilização da funcionalidade de blocos de notas do Jupyter do Workbench do Azure Machine Learning"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 9d8a9f1c32578abff1d98e093469e1a780f6cd80
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Como utilizar o bloco de notas do Jupyter no Azure Machine Learning Workbench

Azure Workbench de aprendizagem máquina suporta experimentação de ciência de dados interativa através de integração do bloco de notas do Jupyter. Este artigo descreve como tornar a utilização eficaz desta funcionalidade para aumentar a velocidade e a qualidade da sua experimentação de ciência de dados interativa.

## <a name="prerequisites"></a>Pré-requisitos
- [Instalar e criar o Azure Machine Learning](quickstart-installation.md).
- Deve estar familiarizado com [bloco de notas do Jupyter](http://jupyter.org/), uma vez que este artigo não sobre teaching como utilizar os Jupyter.

## <a name="jupyter-notebook-architecture"></a>Arquitetura de bloco de notas do Jupyter
Um nível elevado, arquitetura de bloco de notas do Jupyter inclui três componentes, cada pode ser executados em ambientes de computação diferentes:

- **Cliente**: recebe intervenção do utilizador e apresenta composta de saída
- **Servidor**: servidor web que aloja os ficheiros de bloco de notas (.ipynb ficheiros)
- **Kernel**: o ambiente de tempo de execução onde ocorre a execução real das células de bloco de notas

Para obter mais detalhes, consulte oficial [Jupyter documentação](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Segue-se um diagrama que mostram como mapeiam este cliente, o servidor e a arquitetura de kernel para os componentes do Azure ML.

![arquitetura de bloco de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Kernels no bloco de notas do Workbench do Azure ML
Pode aceder a muitas kernels diferentes no Azure ML Workbench configurando as configurações de execução e computação destinos no `aml_config` pasta no seu projeto. A adição de um destino de cálculo nova ao emitir `az ml computetarget attach` comando é o equivalente a adicionar um novo kernel.

>[!NOTE]
>Reveja o [configurar execução](experimentation-service-configuration.md) para obter mais detalhes sobre executado configurações e destinos de computação.

### <a name="kernel-naming-convention"></a>Convenção de nomenclatura de kernel
Os kernels são normalmente denominados no formato de "\<nome do projeto > \<executar configuração nome >". Por exemplo, se tiver uma configuração de execução com o nome _docker python_ num projeto com o nome _myIris_, pode encontrar um kernel com o nome "myIris docker-python" na lista de kernel quando abre um bloco de notas do Jupyter.

Atualmente, o Workbench suporta os seguintes tipos de kernels.

### <a name="local-python-kernel"></a>Local kernel do Python
Este kernel Python suporta a execução no computador local. Está integrado com o suporte de histórico de execuções do Azure Machine Learning. O nome do kernel está normalmente "my_project_name local".

>[!NOTE]
>Não utilize o kernel "Python 3". É um kernel autónomo fornecida pelo Jupyter por predefinição. Não está integrada com as capacidades do Azure Machine Learning.

### <a name="python-kernel-in-docker-local-or-remote"></a>Kernel Python no Docker (locais ou remotos)
Este kernel Python é executado num contentor Docker no seu computador local ou numa VM com Linux remoto. O nome do kernel está normalmente "my_project docker". Associada `docker.runconfig` ficheiro tem o `Framework` campo definido como `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Kernel do PySpark no Docker (locais ou remotos)
Este kernel do PySpark executa scripts num contexto de Spark em execução no contentor de Docker, no seu computador local ou numa VM com Linux remoto. O nome de kernel é normalmente "my_project docker". Associada `docker.runconfig` ficheiro tem o `Framework` campo definido como `PySpark`.

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>Kernel do PySpark num cluster do HDInsight
Este kernel é executado no cluster de HDInsight remoto que anexados como um destino de computação para o seu projeto. O nome de kernel é normalmente "my_project my_hdi". 

>[!IMPORTANT]
>No `.compute` ficheiro para o HDI computação destino, tem de alterar o `yarnDeployMode` campo para `client` (o valor predefinido é `cluster`) para utilizar este kernel. 

## <a name="start-jupyter-server-from-the-workbench"></a>Iniciar o Jupyter servidor a partir do Workbench
Do Workbench do Azure Machine Learning, blocos de notas podem ser acedidos através do Workbench **blocos de notas** separador. O _classificar Iris_ projeto de exemplo inclui uma `iris.ipynb` bloco de notas do exemplo.

![separador de blocos de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Quando um bloco de notas é aberto no Azure Machine Learning Workbench, é apresentado no seu próprio separador documento **modo de pré-visualização**. Esta é uma vista só de leitura que não requer um servidor de Jupyter em execução e de kernel.

![pré-visualização do bloco de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Ao clicar em **iniciar o bloco de notas do servidor** botão inicia servidor Jupyter e muda o bloco de notas para **modo de edição**. A interface de utilizador de bloco de notas do Jupyter familiar aparece incorporada no Workbench. Agora pode definir um kernel do **Kernel** menu e iniciar a sessão interativa de bloco de notas. 

>[!NOTE]
>Tenha em atenção para kernels não local, pode demorar um minuto ou dois para iniciar o se estiver a utilizar este pela primeira vez. Pode executar `az ml experiment prepare` comando a partir da janela da CLI para preparar o destino de computação, de modo kernel pode iniciar muito mais rápida após o destino de computação está preparado.

![Modo de edição](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Esta é uma experiência de bloco de notas do Jupyter totalmente interativa. Todas as operações normais do bloco de notas e atalhos de teclado são suportados por esta janela com a exceção de algumas operações de ficheiros, uma vez que pode ser feitos através do Workbench **blocos de notas** separador e **ficheiro** separador.

## <a name="start-jupyter-server-from-command-line"></a>Servidor do Jupyter de início da linha de comandos
Também pode iniciar uma sessão de bloco de notas emitindo um `az ml notebook start` na janela da linha de comandos:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
O browser predefinido é iniciada de forma automática com o servidor de Jupyter apontar para o diretório de raiz do projeto. Também pode utilizar o URL e o token apresentado na janela da CLI para iniciar localmente outras janelas do browser. 

![dashboard do projeto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Agora pode clicar num `.ipynb` ficheiro do bloco de notas, abrir, defina o kernel (se ainda não foi definido) e iniciar a sessão interativa.

![dashboard do projeto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Utilizar comandos mágicos para gerir as experimentações

Pode utilizar [mágica comandos](http://ipython.readthedocs.io/en/stable/interactive/magics.html) nas células bloco de notas para controlar o histórico de execução e guardar saídas, tais como modelos ou conjuntos de dados.

Para controlar o bloco de notas individuais célula é executado, utilize "% azureml histórico em" mágica comando. Depois de ativar o histórico, cada execução de célula será apresentado como entrada de histórico de execução.

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Para desativar célula executar controlo, utilize o comando mágica "% azureml histórico desativar".

Pode utilizar o comando mágica "% azureml carregamento" para guardar os ficheiros de modelo e os dados da sua execução. Objetos guardados aparecem como saídas na vista do histórico de execução para fornecido executar.

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>As saídas tem de ser guardadas para uma pasta denominada "saídas"

## <a name="next-steps"></a>Passos Seguintes
- Para saber como utilizar o bloco de notas do Jupyter, visite o [documentação oficial do Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Para obter uma compreensão mais aprofundada do ambiente de execução de experimentação do Azure ML, reveja [serviço de experimentação de descrição geral do Azure Machine Learning](experimentation-service-configuration.md)

