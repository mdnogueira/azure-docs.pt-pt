---
title: "Autenticação do serviço de serviço: API de REST com o Data Lake Store utilizando o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory utilizando a REST API"
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
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 754e65c4bcf8574a16b9620e2f21938ecc62b735
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Autenticação de serviço a serviço utilizando a REST API do Data Lake Store
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Neste artigo, pode saber mais sobre como utilizar a API REST para efetuar a autenticação de serviços do Azure Data Lake Store. Para a autenticação de utilizador final com o Data Lake Store utilizando a REST API, consulte [autenticação de utilizador final com o Data Lake Store utilizando a REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Web" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço
Neste cenário, a aplicação fornece as suas próprias credenciais para efetuar as operações. Para tal, tem de emitir um pedido POST, como o mostrado no seguinte fragmento: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

O resultado do pedido inclui um token de autorização (em falta por que `access-token` no resultado abaixo) que transmitir subsequentemente com as chamadas de REST API. Guardar o token de autenticação num ficheiro de texto; precisará ao efetuar chamadas REST para o Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo utiliza a abordagem **não interativa**. Para obter mais informações sobre a abordagem não interativa (chamadas serviço a serviço), veja [Chamadas serviço a serviço utilizando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação de serviço a serviço para autenticar com o Azure Data Lake Store utilizando a REST API. Agora pode ver os seguintes artigos falar sobre como utilizar a API REST para trabalhar com o Azure Data Lake Store.

* [Operações de gestão de conta no Data Lake Store utilizando a REST API](data-lake-store-get-started-rest-api.md)
* [Operações de dados no Data Lake Store utilizando a REST API](data-lake-store-data-operations-rest-api.md)


