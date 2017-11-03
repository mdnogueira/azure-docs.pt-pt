---
title: "Ficheiros de configuração do serviço de experimentação do Machine Learning do Azure"
description: "Este documento fornece detalhes sobre as definições de configuração do serviço de experimentação do Azure ML."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 6a247c225af734757ab0cb0a7502f39535299ca7
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Ficheiros de configuração do serviço de experimentação do Machine Learning do Azure

Quando executar um script no Azure Machine Learning (Azure ML) Workbench, o comportamento de execução é controlado pelos ficheiros no **aml_config** pasta. Esta pasta está na raiz da pasta do projeto. É importante compreender o conteúdo desses ficheiros para alcançar o resultado desejado para a execução de uma forma ideal.

Seguem-se os ficheiros relevantes nesta pasta:
- conda_dependencies.yml
- spark_dependencies.yml
- ficheiros do destino de computação
    - \<nome do destino de computação > .compute
- Execute os ficheiros de configuração
    - \<Execute o nome de configuração > .runconfig

>[!NOTE]
>Normalmente, tem um ficheiro de destino de computação e executar o ficheiro de configuração para cada destino de computação que cria. No entanto, pode criar estes ficheiros de forma independente e ter vários ficheiros de configuração de execução de apontar para o mesmo destino de computação.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Este ficheiro é um [ficheiros do ambiente de conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) que especifica a versão de runtime do Python e os pacotes que depende do seu código. Quando o Workbench do Azure ML executa um script de um contentor de Docker ou cluster do HDInsight, cria um [conda ambiente](https://conda.io/docs/using/envs.html) para o script executar. 

Neste ficheiro, especifique os pacotes de Python que necessita de script para execução. Serviço de experimentação do Azure ML cria o ambiente de conda na imagem do Docker, de acordo com a lista de dependências. A lista de pacotes aqui tem de ser acessível pelo motor de execução. Por esse motivo, pacotes precisam de ser listado em canais, tais como:

* [continuum.IO](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* um ponto final acessível publicamente (URL)
* ou um caminho de ficheiro local
* outras pessoas acessível pelo motor de execução

>[!NOTE]
>Quando em execução num cluster do HDInsight, do Azure ML Workbench cria um ambiente conda apenas para sua execução. Isto permite que os utilizadores diferentes executar em ambientes do python diferentes no mesmo cluster.  

Eis um exemplo de típica **conda_dependencies.yml** ficheiro.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench utiliza o mesmo ambiente conda sem recompilar desde que o **conda_dependencies.yml** permanece intacta. No entanto, se ocorrem alterações neste ficheiro, que resulta numa reconstrução da imagem do Docker.

>[!NOTE]
>Se destinar execução contra _local_ computação contexto, **conda_dependencies.yml** ficheiro é **não** utilizado. As dependências de pacote para o seu ambiente do Azure ML Workbench Python local tem de ser instalado manualmente.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Este ficheiro Especifica o nome de aplicação do Spark ao submeter um script de PySpark e pacotes de Spark que tem de ser instalado. Também pode especificar qualquer repositório Maven público, bem como pacote Spark que pode ser encontrado nos repositórios do Maven.

Segue-se um exemplo:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Otimização de parâmetros, tais como o tamanho de trabalho de cluster, devem passar núcleos na secção "configuration" no ficheiro spark_dependecies.yml 

>[!NOTE]
>Se estiver a executar o script no ambiente do Python, *spark_dependencies.yml* ficheiro é ignorado. Só tem efeito se estiver a executar contra Spark (o Docker ou Cluster de HDInsight).

## <a name="run-configuration"></a>Execute a configuração
Para especificar uma configuração específica de execução, é necessário um par de ficheiros. São normalmente gerados com um comando da CLI. Mas também pode clonar sair aqueles, renomeie-os e editá-los.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password> --type remotedocker

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> --type cluster
```

Este comando cria um par de ficheiros com base no cálculo de destino especificado. Digamos que com o nome do destino de computação _foo_. Este comando gera _foo.compute_ e _foo.runconfig_ no seu **aml_config** pasta.

>[!NOTE]
> _local_ ou _docker_ nomes para os ficheiros de configuração de execução são arbitrários. Azure ML Workbench adiciona que estes dois executar configurações quando criar um projeto em branco para sua comodidade. Pode mudar o nome "<run configuration name>.runconfig" ficheiros que são fornecidos com o modelo de projeto ou criar novos com um nome que pretende.

### <a name="compute-target-namecompute"></a>\<nome do destino de computação > .compute
_\<nome do destino de computação > .compute_ ficheiro Especifica as informações de ligação e a configuração para o destino de computação. É uma lista de pares nome-valor. Follwing são as definições suportadas.

**tipo**: tipo de ambiente de computação. Os valores suportados são:
  - local
  - Docker
  - remotedocker
  - Cluster

**baseDockerImage**: imagem de Docker o utilizada para executar o script de Python/PySpark. O valor predefinido é _microsoft/mmlspark:plus-0.7.91_. Também é suportada uma imagem de outra: _microsoft/mmlspark:plus-gpu-0.7.91_, que lhe dê acesso GPU para a máquina do anfitrião (se GPU estiver presente).

**endereço**: O endereço IP ou FQDN (nome de domínio completamente qualificado) da máquina virtual ou o HDInsight head-nó de cluster.

**nome de utilizador**: O nome de utilizador SSH para aceder a máquina virtual ou o nó principal do HDInsight.

**palavra-passe**: A palavra-passe encriptada para a ligação SSH.

**sharedVolumes**: sinalizador para assinalar desse motor de execução deve utilizar o Docker partilhados são enviados os ficheiros de projeto anterior e descritos da funcionalidade de volume. Ter este sinalizador ativado pode acelerar a execução, uma vez que Docker pode aceder a projetos diretamente, sem a necessidade de copiá-los. É melhor definir _falso_ se o motor de Docker em execução no Windows desde o volume de partilha para Docker no Windows pode ser flaky. Defina-o como _verdadeiro_ se está a ser executado no macOS ou Linux.

**nvidiaDocker**: este sinalizador, quando definido como _verdadeiro_, indica o serviço de experimentação do Azure ML para utilizar _nvidia docker_ comando, por oposição a regular _docker_ comando para iniciar a imagem de Docker. O _nvidia docker_ motor permite que o contentor de Docker ao hardware da GPU de acesso. A definição é necessária se pretender executar a execução de GPU no contentor de Docker. Suporta apenas o anfitrião Linux _nvidia docker_. Por exemplo, DSVM baseado em Linux no Azure é fornecido com _nvidia docker_. _NVIDIA docker_ a partir de agora, não é suportada no Windows.

**nativeSharedDirectory**: Esta propriedade especifica o diretório de base (por exemplo: _~/.azureml/share/_) onde os ficheiros podem ser guardados para poder ser partilhadas em é executado no mesmo destino de computação. Se esta definição é utilizada quando em execução no contentor de Docker, _sharedVolumes_ tem de ser definido como true. Caso contrário, a execução falhará.

### <a name="run-configuration-namerunconfig"></a>\<Execute o nome de configuração > .runconfig
_\<Execute o nome de configuração > .runconfig_ Especifica o comportamento de execução de experimentação do Azure ML. Pode configurar os comportamentos de execução, como o histórico de execução de controlo ou de destino a utilizar, juntamente com muitas outras que computação. Os nomes dos ficheiros de configuração de execução são utilizados para preencher a lista pendente de contexto de execução da aplicação de ambiente de trabalho do Azure ML Workbench.

**ArgumentVector**: Esta secção especifica o script a ser executado como parte de execução deste e os parâmetros para o script. Por exemplo, se tiver o fragmento seguinte na sua "<run configuration name>.runconfig" ficheiro 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"experimentação de ml az submeter foo.runconfig"_ executa automaticamente o comando com _myscript.py_ ficheiro transmitir numa 234 como um parâmetro e define o – sinalizador verboso.

**Destino**: este parâmetro é o nome do _.compute_ de ficheiros que o _runconfig_ referências de ficheiros. Geralmente, aponte o _foo.compute_ ficheiro, mas pode editá-lo para apontar para um destino de computação diferentes.

**As variáveis de ambiente**: Esta secção permite aos utilizadores a definir variáveis de ambiente como parte do respetivo é executado. Utilizador pode especificar variáveis de ambiente utilizando pares nome-valor no seguinte formato:
```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Estas variáveis de ambiente podem ser acedidos no código do utilizador. Por exemplo, este código phyton imprime a variável de ambiente com o nome "EXAMPLE_ENV_VAR"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: Esta propriedade especifica se o Workbench do Azure ML deve iniciar uma sessão de Spark para executar o script. O valor predefinido é _PySpark_. Defina-o como _Python_ se não estiver a executar o código de PySpark, que pode ajudá-lo a iniciar a tarefa mais rápida com menos overhead.

**CondaDependenciesFile**: Esta propriedade aponta para o ficheiro que especifica as dependências de ambiente de conda no *aml_config* pasta. Se definido como _nulo_, aponte para a predefinição **conda_dependencies.yml** ficheiro.

**SparkDependenciesFile**: Esta propriedade aponta para o ficheiro que especifica as dependências do Spark no **aml_config** pasta. Está definido para _nulo_ por predefinição e aponta para a predefinição **spark_dependencies.yml** ficheiro.

**PrepareEnvironment**: Esta propriedade, quando definida como _verdadeiro_, indica o serviço de experimentação para preparar o ambiente de conda com base nas dependências de conda especificadas como parte da sua execução inicial. Esta propriedade é eficaz apenas quando executar em relação a um ambiente de Docker. Esta definição não tem qualquer efeito se estiver a executar em relação a um _local_ ambiente. 

**TrackedRun**: este sinalizador sinalizar o serviço de experimentação se deve ou não controlar do Azure ML Workbench executar a infraestrutura de histórico de execução. O valor predefinido é _verdadeiro_. 

**UseSampling**: _UseSampling_ Especifica se os conjuntos de dados de exemplo do Active Directory para origens de dados são utilizados para a execução. Se definido como _falso_, origens de dados de inserção e utilizar os dados de totais de leitura do arquivo de dados. Se definido como _verdadeiro_, exemplos de Active Directory são utilizados. Os utilizadores podem utilizar o * * DataSourceSettings "para especificar os conjuntos de dados de exemplo específica a utilizar se pretende substituir o exemplo de Active Directory. 

**DataSourceSettings**: Esta secção de configuração especifica as definições da origem de dados. Nesta secção, o utilizador Especifica qual amostra de dados existente para uma determinada origem de dados é utilizada como parte de execução. 

A seguinte definição de configuração especifica esse exemplo com o nome "MySample" é utilizado para a origem de dados com o nome "MyDataSource"
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: substituições de origem de dados podem ser utilizadas quando o utilizador pretende mudar de uma origem de dados para outro sem alterar o seu código. Por exemplo, os utilizadores, podem mudar de um ficheiro de objeto de amostragem para / para baixo, local para o conjunto de dados original, maior armazenado no Blob do Azure ao alterar a referência da origem de dados. Quando é utilizada uma substituição, Azure ML Workbench executa as origens de dados e os pacotes de preparação de dados ao consultar a origem de dados substitute.

O exemplo seguinte substitui as referências do "mylocal.datasource" origens de dados do Azure ML e pacotes de preparação de dados com "myremote.dsource". 
 
```
DataSourceSubstitutions:
    myocal.dsource: myremote.dsource
```

Com base na substituição acima, o seguinte exemplo de código agora lê a partir de "myremote.dsource" em vez de "mylocal.dsource" sem utilizadores alterar o seu código.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a configuração do serviço de experimentação](experimentation-service-configuration.md).
