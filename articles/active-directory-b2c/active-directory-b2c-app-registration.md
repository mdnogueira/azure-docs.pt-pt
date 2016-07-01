<properties
    pageTitle="Pré-visualização do Azure Active Directory B2C: Registo de aplicação | Microsoft Azure"
    description="Como registar a aplicação com o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="swkrish"/>


# Pré-visualização do Azure Active Directory B2C : Registar a aplicação

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Pré-requisito

Para criar uma aplicação que aceite a inscrição e o início de sessão do consumidor, terá primeiro de registar a aplicação com um inquilino do Azure Active Directory B2C. Obtenha o seu inquilino, utilizando os passos descritos em [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todos os passos do respetivo artigo, terá o painel de funcionalidades do B2C afixado no seu Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navegar para o painel de funcionalidades do B2C

Pode navegar até o painel de funcionalidades do B2C a partir do Portal do Azure ou do Portal Clássico do Azure.

### 1. Aceda ao Portal do Azure

Se tiver o painel de funcionalidades do B2C afixado no seu Startboard, irá vê-lo assim que iniciar sessão no [Portal do Azure](https://portal.azure.com/) como Administrador Global do inquilino do B2C.

Também pode aceder ao painel ao clicar em **Procurar** e depois em **Azure AD B2C** no painel de navegação à esquerda no [Portal do Azure](https://portal.azure.com/).

Também pode aceder diretamente ao painel ao navegar até [https://portal.azure.com/ {tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) onde **{inquilino}** deve ser substituído pelo nome utilizado no momento de criação do inquilino (por exemplo, contosob2c). Pode marcar esta ligação para utilização futura.

    > [AZURE.IMPORTANT]
    Precisa de ser um Administrador Global de inquilino do B2C para poder aceder ao painel de funcionalidades do B2C. Um Administrador Global de qualquer outro inquilino ou um utilizador de qualquer outro inquilino não poderá aceder.

### 2. Aceda ao Portal Clássico do Azure

Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/) como Administrador da Subscrição. (Esta é a mesma conta profissional ou de estudante ou a mesma conta Microsoft que utilizou para inscrever-se no Azure). Navegue para a extensão do Active Directory à esquerda e clique no inquilino do B2C. No separador **Início Rápido** (o primeiro separador a abrir), clique em **Gerir definições do B2C** em **Administrar**. Esta ação irá abrir o painel de funcionalidades do B2C numa nova janela do browser ou separador.

Também pode encontrar a ligação **Gerir definições do B2C** na secção **Administração do B2C** no separador **Configurar**.

## Registar uma aplicação

1. No painel de funcionalidades do B2C no Portal do Azure, clique em **Aplicações**.
2. Clique em **+ Adicionar** na parte superior do painel.
3. Introduza um **Nome** para a aplicação que irá descrever a aplicação para os consumidores. Por exemplo, pode introduzir “Contoso B2C app”.
4. Se estiver a escrever uma aplicação baseada na web, alterne o botão **Incluir web app / web API** para **Sim**. As **URLs de resposta** são pontos finais para onde o Azure AD B2C devolverá qualquer tokens que a aplicação solicite. Por exemplo, introduza `https://localhost:44321/`. Se a aplicação inclui um componente do lado do servidor (API) que tem de ser protegido, irá querer criar (e copiar) um **Segredo de Aplicação** também clicando no botão **Gerar chave**.

    > [AZURE.NOTE]
O     **Segredo de Aplicação ** é uma credencial de segurança importante.

5. Se estiver a escrever uma aplicação móvel, ative/desative o botão **Incluir cliente nativo** para **Sim**. Copie o **URI de redirecionamento** predefinido que é criado automaticamente para si.
6. Clique em **Criar** para registar a aplicação.
7. Clique na aplicação que acabou de criar e copie a **ID de Aplicação de Cliente** exclusiva global que utilizará posteriormente no seu código.

## Criar uma Aplicação de Início Rápido

Agora que tem uma aplicação registada no Azure AD B2C, pode concluir um dos nossos tutoriais de início rápido para começar a trabalhar. Seguem-se algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]



<!--HONumber=Jun16_HO2-->


