---
title: "Utilizar a interface de linha de comandos para começar a utilizar o Azure Data Lake Store | Microsoft Docs"
description: "Utilizar a linha de comandos entre plataformas para criar uma conta de Data Lake Store e executar operações básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f33ccee7dd520adf074856616005c929040116dd
ms.openlocfilehash: 89d1811f3f336f2526ccba3b6be26e90ab7120ad


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introdução ao Azure Data Lake Store utilizando a Linha de comandos do Azure
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI do Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

> [!NOTE]
> Para carregar e transferir uma grande quantidade de dados (ficheiros grandes, um grande número de ficheiros ou ambos), recomendamos que utilize o [SDK Python](data-lake-store-get-started-python.md), o [SDK .NET](data-lake-store-get-started-net-sdk.md), ou o [Azure PowerShell](data-lake-store-get-started-powershell.md). Estas opções têm um melhor desempenho porque podem utilizar vários threads para paralelizar o movimento de dados.
> 
>  

Aprenda a utilizar a interface de linha de comandos do Azure para criar uma conta do Azure Data Lake Store e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake Store, veja [Descrição geral do Data Lake Store](data-lake-store-overview.md).

A CLI do Azure está implementada no Node.js. Pode ser utilizado em qualquer plataforma que suporte Node.js, incluindo Windows, Mac e Linux. A CLI do Azure é open source. O código fonte é gerido no GitHub em <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artigo abrange apenas a utilização da CLI do Azure com o Data Lake Store. Para um guia geral sobre como utilizar a CLI do Azure, veja [Como utilizar a CLI do Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI do Azure** -Veja [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração. Certifique-se de que reinicia o computador depois de instalar a CLI.

## <a name="authentication"></a>Autenticação
Este artigo utiliza uma abordagem de autenticação mais simples com o Data Lake Store, em que inicia sessão como utilizador final. O nível de acesso à conta do Data Lake Store e ao sistema de ficheiros é, então, governado pelo nível de acesso do utilizador que tem sessão iniciada. Contudo, existem outras abordagens para autenticar com o Data Lake Store, que são a **autenticação do utilizador final** ou a **autenticação de serviço a serviço**. Para obter instruções e mais informações sobre a forma como autenticar, veja [Authenticate with Data Lake Store using Azure Active Directory (Autenticar no Data Lake Store com o Azure Active Directory)](data-lake-store-authenticate-using-active-directory.md).

## <a name="login-to-your-azure-subscription"></a>Iniciar sessão na subscrição do Azure
1. Siga os passos documentados em [Connect to an Azure subscription from the Azure Command-Line Interface (Azure CLI) (Ligar a uma subscrição do Azure a partir da Interface de Linha de Comandos do Azure (CLI do Azure))](../xplat-cli-connect.md) e ligue à sua subscrição através do método `azure login`.
2. Liste as subscrições associadas à sua conta com o comando `azure account list`.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    Na saída acima, **Azure-sub-1** está ativada atualmente, ao passo que a outra subscrição é **Azure-sub-2**. 
3. Selecione a subscrição com a qual pretende trabalhar. Se quiser trabalhar com a subscrição Azure-sub-2, utilize o comando `azure account set`.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Azure Data Lake Store
Abra uma linha de comandos, shell ou sessão de terminal e execute os comandos seguintes.

1. Mude para o modo Azure Resource Manager utilizando o seguinte comando:
   
        azure config mode arm
2. Crie um novo grupo de recursos. No seguinte comando, forneça os valores de parâmetros que pretende utilizar.
   
        azure group create <resourceGroup> <location>
   
    Se o nome da localização contiver espaços, coloque-o entre aspas. Por exemplo, "EUA Leste 2".
3. Crie uma conta do Data Lake Store.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Criar pastas no Data Lake Store
Pode criar pastas na sua conta do Azure Data Lake Store para gerir e armazenar dados. Utilize o seguinte comando para criar uma pasta denominada "mynewfolder" na raiz do Data Lake Store.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por exemplo:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Carregar dados para o Data Lake Store
Pode carregar os dados para o Data Lake Store diretamente no nível de raiz ou para uma pasta que criou na conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por exemplo:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Listar ficheiros no Data Lake Store
Utilize o seguinte comando para listar os ficheiros numa conta do Data Lake Store.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por exemplo:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

O resultado deve ser semelhante ao seguinte:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Mudar o nome, transferir e eliminar dados do Data Lake Store
* **Para mudar o nome de um ficheiro**, utilize o seguinte comando:
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    Por exemplo:
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **Para transferir um ficheiro**, utilize o seguinte comando: Certifique-se de que o caminho de destino especificado já existe.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    Por exemplo:
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **Para eliminar um ficheiro**, utilize o seguinte comando:
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    Por exemplo:
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    Quando lhe for pedido, introduza **S** para eliminar o item.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Ver a lista de controlo de acesso para uma pasta no Data Lake Store
Utilize o seguinte comando para ver as ACLs numa pasta do Data Lake Store. Na versão atual, as ACLs apenas podem ser definidos na raiz do Data Lake Store. Assim, o parâmetro de caminho abaixo será sempre a raiz (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Por exemplo:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Eliminar a conta do Data Lake Store
Utilize o seguinte comando para eliminar uma conta do Data Lake Store.

    azure datalake store account delete <dataLakeStoreAccountName>

Por exemplo:

    azure datalake store account delete mynewdatalakestore

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Feb17_HO1-->


