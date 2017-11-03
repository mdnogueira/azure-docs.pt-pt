---
title: Requisitos de dados do Azure AD SSPR | Microsoft Docs
description: "Requisitos de dados para a palavra-passe self-service do Azure AD como satisfazê-los e de reposição"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7936f47007285e3f7fa1d3220efa022a6e3881ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implementar sem necessidade de registo de utilizador final de reposição de palavra-passe

A implementação de reposição de palavra-passe Self-Service (SSPR) requer autenticação de dados presentes. Algumas organizações têm os seus utilizadores, introduza os seus dados de autenticação próprios mas, muitas organizações preferem sincronizar com a dados existentes no Active Directory. Se tem o formato correcto dados no seu diretório no local e configurar [do Azure AD Connect utilizando as definições rápidas](./connect/active-directory-aadconnect-get-started-express.md), que os dados ficam disponíveis para o Azure AD e SSPR sem interação do utilizador necessárias.

Os números de telefone devem ter o formato + indicativo do país PhoneNumber exemplo: 4255551234 + 1 para funcionar corretamente.

> [!NOTE]
> Reposição de palavra-passe não suporta extensões telefónicas. Mesmo no formato + 1 4255551234 X 12345, as extensões são removidas antes da chamada é efetuada.

## <a name="fields-populated"></a>Campos preenchidos

Se utilizar as predefinições no Azure AD Connect que são efetuados os seguintes mapeamentos.

| AD no local | Azure AD | Informações de contacto de autenticação do AD do Azure |
| --- | --- | --- |
| telephoneNumber | Telefone do escritório | Telefone alternativo |
| Mobile | Número de telemóvel | Telefone |


## <a name="security-questions-and-answers"></a>Perguntas de segurança e respostas

Perguntas de segurança e respostas são armazenadas em segurança no seu inquilino do Azure AD e só estão acessíveis aos utilizadores através de [portal de registo SSPR](https://aka.ms/ssprsetup). Os administradores não é possível ver ou modificar o conteúdo dos utilizadores perguntas e respostas de outro.

### <a name="what-happens-when-a-user-registers"></a>O que acontece quando um utilizador se regista

Quando um utilizador se regista, a página de registo define os seguintes campos:

* Telefone de autenticação
* E-Mail de autenticação
* Perguntas de segurança e respostas

Se tiver fornecido um valor para **telemóvel** ou **correio eletrónico alternativo**, os utilizadores imediatamente podem utilizar esses valores para repor as palavras-passe, mesmo que o se ainda não está registado para o serviço. Além disso, os utilizadores veem esses valores quando registar pela primeira vez e modificá-las, se pretenderem. Depois de se registar com êxito, estes valores serão ser persistente no **telefone de autenticação** e **correio eletrónico de autenticação** campos, respetivamente.

## <a name="set-and-read-authentication-data-using-powershell"></a>Definir e ler os dados de autenticação utilizando o PowerShell

Os campos seguintes podem ser definidos com o PowerShell

* Correio eletrónico alternativo
* Telemóvel
* Telefone do escritório - só pode ser definida se não a sincronizar com um diretório no local

### <a name="using-powershell-v1"></a>Utilizar o PowerShell V1

Para começar, terá de [transferir e instalar o módulo Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de ter instalado, pode seguir os passos que se seguem para configurar cada campo.

#### <a name="set-authentication-data-with-powershell-v1"></a>Conjunto de dados de autenticação com o PowerShell V1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>Ler os dados de autenticação com PowerShellPowerShell V1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>Telefone de autenticação e o E-Mail de autenticação apenas podem ser lidos através do Powershell V1 utilizando os comandos que se seguem

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>Utilizar o PowerShell V2

Para começar, terá de [transferir e instalar o módulo PowerShell do Azure AD V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Depois de ter instalado, pode seguir os passos que se seguem para configurar cada campo.

Para instalar rapidamente das versões recentes do PowerShell que suportam o módulo de instalação, execute estes comandos (a primeira linha simplesmente verifica se está já instalada):

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>Conjunto de dados de autenticação com o PowerShell V2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>Ler os dados de autenticação com o PowerShell V2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Passos seguintes

* [Como concluir a uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [O registo para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).
* [Tem uma pergunta de licenciamento?](active-directory-passwords-licensing.md)
* [Os métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que motivo importantes acerca do mesmo?](active-directory-passwords-writeback.md)
* [Como comunicar na atividade na SSPR](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que fazer significa?](active-directory-passwords-how-it-works.md)
* [Julgo que algo está danificado. Como posso resolver problemas SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi abrangida algures senão](active-directory-passwords-faq.md)
