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
ms.openlocfilehash: ed82200bf81702bbe35a371e7d86676c2c27d8f4
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implementar sem necessidade de registo de utilizador final de reposição de palavra-passe

Para implementar a reposição de palavra-passe self-service (SSPR) do Azure Active Directory (Azure AD), dados de autenticação tem de estar presente. Algumas organizações têm os seus utilizadores, introduza os seus dados de autenticação próprios. Mas, muitas organizações preferem sincronizar com dados que já existe no Active Directory. Os dados sincronizados são disponibilizados para o Azure AD e SSPR sem necessidade de interação do utilizador se a:
   * Formate corretamente os dados no seu diretório no local.
   * Configurar [do Azure AD Connect, utilizando as definições rápidas](./connect/active-directory-aadconnect-get-started-express.md).

Para funcionar corretamente, números de telefone tem de estar no formato *+ indicativo do país PhoneNumber*, por exemplo, + 1 4255551234.

> [!NOTE]
> Reposição de palavra-passe não suporta extensões telefónicas. Mesmo no formato + 1 4255551234 X 12345, as extensões são removidas antes da chamada é efetuada.

## <a name="fields-populated"></a>Campos preenchidos

Se utilizar as predefinições no Azure AD Connect, são efetuados os seguintes mapeamentos de:

| Active Directory no local | Azure AD | Informações de contacto do Azure AD authentication |
| --- | --- | --- |
| telephoneNumber | Telefone do emprego | Telefone alternativo |
| Mobile | Número de telemóvel | Telefone |


## <a name="security-questions-and-answers"></a>Perguntas de segurança e respostas

As perguntas de segurança e respostas são armazenadas em segurança no seu inquilino do Azure AD e só estão acessíveis aos utilizadores através de [portal de registo SSPR](https://aka.ms/ssprsetup). Os administradores não é possível ver ou modificar o conteúdo da outra utilizadores perguntas e respostas.

### <a name="what-happens-when-a-user-registers"></a>O que acontece quando um utilizador se regista

Quando um utilizador se regista, a página de registo define os seguintes campos:

* **Telefone de autenticação**
* **E-Mail de autenticação**
* **Perguntas de segurança e respostas**

Se tiver fornecido um valor para **telemóvel** ou **e-mail alternativo**, os utilizadores imediatamente podem utilizar esses valores para repor as palavras-passe, mesmo que o se ainda não está registado para o serviço. Além disso, os utilizadores veem esses valores, quando estes registar pela primeira vez e podem modificá-los se pretenderem. Depois de se registar com êxito, estes valores serão ser persistente no **telefone de autenticação** e **correio eletrónico de autenticação** campos, respetivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação através do PowerShell

Os campos seguintes podem ser definidos através do PowerShell:

* **Correio eletrónico alternativo**
* **Telemóvel**
* **Telefone do escritório**: só pode ser definida se não estiver a sincronizar com um diretório no local

### <a name="use-powershell-version-1"></a>Utilizar o PowerShell versão 1

Para começar, terá de [transferir e instalar o módulo Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de ter instalado, pode utilizar os passos que se seguem para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Definir os dados de autenticação com o PowerShell versão 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Ler os dados de autenticação com o PowerShell versão 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Ler as opções de telefone de autenticação e o E-Mail de autenticação

Para ler o **telefone de autenticação** e **correio eletrónico de autenticação** quando utilizar o PowerShell versão 1, utilize os seguintes comandos:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Utilizar o PowerShell versão 2

Para começar, terá de [transferir e instalar o módulo do PowerShell versão 2 do Azure AD](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Depois de ter instalado, pode utilizar os passos que se seguem para configurar cada campo.

Para instalar rapidamente das versões recentes do PowerShell que suportam o módulo de instalação, execute os seguintes comandos. (A primeira linha verifica se o módulo já está instalado.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Definir os dados de autenticação com o PowerShell versão 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Ler os dados de autenticação com o PowerShell versão 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Tem uma pergunta licenciamento?](active-directory-passwords-licensing.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
