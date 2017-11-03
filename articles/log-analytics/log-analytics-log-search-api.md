---
title: "Análise de registos Iniciar pesquisa REST API | Microsoft Docs"
description: "Este guia fornece um tutorial básico que descreve como pode utilizar a REST API de pesquisa de análise de registos no Operations Management Suite (OMS) e fornece exemplos mostram-lhe como utilizar os comandos."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: bwren
ms.openlocfilehash: 5b51c6fcc69c8dff6579a1a1221e88822eccc1a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-log-search-rest-api"></a>Análise de registos de pesquisa REST API de registo
Este guia fornece um tutorial básico, incluindo exemplos, de como pode utilizar a API de REST de pesquisa de análise do registo. Análise de registos faz parte do conjunto de operações de gestão (OMS).

> [!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, deve referir-se ao [documentação para a nova versão da pesquisa de registo API](https://dev.loganalytics.io/).

> [!NOTE]
> Análise de registos foi anteriormente denominada das informações operacionais, que é o nome utilizado no fornecedor de recursos.
>
>

## <a name="overview-of-the-log-search-rest-api"></a>Descrição geral da pesquisa de registo REST API
A API de REST de pesquisa de análise do registo é RESTful e pode ser acedida através da API do Azure Resource Manager. Este artigo fornece exemplos de aceder à API através de [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comandos de código aberto que simplifica a invocar a API do Azure Resource Manager. A utilização de ARMClient é uma das muitas opções para aceder à API de pesquisa de análise do registo. Outra opção consiste em utilizar o módulo Azure PowerShell para OperationalInsights, que inclui cmdlets para aceder a pesquisa. Com estas ferramentas, pode utilizar a API do Gestor de recursos do Azure para efetuar chamadas a áreas de trabalho do OMS e executar comandos de pesquisa dentro delas. A API produz os resultados da pesquisa no formato JSON, permitindo-lhe utilizar os resultados da pesquisa de várias maneiras diferentes através de programação.

O Azure Resource Manager pode ser utilizado através de um [biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) e [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx). Para obter mais informações, consulte as páginas web ligado.

> [!NOTE]
> Se utilizar um comando de agregação, como `|measure count()` ou `distinct`, cada chamada para procurar pode devolver até 500 000 registos. Procura não incluem um comando de agregação devolve até 5 000 registos.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Tutorial de API de REST de pesquisa de análise do registo básico
### <a name="to-use-armclient"></a>Para utilizar ARMClient
1. Instalar [Chocolatey](https://chocolatey.org/), que é um Gestor de pacotes de origem aberta para o Windows. Abra uma janela de linha de comandos como administrador e, em seguida, execute o seguinte comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. Instale ARMClient executando o seguinte comando:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>Para efetuar uma pesquisa utilizando ARMClient
1. Inicie sessão com a sua conta Microsoft ou a sua conta escolar ou profissional:

    ```
    armclient login
    ```

    Um início de sessão com êxito apresenta uma lista de todas as subscrições associadas à conta especificada:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Obter as áreas de trabalho do Operations Management Suite:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Uma chamada Get com êxito seria saída todas as áreas de trabalho associadas à subscrição:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Crie a variável de pesquisa:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Procure utilizando a nova variável de pesquisa:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Exemplos de referência da API de REST de pesquisa de análise de registo
Os exemplos seguintes mostram como pode utilizar a API de pesquisa.

### <a name="search---actionread"></a>Procura - ação/leitura
**Url de exemplo:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Pedido:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
A tabela seguinte descreve as propriedades que estão disponíveis.

| **Propriedade** | **Descrição** |
| --- | --- |
| Parte superior |O número máximo de resultados para devolver. |
| Realce |Contém parâmetros de pré e post, normalmente, utilizados para campos de correspondência de realce |
| Pre |Prefixos a cadeia fornecida para os campos correspondentes. |
| Post |Acrescenta a cadeia fornecida para os campos correspondentes. |
| consulta |A consulta de pesquisa utilizada para recolher e devolver resultados. |
| start |O início da janela de tempo que pretende que os resultados para localizar a partir do. |
| Fim |O fim da janela de tempo que pretende que os resultados para localizar a partir do. |

**Resposta:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Procurar / {ID} - ação/leitura
**O conteúdo de uma procura guardada de pedido:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> Se a pesquisa devolve com um Estado 'Pendente', a consulta, em seguida, os resultados actualizados pode ser feito através desta API. Depois de 6 min, o resultado da pesquisa será removido da cache e HTTP removido vai ser devolvido. Se o pedido inicial pesquisa devolve um Estado 'Bem sucedido' imediatamente, os resultados não foram adicionados à cache fazendo com que esta API devolver HTTP removido se consultado. O conteúdo de um resultado de HTTP 200 está no mesmo formato que o pedido de pesquisa inicial apenas com valores atualizados.
>
>

### <a name="saved-searches"></a>Pesquisas guardadas
**Pedido de lista de pesquisas guardadas:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Suportado métodos: GET colocar eliminar

Suportado métodos de coleção: introdução

A tabela seguinte descreve as propriedades que estão disponíveis.

| Propriedade | Descrição |
| --- | --- |
| Id |O identificador exclusivo. |
| ETag |**Necessário para Patch**. Atualizada pelo servidor de cada operação de escrita. Valor tem de ser igual ao valor armazenado atual ou ' *' para atualizar. 409 devolvido para valores antigo/inválido. |
| Properties.Query |**Necessário**. A consulta de pesquisa. |
| properties.displayName |**Necessário**. O nome a apresentar definidos pelo utilizador da consulta. |
| Properties.Category |**Necessário**. A categoria definido pelo utilizador da consulta. |

> [!NOTE]
> A API de pesquisa de análise de registos atualmente devolve criados pelo utilizador guardar pesquisas quando consultados para pesquisas guardadas numa área de trabalho. A API não devolver pesquisas guardadas fornecidas por soluções.
>
>

### <a name="create-saved-searches"></a>Criar procuras guardadas
**Pedido:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisismyid?api-version=2015-03-20 $savedSearchParametersJson
```

> [!NOTE]
> O nome para pesquisas guardadas todos, agendas e criadas com a API de análise do registo de ações tem de ser em minúsculas.

### <a name="delete-saved-searches"></a>Apagar as pesquisas gravadas
**Pedido:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Atualizar procuras guardadas
 **Pedido:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadados - apenas JSON
Eis uma forma de ver os campos para todos os tipos de registo para os dados recolhidos na sua área de trabalho. Por exemplo, se quiser que saber se o tipo de evento tem um campo com o nome de computador, esta consulta é uma forma de verificar.

**Pedido para campos:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Resposta:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

A tabela seguinte descreve as propriedades que estão disponíveis.

| **Propriedade** | **Descrição** |
| --- | --- |
| nome |Nome do campo. |
| displayName |O nome a apresentar do campo. |
| tipo |O tipo de valor de campo. |
| facetável |Combinação de atual, 'indexada', ' armazenados ' e 'aspeto' Propriedades. |
| apresentar |Propriedade de 'Mostrar' atual. TRUE se o campo está visível na pesquisa. |
| ownerType |Reduzido para apenas os tipos que pertencem a IPs integrado. |

## <a name="optional-parameters"></a>Parâmetros opcionais
As informações seguintes descrevem os parâmetros opcionais disponíveis.

### <a name="highlighting"></a>Realce
O parâmetro de "Realce" é um parâmetro opcional que pode utilizar para o subsistema de pesquisa de pedido inclui um conjunto de marcadores na respetiva resposta.

Estes marcadores indicam o início e de fim do texto realçado que satisfaça os termos de licenciamento fornecidos na sua consulta de pesquisa.
Pode especificar os marcadores de início e de fim que são utilizados pela pesquisa moldar o termo realçado.

**Consulta de pesquisa de exemplo**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Resultado de exemplo:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Tenha em atenção que o resultado anterior contém uma mensagem de erro que foi adicionado como prefixo e anexada.

## <a name="computer-groups"></a>Grupos de computadores
Grupos de computadores são pesquisas guardadas especiais que devolvem um conjunto de computadores.  Pode utilizar um grupo de computadores em outras consultas para limitar os resultados para os computadores no grupo.  Um grupo de computadores é implementado como uma pesquisa guardada com uma etiqueta de grupo com um valor de computador.

Segue-se uma resposta de exemplo para um grupo de computadores.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>A obter grupos de computadores
Para obter um grupo de computadores, utilize o método Get com o ID de grupo.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Criar ou atualizar um grupo de computadores
Para criar um grupo de computadores, utilize o método Put com um ID exclusivo de pesquisa guardada. Se utilizar um ID de grupo de computador existente, em seguida, de um modificada. Quando cria um grupo de computadores no portal da análise de registos, é criado o ID do grupo e do nome.

A consulta utilizada para a definição de grupo tem de devolver um conjunto de computadores para o grupo de funcionar corretamente.  É recomendado que termina a consulta com `| Distinct Computer` para garantir que são devolvidos de dados correto.

A definição da pesquisa guardada deve incluir uma etiqueta com o nome de grupo com um valor de computador para a pesquisa para ser classificados como um grupo de computadores.

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>Eliminar grupos de computadores
Para eliminar um grupo de computadores, utilize o método Delete com o ID de grupo.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para criar consultas com campos personalizados para os critérios.
