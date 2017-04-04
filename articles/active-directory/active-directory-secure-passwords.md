---
title: "Proteger palavras-passe no Azure AD e repor palavras-passe que são bloqueadas pelo Smart Password Lockout | Microsoft Docs"
description: "Explica o que é um inquilino do Azure AD e como gerir o Azure através do Azure Active Directory"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Proteger palavras-passe no Azure AD e repor palavras-passe que são bloqueadas pelo Smart Password Lockout
Este artigo cobre melhores práticas que pode seguir como utilizador ou administrador para proteger as suas contas do Azure Active Directory (Azure AD) e do Serviço de Conta Microsoft. 

 >[!NOTE]
 >Os administradores do Azure AD podem clicar no nome do diretório para repor palavras-passe de utilizador. A partir do [portal de gestão do Azure](https://manage.windowsazure.com), escolha a página Utilizadores, clique no nome do utilizador e em Repor Palavra-passe. 
 >

O Azure AD incorpora as seguintes abordagens comuns relativamente à proteção de palavras-passe:
 *    Requisitos de comprimento de palavras-passe
 *    Requisitos de “complexidade” de palavras-passe
 *    Expiração de palavras-passe regular e periódica 

Para obter informações sobre as capacidades de gestão de palavras-passe, veja [Manage passwords in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords) (Gerir palavras-passe no Azure Active Directory). 

## <a name="azure-ad-password-protection"></a>Proteção de palavras-passe do Azure AD
O Azure AD e o Sistema de Contas Microsoft utilizam abordagens comprovadas da indústria para garantir a proteção das palavras-passe de utilizadores e administradores. 

Esta secção descreve como o Azure AD protege as palavras-passe com os seguintes métodos:
 *    Palavras-passe banidas dinamicamente
 *    Smart Password Lockout

Para obter informações sobre a gestão de palavras-passe com base nas investigações atuais, veja o documento técnico [Password Guidance](http://aka.ms/passwordguidance) (Orientações Sobre Palavras-passe). 

### <a name="dynamically-banned-passwords"></a>Palavras-passe banidas dinamicamente
O Azure AD e o Sistema de Contas Microsoft banem dinamicamente todas as palavras-passe utilizadas frequentemente, para salvaguardar a proteção das palavras-passe. A equipa do Azure AD Identity Protection analisa, rotineiramente, as listas de palavras-passe banidas, impedindo os utilizadores de selecionar as que são mais utilizadas. Este serviço está disponível para clientes do Azure AD e do Serviço de Conta Microsoft. 

Ao criar palavras-passe, é importante que os administradores incentivem os utilizadores a escolher expressões para palavras-passe pouco comuns, que incluam uma combinação única de letras, números e carateres. Desta forma, torna-se praticamente impossível colocar em risco as palavras-passe dos utilizadores. 

**Listas de violações**

O Azure AD está sempre a trabalhar para estar um passo à frente dos cibercriminosos. Uma forma que utilizamos para isso é impedir que os utilizadores criem palavras-passe que estejam na lista de ataques atual.

A equipa do Azure AD Identity Protection analisa continuamente as palavras-passe que são utilizadas mais vezes. Os cibercriminosos também utilizam estratégias semelhantes para informar os respetivos ataques, que podem incluir a criação de [rainbow tables](https://en.wikipedia.org/wiki/Rainbow_table), para descodificar hashes de palavras-passe. 

A Microsoft analisa continuamente [violações a dados](https://www.privacyrights.org/data-breaches), para manter uma lista de palavras-passe banidas dinamicamente atualizada, assegurando que as palavras-passe vulneráveis são banidas antes de se tornarem uma ameaça real aos clientes do Azure AD. Para obter mais informações sobre os nossos esforços de segurança atuais, veja [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx) (Relatório de Informações de Segurança da Microsoft). 

### <a name="smart-password-lockout"></a>Smart Password Lockout

Quando o Azure AD deteta que um potencial cibercriminoso está a tentar piratear uma palavra-passe de utilizador, bloqueamos essa conta com o Smart Password Lockout. O Azure AD foi concebido para determinar o risco associado a sessões de início de sessão específicas. 

Utilizando os dados de segurança mais atualizados, aplicamos semântica de bloqueio às ameaças cibernéticas. Desta forma, os utilizadores não são bloqueados, na eventualidade de um cibercriminoso ter conseguido piratear as palavras-passe de utilizador da sua rede.

Se um utilizador for bloqueado do Azure AD, o ecrã do mesmo será semelhante ao seguinte:

  ![Bloqueado do Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
E relativamente a outras contas Microsoft, o ecrã será parecido com:

  ![Bloqueado de uma conta Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Para obter informações sobre a gestão de palavras-passe no Azure Active Directory, veja [How password management works](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works) (Como funciona a gestão de palavras-passe).

  >![NOTE] ISe for administrador do Azure AD, poderá ser útil utilizar o [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) para impedir que os seus utilizadores criem palavras-passe tradicionais.
  >

## <a name="next-steps"></a>Passos seguintes
[Como atualizar a sua própria palavra-passe](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[The fundamentals of Azure identity management](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity) (Noções básicas da gestão de identidades do Azure)<br>
[How to get operational insights with password management reports](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity) (Como obter informações operacionais com relatórios de gestão de palavras-passe)



