---
title: "Python: operações de gestão de contas no Azure Data Lake Store | Microsoft Docs"
description: "Saiba como utilizar o Python SDK para trabalhar com operações de gestão de contas do Data Lake Store."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 76e84687815ca6f4b031e5f7143ba0079fb053db
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Operações de gestão de contas no Azure Data Lake Store com o Python
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [python](data-lake-store-get-started-python.md)
>
>

Saiba como utilizar o Python SDK para o Azure Data Lake Store, para realizar operações de gestão de conta básica, como criar uma conta do Data Lake Store, listar contas de Data Lake Store, etc. Para obter instruções sobre como realizar operações do sistema de ficheiros no Data Lake Store com o Python, veja [Filesystem operations on Data Lake Store using Python (Operações do sistema de ficheiros no Data Lake Store com o Python)](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Um grupo de recursos do Azure**. Para obter instruções, veja [Criar um grupo de recursos do Azure](../azure-resource-manager/resource-group-portal.md).

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

2. Adicione o fragmento de código seguinte para importar os módulos necessários

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

## <a name="create-client-and-data-lake-store-account"></a>Criar cliente e conta do Data Lake Store

O fragmento que se segue cria, primeiro, o cliente de conta do Data Lake Store. Utiliza o objeto de cliente para criar uma conta do Data Lake Store. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Listar as contas do Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Eliminar a conta do Data Lake Store

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Passos seguintes
* [Operações do sistema de ficheiros no Data Lake Store com o Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Consultar também
* [Referência de Python (Gestão de contas) do Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Referência de Python (Sistema de ficheiros) do Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest)
* [Open Source Big Data applications compatible with Azure Data Lake Store (Aplicações de Macrodados Open Source compatíveis com o Azure Data Lake Store)](data-lake-store-compatible-oss-other-applications.md)
