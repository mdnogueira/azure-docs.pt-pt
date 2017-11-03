---
title: "Descrição geral do Protocolo .NET do Azure AD | Microsoft Docs"
description: "Como utilizar mensagens HTTP para autorizar o acesso a aplicações Web e APIs Web no inquilino utilizando o Azure AD."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
ms.openlocfilehash: 42ce8dfd30cda7d4085778954350550fd9fdf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## Registar a aplicação com o inquilino do AD
Em primeiro lugar, tem de registar a aplicação com o inquilino do Azure Active Directory (Azure AD). Esta ação dar-lhe-á um ID de Aplicação para a aplicação e ativá-lo-á para receber tokens.

* Inicie sessão no [Portal do Azure](https://portal.azure.com).
* Escolha o inquilino do Azure AD ao clicar na conta no canto superior direito da página.
* No painel de navegação da esquerda, clique em **Azure Active Directory**.
* Clique em **Registos de Aplicação** e clique em **Adicionar**.
* Siga os avisos e crie uma nova aplicação. Para este tutorial não importa se é uma aplicação Web ou uma aplicação nativa, mas se pretender exemplos específicos de aplicações Web ou aplicações nativas, veja os [inícios rápidos](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Para aplicações Web, forneça o **URL de Início de sessão** que é o URL base da aplicação, onde os utilizadores podem iniciar sessão, por exemplo `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Para Aplicações Nativas, forneça um **URI de Redirecionamento** que o Azure AD irá utilizar para devolver respostas token. Introduza um valor específico para a aplicação, por exemplo `http://MyFirstAADApp`
* Depois de concluir o registo, o Azure AD atribuirá um identificador de cliente exclusivo à aplicação, o ID da Aplicação. Este valor será necessário nas secções seguintes, por conseguinte, copie-o a partir da página da aplicação.
