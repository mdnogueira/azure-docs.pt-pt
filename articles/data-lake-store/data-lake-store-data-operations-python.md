---
title: "Python: operações do sistema de ficheiros no Azure Data Lake Store | Microsoft Docs"
description: Saiba como utilizar o Python SDK para trabalhar com o sistema de ficheiros do Data Lake Store.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 3540e58a58f20842979212ba41f11ce2908941f5
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Operações do sistema de ficheiros no Azure Data Lake Store com a API REST
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, irá aprender a utilizar o Python SDK para executar operações de sistema de ficheiros no Azure Data Lake Store. Para obter instruções sobre como efetuar operações de gestão de contas no Data Lake Store com o Python, veja [Account management operations on Data Lake Store using Python (Operações da gestão de contas no Data Lake Store com o Python)](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure Data Lake Store**. Siga as instruções em [Get started with Azure Data Lake Store using the Azure portal (Introdução ao Azure Data Lake Store com o portal do Azure)](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar no Data Lake Store com Python, tem de instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O módulo `azure-mgmt-datalake-store`, que inclui operações de gestão de contas do Azure Data Lake Store. Para obter mais informações sobre este módulo, veja [Azure Data Lake Store Management module reference (Referência ao módulo de Gestão do Azure Data Lake Store)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* O módulo `azure-datalake-store`, que inclui operações de sistema de ficheiros do Azure Data Lake Store. Para obter mais informações sobre este módulo, veja [Azure Data Lake Store Filesystem module reference (Referência ao módulo de Sistema de Ficheiros do Azure Data Lake Store)](http://azure-datalake-store.readthedocs.io/en/latest/).

Utilize os comandos seguintes para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE à sua escolha, crie uma aplicação Python nova, como, por exemplo, **mysample.py**.

2. Adicione as linhas seguintes para importar os módulos necessários.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Guarde as alterações a mysample.py.

## <a name="authentication"></a>Autenticação

Nesta secção, vamos falar sobre as diferentes formas de autenticar com o Azure AD. As opções disponíveis são:

* Para a autenticação de utilizador final na sua aplicação, veja [End-user authentication with Data Lake Store using Python (Autenticação de utilizador final com o Data Lake Store através de Python)](data-lake-store-end-user-authenticate-python.md).
* Para a autenticação serviço a serviço na sua aplicação, veja [Service-to-service authentication with Data Lake Store using Python (Autenticação serviço a serviço com o Data Lake Store através de Python)](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Criar cliente do sistema de ficheiros

O fragmento que se segue cria, primeiro, o cliente de conta do Data Lake Store. Utiliza o objeto de cliente para criar uma conta do Data Lake Store. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Criar um diretório

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Carregar um ficheiro


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Transferir um ficheiro

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Eliminar um diretório

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Passos seguintes
* [Operações de gestão de contas no Data Lake Store com o Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Consultar também
* [Referência de Python (Gestão de contas) do Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Referência de Python (Sistema de ficheiros) do Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest)
* [Open Source Big Data applications compatible with Azure Data Lake Store (Aplicações de Macrodados Open Source compatíveis com o Azure Data Lake Store)](data-lake-store-compatible-oss-other-applications.md)
