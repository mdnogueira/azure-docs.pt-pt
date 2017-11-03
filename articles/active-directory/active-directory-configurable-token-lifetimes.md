---
title: "Durações de token configuráveis no Azure Active Directory | Microsoft Docs"
description: "Saiba como definir durações para tokens emitidos pelo Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: billmath
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: d23721eba308096a05211eb6e26e1338a69cae0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durações de token configuráveis no Azure Active Directory (pré-visualização pública)
Pode especificar a duração de um token emitido pelo Azure Active Directory (Azure AD). Pode definir token durações para todas as aplicações na sua organização, para uma aplicação de (multi organização) de multi-inquilino ou para um principal de serviço específicos na sua organização.

> [!NOTE]
> Esta capacidade está atualmente em pré-visualização pública. Esteja preparado para reverter ou remover quaisquer alterações. A funcionalidade está disponível em qualquer subscrição do Azure Active Directory durante a pré-visualização pública. No entanto, quando a funcionalidade torna-se geralmente disponível, alguns aspetos da funcionalidade exijam uma [Azure Active Directory Premium](active-directory-get-started-premium.md) subscrição.
>
>

No Azure AD, um objeto de política representa um conjunto de regras que são impostas em aplicações individuais ou em todas as aplicações numa organização. Cada tipo de política tem uma estrutura exclusiva, com um conjunto de propriedades que são aplicadas a objetos aos quais estão atribuídos.

Pode designar uma política como a política predefinida para a sua organização. A política é aplicada a qualquer aplicação na organização, desde que não seja substituída por uma política com uma prioridade mais alta. Também pode atribuir uma política para aplicações específicas. A ordem de prioridade varia consoante o tipo de política.


## <a name="token-types"></a>Tipos de tokens

Pode definir políticas de duração do token de atualização de tokens, tokens de acesso, os tokens de sessão e tokens de identidade.

### <a name="access-tokens"></a>Tokens de acesso
Os clientes utilizam tokens de acesso para aceder a um recurso protegido. Um token de acesso pode ser utilizado apenas para uma combinação específica do utilizador, o cliente e o recurso. Os tokens de acesso não não possível revogar e são válidos até que a respetiva expiração. Um ator malicioso que tem de obter um token de acesso pode utilizá-lo para a extensão da respetiva duração. Ajuste a duração de um token de acesso é um compromisso entre melhorar o desempenho do sistema e aumentar a quantidade de tempo que o cliente mantém acesso depois da conta de utilizador é desativada. Desempenho do sistema melhorada é conseguido ao reduzir o número de vezes que um cliente precisar de adquirir um token de acesso de raiz.

### <a name="refresh-tokens"></a>Tokens de atualização
Quando um cliente adquire um token de acesso para aceder a recursos protegidos, o cliente recebe um token de atualização e um token de acesso. O token de atualização é utilizado para obter acesso/atualização novo token pares quando expira o token de acesso atual. Um token de atualização está vinculado a uma combinação de utilizador e de cliente. Um token de atualização pode ser revogado e validade do token é verificada sempre que o token é utilizado.

É importante fazer uma distinção entre os clientes confidenciais e público. Para obter mais informações sobre os diferentes tipos de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Durações de token com tokens de atualização de cliente confidencial
Clientes confidenciais são as aplicações que podem armazenar com segurança uma palavra-passe do cliente (segredo). Pode provam que pedidos provenientes da aplicação cliente e não a partir de um ator malicioso. Por exemplo, uma aplicação web é um cliente confidencial porque esta pode armazenar um segredo do cliente no servidor web. Não é exposto. Uma vez que estes fluxos são mais seguros, as durações predefinido da atualização de tokens emitidas para estes fluxos é `until-revoked`, não pode ser alterado utilizando a política e não irá ser revogado em reposições de palavra-passe voluntária.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Durações de token com tokens de atualização de cliente público

