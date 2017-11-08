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
ms.openlocfilehash: 9d61f46070e6956c60f1135b98a9ebe71011b922
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas de palavra-passe e restrições no Azure Active Directory

Este artigo descreve os requisitos de complexidade associados a contas de utilizador armazenadas no seu inquilino do Azure AD e políticas de palavra-passe.

## <a name="administrator-password-policy-differences"></a>Diferenças de política de palavra-passe de administrador

A Microsoft impõe uma política de reposição de palavras-passe de **duas portas** para qualquer função de administrador do Azure (Exemplo: Administrador Global, Administrador de Suporte Técnico, Administrador de Palavras-passe, etc.)

Isto desativa os administradores de utilizar perguntas de segurança e impõe as seguintes.

Política, que requerem dois tipos de dados de autenticação de dois porta (endereço de correio eletrónico **e** número de telefone), aplica-se nas seguintes circunstâncias

* Todas as funções de administrador do Azure
  * Administrador de suporte técnico
  * Administrador de suporte de serviço
  * Administrador de faturação
  * Suporte de Tier1 parceiro
  * Suporte de Tier2 parceiro
  * Administrador de serviço do Exchange
  * Administrador de serviço do Lync
  * Administrador de conta de utilizador
  * Escritores de diretório
  * Administrador de empresa/administrador global
  * Administrador de serviço do SharePoint
  * Administrador de compatibilidade
  * Administrador da aplicação
  * Administrador de segurança
  * Com privilégios de função de administrador
  * Administrador de serviço do Intune
  * Administrador de serviço de Proxy de aplicações
  * Administrador de serviço do CRM
  * Administrador de serviço do Power BI
  
* uma avaliação de 30 dias decorridos **ou**
* Domínio de está presente (contoso.com) **ou**
* O Azure AD Connect está a sincronizar as identidades do seu diretório no local

### <a name="exceptions"></a>Exceções
Política de uma porta, a necessidade de um conjunto de dados de autenticação (endereço de correio eletrónico **ou** número de telefone), aplica-se nas seguintes circunstâncias

* Primeiro de 30 dias numa versão de avaliação **ou**
* Domínio de não está presente (*. c o m) **e** do Azure AD Connect não está a sincronizar identidades


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas de UserPrincipalName que se aplicam a todas as contas de utilizador

Cada conta de utilizador que tem de iniciar sessão Azure AD tem de ter um valor de atributo de nome principal (UPN) de utilizador exclusivo associado à sua conta. A tabela abaixo destaca as políticas que se aplicam a ambas as contas de utilizador do Active Directory sincronizadas para a nuvem e às contas de utilizador só de nuvem no local.

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Carateres permitidos |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Não são permitidos carateres |<ul> <li>Qualquer ' @' caráter que não é separar o nome de utilizador do domínio.</li> <li>Não pode conter um caráter período '.' imediatamente anterior o ' @' símbolo</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não pode exceder 113 carateres</li><li>64 carateres antes do ' @' símbolo</li><li>48 carateres após o ' @' símbolo</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Políticas de palavra-passe que se aplicam apenas a contas de utilizador de nuvem

A tabela seguinte descreve as definições de política de palavra-passe disponíveis que podem ser aplicadas a contas de utilizador que são criadas e geridas no Azure AD.

