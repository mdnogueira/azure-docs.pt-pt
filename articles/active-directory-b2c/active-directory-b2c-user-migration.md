---
title: "Do Azure Active Directory B2C: Abordagens de migração de utilizador"
description: "Aborda núcleos e conceitos avançados na migração de utilizador a utilizar a Graph API e, opcionalmente, utilizando as políticas personalizadas do Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: f98f1826b492b8596f352b403b3b12775814c399
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Do Azure Active Directory B2C: Migração de utilizador
Quando migra o fornecedor de identidade para o Azure Active Directory B2C (Azure AD B2C), poderá também ter de migrar a conta de utilizador. Este artigo explica como migrar as contas de utilizador existente a partir de qualquer fornecedor de identidade para o Azure AD B2C. O artigo não se destinar a ser prescritiva, mas, em vez disso, descreve dois várias abordagens. O programador é responsável por à adequação das cada abordagem.

## <a name="user-migration-flows"></a>Fluxos de migração de utilizador
Com o Azure AD B2C, pode migrar utilizadores através de [Graph API](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). O processo de migração de utilizador se enquadrar dois fluxos:

* **Pré-migração**: este fluxo aplica-se quando tiver ou desmarque acesso às credenciais de um utilizador (nome de utilizador e palavra-passe) ou as credenciais estão encriptadas, mas pode desencriptá-los. O processo de pré-migração envolve a leitura os utilizadores a partir do fornecedor de identidade antiga e criar novas contas no diretório do Azure AD B2C.

* **Pré-migração e a palavra-passe da reposição**: este fluxo se aplica quando a palavra-passe de um utilizador não está acessível. Por exemplo:
    * A palavra-passe é armazenada em formato HASH.
    * A palavra-passe é armazenada num fornecedor de identidade que não é possível aceder. O fornecedor de identidade antigo valida as credenciais de utilizador ao chamar um serviço web.

Em ambos os fluxos que primeiro execute o processo de pré-migração, ler os utilizadores a partir do seu fornecedor de identidade antigo e cria novas contas no diretório do Azure AD B2C. Se não tiver a palavra-passe, criar a conta através da utilização de uma palavra-passe que é gerada aleatoriamente. Em seguida, pedir ao utilizador alterar a palavra-passe ou quando o utilizador inicia sessão pela primeira vez, o Azure AD B2C pede ao utilizador para repô-lo.

## <a name="password-policy"></a>Política de palavra-passe
A política de palavra-passe do Azure AD B2C (para contas locais) é baseada na política do Azure AD. O Azure AD B2C se inscrever ou iniciar sessão e palavra-passe de reposição de utilização de políticas de segurança de palavra-passe "segura" e não expirarem as palavras-passe. Para obter mais informações, consulte [política de palavras-passe do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx).

Se as contas que pretende migrar, utilize uma força de palavra-passe mais fraco que o [força de palavra-passe segura imposta pelo Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), pode desativar o requisito de palavra-passe segura. Para alterar a política de palavra-passe predefinida, defina o `passwordPolicies` propriedade `DisableStrongPassword`. Por exemplo, pode modificar o pedido de utilizador de criação da seguinte forma: 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-graph-api-to-migrate-users"></a>Passo 1: Utilizar Graph API para migrar os utilizadores
Criar a conta de utilizador através da Graph API do Azure AD B2C (a palavra-passe ou com uma palavra-passe aleatória). Esta secção descreve o processo de criação de contas de utilizador no diretório do Azure AD B2C ao utilizar a Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Passo 1.1: Registar a sua aplicação no seu inquilino
Para comunicar com a Graph API, primeiro tem de ter uma conta de serviço com privilégios administrativos. No Azure AD, registar uma aplicação e a autenticação para o Azure AD. As credenciais de aplicação são **ID da aplicação** e **segredo da aplicação**. A aplicação funciona como o próprio, não como um utilizador, para chamar a API de gráfico.

Em primeiro lugar, registe a aplicação de migração no Azure AD. Em seguida, crie uma chave de aplicação (segredo da aplicação) e defina a aplicação com privilégios de escrita.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Escolha o seu Azure AD **B2C** inquilino ao selecionar a sua conta na parte superior direito da janela.

3. No painel esquerdo, selecione **do Azure Active Directory** (não do Azure AD B2C). Para localizar, poderá ter de selecionar **mais serviços**.

4. Selecione **registos de aplicação**.

