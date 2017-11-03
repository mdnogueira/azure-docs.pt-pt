---
title: "Autenticação do serviço de serviço: Data Lake Store com o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory"
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
ms.openlocfilehash: 9a4502ecfd5471cc2a968f995e66e595f59384b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

O Azure Data Lake Store utiliza o Azure Active Directory para autenticação. Antes de criar uma aplicação que funciona com o Azure Data Lake Store, terá de decidir como autenticar a sua aplicação no Azure Active Directory (Azure AD). As duas opções principais disponíveis são:

* Autenticação de utilizador final 
* Autenticação de serviços (Este artigo) 

Ambas estas opções resultam na sua aplicação que está a ser fornecida com um token de OAuth 2.0, que obtém anexado a cada pedido efetuado ao Azure Data Lake Store.

Este artigo aborda como criar um **aplicação web do Azure AD para autenticação de serviço a serviço**. Para obter instruções sobre a configuração da aplicação do Azure AD para autenticação de utilizador final, consulte [autenticação de utilizador final com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Passo 1: Criar uma aplicação web do Active Directory

Criar e configurar uma aplicação de web do Azure AD para autenticação de serviços do Azure Data Lake Store utilizando o Azure Active Directory. Para obter instruções, consulte [criar uma aplicação do Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Ao seguir as instruções apresentadas na hiperligação anterior, certifique-se de que seleciona **aplicação Web / API** de tipo de aplicação, conforme mostrado na captura de ecrã seguinte:

![Criar aplicação de web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "criar web app")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Passo 2: Obter ID da aplicação, a chave de autenticação e o ID do inquilino
Quando programaticamente iniciar sessão, terá do ID para a sua aplicação. Se a aplicação é executada sob as suas próprias credenciais, também tem uma chave de autenticação.

* Para obter instruções sobre como obter a chave de autenticação e o ID de aplicação (também denominada segredo do cliente) para a sua aplicação, consulte [Get de chave de autenticação e ID de aplicação](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Para obter instruções sobre como obter o ID do inquilino, consulte [obter ID de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>Passo 3: Atribuir a aplicação do Azure AD para a pasta ou ficheiro de conta do Azure Data Lake Store


1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a conta do Azure Data Lake Store que pretende associar a aplicação do Azure Active Directory que criou anteriormente.
2. No painel da conta do Data Lake Store, clique em **Explorador de Dados**.
   
    ![Criar diretórios na conta do Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "criar diretórios na conta do Data Lake")
3. No **Explorador de dados** painel, clique no ficheiro ou pasta para o qual pretende fornecer acesso à aplicação do Azure AD e, em seguida, clique em **acesso**. Para configurar o acesso a um ficheiro, tem de clicar em **acesso** do **pré-visualização do ficheiro** painel.
   
    ![Definir ACLs no sistema de ficheiros do Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "definir ACLs no sistema de ficheiros do Data Lake")
4. O **acesso** painel lista o acesso padrão e personalizada de acesso já atribuída para a raiz. Clique em de **adicionar** ícone para adicionar as ACLs de nível de personalizado.
   
    ![Lista de acesso padrão e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "lista acesso padrão e personalizado")
5. Clique em de **adicionar** ícone para abrir o **adicionar acesso de personalizado** painel. Neste painel, clique em **selecionar utilizador ou grupo**e, em seguida, no **selecionar utilizador ou grupo** painel, procure a aplicação do Azure Active Directory que criou anteriormente. Se tiver muitos grupos a pesquisar a partir do, utilize a caixa de texto na parte superior para filtrar o nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **selecione**.
   
    ![Adicione um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "adicionar um grupo")
6. Clique em **selecionar permissões**, selecione as permissões e se pretende atribuir as permissões por predefinição ACL, aceder a ACL, ou ambos. Clique em **OK**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "atribuir permissões de grupo")
   
    Para obter mais informações sobre as permissões no Data Lake Store e predefinido/acesso ACLs, consulte [controlo de acesso no Data Lake Store](data-lake-store-access-control.md).
7. No **adicionar acesso de personalizado** painel, clique em **OK**. O grupo recentemente adicionado, com as permissões associadas, são apresentados no **acesso** painel.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "atribuir permissões de grupo")

> [!NOTE]
> Se pretender utilizar os SDKs para criar uma conta de Data Lake Store, tem de atribuir a aplicação web do Azure AD como uma função para o grupo de recursos no qual criou a conta de Data Lake Store.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Passo 4: Obter o ponto final para os token de OAuth 2.0 (apenas para aplicações baseadas em Java)

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em Active Directory no painel esquerdo.

2. No painel esquerdo, clique em **registos de aplicação**.

3. Na parte superior do painel de registos de aplicação, clique em **pontos finais**.

    ![Ponto final de tokens OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "ponto final de tokens OAuth")

4. Na lista de pontos finais, copie o ponto final para os token de OAuth 2.0.

    ![Ponto final de tokens OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "ponto final de tokens OAuth")   

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou uma aplicação web do Azure AD e recolheu as informações que necessárias nas suas aplicações de cliente, criar utilizando o .NET SDK, Java, Python, REST API, etc. Agora pode avançar para os seguintes artigos falar sobre como utilizar a aplicação nativa do Azure AD para o primeiro autenticar com o Data Lake Store e, em seguida, executar outras operações no arquivo.

* [Autenticação do serviço de serviço com o Data Lake Store com Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticação do serviço de serviço com o Data Lake Store com .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticação de serviço a serviço com o Data Lake Store utilizando o Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticação de serviço a serviço utilizando a REST API do Data Lake Store](data-lake-store-service-to-service-authenticate-rest-api.md)


