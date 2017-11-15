---
title: "Configurar o início de sessão automático-aceleração para uma aplicação através da política de deteção de Realm home page | Microsoft Docs"
description: "Explica o que é um inquilino do Azure AD e como gerir o Azure através do Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>Configurar o início de sessão automático-aceleração para uma aplicação através da política de deteção (HRD) de realm inicial

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>Introdução à deteção de realm inicial e aceleração automática
O documento seguinte fornece uma introdução para deteção de realm inicial e aceleração automática.

### <a name="home-realm-discovery"></a>Deteção de realm inicial
Deteção de Realm inicial é o processo que permite que a Azure Active Directory determinar, no momento de início de sessão, em que um utilizador tem de autenticar.  Quando iniciar sessão para um inquilino do Azure AD para aceder a um recurso ou a página de início de sessão comuns do Azure AD, o utilizador escrever um nome de utilizador (UPN).  Azure AD utiliza esse para detetar em que o utilizador tem de início de sessão.   O utilizador poderá ter de ser direcionado para um dos seguintes sejam autenticados:

- O inquilino doméstico do utilizador (podem ser o mesmo inquilino como o recurso de utilizador está a tentar aceder). 
- Conta Microsoft.   O utilizador é um convidado no inquilino do recurso
- Outro fornecedor de identidade federada com o inquilino do Azure AD.  Um fornecedor de identidade no local, como o AD FS para a instância.

### <a name="auto-acceleration"></a>Aceleração automática 
Algumas organizações configurar o seu inquilino do Azure Active Directory a federar com outro IdP, tais como o AD FS, para autenticação de utilizador.  Nestes casos, quando iniciar sessão numa aplicação, primeiro é apresentada ao utilizador com uma página de início de sessão do Azure AD e, depois de que escreveu o UPN é direcionado para a página de início de sessão do IdP.  Em circunstâncias onde faz sentido, os administradores podem pretender ter utilizadores direcionados diretamente para a página de início de sessão quando iniciar sessão para aplicações específicas, a ignorar a página inicial do Azure Active Directory. Isto é referido como "início de sessão automático-aceleração".

Nos casos em que o inquilino é federado para outro IdP para início de sessão, ativar a aceleração de automática faz com que o utilizador início de sessão mais está mais simples nos casos em que sabe que todos os utilizadores iniciarem sessão podem ser autenticado por esse IdP.  Pode configurar a aceleração de automático para aplicações individuais.

>[!NOTE]
>Se configurar uma aplicação para a aceleração de automática, os utilizadores convidados não é possível iniciar sessão. Colocar o utilizador diretamente para um IdP federado para a autenticação é uma rua unidirecional como não é possível para voltar para a página de início de sessão no Azure Active Directory.  Utilizadores de convidado, o que poderão ter de ser direcionado para outros inquilinos ou uma IdP externo, como a conta Microsoft para ser autenticado, não conseguirá iniciar sessão para essa aplicação, o passo de deteção de Realm inicial será ignorado.  

Existem duas formas de controlar a aceleração de automático para um IdP federada.  O:   

- Utilizar uma sugestão de domínio em pedidos de autenticação para uma aplicação 
- Configurar a política de HomeRealmDiscovery para ativar a aceleração de automática

## <a name="domain-hints"></a>Sugestões de domínio 
Sugestões de domínio são diretivas incluídas no pedido de autenticação de uma aplicação.  Pode ser utilizados para acelerar o utilizador para os respetivos federada IdP-página sessão ou podem ser utilizados por uma aplicação multi-inquilino para acelerar o utilizador diretamente para o com marca corporativa página sessão do Azure AD para o respetivo inquilino.  Por exemplo, uma aplicação largeapp.com poderá permitir que os seus clientes aceder a aplicação com um contoso.largeapp.com URL personalizado e pode incluir uma sugestão de domínio contoso.com do pedido de autenticação. Sintaxe de sugestão de domínio varia consoante o protocolo a ser utilizado e, normalmente, são configurados na aplicação.

**WS-Federation**: whr=contoso.com na cadeia de consulta

