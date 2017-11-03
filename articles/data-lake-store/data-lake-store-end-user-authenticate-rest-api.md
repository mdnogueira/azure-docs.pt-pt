---
title: "Autenticação de utilizador final: API de REST com o Data Lake Store utilizando o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory utilizando a REST API"
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
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: bcddb66806cc58b3513c9c157512ac8b96a51ec1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-rest-api"></a>Autenticação de utilizador final com o Data Lake Store utilizando a REST API
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, pode saber mais sobre como utilizar a API REST para efetuar a autenticação de utilizador final com o Azure Data Lake Store. Para a autenticação de serviço a serviço com o Data Lake Store utilizando a REST API, consulte [autenticação do serviço de serviço com o Data Lake Store utilizando a REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Nativas" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](http://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas da API REST em relação a uma conta do Data Lake Store.

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Autenticação de utilizador final é a abordagem recomendada se pretender que um utilizador inicie sessão sua aplicação utilizar o Azure AD. A aplicação é capaz de aceder aos recursos do Azure com o mesmo nível de acesso que o utilizador com sessão iniciada. O utilizador tem de fornecer as respetivas credenciais periodicamente por ordem para a sua aplicação manter o acesso.

O resultado de ter o início de sessão do utilizador final é que a aplicação recebe um token de acesso e um token de atualização. O token de acesso obtém anexado a cada pedido efetuado ao Data Lake Store ou do Data Lake Analytics e é válido durante uma hora por predefinição. O token de atualização pode ser utilizado para obter um novo token de acesso e é válido para duas semanas por predefinição, se utilizados regularmente. Pode utilizar duas abordagens diferentes para início de sessão do utilizador final.

Neste cenário, a aplicação pede ao utilizador para iniciar sessão e todas as operações são efetuadas no contexto do utilizador. Execute os seguintes passos:

1. Através da sua aplicação, redirecione o utilizador para o seguinte URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI> tem de estar codificado para utilização num URL. Assim, para https://localhost, utilize `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    Para o objetivo deste tutorial, pode substituir os valores de marcador de posição no URL acima e colá-lo na barra de endereço do browser. Será redirecionado para a autenticação utilizando o seu início de sessão do Azure. Depois de iniciar sessão com êxito, a resposta é apresentada na barra de endereço do browser. A resposta estará no seguinte formato:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capture o código de autorização na resposta. Para este tutorial, pode copiar o código de autorização na barra de endereço do browser da web e transmita-na mensagem de pedido para o ponto final do token, conforme mostrado no seguinte fragmento:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > Neste caso, \<REDIRECT-URI> não tem de ser codificado.
   > 
   > 

3. A resposta é um objeto JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). A aplicação utiliza o token de acesso ao aceder ao Azure Data Lake Store e o token de atualização para obter outro token de acesso quando um token de acesso expira.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Quando o token de acesso expira, pode pedir um novo token de acesso utilizando o token de atualização, conforme mostrado no seguinte fragmento:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Para obter mais informações sobre a autenticação de utilizador interativa, veja [Fluxo de concessão de códigos de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).
   
## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação de serviço a serviço para autenticar com o Azure Data Lake Store utilizando a REST API. Agora pode ver os seguintes artigos falar sobre como utilizar a API REST para trabalhar com o Azure Data Lake Store.

* [Operações de gestão de conta no Data Lake Store utilizando a REST API](data-lake-store-get-started-rest-api.md)
* [Operações de dados no Data Lake Store utilizando a REST API](data-lake-store-data-operations-rest-api.md)

