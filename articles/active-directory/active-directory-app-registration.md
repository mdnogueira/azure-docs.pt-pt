---
title: "Registo de aplicações do Azure Active Directory | Microsoft Docs"
description: "Este artigo descreve como utilizar o portal do Azure para registar uma aplicação no Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 2f2817688beb2028fd0bba8522827d87a0097f21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Registar a aplicação com o seu inquilino do Azure Active Directory

Pode utilizar o portal do Azure para registar a aplicação com o seu inquilino do Azure Active Directory (Azure AD). Isto cria um ID de aplicação para a aplicação e ativa-o para receber tokens.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Escolha o seu inquilino do Azure AD, selecionando a sua conta no canto superior direito da página.
3. No painel de navegação da esquerda, escolha **mais serviços**, clique em **registos de aplicação**e clique em **adicionar**.
4. Siga os avisos e crie uma nova aplicação. Se gostaria de exemplos específicos de aplicações web ou aplicações nativas, consulte o nosso [inícios Rápidos](active-directory-developers-guide.md).
  * Para aplicações Web, forneça o **URL de início de sessão**, que é o URL de base da sua aplicação, onde os utilizadores podem iniciar sessão por exemplo `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Para aplicações nativas, forneça um **URI de redirecionamento**, que utiliza o Azure AD para devolver respostas token. Introduza um valor específico para a aplicação, por exemplo `http://MyFirstAADApp`
5. Depois de concluir o registo, o Azure AD atribui a aplicação um identificador exclusivo do cliente, o ID de aplicação.

## <a name="update-application-settings-from-the-azure-portal"></a>Atualizar as definições da aplicação do portal do Azure

Pode facilmente modificar definições de uma aplicação existente no portal do Azure. Por exemplo, poderá pretender configurar um URL de resposta, o que é onde o Azure AD emite o token respostas. Também poderá configurar as permissões para outras aplicações para a instância para permitir a sua aplicação aceder a Microsoft Graph API. Pode fazer tudo isto através da página de definições de aplicação.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Escolha o seu inquilino do Azure AD, selecionando a sua conta no canto superior direito da página.
3. No painel de navegação da esquerda, escolha **mais serviços**, clique em **registos de aplicação**e escolha a aplicação a partir da lista.
4. Clique em **definições** para abrir a página de definições para a aplicação.
  * O **propriedades** página permite modificar as informações gerais para a aplicação. Isto inclui o nome da aplicação, o URL de início de sessão e o URL de fim de sessão.
  * O **URLs de resposta** página permite-lhe adicionar um URL de resposta, o que é onde do Azure AD envia o token respostas.
  * O **proprietários** página permite-lhe adicionar os proprietários da aplicação.
  * O **permissões** página permite-lhe configurar as permissões para a aplicação. Por exemplo, para aceder a Microsoft Graph API, clique em **adicionar** e selecione **Microsoft Graph** no Seletor de API, em seguida, escolha a permissão necessária, por exemplo **ler dados de diretório**.
  * O **chaves** página permite-lhe adicionar segredos de aplicação. O segredo só será apresentado depois imediatamente após a criação, por isso, confirme copia para continuar a utilizar.

## <a name="use-the-inline-manifest-editor"></a>Utilize o editor de manifesto inline

Pode utilizar o editor de manifesto inline para modificar determinadas propriedades de aplicação que não são expostas diretamente no portal do Azure. Por exemplo, pode utilizá-lo para modificar o URI de ID de aplicação da aplicação ou para ativar o fluxo implícito OAuth2.0 em vez do fluxo de código de conceder de autorização de predefinição.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Escolha o seu inquilino do Azure AD, selecionando a sua conta no canto superior direito da página.
3. No painel de navegação da esquerda, escolha **mais serviços**, clique em **registos de aplicação**e escolha a aplicação a partir da lista.
4. Clique em **manifesto** da página de aplicação para abrir o editor de manifesto inline.
5. Pode efetuar alterações ao manifesto diretamente e guardá-lo quando estiver pronto. Em alternativa, pode transferir o manifesto para abri-lo no seu editor favorito e carregar o manifesto atualizado.

## <a name="next-steps"></a>Passos Seguintes

1. Veja o [inícios Rápidos](active-directory-developers-guide.md) para instruções detalhadas de aplicações, efetuar a autenticação com o Azure AD.
2. Consulte a nossa lista completa dos exemplos de código no [GitHub](https://github.com/azure-samples).
