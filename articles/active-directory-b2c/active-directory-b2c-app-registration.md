---
title: "Azure Active Directory B2C: Registo de aplicação | Microsoft Docs"
description: "Como registar a aplicação com o Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e04fbd97dd4d5ecaf12edf47d80572b32d29ed00
ms.lasthandoff: 03/23/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C : Registar a aplicação

> [!IMPORTANT]
> As aplicações criadas a partir do painel Azure AD B2C no portal do Azure têm de ser geridas a partir da mesma localização. Se utilizar o PowerShell ou outro portal para editar as aplicações B2C, estas deixam de ser suportadas e é provável que não funcionem com o Azure AD B2C.
> 
> 

## <a name="prerequisite"></a>Pré-requisito
Para criar uma aplicação que aceite a inscrição e o início de sessão do consumidor, terá primeiro de registar a aplicação com um inquilino do Azure Active Directory B2C. Obtenha o seu inquilino, utilizando os passos descritos em [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todos os passos do respetivo artigo, terá o painel de funcionalidades do B2C afixado no seu Startboard.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Navegar para o painel de funcionalidades do B2C
Se tiver o painel de funcionalidades do B2C afixado no seu Startboard, irá vê-lo assim que iniciar sessão no [Portal do Azure](https://portal.azure.com/) como Administrador Global do inquilino do B2C.

Também pode aceder ao painel ao clicar em **Mais serviços** e depois pesquisar **Azure AD B2C** no painel de navegação à esquerda no [Portal do Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Precisa de ser um Administrador Global de inquilino do B2C para poder aceder ao painel de funcionalidades do B2C. Um Administrador Global de qualquer outro inquilino ou um utilizador de qualquer outro inquilino não poderá aceder.  Pode mudar para o seu inquilino B2C ao utilizar o comutador do inquilino no canto superior direito do portal do Azure.
> 
> 

## <a name="register-a-web-application"></a>Registar uma aplicação Web
1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
2. Clique em **+ Adicionar** na parte superior do painel.
3. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “Contoso B2C app”.
4. Coloque o comutador **Incluir aplicação Web/API Web** em **Sim**. As **URLs de resposta** são pontos finais para onde o Azure AD B2C devolverá qualquer tokens que a aplicação solicite. Por exemplo, introduza `https://localhost:44316/`.
5. Clique em **Criar** para registar a aplicação.
6. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código. 
7. Se a sua aplicação web também chamar uma API da web protegida pelo Azure AD B2C, vai querer criar um **Segredo de Aplicação** ao aceder ao painel **Chaves** e clicar no botão **Gerar Chave**.

> [!NOTE]
> Um **Segredo de Aplicação** é uma credencial de segurança importante e deve ser protegida devidamente.
> 
   

## <a name="register-a-web-api"></a>Registar uma API Web
1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
2. Clique em **+ Adicionar** na parte superior do painel.
3. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “API Contoso B2C”.
4. Coloque o comutador **Incluir aplicação Web/API Web** em **Sim**. As **URLs de resposta** são pontos finais para onde o Azure AD B2C devolverá qualquer tokens que a aplicação solicite. Por exemplo, introduza `https://localhost:44316/`.
5. Introduza um **URI de ID de Aplicação**. Este é o identificador utilizado para a API Web. Por exemplo, introduza "notas". Irá gerar o URI do identificador completo abaixo. 
6. Clique em **Criar** para registar a aplicação.
7. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.
8. Clique em **Âmbitos publicados**. Esta é a secção onde pode definir as permissões (âmbitos) que podem ser concedidas a outras aplicações.
9. Adicione mais âmbitos conforme necessário. Por predefinição, será definido o âmbito "user_impersonation". Esta ação fornece a outras aplicações a capacidade de aceder a esta api em nome do utilizador com sessão iniciada. Isto pode ser removido se desejar. 
10. Clique em **Guardar**.

## <a name="register-a-mobilenative-application"></a>Registar uma aplicação móvel/nativa
1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
2. Clique em **+ Adicionar** na parte superior do painel.
3. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “Contoso B2C app”.
4. Coloque o comutador **Incluir cliente nativo** em **Sim**.
5. Introduza um **URI de redirecionamento** com um esquema personalizado. Por exemplo, com.onmicrosoft.contoso.appname://redirect/path. Certifique-se de que seleciona um [URI de redirecionamento adequado](#choosing-a-redirect-uri).
6. Clique em **Guardar** para registar a aplicação.
7. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.
8. Se a sua aplicação nativa também chamar uma API da web protegida pelo Azure AD B2C, vai querer criar um **Segredo de Aplicação** ao aceder ao painel **Chaves** e clicar no botão **Gerar Chave**.

> [!NOTE]
> Um **Segredo de Aplicação** é uma credencial de segurança importante e deve ser protegida devidamente.
> 

### <a name="choosing-a-redirect-uri"></a>Selecionar um URI de redirecionamento
Existem duas considerações importantes sobre quando selecionar um URI de redirecionamento para aplicações móveis/nativas: 
* **Exclusivo**: o esquema do URI de redirecionamento deve ser exclusivo para cada aplicação. No nosso exemplo (com.onmicrosoft.contoso.appname://redirect/path), utilizamos com.onmicrosoft.contoso.appname como esquema. Recomendamos seguir este padrão. Se duas aplicações partilharem o mesmo esquema, o utilizador verá uma caixa de diálogo "selecionar aplicação". Se o utilizador fizer uma seleção incorreta, o início de sessão falhará. 
* **Completo**: o URI de redirecionamento tem de ter um esquema e um caminho. O caminho tem de conter pelo menos uma barra depois do domínio (por exemplo, //contoso/ irá funcionar e //contoso irá falhar). 

## <a name="build-a-quick-start-application"></a>Criar uma Aplicação de Início Rápido
Agora que tem uma aplicação registada no Azure AD B2C, pode concluir um dos nossos tutoriais de início rápido para começar a trabalhar. Seguem-se algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


