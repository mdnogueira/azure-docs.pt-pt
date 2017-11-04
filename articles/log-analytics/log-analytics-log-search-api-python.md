---
title: Script de Python para obter dados do Log Analytics do Azure | Microsoft Docs
description: "A API de pesquisa de registo de análise do registo permite que qualquer cliente de REST API obter dados a partir de uma área de trabalho de análise de registos.  Este artigo fornece um exemplo de script de Python, utilizando a API de pesquisa de registo."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: bwren
ms.openlocfilehash: a8a4ec7a6ddf2daeca6ead11460fa076a7eb5c94
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>Obter dados de análise de registos com um script de Python
O [API de pesquisa do registo de análise do registo](log-analytics-log-search-api.md) permite que qualquer cliente de REST API obter dados a partir de uma área de trabalho de análise de registos.  Este artigo apresenta um exemplo de script de Python que utiliza a API de pesquisa de registo de análise do registo.  

>[!NOTE]
> Este artigo utiliza a API de pesquisa de registo para o idioma de consulta legado na análise de registos.  Uma atualização será fornecida a este artigo para áreas de trabalho que tenham sido atualizadas para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md).

## <a name="authentication"></a>Autenticação
Este script utiliza um principal de serviço no Azure Active Directory para autenticar para a área de trabalho.  Principais de serviço permitem uma aplicação de cliente pedir que o serviço de autenticar uma conta, mesmo que o cliente não tem o nome da conta. Antes de executar este script, tem de criar um principal de serviço com o processo em [portal de utilização para criar um Azure Active Directory principal de serviço e aplicação que pode aceder aos recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md).  Terá de fornecer o ID da aplicação, o ID de inquilino e a chave de autenticação para o script. 

> [!NOTE]
> Quando lhe [criar uma conta de automatização do Azure](../automation/automation-create-standalone-account.md), um principal de serviço é criado que é adequado utilizar com este script.  Se já tiver um principal de serviço criado pela automatização do Azure, em seguida, deverá conseguir utilizá-lo em vez de criar um novo, embora poderá ter de [criar uma chave de autenticação](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) se ainda não tiver um.

## <a name="script"></a>Script
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [API de pesquisa do registo de análise do registo](log-analytics-log-search-api.md).