---
title: "Política: Do Azure AD SSPR | Microsoft Docs"
description: "As opções de política de reposição de Azure AD self-service palavra-passe"
services: active-directory
keywords: "Gestão de palavras-passe do Active Directory, gestão de palavras-passe, do Azure AD Self-repor a palavra-passe do serviço"
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
ms.openlocfilehash: ae1e6c34b610aff245e051454269f0b8619b54c7
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas de palavra-passe e restrições no Azure Active Directory

Este artigo descreve os requisitos de complexidade associados a contas de utilizador armazenadas no seu inquilino do Azure Active Directory (Azure AD) e políticas de palavra-passe.

## <a name="administrator-password-policy-differences"></a>Diferenças de política de palavra-passe de administrador

Microsoft impõe uma predefinição forte *dois porta* política para qualquer função de administrador do Azure de reposição de palavra-passe. 

Com a política de porta de dois, os administradores não têm a capacidade de utilizar perguntas de segurança.

 Uma política de porta de dois requer dois tipos de dados de autenticação, tais como um endereço de e-mail *e* um número de telefone. Uma política de porta de dois aplica-se nas seguintes circunstâncias:

* São afetadas todas as seguintes funções de administrador do Azure:
  * Administrador de suporte técnico
  * Administrador de suporte de serviço
  * Administrador de faturação
  * Suporte de Tier1 parceiro
  * Suporte de Tier2 parceiro
  * Administrador de serviço do Exchange
  * Administrador de serviço do Lync
  * Administrador de conta de utilizador
  * Escritores de diretório
  * Administrador global ou de administrador da empresa
  * Administrador de serviço do SharePoint
  * Administrador de compatibilidade
  * Administrador de aplicações
  * Administrador de segurança
  * Administrador com função privilegiada
  * Administrador de serviços do Microsoft Intune
  * Administrador de serviço de proxy de aplicações
  * Administrador de serviço do CRM
  * Administrador de serviço do Power BI
  
* Se o prazo de 30 dias decorridos numa subscrição de avaliação

  ou

* Um domínio está presente, tal como contoso.com

  ou

* O Azure AD Connect está a sincronizar as identidades do seu diretório no local

### <a name="exceptions"></a>Exceções
Uma política de uma porta requer um conjunto de dados de autenticação, como um endereço de e-mail *ou* número de telefone. Uma porta de uma política aplica-se nas seguintes circunstâncias:

* É dentro os primeiros 30 dias de uma subscrição de avaliação

  ou

* Um domínio não está presente (*. c o m) 

  e 

  O Azure AD Connect não está a sincronizar identidades


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas de UserPrincipalName que se aplicam a todas as contas de utilizador

Cada conta de utilizador que tem de iniciar sessão Azure AD tem de ter um valor de atributo de nome principal (UPN) de utilizador exclusivo associado à sua conta. A seguinte tabela destaca as políticas que se aplicam a ambas as contas de utilizador de Active Directory no local que são sincronizadas para a nuvem e às contas de utilizador só de nuvem:

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Carateres permitidos |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Não são permitidos carateres |<ul> <li>Qualquer "@" caráter que não é separar o nome de utilizador do domínio.</li> <li>Não pode conter um caráter de período "." existentes imediatamente antes do "@" símbolo</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não pode exceder 113 carateres</li><li>Podem existir até 64 carateres antes do "@" símbolo</li><li>Podem existir até 48 carateres após o "@" símbolo</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de palavra-passe que só se aplicam a contas de utilizador de nuvem

A tabela seguinte descreve as definições de política de palavra-passe disponíveis que podem ser aplicadas a contas de utilizador que são criadas e geridas no Azure AD:

