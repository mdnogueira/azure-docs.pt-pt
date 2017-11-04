---
title: "Personalização de interface (IU) do utilizador - do Azure AD B2C | Microsoft Docs"
description: "Um tópico sobre as funcionalidades de personalização de interface (IU) do utilizador no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 99f5a391-5328-471d-a15c-a2fafafe233d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: 37e9f06555063ceea00b9162c79344130414b82e
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>O Azure Active Directory B2C: Personalizar a interface de utilizador (IU) do Azure AD B2C

Experiência de utilizador é essencial no cliente com acesso à aplicação.  Aumente o seu cliente base por experiências de utilizador com o aspeto e funcionalidade da sua marca de composição. O Azure Active Directory B2C (Azure AD B2C) permite-lhe personalizar a edição do perfil de inscrição, início de sessão, e páginas com controlo de pixel perfeita de reposição de palavra-passe.

> [!NOTE]
> A funcionalidade da página IU personalização descrita neste artigo não se aplica a política de apenas início de sessão, o associada página de reposição de palavra-passe e verificação e-mails.  Utilizam estas funcionalidades de [corporativa funcionalidade](../active-directory/active-directory-add-company-branding.md) em vez disso.
>
> Da mesma forma, se um utilizador intiates uma política de perfil de edição *antes* iniciar sessão, o utilizador será redirecionado para uma página que pode ser personalizada utilizando a [corporativa funcionalidade](../active-directory/active-directory-add-company-branding.md).

Este artigo abrange os seguintes tópicos:

* A funcionalidade de personalização do página IU.
* Uma ferramenta para carregar conteúdo HTML para o Blob Storage do Azure para utilização com a funcionalidade da página IU personalização.
* Elementos de IU utilizados pelo Azure AD B2C, pode personalizar utilizando folhas de estilo cascata (CSS).
* Melhores práticas quando exercising esta funcionalidade.

## <a name="the-page-ui-customization-feature"></a>A funcionalidade de personalização de IU da página

Pode personalizar o aspeto e funcionalidade de palavra-passe inscrição, início de sessão, de cliente reposição e editar o perfil de páginas (configurando [políticas](active-directory-b2c-reference-policies.md)). Os seus clientes obtém uma experiência totalmente integrada quando navegar entre a sua aplicação e páginas servidas pelo Azure AD B2C.

Ao contrário de outros serviços onde as opções de IU, Azure AD B2C utiliza uma abordagem simple e moderna para personalização de IU.

