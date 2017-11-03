---
title: "Introdução à autenticação do Azure AD através do portal do Azure | Microsoft Docs"
description: "Saiba como utilizar o portal do Azure para aceder a autenticação do Azure Active Directory (Azure AD) para consumir API de serviços de suporte de dados do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 829e0759f8aeb8758f6b8895b88b60b66ffb22ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introdução à autenticação do Azure AD através do portal do Azure

Saiba como utilizar o portal do Azure para aceder a autenticação do Azure Active Directory (Azure AD) para aceder à API de serviços de suporte de dados do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta, comece por um [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta de Media Services do Azure utilizando o portal do Azure](media-services-portal-create-account.md).
- Certifique-se de que revê o [aceder à API do Azure suporte de dados dos serviços de descrição geral de autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Quando utiliza a autenticação do Azure AD com Media Services do Azure, tem duas opções de autenticação:

- **Autenticação de utilizador**. Autentica uma pessoa que está a utilizar a aplicação para interagir com recursos de Media Services. A aplicação interativa do primeiro deve solicitar credenciais ao utilizador. Um exemplo é uma aplicação de consola de gestão utilizada por utilizadores autorizados para monitorizar as tarefas de codificação ou transmissão em direto. 
- **Autenticação principal de serviço**. Autenticar-se um serviço. As aplicações que utilizam frequentemente este método de autenticação são as aplicações que executem serviços de daemon, serviços de camada média ou tarefas agendadas: as web apps, aplicações de função, as logic apps, APIs ou um microsserviço.

> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação do serviço de controlo de acesso do Azure. No entanto, a autorização de controlo de acesso vai ser preterida no dia 1 de Junho de 2018. Recomendamos a migração para o modelo de autenticação do Azure AD logo que possível.

## <a name="select-the-authentication-method"></a>Selecione o método de autenticação

1. No [portal do Azure](https://portal.azure.com/), selecione a sua conta de Media Services.
2. Selecione a forma de ligar à API de serviços de suporte de dados.

    ![Selecione a página de método de ligação](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Autenticação de utilizador

Para ligar a API de serviços de suporte de dados utilizando a opção de autenticação de utilizador, a aplicação de cliente precisa de pedir um token do Azure AD que tenha os seguintes parâmetros:  

* Ponto final de inquilino do Azure AD
* URI do recurso dos Media Services
* ID de cliente da aplicação de Media Services (nativo) 
* URI de redirecionamento da aplicação de Media Services (nativo) 
* URI para serviços de suporte de dados REST do recurso

Pode obter os valores para estes parâmetros **API dos serviços de suporte de dados com a autenticação de utilizador** página. 

![Ligar à página de autenticação de utilizador](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Se ligar à API de serviços de suporte de dados utilizando o SDK .NET do suporte de dados dos serviços Microsoft, os valores necessários são disponibilizados como parte do SDK. Para obter mais informações, consulte [autenticação de utilização do Azure AD para aceder à API de serviços de suporte de dados do Azure com o .NET](media-services-dotnet-get-started-with-aad.md).

Se não estiver a utilizar o SDK do cliente do .NET dos Media Services, tem de criar manualmente um pedido de token do Azure AD, utilizando os parâmetros debatidos anteriormente. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o Azure AD token](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para ligar a API de serviços de suporte de dados utilizando a opção de principal de serviço, a aplicação de camada média (web API ou aplicação web) tem de pedir um token do Azure AD que tenha os seguintes parâmetros:  

* Ponto final de inquilino do Azure AD
* URI do recurso dos Media Services 
* URI para serviços de suporte de dados REST do recurso
* Os valores de aplicação do Azure AD: o **ID de cliente** e **segredo do cliente**

Pode obter os valores para estes parâmetros **ligar a API dos serviços de suporte de dados com o principal de serviço** página. Utilize esta página para criar uma nova aplicação do Azure AD ou selecione um existente. Depois de selecionar a aplicação do Azure AD, pode obter o ID de cliente (ID de aplicação) e gerar os valores de (chave) do segredo do cliente. 

![Ligar à página principal do serviço](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Quando o **Principal de serviço** abre painel, está selecionada a primeira aplicação do Azure AD que cumpra os seguintes critérios:

- É um registado aplicação do Azure AD.
- Possui permissões de contribuinte ou controlo de acesso de Owner Role-Based na conta.

Depois de criar ou selecionar uma aplicação do Azure AD, pode criar e copie um segredo do cliente (chave) e o ID de cliente (ID de aplicação). O segredo do cliente e o ID de cliente são necessárias para aceder a token neste cenário.

Se não tiver permissões para criar aplicações do Azure AD no seu domínio, não são mostrados os controlos de aplicação do Azure AD do painel e é apresentada uma mensagem de aviso.

Se ligar à API de serviços de suporte de dados utilizando o SDK de .NET de Media Services, consulte [autenticação de utilização do Azure AD para aceder à API de serviços de suporte de dados do Azure com o .NET](media-services-dotnet-get-started-with-aad.md).

Se não estiver a utilizar o SDK do cliente do .NET dos Media Services, tem de criar manualmente um pedido de token do Azure AD utilizando os parâmetros debatidos anteriormente. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o Azure AD token](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Obter o ID de cliente e o segredo do cliente

Depois de selecionar uma aplicação do Azure AD existente ou selecione a opção para criar um novo, são apresentados os botões seguintes:

![Gerir o botão de permissões e gerir botão de aplicação](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Para abrir o painel de aplicações do Azure AD, clique em **Gerir aplicação**. No **Gerir aplicação** painel, pode obter o ID da aplicação cliente (ID de aplicação). Para gerar um segredo do cliente (chave), selecione **chaves**.

![Gerir a opção de chaves do painel de aplicações](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Gerir permissões e a aplicação

Depois de selecionar a aplicação do Azure AD, pode gerir as aplicações e as permissões. Para configurar a sua aplicação do Azure AD para aceder a outras aplicações, clique em **gerir permissões**. Para tarefas de gestão, tais como a alteração de chaves e os URLs de resposta, ou para editar o manifesto da aplicação, clique em **Gerir aplicação**.

### <a name="edit-the-apps-settings-or-manifest"></a>Editar definições da aplicação ou manifest

Para editar as definições ou o manifesto da aplicação, clique em **Gerir aplicação**.

![Gerir a página da aplicação](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Passos seguintes

Introdução ao [carregar ficheiros para a conta](media-services-portal-upload-files.md).
