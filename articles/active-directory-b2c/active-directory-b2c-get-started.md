---
title: 'Pré-visualização do Azure Active B2C Directory: criar um inquilino do Azure Active Directory B2C | Microsoft Docs'
description: Um tópico sobre como criar um inquilino do Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2016
ms.author: swkrish

---
# Pré-visualização do Azure Active Directory B2C: criar um inquilino do Azure AD B2C
Para começar a utilizar o Microsoft Azure Active Directory (Azure AD) B2C, siga os três passos descritos neste artigo.

[!INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Passo 1: Inscrever-se para uma subscrição do Azure
Se já tiver uma subscrição do Azure, ignore este passo. Se não, inscreva-se numa [subscrição do Azure](../active-directory/sign-up-organization.md) e obtenha acesso ao Azure AD B2C.

> [!NOTE]
> Atualmente, pode utilizar a pré-visualização do Azure AD B2C gratuitamente mas a sua utilização está limitada a 50 000 utilizadores por inquilino. É necessária uma subscrição do Azure para aceder ao [Portal Clássico do Azure](http://manage.windowsazure.com/).
> 
> 

## Passo 2: Criar um inquilino do Azure AD B2C
Utilize os seguintes passos para criar um novo inquilino do Azure AD B2C. Atualmente as funcionalidades do B2C não podem ser ativadas nos seus diretórios existentes, caso existam.

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/) como Administrador da Subscrição. Esta é a mesma conta profissional ou de estudante ou a mesma conta Microsoft que utilizou para inscrever-se no Azure.
2. Clique em **Novo** > **Serviços de Aplicações** > **Active Directory** > **Diretório** > **Criação Personalizada**.
   
    ![Captura de ecrã do início da criação de um inquilino](./media/active-directory-b2c-get-started/new-directory.png)
3. Escolha o **Nome**, **Nome de Domínio** e **País ou Região** do seu inquilino.
4. Selecione a opção que diz **Este é um diretório do B2C**.
5. Clique na marca de verificação para concluir a ação.
   
    ![Captura de ecrã da marca de verificação para criar um diretório do B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. O inquilino é agora criado e aparecerá na extensão do Active Directory. Também passa a ser um Administrador Global do inquilino. Pode adicionar outros administradores globais conforme necessário.

## Passo 3: Navegar para o painel de funcionalidades do B2C no Portal do Azure
1. Navegue para a extensão do Active Directory na barra de navegação no lado esquerdo.
2. Localize o seu inquilino no separador **Diretório** e clique no mesmo.
3. Clique no separador **Configurar**.
4. Clique na ligação **Gerir definições do B2C** na secção **Administração do B2C**.
   
    ![Captura de ecrã da configuração de diretório para B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. O Portal do Azure com o painel de funcionalidades do B2C apresentado é aberto num novo separador do browser ou janela.
   
   > [!IMPORTANT]
   > Pode demorar até 2 a 3 minutos para que o inquilino fique acessível no Portal do Azure. Se repetir estes passos após algum tempo, esta situação é corrigida. Caso contrário, contacte o suporte.
   > 
   > 
6. Afixe este painel ao seu Startboard para facilitar o acesso. (A ferramenta Pin é assinalada a vermelho no canto superior direito do painel de funcionalidades.)
   
    ![Captura de ecrã do painel de funcionalidades do B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > Pode gerir utilizadores e grupos, configuração de reposição de palavras-passes self-service e funcionalidades de imagem corporativa do seu inquilino no [Portal Clássico do Azure](https://manage.windowsazure.com/).
   > 
   > 

## Passos seguintes
Saiba como registar uma aplicação com o Azure AD B2C e criar uma aplicação de Início Rápido ao ler a [Pré-visualização do Azure Active Directory B2C: registar a aplicação](active-directory-b2c-app-registration.md).

<!--HONumber=Jun16_HO2-->


