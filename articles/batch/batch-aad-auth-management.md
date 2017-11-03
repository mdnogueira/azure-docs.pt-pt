---
title: "Utilizar o Azure Active Directory para autenticar as soluções de gestão do Batch | Microsoft Docs"
description: "Aplicações criadas com o Gestor de recursos do Azure e o fornecedor de recursos do Batch autenticar com o Azure AD."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: tamram
ms.openlocfilehash: 26d4adf4f74f9aacc4cf8cf24be293ebdb4d63c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autenticar soluções de gestão do Batch com o Active Directory

As aplicações que chamar o serviço de gestão do Azure Batch autenticar com [do Azure Active Directory] [ aad_about] (Azure AD). Azure AD é o diretório baseado na nuvem do multi-inquilino da Microsoft e o serviço de gestão de identidade. Azure próprio utiliza o Azure AD para a autenticação dos seus clientes, os administradores de serviço e utilizadores organizacionais.

A biblioteca de gestão de lotes .NET expõe tipos para trabalhar com contas do Batch, chaves de conta, aplicações e pacotes de aplicações. Gestão biblioteca .NET do Batch é um cliente do fornecedor de recursos do Azure e é utilizada em conjunto com [do Azure Resource Manager] [ resman_overview] gerir estes recursos através de programação. Azure AD é necessário para autenticar pedidos efetuados através de qualquer cliente de fornecedor de recursos do Azure, incluindo gestão biblioteca .NET do Batch e através de [do Azure Resource Manager][resman_overview].

Neste artigo, vamos explorar a utilizar o Azure AD para autenticar a partir de aplicações que utilizam a biblioteca de gestão de lotes .NET. Vamos mostrar como utilizar o Azure AD para autenticar um administrador de subscrição ou coadministrador, utilizando a autenticação integrada. Utilizamos o [AccountManagment] [ acct_mgmt_sample] projeto de exemplo está disponível no GitHub, para percorrer a utilizar o Azure AD com a biblioteca de gestão de lotes .NET.

Para obter mais informações sobre como utilizar a biblioteca de gestão de lotes .NET e o exemplo de AccountManagement, consulte [gerir contas e Batch quotas com a biblioteca de cliente de gestão do Batch para .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registar a sua aplicação com o Azure AD

O Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) fornece uma interface programática para o Azure AD para utilização nas suas aplicações. Para chamar ADAL a partir da sua aplicação, tem de registar a aplicação num inquilino do Azure AD. Ao registar a aplicação, forneça do Azure AD com informações sobre a sua aplicação, incluindo um nome para o mesmo no inquilino do Azure AD. Em seguida, do Azure AD fornece um ID de aplicação que utiliza para associar a aplicação com o Azure AD no tempo de execução. Para saber mais sobre o ID da aplicação, consulte o artigo [aplicação e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Para registar a aplicação de exemplo AccountManagement, siga os passos a [adicionar uma aplicação](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) secção [integrar aplicações com o Azure Active Directory][aad_integrate]. Especifique **aplicação cliente nativa** para o tipo de aplicação. O setor do URI de 2.0 OAuth padrão para o **URI de redirecionamento** é `urn:ietf:wg:oauth:2.0:oob`. No entanto, pode especificar qualquer URI válido (tais como `http://myaccountmanagementsample`) para o **URI de redirecionamento**, uma vez que não precisa de ser um ponto final real:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Depois de concluir o processo de registo, verá o ID da aplicação e o ID de objeto (principal de serviço) listados para a sua aplicação.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Conceder acesso a API do Azure Resource Manager à sua aplicação

Em seguida, terá de delegar o acesso à sua aplicação para a API do Azure Resource Manager. O identificador do Azure AD para a API do Gestor de recursos é **API de gestão de serviços do Windows Azure**.

Siga estes passos no portal do Azure:

1. No painel de navegação esquerdo do portal do Azure, escolha **mais serviços**, clique em **registos de aplicação**e clique em **adicionar**.
2. Procure o nome da sua aplicação na lista de registos de aplicação:

    ![Procure o nome da sua aplicação](./media/batch-aad-auth-management/search-app-registration.png)

3. Apresentar o **definições** painel. No **acesso à API** secção, selecione **as permissões necessárias**.
4. Clique em **adicionar** para adicionar uma nova permissão necessária. 
5. No passo 1, introduza **API de gestão de serviços do Windows Azure**, selecione essa API da lista de resultados e clique em de **selecione** botão.
6. No passo 2, selecione a caixa de verificação junto a **modelo de implementação clássica do Azure de acesso como os utilizadores da organização**e clique em de **selecione** botão.
7. Clique em de **feito** botão.

O **permissões obrigatórias** painel mostra agora que são concedidas permissões para a aplicação para a ADAL e o Gestor de recursos APIs. São concedidas permissões para a ADAL por predefinição quando o primeiro registar a aplicação com o Azure AD.

![Delegar permissões para a API do Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Pontos finais de AD do Azure

Para autenticar as suas soluções de gestão do Batch com o Azure AD, precisará de dois pontos finais bem conhecidos.

- O **endpoint comuns do Azure AD** fornece uma credencial genérica recolha interface quando não for fornecido um inquilino específico, como no caso da autenticação integrada do:

    `https://login.microsoftonline.com/common`

- O **ponto final do Azure Resource Manager** é utilizada para adquirir um token para autenticar pedidos para o serviço de gestão do Batch:

    `https://management.core.windows.net/`

A aplicação de exemplo AccountManagement define constantes para estes pontos finais. Deixe estes constantes inalteradas:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>O ID de aplicação de referência 

A aplicação de cliente utiliza o ID da aplicação (também referido como o ID de cliente), aceder ao Azure AD no tempo de execução. Assim que tiver registado a sua aplicação no portal do Azure, atualize o código para utilizar o ID de aplicação fornecido pelo Azure AD para a sua aplicação registada. Aplicação de exemplo AccountManagement, copie o ID da aplicação do portal do Azure para a constante adequada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Também copia o URI que especificou durante o processo de registo de redirecionamento. O URI especificado no código de redirecionamento têm de corresponder o URI que forneceu quando se registou a aplicação de redirecionamento.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Depois de registar o exemplo de AccountManagement no inquilino do Azure AD e atualize o código de origem de exemplo com os seus valores, o exemplo está pronto para autenticar a utilizar o Azure AD. Quando executar o exemplo, a ADAL tenta adquirir um token de autenticação. Neste passo, este pede-lhe as credenciais da Microsoft: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Depois de fornecer as suas credenciais, pode continuar a aplicação de exemplo para emitir pedidos autenticados para o serviço de gestão do Batch. 

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a execução de [aplicação de exemplo AccountManagement][acct_mgmt_sample], consulte [gerir contas e Batch quotas com a biblioteca de cliente de gestão do Batch para .NET](batch-management-dotnet.md).

Para saber mais acerca do Azure AD, consulte o [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Exemplos aprofundados que mostra como utilizar a ADAL estão disponíveis no [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteca.

Para autenticar as aplicações de serviço do Batch utilizar o Azure AD, consulte [soluções de serviço de Batch de autenticar com o Active Directory](batch-aad-auth.md). 


[aad_about]: ../active-directory/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação para o Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrar aplicações com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
