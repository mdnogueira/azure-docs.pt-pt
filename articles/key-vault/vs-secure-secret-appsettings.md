---
title: "Em segurança guardar as definições de aplicação secreta para uma aplicação web | Microsoft Docs"
description: "Como de forma segura guardar as definições da aplicação secreta como credenciais do Azure ou de terceiros API chaves utilizando o ASP.NET core fornecedor do Cofre de chaves, o segredo de utilizador ou o .NET 4.7.1 construtores de configuração"
services: visualstudio
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: 
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: 3284f9f9c3cef27cba599238f06b0dcf0f35de78
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Em segurança guardar as definições de aplicação secreta para uma aplicação web

## <a name="overview"></a>Descrição geral
Este artigo descreve como guardar em segurança as definições de configuração do segredo de aplicação para aplicações do Azure.

Tradicionalmente, todos os web configuração da aplicação definições são guardadas em ficheiros de configuração, tais como Web. config. Esta prática leva a verificação em segredo definições, tais como credenciais de nuvem para sistemas de controlo de origem pública, como o Github. Entretanto, pode ser difícil de seguir a melhor prática de segurança devido a sobrecarga necessária para alterar o código de origem e reconfigure as definições de desenvolvimento.

Para certificar-se de que o processo de desenvolvimento é seguro, bibliotecas de ferramentas e a arquitetura são criadas para guardar as definições segredo da aplicação em segurança com o mínimo ou nenhuma alteração do código de origem.

## <a name="aspnet-and-net-core-applications"></a>Aplicações de núcleo ASP.NET e o .NET

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Guardar definições secretas no arquivo do segredo do utilizador que está fora da pasta de controlo de origem
Se estão a fazer o protótipo um rápida ou não tem acesso à internet, comece com mover as definições de secretas fora da pasta de controlo de origem para o arquivo do segredo do utilizador. Arquivo de segredos do utilizador é um ficheiro guardado na pasta do gerador de perfis de utilizador, pelo que não são verificados segredos ao controlo de origem. O diagrama seguinte demonstra como [utilizador segredo](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager) funciona.

![Segredo do utilizador mantém as definições de secretas fora do controlo de código fonte](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Se estiver a executar aplicações de consola .NET core, utilize o Cofre de chaves para guardar o seu segredo em segurança.

### <a name="save-secret-settings-in-azure-key-vault"></a>Guardar definições segredas no Cofre de chaves do Azure
Se estiver a desenvolver um projeto de equipa e precisar de partilhar de forma segura o código de origem, utilize [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/).

1. Crie um cofre de chaves na sua subscrição do Azure. Preencha todos os campos obrigatórios na IU e clique em *criar* na parte inferior do painel

    ![Criar Cofre de chaves do Azure](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Conceda o acesso de membros de equipa e para o Cofre de chaves. Se tiver uma equipa grande, pode criar um [grupo do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-groups-create-azure-portal) e adicione esse grupo de segurança acesso para o Cofre de chaves. No *segredo permissões* lista pendente, verificação *obter* e *lista* em *operações de gestão do segredo*.

    ![Adicionar política de acesso do Cofre de chaves](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adicione o seu segredo ao Cofre de chaves no portal do Azure. Para definições de configuração aninhados, substitua ':' com '-' para o nome secreto do Cofre de chaves é válido. ':' não tem permissão para ser o nome de um cofre de chaves secretas.

    ![Adicione o segredo do Cofre de chaves](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Instalar o [extensão de autenticação de serviços do Azure para Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). Através desta extensão, aplicação pode aceder ao Cofre de chaves utilizando a identidade do início de sessão no Visual Studio.

5. Adicione os seguintes pacotes de NuGet ao seu projeto:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Adicione o seguinte código ao ficheiro Program.cs:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Adicione o URL do Cofre de chave de ficheiro launchsettings.json. O nome de variável de ambiente *KEYVAULT_ENDPOINT* está definida no código adicionado no passo 6.

    ![Adicionar o URL do Cofre de chave como uma variável de ambiente de projeto](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Inicie a depuração do projeto. Deve ser executada com êxito.

## <a name="aspnet-and-net-applications"></a>Aplicações ASP.NET e o .NET

.NET 4.7.1 suporta construtores de configuração do Cofre de chaves e o segredo, que garante que podem ser movidos segredos fora da pasta de controlo de origem sem alterações de código.
Para continuar, [transferir .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) e migrar a sua aplicação, se estiver a utilizar uma versão mais antiga do .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Guardar definições secretas num ficheiro secreto que está fora da pasta de controlo de origem
Se estiver a escrever um protótipo rápido e não pretender aprovisionar os recursos do Azure, visite com esta opção.

1. Instalar o seguinte pacote NuGet ao seu projeto
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Crie um ficheiro que é semelhante a seguir. Guardá-lo sob uma localização fora da pasta do projeto.

    ```xml

       <root>
              <secrets ver="1.0">
                     <secret name="secret1" value="foo_one" />
                        <secret name="secret2" value="foo_two" />
                       </secrets>
      </root>
      ```

3. Defina o ficheiro secreto para ser um construtor de configuração no ficheiro Web. config. Colocar esta secção antes de *appSettings* secção.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Especificar a secção appSettings está a utilizar o construtor de configuração secreta. Certifique-se de que não existe qualquer entrada para a definição secreta com um valor fictício.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Depurar a aplicação. Deve ser executada com êxito.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Guardar definições secretas num cofre de chaves do Azure
Siga as instruções da secção de núcleo ASP.NET para configurar um cofre de chaves para o seu projeto.

1. Instalar o seguinte pacote NuGet ao seu projeto
```
Microsoft.Configuration.ConfigurationBuilders.Azure.1.0.0-alpha1.nupkg
```

2. Defina o construtor de configuração do Cofre de chaves em Web. config. Colocar esta secção antes de *appSettings* secção. Substitua *vaultName* para ser o nome do Cofre de chaves se tiver o seu Cofre de chaves no Azure público ou URI completo se estiver a utilizar Sovereign na nuvem.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="KeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3.  Especificar a secção appSettings está a utilizar o construtor de configuração do Cofre de chaves. Certifique-se de que não existe qualquer entrada para a definição secreta com um valor fictício.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Inicie a depuração do projeto. Deve ser executada com êxito.
