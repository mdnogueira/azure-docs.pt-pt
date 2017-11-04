---
title: "Configurar o serviço de experimentação do Azure Machine Learning | Microsoft Docs"
description: "Este artigo fornece uma descrição de alto nível de serviço de experimentação do Azure Machine Learning com instruções sobre como configurá-lo."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 5635ef890537a2f37d9d6e9066d0258fc0cb346e
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Configurar o serviço de experimentação do Azure Machine Learning

## <a name="overview"></a>Descrição geral
Serviço experimentação do Azure Machine Learning permite cientistas de dados executar as experimentações através da execução do Azure Machine Learning e executar as capacidades de gestão. Fornece uma arquitetura de experimentação seja ágil com iterações rápidas. Workbench de aprendizagem máquina do Azure permite-lhe começar com local é executado no seu computador e fornece um caminho mais fácil para aumentar verticalmente e horizontalmente a outros ambientes, como VMs de ciência de dados remota com uma GPU ou Clusters do HDInsight com o Spark.

Experimentação de serviço foi concebido para fornecer execuções isoladas, reproduzível e consistentes das suas experimentações. Ajuda a gerir os destinos de computação, ambientes de execução, e execute as configurações. Ao utilizar a execução do Workbench do Azure Machine Learning e capacidades de gestão de execução, é possível mover facilmente entre ambientes diferentes. 

Pode executar um script de Python ou PySpark num projeto Workbench localmente ou na escala na nuvem. 

Pode executar os scripts em: 

* Ambiente do Python (3.5.2) no computador local instalado por Workbench.
* Ambiente de Conda Python dentro de um contentor de Docker no computador local
* Ambiente de Conda Python dentro de um contentor de Docker num computador remoto, Linux. Por exemplo, um [DSVM Ubuntu baseado no Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight para o Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) no Azure

>[!IMPORTANT]
>Serviço experimentação do Azure Machine Learning suporta atualmente o Python 3.5.2 e Spark 2.1.11 como versões de runtime do Python e Spark, respetivamente. 


### <a name="key-concepts-in-experimentation-service"></a>Conceitos chave na experimentação de serviço
É importante compreender os seguintes conceitos na execução de experimentação do Azure Machine Learning. Nas secções subsequentes, vamos discutir como utilizar estes conceitos em detalhe. 

#### <a name="compute-target"></a>Destino de computação
A _computação destino_ Especifica onde a executar o programa, tais como o seu ambiente de trabalho, remoto Docker numa VM ou um cluster. Um destino de computação tem de ser endereçável e acessível por si. Workbench dá-lhe a capacidade de criar os destinos de computação e geri-los utilizando a aplicação do Workbench e a CLI. 

_anexar AZ ml computetarget_ comando na CLI permite-lhe criar um destino de computação que pode utilizar na sua execução.

Destinos de computação suportados são:
* Ambiente do Python (3.5.2) local no seu computador instalada pelo Workbench.
* Docker local no seu computador
* Docker remoto em VMs do Linux Ubuntu. Por exemplo, um [DSVM Ubuntu baseado no Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight para o cluster do Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) no Azure

Serviço de experimentação atualmente suporta Python 3.5.2 e Spark 2.1.11 como Python e Spark versões de tempo de execução, respetivamente. 

>[!IMPORTANT]
> Execução de VMs do Windows são Docker **não** suportado como destinos de computação remoto.

#### <a name="execution-environment"></a>Ambiente de execução
O _ambiente de execução_ define a configuração de tempo de execução e as dependências necessárias para executar o programa no Workbench.

Gerir o ambiente de execução local utilizando as suas ferramentas favoritas e gestores de pacote se estiver a executar no tempo de execução da predefinição do Workbench. 

Conda é utilizado para gerir local Docker e remotas execuções de Docker, bem como execuções baseado no HDInsight. Para estes destinos de computação, a configuração do ambiente de execução é gerida através de **Conda_dependencies.yml** e **Spark_dependencies.yml ficheiros**. Estes ficheiros estão no **aml_config** pasta no interior do seu projeto.

**Tempos de execução suportados para ambientes de execução são:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Execute a configuração
Para além de ambiente de execução e de destino de computação, o Azure Machine Learning fornece uma estrutura para definir e alterar *executar configurações*. Execuções diferentes da sua experimentação poderão requerer configuração diferente como parte da experimentação iterativa. Poderá ser varrimento intervalos de parâmetro diferentes, utilizando as origens de dados diferentes e otimização de parâmetros de spark. O serviço de experimentação fornece uma estrutura para gerir configurações de execução.

Em execução _az ml computetarget anexar_ comando produz dois ficheiros no seu **aml_config** pasta no seu projeto: um .compute e um .runconfig seguir esta Convenção: _< your_ computetarget_name > .compute_ e _< your_computetarget_name > .runconfig_. O ficheiro de .runconfig é automaticamente criado para sua comodidade, quando cria um destino de computação. Pode criar e gerir outras configurações de execução utilizando _az ml runconfigurations_ comando na CLI. Também pode criar e editá-los no seu sistema de ficheiros.

