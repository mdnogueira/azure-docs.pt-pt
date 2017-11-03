---
title: "Aprovisionar a máquina de Virtual de ciência de dados Windows no Azure | Microsoft Docs"
description: "Configurar e criar uma máquina de Virtual de ciência de dados no Azure para análise e machine learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: d0a9926f49e2be66a9d51a1bb0e4e19342205880
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Aprovisionar a máquina de Virtual de ciência de dados Windows no Azure
A máquina de Virtual de ciência de dados do Microsoft é uma imagem de máquina virtual (VM) do Windows Azure previamente instalado e configurado com várias ferramentas populares que são frequentemente utilizadas para análise de dados e de aprendizagem. As ferramentas incluídas são:

* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning-services/) Workbench
* [Servidor do Microsoft ML](https://docs.microsoft.com/machine-learning-server/index) edição Programador
* Distribuição do Python anaconda
* Bloco de notas do Jupyter (com R, Python, PySpark kernels)
* Edição do Visual Studio Community
* Ambiente de trabalho do Power BI
* Edição do SQL Server 2017 Programador
* Instância de Spark autónoma de desenvolvimento local e teste
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning e as ferramentas de análise de dados
  * Profunda estruturas de aprendizagem: um conjunto avançado de estruturas de AI, incluindo [Toolkit de cognitivos](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras estão incluídas na VM.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): um rápido de machine learning técnicas, como online, hash, allreduce, reductions, learning2search, Active Directory, de suporte do sistema e a configuração interativa.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta fornecer implementação de árvore rápido e preciso elevada.
  * [Rattle](http://rattle.togaware.com/) (o R analíticos ferramenta para saber mais facilmente): uma ferramenta que torna a introdução à análise de dados e a máquina no R fácil, com a exploração de dados baseadas em GUI e modelação com a geração de código de R automática.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : uma extração de dados do elemento visual e do machine learning software em Java.
  * [Apache desagregação](https://drill.apache.org/): um motor de consulta de SQL sem esquema para o Hadoop, NoSQL e armazenamento na nuvem.  Suporte ODBC e JDBC interfaces para ativar a consulta NoSQL e ficheiros a partir de ferramentas de BI padrão, como o Power BI, o Excel, o Tableau.
* Bibliotecas na R e Python para utilizam no Azure Machine Learning e outros serviços do Azure
* Git, incluindo o Git Bash para trabalhar com os repositórios de código de origem, incluindo o GitHub, Visual Studio Team Services
* Portas de Windows de vários populares Linux da linha de comandos utilitários (incluindo awk po, perl, grep, localizar, wget, curl etc) acessível através da linha de comandos. 

Se o fizer ciência de dados envolve iterating numa sequência de tarefas:

1. Localizar, carregar e processar previamente os dados
2. Criar e testar modelos
3. Implementação de modelos para consumo em aplicações inteligentes

Cientistas de dados utilizam uma variedade de ferramentas para concluir estas tarefas. Pode ser bastante demorada para encontrar as versões do software, adequadas e, em seguida, transferir e instalá-los. A máquina de Virtual de ciência de dados do Microsoft poderá facilitar este fardo, fornecendo uma imagem de prontos a utilizar pode ser aprovisionada no Azure com todas as ferramentas de populares várias previamente instalado e configurado. 

A máquina de Virtual de ciência de dados do Microsoft jump-starts o projeto de análise. Permite-lhe trabalhar nos tarefas em vários idiomas, incluindo R, Python, SQL e c#. O Visual Studio fornece um IDE para desenvolver e testar o seu código que é fácil de utilizar. O Azure SDK incluído na VM permite-lhe criar as suas aplicações com vários serviços na plataforma de nuvem da Microsoft. 

Não existem sem custos de software para esta imagem VM de ciência de dados. Apenas paga as taxas de utilização do Azure que dependente o tamanho da máquina virtual que for aprovisionado. Obter mais detalhes sobre as taxas de computação podem ser encontrados na secção de detalhes de preços no [máquinas de virtuais de ciência de dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) página. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Outras versões da Máquina Virtual de ciência de dados
Um [Ubuntu](dsvm-ubuntu-intro.md) imagem está disponível, com diversas ferramentas semelhantes e alguns avançada adicionais estruturas de aprendizagem. A [CentOS](linux-dsvm-intro.md) imagem também está disponível. Podemos também oferecem um [edição do Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) da máquina virtual de ciência de dados embora alguns ferramentas só estão disponíveis na edição Windows Server 2016.  Caso contrário, este artigo também se aplica a edição do Windows Server 2012.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar uma Máquina Virtual de ciência de dados de Microsoft, tem de ter o seguinte:

* **Uma subscrição do Azure**: obter um, consulte [avaliação gratuita do Azure obter](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Criar a máquina de Virtual de ciência de dados da Microsoft
Eis os passos para criar uma instância de Máquina Virtual Microsoft dados ciência:

1. Navegue para a máquina virtual listagem no [portal do Azure](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Selecione o **criar** na parte inferior para ser levados num assistente.![ configurar dados-ciência-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. Requer que o assistente utilizado para criar a máquina de Virtual de ciência de dados do Microsoft **entradas** para cada um do **quatro passos** enumerado à direita desta figura. Seguem-se as entradas necessárias para configurar cada um destes passos:
   
   1. **Noções básicas**
      
      1. **Nome**: nome do seu servidor de ciência de dados que está a criar.
      2. **Tipo de disco da VM**: escolher entre SSD ou HDD. Para a GPU (série de NC), escolha **HDD** como o tipo de disco. 
      3. **Nome de utilizador**: id de início de sessão de conta de administrador.
      4. **Palavra-passe**: palavra-passe da conta de administrador.
      5. **Subscrição**: Se tiver mais do que uma subscrição, selecione aquele no qual a máquina está a ser criado e cobrados.
      6. **Grupo de recursos**: pode criar uma nova ou utilize um grupo existente.
      7. **Localização**: selecione o Centro de dados que é mais adequado. Normalmente, é o Centro de dados que contém a maioria dos seus dados ou se mais próxima da sua localização física para acesso de rede mais rápido.
   2. **Tamanho**: selecione um dos tipos de servidor que cumpra os requisitos funcionais e restrições de custo. Pode obter mais opções de tamanhos de VM, selecionando "Vista All".
   3. **Definições**:
      
      1. **Utilizar discos geridos**: escolha geridos se pretender que o Azure para gerir os discos para a VM.  Caso contrário, terá de especificar uma conta de armazenamento nova ou exitsting. 
      2. **Outros parâmetros**: normalmente utiliza os valores predefinidos. Pode passar através da ligação de ajuda sobre as campos específicos informativa no caso de que pretende considerar a utilização de valores não predefinidos.
   4. **Resumo**: Certifique-se de que todas as informações que introduziu estão corretas e clique em **criar**. **Tenha em atenção**: A VM não tem quaisquer taxas adicionais para além de computação para o tamanho de servidor que selecionou no **tamanho** passo. 

> [!NOTE]
> O aprovisionamento deve demorar cerca de 10 a 20 minutos. O estado de aprovisionamento da é apresentado no portal do Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Como aceder a máquina de Virtual de ciência de dados do Microsoft
Depois da VM é criada, pode ambiente de trabalho remoto para a mesma utilizando as credenciais de conta de administrador que configurou no precedente **Noções básicas** secção. 

Assim que a VM é criada e aprovisionada, estará pronto começar a utilizar as ferramentas que estão instaladas e configuradas no mesmo. Existem mosaicos de menu de início e de ambiente de trabalho ícones de muitas das ferramentas. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Ferramentas instaladas na máquina Virtual Microsoft dados ciência

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Workbench de aprendizagem máquina é uma aplicação de ambiente de trabalho e a interface de linha de comandos. O Workbench tem preparação de dados incorporados que aprende os passos de preparação de dados, como executá-los. Também fornece gestão de projeto, execute o histórico e integração de bloco de notas para aumente a produtividade. Pode tirar partido das melhores estruturas de código aberto, incluindo TensorFlow, cognitivos Toolkit, Spark ML e scikit-Saiba como desenvolver os seus modelos. No DSVM, fornecemos um ícone de ambiente de trabalho (InstallAMLFromLocal), a extrair localmente o workbench do Azure Machine Learning no diretório de cada utilizador % LOCALAPPDATA %. Cada utilizador que tem de utilizar o Workbench tem de efetuar uma ação de duplo clicar no ícone de ambiente de trabalho InstallAMLFromLocal para instalar as respetivas instâncias do Workbench do tempo. O Azure Machine Learning também cria e utiliza um ambiente do Python por utilizador que é extraído do % LOCALAPPDATA%\amlworkbench\python.

### <a name="microsoft-ml-server-developer-edition"></a>Edição de programador do Microsoft Server de ML
Se pretender utilizar bibliotecas do Microsoft enterprise para dimensionável R ou Python para a sua análise, a VM possui edição de ML programador do servidor da Microsoft (anteriormente conhecida como Microsoft R Server) instalada. Microsoft Server de ML é uma plataforma de análise amplamente implementável classe empresarial disponível para o R e Python e é dimensionável, comercialmente suportada e seguro. O suporte para uma variedade de estatísticas de macrodados, modelação preditiva e do machine learning capacidades, o servidor de ML suporta o intervalo completo de análise – exploração, análise, visualização e modelação. Ao utilizar e expandir open source para R e Python, Microsoft ML Server é totalmente compatível com R / scripts Python, funções e CRAN / através do pip / Dimensionar Conda pacotes, para analisar os dados na empresa. Também aborda as limitações de memória de abrir R de origem através da adição de processamento paralelo e segmentos de dados. Isto permite-lhe executar a análise nos dados muito maior do que o se adequa na memória principal.  Visual Studio Community edição incluído na VM contém as ferramentas de R para Visual Studio e Python ferramentas para a extensão do Visual Studio que fornece um IDE de completa para trabalhar com R ou Python. Fornecemos também outros IDEs, bem como [RStudio](http://www.rstudio.com) e [edição PyCharm Community](https://www.jetbrains.com/pycharm/) na VM. 

### <a name="python"></a>Python
Para o desenvolvimento com o Python, distribuição Anaconda Python 2.7 e 3.5 foi instalada. Esta distribuição contém o Python base juntamente com cerca de 300 dos pacotes de análise de bibliotecas, engenharia e os dados mais populares. Pode utilizar as ferramentas do Python para Visual Studio (das PTVS) que é instalada na edição do Visual Studio 2015 Community ou um do IDEs incluídas com Anaconda como INATIVO ou Spyder. Pode iniciar um ao pesquisar na barra de pesquisa (**Win** + **S** chave).

> [!NOTE]
> Para apontar o ferramentas do Python para Visual Studio Anaconda Python 2.7 e 3.5, terá de criar ambientes personalizadas para cada versão. Para definir estes caminhos de ambiente na edição Community 2015 Studio Visual, navegue até à **ferramentas** -> **ferramentas do Python** -> **ambientes do Python**e, em seguida, clique em **+ personalizada**. 
> 
> 

Anaconda Python 2.7 está instalado em C:\Anaconda e Anaconda Python 3.5 está instalado em c:\Anaconda\envs\py35. Consulte [documentação das PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obter passos detalhados. 

### <a name="jupyter-notebook"></a>Bloco de Notas do Jupyter
Distribuição de anaconda também inclui um bloco de notas do Jupyter, um ambiente para partilhar o código e análise. Um servidor de bloco de notas do Jupyter foi previamente configurado com o Python 2.7, Python 3.5, PySpark, Leonor e R kernels. Não há um ícone de ambiente de trabalho com o nome "De notas do Jupyter" para iniciar o servidor do Jupyter e iniciar o browser para aceder ao servidor de bloco de notas. 

> [!NOTE]
> Continue se obter quaisquer avisos de certificado. 
> 
> 

Podemos ter empacotada vários blocos de notas do exemplo no Python e em R. Os blocos de notas do Jupyter mostram como trabalhar com o Microsoft ML Server, SQL Server ML Services (análise na base de dados), Python, Microsoft cognitivos ToolKit, Tensorflow e outras tecnologias do Azure, uma vez aceder Jupyter. Pode ver a ligação para os exemplos na home page do bloco de notas, depois de se autenticar para o bloco de notas do Jupyter com a palavra-passe que criou no passo anterior. 

### <a name="visual-studio-2017-community-edition"></a>Edição do Visual Studio Community de 2017
Edição de Comunidade do Studio Visual instalada na VM. É uma versão gratuita do IDE popular da Microsoft que pode utilizar para fins de avaliação e para as equipas de pequenas. Pode consultar os termos de licenciamento [aqui](https://www.visualstudio.com/support/legal/mt171547).  Abra o Visual Studio, fazendo duplo clique no ícone de ambiente de trabalho ou a **iniciar** menu. Também pode procurar programas com **Win** + **S** e introduzir "Visual Studio". Uma vez aqui, pode criar projetos em idiomas c#, Python, R, node.js. Plug-ins são também instalados que tornam conveniente trabalhar com os serviços do Azure como o catálogo de dados do Azure, Azure HDInsight (Hadoop, Spark) e do Azure Data Lake. 

> [!NOTE]
> Poderá receber uma mensagem a indicar que o período de avaliação expirou. Introduza as credenciais da conta Microsoft ou criar uma nova conta gratuita para obter acesso para a edição de Comunidade do Visual Studio. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>Edição do SQL Server 2017 Programador
Uma versão do programador do SQL Server 2017 com os serviços de ML para executar a análise na base de dados é fornecida na VM no R ou Python. Serviços de ML fornecem uma plataforma para desenvolver e implementar aplicações inteligentes. Pode utilizar o avançado e poderosa nestes idiomas e o número de pacotes da Comunidade para criar modelos e gerar predições para os seus dados do SQL Server. Pode manter análise próximo os dados porque os serviços de ML (na base de dados) integra-se o R e Python idioma dentro do SQL Server. Isto elimina os custos e riscos de segurança associados movimento de dados.

> [!NOTE]
> A edição do SQL Server para programadores só pode ser utilizada para desenvolvimento e fins de teste. Precisa de uma licença para executá-la na produção. 
> 
> 

Pode aceder ao SQL server, iniciando **SQL Server Management Studio**. O nome da VM é preenchido como o nome do servidor. Utilize a autenticação do Windows quando a sessão iniciada como administrador no Windows. Assim que estiver no SQL Server Management Studio, pode criar outros utilizadores, criar bases de dados, importar dados e executar consultas do SQL Server. 

Para ativar a análise na base de dados utilizando os serviços de ML SQL, execute o seguinte comando, como uma ação no SQL Server management studio após iniciar sessão como o administrador do servidor de tempo. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Várias ferramentas do Azure são instaladas na VM:

* Não há um atalho do ambiente de trabalho para aceder à documentação do SDK do Azure. 
* **AzCopy**: utilizado para mover dados e para a sua conta de armazenamento do Microsoft Azure. Para ver a utilização, escreva **Azcopy** numa linha de comandos para ver a utilização. 
* **Explorador de armazenamento do Microsoft Azure**: utilizado para percorrer os objetos que tem armazenados nos sua conta do Storage do Azure e a transferência de dados e de armazenamento do Azure. Pode escrever **Explorador de armazenamento** na pesquisa ou encontrá-lo no menu Iniciar do Windows para aceder a esta ferramenta. 
* **Adlcopy**: utilizado para mover dados para o Azure Data Lake. Para ver a utilização, escreva **adlcopy** numa linha de comandos. 
* **dtui**: utilizada para mover dados para e da BD do Cosmos do Azure, uma base de dados NoSQL na nuvem. Tipo **dtui** na linha de comandos. 
* **Runtime de integração de fábrica de dados do Azure**: permite que o movimento de dados entre origens de dados no local e na nuvem. Utilizada dentro de ferramentas, como o Azure Data Factory. 
* **Microsoft Azure Powershell**: uma ferramenta utilizada para administrar os recursos do Azure no Powershell linguagem de script também é instalada na VM. 

### <a name="power-bi"></a>Power BI
Para ajudar a criar dashboards e excelente visualizações, o **Power BI Desktop** foi instalado. Utilize esta ferramenta para retirar dados de diferentes origens, para criar os seus dashboards e relatórios e publicá-los para a nuvem. Para informações, consulte o [Power BI](http://powerbi.microsoft.com) site. Pode encontrar o ambiente de trabalho do Power BI no menu Iniciar. 

> [!NOTE]
> Necessita de uma conta do Office 365 para aceder ao Power BI. 
> 
> 

## <a name="additional-microsoft-development-tools"></a>Ferramentas de desenvolvimento Microsoft adicionais
O [ **instalador de plataforma Web Microsoft** ](https://www.microsoft.com/web/downloads/platform.aspx) pode ser utilizado para detetar e transferir a outras ferramentas de desenvolvimento Microsoft. Também é um atalho para a ferramenta fornecido no ambiente de trabalho de máquinas de virtuais de ciência de dados de Microsoft.  

## <a name="important-directories-on-the-vm"></a>Diretórios importantes na VM
| Item | Diretório |
| --- | --- |
| Configurações de servidor de bloco de notas do Jupyter |C:\ProgramData\jupyter |
| Diretório de raiz de amostras de notas do Jupyter |c:\dsvm\notebooks |
| Outros exemplos |c:\dsvm\samples |
| Anaconda (predefinição: Python 2.7) |c:\Anaconda |
| Ambiente de Python 3.5 de anaconda |c:\Anaconda\envs\py35 |
| Microsoft ML Server autónomo Python  | C:\Programas\Microsoft Files\Microsoft\ML Server\PYTHON_SERVER |
| Instância de predefinida R (ML servidor autónomo) |C:\Programas\Microsoft Files\Microsoft\ML Server\R_SERVER |
| Diretório de instância de na base de dados de serviços de ML do SQL Server |C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Workbench de aprendizagem máquina do Azure (por utilizador) | %localappdata%\amlworkbench | 
| Diversas ferramentas |c:\dsvm\tools |

> [!NOTE]
> Instâncias da Microsoft dados ciência de Máquina Virtual criada antes 1.5.0 (antes de 3 de Setembro de 2016) utilizado uma estrutura de diretório ligeiramente diferente ao que foi especificado na tabela anterior. 
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Seguem-se alguns passos seguintes para continuar a exploração e de aprendizagem. 

* Explorar as várias ferramentas de ciência de dados de ciência de dados VM clicando no menu de início e a saída as ferramentas de verificação listado no menu.
* Saiba mais sobre serviços do Azure Machine Learning e Workbench, visitando o produto [página início rápido e tutoriais](https://docs.microsoft.com/azure/machine-learning/preview/). 
* Navegue para **c:\Programas\Microsoft Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** para exemplos que utilizam a biblioteca de RevoScaleR do R suporta análise de dados à escala da empresa.  
* Leia o artigo: [10 coisas que pode fazer na ciência de dados de Máquina Virtual](http://aka.ms/dsvmtenthings)
* Aprenda a criar soluções analíticas ponto a ponto sistematicamente utilizando o [o processo de ciência de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visite o [galeria do Azure Machine Learning](http://gallery.cortanaintelligence.com) machine learning e dados de análise exemplos que utilizam o Azure Machine learning e os dados relacionados de serviços no Azure. Também fornecemos um ícone no **iniciar** menu e no ambiente de trabalho da máquina virtual para esta galeria.