| Propriedade | Requisitos |
| --- | --- |
| Carateres permitidos |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Não são permitidos carateres |<ul><li>Carateres Unicode.</li><li>Espaços.</li><li> Apenas palavras-passe fortes: não pode conter um caráter de ponto "." existentes imediatamente antes do "@" símbolo.</li></ul> |
| Restrições de palavra-passe |<ul><li>Um mínimo de 8 carateres e um máximo de 16 carateres.</li><li>Apenas palavras-passe fortes: requer três fora de quatro das seguintes opções:<ul><li>Character.s em minúsculas</li><li>Carateres maiúsculos.</li><li>Números (0-9).</li><li>Símbolos (consulte as restrições de palavra-passe anteriores).</li></ul></li></ul> |
| Duração de expiração da palavra-passe |<ul><li>Valor predefinido: **90** dias.</li><li>O valor é configurável ao utilizar o `Set-MsolPasswordPolicy` cmdlet a partir do módulo Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de palavra-passe |<ul><li>Valor predefinido: **14** dias (antes de expira a palavra-passe).</li><li>O valor é configurável ao utilizar o `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Expiração da palavra-passe |<ul><li>Valor predefinido: **falso** dias (indica que expiração de palavra-passe está ativada).</li><li>O valor pode ser configurado para contas de utilizador individuais utilizando o `Set-MsolUser` cmdlet.</li></ul> |
| Histórico de alterações de palavra-passe |A palavra-passe último *não é possível* ser utilizadas novamente quando o utilizador altera uma palavra-passe. |
| Histórico de reposição de palavra-passe | A palavra-passe último *pode* ser utilizadas novamente quando o utilizador repõe uma palavra-passe esquecida. |
| Bloqueio de conta |Após 10 sem início de sessão tentativas com a palavra-passe errada, o utilizador está bloqueado para um minuto. Ainda mais o início de sessão incorreto tenta bloqueio enviados ao utilizador para aumentar a duração de tempo. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Definir políticas de expiração de palavra-passe no Azure AD

Um administrador global para um serviço em nuvem da Microsoft pode utilizar o módulo do Microsoft Azure AD para o Windows PowerShell para definir palavras-passe do utilizador para não expirarem. Também pode utilizar cmdlets do Windows PowerShell para remover o nunca-expira a configuração ou para ver o que utilizador palavras-passe estão definidas para nunca expirar. 

Esta orientação aplica-se para outros fornecedores, como o Intune e o Office 365, que também dependem do Azure AD para os serviços de identidade e de diretório. Expiração de palavra-passe é a única parte da política que pode ser alterada.

> [!NOTE]
> Apenas as palavras-passe para contas de utilizador que não estão sincronizadas através de sincronização de diretórios podem ser configuradas para não expirarem. Para mais informações sobre a sincronização de diretórios, consulte [Connect AD com o Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verifique as políticas de palavra-passe utilizando o PowerShell

Para começar, terá de [transferir e instalar o módulo Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois de ter instalado, pode utilizar os seguintes passos para configurar cada campo.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>Como verificar a política de expiração uma palavra-passe
1. Liga ao Windows PowerShell, utilizando as credenciais de administrador da empresa.
2. Execute um dos seguintes comandos:

   * Para ver se a palavra-passe de um único utilizador está definida para nunca expirar, execute o seguinte cmdlet utilizando o UPN (por exemplo,  *aprilr@contoso.onmicrosoft.com* ) ou o ID de utilizador do utilizador que pretende verificar:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Para ver o **palavra-passe nunca expira** definir para todos os utilizadores, execute o seguinte cmdlet:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Definir uma palavra-passe a expirar

1. Liga ao Windows PowerShell, utilizando as credenciais de administrador da empresa.
2. Execute um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para que a palavra-passe expira, execute o seguinte cmdlet utilizando o UPN ou o ID de utilizador do utilizador:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para definir as palavras-passe de todos os utilizadores na organização de modo a que expirarem, utilize o seguinte cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Definir uma palavra-passe nunca expira

1. Liga ao Windows PowerShell, utilizando as credenciais de administrador da empresa.
2. Execute um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para nunca expirar, execute o seguinte cmdlet utilizando o UPN ou o ID de utilizador do utilizador:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para definir as palavras-passe de todos os utilizadores numa organização nunca expirem, execute o seguinte cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > As palavras-passe definida como `-PasswordNeverExpires $true` ainda idade com base no `pwdLastSet` atributo. Se definir as palavras-passe de utilizador para nunca expirar e avance 90 dias, as palavras-passe expirarem. Com base no `pwdLastSet` atributo, se alterar a expiração para `-PasswordNeverExpires $false`, todas as palavras-passe que tenham um `pwdLastSet` mais antiga do que 90 dias exigir que o utilizador para alterá-los da próxima vez que iniciar sessão. Esta alteração pode afetar a um grande número de utilizadores. 

## <a name="next-steps"></a>Passos seguintes

Os artigos seguintes fornecem informações adicionais sobre a reposição através do Azure AD palavra-passe:

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
