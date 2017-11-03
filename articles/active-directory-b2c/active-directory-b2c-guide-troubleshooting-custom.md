---
title: "O Azure Active Directory B2C: Resolver problemas de políticas personalizadas | Microsoft Docs"
description: "Saiba mais sobre abordagens para resolver erros ao trabalhar com as políticas personalizadas no Azure Active Directory."
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.openlocfilehash: 023390ba36a74217503ff8b3076ad17978fe3fb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Resolver problemas de políticas personalizadas do Azure AD B2C e a arquitetura de experiência de identidade

Se utilizar o Azure Active Directory B2C políticas personalizadas (Azure AD B2C), podem ocorrer desafios de configurar o Framework de experiência de identidade no respetivo formato XML de idiomas de política.  Aprende a escrever políticas personalizadas pode ser como learning novo idioma. Neste artigo, vamos descrever as ferramentas e sugestões que podem ajudá-lo rapidamente detetarem e resolver problemas. 

> [!NOTE]
> Este artigo foca-se na sua configuração de política personalizada do Azure AD B2C de resolução de problemas. Não de endereços a aplicação da entidade confiadora ou a respetiva biblioteca de identidade.

## <a name="xml-editing"></a>Edição de XML

O erro mais comuns em configurar políticas personalizadas é incorretamente formatado XML. Um bom editor de XML é praticamente essencial. Um editor de XML boa apresenta XML nativamente, color-codes conteúdo, preenche termos comuns, mantém elementos XML indexados e pode validar com o esquema. Seguem-se dois dos nossos Favoritos editores de XML:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Bloco de notas + +](https://notepad-plus-plus.org/)

Validação de esquema XML identifica os erros antes de carregar o ficheiro XML. Na pasta raiz do pacote de arranque, obter a definição de esquema XML TrustFrameworkPolicy_0.3.0.0.xsd. Para obter mais informações, na documentação do editor de XML, procure *ferramentas XML* e *validação XML*.

Poderão ser úteis uma revisão de regras XML. O Azure AD B2C rejeita qualquer XML formatação erros que Deteta. Ocasionalmente, tem um formato XML poderá fazer com que mensagens de erro que são enganosa.

## <a name="upload-policies-and-policy-validation"></a>Políticas de carregamento e validação da política

 Validação de carregamento do ficheiro XML é automática. A maioria dos erros fazer com que o carregamento falha. Validação inclui o ficheiro de política que estiver a carregar. Também inclui a cadeia de ficheiros, que o ficheiro de carregamento refere-se ao (o ficheiro de política de terceiros entidade confiadora, o ficheiro de extensões e o ficheiro de base). 
 
 Erros de validação comuns incluem o seguinte:

Fragmento de erro:`... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* O valor de ClaimType poderá mal escrito ou não existe no esquema.
* ClaimType valores tem de ser definidos em, pelo menos, um dos ficheiros na política. 
    Por exemplo: ` <ClaimType Id="socialIdpUserId">`
* Se o ClaimType está definida no ficheiro de extensões, mas também é utilizado um valor de TechnicalProfile no ficheiro de base, carregar o ficheiro de base resulta num erro.

Fragmento de erro:`...makes a reference to a ClaimsTransformation with id...`
* As causas do erro poderão ser iguais para o ClaimType erro.

Fragmento de erro:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Verifique se o TenantId valor o  **\<TrustFrameworkPolicy\>**  e  **\<BasePolicy\>**  elementos corresponder ao seu inquilino de destino do Azure AD B2C.  

## <a name="troubleshoot-the-runtime"></a>Resolver problemas relacionados com o tempo de execução

* Utilize `Run Now` e `https://jwt.io` para testar as suas políticas independentemente do seu web ou aplicação móvel. Este Web site funciona como uma aplicação da entidade confiadora. Apresenta o conteúdo do Token Web JSON (JWT) que é gerado pela política do Azure AD B2C. Para criar uma aplicação de teste na estrutura de experiência de identidade, utilize os seguintes valores:
    * Nome: TestApp
    * Web App/Web API: não
    * O Native client: não

* Para rastrear a troca de mensagens entre o browser cliente e o Azure AD B2C, utilize [Fiddler](http://www.telerik.com/fiddler). Pode ajudar a obter uma indicação de onde está a falhar da sua viagem de utilizador na sua etapas da orquestração.

* No **modo de desenvolvimento**, utilize **Application Insights** para rastrear a atividade da sua viagem do Framework de experiência de identidade utilizador. No **modo de desenvolvimento**, pode observar a troca de afirmações entre o Framework de experiência de identidade e de vários fornecedores de afirmações que são definidos por perfis técnicos, tais como fornecedores de identidade, serviços baseados na API, o diretório de utilizador do Azure AD B2C e outros serviços, como o Azure várias-Factor-autenticação.  

## <a name="recommended-practices"></a>Práticas recomendadas

**Manter várias versões dos seus cenários. Agrupá-los num projeto com a sua aplicação.** A base de extensões de ficheiros e de entidade confiadora terceiros dependem diretamente entre si. Guardá-los como um grupo. São adicionadas novas funcionalidades para as suas políticas, manter versões de trabalho separado. Versões de trabalho de fase no seu próprio sistema com o código de aplicação interagem com de ficheiros.  As aplicações podem invocar várias entidades confiadoras terceiros políticas diferentes de um inquilino. Estes podem ficar dependentes as afirmações que se esperam das políticas do Azure AD B2C.

**Desenvolver e testar técnicas perfis com os percursos de utilizador conhecido.** Utilize políticas de pacote de arranque testada para configurar os perfis técnicas. Testá-los separadamente antes de lhe incorporá-los na suas própria percursos de utilizador.

**Desenvolver e testar percursos de utilizador com perfis de técnicas testadas.** Altere os passos de orquestração da journey utilizador de forma incremental. Crie progressivamente os seus cenários pretendidos.

## <a name="next-steps"></a>Passos seguintes

* No GitHub, transfira o ficheiro. zip (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) [active-directory-b2c-custom-policy-starterpack].
