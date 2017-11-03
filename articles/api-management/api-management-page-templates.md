---
title: "Página modelos na API Management do Azure | Microsoft Docs"
description: "Saiba como personalizar os conteúdos de páginas de portal de programador utilizando um conjunto de modelos na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: aca44e14ab85fcfeb9d1eb3c3eadfff7831c372f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="page-templates-in-azure-api-management"></a>Modelos de página na API Management do Azure
Gestão de API do Azure fornece a capacidade para personalizar os conteúdos de páginas de portal de programador utilizando um conjunto de modelos que configurar o respetivo conteúdo. Utilizando [DotLiquid](http://dotliquidmarkup.org/) sintaxe e o editor à sua escolha, tal como [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto de fornecido localizado [recursos de cadeia](api-management-template-resources.md#strings), [recursos de glifo](api-management-template-resources.md#glyphs), e [controlos de página](api-management-page-controls.md), tem uma enorme flexibilidade para configurar o conteúdo das páginas como julgar utilizando estes modelos.  
  
 Os modelos nesta secção permitem-lhe personalizar os conteúdos de início de sessão, iniciar a cópia de segurança e não encontrar página páginas no portal do programador.  
  
-   [A iniciar sessão](#SignIn)  
  
-   [Inscrever-se](#SignUp)  
  
-   [Página não foi encontrado](#PageNotFound)  
  
> [!NOTE]
>  Modelos predefinidos de exemplo estão incluídos na documentação do seguinte, mas estão sujeitos a alterações devido a melhoramentos contínuos. Pode ver os modelos predefinidos em direto no portal do programador, navegando para os modelos de individuais pretendidos. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="SignIn"></a>A iniciar sessão  
 O **sessão** modelo permite-lhe personalizar a página no portal do Programador de início de sessão.  
  
 ![Inicie sessão na página](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM sessão nos modelos de Portal de programador da página")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Controlos  
 Este modelo, pode utilizar o seguinte [página controlos](api-management-page-controls.md).  
  
-   [início de sessão básica](api-management-page-controls.md#basic-signin)  
  
-   [fornecedores](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Modelo de dados  
 [Início de sessão de utilizador](api-management-template-data-model-reference.md#UseSignIn) entidade.  
  
### <a name="sample-template-data"></a>Dados de exemplo do modelo  
  
```json  
{  
    "Email": null,  
    "Password": null,  
    "ReturnUrl": null,  
    "RememberMe": false,  
    "RegistrationEnabled": true,  
    "DelegationEnabled": false,  
    "DelegationUrl": null,  
    "SsoSignUpUrl": null,  
    "AuxServiceUrl": "https://manage.windowsazure.com/#Workspaces/ApiManagementExtension/service/contoso5/dashboard",  
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
    ],  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsEnabled": false  
}  
```  
  
##  <a name="SignUp"></a>Inscrever-se  
 O **inscrever** modelo permite-lhe personalizar a página no portal do Programador de inscrição.  
  
 ![Inscrever-se a página](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM início de sessão dos modelos de Portal de programador da página")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Controlos  
 Este modelo, pode utilizar o seguinte [página controlos](api-management-page-controls.md).  
  
-   [inscrição](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Modelo de dados  
 [Inscrição do utilizador](api-management-template-data-model-reference.md#UserSignUp) entidade.  
  
### <a name="sample-template-data"></a>Dados de exemplo do modelo  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a>Página não foi encontrado  
 O **página não foi encontrada** modelo permite-lhe personalizar a página não foi encontrada página no portal do programador.  
  
 ![Não foi encontrado página](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM não foi encontrado o Programador de página modelos Portal")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Controlos  
 Este modelo não pode utilizar qualquer [página controlos](api-management-page-controls.md).  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|referenceCode|Cadeia|Código gerado se esta página tiver sido apresentada como resultado de um erro interno.|  
|código de erro|Cadeia|Código gerado se esta página tiver sido apresentada como resultado de um erro interno.|  
|emailBody|Cadeia|Corpo gerado se esta página tiver sido apresentada como resultado de um erro interno do correio eletrónico.|  
|requestedUrl|Cadeia|O URL pedido quando a página não foi encontrada.|  
|referrerUrl|Cadeia|O URL de referência para o URL pedido.|  
  
### <a name="sample-template-data"></a>Dados de exemplo do modelo  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](api-management-developer-portal-templates.md).