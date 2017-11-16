---
title: "Azure Active Directory dos serviços de domínio: Ativar a delegação restringida de kerberos | Microsoft Docs"
description: "Ativar a delegação restringida de kerberos em domínios geridos de serviços de domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0235944ef89cab7af152664651711edd5e80e632
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configurar a delegação restringida de kerberos (KCD) um domínio gerido
Muitas aplicações devem ter acesso a recursos no contexto do utilizador. Active Directory suporta um mecanismo de delegação de Kerberos, que permite que este caso de utilização de chamada. Além disso, pode restringir a delegação para que podem ser acedidos apenas os recursos específicos no contexto do utilizador. Domínios geridos de serviços de domínio do AD do Azure são diferentes das tradicionais domínios do Active Directory, uma vez que seja mais segura estejam bloqueados para baixo.

Este artigo mostra como configurar a delegação restringida de kerberos num domínio gerido dos serviços de domínio do Azure AD.

## <a name="kerberos-constrained-delegation-kcd"></a>Delegação restringida de Kerberos (KCD)
A delegação de Kerberos permite a uma conta para representar outro principal de segurança (por exemplo, um utilizador) para aceder a recursos. Considere uma aplicação web que acede a uma API de web de back-end no contexto de um utilizador. Neste exemplo, a aplicação web (em execução no contexto de uma conta de serviço ou uma conta de computador /) representa o utilizador tem quando aceder ao recurso (web API de back-end). A delegação de Kerberos é inseguras, uma vez que não restringe os recursos que pode aceder a impersonating conta no contexto do utilizador.

Delegação restringida de Kerberos (KCD) restringe os serviços/recursos a que o servidor especificado pode agir em nome de um utilizador. KCD tradicional requer privilégios de administrador de domínio para configurar uma conta de domínio para um serviço e restringe a conta para um único domínio.

KCD tradicional também tem alguns problemas associados. Nos sistemas operativos anteriores, se o administrador de domínio configurada conta com base KCD para o serviço, o administrador de serviço não tinha nenhuma forma útil de saber que serviços front-end delegados aos serviços de recurso detinham. E qualquer serviço front-end que poderia delegar a um serviço de recursos representado um ponto de ataque potencial. Se um servidor que alojava um serviço front-end fosse comprometido e configurado para delegar a serviços de recursos, os serviços de recursos também podiam ser comprometidos.

> [!NOTE]
> Num domínio gerido dos serviços de domínio do Azure AD, não tiver privilégios de administrador de domínio. Por conseguinte, **KCD tradicional não é possível configurar um domínio gerido**. Utilize KCD baseada em recursos, conforme descrito neste artigo. Este mecanismo também é mais seguro.
>
>

## <a name="resource-based-kerberos-constrained-delegation"></a>Delegação restringida de kerberos baseada em recursos
Partir do Windows Server 2012, os administradores de serviço obtêm a capacidade de configurar a delegação restrita para o seu serviço. Neste modelo, o administrador de serviço de back-end pode permitir ou negar serviços front-end específicos da utilização KCD. Este modelo é conhecido como **delegação restrita de kerberos baseada em recursos**.

Baseada em recursos KCD está configurado com o PowerShell. Utilize os cmdlets Set-ADComputer ou Set-ADUser, dependendo se a conta impersonating é uma conta de computador ou uma conta de serviço/conta de utilizador.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configurar KCD baseada em recursos de uma conta de computador um domínio gerido
Suponha que tem uma aplicação web em execução no computador ' contoso100-webapp.contoso100.com'. Precisa de aceder ao recurso (uma API web em execução no ' contoso100-api.contoso100.com') no contexto de utilizadores de domínio. Eis como poderia configurar KCD baseada em recursos para este cenário.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configurar KCD baseada em recursos para uma conta de utilizador um domínio gerido
Suponha que tem uma aplicação web em execução como uma conta de serviço 'appsvc' e de que necessita aceder ao recurso (uma API web em execução como uma conta de serviço - 'backendsvc') no contexto de utilizadores de domínio. Eis como poderia configurar KCD baseada em recursos para este cenário.

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Descrição geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