| Propriedade | Requisitos |
| --- | --- |
| Carateres permitidos |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Não são permitidos carateres |<ul><li>Carateres Unicode</li><li>Espaços</li><li> **Apenas palavras-passe fortes**: não pode conter um caráter de ponto '.' imediatamente anterior o ' @' símbolo</li></ul> |
| Restrições de palavra-passe |<ul><li>mínimo de 8 carateres e 16 carateres no máximo</li><li>**Apenas palavras-passe fortes**: 3 requer em 4 dos seguintes procedimentos:<ul><li>Caracteres em minúsculas</li><li>Carateres maiúsculos</li><li>Números (0-9)</li><li>Símbolos (consultar acima de restrições de palavra-passe)</li></ul></li></ul> |
| Duração de expiração da palavra-passe |<ul><li>Valor predefinido: **90** dias </li><li>O valor é configurável utilizando o cmdlet Set-MsolPasswordPolicy do módulo Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de palavra-passe |<ul><li>Valor predefinido: **14** dias (antes de expira a palavra-passe)</li><li>O valor é configurável utilizando o cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiração da palavra-passe |<ul><li>Valor predefinido: **falso** dias (indica que expiração de palavra-passe está ativada) </li><li>Valor pode ser configurado para utilizar o cmdlet Set-MsolUser de contas de utilizador individuais. </li></ul> |
| Palavra-passe **alterar** histórico |Última palavra-passe **não é possível** ser utilizadas novamente quando **alterar** uma palavra-passe. |
| Palavra-passe **repor** histórico | Última palavra-passe **poderá** ser utilizadas novamente quando **repor** uma palavra-passe esquecida. |
| Bloqueio de conta |Após 10 sem início de sessão tentativas (palavra-passe errada), o utilizador será bloqueado para um minuto. Ainda mais o início de sessão incorreto tenta bloqueio enviados ao utilizador para aumentar a duração. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Definir políticas de expiração de palavra-passe no Azure Active Directory

Um administrador global para um serviço em nuvem da Microsoft pode utilizar o Microsoft Azure Active Directory módulo para o Windows PowerShell para configurar palavras-passe do utilizador para não expirarem. Também pode utilizar cmdlets do Windows PowerShell para remover o nunca-expira a configuração ou para ver o que utilizador palavras-passe estão configuradas para não expirarem. Esta orientação aplica-se para outros fornecedores de serviços como o Microsoft Intune e o Office 365, que também pode confiar no Microsoft Azure Active Directory para serviços de identidade e de diretório. Esta é a parte apenas da política que pode ser alterada.

> [!NOTE]
> Apenas as palavras-passe para contas de utilizador que não estão sincronizadas através de sincronização de diretórios podem ser configuradas para não expirarem. Para obter mais informações sobre a sincronização de diretórios consulte[Connect AD com o Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>Definir ou políticas de palavra-passe através do PowerShell, consulte

Para começar, terá de [transferir e instalar o módulo Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois de ter instalado, pode seguir os passos abaixo para configurar cada campo.

### <a name="how-to-check-expiration-policy-for-a-password"></a>Como verificar a política de expiração de uma palavra-passe
1. Ligar ao Windows PowerShell com as suas credenciais de administrador da empresa.
2. Execute um dos seguintes comandos:

   * Para ver se a palavra-passe de um único utilizador está definida para nunca expirar, execute o seguinte cmdlet com o nome principal de utilizador (UPN) (por exemplo, aprilr@contoso.onmicrosoft.com) ou o ID de utilizador do utilizador que pretende verificar:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Para ver a definição "Palavra-passe nunca expira" para todos os utilizadores, execute o seguinte cmdlet:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Definir uma palavra-passe a expirar

1. Ligar ao Windows PowerShell com as suas credenciais de administrador da empresa.
2. Execute um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para que a palavra-passe expira, execute o seguinte cmdlet utilizando o nome principal de utilizador (UPN) ou o ID de utilizador do utilizador:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para definir as palavras-passe de todos os utilizadores na organização de modo a que expirarem, utilize o seguinte cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Definir uma palavra-passe nunca expira

1. Ligar ao Windows PowerShell com as suas credenciais de administrador da empresa.
2. Execute um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para nunca expirar, execute o seguinte cmdlet utilizando o nome principal de utilizador (UPN) ou o ID de utilizador do utilizador:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para definir as palavras-passe de todos os utilizadores numa organização nunca expirem, execute o seguinte cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Se definir `-PasswordNeverExpires $true` a palavra-passe será ainda idade com base no `pwdLastSet` atributo. Isto significa que, se definir palavras-passe nunca expira e, em seguida, 90 dias aceda com base no `pwdLastSet` e alterar `-PasswordNeverExpires $false` todas as palavras-passe que tenham um `pwdLastSet` mais antiga do que 90 dias, terá de alterar no próximo início de sessão. Esta alteração pode afetar um grande número de utilizadores. 

## <a name="next-steps"></a>Passos seguintes

As ligações seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe com o Azure AD

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre Licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