Eis como funciona: Azure AD B2C executa o código no browser do cliente e utiliza uma abordagem moderna chamada [de partilha de recursos de várias origens (CORS)](http://www.w3.org/TR/cors/).  Durante a execução, o conteúdo é carregado a partir de um URL que especificar numa política. Pode especificar diferentes URLs para páginas diferentes. Depois do conteúdo carregado a partir do seu URL intercalado com um fragmento HTML inserido do Azure AD B2C, é apresentada a página para o seu cliente. Tudo o que precisa de fazer é:

1. Criar HTML5 formado conteúdo com vazio `<div id="api"></div>` elemento localizado algures no `<body>`. Esta marca de elemento onde é inserido o conteúdo do Azure AD B2C.
1. Aloje o conteúdo num ponto final HTTPS (com a CORS permitida). Tenha em atenção que ambos obter e métodos de pedido de opções devem ser ativados quando configurar a CORS.
1. Utilize o CSS para os elementos da IU do Azure AD B2C insere de estilo.

### <a name="a-basic-example-of-customized-html"></a>Um exemplo básico da HTML personalizado

O exemplo seguinte é o mais básico conteúdo HTML que pode utilizar para testar esta capacidade. Utilize o [ferramenta auxiliar](active-directory-b2c-reference-ui-customization-helper-tool.md) para carregar e configurar este conteúdo no seu armazenamento de Blobs do Azure. Pode, em seguida, certifique-se de que o botões básicos, não stylized & campos de formulário em cada página são apresentados e funcional.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>Testar a funcionalidade de personalização da IU

Pretende experimentar a funcionalidade da IU a personalização utilizando o nosso exemplo HTML e conteúdo do CSS?  Fornecemos [uma ferramenta de programa auxiliar](active-directory-b2c-reference-ui-customization-helper-tool.md) que carrega e configura o conteúdo de exemplo no seu armazenamento de Blobs do Azure.

> [!NOTE]
> Pode alojar o conteúdo de IU em qualquer lugar: nos servidores web, CDNs, AWS S3, sistemas de partilha de ficheiros, etc. Desde que o conteúdo estiver alojado num ponto final de HTTPS publicamente disponível com a CORS ativada, está pronto para continuar. Utilizamos Blob storage do Azure ilustrativa apenas para efeitos.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>Os fragmentos de IU incorporados pelo Azure AD B2C

As secções seguintes listam os fragmentos de HTML5 do Azure AD B2C une no `<div id="api"></div>` elemento localizado no seu conteúdo. **Não é inserir estes fragmentos no seu conteúdo HTML 5.** O serviço do Azure AD B2C insere-os em tempo de execução. Utilize estes fragmentos como uma referência ao conceber a sua própria folhas de estilo cascata (CSS).

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>Fragmento inserido na "página de identidade fornecedor seleção"

Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher durante a inscrição ou início de sessão. Estes botões incluem fornecedores de identidade de redes sociais, como o Facebook e Google + ou contas locais (com base no nome de utilizador ou endereço de e-mail).

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>Fragmento inserido "conta Local página de inscrição"

Esta página contém um formulário de inscrição de conta local, com base num endereço de e-mail ou um nome de utilizador. O formulário pode conter controlos de entrada diferentes, tais como a caixa de entrada de texto, caixa de entrada de palavra-passe, botão de opção, as caixas de lista pendente de selecção única e selecionar vários caixas de verificação.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>Fragmento inserido "" conta de redes sociais página de inscrição"

Nesta página pode aparecer quando inscrever-se com uma conta existente de um fornecedor de identidade de redes sociais, como o Facebook ou Google +.  É utilizado quando têm de ser recolhidas informações adicionais do utilizador final a utilizar um formulário de inscrição. Esta página é semelhante à conta local página de inscrição (mostrada na secção anterior) com a exceção os campos de entrada de palavra-passe.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Fragmento inserido "Unified se inscrever ou iniciar sessão página"

Esta página processa tanto inscrição & início de sessão de clientes, que podem utilizar fornecedores de identidade de redes sociais, como o Facebook Google + ou para contas locais.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>Fragmento inserido na "página do multi-factor authentication"

Nesta página, os utilizadores podem verificar os respetivos números de telefone (utilizando o Editor de texto ou de voz) durante a inscrição ou início de sessão.

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>Fragmento inserido "" página de erro"

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>Localizing conteúdo HTML

Pode localizar o conteúdo HTML ao ativar ['Personalização de idioma'](active-directory-b2c-reference-language-customization.md).  Ativar esta funcionalidade permite que o Azure AD B2C reencaminhar o parâmetro abrir ID Connect `ui-locales`, para o ponto final.  O servidor de conteúdo pode utilizar este parâmetro para fornecer páginas HTML personalizadas que são específicas do idioma.

## <a name="things-to-remember-when-building-your-own-content"></a>Aspetos a lembrar-se ao criar o seu próprio conteúdo

Se estiver a planear utilizar a funcionalidade de personalização do página IU, reveja as seguintes melhores práticas:

* Não copie o conteúdo de predefinido do Azure AD B2C e tentar modificá-lo. É melhor para criar o seu conteúdo HTML5 a partir do zero e a utilizar conteúdo predefinido como referência.
* Por motivos de segurança, iremos não permitem incluir quaisquer JavaScript no seu conteúdo. O que precisa da maioria deve estar disponível a box. Se não, utilizar [voz do utilizador](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) para pedir novas funcionalidades.
* Versões de browser suportado:
  * Internet Explorer 11, 10, Edge
  * Suporte limitado para a Internet Explorer 9, 8
  * Google Chrome 42.0 e acima
  * Mozilla Firefox 38.0 e acima
