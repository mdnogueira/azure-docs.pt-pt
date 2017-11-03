---
title: "Aplicação do Azure Active Directory e objetos de principais de serviço"
description: "Ver um debate da relação entre a aplicação e objetos de principal de serviço no Azure Active Directory"
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b00acc192e868a7c1ade9fc68cf4d3ca04f1a070
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Aplicação e objetos de principal de serviço no Azure Active Directory (Azure AD)
Por vezes, o significado da "aplicação" pode ser misunderstood quando utilizados no contexto do Azure AD. O objetivo deste artigo é esclarecer aspetos conceptuais e concretos de integração de aplicações do Azure AD, com uma ilustração de registo e consentimento para um [aplicação multi-inquilino](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Descrição geral
Uma aplicação que foi integrada com o Azure AD tem implicações ponha o aspeto de software. "Aplicação" é frequentemente utilizada como um termo conceptual, referenciando não só o software de aplicação, mas também o registo do Azure AD e a função em "conversações" durante a execução de autenticação/autorização. Por definição, uma aplicação pode funcionar numa [cliente](active-directory-dev-glossary.md#client-application) função (consumir um recurso), um [servidor recursos](active-directory-dev-glossary.md#resource-server) função (a exposição de APIs para clientes), ou mesmo ambos. O protocolo de conversação está definido por uma [fluxo de conceder de autorização do OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), permitindo que o cliente recurso acesso/proteger dados de um recurso, respetivamente. Agora vamos um nível mais aprofundado e ver como o modelo de aplicação do Azure AD representa uma aplicação no momento da conceção e tempo de execução. 

## <a name="application-registration"></a>Registo de aplicação
Quando registar uma aplicação do Azure AD no [portal do Azure][AZURE-Portal], são criados dois objetos no seu inquilino do Azure AD: um objeto de aplicação e um objeto principal do serviço.

#### <a name="application-object"></a>Objeto de aplicação
Uma aplicação do Azure AD é definida por um e apenas objeto da aplicação, que reside no inquilino do Azure AD, onde a aplicação foi registada, conhecido como inquilino "raiz" da aplicação. O Azure AD Graph [entidade aplicação] [ AAD-Graph-App-Entity] define o esquema para propriedades de um objeto de aplicação. 

#### <a name="service-principal-object"></a>Objeto principal do serviço
Para poder aceder aos recursos que estão protegidos por um inquilino do Azure AD, a entidade que necessite de acesso tem de ser representada por um principal de segurança. Isto é verdadeiro para os utilizadores (principal de utilizador) e aplicações (principal de serviço). O principal de segurança define a política de acesso e permissões de utilizador/aplicação nesse inquilino. Isto permite funcionalidades de núcleos, tais como autenticação de utilizador/aplicação durante o início de sessão e autorização durante o acesso a recursos.

Quando uma aplicação obtém permissão para aceder a recursos de um inquilino (no registo ou [consentimento](active-directory-dev-glossary.md#consent)), é criado um objeto principal do serviço. O Azure AD Graph [ServicePrincipal entidade] [ AAD-Graph-Sp-Entity] define o esquema para propriedades de um serviço principal do objeto.  

#### <a name="application-and-service-principal-relationship"></a>Aplicação e a relação principal de serviço
Considere o objeto de aplicação, como o *global* representação da sua aplicação para utilização em todos os inquilinos e o principal de serviço como o *local* representação para utilização de um inquilino específico. A funciona do objeto de aplicação que o modelo a partir do qual comuns e as propriedades predefinidas é *derivada* para utilização na criação de objetos de principal de serviço correspondentes. Um objeto de aplicação, por conseguinte, tem uma relação de 1:1 com a aplicação de software e uma relações 1:many com os respetivos objetos de principal de serviço correspondentes.

Um principal de serviço têm de ser criado em cada inquilino onde a aplicação for utilizada, permitindo-lhe estabelecer uma identidade para início de sessão e/ou acesso a recursos que está a ser protegida pelo inquilino. A aplicação de uma único inquilino tem apenas um principal de serviço (por inquilino inicial), criado e autorizado para utilização durante o registo de aplicação. Uma multi-inquilino aplicação/API Web também tem um principal de serviço criado em cada inquilino onde um utilizador desse inquilino se consentiu para a sua utilização.  

> [!NOTE]
> Quaisquer alterações efetuadas para o objeto de aplicação, também são refletidas no respetivo objeto principal do serviço no raiz inquilino da aplicação só (inquilino onde foi registado). Para aplicações de multi-inquilinos, alterações ao objeto de aplicação não são refletidas no objetos principal do serviço dos inquilinos qualquer consumidor, até que o acesso é removido a [painel de acesso de aplicação](https://myapps.microsoft.com) e concedidas novamente.
><br>  
> Note também que aplicações nativas estão registadas como multi-inquilino por predefinição.
> 
> 

## <a name="example"></a>Exemplo
O diagrama seguinte ilustra a relação entre uma aplicação objeto da aplicação e serviço correspondente objetos principais, no contexto de uma aplicação de multi-inquilino de exemplo denominados **HR aplicação**. Neste cenário, existem três inquilinos do Azure AD: 

* **Adatum** -o inquilino utilizado pela empresa que desenvolveu o **aplicação de RH**
* **Contoso** -o inquilino utilizado pela organização Contoso, que é um consumidor do **aplicação de RH**
* **Fabrikam** -o inquilino utilizado pela organização Fabrikam, que também consome o **aplicação de RH**

![Relação entre um objeto de aplicação e um objeto principal do serviço](./media/active-directory-application-objects/application-objects-relationship.png)

No diagrama anterior, o passo 1 é o processo de criação de aplicação e objetos de principal de serviço no inquilino inicial da aplicação.

No passo 2, quando os administradores de Contoso e Fabrikam concluir consentimento, um objeto principal do serviço é criado no inquilino do Azure AD da empresa e atribuir as permissões concedido ao administrador. Note também que a aplicação de RH pode ser configurado/foi concebida para permitir consentimento por utilizadores para utilização individuais.

No passo 3, os inquilinos de consumidor da HR aplicação (Contoso e Fabrikam) cada tem os seus próprios objeto principal do serviço. Cada representa a utilização de uma instância da aplicação em runtime, regida pelas permissões autorizada pelo administrador do respetivo.

## <a name="next-steps"></a>Passos seguintes
Objeto de aplicação de uma aplicação pode ser acedido através da AD Graph API do Azure, o [do portal do Azure] [ AZURE-Portal] editor de manifesto de aplicação, ou [cmdlets do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado pelo respetivo OData [entidade aplicação][AAD-Graph-App-Entity].

Objeto principal do serviço de uma aplicação pode ser acedido através da AD Graph API do Azure ou [cmdlets do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado pelo respetivo OData [ServicePrincipal entidade][AAD-Graph-Sp-Entity].

O [Explorador do Azure AD Graph](https://graphexplorer.azurewebsites.net/) é útil para consultar a aplicação e os objetos de principal de serviço.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
