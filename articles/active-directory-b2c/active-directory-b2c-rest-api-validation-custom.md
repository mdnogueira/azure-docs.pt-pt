---
title: "O Azure Active Directory B2C: API de REST afirmações trocas como validação | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.openlocfilehash: eb44a0d2234c9ee3801d8b3a1655d877aa2f4fef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Instruções: Integrar o REST API trocas de afirmações da sua viagem do Azure AD B2C utilizador como validação no intervenção do utilizador

A estrutura de experiência de identidade (IEF) subjacente do Azure Active Directory B2C (Azure AD B2C) permite que o Programador de identidade integrar uma interação com uma API RESTful em journey um utilizador.  

No final destas instruções, poderá criar um journey de utilizador do Azure AD B2C que interage com os serviços RESTful.

O IEF envia os dados em afirmações e recebe dados novamente nas afirmações. A interação com a API:

- Pode ser estruturada como uma troca de afirmações de REST API ou como um perfil de validação, o que acontece no interior de um passo de orquestração.
- Normalmente, valida a entrada do utilizador. Se o valor do utilizador é rejeitado, o utilizador pode tentar novamente introduzir um valor válido com a oportunidade para devolver uma mensagem de erro.

Também pode conceber a interação como um passo de orquestração. Para obter mais informações, consulte [explicação passo a passo: trocas da sua do Azure AD B2C utilizador viagem como um passo de orquestração de afirmações de integrar o API de REST](active-directory-b2c-rest-api-step-custom.md).

Para o exemplo de perfil de validação, utilizamos o journey de utilizador de edição do perfil no ficheiro de pacote de arranque ProfileEdit.xml.

Iremos pode verificar que o nome fornecido pelo utilizador na edição de perfil não faz parte de uma lista de exclusão.

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino do Azure AD B2C configurado para concluir uma conta local sessão-up/início de sessão, conforme descrito em [introdução](active-directory-b2c-get-started-custom.md).
- Um ponto final de REST API para interagir com. Nestas instruções, configurámos um site de demonstração chamado [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) com um serviço de REST API.

## <a name="step-1-prepare-the-rest-api-function"></a>Passo 1: Preparar a função de REST API

> [!NOTE]
> A configuração de funções de API de REST está fora do âmbito deste artigo. [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece uma excelente toolkit para criar serviços RESTful na nuvem.

Criámos uma função do Azure que recebe uma afirmação que se espera como `playerTag`. A função valida se esta afirmação existe. Pode aceder ao código de conclusão de função do Azure no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

O IEF espera o `userMessage` afirmação que devolve a função do Azure. Esta afirmação será apresentada como uma cadeia para o utilizador se a validação falhar, por exemplo, quando é devolvido um Estado de 409 conflito no exemplo anterior.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Passo 2: Configurar a troca de afirmações de RESTful API como um perfil no seu ficheiro TrustFrameworkExtensions.xml técnico

Um perfil técnico é a configuração completa do exchange com o serviço RESTful assim o desejar. Abra o ficheiro de TrustFrameworkExtensions.xml e adicione o seguinte fragmento XML no interior do `<ClaimsProviders>` elemento.

> [!NOTE]
> No XML de seguinte, o fornecedor RESTful `Version=1.0.0.0` será descrita como o protocolo. Considere-o como a função que irá interagir com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

O `InputClaims` elemento define as afirmações que serão enviadas o IEF para o serviço REST. Neste exemplo, o conteúdo da afirmação `givenName` serão enviados para o serviço REST como `playerTag`. Neste exemplo, o IEF não espera que afirmações novamente. Em vez disso, aguarda por uma resposta do serviço REST e atos com base em códigos de estado que recebe.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Passo 3: Incluem a troca do serviço RESTful afirmações no perfil técnica automática permitido em que pretende validar o intervenção do utilizador

A utilização mais comuns do passo validação está a ser a interação com um utilizador. Todas as interações onde o utilizador deve fornecer comentários são *automática permitido perfis técnicas*. Neste exemplo, iremos adicionar a validação para o perfil de técnico Self Asserted ProfileUpdate. Esta é a técnica de perfil que o ficheiro de política entidade confiadora (RP) de terceiros `Profile Edit` utiliza.

Para adicionar a troca de afirmações para o perfil técnico asserted automática:

1. Abra o ficheiro de TrustFrameworkBase.xml e procure `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Reveja a configuração deste perfil técnica. Observe como o exchange com o utilizador está definido como afirmações que serão pedidas do utilizador (afirmações de entrada) e as afirmações que serão esperadas novamente a partir do fornecedor de Self-asserted (afirmações de saída).
3. Procurar `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`e tenha em atenção que este perfil é invocado como orchestration passo 6 `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Passo 4: Carregar e testar o ficheiro de política RP de edição do perfil

1. Carregar a nova versão do ficheiro TrustFrameworkExtensions.xml.
2. Utilize **executar agora** para testar o ficheiro de política do perfil editar RP.
3. Testar a validação, fornecendo um dos nomes existentes (por exemplo, mcvinny) a **o nome fornecido** campo. Se tudo está configurado corretamente, deverá receber uma mensagem que notifica o utilizador que a tag de leitor já está a ser utilizada.

## <a name="next-steps"></a>Passos seguintes

[Modificar o registo de utilizador e a edição de perfil para recolher informações adicionais dos seus utilizadores](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Instruções: Integrar o REST API trocas de afirmações da sua viagem do Azure AD B2C utilizador como um passo de orquestração](active-directory-b2c-rest-api-step-custom.md)
