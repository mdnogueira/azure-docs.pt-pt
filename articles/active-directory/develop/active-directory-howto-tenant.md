---
title: Como obter um inquilino do Azure AD | Microsoft Docs
description: "Como obter um inquilino do Azure Active Directory para registar e criar aplicações."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: fe33d490b754e2f793f5c7a13dc55ca038b1b71c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Como obter um inquilino do Azure Active Directory
No Azure Active Directory (Azure AD), uma organização é representada por um [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant).  Quando assina um serviço em nuvem da Microsoft, tal como o Azure, o Microsoft Intune ou o Office 365, a organização recebe e fica proprietária de uma instância dedicada do serviço Azure AD.  Cada inquilino do Azure AD é distinto e separado dos outros inquilinos do Azure AD.  

Um inquilino aloja os utilizadores duma empresa e respetivas informações: palavras-passe, dados de perfil de utilizador, permissões, etc.  Também contém grupos, aplicações e outras informações relativas a uma organização e à sua segurança.

Para permitir que os utilizadores do Azure AD iniciem sessão na aplicação, tem de registá-la no seu próprio inquilino.  A publicação de uma aplicação num inquilino do Azure AD é **absolutamente gratuita**.  Na verdade, a maior parte dos programadores cria vários inquilinos e aplicações com o objetivo de experimentar, desenvolver e testar.  As organizações que subscrevem e consomem a aplicação podem optar por adquirir licenças se desejarem tirar partido das funcionalidades avançadas do diretório.

Por isso, como deve proceder para obter um inquilino do Azure AD?  O processo poderá ser um pouco diferente se:

* [Tiver já uma subscrição do Office 365](#use-an-existing-office-365-subscription)
* [Tiver já uma subscrição do Azure associada a uma Conta Microsoft](#use-an-msa-azure-subscription)
* [Tiver já uma subscrição do Azure associada a uma conta da organização](#use-an-organizational-azure-subscription)
* [Não tiver nada do que está acima e pretender começar do zero](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Utilizar uma subscrição do Office 365 existente
Se tiver uma subscrição existente do Office 365, já tem um inquilino do Azure AD! Pode iniciar sessão no [Portal do Azure](https://portal.azure.com) com a conta do Office 365 e começar a utilizar o Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Utilizar uma subscrição do Azure MSA
Se já tiver uma subscrição do Azure com a sua conta individual da Microsoft, já possui um inquilino!  Quando iniciar sessão no [Portal do Azure](https://portal.azure.com), será automaticamente registado no seu inquilino predefinido. Pode utilizar este inquilino como julgar conveniente, mas poderá querer criar uma conta de administrador organizacional.

Para tal, siga estes passos.  Em alternativa, poderá pretender criar um novo inquilino e criar um administrador nesse inquilino seguindo um processo semelhante.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) com a sua conta individual
2. Navegue para a secção “Azure Active Directory” do portal (que se encontra na barra de navegação esquerda, em **Mais Serviços**)
3. Deve ter a sessão automaticamente iniciada no "Diretório Predefinido", caso contrário, pode mudar de diretório, ao clicar no nome da sua conta no canto superior direito.
4. Na secção **Tarefas Rápidas**, escolha **Adicionar um utilizador**.
5. No formulário Adicionar Utilizador, indique a seguinte informação:

   * Nome: (escolher um valor adequado)
   * Nome de Utilizador: (escolher um nome de utilizador para este administrador)
   * Perfil: (preencher os valores adequados para o Nome Próprio, Apelido, Cargo e Departamento)
   * Função: Administrador Global
6. Após preencher o formulário Adicionar Utilizador e receber a palavra-passe temporária do novo utilizador administrativo, certifique-se de tomar nota desta palavra-passe pois terá de iniciar sessão com este novo utilizador para alterar a palavra-passe. Também pode enviar a palavra-passe diretamente para o utilizador, utilizando um e-mail alternativo.
7. Clique em **Criar**, para criar o novo utilizador.
8. Para alterar a palavra-passe temporária, inicie sessão em [https://login.microsoftonline.com](https://login.microsoftonline.com) com esta nova conta de utilizador e altere a palavra-passe quando solicitado.

## <a name="use-an-organizational-azure-subscription"></a>Utilizar uma subscrição organizacional do Azure
Se já tiver uma subscrição do Azure com a sua conta organizacional, já possui um inquilino!  No [Portal do Azure](https://portal.azure.com), encontrará um inquilino ao navegar para "Mais Serviços" e "Azure Active Directory."  Pode utilizar este inquilino como julgar conveniente.

## <a name="start-from-scratch"></a>Começar do zero
Se tudo o que estiver acima não fizer qualquer sentido, não se preocupe.  Visite simplesmente [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para subscrever o Azure com uma nova organização.  Depois de concluir o processo, terá o seu próprio inquilino no Azure AD com o nome de domínio que escolheu durante a subscrição.  No [Portal do Azure](https://portal.azure.com), pode encontrar o seu inquilino ao navegar para “Azure Active Directory” na barra de navegação esquerda.

Como parte do processo de subscrição do Azure, ser-lhe-á pedido para fornecer os detalhes do cartão de crédito.  Pode continuar com confiança, não lhe será cobrado nada por publicar aplicações no Azure AD ou por criar novos inquilinos.
