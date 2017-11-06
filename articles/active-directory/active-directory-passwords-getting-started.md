---
title: "Guia de Início Rápido: Azure AD SSPR | Microsoft Docs"
description: "Implementar rapidamente a reposição de palavras-passe self-service do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d97fad04a0aa549d0e3a182282f898302e8e41a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Implementação rápida da reposição personalizada de palavras-passe do Azure AD

> [!IMPORTANT]
> **Está aqui porque está a ter problemas em iniciar sessão?** Se assim for, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

A reposição de palavras-passe self-service (Self-service Password Reste, SSPR) oferece aos administradores de TI uma forma simples de capacitarem os utilizadores a repor ou desbloquear as palavras-passe ou as contas deles. O sistema inclui relatórios detalhados para controlar quando os utilizadores utilizam o sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos.

Este guia pressupõe que já tem uma versão de avaliação ou um inquilino licenciado do Azure AD em funcionamento. Se precisar de ajuda para configurar o Azure AD, veja o artigo [Introdução ao Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Ativar a SSPR para o seu inquilino do Azure AD

1. No seu inquilino já existente do Azure Ad, selecione **“Reposição de palavras-passe”**

2. No ecrã **“Propriedades”**, na opção “Reposição Personalizada de Palavras-passe Ativada”, escolha uma das seguintes opções:
    * Nenhum - ninguém pode utilizar a funcionalidade SSPR.
    * Selecionado - só os membros de um grupo específico do Azure AD que escolher podem utilizar a funcionalidade SSPR. Recomendamos que defina um grupo de utilizadores e utilize esta definição ao implementar, para uma prova de conceito.
    * Todos - todos os utilizadores com contas no seu inquilino do Azure AD podem utilizar a funcionalidade SSPR. Recomendamos que isto seja definido quando estiver pronto para implementar esta funcionalidade para o inquilino completo depois de concluir uma prova de conceito.

3. No ecrã **"Métodos de autenticação"**, escolha
    * Número de métodos necessários para repor - suportamos um mínimo de um ou um máximo de dois
    * Métodos disponíveis para os utilizadores - precisamos de, pelo menos, um; contudo, não custa nada ter uma opção extra disponível
        * **Enviar por E-mail** envia um e-mail com um código para o endereço de e-mail de autenticação configurado do utilizador
        * **Número de Telemóvel** disponibiliza ao utilizador a opção de receber uma chamada ou uma mensagem de texto com um código no telemóvel configurado
        * **Telefone do Escritório** liga para o utilizador e comunica um código no telefone do escritório configurado
        * **Perguntas de Segurança** requer que escolha
            * Número de perguntas necessárias para o registo - é o número mínimo para um registo bem-sucedido e significa que o utilizador pode optar por responder a mais para criar um conjunto de perguntas de onde escolher. Esta opção pode ser definida entre três e cinco e tem de ser igual ou maior do que o número de perguntas necessárias para a reposição.
                * Podem ser adicionadas perguntas personalizadas, clicando no botão “Personalizar” quando escolher as perguntas de segurança
            * Número de perguntas necessárias para a reposição - podem ser definidas entre três e cinco perguntas que têm de ser respondidas corretamente para que seja possível repor ou desbloquear a palavra-passe do utilizador.
            
    ![Autenticação][Authentication]

4. RECOMENDADO: a **“Personalização”** permite-lhe alterar a ligação “Contactar o administrador” para apontar para um página ou endereço de e-mail que definir. Recomendamos que defina esta ligação para algo como um endereço de e-mail ou site que os utilizadores estejam habituados a utilizar para suporte.

5. OPCIONAL: o ecrã **"Registo"** dá aos administradores as opções para:
    * Exigir que os utilizadores se registem ao iniciar sessão
    * Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação

6. OPCIONAL: o ecrã **"Notificação"** dá aos administradores as opções para:
    * Notificar os utilizadores sobre reposições de palavras-passe
    * Notificar todos os administradores quando outros administradores repõem as palavras-passe deles

**Neste momento, já tem configurado o SSPR no seu inquilino do Azure AD**. Os utilizadores podem agora utilizar as instruções nos artigos [Registar-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md) e [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md) para atualizarem a palavra-passe sem a intervenção do administrador. Pode parar por aqui se estiver apenas na cloud ou continuar e configurar a sincronização de palavras-passe para um domínio do AD no local.

> [!IMPORTANT]
> Teste o SSPR com um utilizador e não com um administrador, pois a Microsoft aplica requisitos de autenticação fortes às contas de tipo administrador do Azure. Para obter mais informações sobre a política de palavras-passe de administrador, veja o nosso [artigo de política de palavra-passe](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Configurar a sincronização com a origem de identidades existente

Para ativar a sincronização de identidades no local com o Azure AD, tem de instalar e configurar o [Azure AD Connect](./connect/active-directory-aadconnect.md) num servidor da sua organização. Esta aplicação processa a sincronização de utilizadores e grupos da sua origem de identidades existente com o seu inquilino do Azure AD.

* [Upgrade from DirSync or Azure AD Sync to Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md) (Atualizar de DirSync ou do Azure AD Sync para o Azure AD Connect)
* [Introdução ao Azure AD Connect com as definições rápidas](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurar a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar palavras-passe do Azure AD no seu diretório local.

### <a name="on-premises-policy-change"></a>Alteração à política no local

Se estiver a sincronizar os utilizadores de um domínio do Active Directory no local e pretender permitir que os utilizadores reponham as respetivas palavras-passe imediatamente, efetue a alteração seguinte na sua política de palavras-passe no local:

**Configuração do Computador** > **Políticas** > **Definições do Windows** > **Definições de Segurança**  >  **Políticas de Conta** > **Política de Palavras-passe**

**Duração mínima da palavra-passe** - 0 dias

Esta definição de segurança determina o período de tempo (em dias) que uma palavra-passe tem de ser utilizada antes de o utilizador a poder alterar. Defini-la para **0 dias** permite que os utilizadores utilizem a SSPR se as palavras-passe forem alteradas pelas respetivas equipas de suporte.

![Política][Policy]

## <a name="disabling-self-service-password-reset"></a>Desativar a reposição de palavras-passe self-service

Para desativar a reposição de palavras-passe self-service, basta abrir o inquilino do Azure AD e aceder a **Reposição de Palavras-passe > Propriedades** > escolher **Nenhuma**, em **Reposição de Palavras-passe Self-service Ativada**

### <a name="learn-more"></a>Saiba mais
As ligações seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe com o Azure AD

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre Licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, aprendeu a configurar a reposição de palavras-passe self-service para os seus utilizadores. Para continuar para o portal do Azure para concluir estes passos, siga a ligação abaixo para o portal.

> [!div class="nextstepaction"]
> [Ativar a reposição de palavras-passe self-service](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Política de grupo de palavras-passe no local definida para 0 dias"
