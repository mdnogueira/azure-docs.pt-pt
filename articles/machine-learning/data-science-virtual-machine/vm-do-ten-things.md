---
title: "Dez coisas, pode fazê-lo no máquina de Virtual de ciência de dados no Azure | Microsoft Docs"
description: "Efetue várias exploração de dados e a tarefa de modelação na ciência de dados de Máquina Virtual."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: gokuma;weig;bradsev
ms.openlocfilehash: 9aafd6300bebbb5505aa4ed707cac9716c8e6aa1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>Dez coisas que pode fazer na Máquina Virtual Data Science

A Máquina Virtual de ciência de dados Microsoft (DSVM) é um ambiente de desenvolvimento de ciência de dados de elevado desempenho que lhe permite efetuar várias tarefas de exploração e modelação de dados. O ambiente inclui já criados e integrados em com várias ferramentas de análise de dados populares que facilitam a começar a trabalhar rapidamente com o Analysis Services no local, as implementações de nuvem ou híbrida. O DSVM funciona diretamente com vários serviços do Azure e é capaz de ler e processar os dados que já estão armazenados no Azure, no Azure SQL Data Warehouse, o Azure Data Lake, o Storage do Azure, ou do BD Azure Cosmos. Também pode tirar partido outras ferramentas de análise, como o Azure Machine Learning e o Azure Data Factory.

Neste artigo iremos guiá-lo como utilizar o seu DSVM para efetuar várias tarefas de ciência de dados e interagir com outros serviços do Azure. Seguem-se algumas das ações que pode fazer no DSVM:

1. Explore os dados e desenvolver modelos localmente no DSVM utilizar o Microsoft Server de R, Python
2. Utilizar um bloco de notas do Jupyter para experimentar os dados num browser com o Python 2, 3 do Python, Microsoft R uma versão preparada enterprise de R concebida para escalabilidade e desempenho
3. Operacionalizar modelos incorporados a utilizar o R e Python no Azure Machine Learning para que aplicações cliente podem aceder seus modelos utilizando uma interface de serviços web simples
4. Administrar os recursos do Azure através do portal do Azure ou o Powershell
5. Expandir o seu espaço de armazenamento e partilha conjuntos de dados em grande escala / code entre a sua equipa de toda, criando um File storage do Azure como uma unidade mountable no seu DSVM
6. Partilhe o código com a sua equipa utilizando GitHub e o repositório utilizando os pré-instaladas Git clientes - Git Bash, Git GUI de acesso.
7. Aceder aos vários serviços do Azure e análise de dados como um armazenamento de Blobs do Azure, Azure Data Lake do Azure HDInsight (Hadoop), a BD do Cosmos do Azure, Azure SQL Data Warehouse & bases de dados
8. Crie relatórios e o dashboard através do Power BI Desktop previamente instalado o DSVM e implementá-las na nuvem
9. Dimensionar dinamicamente a sua DSVM para satisfazer as necessidades de projeto
10. Instalar ferramentas adicionais na sua máquina virtual   

