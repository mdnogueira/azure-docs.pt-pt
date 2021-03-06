---
title: "Aprovisionar uma máquina de Virtual ciência do Linux (Ubuntu) dados no Azure | Microsoft Docs"
description: "Configure e crie um dados ciência de Máquina Virtual para Linux (Ubuntu) no Azure para fazer análises e machine learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 557eab702175b1741cdcde62045947f2c031fbcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Aprovisionar as máquinas de ciência de dados para Linux (Ubuntu)

As máquinas de ciência de dados para o Linux é uma imagem de máquina virtual baseada no Ubuntu que torna mais fácil de iniciar a aprendizagem profunda no Azure. Learning profunda ferramentas incluem:

  * [Caffe](http://caffe.berkeleyvision.org/): uma estrutura de aprendizagem profunda criada para velocidade, expressivity e modularidade
  * [Caffe2](https://github.com/caffe2/caffe2): uma versão de plataforma do Caffe
  * [Toolkit de cognitivos](https://github.com/Microsoft/CNTK): uma profunda toolkit de software da Microsoft Research de aprendizagem
  * [H2O](https://www.h2o.ai/): uma plataforma de macrodados open source e a interface gráfica do utilizador
  * [Keras](https://keras.io/): uma alto nível rede neuronal da API no Python para Theano e TensorFlow
  * [MXNet](http://mxnet.io/): uma biblioteca de aprendizagem profunda flexível e eficiente com vários enlaces de idioma
  * [NVIDIA dígitos](https://developer.nvidia.com/digits): um sistema de gráfico que simplifica as tarefas comuns de aprendizagem profunda
  * [TensorFlow](https://www.tensorflow.org/): uma biblioteca de open source para intelligence máquina do Google
  * [Theano](http://deeplearning.net/software/theano/): biblioteca Python da para definir, otimizar e avaliação de forma eficiente matemática expressões que envolvem matrizes multidimensionais
  * [Torch](http://torch.ch/): uma estrutura de informática científicos com suporte wide para algoritmos de machine learning
  * CUDA, cuDNN e o controlador NVIDIA
  * Muitas blocos de notas do Jupyter de exemplo

Todas as bibliotecas são versões GPU, embora também são executadas na CPU.

As máquinas de ciência de dados para o Linux também contém as ferramentas populares para desenvolvimento e ciência de atividades de dados, incluindo:

* Edição de programador do Microsoft R com Microsoft R Open
* Distribuição do Python anaconda (versões 2.7 e 3.5), incluindo bibliotecas de análise de dados mais populares
* JuliaPro - uma distribuição organizada de idioma de Leonor com bibliotecas de análise de dados e científicos populares
* Instância de Spark autónomo e de nó único Hadoop (HDFS, Yarn)
* JupyterHub - um servidor de bloco de notas Jupyter multiuser R, Python, PySpark, kernels Leonor de suporte
* Explorador do Storage do Azure
* Interface de linha de comandos do Azure (CLI) para gerir recursos do Azure
* Ferramentas do Machine learning
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): um rápido de machine learning técnicas, como online, hash, allreduce, reductions, learning2search, Active Directory, de suporte do sistema e a configuração interativa
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta fornecer implementação de árvore rápido e preciso elevada
  * [Rattle](http://rattle.togaware.com/): uma ferramenta gráfica que torna a introdução de machine learning no R fácil e de análise de dados
  * [LightGBM](https://github.com/Microsoft/LightGBM): gradação rápida, distribuída, elevado desempenho os aumentos framework
* SDK do Azure em Java, Python, node.js, Ruby, PHP
* Bibliotecas na R e Python para utilizam no Azure Machine Learning e outros serviços do Azure
* Ferramentas de desenvolvimento e editores (RStudio PyCharm, IntelliJ, Emacs, vim)


Se o fizer ciência de dados envolve iterating numa sequência de tarefas:

1. Localizar, carregar e processar previamente os dados
2. Criar e testar modelos
3. Implementação de modelos para consumo em aplicações inteligentes

Cientistas de dados utilizam várias ferramentas para concluir estas tarefas. Pode ser bastante demorada para encontrar as versões do software adequadas e, em seguida, para transferir, compilação e instalar estas versões.

As máquinas de ciência de dados para o Linux poderá facilitar este fardo substancialmente. Utilize-o para iniciar o projeto de análise. Permite-lhe trabalhar nos tarefas em vários idiomas, incluindo R, Python, SQL Server, Java e C++. O Azure SDK incluído na VM permite-lhe criar as suas aplicações utilizando vários serviços no Linux para a plataforma de nuvem da Microsoft. Além disso, tem acesso a outros idiomas como Ruby, Perl, PHP e node.js, que também são instalados previamente.

Não existem sem custos de software para esta imagem VM de ciência de dados. Paga apenas as taxas de utilização de hardware do Azure são avaliadas com base no tamanho da máquina virtual que for aprovisionado. Obter mais detalhes sobre as taxas de computação podem ser encontrados no [página de listagem de VM no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Outras versões da Máquina Virtual de ciência de dados
A [CentOS](linux-dsvm-intro.md) imagem também está disponível, com muitas das mesmas ferramentas como a imagem de Ubuntu. A [Windows](provision-vm.md) imagem está também disponível.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar uma máquina de Virtual de ciência de dados para o Linux, tem de ter uma subscrição do Azure. Para obter uma, consulte o artigo [avaliação gratuita do Azure obter](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Criar a máquina de Virtual de ciência de dados para Linux
Eis os passos para criar uma instância da máquina de Virtual de ciência de dados para Linux:

1. Navegue para a máquina virtual listagem no [portal do Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu).
2. Clique em **criar** (na parte inferior) para fazer aparecer o assistente.![ configurar dados-ciência-vm](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
3. As secções seguintes fornecem as entradas para todos os passos no assistente (enumerado à direita da ilustração anterior) utilizadas para criar a Máquina Virtual de ciência de dados de Microsoft. Seguem-se as entradas necessárias para configurar cada um destes passos:
   
   a. **Noções básicas**:
   
   * **Nome**: nome do seu servidor de ciência de dados que está a criar.
   * **Nome de utilizador**: primeira conta de início de sessão ID.
   * **Palavra-passe**: primeira conta palavra-passe (pode utilizar a chave pública SSH em vez da palavra-passe).
   * **Subscrição**: Se tiver mais do que uma subscrição, selecione aquele no qual a máquina está a ser criado e cobrados. Tem de ter privilégios de criação de recursos para esta subscrição.
   * **Grupo de recursos**: pode criar uma nova ou utilize um grupo existente.
   * **Localização**: selecione o Centro de dados que é mais adequado. Normalmente, é o Centro de dados que a maioria dos seus dados, ou se aproxima-se à sua localização física para acesso de rede mais rápido.
   
   b. **Tamanho**:
   
   * Selecione um dos tipos de servidor que cumpra os requisitos funcionais e restrições de custo. Selecione **ver tudo** para ver mais opções de tamanhos de VM. Selecione uma VM de classe de NC para formação GPU.
   
   c. **Definições**:
   
   * **Tipo de disco**: escolha **Premium** se preferir uma unidade de estado sólido (SSD). Caso contrário, escolha **padrão**. VMs de GPU necessitam de um disco padrão.
   * **Conta de armazenamento**: pode criar uma nova conta de armazenamento do Azure na sua subscrição ou utilize uma já existente na mesma localização que escolheu no **Noções básicas** passo do assistente.
   * **Outros parâmetros**: na maioria dos casos, utilize apenas os valores predefinidos. A ter em consideração os valores não predefinidos, coloque o cursor sobre a ligação informativa para ajuda sobre as campos específicos.
   
   d. **Resumo**:
   
   * Certifique-se de que todas as informações que introduziu estão corretas.
   
   e. **Comprar**:
   
   * Para iniciar o aprovisionamento, clique em **comprar**. É fornecida uma ligação para os termos da transação. A VM não tem quaisquer taxas adicionais para além de computação para o tamanho de servidor que selecionou no **tamanho** passo.

O aprovisionamento deve demorar cerca de 5-10 minutos. O estado de aprovisionamento da é apresentado no portal do Azure.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Como aceder a máquina de Virtual de ciência de dados para Linux
Depois de criada a VM, pode iniciar sessão-lo através de SSH. Utilizar as credenciais da conta que criou no **Noções básicas** secção do passo 3 para a interface de shell de texto. No Windows, pode transferir uma ferramenta de cliente SSH como [Putty](http://www.putty.org). Se preferir um ambiente de trabalho gráfico (X Windows sistema), pode utilizar o Putty de reencaminhamento de X11 ou instalar o cliente X2Go.

> [!NOTE]
> O cliente X2Go efetuar melhor nos testes de reencaminhamento de X11. Recomendamos que utilize o cliente X2Go para uma interface gráfica do ambiente de trabalho.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Instalar e configurar o cliente X2Go
A VM com Linux já está a ser aprovisionado com o servidor de X2Go e pronto para aceitar ligações de cliente. Para se ligar no ambiente de trabalho de gráfico de VM com Linux, execute o seguinte procedimento no cliente:

1. Transferir e instalar o cliente X2Go para a sua plataforma de cliente de [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Executar o cliente X2Go e selecione **nova sessão**. É aberta uma janela de configuração com vários separadores. Introduza os seguintes parâmetros de configuração:
   * **Separador sessões**:
     * **Anfitrião**: O nome de anfitrião ou endereço IP da sua VM de ciência de dados do Linux.
     * **Início de sessão**: nome de utilizador na VM do Linux.
     * **Porta SSH**: deixe-22, o valor predefinido.
     * **Tipo de sessão**: Altere o valor para XFCE. Atualmente, a VM com Linux suporta apenas ambiente de trabalho XFCE.
   * **Separador de suporte de dados**: pode desativar o suporte de som e cliente impressão se não pretender utilizá-los.
   * **Pastas partilhadas**: Se pretender que diretórios das suas máquinas de cliente instaladas na VM do Linux, adicione os diretórios de máquina do cliente que pretende partilhar com a VM neste separador.

Depois de iniciar sessão para a VM ao utilizar o cliente SSH ou de ambiente de trabalho gráfico XFCE através do cliente X2Go, está pronto para começar a utilizar as ferramentas que estão instaladas e configuradas na VM. No XFCE, pode ver os atalhos de menu de aplicações e ícones do ambiente de trabalho para muitas das ferramentas.

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Ferramentas instaladas na máquina de Virtual de ciência de dados para Linux
### <a name="deep-learning-libraries"></a>Bibliotecas de aprendizagem profunda

#### <a name="cntk"></a>CNTK
O Toolkit de cognitivos é um open source, profunda toolkit de aprendizagem. Enlaces de Python estão disponíveis nos ambientes de Conda raiz e py35. Também tem uma ferramenta da linha de comandos (cntk) que já se encontra no caminho.

Blocos de notas do exemplo Python estão disponíveis no JupyterHub. Para executar um exemplo básico da linha de comandos, execute os seguintes comandos na shell:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Para obter mais informações, consulte a secção CNTK [GitHub](https://github.com/Microsoft/CNTK)e o [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe
Caffe é uma arquitetura de aprendizagem profunda do Centro de formação e Berkeley visão. Está disponível no /opt/caffe. Exemplos podem ser encontrados no /opt/caffe/examples.

#### <a name="caffe2"></a>Caffe2
Caffe2 é uma arquitetura de aprendizagem profunda do Facebook que está incorporada no Caffe. Está disponível no Python 2.7 no ambiente Conda raiz. Para o ativar, execute o seguinte a partir da shell:

    source /anaconda/bin/activate root

Algumas notas de exemplo estão disponíveis no JupyterHub.

#### <a name="h2o"></a>H2O
H2O é uma plataforma de Análise Preditiva e de aprendizagem rápida, dentro da memória, distribuída. Um pacote do Python está instalado em ambientes de Anaconda de raiz e py35. Também é instalado um pacote de R. Para iniciar o H2O a partir da linha de comandos, execute `java -jar /dsvm/tools/h2o/current/h2o.jar`; existem vários [opções da linha de comandos](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) que pretende configurar. O fluxo de IU da Web de podem ser acedido ao navegar para http://localhost:54321 para começar a utilizar. Notas de exemplo também estão disponíveis no JupyterHub.

#### <a name="keras"></a>Keras
Keras é uma alto nível rede neuronal da API no Python é capaz de executar na parte superior do TensorFlow ou Theano. Está disponível em ambientes de Python raiz e py35. 

#### <a name="mxnet"></a>MXNet
MXNet é uma arquitetura de aprendizagem profunda concebida para eficácia e flexibilidade. Tem enlaces R e Python incluídos no DSVM. Notas de exemplo são incluídas no JupyterHub, e código de exemplo está disponível no /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>NVIDIA DÍGITOS
O NVIDIA profunda Learning GPU formação sistema, conhecido como dígitos, é um sistema para simplificar as tarefas de aprendizagem profunda comuns, como a gestão de dados, estruturar e as redes neurais formação em sistemas GPU e monitorização do desempenho em tempo real com visualização avançada. 

DÍGITOS está disponível como um serviço, denominado dígitos. Inicie o serviço e navegue até à http://localhost:5000 para começar a utilizar.

DÍGITOS também é instalado como um módulo de Python no ambiente Conda raiz.

#### <a name="tensorflow"></a>TensorFlow
TensorFlow é biblioteca de aprendizagem profunda da Google. É uma biblioteca de software código aberto para o cálculo numérica utilizar gráficos de fluxo de dados. TensorFlow está disponível no ambiente py35 Python e alguns blocos de notas do exemplo estão incluídos no JupyterHub.

#### <a name="theano"></a>Theano
Theano é uma biblioteca de Python para o cálculo numérica eficiente. Está disponível em ambientes de Python raiz e py35. 

#### <a name="torch"></a>Torch
Torch é uma arquitetura de informática científicos com suporte wide para algoritmos do machine learning. Está disponível no /dsvm/tools/torch e a sessão interativa ésimo e o Gestor de pacotes luarocks estão disponíveis na linha de comandos. Exemplos estão disponíveis no /dsvm/samples/torch.

PyTorch também está disponível no ambiente de Anaconda de raiz. Os exemplos são no /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Servidor Microsoft R
R é um dos idiomas mais populares para análise de dados e de aprendizagem. Se pretender utilizar o R para a sua análise, a VM possui Microsoft R Server (MRS) com o Microsoft R abrir (MRO) e a biblioteca de Kernel de bibliotecas (MKL). O MKL otimiza operações bibliotecas comuns no algoritmos analíticos. MRO é 100 por cento compatível com CRAN R e qualquer uma das bibliotecas publicadas no CRAN R pode ser instalado no MRO. MRS dá-lhe dimensionar e operationalization dos modelos de R para os serviços web. Pode editar os programas de R dos editores predefinido, como RStudio, vi ou Emacs. Se preferir utilizar o editor de Emacs, tiver sido previamente instalado. O pacote de Emacs RIMIR (Emacs Speaks estatísticas) simplifica a trabalhar com ficheiros de R dentro do editor de Emacs.

Para iniciar R consola, basta escrevê **R** na shell. Isto leva-o para um ambiente interativo. Para desenvolver o seu programa de R, normalmente, utilize um editor como Emacs ou vi e, em seguida, execute os scripts dentro do R. Com RStudio, tem um ambiente IDE completa gráfico para desenvolver o seu programa de R.

Há também um script do R para que instale o [pacotes primeiros 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) se pretender. Este script pode ser executado após são na interface interativa R, que pode ser introduzida (tal como mencionado) escrevendo **R** na shell.  

### <a name="python"></a>Python
Para o desenvolvimento com o Python, distribuição Anaconda Python 2.7 e 3.5 foi instalada. Esta distribuição contém o Python base juntamente com cerca de 300 dos pacotes de análise de bibliotecas, engenharia e os dados mais populares. Pode utilizar os editores de texto predefinido. Além disso, pode utilizar Spyder, um IDE do Python que está incluído com as distribuições de Anaconda Python. Spyder necessita de um ambiente de trabalho gráfico ou X11 reencaminhamento. Um atalho para Spyder é fornecido no ambiente de trabalho do gráfico.

Uma vez que temos o Python 2.7 e 3.5, terá de ativar especificamente a versão Python pretendida (conda ambiente) que pretende trabalhar na sessão atual. O processo de ativação define a variável de caminho para a versão pretendida do Python.

Para ativar o ambiente do Python 2.7 conda, execute o seguinte comando a partir da shell:

    source /anaconda/bin/activate root

Python 2.7 está instalado no */anaconda/bin*.

Para ativar o ambiente do Python 3.5 conda, execute o seguinte a partir da shell:

    source /anaconda/bin/activate py35


Python 3.5 está instalado no */anaconda/envs/py35/bin*.

Para invocar uma sessão interativa de Python, basta escrevê **python** na shell. Se estiver numa interface gráfica ou ter o conjunto de cópias de segurança de reencaminhamento de X11, pode escrever **pycharm** para iniciar o IDE do Python PyCharm.

Para instalar as bibliotecas adicionais do Python, tem de executar ```conda``` ou ````pip```` comandos em sudo e forneça o caminho completo do Gestor de pacote Python (conda ou pip) para instalar o ambiente do Python correto. Por exemplo:

    sudo /anaconda/bin/pip install <package> #for Python 2.7 environment
    sudo /anaconda/envs/py35/bin/pip install <package> # for Python 3.5 environment


### <a name="jupyter-notebook"></a>Bloco de notas do Jupyter
A distribuição de Anaconda também inclui um bloco de notas do Jupyter, um ambiente para partilhar o código e análise. O bloco de notas do Jupyter é acedido através do JupyterHub. Inicie sessão com o nome de utilizador de Linux local e a palavra-passe.

O servidor de bloco de notas do Jupyter foi previamente configurado com o Python 2, Python 3 e R kernels. Não há um ícone de ambiente de trabalho com o nome "De notas do Jupyter" para iniciar o browser para aceder ao servidor de bloco de notas. Se estiver na VM através do cliente SSH ou X2Go, também pode visitar [https://localhost:8000 /](https://localhost:8000/) para aceder ao servidor de bloco de notas do Jupyter.

> [!NOTE]
> Continue se obter quaisquer avisos de certificado.
> 
> 

Pode aceder ao servidor de bloco de notas do Jupyter de qualquer anfitrião. Basta escrevê *https://\<nome DNS de VM ou o endereço IP\>: 8000 /*

> [!NOTE]
> Porta 8000 está aberta na firewall por predefinição quando a VM está aprovisionada.
> 
> 

Podemos ter empacotada exemplo blocos de notas – Python num e um em R. Pode ver a ligação para os exemplos na home page do bloco de notas, depois de se autenticar para o bloco de notas do Jupyter utilizando o seu nome de utilizador de Linux local e a palavra-passe. Pode criar um novo bloco de notas, selecionando **novo**e, em seguida, o kernel do idioma apropriado. Se não vir o **novo** botão, clique em de **Jupyter** ícone na parte superior esquerda para aceder à home page do servidor do bloco de notas.

### <a name="apache-spark-standalone"></a>Apache Spark autónomo 
Uma instância autónoma do Apache Spark está pré-instalado DSVM o Linux para o ajudar a desenvolver aplicações do Spark localmente primeiro antes de testar e implementar em grandes clusters. Pode executar programas PySpark através do kernel do Jupyter. Quando abrir o Jupyter, clique em de **novo** botão e deverá ver uma lista de kernels disponíveis. O "Spark - Python" é o kernel do PySpark que lhe permite criar Spark aplicações utilizando a linguagem de Python. Também pode utilizar um IDE do Python como PyCharm ou Spyder para criar, o programa de Spark. Desde, esta é uma instância autónoma, a pilha de Spark executa o programa de cliente chamar. Isto torna mais rápido e mais fácil solucionar problemas em comparação com a desenvolver um cluster do Spark. 

Um bloco de notas do PySpark de exemplo é fornecido no Jupyter que pode encontrar no diretório "SparkML" sob o diretório de raiz do Jupyter ($HOME/blocos de notas/SparkML/pySpark). 

Se estiver de programação R para Spark, pode utilizar o Microsoft R Server, SparkR ou sparklyr. 

Antes de executar no contexto do Spark no Microsoft R Server, terá de executar uma uma vez que o passo de configuração para ativar um único nó HDFS do Hadoop e o Yarn instância local. Por predefinição, serviços do Hadoop são instalados, mas o DSVM desativados. Para ativá-la, terá de executar os comandos seguintes como raiz na primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Pode parar o Hadoop relacionados com serviços quando não precisar deles executando ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` uma amostra que demonstra como desenvolver e testar MRS no contexto de Spark remoto (que é a instância de Spark autónomo no DSVM) é fornecida e disponível na `/dsvm/samples/MRS` diretório. 

### <a name="ides-and-editors"></a>IDEs e editores
Tem de escolher entre vários editores de código. Isto inclui vi/VIM, Emacs, PyCharm, RStudio e IntelliJ. IntelliJ, RStudio PyCharm são editores gráficas e tem de ter sessão iniciada para um ambiente de trabalho gráfico utilizá-los. Estes editores tem o ambiente de trabalho e aplicações atalhos do menu iniciá-los.

**VIM** e **Emacs** são editores baseados em texto. Em Emacs, iremos tenha instalado um pacote de suplemento chamado Emacs Speaks estatísticas (RIMIR) que facilita a trabalhar com R dentro do editor de Emacs. Podem encontrar mais informações em [RIMIR](http://ess.r-project.org/).

**LaTex** está instalada através do pacote de texlive juntamente com um suplemento Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) pacote, o que simplifica a criação os documentos LaTex dentro Emacs.  

### <a name="databases"></a>Bases de Dados

#### <a name="graphical-sql-client"></a>Cliente gráfica do SQL Server
**SQuirrel SQL**, um cliente do SQL Server gráfico, foi fornecido para ligação às bases de dados diferentes (por exemplo, o Microsoft SQL Server e o MySQL) e para executar consultas do SQL Server. Pode executar este partir de uma sessão de ambiente de trabalho gráfica (utilizando o cliente X2Go, por exemplo). Para invocar SQuirrel SQL, pode iniciar do ícone no ambiente de trabalho ou execute o seguinte comando na shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Antes da primeira utilização, configurar os controladores e os aliases de base de dados. Os controladores JDBC estão localizados em:

*/usr/share/Java/jdbcdrivers*

Para obter mais informações, consulte [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas de linha de comandos para aceder ao Microsoft SQL Server
O pacote de controladores ODBC para o SQL Server também inclui duas ferramentas da linha de comandos:

**BCP**: O volume de utilitário bcp copia dados entre uma instância do Microsoft SQL Server e um ficheiro de dados num formato especificado por um utilizador. Pode ser utilizado o utilitário bcp para importar um grande número de linhas novas para tabelas do SQL Server ou para exportar dados para fora tabelas para ficheiros de dados. Para importar dados para uma tabela, tem de utilizar um ficheiro de formato criado para essa tabela ou compreender a estrutura da tabela e os tipos de dados que são válidos para as colunas.

Para obter mais informações, consulte [estabelecer ligação com o bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SQLCMD**: pode introduzir instruções Transact-SQL com o utilitário sqlcmd, bem como os procedimentos de sistema e os ficheiros na linha de comandos do script. Este utilitário utiliza ODBC a execução de lotes de Transact-SQL.

Para obter mais informações, consulte [ligar com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Existem algumas diferenças neste utilitário entre plataformas Linux e Windows. Consulte a documentação para obter mais detalhes.
> 
> 

#### <a name="database-access-libraries"></a>Bibliotecas de acesso de base de dados
Bibliotecas estão disponíveis no R e Python bases de dados de acesso.

* No R, o **RODBC** pacote ou **dplyr** pacote permite-lhe consultar ou executar instruções SQL no servidor de base de dados.
* No Python, o **pyodbc** biblioteca fornece acesso a base de dados com ODBC como camada subjacente.  

### <a name="azure-tools"></a>Ferramentas do Azure
As seguintes ferramentas do Azure são instaladas na VM:

* **Interface de linha de comandos do Azure**: A CLI do Azure permite-lhe criar e gerir recursos do Azure através de comandos da shell. Para invocar as ferramentas do Azure, basta escrevê **do azure ajuda**. Para obter mais informações, consulte o [página de documentação do CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Explorador de armazenamento do Microsoft Azure**: Explorador de armazenamento do Microsoft Azure é uma ferramenta gráfica que é utilizada para percorrer os objetos que tem de ser armazenados na sua conta de armazenamento do Azure e para carregar e transferir dados para e de blobs do Azure. Pode aceder Explorador de armazenamento a partir do ícone de atalho do ambiente de trabalho. Pode invocar a partir de uma linha de shell, escrevendo **StorageExplorer**. Tem de ter sessão iniciada a partir de um cliente X2Go ou ter o conjunto de cópias de segurança de reencaminhamento de X11.
* **Bibliotecas do Azure**: seguem-se algumas das bibliotecas pré-instaladas.
  
  * **Python**: relacionadas com o Azure bibliotecas no Python que é instaladas são **azure**, **azureml**, **pydocumentdb**, e **pyodbc**. Com as três primeiros bibliotecas, pode aceder a serviços de armazenamento do Azure, Azure Machine Learning e base de dados do Azure Cosmos (uma base de dados NoSQL no Azure). A biblioteca quarta, pyodbc (juntamente com o Microsoft ODBC driver para SQL Server), permite que o acesso do SQL Server, SQL Database do Azure e Azure SQL Data Warehouse do Python através da utilização de uma interface ODBC. Introduza **lista pip** para ver todas as bibliotecas listadas. Lembre-se de que executa este comando no Python 2.7 e 3.5 ambientes.
  * **R**: relacionadas com o Azure, as bibliotecas no R instalados estão **AzureML** e **RODBC**.
  * **Java**: A lista de bibliotecas de Java do Azure pode ser encontrada no diretório **/dsvm/sdk/AzureSDKJava** na VM. As bibliotecas de chaves são Azure armazenamento e gestão de APIs, base de dados do Azure Cosmos e JDBC controladores para o SQL Server.  

Pode aceder a [portal do Azure](https://portal.azure.com) do browser Firefox pré-instaladas. No portal do Azure, pode criar, gerir e monitorizar os recursos do Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O Azure Machine Learning é um serviço em nuvem completamente gerido que lhe permite criar, implementar e partilhar as soluções de Análise Preditiva. Criar os modelos e experimentações do Azure Machine Learning Studio. Pode ser acedido a partir de um browser web na máquina virtual de ciência de dados, visitando [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de se inscrever Azure Machine Learning Studio, tem acesso a uma tela de experimentação onde pode criar um fluxo lógico para os algoritmos do machine learning. Também tem acesso a um bloco de notas do Jupyter alojado no Azure Machine Learning e pode trabalhar de forma totalmente integrada com experimentações no Machine Learning Studio. Operacionalize os modelos de machine learning que criou envolvendo-los numa interface de serviço web. Isto permite aos clientes escritos em qualquer idioma invocar predições a partir de modelos de machine learning. Para obter mais informações, consulte o [documentação de Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Pode também criar os seus modelos no R ou Python na VM e, em seguida, implementá-la na produção no Azure Machine Learning. Iremos instalou bibliotecas R (**AzureML**) e Python (**azureml**) para ativar esta funcionalidade.

Para obter informações sobre como implementar modelos no R e Python no Azure Machine Learning, consulte [dez coisas que pode fazer na ciência de dados de Máquina Virtual](vm-do-ten-things.md) (em especial, a secção "Criar modelos de utilizar o R ou Python e Operacionalizá-los utilizando o Azure Machine Learning").

> [!NOTE]
> Estas instruções destinam-se a versão do Windows da VM de ciência de dados. Mas as informações fornecidas na implementação de modelos Azure Machine Learning há aplicáveis para a VM com Linux.
> 
> 

### <a name="machine-learning-tools"></a>Ferramentas do Machine learning
A VM inclui alguns do machine learning ferramentas e algoritmos que tenham sido pré-compilado e previamente instalados localmente. Estas incluem:

* **Vowpal Wabbit**: um algoritmo de aprendizagem rápido online.
* **xgboost**: uma ferramenta que proporciona algoritmos da árvore otimizada, elevada.
* **Rattle**: com base em R uma ferramenta gráfica para exploração de dados fácil e de modelação.
* **Python**: Anaconda Python vem agrupada com algoritmos do machine learning com bibliotecas como saber de Scikit. Pode instalar outras bibliotecas utilizando o `pip install` comando.
* **LightGBM**: gradação rápida, distribuída, elevado desempenho os aumentos framework com base nos algoritmos da árvore de decisões.
* **R**: uma biblioteca completa de funções do machine learning está disponível para R. Algumas das bibliotecas que vêm pré-instaladas são lm, glm, randomForest, rpart. Outras bibliotecas de podem ser instaladas através da execução:
  
        install.packages(<lib name>)

Eis algumas informações adicionais sobre as ferramentas de três aprendizagem primeiro na lista.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit é uma sistema operativo que utiliza técnicas, como online, hash, allreduce, reductions, learning2search, Active Directory, de aprendizagem e a configuração interativa.

Para executar a ferramenta num exemplo básico, efetue o seguinte:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Existem outras, maior demonstrações nesse diretório. Para obter mais informações sobre VW, consulte [nesta secção do GitHub](https://github.com/JohnLangford/vowpal_wabbit)e o [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Esta é uma biblioteca que está concebida e otimizada para algoritmos (árvore elevada). O objetivo desta biblioteca consiste em emitir os limites de cálculo das máquinas para extremes necessárias para fornecer árvore em grande escala, os aumentos que dimensionável, portáteis e precisos.

É fornecido como uma linha de comandos, bem como uma biblioteca de R.

Para utilizar esta biblioteca no R, pode iniciar uma sessão interativa de R (apenas escrevendo **R** na shell) e carregar a biblioteca.

Eis um exemplo simples, que pode executar numa linha de comandos do R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Para executar a linha de comandos xgboost, seguem-se os comandos para executar na shell:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Um ficheiro de .model é escrito para o diretório especificado. Pode encontrar informações sobre este exemplo de demonstração [no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para mais informações sobre xgboost, consulte o [página de documentação xgboost](https://xgboost.readthedocs.org/en/latest/)e a respetiva [repositório do GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle (o **R** **A**nalytical **T**ool **T**Nã **L**mais significativos beneficiar **i**asily) utiliza a exploração de dados baseadas em GUI e modelação. Apresente resumos de análises e visual de dados, os dados de transformações que podem ser prontamente modelados, cria supervisionados e supervisionados os modelos de dados, apresenta o desempenho dos modelos graficamente, e define dados pontuações de novo. Também gera código do R, replicar as operações na IU do que podem ser executadas diretamente no R ou utilizadas como um ponto de partida para análise adicional.

Para executar Rattle, tem de ser uma gráfica ambiente de trabalho início de sessão. No terminal, escreva ```R``` para introduzir o ambiente do R. Na linha de R, introduza os seguintes comandos:

    library(rattle)
    rattle()

Agora uma interface gráfica abre-se com um conjunto de separadores. Eis os passos de início rápido no Rattle precisam para utilizar um conjunto de dados de Meteorologia de exemplo e criar um modelo. Em alguns dos passos abaixo, lhe for pedido para instalar automaticamente e carregar alguns pacotes necessários do R que ainda não no sistema.

> [!NOTE]
> Se não tiver acesso para instalar o pacote no diretório do sistema (predefinição), poderá ver uma linha de comandos na janela de consola do R para instalar pacotes para a sua biblioteca pessoal. Resposta *y* se vir estes pedidos.
> 
> 

1. Clique em **Executar**.
2. Uma caixa de diálogo aparece, pedimos-lhe se pretende utilizar no conjunto de dados de Meteorologia de exemplo. Clique em **Sim** ao carregar o exemplo.
3. Clique em de **modelo** separador.
4. Clique em **executar** para construir uma árvore de decisões.
5. Clique em **desenhar** para apresentar a árvore de decisões.
6. Clique em de **floresta** botão de opção e clique em **executar** para criar uma floresta aleatória.
7. Clique em de **Evaluate** separador.
8. Clique em de **risco** botão de opção e clique em **executar** a apresentar dois rastreia de desempenho de risco (Cumulative).
9. Clique em de **registo** separador para mostrar o código de gerar R para as operações de anteriores.
   (Resultam de erros na versão atual do Rattle, tem de inserir um  *#*  caráter à frente do *exportar este registo...*  no texto do registo.)
10. Clique em de **exportar** botão para guardar o ficheiro de script do R denominado *weather_script. R* para a pasta raiz.

Pode sair Rattle e R. Agora pode modificar o script R gerado ou utilizá-lo, dado que está a executá-la em qualquer altura para repetir tudo o que foi concluído dentro da IU Rattle. Especialmente para principiantes em R, esta é uma forma fácil para fazer a análise e machine learning numa interface gráfica simple, ao gerar automaticamente o código no R para modificar e/ou mais rapidamente.

## <a name="next-steps"></a>Passos seguintes
Eis como pode continuar a exploração e de aprendizagem automática:

* O [ciência de dados na máquina de Virtual de ciência de dados para Linux](linux-dsvm-walkthrough.md) instruções mostram como efetuar várias tarefas de ciência de dados comuns com a VM de ciência de dados de Linux aprovisionados aqui. 
* Explore as várias ferramentas de ciência de dados de ciência de dados VM por experimentar as ferramentas descritas neste artigo. Também pode executar *dsvm-mais-info* na shell na máquina virtual para uma introdução básica e ponteiros para obter mais informações sobre as ferramentas instaladas na VM.  
* Aprenda a criar soluções analíticas ponto-a-ponto sistematicamente utilizando o [o processo de ciência de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visite o [galeria do Cortana análise](http://gallery.cortanaanalytics.com) machine learning e dados análise exemplos que utilizam o conjunto de análise da Cortana.

