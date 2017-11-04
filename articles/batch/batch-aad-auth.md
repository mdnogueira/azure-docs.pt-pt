---
title: "Utilizar o Azure Active Directory para autenticação de soluções de serviços do Azure Batch | Microsoft Docs"
description: "Batch suporta do Azure AD para a autenticação do serviço Batch."
services: batch
documentationcenter: .net
author: v-dotren
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: tamram
ms.openlocfilehash: a5dd04e992bd181e512d176fd913a7395fd6b702
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticar soluções do serviço Batch com o Active Directory

O Azure Batch suporta a autenticação com [do Azure Active Directory] [ aad_about] (Azure AD). Azure AD é o diretório baseado na nuvem do multi-inquilino da Microsoft e o serviço de gestão de identidade. Azure próprio utiliza o Azure AD para autenticar os clientes, os administradores de serviço e utilizadores organizacionais.

Quando utilizar a autenticação do Azure AD com o Azure Batch, pode autenticar-se de uma das seguintes formas:

- Ao utilizar **a autenticação integrada** para autenticar um utilizador que está a interagir com a aplicação. Uma aplicação utilizando a autenticação integrada recolhe as credenciais do utilizador e utiliza essas credenciais ao autenticar o acesso a recursos do Batch.
- Utilizando um **principal de serviço** para autenticar uma aplicação autónoma. Um principal de serviço define a política e as permissões para uma aplicação para poder representar a aplicação quando acedem a recursos no tempo de execução.

