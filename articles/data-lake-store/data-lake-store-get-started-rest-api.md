---
title: "API REST: operações de gestão de contas no Azure Data Lake Store | Microsoft Docs"
description: "Utilizar a API REST do Azure Data Lake Store e WebHDFS para efetuar operações de gestão de contas no Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 6c43f2b341280731707e486ba6f22f11560102c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Operações de gestão de contas no Azure Data Lake Store com a API REST
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [python](data-lake-store-get-started-python.md)
>
>

Neste artigo, aprenderá a efetuar operações de gestão de contas no Data Lake Store com a API REST. As operações de gestão de contas incluem criar uma conta do Data Lake Store, eliminar uma conta do Data Lake Store, etc. Para obter instruções sobre como efetuar operações do sistema de ficheiros no Data Lake Store com a API REST, veja [Operações do sistema de ficheiros no Data Lake Store com a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas da API REST em relação a uma conta do Data Lake Store.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para a autenticação de utilizador final na sua aplicação (interativa), veja [Autenticação de utilizador final com o Data Lake Store com o SDK .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para a autenticação serviço a serviço na sua aplicação (não interativa), veja [Autenticação serviço a serviço com o Data Lake Store com o SDK .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-store-account"></a>Criar uma conta do Data Lake Store
Esta operação baseia-se na chamada da API REST definida [aqui](https://msdn.microsoft.com/library/mt694078.aspx).

Utilize o seguinte comando cURL. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

No comando acima, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. O payload do pedido para este comando está contido no ficheiro **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do ficheiro input.json assemelha-se ao seguinte fragmento:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Eliminar uma conta do Data Lake Store
Esta operação baseia-se na chamada da API REST definida [aqui](https://msdn.microsoft.com/library/mt694075.aspx).

Utilize o seguinte comando cURL para eliminar uma conta do Data Lake Store. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Deve ver um resultado como o seguinte fragmento:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Passos seguintes
* [Operações do sistema de ficheiros no Data Lake Store com a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Consultar também
* [Referência à API REST do Azure Data Lake Store](https://docs.microsoft.com/rest/api/datalakestore/)
* [Open Source Big Data applications compatible with Azure Data Lake Store (Aplicações de Macrodados Open Source compatíveis com o Azure Data Lake Store)](data-lake-store-compatible-oss-other-applications.md)