**SAML**: ou um SAML pedido de autenticação que contém uma sugestão de domínio ou um whr=contoso.com de cadeia de consulta

**Abrir ID Connect**: um domain_hint=contoso.com de cadeia de consulta 

Se uma sugestão de domínio está incluída no pedido de autenticação da aplicação e o inquilino está Federado com esse domínio, tenta do Azure AD para redirecionamento de início de sessão para IdP configurado para esse domínio.  Se a sugestão de domínio não fazer referência a um domínio verificado federado, é ignorada e deteção de realm inicial normal é invocada.

Consulte este [blogue](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/) para obter mais informações sobre a aceleração de automática utilizando as sugestões de domínio suportadas pelo Azure Active Directory.

>[!NOTE]
>Se uma sugestão de domínio está incluída na sua presença substitui qualquer política HRD que está definida para a aplicação de um pedido de autenticação.

## <a name="home-realm-discovery-hrd-policy"></a>Política do realm inicial (HRD) de deteção
Algumas aplicações não fornecem uma forma de configurar o pedido de autenticação, emitir e nestes casos não é possível utilizar sugestões de domínio para controlar a aceleração de automática.   Aceleração automática pode ser configurada através da política para conseguir o mesmo comportamento.  

### <a name="setting-hrd-policy"></a>Definição de política HRD
Existem três passos para a definição de início de sessão automático-aceleração de uma aplicação


1. Criar uma política HRD para aceleração automática
2. Localizar o principal de serviço ao qual pretende anexar a política
3. Anexar a política para o princípio de serviço.  As políticas que podem ter sido criadas de um inquilino, mas não têm qualquer efeito até que estão ligados a uma entidade.  Uma política HRD pode ser anexada a um Principal de serviço e apenas uma política HRD pode estar ativa numa determinada entidade num dado momento.  

Pode utilizar o Microsoft Active Directory Graph API do Azure diretamente ou os Cmdlets do PowerShell do Active Directory do Azure para configurar a aceleração de automática através da política de HRD

A Graph API que manipula política é descrita [aqui](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations).

Eis um exemplo de definição de política HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

O tipo de política é "HomeRealmDiscoveryPolicy".

Se **AccelerateToFederatedDomain** for FALSO, a política não tem qualquer efeito **PreferredDomain** deve indicar um domínio para acelerar a e pode ser omitido caso o inquilino tem um e apenas um federado domínio.  Se for omitido e existir mais do que um verificado, o domínio federado, a política não tem efeito.

Se **PreferredDomain** especificado tem de corresponder um domínio verificado, federado para o inquilino e todos os utilizadores da aplicação em questão devem conseguir iniciar sessão no domínio.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e a avaliação das políticas HRD
Políticas HRD podem ser criadas e, em seguida, atribuídas a organizações específicas e principais de serviço. Isto significa que é possível que várias políticas aplicar a uma aplicação específica. A política HRD que entra em vigor segue estas regras:


- Se uma sugestão de domínio estiver presente no pedido de autenticação, em seguida, qualquer política HRD é ignorada e o comportamento especificado pela sugestão de domínio é utilizado.
- Caso contrário, se uma política é atribuída explicitamente para o principal de serviço, é imposto. 
- Se não houver nenhuma sugestão de domínio e nenhuma política é atribuída explicitamente para o principal de serviço, um explicitamente atribuída à organização principal do serviço principal é imposta. 
- Se não houver nenhuma sugestão de domínio e nenhuma política foi atribuída o principal de serviço ou a organização, é utilizado o comportamento HRD predefinido.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Tutorial para definir o início de sessão automático-aceleração de uma aplicação através da política de HRD com os cmdlets do PowerShell do Active Directory do Azure
Vamos explicar alguns cenários, incluindo:


- Configuração de aceleração de automática para uma aplicação para um inquilino com um único domínio federado
- Configuração de aceleração de automática para uma aplicação para um dos vários domínios que são verificados para o seu inquilino
- Listar as aplicações para o qual está configurada uma política

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos abaixo criar, atualizar, ligar e eliminar políticas no principais de serviço de aplicação no Azure AD.