Clientes públicos segura não é possível armazenar uma palavra-passe do cliente (segredo). Por exemplo, uma aplicação iOS/Android não é possível obfuscate um segredo do proprietário do recurso, pelo que é considerado um cliente público. Pode definir políticas de recursos para impedir a obter um novo par de token de acesso/atualização tokens de atualização de clientes públicos com mais de um período de tempo especificado. (Para tal, utilize a propriedade de atualizar Token tempo máx. Inativos). Também pode utilizar políticas para definir um período para além dos quais os tokens de atualização já não são aceites. (Para tal, utilize a propriedade de atualizar a Token idade máxima). Pode ajustar a duração de um token de atualização para controlar quando e com que frequência o utilizador é necessário reintroduzir as credenciais, em vez de ser reautenticar silenciosamente, quando utilizar uma aplicação cliente público.

### <a name="id-tokens"></a>Tokens de ID
Tokens de ID são transferidos para sites e clientes nativos. Tokens de ID contêm informações de perfil sobre um utilizador. Um token de ID está vinculado a uma combinação específica do utilizador e de cliente. Tokens de ID são considerados válidos até que a respetiva expiração. Normalmente, uma aplicação web corresponde a um utilizador da duração de sessão na aplicação para a duração do ID token emitido para o utilizador. Pode ajustar a duração de um token de ID para controlar a frequência a aplicação web expira a sessão de aplicação e frequência com exige que o utilizador a possível reautenticar com o Azure AD (silenciosamente ou interativamente).

### <a name="single-sign-on-session-tokens"></a>Tokens de sessão de início de sessão único
Quando um utilizador efetua a autenticação com o Azure AD e seleciona a **manter a minha sessão iniciada** caixa de verificação, é estabelecida uma sessão de início de início de sessão único (SSO) com o browser do utilizador e o Azure AD. O token SSO, sob a forma de um cookie representa esta sessão. Tenha em atenção que o token de sessão SSO não está vinculado a uma aplicação de cliente/recursos específico. Os tokens de sessão SSO podem ser revogados e respetiva validade é verificada sempre que são utilizados.

AD do Azure utiliza dois tipos de tokens de sessão SSO: nonpersistent e persistente. Tokens de sessão persistentes são armazenados como os cookies persistentes pelo browser. Tokens de sessão nonpersistent são armazenados como cookies de sessão. (Cookies de sessão serão destruídos quando o browser é fechado.)

Tokens de sessão nonpersistent têm uma duração de 24 horas. Persistentes tokens têm uma duração de 180 dias. Sempre que é utilizado um token de sessão do SSO no seu período de validade, o período de validade é expandido outro 24 horas ou 180 dias, consoante o tipo de token. Se um token de sessão SSO não é utilizado no seu período de validade, é considerada expirou e já não é aceite.

Pode utilizar uma política para definir o tempo após o primeiro token de sessão foi emitido para além de que o token de sessão já não é aceite. (Para tal, utilize a propriedade de idade máxima Token do sessão). Pode ajustar a duração de um token de sessão para controlar quando e com que frequência um utilizador é necessário reintroduzir as credenciais, em vez de forma silenciosa a ser autenticado, ao utilizar uma aplicação web.

