---
title: "Autenticação de serviços ao Cofre de chaves do Azure através do .NET"
description: "Utilize a biblioteca de Microsoft.Azure.Services.AppAuthentication para autenticar para o Cofre de chaves do Azure através do .NET."
keywords: credenciais locais authentication Cofre de chaves do Azure
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: 
ms.service: microsoft-keyvault
ms.technology: 
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: f67f81aeee0775ea8d90e4459f2c46266a774786
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticação de serviços ao Cofre de chaves do Azure através do .NET

Para autenticar para o Cofre de chaves do Azure, terá de uma credencial do Azure Active Directory (AD), um segredo partilhado ou um certificado. Gerir essas credenciais pode ser difícil e é tempting agrupar as credenciais para uma aplicação, incluindo-las nos ficheiros de origem ou de configuração.

O `Microsoft.Azure.Services.AppAuthentication` para a biblioteca .NET simplifica este problema. Utiliza credenciais do programador para autenticar durante o desenvolvimento local. Quando a solução for implementada posteriormente para o Azure, a biblioteca de comutadores automaticamente para as credenciais de aplicação.  

A utilização de credenciais de programador durante o desenvolvimento local é mais segura porque não tem de criar as credenciais do Azure AD ou credenciais entre os programadores de partilha.

O `Microsoft.Azure.Services.AppAuthentication` biblioteca gere autenticação automaticamente, que por sua vez permite-lhe focar-se na sua solução, em vez das suas credenciais.

O `Microsoft.Azure.Services.AppAuthentication` biblioteca suporta o desenvolvimento local com o Microsoft Visual Studio, CLI do Azure ou a autenticação integrada do Azure AD. Quando implementada para uma Máquina Virtual do Azure (VM) ou de serviços de aplicações do Azure, a biblioteca utiliza automaticamente [identidade de serviço geridas](/azure/active-directory/msi-overview) (MSI). Sem alterações de configuração ou de código são necessárias. A biblioteca também suporta a utilização direta do Azure AD [credenciais do cliente](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal) quando MSI não está disponível ou quando o contexto de segurança do Programador não é possível determinar durante o desenvolvimento local.

<a name="asal"></a>
## <a name="using-the-library"></a>Utilizar a biblioteca

Para aplicações de .NET, a forma mais simples para funcionar com uma identidade de serviço geridas (MSI) é através de `Microsoft.Azure.Services.AppAuthentication` pacote. Eis como começar a utilizar:

1. Adicione uma referência para o [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) pacote NuGet à sua aplicação.

2. Adicione o seguinte código:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

O `AzureServiceTokenProvider` classe coloca em cache o token na memória e obtém-lo a partir do Azure AD imediatamente antes da expiração. Por conseguinte, já não terá de verificar a expiração antes de chamar o `GetAccessTokenAsync` método. Chame somente o método quando pretender utilizar o token. 