> [!NOTE]
> São aplicáveis encargos de utilização adicional para muitos os serviços de armazenamento e análise de dados adicionais apresentados neste artigo. Consulte o [preços do Azure](https://azure.microsoft.com/pricing/) página para obter mais detalhes.
> 
> 

**Pré-requisitos**

* Necessita de uma subscrição do Azure. Pode inscrever-se numa avaliação gratuita [aqui](https://azure.microsoft.com/free/).
* Estão disponíveis instruções para o aprovisionamento de uma Máquina Virtual de ciência de dados no portal do Azure em [criar uma máquina virtual](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-r-server-or-python"></a>1. Explore os dados e desenvolver modelos utilizando o servidor do Microsoft R ou Python
É possível utilizar idiomas, como o R e Python para efetuar a análise de dados à direita no DSVM.

R, pode utilizar um IDE chamado "Rotações R Enterprise 8.0", que pode ser encontrado no menu Iniciar ou o ambiente de trabalho. Microsoft forneceu bibliotecas adicionais sobre o abra origem/CRAN-R para ativar a análise dimensionável e a capacidade para analisar dados de maiores do que o tamanho de memória permitido efetuando paralelas analysis fragmentada. Também pode instalar um IDE de R do seu como opção [RStudio](https://www.rstudio.com/products/rstudio-desktop/).

Para o Python, pode utilizar um IDE como edição de Comunidade do Studio Visual que tem as ferramentas do Python para a extensão do Visual Studio (PTVS) pré-instaladas. Por predefinição, apenas um Python 2.7 básico está configurada no PTVS (sem qualquer biblioteca de análise como SciKit, Pandas). Para ativar o Anaconda Python 2.7 e 3.5, precisa de fazer o seguinte:

* Criar ambientes personalizadas para cada versão, navegando até **ferramentas** -> **ferramentas do Python** -> **ambientes do Python** e, em seguida, clicando em " **+ Personalizada**"na edição de Comunidade de 2015 Visual do Studio
* Forneça uma descrição e defina o ambiente de caminhos de prefixo como *c:\anaconda* para Anaconda Python 2.7 ou *c:\anaconda\envs\py35* para Anaconda Python 3.5
* Clique em **de deteção automática** e, em seguida, **aplicar** para guardar o ambiente.

Eis o aspeto a configuração de ambiente personalizadas no Visual Studio.

![Configuração das PTVS](./media/vm-do-ten-things/PTVSSetup.png)

Consulte o [documentação das PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obter detalhes adicionais sobre como criar ambientes do Python.

Agora que é configuradas para criar um novo projeto de Python. Navegue para **ficheiro** -> **novo** -> **projeto** -> **Python** e selecione o tipo de Aplicação de Python que está a criar. Pode configurar o ambiente do Python para o projeto atual para a versão pretendida (Anaconda 2.7 ou 3.5): clique com botão direito do **ambiente Python**, selecione **ambientes do Python Adicionar/remover**e, em seguida, selecione o ambiente pretendido para associar o projeto. Pode encontrar mais informações sobre como trabalhar com a PTVS no produto [documentação](https://github.com/Microsoft/PTVS/wiki) página.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Utilizar um bloco de notas do Jupyter para explorar e modelar os seus dados com o Python ou R
O bloco de notas do Jupyter é um ambiente poderoso que fornece um baseada no browser "IDE" de exploração de dados e de modelação. Pode utilizar o Python 2, Python 3 ou R (Open Source e o servidor do Microsoft R) no bloco de notas do Jupyter.

Para iniciar o clique do bloco de notas do Jupyter no ícone de menu de início / ícone de ambiente de trabalho intitulada **bloco de notas do Jupyter**. No DSVM também pode navegar para "https://localhost:9999 /" para o bloco de notas Júpiter de acesso. Se o lhe solicita uma palavra-passe, utilize instruções fornecidas a ***como criar uma palavra-passe segura no servidor de bloco de notas do Jupyter*** secção o [aprovisionar a máquina de Virtual de ciência de dados do Microsoft](provision-vm.md) tópico para criar uma palavra-passe segura para aceder ao bloco de notas do Jupyter. 

Depois de ter de abrir o bloco de notas, deverá ver um diretório que contém alguns blocos de notas de exemplo que são pré-empacotadas para o DSVM. Agora, pode:

* Clique no bloco de notas para ver o código.
* executar cada célula premindo **SHIFT-INTRODUZA**.
* executar o bloco de notas completo clicando no **célula** -> **executar**
* criar um novo bloco de notas, clicando no ícone do Jupyter (canto superior esquerdo) e, em seguida, clicar em **novo** botão à direita e, em seguida, escolha o idioma de bloco de notas (também conhecido como kernels).   

> [!NOTE]
> Atualmente suportamos o Python 2.7, Python 3.5 e R. O kernel do R suporta programação de Open source para R, bem como a empresa dimensionável Microsoft R Server.   
> 
> 

Assim que estiver no bloco de notas pode explorar os dados, criar o modelo, testar o modelo utilizando a sua escolha de bibliotecas.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Criar modelos utilizando o R ou Python e Operationalize-los através do Azure Machine Learning
Depois de ter criado e validar o seu modelo o passo seguinte é normalmente implementá-la em produção. Isto permite que o cliente aplicações invocar as predições modelo um em tempo real ou numa base de modo de batch. O Azure Machine Learning fornece um mecanismo para operacionalizar um modelo incorporado no R ou Python.

Quando operacionalizar o seu modelo no Azure Machine Learning, um serviço web está exposto que permite aos clientes efetuar chamadas REST que transmita os parâmetros de entrada e receberem predições do modelo como saídas.   

> [!NOTE]
> Caso tenha não ainda efetuado a cópia de segurança do Azure Machine Learning, pode obter uma área de trabalho gratuita ou uma área de trabalho padrão, visitando o [Azure Machine Learning Studio](https://studio.azureml.net/) home page e clicando no "Introdução".   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Compilação e Python Operacionalizar modelos
Eis um fragmento de código desenvolvido num bloco de notas do Jupyter Python que cria um modelo simple utilizando a biblioteca de saber de SciKit.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

O método utilizado para implementar os modelos do python para o Azure Machine Learning encapsula num wrapper a predição de modelo para uma função e decorates-lo com os atributos fornecidos pela biblioteca de python do Azure Machine Learning pré-instaladas com referência à sua do Azure Machine Learning ID da área de trabalho, chave de API e os parâmetros de entrada e de retorno.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Um cliente pode agora efetuar chamadas para o serviço web. Existem wrappers de conveniência que construir os pedidos de REST API. Eis um código de exemplo para consumir o serviço web.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Biblioteca do Azure Machine Learning só é suportada atualmente no Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Compilação e R Operacionalizar modelos
Pode implementar modelos de R incorporados na máquina de Virtual de ciência de dados ou noutro local no Azure Machine Learning de forma semelhante à forma como é efetuada para o Python. Obtenção são os passos:

* criar um ficheiro de settings.json para fornecer a sua área de trabalho ID e a autenticação de token conforme mostrado no seguinte exemplo de código.
* Escreva um wrapper para a função de prever o modelo.
* chamar ```publishWebService``` na biblioteca do Azure Machine Learning para passar o wrapper de função.  

Eis os fragmentos de procedimento e código podem ser utilizados para configurar, criar, publicar e consumir um modelo como um serviço web no Azure Machine Learning.

#### <a name="setup"></a>Configurar
1. Instalar o pacote de R do Machine Learning, escrevendo ```install.packages("AzureML")``` rotações R Enterprise 8.0 IDE ou do IDE do R.
2. Transferir RTools de [aqui](https://cran.r-project.org/bin/windows/Rtools/). É necessário o utilitário de zip no caminho (e zip.exe nomeado) para operacionalizar o seu pacote de R no Machine Learning.
3. Criar um ficheiro de settings.json sob um diretório denominado ```.azureml``` sob o diretório raiz e introduza os parâmetros da sua área de trabalho do Azure Machine Learning:

Settings.JSON estrutura de ficheiros:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Criar um modelo no R e publicá-lo no Azure Machine Learning
    library(AzureML)
    ws <- workspace(config="~/.azureml/settings.json")

    if(!require("lme4")) install.packages("lme4")
    library(lme4)
    set.seed(1)
    train <- sleepstudy[sample(nrow(sleepstudy), 120),]
    m <- lm(Reaction ~ Days + Subject, data = train)

    # Define a prediction function to publish based on the model:
    sleepyPredict <- function(newdata){
          predict(m, newdata=newdata)
    }

    ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Consumir o modelo de implementação no Azure Machine Learning
Para consumir o modelo a partir de uma aplicação de cliente, utilizamos a biblioteca do Azure Machine Learning para procurar o serviço web publicadas através do nome de `services` chamada da API para determinar o ponto final. Em seguida, chame somente o `consume` funcionar e passar na moldura de dados para possível prever.
O seguinte código é utilizado para consumir o modelo publicado como um serviço web Azure Machine Learning.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Podem encontrar mais informações sobre a biblioteca do Azure Machine Learning R [aqui](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Administrar os recursos do Azure através do portal do Azure ou o Powershell
O DSVM não só permite-lhe tirar partido da sua solução de análise localmente a máquina virtual, mas também lhe permite aceder aos serviços em nuvem do Azure da Microsoft. O Azure oferece vários computação, armazenamento, os serviços de análise de dados e outros serviços que podem administrar e aceder a partir do seu DSVM.

Para administrar os recursos subscrição e na nuvem do Azure pode utilizar o browser e aponte para o [portal do Azure](https://portal.azure.com). Também pode utilizar o Azure Powershell para administrar a sua subscrição do Azure e os recursos através de um script.
Pode executar o Azure Powershell a partir de um atalho no ambiente de trabalho ou a partir do menu Iniciar intitulada "Microsoft Azure Powershell". Consulte [documentação do Microsoft Azure Powershell](../../powershell-azure-resource-manager.md) para obter mais informações sobre como pode administrar a subscrição do Azure e os recursos através de scripts do Windows Powershell.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Expandir o seu espaço de armazenamento com um sistema de ficheiros partilhados
Cientistas de dados podem partilhar grandes conjuntos de dados, código ou outros recursos dentro da equipa. DSVM próprio tem cerca de 70GB de espaço disponível. Para expandir o armazenamento, pode utilizar o serviço de ficheiros do Azure e um montá-la a DSVM ou aceder ao mesmo através de uma API REST.   

> [!NOTE]
> O espaço máximo da partilha do serviço de ficheiros do Azure é de 5TB e limite de tamanho de ficheiro individual é 1TB.   
> 
> 

Pode utilizar o Azure Powershell para criar uma partilha de serviço de ficheiros do Azure. Eis o script para ser executada no Azure PowerShell para criar uma partilha de ficheiros do Azure de serviço.

    # Authenticate to Azure.
    Login-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Agora que criou uma partilha de ficheiros do Azure, possível montá-la em qualquer máquina virtual no Azure. Recomenda-se vivamente que a VM se encontra no mesmo centro de dados do Azure como a conta de armazenamento para evitar custos de transferência de dados e latência. Seguem-se os comandos para montar a unidade no DSVM que podem ser executados no Azure Powershell.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Agora pode aceder a esta unidade como faria com qualquer unidade normal na VM.

## <a name="6-share-code-with-your-team-using-github"></a>6. Código de partilha com a sua equipa utilizando GitHub
GitHub é um repositório de código, onde poderá encontrar uma grande quantidade de código de exemplo e as origens de diversas ferramentas utilizando várias tecnologias partilhadas pela Comunidade de programadores. Utiliza Git como a tecnologia de controlar e armazenar versões dos ficheiros de código. GitHub também é uma plataforma de onde pode criar o seus próprios repositório para armazenar o código partilhado e a documentação da sua equipa, implementar o controlo de versão e também controlar quem tem acesso para ver e contribuir código. Visite o [páginas de ajuda do GitHub](https://help.github.com/) para obter mais informações sobre como utilizar o Git. Pode utilizar o GitHub como uma das formas para colaborar com a sua equipa, utilize o código desenvolvido pela Comunidade e código a contribuir para a Comunidade.

O DSVM é fornecido carregado com ferramentas de cliente da linha de comandos como bem GUI para aceder ao repositório do GitHub. A ferramenta da linha de comandos para trabalhar com o Git e do GitHub é denominada o Git Bash. Visual Studio instalado no DSVM tem as extensões de Git. Pode encontrar os ícones de arranque para estas ferramentas no menu Iniciar e o ambiente de trabalho.

Para transferir o código a partir de um repositório do GitHub que utiliza o ```git clone``` comando. Por exemplo para transferir o repositório de ciência de dados publicado pela Microsoft para o diretório atual pode executar o comando seguinte assim que estiver no ```git-bash```.

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

No Visual Studio, pode efetuar a mesma operação de clonagem. A seguinte captura de ecrã mostra como aceder às ferramentas Git e GitHub no Visual Studio.

![Git no Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Pode encontrar mais informações sobre como utilizar o Git para trabalhar com o seu repositório de GitHub a partir de vários recursos disponíveis no github.com. O [cábula](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) é uma referência de útil.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Aceder a diversos serviços de dados e análise do Azure
### <a name="azure-blob"></a>Blob do Azure
Blob do Azure é um armazenamento de nuvem fiável e económica para dados de grandes e em pequenos. Informe-nos analisar como pode mover dados para o Blob do Azure e aceder a dados armazenados num Blob do Azure.

**Pré-requisito**

* **Criar a sua conta de armazenamento de Blobs do Azure de [portal do Azure](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Confirme que a ferramenta de linha de comandos AzCopy pré-instaladas encontra-se em ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Pode adicionar o diretório que contém o azcopy.exe para a variável de ambiente PATH para evitar escrever o caminho de comando completo, ao executar esta ferramenta. Para obter mais informações sobre a ferramenta do AzCopy consulte [documentação do AzCopy](../../storage/common/storage-use-azcopy.md)
* Inicie a ferramenta Explorador de armazenamento do Azure. Pode ser transferido a partir de [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Mover dados de VM para o Blob do Azure: AzCopy**

Para mover dados entre os ficheiros locais e o armazenamento de BLOBs, pode utilizar o AzCopy na linha de comandos ou PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Substitua **C:\myfolder** para o caminho onde o ficheiro está armazenado, **mystorageaccount** ao seu nome de conta de armazenamento de BLOBs, **mycontainer** para o nome do contentor, **chave da conta de armazenamento** a sua chave de acesso de armazenamento de Blobs. Pode encontrar as credenciais da conta de armazenamento no [portal do Azure](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Execute o comando do AzCopy no PowerShell ou numa linha de comandos. Eis algumas exemplo de utilização do comando do AzCopy:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Depois de executar o comando do AzCopy para copiar para um blob do Azure, consulte o artigo mostra o ficheiro cópias de segurança no Explorador de armazenamento do Azure em breve.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Mover dados de VM para o Blob do Azure: Explorador de armazenamento do Azure**

Também pode carregar dados a partir do ficheiro local na VM através do Explorador de armazenamento do Azure:

* Carregar dados para um contentor, selecione o contentor de destino e clique em de **carregar** botão.![ Carregar no Explorador de armazenamento](./media/vm-do-ten-things/storage-accounts.png)
* Clique em de **...**  à direita do **ficheiros** caixa, selecione um ou vários ficheiros para carregar a partir do sistema de ficheiros e clique em **carregar** para iniciar o carregamento de ficheiros.![ Carregar ficheiros para o blob](./media/vm-do-ten-things/upload-files-to-blob.png)

**Ler dados de Blobs do Azure: módulo leitor de Machine Learning**

No Azure Machine Learning Studio, pode utilizar um **módulo importar dados** ler dados a partir do blob.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Ler dados de Blobs do Azure: Python ODBC**

Pode utilizar **BlobService** biblioteca para ler os dados diretamente a partir do blob no programa de um bloco de notas do Jupyter ou Python.

Em primeiro lugar, importe pacotes necessários:

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random

Em seguida, ligue as credenciais de conta do Blob do Azure e ler dados a partir do Blob:

    CONTAINERNAME = 'xxx'
    STORAGEACCOUNTNAME = 'xxxx'
    STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
    BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
    localfilename = 'trip_data_1.csv'
    LOCALDIRECTORY = os.getcwd()
    LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

    #download from blob
    t1 = time.time()
    blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
    t2 = time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

Os dados é lida como um intervalo de dados:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Armazenamento do Data Lake do Azure é um repositório de hiper escala para cargas de trabalho de análise de macrodados e compatível com distribuído ficheiro sistema Hadoop (HDFS). Funciona com o ecossistema do Hadoop e o Azure Data Lake Analytics. Vamos mostrar como pode mover dados para o Azure Data Lake Store e executar a análise a utilizar o Azure Data Lake Analytics.

**Pré-requisito**

* Criar o Azure Data Lake Analytics no [portal do Azure](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* O **ferramentas do Azure Data Lake** no **Visual Studio** encontrada neste [ligação](https://www.microsoft.com/download/details.aspx?id=49504) já está instalado o Visual Studio Community edição que se encontre na máquina virtual. Depois de iniciar o Visual Studio e o registo na sua subscrição do Azure, deverá ver a sua conta de análise de dados do Azure e o armazenamento no painel da esquerda do Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Mover dados de VM para o Data Lake: Explorador do Azure Data Lake**

Pode utilizar **Explorador do Azure Data Lake** para carregar dados a partir de ficheiros locais na sua máquina Virtual para o armazenamento do Data Lake.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Também pode criar um pipeline de dados para productionize o movimento de dados de ou para utilizar o Azure Data Lake a [Factory(ADF) de dados do Azure](https://azure.microsoft.com/services/data-factory/). Vamos referir-se a isto [artigo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) para ajudá-lo pelos passos para criar os dados de pipelines.

**Ler dados de Blobs do Azure para o Data Lake: U-SQL**

Se os dados residirem no Blob storage do Azure, podem ler diretamente dados a partir do blob storage do Azure na consulta de U-SQL. Antes de composição a consulta de U-SQL, certifique-se a que sua conta do blob storage está ligada à sua do Azure Data Lake. Aceda a **portal do Azure**, localizar o dashboard do Azure Data Lake Analytics, clique em **Adicionar origem de dados**, selecione o tipo de armazenamento a **Storage do Azure** e plug-in a sua conta do Storage do Azure O nome e chave. Em seguida, é capaz de fazer referência os dados armazenados na conta de armazenamento.

![Introduza a conta de armazenamento e a chave](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

No Visual Studio, pode ler dados a partir do blob storage, efetue algumas manipulação de dados, engenharia da funcionalidade e os dados resultantes para o Azure Data Lake ou o Blob Storage do Azure de saída. Quando referenciar os dados no armazenamento de BLOBs, utilize **wasb: / /**; quando referenciar os dados no Azure Data Lake, utilize **swbhdfs: / /**

![Intervalo de dados](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Pode utilizar as seguintes consultas de U-SQL no Visual Studio:

    @a =
        EXTRACT medallion string,
                hack_license string,
                vendor_id string,
                rate_code string,
                store_and_fwd_flag string,
                pickup_datetime string,
                dropoff_datetime string,
                passenger_count int,
                trip_time_in_secs double,
                trip_distance double,
                pickup_longitude string,
                pickup_latitude string,
                dropoff_longitude string,
                dropoff_latitude string

        FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
        USING Extractors.Csv();

    @b =
        SELECT vendor_id,
        COUNT(medallion) AS cnt_medallion,
        SUM(passenger_count) AS cnt_passenger,
        AVG(trip_distance) AS avg_trip_dist,
        MIN(trip_distance) AS min_trip_dist,
        MAX(trip_distance) AS max_trip_dist,
        AVG(trip_time_in_secs) AS avg_trip_time
        FROM @a
        GROUP BY vendor_id;

    OUTPUT @b   
    TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
    USING Outputters.Csv();

    OUTPUT @b   
    TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
    USING Outputters.Csv();



Depois da consulta é submetida para o servidor, é apresentado um diagrama que mostra o estado da tarefa.

![Diagrama de estado de tarefa](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Consultar dados no Data Lake: U-SQL**

Depois do conjunto de dados é ingerido no Azure Data Lake, pode utilizar [linguagem U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) para consultar e explorar os dados. Linguagem U-SQL é semelhante a T-SQL, mas combina algumas funcionalidades do c# para que os utilizadores podem escrever módulos personalizados, funções definidas pelo utilizador e etc. Pode utilizar os scripts no passo anterior.

Depois da consulta foi submetida para o servidor, tripdata_summary. CSV pode ser encontrado em breve no **Explorador do Azure Data Lake**, poderá pré-visualizar os dados pelo contexto o ficheiro.

![Ficheiro no Explorador do Azure Data Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Para ver as informações de ficheiro:

![Resumo de ficheiro](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clusters do Hadoop HDInsight
O Azure HDInsight é um serviço gerido do Apache Hadoop, Spark, HBase e Storm na nuvem. Pode trabalhar facilmente com clusters do HDInsight do Azure da máquina virtual de ciência de dados.

**Pré-requisito**

* Criar a sua conta de armazenamento de Blobs do Azure de [portal do Azure](https://portal.azure.com). Esta conta de armazenamento é utilizada para armazenar dados para os clusters do HDInsight.

![Criar conta de armazenamento de Blobs do Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personalizar Clusters do Hadoop HDInsight Azure do [portal do Azure](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Tem de associar a conta de armazenamento criada com o cluster do HDInsight quando é criado. Esta conta de armazenamento é utilizada para aceder aos dados que podem ser processados no cluster.

![Ligar à conta de armazenamento criada com o cluster do HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Tem de ativar **acesso remoto** ao nó principal do cluster depois de criado. Lembrar as credenciais de acesso remoto que especificar aqui (diferentes das especificadas para o cluster durante a criação do respetivo): precisar no procedimento anterior.

![Ativar o acesso remoto](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Crie uma área de trabalho do Azure Machine Learning. Das suas experimentações do Machine Learning são armazenadas nesta área de trabalho do Machine Learning. Selecione as opções de realçados no Portal, conforme mostrado na captura de ecrã seguinte:

![Criar um espaço de trabalho do Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Em seguida, introduza os parâmetros para a sua área de trabalho

![Introduza os parâmetros de área de trabalho do Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Carregar os dados utilizando IPython bloco de notas. Primeiro importar pacotes necessários, plug-in credenciais, criar uma base de dados na sua conta de armazenamento, em seguida, carregar dados para os clusters do HDI.

        #Import required Packages
        import pyodbc
        import time as time
        import json
        import os
        import urllib
        import urllib2
        import warnings
        import re
        import pandas as pd
        import matplotlib.pyplot as plt
        from azure.storage.blob import BlobService
        warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
        queryString = "create database if not exists nyctaxidb;"
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.trip
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            rate_code string,
                            store_and_fwd_flag string,
                            pickup_datetime string,
                            dropoff_datetime string,
                            passenger_count int,
                            trip_time_in_secs double,
                            trip_distance double,
                            pickup_longitude double,
                            pickup_latitude double,
                            dropoff_longitude double,
                            dropoff_latitude double)  
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.fare
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            pickup_datetime string,
                            payment_type string,
                            fare_amount double,
                            surcharge double,
                            mta_tax double,
                            tip_amount double,
                            tolls_amount double,
                            total_amount double)
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* Em alternativa, pode seguir este [instruções](../team-data-science-process/hive-walkthrough.md) carregar dados de NYC Taxi para HDI cluster. Os principais passos incluem:
  
  * AzCopy: transferência zipado do CSV a partir do blob público para a pasta local
  * AzCopy: carregar deszipadas CSVS da pasta local para o HDI cluster
  * Inicie sessão no nó principal do cluster do Hadoop e preparar para análises exploratórias dados

Depois dos dados são carregados para o HDI cluster, pode verificar os dados no Explorador de armazenamento do Azure. Sendo uma nyctaxidb de base de dados criado no HDI cluster.

**Exploração de dados: consultas do Hive no Python**

Uma vez que os dados num cluster de Hadoop, pode utilizar o pacote de pyodbc para ligar a Clusters do Hadoop e a consulta de base de dados utilizando o Hive exploração e engenharia da funcionalidade. Pode ver as tabelas existentes que criou no passo pré-requisitos.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Ver as tabelas existentes](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Vamos ver o número de registos em cada mês e as frequências dos tipped ou não se encontra na tabela viagem:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Desenho de número de registos em cada mês](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![Desenho de frequências de sugestão](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Iremos pode também a distância entre a localização de recolha e a localização de dropoff de computação e, em seguida, compare-as com a distância viagem.

    queryString = """
                    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                        *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                        pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                        from nyctaxidb.trip
                        where month=1
                            and pickup_longitude between -90 and -30
                            and pickup_latitude between 30 and 90
                            and dropoff_longitude between -90 and -30
                            and dropoff_latitude between 30 and 90;
                """
    results = pd.read_sql(queryString,connection)
    results.head(5)


![Tabela de recolha e dropoff](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Desenho da distância de recolha/dropoff à distância viagem](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Agora vamos preparar um conjunto de (1%) amostragem por baixo de dados para a modelação. Podemos utilizar estes dados no módulo de leitor de Machine Learning.

        queryString = """
        create  table if not exists nyctaxi_downsampled_dataset_testNEW (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\\n'
        stored as textfile;
        """
        cursor.execute(queryString)

        --- now insert contents of the join into the preceding internal table

        queryString = """
        insert overwrite table nyctaxi_downsampled_dataset_testNEW
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
        rand() as sample_key

        from trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01
        """
        cursor.execute(queryString)

Ao fim de algum, pode ver que os dados terem sido carregados nos clusters do Hadoop:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Tabela de dados](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Ler dados a partir do HDI com Machine Learning: módulo leitor**

Também pode utilizar o **leitor** módulo no Machine Learning Studio para aceder à base de dados no cluster do Hadoop. Plug-in as credenciais da sua HDI clusters e a conta de armazenamento do Azure para ativar a compilação Utilize modelos de machine learning utilizar base de dados em clusters HDI.

![Propriedades do módulo leitor](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

O conjunto de dados classificado pode depois ser visualizado:

![Ver o conjunto de dados classificado](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse & bases de dados
O Azure SQL Data Warehouse é um armazém de dados elásticas como um serviço com a experiência de SQL Server de classe empresarial.

Pode aprovisionar o Azure SQL Data Warehouse, seguindo as instruções fornecidas neste [artigo](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Depois de aprovisionar o seu armazém de dados SQL do Azure, pode utilizá-lo [instruções](../team-data-science-process/sqldw-walkthrough.md) para efetuar o carregamento de dados, exploração e modelação através de dados no armazém de dados do SQL Server.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
BD do Cosmos do Azure é uma base de dados NoSQL na nuvem. Este permite-lhe trabalhar com documentos como JSON e permite-lhe armazenar e consultar documentos.

Terá de realizar os seguintes passos por requisitos para aceder à base de dados do Azure Cosmos do DSVM.

1. Instalar o SDK Python do DocumentDB (executar ```pip install pydocumentdb``` da linha de comandos)
2. Criar uma conta de base de dados do Azure Cosmos e bases de dados de [portal do Azure](https://portal.azure.com)
3. Transferir "Azure Cosmos DB ferramenta de migração" do [aqui](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) e extrair para um diretório da sua preferência
4. Importar dados JSON (dados volcano) armazenados num [blob público](https://cahandson.blob.core.windows.net/samples/volcano.json) para Cosmos base de dados com os seguintes parâmetros de comando para a ferramenta de migração (dtui.exe do diretório onde instalou a ferramenta de migração de BD do Cosmos). Introduza a localização de origem e de destino com os seguintes parâmetros:
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/; AccountKey = [[chave]; base de dados = volcano /t.Collection:volcano1

Depois de importar os dados, pode ir para Jupyter e abra o bloco de notas intitulado *DocumentDBSample* que contém o código do python para aceder ao DocumentDB e efetue algumas consultas básicas. Pode saber mais sobre a base de dados do Cosmos, visitando o serviço [página de documentação](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Crie relatórios e o dashboard através do Power BI Desktop
Informe-na visualizar o ficheiro de Volcano JSON que vimos no exemplo anterior do Cosmos DB no Power BI para obter informações acerca visual os dados. Os passos detalhados estão disponíveis no [artigo do Power BI](../../cosmos-db/powerbi-visualize.md). Eis os passos de alto nível:

1. Abra o ambiente de trabalho do Power BI e fazer "Obter dados". Especifique o URL como: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Deverá ver os registos JSON importados como uma lista
3. Converter a lista de uma tabela para que Power BI possa trabalhar com o mesmo
4. Expanda as colunas clicando no ícone de expansão (aquele com o ícone de "seta para a esquerda e seta para a direita uma" à direita da coluna)
5. Repare que a localização é um campo de "Registo". Expanda o registo e selecione apenas as coordenadas. Coordenada é uma coluna de lista
6. Adicionar uma nova coluna ao converter a coluna de coordenada lista numa vírgula separada LatLong coluna concatenar dois elementos no campo de lista coordenada utilizando a fórmula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Por fim, converter o ```Elevation``` coluna Decimal e selecione o **fechar** e **aplicar**.

Em vez de precedente passos, pode colar o seguinte código que os scripts de fora os passos utilizados no Editor de avançadas no Power BI que permite-lhe escrever transformações de dados num idioma de consulta.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Tem agora os dados no seu modelo de dados do Power BI. O ambiente de trabalho do Power BI deverá aparecer da seguinte forma:

![Ambiente de trabalho do Power BI](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Pode iniciar a criação de relatórios e visualizações utilizando o modelo de dados. Pode seguir os passos neste [artigo do Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) para criar um relatório. O resultado final é um relatório que o seguinte aspeto.

![Power BI Desktop relatório vista - conector do Power BI](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dimensionar dinamicamente a sua DSVM para satisfazer as necessidades de projeto
Pode dimensionar para cima e baixo DSVM para satisfazer as necessidades de projeto. Se não precisa de utilizar a VM no evening ou fins de semana, pode apenas encerrar VM a partir do [portal do Azure](https://portal.azure.com).

> [!NOTE]
> Pode implicar custos de computação, se utilizar apenas o botão Encerrar do sistema operativo na VM.  
> 
> 

Se precisar de processar algumas análise em grande escala e precisar de mais capacidade de CPU e/ou de memória e/ou de disco, pode encontrar uma escolha grande de tamanhos de VM em termos de núcleos CPU, a capacidade de memória e os tipos de disco (incluindo unidades de estado sólido) que satisfazem as necessidades budgetary e computação. A lista completa das VMs, juntamente com os respetivos preço por hora de computação está disponível na [preços de máquinas virtuais do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) página.

Da mesma forma, se reduz a necessidade de capacidade de processamento de VM (por exemplo: mover uma carga de trabalho principal para um Hadoop ou um cluster do Spark), pode reduzir verticalmente o cluster a partir de [portal do Azure](https://portal.azure.com) e as definições da sua instância VM. Eis uma captura de ecrã.

![Definições de instância VM](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Instalar ferramentas adicionais na sua máquina virtual
Podemos ter empacotada várias ferramentas acreditamos que conseguem para endereço muitas das necessidades de análise de dados comuns e que deve poupar tempo, evitando terem de instalar e configurar os seus ambientes um por um e poupar dinheiro por pagar apenas para os recursos que utilizar.

Pode tirar partido dos outros serviços de dados e análise do Azure profiled neste artigo para melhorar o seu ambiente de análise. Compreendemos que em alguns casos às suas necessidades podem necessitar de ferramentas adicionais, incluindo algumas ferramentas de terceiros proprietárias. Ter acesso administrativo total na máquina virtual para instalar novas ferramentas que precisa. Também pode instalar pacotes adicionais no Python e R não estão instalados previamente. Para Python pode utilizar tanto ```conda``` ou ```pip```. Para R, pode utilizar o ```install.packages()``` no R consola ou utilize o IDE e escolha "**pacotes** -> **instalar pacotes...** ".

## <a name="summary"></a>Resumo
Estes são apenas algumas coisas, pode fazê-lo na máquina Virtual Microsoft dados ciência. Existem inúmeros aspetos mais que pode fazer para que seja um ambiente de análise em vigor.

