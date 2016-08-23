<properties
   pageTitle="Introdução ao Azure AD Privileged Identity Management | Microsoft Azure"
   description="Saiba como gerir identidades privilegiadas com a aplicação do Azure Active Directory Privileged Identity Management no Portal do Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/29/2016"
   ms.author="kgremban"/>

# Introdução ao Azure AD Privileged Identity Management


O Azure Active Directory (AD) Privileged Identity Management permite-lhe gerir, controlar e monitorizar as identidades privilegiadas e aceder a recursos no Azure AD, bem como outros serviços online Microsoft como o Office 365 ou o Microsoft Intune.  

Este artigo mostra-lhe como adicionar a aplicação Azure AD PIM ao dashboard do Portal do Azure.

## Adicionar a aplicação Privileged Identity Management

Antes de utilizar o Azure AD Privileged Identity Management, terá de adicionar a aplicação ao dashboard do Portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) como administrador global do diretório.
2. Se a organização tiver mais do que um diretório, clique no nome de utilizador no canto superior direito do Portal do Azure e selecione o diretório onde utilizará o PIM.
3. Selecione **Novo** > **Segurança + Identidade** > **Azure AD Privileged Identity Management**.

    ![Ativar PIM no portal][1]

4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de Privileged Identity Management será aberta.


Se for a primeira pessoa a utilizar o Azure AD Privileged Identity Management no diretório, o [assistente de segurança](active-directory-privileged-identity-management-security-wizard.md) irá guiá-lo na experiência de atribuição inicial. Depois disso, tornar-se-à automaticamente o primeiro **Administrador de segurança** e **Administrador com função privilegiada** no diretório. Apenas um administrador com função privilegiada pode aceder a esta aplicação para gerir o acesso de outros administradores.  

Se lhe foi atribuída uma ou mais funções, tem a opção de **Ativar as minhas funções**. Se for um administrador com função privilegiada, verá também uma opção para **Gerir funções privilegiadas**.  


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passos seguintes

A [descrição geral do Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclui mais informações sobre como pode gerir o acesso administrativo na organização.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png



<!--HONumber=Aug16_HO1-->


