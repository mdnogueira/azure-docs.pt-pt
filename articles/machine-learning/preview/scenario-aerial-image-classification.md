---
title: "Classificação de imagem com vista aérea | Microsoft Docs"
description: "Fornece instruções para o cenário do mundo real na classificação de imagem com vista aérea"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 10/27/2017
ms.openlocfilehash: cfffe5145f8762558e6ee573f6f2bb69d32424ad
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="aerial-image-classification"></a>Classificação de imagem com vista aérea

Este exemplo demonstra como utilizar o Azure Machine Learning Workbench para coordenar formação distribuída e operationalization dos modelos de classificação de imagem. Podemos utilizar duas abordagens para formação: (i) limitar uma rede neuronal profundo utilizando um [do Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) cluster GPU e (ii) utilizando o [Microsoft Machine Learning para Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) o pacote para imagens de featurize utilizando modelos CNTK pretrained e para preparar classificadores utilizando as funcionalidades derivadas. Vamos, em seguida, aplicar os modelos de formação de forma paralela para conjuntos de imagem grande em nuvem, utilizando um [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) cluster, permitindo-nos aumentar a velocidade de formação e operationalization ao adicionar ou remover nós de trabalho.

Este exemplo destaca duas abordagens para transferir learning, que tira partido do pretrained modelos para evitar os custos de redes de profundidade neuronal formação a partir do zero. Reparametrização uma rede neuronal profundo normalmente requer a computação GPU, mas pode levar a precisão maior ao conjunto de preparação é suficientemente grande. Um classificador simple nas imagens featurized de preparação não necessita de computação para a GPU, está inerentemente rápida e dimensionável arbitrariamente e enquadra menos parâmetros. Este método, por conseguinte, é uma excelente opção quando alguns exemplos de formação estão disponíveis como é muitas vezes o caso em casos de utilização personalizada. 

O cluster do Spark utilizado neste exemplo tem 40 nós de trabalho e custos ~$40/hr operar; os recursos de cluster de AI do Batch incluem oito GPUs e custo ~$10/hr operar. A concluir estas instruções demora cerca de três horas. Quando tiver terminado, siga as instruções de limpeza para remover os recursos que criou e parar a incorrer em custos.

## <a name="link-to-the-gallery-github-repository"></a>Ligação para o repositório do GitHub da Galeria

O repositório do GitHub público para este cenário do mundo real contém todos os materiais, incluindo exemplos de código, necessários para este exemplo:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Utilize a descrição do cenário

Neste cenário, vamos dar formação sobre modelos de machine learning para classificar o tipo de telefone apresentado nas imagens com vista aérea do medidor 224 x 224 medição rastreia. Os modelos de classificação de utilização do telefone podem ser utilizados para controlar urbanization, deforestation, perda de wetlands e outras principais tendências ambientais utilizando periodicamente recolhidos imagery com vista aérea. Iremos preparado formação e validação conjuntos de imagem com base no imagery dos E.U.A. National Agriculture Imagery programa e telefone utilizam etiquetas publicadas pelos E.U.A. Base de dados do telefone National abrange. Imagens de exemplo em cada classe de utilização do telefone são mostradas aqui:

![Regiões de exemplo para cada telefone utilizam etiqueta](media/scenario-aerial-image-classification/example-labels.PNG)

Depois de formação e a validar o modelo de classificador, iremos irá aplicá-la para imagens com vista aérea Middlesex County, MA – home da Microsoft em Inglaterra do novo Research & Centro de desenvolvimento (NERD) – para demonstrar como estes modelos podem ser utilizados para estudar a tendências na urbano de expansão desenvolvimento.

Para produzir um classificador de imagem a utilizar a transferência de aprendizagem, cientistas de dados, muitas vezes, construir vários modelos com uma variedade de métodos e selecione o maior performant modelo. Workbench de aprendizagem máquina do Azure pode ajudar os dados cientistas coordenam formação em ambientes de computação diferentes, controlam e comparam o desempenho dos vários modelos e aplicar um modelo que escolheu para grandes conjuntos de dados na nuvem.

## <a name="scenario-structure"></a>Estrutura do cenário

