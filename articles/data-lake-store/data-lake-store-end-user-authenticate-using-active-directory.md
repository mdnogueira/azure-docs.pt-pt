---
title: "Autenticação de utilizador final: Data Lake Store com o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory"
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
ms.openlocfilehash: 98898675b85d62c97a215f9922f1393001013943
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

O Azure Data Lake Store utiliza o Azure Active Directory para autenticação. Antes de criar uma aplicação que funciona com o Azure Data Lake Store ou do Azure Data Lake Analytics, tem de decidir como autenticar a sua aplicação no Azure Active Directory (Azure AD). As duas opções principais disponíveis são:

* Autenticação de utilizador final (Este artigo)
* Autenticação do serviço de serviço (Escolha esta opção da lista pendente acima)

Ambas estas opções resultam na sua aplicação que está a ser fornecida com um token de OAuth 2.0, que obtém anexado a cada pedido efetuado ao Azure Data Lake Store ou do Azure Data Lake Analytics.

Este artigo aborda como criar um **aplicação nativa do Azure AD para autenticação de utilizador final**. Para obter instruções sobre a configuração da aplicação do Azure AD para autenticação de serviço a serviço, consulte [autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* O ID de subscrição. Pode obtê-lo a partir do portal do Azure. Por exemplo, está disponível a partir do painel de conta de Data Lake Store.
  
    ![Obter ID de subscrição](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* O nome de domínio do Azure AD. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. Na captura de ecrã abaixo, o nome de domínio é **contoso.onmicrosoft.com**, e o GUID entre parênteses Retos é o ID do inquilino. 
  
    ![Obter o domínio do AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* O ID de inquilino do Azure. Para obter instruções sobre como obter o ID do inquilino, consulte [obter o ID de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Este mecanismo de autenticação é a abordagem recomendada se pretender que um utilizador final para iniciar sessão sua aplicação através do Azure AD. A aplicação, em seguida, é capaz de aceder aos recursos do Azure com o mesmo nível de acesso como o utilizador final que tem sessão iniciada. O utilizador final tem de fornecer as respetivas credenciais periodicamente por ordem para a sua aplicação manter o acesso.

O resultado de ter o início de sessão do utilizador final é que a aplicação recebe um token de acesso e um token de atualização. O token de acesso obtém anexado a cada pedido efetuado ao Data Lake Store ou do Data Lake Analytics e é válido durante uma hora por predefinição. O token de atualização pode ser utilizado para obter um novo token de acesso e é válido para duas semanas por predefinição. Pode utilizar duas abordagens diferentes para início de sessão do utilizador final.

### <a name="using-the-oauth-20-pop-up"></a>Utilizar o pop-up de OAuth 2.0
A aplicação pode acionar uma pop-up, de autorização de OAuth 2.0 no qual o utilizador final pode introduzir as respetivas credenciais. Este pop-up também funciona com o processo de autenticação do Azure AD dois fatores (2FA), se necessário. 

> [!NOTE]
> Este método ainda não é suportado no Azure AD Authentication Library (ADAL) para o Python ou Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Na passagem direta credenciais de utilizador
A aplicação diretamente pode fornecer as credenciais de utilizador para o Azure AD. Este método só funciona com contas de utilizador de ID organizacionais; Não é compatível com pessoal / "live ID" contas de utilizador, incluindo as contas que termine em @outlook.com ou @live.com. Além disso, este método não é compatível com contas de utilizador exigem autenticação Azure AD dois fatores (2FA).

### <a name="what-do-i-need-for-this-approach"></a>O que é necessário para esta abordagem?
* Nome de domínio do Azure AD. Este requisito já está listado no pré-requisito deste artigo.
* ID de inquilino do Azure AD Este requisito já está listado no pré-requisito deste artigo.
* Azure AD **aplicação nativa**
* ID da aplicação para a aplicação nativa do Azure AD
* URI de redirecionamento para a aplicação nativa do Azure AD
* Definir permissões delegadas


## <a name="step-1-create-an-active-directory-native-application"></a>Passo 1: Criar uma aplicação nativa do Active Directory

Criar e configurar uma aplicação nativa do Azure AD para autenticação de utilizador final com o Azure Data Lake Store utilizando o Azure Active Directory. Para obter instruções, consulte [criar uma aplicação do Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Ao seguir as instruções na ligação, certifique-se de que seleciona **nativo** de tipo de aplicação, conforme mostrado na captura de ecrã seguinte:

![Criar aplicação de web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "aplicação nativa de criação")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Passo 2: Obter o ID da aplicação e URI de redirecionamento

Consulte [obter o ID de aplicação](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) para obter o ID da aplicação (também denominado o ID de cliente no portal clássico do Azure) da aplicação nativa do Azure AD.

Para obter o URI de redirecionamento, efetue os seguintes passos.

1. A partir do portal do Azure, selecione **do Azure Active Directory**, clique em **registos de aplicação**e, em seguida, localize e clique na aplicação nativa do Azure AD que criou.

2. Do **definições** painel para a aplicação, clique em **redirecionar URIs**.

    ![Obter o URI de redirecionamento](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copie o valor apresentado.


## <a name="step-3-set-permissions"></a>Passo 3: Conjunto de permissões

1. A partir do portal do Azure, selecione **do Azure Active Directory**, clique em **registos de aplicação**e, em seguida, localize e clique na aplicação nativa do Azure AD que criou.

2. Do **definições** painel para a aplicação, clique em **as permissões necessárias**e, em seguida, clique em **adicionar**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. No **adicione acesso da API** painel, clique em **selecionar um API**, clique em **do Azure Data Lake**e, em seguida, clique em **selecione**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  No **adicione acesso da API** painel, clique em **selecionar permissões**, selecione a caixa de verificação para conceder **acesso total a Data Lake Store**e, em seguida, clique em **selecione**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Clique em **Concluído**.

5. Repita os dois últimos passos para conceder permissões para **API de gestão de serviços do Windows Azure** bem.
   
## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou uma aplicação nativa do Azure AD e recolheu as informações necessárias nas suas aplicações de cliente, criar utilizando o .NET SDK, Java SDK, REST API, etc. Agora pode avançar para os seguintes artigos falar sobre como utilizar a aplicação web do Azure AD para primeiro autenticar com o Data Lake Store e, em seguida, executar outras operações no arquivo.

* [Finais-utilizadores-autenticação com o Data Lake Store com Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Autenticação de utilizador final com o Data Lake Store com .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Autenticação de utilizador final com o Data Lake Store utilizando o Python](data-lake-store-end-user-authenticate-python.md)
* [Autenticação de utilizador final com o Data Lake Store utilizando a REST API](data-lake-store-end-user-authenticate-rest-api.md)