### <a name="token-lifetime-policy-properties"></a>Propriedades da política de duração do token
Uma política de duração do token é um tipo de objeto de política que contém as regras de duração do token. Utilize as propriedades da política para controlar durações de token especificadas. Se nenhuma política estiver definida, o sistema impõe o valor de duração predefinido.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de duração do token configurável
| Propriedade | Cadeia de propriedade de política | Afeta | Predefinição | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Duração do Token de acesso |AccessTokenLifetime |Os tokens de acesso, os tokens de ID, SAML2 tokens |1 hora |10 minutos |1 dia |
| Atualize o tempo inativo do Token máx. |MaxInactiveTime |Tokens de atualização |nos últimos 14 dias |10 minutos |90 dias |
| Idade máxima Token do fator único atualização |MaxAgeSingleFactor |Atualizar tokens (para todos os utilizadores) |Até revogado |10 minutos |Até revogado<sup>1</sup> |
| Idade máxima Token do multi-factor atualização |MaxAgeMultiFactor |Atualizar tokens (para todos os utilizadores) |Até revogado |10 minutos |Até revogado<sup>1</sup> |
| Idade máxima Token do fator único sessão |MaxAgeSessionSingleFactor<sup>2</sup> |Tokens de sessão (persistentes e nonpersistent) |Até revogado |10 minutos |Até revogado<sup>1</sup> |
| Idade máxima Token do multi-factor sessão |MaxAgeSessionMultiFactor<sup>3</sup> |Tokens de sessão (persistentes e nonpersistent) |Até revogado |10 minutos |Até revogado<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento máximo explícito que pode ser definido para estes atributos.
* <sup>2</sup>se **MaxAgeSessionSingleFactor** não está definido, este valor demora a **MaxAgeSingleFactor** valor. Se for definido nenhum dos parâmetros, a propriedade tem o valor predefinido (até-revogados).
* <sup>3</sup>se **MaxAgeSessionMultiFactor** não está definido, este valor demora a **MaxAgeMultiFactor** valor. Se for definido nenhum dos parâmetros, a propriedade tem o valor predefinido (até-revogados).

### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Predefinição |
| --- | --- | --- |
| Atualizar o Token idade máxima (emitidos para utilizadores federados que tenham informações de revogação insuficientes<sup>1</sup>) |Tokens de atualização (emitidos para utilizadores federados que tenham informações de revogação insuficientes<sup>1</sup>) |12 horas |
| Atualize o tempo inativo de máx. Token (emitidos para clientes confidenciais) |Atualizar tokens (emitidos para clientes confidenciais) |90 dias |
| Atualizar o Token idade máxima (emitidos para clientes confidenciais) |Atualizar tokens (emitidos para clientes confidenciais) |Até revogado |

