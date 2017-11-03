---
title: "O Azure Active Directory B2C: Personalizar dinamicamente a interface de utilizador (IU) do Azure AD B2C, utilizando as políticas personalizadas"
description: "Suporta várias experiências de imagem corporativa com conteúdo de HTML5/CSS que alterado de forma dinâmica no tempo de execução."
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
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: fffb6c82b2e04976c420fba07bbcf967ffd25929
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>O Azure Active Directory B2C: Configurar a IU com conteúdo dinâmico utilizando as políticas personalizadas
Ao utilizar o Azure Active Directory B2C políticas personalizadas (Azure AD B2C), que pode enviar um parâmetro por uma cadeia de consulta. Transferindo o parâmetro para o ponto final de HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou início de sessão do Azure AD B2C, com base num parâmetro que passa a partir do seu web ou aplicação móvel. 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo incida no como personalizar a interface de utilizador do Azure AD B2C com *conteúdo dinâmico* utilizando as políticas personalizadas. Para começar a utilizar, consulte o artigo [personalização de IU numa política personalizada do](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>O artigo do Azure AD B2C, [personalização de IU configurar numa política personalizada do](active-directory-b2c-ui-customization-custom.md), descreve as noções básicas sobre o seguintes:
> * Funcionalidade de personalização de interface (IU) do utilizador de página.
> * Ferramentas essenciais para testar a funcionalidade da página IU personalização utilizando o nosso conteúdo de exemplo.
> * Os elementos de IU de núcleo de cada tipo de página.
> * Melhores práticas para aplicar esta funcionalidade.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Adicionar uma ligação para modelos de HTML5/CSS da sua viagem de utilizador

Numa política personalizada, uma definição de conteúdo define a página de HTML5 URI que é utilizado para um passo de IU especificado (por exemplo, as páginas de início de sessão ou inscrição). A política base define o aspeto e funcionalidade do predefinido por apontar para um URI de HTML5 ficheiros (CSS). Na política de extensão, pode modificar o aspeto e funcionalidade através da substituição LoadUri para o ficheiro HTML5. Definições de conteúdo contém URLs para conteúdo externo que é definido pela composição ficheiros HTML5/CSS, conforme apropriado. 

O `ContentDefinitions` secção contém uma série de `ContentDefinition` elementos XML. O atributo ID do `ContentDefinition` elemento Especifica o tipo de página que está relacionada com a definição de conteúdo. Ou seja, o elemento define o contexto de um modelo personalizado do HTML5/CSS vai aplicar. A tabela seguinte descreve o conjunto de definição de conteúdo IDs que são reconhecidos pelo motor de IEF e os tipos de página que referem-se aos mesmos.

| ID de definição de conteúdo | Modelo de HTML5 predefinido| Descrição | 
|-----------------------|--------|-------------|
| *API.Error* | [Exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Esta página é apresentada quando é encontrado uma excepção ou um erro. |
| *API.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção de fornecedor de identidade**. Esta página apresenta uma lista de fornecedores de identidade que os utilizadores podem escolher durante o início de sessão. As opções são, normalmente, fornecedores de identidade empresarial, os fornecedores de identidade de redes sociais, como o Facebook e Google + ou contas locais. |
| *API.idpselections.Signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de fornecedor de identidade para inscrição**. Esta página apresenta uma lista de fornecedores de identidade que os utilizadores, podem escolher de entre durante a inscrição. As opções são o fornecedores de identidade empresarial, os fornecedores de identidade de redes sociais como o Facebook e Google + ou contas locais. |
| *API.localaccountpasswordreset* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Se esqueceu a página de palavra-passe**. Esta página contém um formulário que os utilizadores tem de concluir para iniciar uma reposição de palavra-passe.  |
| *API.localaccountsignin* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de início de sessão da conta local**. Esta página contém um formulário para iniciar sessão com uma conta local, com base no endereço de e-mail ou um nome de utilizador. O formulário pode conter uma caixa de entrada de texto e a caixa de entrada de palavra-passe. |
| *API.localaccountsignup* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição conta local**. Esta página contém um formulário para inscrever-se de uma conta local é baseada num endereço de e-mail ou um nome de utilizador. O formulário pode conter vários controlos de entrada, tais como: um texto de entrada caixa, uma caixa de entrada de palavra-passe, um botão de opção, as caixas de lista pendente de selecção única e caixas de verificação de seleção múltipla. |
| *API.phonefactor* | [1.0.0.cshtml multifator](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator**. Nesta página, os utilizadores podem verificar os respetivos números de telefone (utilizando o texto ou de voz) durante a inscrição ou início de sessão. |
| *API.selfasserted* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de redes sociais conta**. Esta página contém um formulário que os utilizadores terão de concluir quando inscrever utilizando uma conta existente de um fornecedor de identidade de redes sociais. Esta página é semelhante à anterior conta sociais página de inscrição, exceto para os campos de entrada de palavra-passe. |
| *API.selfasserted.profileupdate* | [updateprofile.HTML](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os utilizadores podem aceder ao atualizar o respetivo perfil. Esta página é semelhante para a página de inscrição de conta de redes sociais, exceto para os campos de entrada de palavra-passe. |
| *API.signuporsignin* | [Unified.HTML](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou início de sessão unificada**. Esta página processa o processo de inscrição e o início de sessão do utilizador. Os utilizadores podem utilizar fornecedores de identidade empresarial, fornecedores de identidade de redes sociais, como o Facebook Google + ou para contas locais.  |

## <a name="serving-dynamic-content"></a>Que serve o conteúdo dinâmico
No [personalização de IU configurar numa política personalizada do](active-directory-b2c-ui-customization-custom.md) artigo, carregar ficheiros HTML5 para o Blob storage do Azure. Esses ficheiros HTML5 são estáticos e compor HTML mesmo conteúdo para cada pedido. 

Neste artigo, utilize uma aplicação web do ASP.NET, que pode aceitar parâmetros de cadeia de consulta e respondem em conformidade. 

Esta explicação passo a passo, poderá:
* Crie uma aplicação web ASP.NET Core que aloja os modelos de HTML5. 
* Adicionar um modelo personalizado HTML5, _unified.cshtml_. 
* Publicar a aplicação web no App Service do Azure. 
* Conjunto de recursos de várias origens (CORS) de partilha para a sua aplicação web.
* Substituir o `LoadUri` elementos para apontar para o ficheiro HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Passo 1: Criar uma aplicação web ASP.NET

1. No Visual Studio, crie um projeto selecionando **ficheiro** > **novo** > **projeto**.

2. No **novo projeto** janela, selecione **Visual c#** > **Web** > **(.NET Core)umaaplicaçãoWebASP.NETCore**.

3. Nome da aplicação (por exemplo, *Contoso.AADB2C.UI*) e, em seguida, selecione **OK**.

    ![Criar novo projeto do Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Selecione o **aplicação Web** modelo.

5. Definir a autenticação **sem autenticação**.

    ![Selecione o modelo de aplicação Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Selecione **OK** para criar o projeto.

## <a name="step-2-create-mvc-view"></a>Passo 2: Criar vista MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Passo 2.1: Transferir o modelo de HTML5 incorporado do B2C
O modelo personalizado do HTML5 baseia-se no modelo de HTML5 incorporado do Azure AD B2C. Pode transferir o [unified.html ficheiro](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) ou transferir o modelo de [pacote starter](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Utilize este ficheiro HTML5 para criar uma página de inscrição ou início de sessão unificada.

### <a name="step-22-add-the-mvc-view"></a>Passo 2.2: Adicionar a vista MVC
1. Clique com o botão direito na pasta de vistas moradas particulares e, em seguida, **adicionar** > **Novo Item**.

    ![Adicionar novo item MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. No **adicionar o novo Item - Contoso.AADB2C.UI** janela, selecione **Web > ASP.NET**.

3. Selecione **MVC visualizar a página**.

4. No **nome** caixa, altere o nome **unified.cshtml**.

5. Selecione **Adicionar**.

    ![Adicionar vista MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Se o *unified.cshtml* ficheiro já não está aberto, faça duplo clique no ficheiro para abri-lo e, em seguida, desmarque o conteúdo do ficheiro.

7. Nestas instruções, iremos remover a referência à página de esquema. Adicione o seguinte fragmento de código para _unified.cshtml_:

    ```C#
    @{
        Layout = null;
    }
    ```

8. Abra o _unified.cshtml_ ficheiro transferiu a partir do modelo de HTML5 incorporado do Azure AD B2C.

9. Substitua o único arroba (@) com o valor de duplo arroba (@).

10. Copiar o conteúdo do ficheiro e cole-a abaixo da definição de esquema. O código deve ter o seguinte aspeto:

    ![ficheiro Unified.cshtml depois de adicionar o HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Passo 2.3: Alterar a imagem de fundo

Localize o `<img>` elemento que contém o `ID` valor *background_background_image*e, em seguida, substitua o `src` valor com **https://kbdevstorage1.blob.core.windows.net/ Asset-blobs/19889_en_1** ou qualquer outra imagem de fundo que pretende utilizar.

![Alterar o fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Passo 2.4: Adicionar vista para o controlador MVC

1. Abra **Controllers\HomeController.cs**e adicione o seguinte método: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Este código especifica qual o método, deve utilizar um *vista* ficheiro de modelo para compor uma resposta para o browser. Porque não especificamos explicitamente o nome do *vista* ficheiro de modelo MVC por predefinição, utiliza o _unified.cshtml_ ficheiro vista na */vistas/Home* pasta.
    
    Depois de adicionar o _unificada_ método, o código deve ter o seguinte aspeto:
    
    ![Alterar o controlador para compor a vista](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Depurar a aplicação web e certifique-se de que o _unificada_ página está acessível (por exemplo, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Passo 2.5: Publicar no Azure
1. No **Explorador de soluções**, clique com botão direito do **Contoso.AADB2C.UI** projeto e, em seguida, selecione **publicar**.

    ![Publicar no serviço de aplicações do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Selecione o **App Service do Microsoft Azure** mosaico e, em seguida, selecione **publicar**.

    ![Criar novo serviço de aplicações do Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    O **criar App Service** é aberta a janela. No mesmo pode começar a criar todos os recursos do Azure necessários para executar a aplicação web do ASP.NET no Azure.

    > [!NOTE]
    > Para obter mais informações sobre a publicação, consulte [criar uma aplicação web ASP.NET no Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. No **nome da aplicação Web** caixa, escreva um nome de aplicação único (carateres válidos são a-z, A-Z, 0-9 e hífen (-). O URL da aplicação Web é `http://<app_name>.azurewebsites.NET`, onde `<app_name>` é o nome da aplicação Web. Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

4. Selecione **Criar** para começar a criar os recursos do Azure.

    ![Forneça as propriedades do serviço de aplicações](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Depois do processo de criação estiver concluído, o assistente publica a aplicação web do ASP.NET para o Azure e, em seguida, inicia a aplicação no browser predefinido.

5. Copie o URL do _unificada_ página (por exemplo, _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Passo 3: Configurar a CORS no App Service do Azure
1. No [portal do Azure](https://portal.azure.com/), selecione **serviços aplicacionais**e, em seguida, selecione o nome da sua aplicação API.

    ![Selecione a aplicação API no portal do Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. No **definições** secção em **API** secção, selecione **CORS**.

    ![Selecione as definições CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. No **CORS** janela, no **origens permitidas** caixa, efetue um dos seguintes procedimentos:

    * Introduza o URL ou URLs que pretende receber chamadas JavaScript provenientes.
    * Introduza um asterisco (*) para especificar que todos os domínios de origem são aceites.

4. Selecione **Guardar**.

    ![A janela CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Depois de selecionar **guardar**, a aplicação API aceita chamadas JavaScript a partir de URLs especificados. 

## <a name="step-4-html5-template-validation"></a>Passo 4: HTML5 validação de modelo
O modelo de HTML5 está pronto a utilizar. No entanto, não está disponível a `ContentDefinition` código. Antes de poder adicionar `ContentDefinition` para a política personalizada, certifique-se de que:
* O conteúdo é HTML5 acessível e não compatíveis.
* O servidor de conteúdo está ativado para CORS.

    >[!NOTE]
    >Para verificar se o site onde está a alojar o conteúdo tiver ativado a CORS e pode testar a pedidos CORS, vá para o [teste cors.org](http://test-cors.org/) Web site. 

* O conteúdo servido é seguro através de **HTTPS**.
* Estiver a utilizar *URLS absolutos*, tais como *https://yourdomain/content*para todas as ligações, conteúdo do CSS e imagens.

## <a name="step-5-configure-your-content-definition"></a>Passo 5: Configurar a definição de conteúdo
Para configurar `ContentDefinition`, efetue o seguinte procedimento:
1. Abra o ficheiro de base da sua política (por exemplo, *TrustFrameworkBase.xml*).

2. Procure o `<ContentDefinitions>` elemento e, em seguida, copie todo o conteúdo do `<ContentDefinitions>` nós.

3. Abra o ficheiro de extensão (por exemplo, *TrustFrameworkExtensions.xml*) e, em seguida, procure o `<BuildingBlocks>` elemento. Se o elemento não existir, adicioná-lo.

4. Cole os conteúdos integrais do `<ContentDefinitions>` nó que copiou como subordinado do `<BuildingBlocks>` elemento. 

5. Procure o `<ContentDefinition>` nó que contém `Id="api.signuporsignin"` no XML que copiou.

6. Altere o valor de `LoadUri` de _~/tenant/default/unified_ para _https://<app_name>.azurewebsites.net/home/unified_.  
    A política personalizada deve ter o seguinte aspeto:
    
    ![A definição de conteúdo](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Passo 6: Carregar a política para o seu inquilino
1. No [portal do Azure](https://portal.azure.com), mude para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, selecione **do Azure AD B2C**.

2. Selecione **identidade experiência Framework**.

3. Selecione **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política se existir** caixa de verificação.

6. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e certifique-se de que transfere a validação.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Passo 7: Testar a política personalizada utilizando o executar agora
1. Selecione **definições do Azure AD B2C**e, em seguida, selecione **identidade experiência Framework**.

    >[!NOTE]
    >Executar agora requer, pelo menos, uma aplicação para preregistered no inquilino. Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Abra **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) entidade confiadora que carregado e, em seguida, selecione **executar agora**.  
    Deve conseguir ver o seu HTML5 personalizadas com a imagem de fundo que criou anteriormente.

    ![A política de inscrição ou início de sessão](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Passo 8: Adicionar conteúdo dinâmico
Alterar o fundo com base no parâmetro de cadeia de consulta com o nome _campaignId_. A aplicação de RP (web apps e móveis) envia o parâmetro para o Azure AD B2C. A política lê o parâmetro e envia o respetivo valor para o modelo de HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Passo 8.1: Adicionar um parâmetro de definição de conteúdo

Adicionar o `ContentDefinitionParameters` elemento efetuando o seguinte procedimento:
1. Abra o *SignUpOrSignin* ficheiros da sua política (por exemplo, *SignUpOrSignin.xml*).

2. Procure o `<DefaultUserJourney>` nós. 

3. No `<DefaultUserJourney>` nó, adicione o seguinte fragmento XML:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Passo 8.2: Alterar o código para aceitar um parâmetro de cadeia de consulta e substitua a imagem de fundo
Modificar o HomeController `unified` método para aceitar o parâmetro campaignId. O método, em seguida, verifica o parâmetro de valor e define o `ViewData["background"]` variável em conformidade.

1. Abra o *Controllers\HomeController.cs* de ficheiros e, em seguida, altere o `unified` método adicionando o seguinte fragmento de código:

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Localize o `<img>` elemento com o ID `background_background_image`e substitua o `src` valor com `@ViewData["background"]`.

    ![Alterar o fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: carregar as alterações e publicar a política
1. Publica o projeto do Visual Studio no App Service do Azure.

2. Carregar o *SignUpOrSignin.xml* política para o Azure AD B2C.

3. Abra **B2C_1A_signup_signin**, a política personalizada do RP carregado e, em seguida, selecione **executar agora**.  
    Deverá ver a imagem de fundo mesmo que foi apresentada anteriormente.

4. Copie o URL da barra de endereço do browser.

5. Adicionar o _campaignId_ parâmetro de cadeia para o URI de consulta. Por exemplo, adicionar `&campaignId=hawaii`, conforme mostrado na imagem seguinte:

    ![Alterar o fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Selecione **Enter** para apresentar a imagem de fundo Hawaii.

    ![Alterar o fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Altere o valor para *Tóquio*e, em seguida, selecione **Enter**.  
    O browser apresenta a imagem de fundo Tóquio.

    ![Alterar o fundo da página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Passo 9: Altere o resto do journey do utilizador
Se selecionar a **inscrever-se agora** ligação na página início de sessão, o browser apresenta a imagem de fundo predefinido, não a imagem definido. Este comportamento seja porque alterou apenas a página de inscrição ou início de sessão. Para alterar o resto das definições de conteúdo Assert automática:
1. Volte ao "Passo 2" e efetue o seguinte:

    a. Transferir o *selfasserted* ficheiro.

    b. Copie o conteúdo do ficheiro.

    c. Criar uma nova vista, *selfasserted*.

    d. Adicionar *selfasserted* para o **home page** controlador.

2. Volte ao "Passo 4" e efetue o seguinte: 

    a. A política de extensão, localize o `<ContentDefinition>` nó que contém `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, e `Id="api.localaccountpasswordreset"`.

    b. Definir o `LoadUri` atributo para o *selfasserted* URI.

3. Volte ao "Passo 8.2" e alterar o código para aceitar parâmetros de cadeia de consulta, mas desta vez para o *selfasserted* função. 

4. Carregar o *TrustFrameworkExtensions.xml* política e certifique-se de que transfere a validação.

5. Execute o teste de política e, em seguida, selecione **inscrever-se agora** para ver o resultado.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Transferir os ficheiros de política concluída e o código
* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) explicação passo a passo, recomendamos que crie o seu cenário utilizando os seus próprios ficheiros de política personalizada. Para sua referência, fornecemos [ficheiros de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Pode transferir o código de conclusão de [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