5. Selecione **novo registo de aplicação**.

    ![Novo registo de aplicação](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. Crie uma nova aplicação da seguinte forma:
    * Para **nome**, utilize **B2CUserMigration** ou qualquer outro nome.
    * Para **tipo de aplicação**, utilize **aplicação/API Web**.
    * Para **URL de início de sessão**, utilize **https://localhost** (uma vez que não é relevante para esta aplicação).
    * Selecione **Criar**.

7. Depois da aplicação é criada, no **aplicações** lista, selecione o recém-criado **B2CUserMigration** aplicação.

8. Selecione **propriedades**, copiar o **ID da aplicação**e guarde-o para utilizar mais tarde.

### <a name="step-12-create-the-application-secret"></a>Passo 1.2: Criar o segredo de aplicação
1. No portal do Azure **aplicação registada** janela, selecione **chaves**.

2. Adicionar uma nova chave (também conhecido como um segredo do cliente) e, em seguida, copie a chave para utilização posterior.

    ![ID da aplicação e as chaves](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Passo 1.3: Conceder permissão administrativa para a aplicação
1. No portal do Azure **aplicação registada** janela, selecione **as permissões necessárias**.

2. Selecione **Windows Azure Active Directory**.

3. No **ativar o acesso ao** painel, em **permissões de aplicação**, selecione **leitura e escrita de dados de diretório**e, em seguida, selecione **guardar**.

4. No **as permissões necessárias** painel, selecione **conceder permissões**.

    ![Permissões de aplicação](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Tem agora uma aplicação com permissões para criar, ler e atualizar utilizadores a partir do seu inquilino do Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Passo 1.4: Limpeza ambiente (opcional)
Ler e escrever permissões de dados de diretório *não* inclui o direito para eliminar os utilizadores. Para conceder a aplicação a capacidade de eliminar os utilizadores (limpar o ambiente), tem de efetuar um passo adicional, que envolve a executar o PowerShell para definir as permissões de administrador de conta de utilizador. Caso contrário, pode avançar para a secção seguinte.

> [!IMPORTANT]
> Tem de utilizar uma conta de administrador de inquilino B2C é *local* para o inquilino do B2C. A sintaxe do nome de conta é  *admin@contosob2c.onmicrosoft.com* .

>[!NOTE]
> Requer o seguinte script do PowerShell [do Azure Active Directory PowerShell versão 2](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

Este script do PowerShell, efetue o seguinte:
1. Ligar ao seu serviço online. Para tal, execute o `Connect-AzureAD` cmdlet no Windows PowerShell linha de comandos e forneça as suas credenciais. 

2. Utilize o **ID da aplicação** para atribuir a aplicação a função de administrador de conta de utilizador. Estas funções tem identificadores bem conhecidos, pelo que tudo o que precisa de fazer é introduzir o **ID da aplicação** no script.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Alterar o `$AppId` valor com o seu Azure AD **ID da aplicação**.

## <a name="step-2-pre-migration-application-sample"></a>Passo 2: Exemplo de aplicação de migração de pré-lançamento
[Transferir e executar o código de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Poderá transferi-lo como um ficheiro. zip.

### <a name="step-21-edit-the-migration-data-file"></a>Passo 2.1: Edite o ficheiro de dados de migração
A aplicação de exemplo utiliza um ficheiro JSON que contém dados de utilizador fictício. Depois de executar com êxito o exemplo, pode alterar o código para consumir dados da sua própria base de dados. Ou pode exportar o perfil de utilizador para um ficheiro JSON e, em seguida, definir a aplicação para utilizar esse ficheiro.

Para editar o ficheiro JSON, abra o `AADB2C.UserMigration.sln` solução do Visual Studio. No `AADB2C.UserMigration` projeto, abra o `UsersData.json` ficheiro.

![Ficheiro de dados do utilizador](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Como pode ver, o ficheiro contém uma lista de entidades do utilizador. Cada entidade de utilizador tem as seguintes propriedades:
* Correio eletrónico
* displayName
* Nome próprio
* Apelido
* palavra-passe (pode estar vazio)

> [!NOTE]
> No momento da compilação, o Visual Studio copia o ficheiro para o `bin` diretório.

### <a name="step-22-configure-the-application-settings"></a>Passo 2.2: Configurar as definições da aplicação
Sob o `AADB2C.UserMigration` projeto, abra o *App. config* ficheiro. Substitua as seguintes definições de aplicação com os seus próprios valores:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
    
</appSettings>
```

> [!NOTE]
> * A utilização de uma cadeia de ligação de tabela do Azure é descrita nas secções seguintes.
> * Nome do seu inquilino B2C é o domínio que introduziu durante a criação de inquilino e for apresentado no portal do Azure. O nome do inquilino, normalmente, termina com o sufixo *. onmicrosoft.com* (por exemplo, *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>Passo 2.3: Executar o processo de pré-migração
Clique com botão direito do `AADB2C.UserMigration` solução e, em seguida, a reconstrução de exemplo. Se for bem sucedida, agora, deve ter um `UserMigration.exe` ficheiros executáveis localizados em `AADB2C.UserMigration\bin\Debug`. Para executar o processo de migração, utilize um dos seguintes parâmetros da linha de comandos:

* Para **migrar os utilizadores com palavra-passe**, utilize o `UserMigration.exe 1` comando.

* Para **migrar os utilizadores com palavra-passe aleatória**, utilize o `UserMigration.exe 2` comando. Esta operação também cria uma entidade de tabela do Azure. Posteriormente, deve configurar a política para chamar o serviço de REST API. O serviço utiliza uma tabela do Azure para controlar e gerir o processo de migração.

![Demonstração de processo de migração](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Passo 2.4: Verificar o processo de pré-migração
Para validar a migração, utilize um dos seguintes dois métodos:

* Para procurar um utilizador por nome de apresentação, utilize o portal do Azure:
    
    a. Abra **do Azure AD B2C**e, em seguida, selecione **utilizadores e grupos**.

    b. Na caixa de pesquisa, escreva o nome a apresentar do utilizador e, em seguida, veja o perfil do utilizador. 

* Para obter um utilizador por endereço de correio eletrónico de início de sessão, utilize esta aplicação de exemplo:

    a. Execute o seguinte comando:

    ```Console
        UserMigration.exe 3 {email address}
    ```
        
    > [!TIP]
    > Também pode guardar a saída para um ficheiro JSON utilizando o seguinte comando:
    >
    >```Console
    >    UserMigration.exe 3 {email address} >> UserProfile.json
    >```

    > [!TIP]
    > Também pode obter um utilizador por nome a apresentar, utilizando o seguinte comando: `UserMigration.exe 4 <Display name>`.

    b. Abra o *UserProfile.json* ficheiro num editor de JSON. Com o Visual Studio Code, pode formatar um documento JSON selecionando Shift + Alt + F ou selecionando **formatar documento** no menu de contexto.

    ![O ficheiro UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Passo 2.5: Limpeza ambiente (opcional)
Se pretende limpar até o inquilino do Azure AD e remover utilizadores a partir do diretório do Azure AD, execute o `UserMigration.exe 5` comando.

> [!NOTE]
> * Para limpar o seu inquilino, configure permissões de administrador de conta de utilizador para a sua aplicação.
> * A aplicação de migração de exemplo limpa todos os utilizadores que estão listados no ficheiro de JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Passo 2.6: Inicie sessão com utilizadores migrados (com a palavra-passe)
Depois de executar o processo de pré-migração com palavras-passe do utilizador, as contas, estará pronto a utilizar e os utilizadores poderem iniciar a sua aplicação utilizando o Azure AD B2C. Se não tiver acesso para palavras-passe do utilizador, continue a secção seguinte.

## <a name="step-3-help-users-reset-their-password"></a>Passo 3: Ajudar os utilizadores a repor a palavra-passe
Se migrar os utilizadores com uma palavra-passe aleatória, ter de redefinir a palavra-passe. Para ajudá-lo a repor a palavra-passe, envie um e-mail de boas-vindas com uma ligação para repor a palavra-passe.

Para obter a ligação para a política de reposição de palavra-passe, efetue o seguinte:

1. Selecione **definições do Azure AD B2C**e, em seguida, selecione **Repor palavra-passe** propriedades da política.

2. Selecione a aplicação.

    >[!NOTE]
    >Executar agora requer, pelo menos, uma aplicação para preregistered no inquilino. Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo. 

3. Selecione **executar agora**e, em seguida, verifique a política.

4. No **executar agora endpoint** caixa, copie o URL e, em seguida, envie-o aos seus utilizadores.

    ![Conjunto de registos de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Passo 4: (Opcional) alterar a política para verificar e definir o estado de migração de utilizador

> [!NOTE]
> Para ver e alterar o estado de migração de utilizador, tem de utilizar uma política personalizada. Para obter mais informações, consulte [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
>

Quando os utilizadores tentam iniciar sessão sem uma reposição de palavra-passe pela primeira vez, a política deverá devolver uma mensagem de erro amigável. Por exemplo: 
>*A palavra-passe expirou. Para o fazer, selecione a ligação de reposição de palavra-passe.* 

Este passo opcional requer a utilização de políticas personalizadas do Azure AD B2C, conforme descrito no [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

Nesta secção, pode alterar a política para verificar o estado de migração de utilizador no início de sessão. Se o utilizador não tiver de alterar a palavra-passe, devolver uma mensagem de erro de HTTP 409 que pede ao utilizador para selecionar o **esqueceu a palavra-passe?** ligação. 

Para controlar a alteração de palavra-passe, pode utilizar uma tabela do Azure. Quando executa o processo de pré-migração com o parâmetro da linha de comandos `2`, criar uma entidade de utilizador numa tabela do Azure. O serviço faz o seguinte:

* No início de sessão, a política do Azure AD B2C invoca a serviço RESTful, enviar uma mensagem de e-mail como uma afirmação de entrada de migração. O serviço de pesquisa para o endereço de e-mail na tabela do Azure. Se o endereço, o serviço emite uma mensagem de erro: *tem de alterar palavra-passe*.

* Depois do utilizador altera com êxito a palavra-passe, remova a entidade da tabela do Azure.

>[!NOTE]
>Utilizamos uma tabela do Azure para simplificar a amostra. Pode armazenar o estado de migração em qualquer base de dados ou como uma propriedade personalizada na conta do Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: atualizar a definição de aplicação
1. Para testar a demonstração de RESTful API, abra o `AADB2C.UserMigration.sln` solução do Visual Studio no Visual Studio. 

2. No `AADB2C.UserMigration.API` projeto, abra o *App. config* ficheiro. Substitua a definição de aplicação com o seu próprio valor:

    ```XML
    <appSettings>
        <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
    </appSettings>
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Passo 4.2: Implementar a aplicação de web no App Service do Azure
Publica o seu serviço de API no App Service do Azure. Para obter mais informações, consulte [implementar a aplicação no App Service do Azure](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy).

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Passo 4.3: Adicionar um perfil técnica e a validação do perfil técnica para a política 
1. No seu diretório de trabalho, abra o *TrustFrameworkExtensions.xml* ficheiro de política de extensão. 

2. Procure o `<ClaimsProviders>` nó e, em seguida, no nó, adicione o seguinte fragmento XML. Não se esqueça de alterar o valor de `ServiceUrl` para apontar para o seu URL de ponto final. 

    ```XML
    <ClaimsProvider>
        <DisplayName>REST APIs</DisplayName>
        <TechnicalProfiles>
    
        <TechnicalProfile Id="LocalAccountSignIn">
            <DisplayName>Local account just in time migration</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    
        <TechnicalProfile Id="LocalAccountPasswordReset">
            <DisplayName>Local account just in time migration</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    
    <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
    
        <!-- This technical profile uses a validation technical profile to authenticate the user. -->
        <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    
        <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
            <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

O perfil de técnico anterior define uma afirmação de entrada: `signInName` (enviar como e-mail). No início de sessão, a afirmação é enviada para o ponto final de RESTful.

Depois de definir o perfil técnico para a API RESTful, indique a política do Azure AD B2C para chamar o perfil técnico. Substitui o fragmento XML `SelfAsserted-LocalAccountSignin-Email`, que está definido na política de base. O fragmento XML também adiciona `ValidationTechnicalProfile`, com ReferenceId apontar para o seu perfil técnica `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Passo 4.4: Carregar a política para o seu inquilino
1. No [portal do Azure](https://portal.azure.com), mude para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, selecione **do Azure AD B2C**.

2. Selecione **identidade experiência Framework**.

3. Selecione **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política se existir** caixa de verificação.

6. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e certifique-se de que transfere a validação.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Passo 4.5: Testar a política personalizada utilizando o executar agora
1. Selecione **definições do Azure AD B2C**e, em seguida, aceda a **identidade experiência Framework**.

2. Abra **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) entidade confiadora que carregado e, em seguida, selecione **executar agora**.

3. Tente iniciar sessão com uma das credenciais de utilizadores migrados e, em seguida, selecione **sessão**. A API REST deve acionar a seguinte mensagem de erro:

    ![Conjunto de registos de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Passo 4.6: (Opcional) a API REST de resolução de problemas
Pode ver e monitorizar as informações de registo em tempo quase real.

1. No menu de definições da sua aplicação RESTful, sob **monitorização**, selecione **registos de diagnóstico**. 

2. Definir **registo de aplicações (sistema de ficheiros)** para **no**. 

3. Definir o **nível** para **verboso**.

4. Selecione **guardar**

    ![Conjunto de registos de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. No **definições** menu, selecione **sequência do registo**.

6. Verifique o resultado da RESTful API.

Para obter mais informações, consulte [a consola e registos de transmissão em fluxo](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console).

> [!IMPORTANT]
> Utilize os registos de diagnóstico apenas durante o desenvolvimento e teste. O resultado de RESTful API poderá conter informações confidenciais que não devem ser expostas na produção.
>

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Transferir os ficheiros de política concluída
Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) explicação passo a passo, recomendamos que crie o seu cenário utilizando os seus próprios ficheiros de política personalizada. Para sua referência, fornecemos [ficheiros de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). 