* <sup>1</sup>Federated utilizadores com informações de revogação insuficientes incluem os utilizadores que não tem o atributo "LastPasswordChangeTimestamp" sincronizado com êxito. Estes utilizadores recebem este idade máxima curto porque não é possível verificar a revogar tokens que estão associados a uma credencial antiga (por exemplo, uma palavra-passe que foi alterada) e tem de verificar novamente no mais frequentemente para se certificar de que o utilizador e tokens associados ainda são AAD na boa  colocado. Para melhorar a esta experiência, administradores de inquilinos tem de garantir que estes estão a sincronizar o atributo "LastPasswordChangeTimestamp" (Isto pode ser definido no objeto de utilizador com o Powershell ou através de AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Avaliação da política e a atribuição de prioridades
Pode criar e, em seguida, atribuir uma política de duração do token para uma aplicação específica, a sua organização e principais de serviço. Várias políticas podem ser aplicadas a uma aplicação específica. A política de duração do token que entra em vigor segue estas regras:

* Se uma política é atribuída explicitamente para o principal de serviço, é imposta.
* Se nenhuma política é atribuída explicitamente para o principal de serviço, um explicitamente atribuída à organização principal do principal de serviço é imposta.
* Se nenhuma política é atribuída explicitamente para o principal de serviço ou à organização, é imposta a política atribuída à aplicação.
* Não se tiver sido atribuída a nenhuma política para o principal de serviço, a organização ou o objeto de aplicação, os valores predefinidos é imposta. (Consulte a tabela em [propriedades de duração do token configuráveis](#configurable-token-lifetime-properties).)

Para obter mais informações sobre a relação entre objetos de aplicação e objetos de principal de serviço, consulte [aplicação e objetos de principal de serviço no Azure Active Directory](active-directory-application-objects.md).

Validade do token é avaliada quando que o token é utilizado. A política com a prioridade mais elevada na aplicação que estiver a ser acedida entra em vigor.

> [!NOTE]
> Eis um exemplo de cenário.
>
> Um utilizador pretende aceder às duas aplicações da web: a aplicação Web A e B. de aplicação Web
> 
> Fatores:
> * Ambas as aplicações web estão na mesma organização principal.
> * Token 1 de política de duração com uma duração máx. Token de sessão de oito horas está definido como predefinição de organização principal.
> * Aplicação Web A é uma aplicação web de utilização normal e não se encontra associada a todas as políticas.
> * B de aplicação Web é utilizada para processos altamente confidenciais. O principal de serviço está associado ao Token duração política 2, a qual tem uma duração máx. Token de sessão de 30 minutos.
>
> Em 12:00 PM, o utilizador inicia uma nova sessão de browser e tenta aceder a aplicação Web O utilizador é redirecionado para o Azure AD e é-lhe pedido para iniciar sessão. Esta ação cria um cookie que tem um token de sessão no browser. O utilizador é redirecionado novamente para a aplicação Web A com um token de ID, que permite ao utilizador aceder à aplicação.
>
> Em 12:15 PM, o utilizador tentar aceder ao B. de aplicação Web O browser redireciona para o Azure AD, o qual Deteta o cookie de sessão. Principal de serviço de B aplicação Web está ligado a 2 de política de duração do Token, mas também faz parte da organização principal, com predefinido de 1 de política de duração do Token. 2 de política de duração do token entra em vigor porque políticas ligadas principais de serviço tem uma prioridade superior à políticas predefinidas de organização. O token de sessão foi originalmente emitiu nos últimos 30 minutos, para que seja considerada válida. O utilizador é redirecionado novamente para B de aplicação Web com um token de ID que lhe concede acesso.
>
> Em 1:00 PM, o utilizador tenta aceder a aplicação Web O utilizador é redirecionado para o Azure AD. A aplicação de Web não está ligado a quaisquer políticas, mas porque se encontra numa organização com predefinido de 1 de política de duração do Token, essa política entra em vigor. Foi detetado o cookie de sessão que foi originalmente emitiu nas últimas oito horas. O utilizador é redirecionado automaticamente a aplicação Web A com um novo token de ID. O utilizador não é necessário para autenticar.
>
> Imediatamente depois disso, o utilizador tenta aceder B. de aplicação Web O utilizador é redirecionado para o Azure AD. Como anteriormente, 2 de política de duração do Token entra em vigor. Porque o token foi emitido há mais de 30 minutos, é pedido ao utilizador para introduzir as respetivas credenciais de início de sessão. Um token de sessão brand-new e o token de ID são emitidos. O utilizador pode, em seguida, aceder B. de aplicação Web
>
>

## <a name="configurable-policy-property-details"></a>Detalhes de propriedade configurável de política
### <a name="access-token-lifetime"></a>Duração do Token de acesso
**Cadeia:** AccessTokenLifetime

**Afeta:** tokens de acesso, os tokens de ID

**Resumo:** esta política controla o período de tempo acesso e tokens de ID para este recurso são considerados válidos. Reduzir a propriedade de duração do Token de acesso atenua o risco de um token de acesso ou o token de ID a ser utilizado por um ator malicioso por um longo período de tempo. (Não não possível revogar estes tokens.) O compromisso é que o desempenho é afetado negativamente, porque os tokens têm de ser substituído com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Atualize o tempo inativo do Token máx.
**Cadeia:** MaxInactiveTime

**Afeta:** tokens de atualização

**Resumo:** esta política controla qual um token de atualização pode ser antes de um cliente é já não é utilizado para obter um novo par de token de acesso/atualização quando tentar aceder a este recurso. Porque, normalmente, um novo token de atualização é devolvido quando é utilizado um token de atualização, esta política impede o acesso se o cliente tenta aceder a qualquer recurso utilizando o token de atualização atual durante o período de tempo especificado.

Esta política força os utilizadores que não estão ativos no respetivo cliente a reautenticação para obter um novo token de atualização.

A propriedade atualizar Token tempo máx. inativa tem de ser definida para um valor inferior a idade máxima Token fator único e as propriedades de multi-factor atualizar Token idade máxima.

### <a name="single-factor-refresh-token-max-age"></a>Idade máxima Token do fator único atualização
**Cadeia:** MaxAgeSingleFactor

**Afeta:** tokens de atualização

**Resumo:** controlos esta política quanto um utilizador pode utilizar um token de atualização para obter um novo par de token de acesso/atualização depois de se pela última vez a autenticação com êxito ao utilizar apenas um fator único. Depois de um utilizador efetua a autenticação e recebe um novo token de atualização, o utilizador pode utilizar o fluxo do token de atualização para o período de tempo especificado. (Isto é verdadeiro, desde que o token de atualização atual não foi revogado e não for deixado não utilizados durante um período superior o tempo inativo.) Nessa altura, o utilizador é forçado a reautenticação para receber um novo token de atualização.

Reduzir a duração máxima obriga a que os utilizadores autentiquem com mais frequência. Porque a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que seja igual ou menor que a propriedade de multi-factor atualizar Token idade máxima.

### <a name="multi-factor-refresh-token-max-age"></a>Idade máxima Token do multi-factor atualização
**Cadeia:** MaxAgeMultiFactor

**Afeta:** tokens de atualização

**Resumo:** controlos esta política quanto um utilizador pode utilizar um token de atualização para obter um novo par de token de acesso/atualização depois de se pela última vez a autenticação com êxito através de vários fatores. Depois de um utilizador efetua a autenticação e recebe um novo token de atualização, o utilizador pode utilizar o fluxo do token de atualização para o período de tempo especificado. (Isto é verdadeiro, desde que o token de atualização atual não foi revogado e não é utilizado durante um período superior o tempo inativo.) Nessa altura, os utilizadores são forçados para voltar para receber um novo token de atualização.

Reduzir a duração máxima obriga a que os utilizadores autentiquem com mais frequência. Porque a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que seja igual ou maior do que a propriedade de único fatores atualizar Token idade máxima.

### <a name="single-factor-session-token-max-age"></a>Idade máxima Token do fator único sessão
**Cadeia:** MaxAgeSessionSingleFactor

**Afeta:** tokens de sessão (persistentes e nonpersistent)

**Resumo:** controlos esta política quanto um utilizador pode utilizar um token de sessão para obter um novo ID e o token da sessão depois de se pela última vez a autenticação com êxito ao utilizar apenas um fator único. Depois de um utilizador efetua a autenticação e recebe um token de sessão de novo, o utilizador pode utilizar o fluxo do token de sessão para o período de tempo especificado. (Isto é verdadeiro, desde que o token de sessão atual não foi revogado e não expirou.) Após o período de tempo especificado, o utilizador é forçado a reautenticação para receber um token de sessão de novo.

Reduzir a duração máxima obriga a que os utilizadores autentiquem com mais frequência. Porque a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que seja igual ou menor do que a propriedade de idade de máxima de Token de sessão de multi-factor.

### <a name="multi-factor-session-token-max-age"></a>Idade máxima Token do multi-factor sessão
**Cadeia:** MaxAgeSessionMultiFactor

**Afeta:** tokens de sessão (persistentes e nonpersistent)

**Resumo:** esta política os controlos quanto tempo um utilizador pode utilizar um token de sessão para obter um novo ID e a sessão token desde a última vez que o se a autenticação com êxito através de vários fatores. Depois de um utilizador efetua a autenticação e recebe um token de sessão de novo, o utilizador pode utilizar o fluxo do token de sessão para o período de tempo especificado. (Isto é verdadeiro, desde que o token de sessão atual não foi revogado e não expirou.) Após o período de tempo especificado, o utilizador é forçado a reautenticação para receber um token de sessão de novo.

Reduzir a duração máxima obriga a que os utilizadores autentiquem com mais frequência. Porque a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que seja igual ou maior do que a propriedade de idade de máxima de Token de sessão de fator único.

## <a name="example-token-lifetime-policies"></a>Políticas de duração do token de exemplo
Muitos cenários são possíveis no Azure AD quando pode criar e gerir token durações para aplicações, a principais de serviço e a sua organização geral. Nesta secção, iremos guiá-lo através de alguns cenários comuns de política que podem ajudar a impor novas regras para:

* Duração do token
* Tempo inativo do token máx.
* Idade máxima de token

Nos exemplos, pode saber como:

* Gerir a política predefinida de uma organização
* Criar uma política de início de sessão na web
* Criar uma política para uma aplicação nativa que chama uma API web
* Gerir uma política avançada

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, criar, atualizar, ligar e eliminar políticas para as aplicações, a principais de serviço e a sua organização geral. Se não estiver familiarizado com o Azure AD, é recomendável que conheça [como obter um inquilino do Azure AD](active-directory-howto-tenant.md) antes de avançar com estes exemplos.  

Para começar a utilizar, efetue os seguintes passos:

1. Transferir a versão mais recente [versão de pré-visualização do Azure AD PowerShell módulo pública](https://www.powershellgallery.com/packages/AzureADPreview).
2. Execute o `Connect` comando para iniciar sessão na sua conta de administrador do Azure AD. Executar este comando sempre que iniciar uma sessão de novo.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas que foram criadas na sua organização, execute o seguinte comando. Execute este comando após a maioria das operações nos seguintes cenários. Executar o comando também o ajuda a obter o * * * * das suas políticas.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemplo: Gerir a política predefinida de uma organização
Neste exemplo, criar uma política que permita aos utilizadores iniciar sessão com uma frequência inferior em toda a organização. Para tal, crie uma política de duração do token de fator único atualizar Tokens, pelo que é aplicada em toda a organização. É aplicada a política para todas as aplicações na sua organização e para cada serviço principal que já não tem uma política definida.

1. Crie uma política de duração do token.

    1.  Conjunto o Token de atualização de fator único para "até-revogado." O token não expira até que o acesso é revogado. Crie a seguinte definição de política:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Para criar uma política, execute o seguinte comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Para ver a nova política bem como obter a política **ObjectId**, execute o seguinte comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Atualize a política.

    Poderá decidir que a política primeiro definido neste exemplo não é tão strict como necessita do seu serviço. Para definir o fator único atualizar Token expira em dois dias, execute o seguinte comando:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política de início de sessão na web

Neste exemplo, criar uma política que requer que os utilizadores autentiquem com mais frequência na sua aplicação web. Esta política define a duração dos tokens de acesso/ID e a idade máxima de um token de sessão multifator para o principal de serviço da sua aplicação web.

1. Crie uma política de duração do token.

    Esta política, para web início de sessão, define o acesso/ID de duração do token e a idade de token de sessão de fator único máximo para duas horas.

    1.  Para criar uma política, execute este comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política de e para obter uma política **ObjectId**, execute o seguinte comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Atribua a política para o principal de serviço. Também tem de obter o **ObjectId** do seu principal de serviço. 

    1.  Para ver os principais de serviço da sua organização, pode consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ou, no [Explorador do Azure AD Graph](https://graphexplorer.cloudapp.net/), inicie sessão na sua conta do Azure AD.

    2.  Se tiver o **ObjectId** do seu principal de serviço, execute o seguinte comando:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemplo: Criar uma política para uma aplicação nativa que chama uma API web
Neste exemplo, criar uma política que requer que os utilizadores autentiquem com menos frequência. A política também lengthens a quantidade de tempo que um utilizador pode ficar inativo antes do utilizador deve voltar. A política é aplicada para a API web. Quando a aplicação nativa pedidos web API como um recurso, a política é aplicada.

1. Crie uma política de duração do token.

    1.  Para criar uma política strict para uma API web, execute o seguinte comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política de e para obter uma política **ObjectId**, execute o seguinte comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Atribua a política para a API web. Também tem de obter o **ObjectId** da sua aplicação. A melhor forma de determinar a sua aplicação **ObjectId** consiste em utilizar o [portal do Azure](https://portal.azure.com/).

   Se tiver o **ObjectId** da sua aplicação, execute o seguinte comando:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Exemplo: Gerir uma política avançada
Neste exemplo, criar algumas políticas, para saber como funciona o sistema de prioridade. Também pode saber como gerir várias políticas que são aplicadas a vários objetos.

1. Crie uma política de duração do token.

    1.  Para criar uma política predefinida de organização que define a duração do Token de atualização única fatores para 30 dias, execute o seguinte comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política bem como obter a política **ObjectId**, execute o seguinte comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Atribua a política a um principal de serviço.

    Agora, tem uma política que se aplica a toda a organização. Pode querer manter esta política de 30 dias para um principal de serviço específica, mas a alterar a política predefinida de organização para o limite superior de "até-revogado."

    1.  Para ver os principais de serviço da sua organização, pode consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ou, no [Explorador do Azure AD Graph](https://graphexplorer.cloudapp.net/), inicie sessão com a sua conta do Azure AD.

    2.  Se tiver o **ObjectId** do seu principal de serviço, execute o seguinte comando:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Definir o `IsOrganizationDefault` sinalizador para false:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Crie uma nova predefinição política da organização:

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Tem agora a política original ligada ao seu principal de serviço e a nova política é definida como a política de predefinida da organização. É importante lembrar-se de que as políticas aplicadas a principais de serviço tem prioridade sobre as políticas de predefinida da organização.

## <a name="cmdlet-reference"></a>Referência de cmdlets

### <a name="manage-policies"></a>Gerir políticas

Pode utilizar os seguintes cmdlets para gerir as políticas.

#### <a name="new-azureadpolicy"></a>Novo AzureADPolicy

Cria uma nova política.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matriz de stringified JSON que contém todas as regras da política. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Cadeia com o nome da política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se for VERDADEIRO, define a política como a política da organização predefinido. Se for FALSO, não produz qualquer efeito. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Tipo de política. Para o token durações, utilize sempre "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcional] |Define um ID alternativo para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtém todas as políticas do Azure AD ou uma política especificada.

```PowerShell
Get-AzureADPolicy
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code>[Opcional] |**ObjectId (Id)** da política que pretende. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todas as aplicações e principais de serviço que estejam ligadas a uma política.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da política que pretende. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Conjunto AzureADPolicy
As atualizações de uma política existente.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da política que pretende. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Cadeia com o nome da política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Opcional] |Matriz de stringified JSON que contém todas as regras da política. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Opcional] |Se for VERDADEIRO, define a política como a política da organização predefinido. Se for FALSO, não produz qualquer efeito. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Opcional] |Tipo de política. Para o token durações, utilize sempre "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcional] |Define um ID alternativo para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remover AzureADPolicy
Elimina a política especificada.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da política que pretende. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Políticas de aplicações
Pode utilizar os seguintes cmdlets para políticas de aplicações.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>AzureADApplicationPolicy adicionar
Ligações à política especificada para uma aplicação.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtém a política que está atribuída a uma aplicação.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da aplicação. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remover AzureADApplicationPolicy
Remove uma política a partir de uma aplicação.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Políticas de principais de serviço
Pode utilizar os seguintes cmdlets para políticas de principal de serviço.

#### <a name="add-azureadserviceprincipalpolicy"></a>AzureADServicePrincipalPolicy adicionar
Liga à política especificada para um principal de serviço.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtém qualquer política ligada para o principal de serviço especificado.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da aplicação. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remover AzureADServicePrincipalPolicy
Remove a política de principal de serviço especificado.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |
