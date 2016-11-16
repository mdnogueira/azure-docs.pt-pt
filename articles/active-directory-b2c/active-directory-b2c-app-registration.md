---
title: "Azure Active Directory B2C: Registo de aplicação | Microsoft Docs"
description: "Como registar a aplicação com o Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9078d36789c3cc653b298b7a4eaee1cbe888b85f


---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C : Registar a aplicação
## <a name="prerequisite"></a>Pré-requisito
Para criar uma aplicação que aceite a inscrição e o início de sessão do consumidor, terá primeiro de registar a aplicação com um inquilino do Azure Active Directory B2C. Obtenha o seu inquilino, utilizando os passos descritos em [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todos os passos do respetivo artigo, terá o painel de funcionalidades do B2C afixado no seu Startboard.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Navegar para o painel de funcionalidades do B2C
Se tiver o painel de funcionalidades do B2C afixado no seu Startboard, irá vê-lo assim que iniciar sessão no [Portal do Azure](https://portal.azure.com/) como Administrador Global do inquilino do B2C.

Também pode aceder ao painel ao clicar em **Procurar** e depois em **Azure AD B2C** no painel de navegação à esquerda no [Portal do Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Precisa de ser um Administrador Global de inquilino do B2C para poder aceder ao painel de funcionalidades do B2C. Um Administrador Global de qualquer outro inquilino ou um utilizador de qualquer outro inquilino não poderá aceder.  Pode mudar para o seu inquilino B2C ao utilizar o alternador inquilino no canto superior-direito do Portal do Azure.
> 
> 

## <a name="register-an-application"></a>Registar uma aplicação
1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
2. Clique em **+ Adicionar** na parte superior do painel.
3. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “Contoso B2C app”.
4. Se estiver a escrever uma aplicação baseada na web, alterne o botão **Incluir web app / web API** para **Sim**. As **URLs de resposta** são pontos finais para onde o Azure AD B2C devolverá qualquer tokens que a aplicação solicite. Por exemplo, introduza `https://localhost:44321/`. Se a sua aplicação web também chamar algumas APIs da web protegidas pelo Azure AD B2C, vai querer criar um **Segredo de Aplicação** ao clicar no botão **Gerar Chave**.
   
   > [!NOTE]
   > Um **Segredo de Aplicação** é uma credencial de segurança importante e deve ser protegida devidamente.
   > 
   > 
5. Se estiver a escrever uma aplicação móvel, ative/desative o botão **Incluir cliente nativo** para **Sim**. Copie o **URI de redirecionamento** predefinido que é criado automaticamente para si.
6. Clique em **Criar** para registar a aplicação.
7. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.

> [!IMPORTANT]
> As aplicações criadas no painel de funcionalidades B2C têm de ser geridas no mesmo sítio. Se utilizar o PowerShell ou outro portal para editar as aplicações B2C, estas deixam de ser suportadas e é provável que não funcionem com o Azure AD B2C.
> 
> 

## <a name="build-a-quick-start-application"></a>Criar uma Aplicação de Início Rápido
Agora que tem uma aplicação registada no Azure AD B2C, pode concluir um dos nossos tutoriais de início rápido para começar a trabalhar. Seguem-se algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]




<!--HONumber=Nov16_HO2-->