Configuração de execução no Workbench também lhe permite especificar variáveis de ambiente. Pode especificar variáveis de ambiente e utilizá-los no seu código adicionando a seguinte secção no seu ficheiro .runconfig. 

```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Estas variáveis de ambiente podem ser acedidos no seu código. Por exemplo, este fragmento de código phyton imprime a variável de ambiente com o nome "EXAMPLE_ENV_VAR1"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**A figura seguinte mostra o fluxo de alto nível para experimentação inicial executar.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Cenários de execução da experimentação
Nesta secção, iremos aprofundar os cenários de execução e obter informações sobre o Azure Machine Learning é executado experimentações, especificamente com uma experimentação localmente, numa VM remota e num Cluster do HDInsight. Esta secção é obter instruções sobre a partir de criação de um destino de computação para a execução das suas experimentações.

>[!NOTE]
>Para o resto deste artigo estamos a utilizar os comandos da CLI (interface de linha de comandos) para mostrar os conceitos e as capacidades. Capacidades descritas aqui também podem ser utilizadas do Workbench.

## <a name="launching-the-cli"></a>Iniciar a CLI
Uma forma fácil de iniciar o CLI está a abrir um projeto no Workbench e navegar até **ficheiro--> Abra a linha de comandos**.

![](media/experimentation-service-configuration/opening-cli.png)

Este comando inicia uma janela de terminal, no qual pode introduzir comandos para executar scripts na pasta do projeto atual. Esta janela de terminal está configurada com o ambiente do Python 3.5.2, que é instalado por Workbench.

>[!NOTE]
> Quando executar qualquer _az ml_ comando na janela de comando, tem de ser autenticados no Azure. CLI utiliza uma cache de autenticação independentes, em seguida, a aplicação de ambiente de trabalho e, por isso, início de sessão em Workbench não significa que é autenticadas no seu ambiente CLI. Para autenticar, siga os passos abaixo. Token de autenticação é colocado em cache localmente durante um período de tempo para que apenas terá de repetir estes passos, quando o token expira. Quando o token expira ou se está a ver erros de autenticação, execute os seguintes comandos:

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Quando executa _az ml_ comando dentro de uma pasta do projeto, certifique-se de que o projeto pertence a uma conta do Azure Machine Learning experimentação no _atual_ subscrição do Azure. Caso contrário, poderá encontrar erros de execução.


## <a name="running-scripts-and-experiments"></a>Executar scripts e experimentações
Com o Workbench, pode executar o Python e scripts PySpark sobre várias computação destinos utilizando o _experimentação de ml az submeter_ comando. Este comando necessita de uma definição de configuração de execução. 

Workbench cria um ficheiro de .runconfig correspondente quando criar um destino de computação, mas pode criar configurações de execução adicionais utilizando _az ml runconfiguration criar_ comando. Pode editar manualmente os ficheiros de configuração de execução.

Configurações de execução apareçam como parte da experimentação executar experiência no Workbench. 

>[!NOTE]
>Pode saber mais sobre o ficheiro de configuração de execução no [experimentação referência da configuração de execução](experimentation-service-configuration-reference.md) secção.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Executar um script localmente no tempo de execução do Workbench instalados
Workbench permite-lhe executar os scripts diretamente no tempo de execução do Python instalado o Workbench 3.5.2. Este tempo de execução predefinido está instalado no momento da configuração Workbench e inclui bibliotecas do Azure Machine Learning e as dependências. Resultados de execução e artefactos para execuções de locais ainda estão guardados no serviço de histórico de execução na nuvem.

Ao contrário das execuções de com base em Docker, esta configuração é _não_ geridos pelo Conda. Terá de aprovisionar manualmente as dependências de pacote para o seu ambiente do Workbench Python local.

Pode executar o seguinte comando para executar o script localmente no ambiente do Workbench-instalado o Python. 

```
$az ml experiment submit -c local myscript.py
```

Pode encontrar o caminho para o ambiente do Python predefinido, escrevendo o seguinte comando na janela do Workbench CLI.
```
$ conda env list
```

>[!NOTE]
>A execução de PySpark localmente diretamente no local Spark ambientes está atualmente **não** suportado. Workbench suporta scripts do PySpark em execução no local Docker. Imagem do Azure Machine Learning base Docker vem com o Spark 2.1.11 pré-instaladas. 

_**Descrição geral de execução local para um script de Python:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Executar um script no local Docker
Também pode executar seus projetos num contentor Docker no seu computador local através do serviço de experimentação. Workbench fornece uma imagem base do Docker que vem com bibliotecas do Azure Machine Learning e, bem como o facilitar local Spark execuções do Spark 2.1.11 runtime. Docker tem de já estar em execução no computador local.

Para executar o script de Python ou PySpark num local Docker, pode executar os comandos seguintes na CLI.

```
$az ml experiment submit -c docker myscript.py
```
ou
```
az ml experiment submit --run-configuration docker myscript.py
```

O ambiente de execução do local Docker está preparado para utilizar a imagem de Docker base do Azure Machine Learning. Workbench transfere esta imagem quando em execução para a primeira vez e sobrepõe-lo com pacotes especificados no ficheiro de conda_dependencies.yml. Esta operação torna iniciais execução inferior mas execuções subsequentes são consideravelmente mais depressa graças ao Workbench reutilizar as camadas em cache. 

>[!IMPORTANT]
>Tem de executar _experimentação de ml az preparar docker - c_ comando pela primeira vez para preparar a imagem de Docker para a sua primeira passagem. Também pode definir o **PrepareEnvironment** parâmetro como true no seu ficheiro docker.runconfig. Esta ação automaticamente prepara o seu ambiente, como parte da sua execução de execução.  

>[!NOTE]
>Se executar um script de PySpark no Spark, spark_dependencies.yml também é utilizado para além de conda_dependencies.yml.

Executar os scripts numa imagem de Docker dá-lhe as seguintes vantagens:

1. Assegura que o script pode ser executado de forma fiável nos outros ambientes de execução. Em execução num contentor Docker ajuda-o a detetar e evitar quaisquer referências locais que podem afetar a portabilidade. 

2. Permite-lhe testar rapidamente a código em tempos de execucação e estruturas complexas para instalar e configurar, como o Apache Spark, sem ter de instalá-las pessoalmente.


_**Descrição geral de execução de Docker local para um script de Python:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Executar um script num Docker remoto
Em alguns casos, recursos disponíveis no seu computador local poderão não ser suficiente para preparar o modelo pretendido. Nesta situação, o serviço de experimentação permite uma forma fácil de executar os scripts Python ou PySpark em VMs mais poderosas com execução remota de Docker. 

VM remoto deve satisfazer os seguintes requisitos:
* VM remoto tem de estar em execução Linux Ubuntu e deve estar acessível através de SSH. 
* VM remoto tem de ter Docker em execução.

>[!IMPORTANT]
> Execução de VMs do Windows é Docker **não** suportado como destinos de remoto de computação


Pode utilizar o seguinte comando para criar ambas a definição de destino de computação e executar a configuração para execuções de com base em Docker remotas.

```
az ml computetarget attach --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" --type remotedocker
```

Depois de configurar o destino de computação, pode utilizar o seguinte comando para executar o script.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Tenha em atenção que execução ambiente estiver configurado com as especificações na conda_dependencies.yml. spark_dependencies.yml também é utilizado se não for especificado no ficheiro .runconfig PySpark framework. 

O processo de construção de Docker para VMs remotos é exatamente o mesmo que o processo para local Docker é executada pelo deve esperar uma experiência de execução semelhante.

>[!TIP]
>Se preferir evitar a latência introduzida ao criar a imagem de Docker para a sua primeira passagem, pode utilizar o seguinte comando para preparar o destino de computação antes de executar o script. preparar a experimentação de ml AZ - c<remotedocker>


_**Descrição geral da execução de vm remota para um script de Python:**_
![](media/experimentation-service-configuration/remote-vm-run.png)


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Executar um script num cluster do HDInsight
O HDInsight é uma plataforma para a análise de macrodados que suportam o Apache Spark popular. Workbench permite experimentação sobre macrodados com clusters do HDInsight Spark. 

Pode criar um destino de computação e execute a configuração para um cluster do Spark do HDInsight utilizando o seguinte comando:

```
$ az ml computetarget attach --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword" --type cluster 
```

>[!NOTE]
>Se utilizar o FQDN em vez de um endereço IP e nome do cluster do HDI Spark _foo_, é o ponto final SSH no nó controladores com o nome _foo-h_. Não se esqueça de **-ssh** postfix no nome do servidor ao utilizar o FQDN para _– endereço_ parâmetro.


Assim que tiver o contexto de computação, pode executar o seguinte comando para executar o script do PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench prepara e gere o ambiente de execução num cluster do HDInsight utilizando Conda. Configuração é gerida pelo _conda_dependencies.yml_ e _spark_dependencies.yml_ ficheiros de configuração. 

Precisa de acesso SSH para o cluster do HDInsight para executar experimentações neste modo. 

>[!NOTE]
>Configuração suportada é que clusters do HDInsight Spark Linux em execução (Ubuntu com o Python/PySpark 3.5.2 e Spark 2.1.11).

_**Descrição geral do HDInsight com base em execução para um script de PySpark**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Executar um script no GPU
Para executar os scripts no GPU, pode seguir as orientações neste artigo:[como utilizar GPU no Azure Machine Learning](how-to-use-gpu.md)


## <a name="next-steps"></a>Passos seguintes
* [Criar e instalar o Azure Machine Learning](quickstart-installation.md)
* [Gestão de modelo](model-management-overview.md)