Neste exemplo, os dados de imagem e modelos pretrained são alojados numa conta de armazenamento do Azure. Um cluster do Azure HDInsight Spark lê estes ficheiros e constrói um modelo de classificação de imagem utilizando MMLSpark. O modelo treinado e respetivos predições, em seguida, são escritas para a conta do storage, onde podem ser analisados e visualizados através de um bloco de notas do Jupyter executar localmente. Azure Workbench de aprendizagem máquina coordena execução remota de scripts no cluster do Spark. É também controla as métricas de precisão para vários modelos que preparado através de vários métodos, permitindo que o utilizador selecionar mais performant modelo.

![Schematic para o cenário de mundo real de classificação de imagem com vista aérea](media/scenario-aerial-image-classification/scenario-schematic.PNG)

O [instruções passo a passo](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) comece por ao servir pelos através da criação e a preparação de uma conta de armazenamento do Azure e o cluster do Spark, incluindo a instalação de transferência e de dependência de dados. Em seguida, descrevem como iniciar tarefas de preparação e compare o desempenho dos modelos de resultantes. Por fim, estes mostram como aplicar um modelo que escolheu para um conjunto de imagem grande no cluster do Spark e analise os resultados de predição localmente.


## <a name="set-up-the-execution-environment"></a>Configurar o ambiente de execução

As seguintes instruções descrevem o processo de configuração de ambiente de execução para este exemplo.