O `GetAccessTokenAsync` método requer um identificador de recurso. Para obter mais informações, consulte [serviços do Azure que suportam uma identidade de serviço geridas](https://docs.microsoft.com/en-us/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Amostras

Os seguintes exemplos mostram o `Microsoft.Azure.Services.AppAuthentication` biblioteca em ação:

1. [Utilizar uma identidade de serviço geridas (MSI) para obter um segredo do Cofre de chaves do Azure no tempo de execução](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Implemente um modelo Azure Resource Manager a partir de uma VM do Azure com um MSI através de programação](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Utilize o exemplo .NET Core e o MSI para chamar serviços do Azure a partir de uma VM com Linux do Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Autenticação de desenvolvimento local

Para o desenvolvimento local, existem dois cenários de autenticação primária:

- [Autenticação aos serviços do Azure](#authenticating-to-azure-services)
- [Autenticar para serviços personalizados](#authenticating-to-custom-services)

Aqui, saber os requisitos para cada cenário e ferramentas suportadas.


### <a name="authenticating-to-azure-services"></a>Autenticação aos serviços do Azure

Máquinas locais não suportam a identidade de serviço geridas (MSI).  Como resultado, o `Microsoft.Azure.Services.AppAuthentication` biblioteca utiliza as credenciais de programador para executar no seu ambiente de desenvolvimento local. Quando a solução for implementada para o Azure, a biblioteca utiliza MSI para mudar para um fluxo de concessão de credencial de cliente OAuth 2.0.  Isto significa que pode testar o mesmo código localmente e remotamente sem preocupe.

Para o desenvolvimento local, `AzureServiceTokenProvider` obtém os tokens **Visual Studio**, **interface de linha de comandos do Azure** (CLI), ou **a autenticação integrada do Azure AD**. Cada opção é experimentada sequencialmente e a biblioteca utiliza a primeira opção é concluída com êxito. Se nenhuma opção funciona, um `AzureServiceTokenProviderException` é emitida uma exceção com informações detalhadas.

### <a name="authenticating-with-visual-studio"></a>Autenticar com o Visual Studio

Para utilizar o Visual Studio, certifique-se:

1. Instalou [v15.5 do Visual Studio 2017](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) ou posterior.

2. O [extensão de autenticação da aplicação para o Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) está instalado.
 
3. A sessão iniciada no Visual Studio e tiver selecionado uma conta a utilizar para o desenvolvimento local. Utilize **ferramentas**&nbsp;>&nbsp;**opções**&nbsp;>&nbsp;**autenticação de serviço do Azure**para escolher uma conta de desenvolvimento local. 

Caso se depare com problemas com o Visual Studio, como erros relativos ao ficheiro de fornecedor de tokens, reveja com atenção estes passos. 

Também poderá ser necessário voltar o seu token de programador.  Para fazê-lo, aceda a **ferramentas**&nbsp;>&nbsp;**opções**>**Azure&nbsp;serviço&nbsp;autenticação**  e procure um **novamente autenticado** ligação sob a conta selecionada.  Selecione para se autenticar. 

### <a name="authenticating-with-azure-cli"></a>Autenticação com a CLI do Azure

Para utilizar a CLI do Azure para desenvolvimento local:

1. Instalar [CLI do Azure v2.0.12](/cli/azure/install-azure-cli) ou posterior. Atualize versões anteriores. 

2. Utilize **início de sessão az** para iniciar sessão no Azure.

Utilize `az account get-access-token` para verificar o acesso.  Se receber um erro, certifique-se de que o passo 1 concluída com êxito. 

Se a CLI do Azure não está instalado para o diretório predefinido, poderá receber um erro de relatório que `AzureServiceTokenProvider` não é possível localizar o caminho para a CLI do Azure.  Utilize o **AzureCLIPath**variável de ambiente para definir a pasta de instalação da CLI do Azure. `AzureServiceTokenProvider`Adiciona o diretório especificado no **AzureCLIPath** variável de ambiente para o **caminho** variável de ambiente quando for necessário.

Se tem sessão iniciada a CLI do Azure através de várias contas ou a conta tem acesso a várias subscrições, tem de especificar a subscrição específica a utilizar.  Para tal, utilize:

```
az account set --subscription <subscription-id>
```

Este comando gera saída só em caso de falha.  Para verificar as definições de conta atual, utilize:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Autenticação com a autenticação do Azure AD integrar

Para utilizar autenticação do Azure AD, certifique-se de que:

- O active directory no local [sincronizações para o Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- O código está em execução num computador associado a um domínio.


### <a name="authenticating-to-custom-services"></a>Autenticar para serviços personalizados

Quando um chamadas de serviço, serviços do Azure, os passos anteriores funcionam porque os serviços do Azure permitem o acesso a utilizadores e aplicações.  

Ao criar um serviço que chama um serviço personalizado, utilize credenciais de cliente do Azure AD para autenticação de desenvolvimento local.  Existem duas opções: 

1.  Utilize um principal de serviço para iniciar sessão no Azure:

    1.  [Criar um principal de serviço](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Utilize a CLI do Azure para iniciar sessão:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Uma vez que o principal de serviço pode não ter acesso a uma subscrição, utilize o `--allow-no-subscriptions` argumento.

2.  Utilize as variáveis de ambiente para especificar detalhes de principal de serviço.  Para obter mais informações, consulte [executar a aplicação utilizando um principal de serviço](#running-the-application-using-a-service-principal).

Uma vez que iniciar sessão no Azure, `AzureServiceTokenProvider` utiliza o principal de serviço para obter um token para o desenvolvimento local.

Isto aplica-se apenas ao desenvolvimento local. Quando a sua solução for implementada para o Azure, a biblioteca muda para autenticação de MSI.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Executar a aplicação com uma identidade de serviço geridas 

Quando executar o código de um serviço de aplicações do Azure ou uma VM do Azure com MSI ativada, a biblioteca utiliza automaticamente uma identidade de serviço geridas. Sem alterações de código são necessárias. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Executar a aplicação utilizando um Principal de serviço 

Poderá ser necessário criar uma credencial de cliente do Azure AD para autenticar. Os exemplos comuns incluem:

1. O código é executado no ambiente de desenvolvimento local, mas não na identidade do programador.  Recursos de infraestrutura de serviço, por exemplo, utiliza o [NetworkService conta](/azure/service-fabric/service-fabric-application-secret-management) para o desenvolvimento local.
 
2. O código é executado no ambiente de desenvolvimento local e autenticar para um serviço personalizado, pelo que não é possível utilizar a identidade do programador. 
 
3. O código está em execução num recurso de computação do Azure que ainda não suporta geridos identidade de serviço, como o Azure Batch.

Para utilizar um certificado para iniciar sessão com o Azure AD:

1. Criar um [certificado de serviço principal](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Implementar o certificado como a _LocalMachine_ ou _CurrentUser_ armazenar. 

3. Definir uma variável de ambiente com o nome **AzureServicesAuthConnectionString** para:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Substitua _{AppId}_, _{TenantId}_, e _{Thumbprint}_ com valores gerados no passo 1.

    **CertificateStoreLocation** deve ser _CurrentUser_ ou _LocalMachine_, com base no seu plano de implementação.

4. Execute a aplicação. 

Para iniciar sessão com um Azure AD partilhado secreta credencial:

1. Criar um [principal de serviço com uma palavra-passe](/azure/azure-resource-manager/resource-group-authenticate-service-principal) e conceder acesso ao Cofre de chaves. 

2. Definir uma variável de ambiente com o nome **AzureServicesAuthConnectionString** para:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Substitua _{AppId}_, _{TenantId}_, e _{ClientSecret}_ com valores gerados no passo 1.

3. Execute a aplicação. 

Assim que tudo está configurado corretamente, não existem mais alterações de código são necessários.  `AzureServiceTokenProvider`utiliza a variável de ambiente e o certificado para autenticar com o Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Suporte de cadeia de ligação

Por predefinição, `AzureServiceTokenProvider` utiliza vários métodos para obter um token. 

Para controlar o processo, utilize uma cadeia de ligação transmitida para o `AzureServiceTokenProvider` construtor ou especificado no *AzureServicesAuthConnectionString* variável de ambiente. 

São suportadas as seguintes opções:

| Ligação&nbsp;cadeia&nbsp;opção | Cenário | Comentários|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Desenvolvimento local | AzureServiceTokenProvider utiliza AzureCli ao obter o token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Desenvolvimento local | AzureServiceTokenProvider utiliza o Visual Studio ao obter o token. |
| `RunAs=CurrentUser;` | Desenvolvimento local | AzureServiceTokenProvider utiliza a autenticação integrada do Azure AD para obter o token. |
| `RunAs=App;` | Identidade do Serviço Gerido | AzureServiceTokenProvider utiliza a identidade de serviço geridas ao obter o token. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Principal de serviço | `AzureServiceTokenProvider`utiliza o certificado ao obter o token do Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Principal de serviço | `AzureServiceTokenProvider`utiliza o certificado ao obter o token do Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Principal de serviço |`AzureServiceTokenProvider`utiliza o segredo para obter o token do Azure AD. |


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [identidade de serviço geridas](/azure/app-service/app-service-managed-service-identity).

- Saiba mais formas diferentes de [autenticar e autorizar aplicações](/azure/app-service/app-service-authentication-overview).

- Saiba mais acerca do Azure AD [cenários de autenticação](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).