Para saber mais acerca do Azure AD, consulte o [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Pontos finais para autenticação

Para efetuar a autenticação de aplicações do Batch com o Azure AD, terá de incluir alguns pontos finais bem conhecidos no seu código.

### <a name="azure-ad-endpoint"></a>Ponto final do AD do Azure

A base de ponto final da autoridade do Azure AD:

`https://login.microsoftonline.com/`

Para autenticar com o Azure AD, pode utilizar este ponto final, juntamente com o ID de inquilino (ID de diretório). O ID do inquilino identifica o inquilino do Azure AD a utilizar para autenticação. Para obter o ID do inquilino, siga os passos descritos em [obter o ID de inquilino do seu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> O ponto final de inquilino específico é necessário quando se autenticar através de um principal de serviço. 
> 
> O ponto final de inquilino específico é opcional quando se autenticar utilizando a autenticação integrada, mas recomendada. No entanto, também pode utilizar o ponto final comum do Azure AD. O ponto final comum fornece uma credencial genérica recolha interface quando não for fornecido um inquilino específico. O ponto final comum `https://login.microsoftonline.com/common`.
>
>

Para obter mais informações sobre os pontos finais do Azure AD, consulte [cenários de autenticação para o Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Ponto final de recursos do batch

Utilize o **ponto final de recursos do Azure Batch** adquirir um token para autenticar pedidos para o serviço Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registar a aplicação com um inquilino

Utilizar o Azure AD para autenticar o primeiro passo é registar a sua aplicação num inquilino do Azure AD. Registar a aplicação permite-lhe chamar do Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) a partir do código. A ADAL fornece uma API para autenticar com o Azure AD da sua aplicação. Registar a aplicação não é necessária se planeia utilizar a autenticação integrada ou um principal de serviço.

Ao registar a aplicação, fornecer informações sobre a sua aplicação para o Azure AD. Em seguida, do Azure AD fornece um ID de aplicação que utiliza para associar a aplicação com o Azure AD no tempo de execução. Para saber mais sobre o ID da aplicação, consulte o artigo [aplicação e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Para registar a aplicação do Batch, siga os passos a [adicionar uma aplicação](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) secção [integrar aplicações com o Azure Active Directory][aad_integrate]. Se registar a aplicação como uma aplicação nativa, pode especificar qualquer URI válido para o **URI de redirecionamento**. Não é necessário ser um ponto final real.

Depois de já registou a aplicação, verá o ID da aplicação:

![Registar a aplicação do Batch com o Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Para obter mais informações sobre como registar uma aplicação com o Azure AD, consulte [cenários de autenticação para o Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obter o ID de inquilino do seu Active Directory

O ID do inquilino identifica o inquilino do Azure AD que fornece serviços de autenticação à sua aplicação. Para obter o ID do inquilino, siga estes passos:

1. No portal do Azure, selecione o Active Directory.
2. Clique em **Propriedades**.
3. Copie o valor GUID fornecido para o ID de diretório. Este valor é também denominado o ID do inquilino.

![Copie o ID de diretório](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Utilizar a autenticação integrada

Para autenticar com a autenticação integrada, tem de conceder as permissões de aplicação para ligar a API do serviço de Batch. Este passo permite que a sua aplicação para efetuar chamadas à API do serviço do Batch, com o Azure AD.

Assim que tiver [registado a sua aplicação](#register-your-application-with-an-azure-ad-tenant), siga estes passos no portal do Azure para conceder acesso para o serviço Batch:

1. No painel de navegação esquerdo do portal do Azure, escolha **mais serviços**, clique em **registos de aplicação**.
2. Procure o nome da sua aplicação na lista de registos de aplicação:

    ![Procure o nome da sua aplicação](./media/batch-aad-auth/search-app-registration.png)

3. Abra o **definições** painel para a sua aplicação. No **acesso à API** secção, selecione **as permissões necessárias**.
4. No **as permissões necessárias** painel, clique em de **adicionar** botão.
5. No passo 1, procure a API do Batch. Procure para cada uma destas cadeias até encontrar a API:
    1. **MicrosoftAzureBatch**.
    2. **Batch do Microsoft Azure**. Os inquilinos mais recentes podem utilizar este nome.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch. 
6. Depois de localizar a API do Batch, selecione-o e clique em de **selecione** botão.
6. No passo 2, selecione a caixa de verificação junto a **acesso do Azure Batch serviço** e clique em de **selecione** botão.
7. Clique em de **feito** botão.

O **permissões obrigatórias** painel agora mostra que a aplicação do Azure AD tem acesso a ADAL e o lote API do serviço. São concedidas permissões para a ADAL automaticamente quando o primeiro registar a aplicação com o Azure AD.

![Permissões de concessão de API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Utilize um principal de serviço 

Para efetuar a autenticação de uma aplicação que é executada em modo automático, utilize um principal de serviço. Depois de já registou a aplicação, siga estes passos no portal do Azure para configurar um principal de serviço:

1. Pedir uma chave secreta para a sua aplicação.
2. Atribua uma função RBAC para a aplicação.

### <a name="request-a-secret-key-for-your-application"></a>Pedido de uma chave secreta para a sua aplicação

Quando a aplicação se autentica com um principal de serviço, envia o ID da aplicação e uma chave secreta para o Azure AD. Terá de criar e copie a chave secreta para utilizar a partir do código.

Siga estes passos no portal do Azure:

1. No painel de navegação esquerdo do portal do Azure, escolha **mais serviços**, clique em **registos de aplicação**.
2. Procure o nome da sua aplicação na lista de registos de aplicação.
3. Apresentar o **definições** painel. No **acesso à API** secção, selecione **chaves**.
4. Para criar uma chave, introduza uma descrição para a chave. Em seguida, selecione uma duração para a chave de um ou dois anos. 
5. Clique em de **guardar** botão para criar e apresentar a chave. Copie o valor da chave para num local seguro, como não conseguirá aceder novamente depois de deixar o painel. 

    ![Criar uma chave secreta](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Atribuir uma função RBAC para a aplicação

Para autenticar com um principal de serviço, terá de atribuir uma função RBAC para a aplicação. Siga estes passos.

1. No portal do Azure, navegue para a conta de Batch utilizada pela sua aplicação.
2. No **definições** painel para a conta de Batch, selecione **controlo de acesso (IAM)**.
3. Clique em de **adicionar** botão. 
4. Do **função** pendente, escolha o o _contribuinte_ ou _leitor_ função para a sua aplicação. Para obter mais informações sobre estas funções, consulte [introdução ao controlo de acesso baseado em funções no portal do Azure](../active-directory/role-based-access-control-what-is.md).  
5. No **selecione** campo, introduza o nome da sua aplicação. Selecione a aplicação a partir da lista e clique em **guardar**.

A aplicação deverá agora aparecer nas definições de controlo de acesso com uma função RBAC atribuída. 

![Atribuir uma função RBAC para a aplicação](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obter o ID de inquilino do seu Azure Active Directory

O ID do inquilino identifica o inquilino do Azure AD que fornece serviços de autenticação à sua aplicação. Para obter o ID do inquilino, siga estes passos:

1. No portal do Azure, selecione o Active Directory.
2. Clique em **Propriedades**.
3. Copie o valor GUID fornecido para o ID de diretório. Este valor é também denominado o ID do inquilino.

![Copie o ID de diretório](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Exemplos de código

Os exemplos de código nesta secção mostram como autenticar com o Azure AD através da autenticação integrada e com um principal de serviço. Estes exemplos de código utilizam .NET, mas os conceitos são semelhantes para outros idiomas.

> [!NOTE]
> Um token de autenticação do Azure AD expira após uma hora. Quando utilizar uma longa duração **BatchClient** objeto, recomendamos que é possível obter um token da ADAL em cada pedido para se certificar de que tem sempre um token válido. 
>
>
> Para alcançar isto no .NET, escrever um método que obtém o token do Azure AD e passar este método para um **BatchTokenCredentials** objeto como delegado. O método de delegado denomina-se em cada pedido para o serviço Batch para se certificar de que é fornecido um token válido. Por predefinição ADAL coloca em cache tokens, pelo que é obtido um novo token do Azure AD apenas quando necessário. Para obter mais informações sobre tokens no Azure AD, consulte [cenários de autenticação para o Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exemplo de código: utilizar o Azure AD autenticação integrada com .NET do Batch

Para autenticar com a autenticação integrada do .NET do Batch, referenciar a [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) pacote e o [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pacote.

Incluem o seguinte `using` as instruções no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ponto final de referência o Azure AD no seu código, incluindo o ID do inquilino. Para obter o ID do inquilino, siga os passos descritos em [obter o ID de inquilino do seu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referência de ponto final de recursos do serviço do Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referência a sua conta do Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique o ID de aplicação (ID de cliente) para a sua aplicação. O ID da aplicação está disponível a partir do registo da aplicação no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Também copia o URI que especificou durante o processo de registo de redirecionamento. O URI especificado no código de redirecionamento têm de corresponder o URI que forneceu quando se registou a aplicação de redirecionamento:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escreva um método de chamada de retorno para adquirir o token de autenticação do Azure AD. O **GetAuthenticationTokenAsync** método de chamada de retorno mostrado aqui chamadas ADAL para autenticar um utilizador que está a interagir com a aplicação. O **AcquireTokenAsync** método fornecido pelo ADAL pede ao utilizador para as suas credenciais e continua a aplicação depois do utilizador proporciona-lhes (exceto se já tiver em cache credenciais):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Construir uma **BatchTokenCredentials** objeto que executa o delegado como um parâmetro. Utilizar essas credenciais para abrir um **BatchClient** objeto. Pode utilizá-lo **BatchClient** objeto para operações subsequentes contra o serviço Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exemplo de código: através de um principal de serviço do Azure AD com .NET do Batch

Para autenticar com um principal de serviço a partir do .NET do Batch, referenciar a [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) pacote e o [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pacote.

Incluem o seguinte `using` as instruções no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ponto final de referência o Azure AD no seu código, incluindo o ID do inquilino. Quando utilizar um principal de serviço, tem de fornecer um ponto final de inquilino específico. Para obter o ID do inquilino, siga os passos descritos em [obter o ID de inquilino do seu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referência de ponto final de recursos do serviço do Batch:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referência a sua conta do Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique o ID de aplicação (ID de cliente) para a sua aplicação. O ID da aplicação está disponível a partir do registo da aplicação no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Especifique a chave secreta que copiou do portal do Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Escreva um método de chamada de retorno para adquirir o token de autenticação do Azure AD. O **GetAuthenticationTokenAsync** método de chamada de retorno mostrado aqui chamadas ADAL para autenticação automática:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Construir uma **BatchTokenCredentials** objeto que executa o delegado como um parâmetro. Utilizar essas credenciais para abrir um **BatchClient** objeto. Em seguida, pode utilizar que **BatchClient** objeto para operações subsequentes contra o serviço Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais acerca do Azure AD, consulte o [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Exemplos aprofundados que mostra como utilizar a ADAL estão disponíveis no [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteca.

Para obter mais informações sobre principais de serviço, consulte [aplicação e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Para criar um principal de serviço com o portal do Azure, consulte [portal de utilização para criar do Active Directory principal de serviço e aplicação que pode aceder aos recursos](../resource-group-create-service-principal-portal.md). Também pode criar um principal de serviço com o PowerShell ou a CLI do Azure. 

Para autenticar as aplicações de gestão do Batch utilizar o Azure AD, consulte [soluções de gestão de Batch de autenticar com o Active Directory](batch-aad-auth-management.md). 

[aad_about]: ../active-directory/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação para o Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrar aplicações com o Azure Active Directory"
[azure_portal]: http://portal.azure.com