### <a name="prerequisites"></a>Pré-requisitos
- Um [conta do Azure](https://azure.microsoft.com/en-us/free/) (gratuitas estão disponíveis)
    - Irá criar um cluster do HDInsight Spark com 40 nós de trabalho (168 núcleos no totais). Certifique-se de que a conta tem suficiente disponíveis núcleos revendo "utilização + quotas" separador para a sua subscrição no portal do Azure.
       - Se tiver menos núcleos disponíveis, pode modificar o modelo de cluster do HDInsight para reduzir o número de funcionários aprovisionado. Instruções para isto são apresentados na secção "Criar o cluster do HDInsight Spark".
    - Este exemplo cria um cluster de formação de AI do Batch, com dois NC6 (1 GPU, 6 vCPU) VMs. Certifique-se de que a conta tem suficiente núcleos disponíveis na região EUA leste, revendo "utilização + quotas" separador para a sua subscrição no portal do Azure.
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Siga o [instalar e criar o guia de introdução](quickstart-installation.md) para instalar o Workbench do Azure Machine Learning e criar a experimentação e contas de gestão de modelo.
- [Lote AI](https://github.com/Azure/BatchAI) Python SDK e o Azure CLI 2.0
    - Instalar o SDK de AI do Batch e o Azure CLI 2.0, seguindo as instruções de [secção pré-requisitos de receitas](https://github.com/Azure/BatchAI/tree/master/recipes).
        - A partir desta redação, o Workbench do Azure Machine Learning utiliza uma bifurcação separada do 2.0 de CLI do Azure. Para efeitos de clareza, denominamos versão o Workbench do CLI como "CLI iniciado a partir do Workbench de aprendizagem máquina do Azure" e a versão de lançamento geral (que inclui o Batch AI) "CLI do Azure 2.0."
    - Criar um Azure Active Directory principal de serviço e aplicação seguindo [estas instruções](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). O ID de cliente do registo, o segredo e o ID do inquilino.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), um livre utilitário para coordenar a transferência de ficheiros entre contas de armazenamento do Azure
    - Certifique-se de que a pasta que contém o executável do AzCopy na variável de ambiente PATH do sistema. (Estão disponíveis instruções sobre a modificação de variáveis de ambiente [aqui](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Um cliente SSH; Recomendamos [PuTTY](http://www.putty.org/).

Este exemplo foi testado em PCs Windows 10; poderá executá-la a partir de qualquer máquina Windows, incluindo máquinas de virtuais de ciência de dados do Azure. O 2.0 CLI do Azure foi instalado a partir de um MSI em conformidade com [estas instruções](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Pequenas alterações poderão ser necessárias (por exemplo, as alterações à filepaths) ao executar este exemplo no macOS.

### <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Este exemplo requer um cluster do HDInsight Spark e uma conta de armazenamento do Azure para ficheiros relevantes do anfitrião. Siga estas instruções para criar estes recursos num novo grupo de recursos do Azure:

#### <a name="create-a-new-workbench-project"></a>Criar um novo projeto do Workbench

Crie um novo projeto com este exemplo como um modelo:
1.  Abra máquina do Azure Learning Workbench
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "Com vista aérea de imagem de classificação" e selecione o modelo
5.  Clique em **Criar**.
 
#### <a name="create-the-resource-group"></a>Criar o grupo de recursos

1. Depois de carregar o projeto no Azure Machine Learning Workbench, abra uma Interface de linha de comandos (CLI) ao clicar em ficheiro -> Abra a linha de comandos.
    Utilize esta versão da CLI para a maioria do tutorial. (Se encontra onde indicado, é-lhe pedido para abrir outra versão da CLI para preparar os recursos do Batch AI.)

1. Da interface da linha de comandos, inicie sessão sua conta do Azure executando o seguinte comando:

    ```
    az login
    ```

    É-lhe pedido para visitar um URL e o tipo num código temporário fornecido; o Web site pedidos as credenciais da conta do Azure.
    
1. Quando o início de sessão estiver concluído, voltar para a CLI e execute o seguinte comando para determinar quais as subscrições do Azure estão disponíveis para a sua conta do Azure:

    ```
    az account list
    ```

    Este comando apresenta uma lista de todas as subscrições associadas à sua conta do Azure. Localize o ID da subscrição que pretende utilizar. O ID de subscrição de escrita se encontra onde indicado no comando seguinte, em seguida, a definir a subscrição Active Directory, executando o comando:

    ```
    az account set --subscription [subscription ID]
    ```

1. Os recursos do Azure criados neste exemplo são armazenados em conjunto num grupo de recursos do Azure. Escolha um nome de grupo de recursos exclusiva e escrevem-sempre que for indicado, em seguida, execute ambos os comandos para criar o grupo de recursos do Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Criar a conta de armazenamento

Iremos agora criar a conta de armazenamento que anfitriões projeto ficheiros que tem de ser acedidos pelo HDInsight Spark.

1. Escolha um nome de conta de armazenamento exclusivas e escrevem-se encontra onde indicado a seguir `set` comando, em seguida, criar uma conta de armazenamento do Azure ao executar os dois comandos:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Lista as chaves de conta de armazenamento, executando o seguinte comando:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Registe o valor de `key1` como a chave de armazenamento no comando seguinte, em seguida, execute o comando para armazenar o valor.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. No seu editor de texto favorito, carregar o `settings.cfg` subdiretório de "Código" para o projeto do Azure Machine Learning Workbench de ficheiro e inserir o nome da conta de armazenamento e a chave, conforme indicado. Guarde e feche o `settings.cfg` ficheiro.
1. Se ainda não o fez, transfira e instale o [AzCopy](http://aka.ms/downloadazcopy) utilitário. Certifique-se de que o executável do AzCopy no seu caminho do sistema, escrevendo "AzCopy" e prima Enter para mostrar a respetiva documentação.
1. Emita os seguintes comandos para copiar todos os dados de exemplo, modelos pretrained e scripts de formação do modelo para as localizações na sua conta de armazenamento adequadas:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Prevê a transferência de ficheiros para demorar até 20 minutos. Enquanto espera, pode avançar para a secção seguinte: poderá ter de abrir outra Interface de linha de comandos através do Workbench e redefinir as variáveis temporárias não existe.

#### <a name="create-the-hdinsight-spark-cluster"></a>Criar o cluster do Spark do HDInsight

O nosso método recomendado para criar um cluster do HDInsight utiliza o Spark do HDInsight cluster modelo do resource manager incluído na subpasta "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" deste projeto.

1. O modelo de cluster do HDInsight Spark é o ficheiro de "Template" sob a subpasta "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" deste projeto. Por predefinição, o modelo cria um cluster do Spark com 40 nós de trabalho. Se terá de ajustar esse número, abra o modelo no seu editor de texto favorito e substitua todas as instâncias do "40" com o número de nós de trabalho da sua preferência.
    - Poderá encontrar erros de memória esgotada, se o número de nós de trabalho que escolher é pequeno. Para combat erros de memória, pode executar os scripts de formação e operationalization num subconjunto de dados disponíveis conforme descrito mais à frente neste documento.
2. Escolha um nome exclusivo e uma palavra-passe para o HDInsight cluster e escrevê-las sempre que for indicado no seguinte comando: em seguida, criar o cluster emitindo os comandos:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Implementação do cluster pode demorar até 30 minutos (incluindo aprovisionamento e o script de execução da ação).

### <a name="set-up-batch-ai-resources"></a>Configurar recursos do Batch AI

Enquanto a aguardar a transferência de ficheiros de conta de armazenamento e a implementação de cluster do Spark concluir, pode preparar o cluster Batch AI servidor NFS (Network File) e a GPU. Abra uma linha de comandos do Azure CLI 2.0 e execute o seguinte comando:

```
az --version 
```

Confirme que `batchai` está listado entre os módulos instalados. Se não, pode utilizar uma Interface de linha de comando diferentes (por exemplo, um aberto através do Workbench).

Em seguida, verifique se o registo do fornecedor foi concluída com êxito. (O registo de fornecedor aceita introdução de quinze minutos e ainda pode estar em curso, se tiver sido concluído recentemente o [instruções de configuração do Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes).) Certifique-se de que "Microsoft.Batch" e "Microsoft.BatchAI" aparecem com o estado "Registada" na saída do comando seguinte:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Caso contrário, execute o seguinte fornecedor comandos de registo e aguarde ~ 15 minutos para o registo concluir.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Modificar os seguintes comandos para substituir as expressões entre parênteses com os valores utilizados anteriormente durante a criação de conta de armazenamento e de grupos de recursos. Em seguida, armazene-os valores variáveis executando os comandos:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Identificar a pasta que contém o projeto do Azure Machine Learning (por exemplo, `C:\Users\<your username>\AzureML\aerialimageclassification`). Substitua o valor entre parênteses filepath da pasta (com nenhuma barra invertida à direita) e execute o comando:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Agora está pronto para criar os recursos do Batch AI necessários para este tutorial.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Preparar o servidor de ficheiros de rede de AI do Batch

O cluster de AI do Batch acede aos seus dados de formação num servidor de ficheiros de rede. Acesso a dados pode ser several-fold mais rápida ao aceder a ficheiros de NFS vs. uma partilha de ficheiros do Azure ou o Blob Storage do Azure.

1. Emita o comando seguinte para criar um servidor de ficheiros de rede:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --vm-size Standard_D2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Verifique o estado de aprovisionamento do seu servidor de ficheiros de rede utilizando o seguinte comando:

    ```
    az batchai file-server list
    ```

    Quando o "provisioningState" do servidor de ficheiros de rede com o nome "landuseclassifier" é "foi concluída com êxito", está pronto para ser utilizado. Espera-se de aprovisionamento para demorar cerca de cinco minutos.
1. Localize o endereço IP do seu NFS na saída do comando anterior (a propriedade "fileServerPublicIp" em "mountSettings"). Escrita IP endereços, onde seja indicado no comando seguinte, em seguida, armazenar o valor ao executar o comando:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Utilizando a ferramenta SSH favorita (utiliza comandos de exemplo seguintes [PuTTY](http://www.putty.org/)), execute este projeto `prep_nfs.sh` script no NFS para transferir a imagem de formação e validação define não existe.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Não se preocupe se as atualizações de progresso de transferência e de extração de dados desloque-se entre a janela de shell, por isso, rapidamente que estão illegible.

Se assim o desejar, pode confirmar que a transferência de dados tem proceeded como planeada por iniciar sessão no servidor de ficheiros com a ferramenta SSH favorito e verificar o `/mnt/data` conteúdo do diretório. Encontrará duas pastas, training_images e validation_images, cada contendo com subpastas denominadas, de acordo com telefone utilizar categorias.  Os conjuntos de formação e validação devem conter ~ 44 k e imagens de k de ~ 11, respetivamente.

#### <a name="create-a-batch-ai-cluster"></a>Criar um cluster de AI do Batch

1. Crie o cluster emitindo os seguintes comandos:

    ```
    set AZURE_BATCHAI_STORAGE_ACCOUNT=%STORAGE_ACCOUNT_NAME%
    set AZURE_BATCHAI_STORAGE_KEY=%STORAGE_ACCOUNT_KEY%
    az batchai cluster create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 
    ```

1. Utilize o seguinte comando para verificar o que seu cluster do Estado de aprovisionamento:

    ```
    az batchai cluster list
    ```

    Quando o estado de alocação para o cluster com o nome "landuseclassifier" alterações de redimensionamento para gradual, é possível submeter as tarefas. No entanto, as tarefas não iniciar a execução até que todas as VMs no cluster tem deixado o estado "a preparação". Se a propriedade "erros" do cluster não for nula, Ocorreu um erro durante a criação do cluster e não deve ser utilizada.

#### <a name="record-batch-ai-training-credentials"></a>Registos credenciais de formação de AI do Batch

Enquanto a aguardar para atribuição de cluster a concluir, abra o `settings.cfg` ficheiro do subdiretório "Código" deste projeto no editor de texto à sua escolha. Atualize as seguintes variáveis com as suas próprias credenciais:
- `bait_subscription_id`(o seu ID de subscrição do Azure 36 carateres)
- `bait_aad_client_id`(o ID de aplicação/cliente de Azure Active Directory mencionado na secção "Pré-requisitos")
- `bait_aad_secret`(o Azure Active Directory segredo da aplicação mencionado na secção "Pré-requisitos")
- `bait_aad_tenant`(o ID de inquilino de Azure Active Directory mencionado na secção "Pré-requisitos")
- `bait_region`(a partir desta redação é a única opção: eastus)
- `bait_resource_group_name`(o grupo de recursos que selecionou anteriormente)

Após ter atribuída estes valores, as linhas de modificação do ficheiro settings.cfg devem assemelhar-se o seguinte texto:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Guarde e feche `settings.cfg`.

Já pode fechar a janela CLI onde é executada os comandos de criação de recursos de AI do Batch. Todos os passos adicionais neste tutorial utilizam um CLI iniciado a partir do Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Preparar o ambiente de execução do Workbench do Azure Machine Learning

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Registar o cluster do HDInsight como um destino de computação do Azure Machine Learning Workbench

Depois de concluída a criação de clusters do HDInsight, registe o cluster como um destino de computação para o projeto da seguinte forma:

1.  Emita o comando seguinte a partir da Interface de linha de comandos do Azure Machine Learning:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    Este comando adiciona dois ficheiros, `myhdi.runconfig` e `myhdi.compute`, ao seu projeto `aml_config` pasta.

1. Abra o `myhdi.compute` ficheiros no seu editor de texto favorito. Modificar o `yarnDeployMode: cluster` linha ler `yarnDeployMode: client`, em seguida, guarde e feche o ficheiro.
1. Execute o seguinte comando para preparar o ambiente do HDInsight para utilização:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Instalar dependências locais

Abra um CLI do Azure Machine Learning Workbench e instalar dependências necessárias para execução local através da emissão do comando seguinte:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Aquisição e compreensão de dados

Este cenário utiliza dados imagery com vista aérea publicamente disponíveis a partir de [National programa de Imagery Agriculture](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) na resolução de medição de 1. Podemos ter gerado os conjuntos de 224 pixel x 224 pixel ficheiros PNG recortada a partir dos dados NAIP originais e ordenados de acordo com as etiquetas de utilização de telefone do [National telefone abrangem base de dados de](https://www.mrlc.gov/nlcd2011.php). Uma imagem de exemplo com etiqueta "Developed" é apresentada no tamanho máximo:

![Um mosaico de exemplo do telefone programada](media/scenario-aerial-image-classification/sample-tile-developed.png)

Conjuntos com balanceamento de classe de ~ 44 k e imagens de k 11 são utilizadas para formação de modelo e validação, respetivamente. Iremos demonstrar a implementação de modelo numa imagem k ~ 67 definir tiling Middlesex County, MA – home page do e da Microsoft Research em Inglaterra do novo Centro de desenvolvimento (NERD). Para obter mais informações sobre como foram construídos estes conjuntos de imagem, consulte o [repositório de git de classificação de imagem Constrangedoramente paralelas](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Localização do Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Durante a configuração, os conjuntos de imagem com vista aérea utilizados neste exemplo foram transferidos para a conta de armazenamento que criou. A formação, validação e imagens operationalization são todos os 224 pixel x 224 pixel PNG os ficheiros de uma resolução de um pixel por quadrado medidor. As imagens de formação e validação tem sido organizadas em subpastas com base na respetiva etiqueta de utilização do telefone. (As etiquetas de utilização do telefone das imagens operationalization desconhecidas e em muitos casos é ambíguos; algumas destas imagens contêm vários tipos de telefone.) Para obter mais informações sobre como foram construídos estes conjuntos de imagem, consulte o [repositório de git de classificação de imagem Constrangedoramente paralelas](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Para ver o exemplo imagens no seu armazenamento do Azure da conta (opcionais):
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Procure o nome da conta de armazenamento na barra de procura ao longo da parte superior do ecrã. Clique na sua conta de armazenamento nos resultados da pesquisa.
2. Clique na ligação "Blobs" no painel principal da conta de armazenamento.
3. Clique no contentor com o nome "comboio." Deverá ver uma lista de diretórios com o nome, de acordo com telefone utilizar.
4. Clique em qualquer um dos diretórios para carregar a lista de imagens que nele contidos.
5. Clique em qualquer imagem e transferi-la para ver a imagem.
6. Se pretender, clique nos contentores com o nome de "teste" e "middlesexma2016" para ver, bem como os respetivos conteúdos.

## <a name="modeling"></a>Modelação

### <a name="training-models-with-azure-batch-ai"></a>Modelos de formação do Azure Batch AI

O `run_batch_ai.py` script na subpasta "Code\02_Modeling" do projeto Workbench é utilizado para emitir uma tarefa de preparação de AI do Batch. Esta tarefa retrains um classificador de imagem DNN escolhido pelo utilizador (AlexNet ou 18 ResNet pretrained no ImageNet). A profundidade de reparametrização também pode ser especificada: reparametrização apenas a camada final de rede poderá reduzir overfitting quando estiverem disponíveis, alguns exemplos de formação ao fine-tuning toda a rede (ou, para AlexNet, as camadas completamente ligadas) pode causar a maior modelo desempenho quando o conjunto de preparação é suficientemente grande.

Quando tiver concluído a tarefa de preparação, este script guarda o modelo (juntamente com um ficheiro que descreve o mapeamento entre a saída de número inteiro o modelo e as etiquetas de cadeia) e serão as predições para o blob storage. Ficheiro de registo da tarefa BAIT é analisado para extrair o timecourse de melhoramento da taxa de erros através dos epochs formação. O timecourse de melhoramento de taxa de erro é registado a funcionalidade de histórico de execução do Workbench AML para posterior visualização.

Selecione um nome para o modelo treinado, um tipo de modelo pretrained e uma profundidade reparametrização. Escrita que as suas seleções onde seja indicado no comando seguinte, em seguida, começam reparametrização executando o comando de uma Interface do Azure ML comando linha:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Esperar que o Azure Machine Learning executar colocar prestes a meia hora para concluir. Recomendamos que execute alguns comandos semelhantes (vários o nome do modelo de saída, tipo de modelo pretrained e a profundidade reparametrização) para que pode comparar o desempenho dos modelos que preparado com métodos diferentes.

### <a name="training-models-with-mmlspark"></a>Modelos de formação MMLSpark

O `run_mmlspark.py` script na subpasta "Code\02_Modeling" do projeto Workbench é utilizado para preparar um [MMLSpark](https://github.com/Azure/mmlspark) modelo para classificação de imagem. O featurizes primeiro de script de preparação definido imagens com um classificador de imagem que DNN pretrained no conjunto de dados ImageNet (AlexNet ou ResNet uma camada de 18). O script, em seguida, utiliza as imagens de featurized para preparar um modelo de MMLSpark (uma floresta aleatória ou um modelo de regressão logística da) para classificar as imagens. O conjunto de imagem de teste, em seguida, é featurized e pontuadas com o modelo treinado. A precisão das predições o modelo no conjunto de teste é calculada e iniciou a funcionalidade de histórico de execução do Workbench do Azure Machine Learning. Por fim, o modelo treinado do MMLSpark e respetivos predições no conjunto de teste são guardadas para armazenamento de Blobs.

Selecione um nome de modelo de saída exclusivo para o modelo treinado, um tipo de modelo pretrained e um tipo de modelo MMLSpark. Escrita que as suas seleções onde seja indicado no modelo de comando seguinte, em seguida, começam reparametrização executando o comando de uma Interface do Azure ML comando linha:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Mais `--sample_frac` parâmetro pode ser utilizado para dar formação e testar o modelo com um subconjunto de dados disponíveis. Utilizar uma fração de exemplo pequeno reduz os requisitos de memória e tempo de execução em detrimento de precisão de modelo treinado. (Por exemplo, uma execução com `--sample_frac 0.1` demore aproximadamente twenty minutos.) Para obter mais informações sobre esta e outros parâmetros, execute `python Code\02_Modeling\run_mmlspark.py -h`.

Os utilizadores são encouraged para executar este script várias vezes com diferentes parâmetros de entrada. Em seguida, pode ser comparado com o desempenho dos modelos de resultantes na funcionalidade de histórico de execuções do Workbench do Azure Machine Learning.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Comparar o desempenho do modelo a utilizar a funcionalidade de histórico de execuções do Workbench

Depois de ter executado dois ou mais formação é executada de cada tipo, navegue para a funcionalidade de histórico de execuções do Workbench clicando no ícone de relógio ao longo da barra de menu da esquerda. Selecione `run_mmlspark.py` da lista de scripts no lado esquerdo. Um painel carrega a precisão do conjunto de teste para todas as execuções de comparação. Para ver mais detalhes, desloque para baixo e clique no nome de uma execução individual.

## <a name="deployment"></a>Implementação

Para aplicar um dos seus modelos de formação para imagens com vista aérea tiling Middlesex County, MA utilizando execução remota no HDInsight, insira o nome do seu modelo pretendido para o seguinte comando e executá-lo:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Mais `--sample_frac` parâmetro pode ser utilizado para operacionalizar o modelo com um subconjunto de dados disponíveis. Utilizar uma fração de exemplo pequeno reduz os requisitos de memória e tempo de execução em detrimento de alternância de predição. Para obter mais informações sobre esta e outros parâmetros, execute `python Code\03_Deployment\batch_score_spark -h`.

Este script escreve predições o modelo à sua conta de armazenamento. Podem ser examinadas serão as predições conforme descrito na secção seguinte.

## <a name="visualization"></a>Visualização

O bloco de notas do Jupyter "Modelo de análise de predição" na subpasta "Code\04_Result_Analysis" do projeto Workbench visualiza predições de um modelo. Carregar e execute o bloco de notas da seguinte forma:
1. Abra o projeto no Workbench e clique na pasta ícone ("ficheiros") ao longo do menu da esquerda para carregar a listagem de diretórios.
2. Navegue para a subpasta "Code\04_Result_Analysis" e clique em do bloco de notas com o nome "Modelo de análise de predição." Deverá ser apresentada uma composição de pré-visualização do bloco de notas.
3. Clique em "Iniciar bloco de notas do servidor" para carregar o bloco de notas.
4. Na primeira célula, introduza o nome do modelo cujos resultados que gostaria de analisar sempre que for indicado.
5. Clique em "célula -> executar todas as" a executar todas as células no bloco de notas.
6. Ler juntamente com o bloco de notas para saber mais sobre as estatísticas e visualizações apresente.

## <a name="cleanup"></a>Limpeza
Quando tiver concluído o exemplo, recomendamos que elimina todos os recursos que criou, executando o seguinte comando da Interface de linha de comandos do Azure:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referências

- [O repositório de classificação de imagem Constrangedoramente paralelas](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Descreve o conjunto de dados construção de imagery livremente disponível e as etiquetas
- [MMLSpark](https://github.com/Azure/mmlspark) repositório do GitHub
   - Contém exemplos adicionais do modelo de formação e avaliação com MMLSpark

## <a name="conclusions"></a>Conclusões

Workbench de aprendizagem máquina do Azure ajuda a cientistas de dados facilmente implementar o seu código destinos remoto de computação. Neste exemplo, código de formação MMLSpark local foi implementado para uma execução remota num cluster do HDInsight e um script local iniciada uma tarefa de preparação num cluster do Azure Batch AI GPU. Funcionalidade de histórico de execução do Workbench do Azure Machine Learning controlados o desempenho dos vários modelos e ajudou-na identificar o modelo mais exato. Funcionalidade de blocos de notas do Jupyter do Workbench ajudou a visualizar predições dos nossos modelos num ambiente interativa e gráfica.

## <a name="next-steps"></a>Passos seguintes
Para aprofundar mais aprofundado deste exemplo:
- Na funcionalidade de histórico de execuções do Workbench do Azure Machine Learning, clique nos símbolos de equipamento para selecionar os gráficos e métricas que são apresentadas.
- Examine os scripts de exemplo para chamar as instruções de `run_logger`. Certifique-se de que compreende a forma como cada métrica é a ser registada.
- Examine os scripts de exemplo para chamar as instruções de `blob_service`. Certifique-se de que compreende os modelos de formação como e predições são armazenadas e obtidas de nuvem.
- Explore o conteúdo dos contentores criados na sua conta de armazenamento de Blobs. Certifique-se de que compreende que script ou comando é responsável pela criação de cada grupo de ficheiros.
- Modificar o script de formação para preparar um tipo de modelo MMLSpark diferente ou para alterar o modelo de sintonização. Utilize a funcionalidade de histórico de execução para determinar se as suas alterações aumentado ou diminuído exatidão o modelo.
