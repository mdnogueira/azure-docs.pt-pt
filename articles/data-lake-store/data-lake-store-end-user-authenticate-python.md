---
title: "Autenticação de utilizador final: Python com o Data Lake Store utilizando o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory com o Python"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 48990c57fb10127733623000a105507b5a48d900
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Autenticação de utilizador final com o Data Lake Store utilizando o Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Neste artigo, pode saber mais sobre como utilizar o SDK Python para fazer a autenticação de utilizador final com o Azure Data Lake Store. Autenticação de utilizador final pode ainda ser dividida em duas categorias:

* Autenticação de utilizador final sem multi-factor authentication
* Autenticação de utilizador final com multi-factor authentication

Ambas estas opções são abordadas neste artigo. Para a autenticação de serviço a serviço com o Data Lake Store utilizando o Python, consulte [autenticação do serviço de serviço com o Data Lake Store utilizando o Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Nativas" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. No IDE à sua escolha, crie uma nova aplicação de Python, por exemplo, **mysample.py**.

2. Adicione o fragmento de código seguinte para importar os módulos necessários

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Guarde as alterações a mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autenticação de utilizador final com multi-factor authentication

### <a name="for-account-management"></a>Para a gestão de conta

Utilize o seguinte fragmento para autenticar com o Azure AD para operações de gestão de conta numa conta do Data Lake Store. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para uma existente do Azure AD **nativo** aplicação.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Para operações de sistema de ficheiros

Utilize esta opção para autenticar com o Azure AD para operações de sistema de ficheiros na conta do Data Lake Store. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para uma existente do Azure AD **nativo** aplicação.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticação de utilizador final sem multi-factor authentication

Este é preterido. Para obter mais informações, consulte [Authentication do Azure utilizando o Python SDK](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação de utilizador final para autenticar com o Azure Data Lake Store com o Python. Agora pode ver os seguintes artigos falar sobre como utilizar o Python para trabalhar com o Azure Data Lake Store.

* [Operações de gestão de conta no Data Lake Store com o Python](data-lake-store-get-started-python.md)
* [Operações de dados no Data Lake Store com o Python](data-lake-store-data-operations-python.md)

