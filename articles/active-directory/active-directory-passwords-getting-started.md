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
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 8497c6c6d7cfc6c4457073783d20f48a722ea18e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-azure-ad-self-service-password-reset"></a>Guia de Início Rápido: reposição de palavras-passe self-service do Azure AD

> [!IMPORTANT]
> **Está aqui porque está a ter problemas em iniciar sessão?** Se assim for, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

## <a name="rapidly-deploy-self-service-password-reset"></a>Implementar rapidamente a reposição de palavras-passe self-service

A reposição de palavras-passe self-service (Self-service Password Reste, SSPR) oferece aos administradores de TI uma forma simples de capacitarem os utilizadores a repor ou desbloquear as palavras-passe ou as contas deles. O sistema inclui relatórios detalhados para controlar quando os utilizadores utilizam o sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos.

Este guia pressupõe que já tem uma versão de avaliação ou um inquilino licenciado do Azure AD em funcionamento. Se precisar de ajuda para configurar o Azure AD, veja o artigo [Introdução ao Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

1. No seu inquilino já existente do Azure Ad, selecione **“Reposição de palavras-passe”**

2. No ecrã **“Propriedades”**, na opção “Reposição de Palavras-passe Self-service Ativada”, escolha uma das opções seguintes
    * Ninguém - ninguém pode utilizar a funcionalidade SSPR
    * Um grupo - só os membros de um grupo específico do Azure AD que escolher podem utilizar a funcionalidade SSPR
    * Toda a gente - todos os utilizadores com contas no seu inquilino do Azure AD podem utilizar a funcionalidade SSPR

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

4. RECOMENDADO: **"Personalização"** permite-lhe alterar a ligação "Contactar o administrador" para apontar para um página ou endereço de e-mail que definir

5. OPCIONAL: o ecrã **"Registo"** dá aos administradores as opções para:
    * Exigir que os utilizadores se registem ao iniciar sessão
    * Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação

6. OPCIONAL: o ecrã **"Notificação"** dá aos administradores as opções para:
    * Notificar os utilizadores sobre reposições de palavras-passe
    * Notificar todos os administradores quando outros administradores repõem as palavras-passe deles

**Neste momento, já tem configurado o SSPR no seu inquilino do Azure AD**. Pode parar por aqui ou continuar e configurar a sincronização de palavras-passe para um domínio do AD no local.

> [!NOTE]
> Teste o SSPR com um utilizador e não com um administrador, pois a Microsoft aplica requisitos de autenticação fortes às contas de tipo administrador do Azure. Para obter mais informações sobre a política de palavras-passe de administrador, veja o nosso [artigo de política de palavra-passe](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Configurar a sincronização com a origem de identidades existente

Para ativar a sincronização de identidades no local com o Azure AD, tem de instalar e configurar o [Azure AD Connect](./connect/active-directory-aadconnect.md) num servidor da sua organização. Esta aplicação processa a sincronização de utilizadores e grupos da sua origem de identidades existente com o seu inquilino do Azure AD.

* [Upgrade from DirSync or Azure AD Sync to Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md) (Atualizar de DirSync ou do Azure AD Sync para o Azure AD Connect)
* [Introdução ao Azure AD Connect com as definições rápidas](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurar a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar palavras-passe do Azure AD no seu diretório local.

## <a name="disabling-self-service-password-reset"></a>Desativar a reposição de palavras-passe self-service

Para desativar a reposição de palavras-passe self-service, basta abrir o inquilino do Azure AD e aceder a **Reposição de Palavras-passe > Propriedades** > escolher **Nenhuma**, em **Reposição de Palavras-passe Self-service Ativada**

### <a name="learn-more"></a>Saiba mais
As ligações seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe com o Azure AD

* [**Licensing**](active-directory-passwords-licensing.md) - Configure your Azure AD Licensing (Licenciamento - Configurar o Licenciamento do Azure AD)
* [**Data**](active-directory-passwords-data.md) - Understand the data that is required and how it is used for password management (Dados - Compreender os dados necessários e de que forma são utilizados na gestão de palavras-passe)
* [**Rollout**](active-directory-passwords-best-practices.md) - Plan and deploy SSPR to your users using the guidance found here (Implementação - Planear e implementar o SSPR nos seus utilizadores com as orientações disponíveis aqui)
* [**Customize**](active-directory-passwords-customize.md) - Customize the look and feel of the SSPR experience for your company (Personalizar - Personalizar o aspeto e a funcionalidade da experiência de SSPR da sua empresa)
* [**Policy**](active-directory-passwords-policy.md) - Understand and set Azure AD password policies (Política - Compreender e definir políticas de palavras-passe do Azure AD)
* [**Reporting**](active-directory-passwords-reporting.md) - Discover if, when, and where your users are accessing SSPR functionality (Relatórios - Descubra se, quando e a partir de onde é que os utilizadores acedem à funcionalidade SSPR)
* [**Technical Deep Dive**](active-directory-passwords-how-it-works.md) - Go behind the curtain to understand how it works (Análise Técnica - veja por detrás dos bastidores para saber como funciona)
* [**Frequently Asked Questions**](active-directory-passwords-faq.md) - How? Porquê? What? Where? Who? When? - Answers to questions you always wanted to ask (Perguntas Mais Frequentes - Como? Porquê? O quê? Onde? Quem? - Respostas para as perguntas que sempre quis saber)
* [**Troubleshoot**](active-directory-passwords-troubleshoot.md) - Learn how to resolve common issues that we see with SSPR (Resolução de Problemas - Saiba como resolver problemas comuns do SSPR)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, aprendeu a configurar a reposição de palavras-passe self-service para os seus utilizadores. Para continuar para o portal do Azure para concluir estes passos, siga a ligação abaixo para o portal.

> [!div class="nextstepaction"]
> [Ativar a reposição de palavras-passe self-service](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