1.  Para começar, transfira a pré-visualização de Cmdlet do Azure AD PowerShell mais recente. 
2.  Assim que tiver Cmdlets do Azure AD PowerShell, execute o comando de ligar para iniciar sessão com o Azure AD com a sua conta de administrador.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Execute o seguinte comando para ver todas as políticas da sua organização.

    ``` powershell
    Get-AzureADPolicy
    ```

Se não forem devolvidas, não tem políticas criadas no seu inquilino

### <a name="example-setting-auto-acceleration-for-an-application"></a>Exemplo: Definir a aceleração de automática para uma aplicação 
Neste exemplo, criar uma política que auto-acelera utilizadores para um ecrã de início de sessão do AD FS quando iniciar sessão a uma aplicação.  Isto é feito sem-los a ter de introduzir um nome de utilizador a página de início de sessão do Azure AD pela primeira vez. 

#### <a name="step-1-create-an-hrd-policy"></a>Passo 1: Criar uma política HRD
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Se tiver um único domínio federado, que autentica utilizadores para aplicações, apenas terá de criar uma política HRD.  
Para ver a nova política e obtenha o ObjectID, execute o seguinte comando.

``` powershell
Get-AzureADPolicy
```


Assim que tiver uma política HRD, para ativar a aceleração de automática, pode atribuí-la para vários princípios de serviço de aplicações.

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>Passo 2: Localize o principal de serviço para atribuir a política.  
É necessário o ObjectId de principais de serviço que pretende atribuir a política. Existem várias formas de localizar o ID de objeto de principais de serviço.    

Pode utilizar o portal, pode consultar o [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou vá para o nosso [gráfico Explorer ferramenta](https://graphexplorer.cloudapp.net/) e iniciar sessão na sua conta do Azure AD para ver os principais de serviço da sua organização ou uma vez que está a utilizar PowerShell, pode utilizar o cmdlet get-AzureADServicePrincipal para listar os princípios de serviço e os respetivos Ids.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passo 3: Atribuir a política para o principal de serviço  
Assim que tiver o ObjectId do principal da aplicação que pretende configurar automaticamente-aceleração de serviço, execute o seguinte comando para associar a política HRD que criou no passo 1 com o principal de serviço localizada no passo 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Pode repetir este comando para cada Principal do serviço que pretende adicionar a política.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Passo 4: Verificar os principais de serviço de aplicações a política de aceleração automática está atribuída a
Para verificar as aplicações que tem automática aceleração política configurada utilize o cmdlet Get-AzureADPolicyAppliedObject e transmita-o objectId da política que pretende verificar.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Passo 5: Terminar!
Experimente a aplicação para verificar se a nova política está a funcionar.

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Exemplo: Listar as aplicações para o qual está configurada uma política de aceleração automática

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>Passo 1: Lista de todas as políticas criadas na sua organização 

``` powershell
Get-AzureADPolicy
```

Tenha em atenção o **ID de objeto** da política que pretende para listar atribuições para.

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>Passo 2: Lista de principais de serviço que a política é atribuída a.  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>Exemplo: Remover uma política de aceleração automática para uma aplicação
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>Passo 1: Utilizar o exemplo anterior para obter o ObjectId da política e de que o principal de serviço de aplicações que pretende remover a
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Passo 2: Remova a atribuição de política de principal de serviço de aplicações.  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>Passo 3: Verificação a remoção por listar os principais de serviço a política é atribuída a 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como funciona a autenticação no Azure AD, consulte [cenários de autenticação para o Azure AD](develop/active-directory-authentication-scenarios.md).
- Para obter mais informações sobre o utilizador-início de sessão único consulte [acesso a aplicações e início de sessão no Azure Active Directory](active-directory-enterprise-apps-manage-sso.md)
- Visite o [guia para programadores do Active Directory](develop/active-directory-developers-guide.md) para uma descrição geral de todos os conteúdos relacionados com o programador.
