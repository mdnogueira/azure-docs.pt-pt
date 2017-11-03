---
title: "A adição de um Azure Active Directory utilizando os serviços ligados no Visual Studio | Microsoft Docs"
description: "Adicionar um Azure Active Directory utilizando a caixa de diálogo do Visual Studio adicionar ligado serviços"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>A adição de um Azure Active Directory utilizando os serviços ligados no Visual Studio
Ao utilizar o Azure Active Directory (Azure AD), pode suportar único Sign-On (SSO) para aplicações web de MVC do ASP.NET, ou a autenticação do Active Directory nos serviços de Web API. Com a autenticação do Active Directory do Azure, os utilizadores podem utilizar as contas do Azure Active Directory para ligar às suas aplicações web. As vantagens de autenticação do Active Directory do Azure com a Web API incluem a segurança de dados melhorado quando a exposição de uma API de uma aplicação web. Com o Azure AD, não é necessário que gerir um sistema de autenticação separado com a sua própria conta e o utilizador de gestão.

## <a name="prerequisites"></a>Pré-requisitos
- Conta do Azure – se não tiver uma conta do Azure, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ligar ao Azure Active Directory utilizando a caixa de diálogo Serviços ligados
1. No Visual Studio, criar ou abrir projeto MVC do ASP.NET ou um projeto API Web do ASP.NET.

1. Do Explorador de soluções, clique com botão direito do **serviços ligados** nó e, no menu de contexto, selecione **adicionar serviços ligados**.

1. No **serviços ligados** página, selecione **autenticação no Azure Active Directory**.
   
    ![Página de serviços ligada](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. No **introdução** página do **configurar o Azure AD Authentication** assistente, selecione **seguinte**.
   
    ![Página de introdução](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. No **no início de sessão único** página do **configurar o Azure AD Authentication** assistente, selecione o domínio do **domínio** na lista pendente. A lista de domínios contém todos os domínios acessíveis pelas contas listadas na caixa de diálogo Definições da conta. Como alternativa, pode introduzir um nome de domínio, se não encontrar um que procura, tais como `mydomain.onmicrosoft.com`. Pode escolher a opção para criar uma aplicação do Azure Active Directory ou utiliza as definições de uma aplicação do Azure Active Directory existente. Selecione **seguinte** quando terminar.
   
    ![Início de sessão único na página](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. No **Directory acesso** página do **configurar o Azure AD Authentication** assistente, certifique-se de que o **ler os dados de diretório** opção for selecionada. 
   
    ![Página de acesso de diretório](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Selecione **concluir** para adicionar o código de configuração necessárias e as referências para ativar o seu projeto para a autenticação do Azure AD. Pode ver o domínio do Active Directory no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Visual Studio apresentará um [o que aconteceu](#how-your-project-is-modified) artigo para lhe mostrar como o projeto foi modificado. Se pretender verificar que tudo trabalhado, abra um dos ficheiros de configuração modificado e certifique-se de que as definições mencionadas no artigo existem. 

## <a name="how-your-project-is-modified"></a>Como o projeto é modificado
Quando executar o assistente, o Visual Studio adiciona Azure Active Directory e as referências associadas ao seu projeto. Ficheiros de configuração e ficheiros de código no seu projeto também são modificados para adicionar suporte para o Azure AD. As modificações específicas que faz com que o Visual Studio dependem do tipo de projeto. Para obter informações detalhadas sobre como são modificados os projetos de MVC do ASP.NET, consulte [os projetos de MVC happened –](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para projetos de Web API, consulte [o que aconteceu – projetos de API Web](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Passos seguintes
* [Fórum MSDN do Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Documentação do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Mensagem de blogue: Introdução ao Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

