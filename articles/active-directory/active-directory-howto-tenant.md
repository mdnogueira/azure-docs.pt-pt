<properties
    pageTitle="Como obter um inquilino do Azure AD | Microsoft Azure"
    description="Como obter um inquilino do Azure Active Directory para registar e criar aplicações."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# Como obter um inquilino do Azure Active Directory

No Azure Active Directory (Azure AD), uma organização é representada por um [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant).  Quando assina um serviço em nuvem da Microsoft, tal como o Azure, o Microsoft Intune ou o Office 365, a organização recebe e fica proprietária de uma instância dedicada do serviço Azure AD.  Cada inquilino do Azure AD é distinto e separado dos outros inquilinos do Azure AD.  

Um inquilino aloja os utilizadores duma empresa e respetivas informações: palavras-passe, dados de perfil de utilizador, permissões, etc.  Também contém grupos, aplicações e outras informações relativas a uma organização e à sua segurança.

Para permitir que os utilizadores do Azure AD iniciem sessão na aplicação, tem de registá-la no seu próprio inquilino.  A publicação de uma aplicação num inquilino do Azure AD é **absolutamente gratuita**.  Na verdade, a maior parte dos programadores cria vários inquilinos e aplicações com o objetivo de experimentar, desenvolver e testar.  As organizações que subscrevem e consomem a aplicação podem optar por adquirir licenças se desejarem tirar partido das funcionalidades avançadas do diretório.

Por isso, como deve proceder para obter um inquilino do Azure AD?  O processo poderá ser um pouco diferente se:

- [Tiver já uma subscrição do Office 365](#use-an-existing-office-365-subscription)
- [Tiver já uma subscrição do Azure associada a uma conta Microsoft](#use-an-msa-azure-subscription)
- [Tiver já uma subscrição do Azure associada a uma conta da organização](#use-an-organizational-azure-subscription)
- [Não tiver nada do que está acima e pretender começar do zero](#start-from-scratch)

## Utilizar uma subscrição do Office 365 existente
Se tiver uma subscrição do Office 365, mas não tiver uma subscrição do Azure (e não for possível iniciar sessão no [Portal de Gestão do Azure](https://manage.windowsazure.com)), siga [estas instruções](https://technet.microsoft.com/library/dn832618.aspx) para ter acesso ao inquilino do Azure AD.

## Utilizar uma subscrição do Azure MSA
Se já tiver uma subscrição do Azure com a sua conta individual da Microsoft, já possui um inquilino!  No [Portal de Gestão do Azure](https://manage.windowsazure.com), encontrará um inquilino com o nome “Inquilino Predefinido” listado em “Todos os Itens” e “Active Directory”.  Pode utilizar este inquilino como julgar conveniente, mas poderá querer criar uma conta de administrador organizacional.

Para tal, siga estes passos.  Em alternativa, poderá pretender criar um novo inquilino e criar um administrador nesse inquilino seguindo um processo semelhante.

1.  Inicie sessão no [Portal de Gestão do Azure](https://manage.windowsazure.com) com a sua conta individual
2.  Navegue para a secção “Active Directory” do portal (que se encontra na barra de navegação esquerda)
3.  Selecione a entrada “Diretório Predefinido” na lista de diretórios disponíveis
4.  Clique na ligação Utilizadores na parte superior da página.  Verá um único utilizador na lista com o valor “Conta Microsoft” na coluna Origem
5.  Clique em “Adicionar Utilizador” na parte inferior da página
6.  No formulário Adicionar Utilizador indique a seguinte informação:
    - Tipo de Utilizador: novo utilizador na organização
    - Nome de Utilizador: (escolher um nome de utilizador para este administrador)
    - Nome/Apelido/Nome a Apresentar: (escolher os valores adequados)
    - Função: Administrador Global
    - Endereço de e-mail alternativo: (introduzir os valores adequados)
    - Opcional: ative a Multi-Factor Authentication
    - Por último, clique no botão verde “CRIAR” para finalizar a criação do utilizador (e apresentar a palavra-passe temporária).
7.  Após preencher o formulário Adicionar Utilizador e receber a palavra-passe temporária do novo utilizador administrativo, certifique-se de tomar nota desta palavra-passe pois terá de iniciar sessão com este novo utilizador para alterar a palavra-passe. Também pode enviar a palavra-passe diretamente para o utilizador, utilizando um e-mail alternativo.
8.  Para alterar a palavra-passe temporária, inicie sessão em https://login.microsoftonline.com com esta nova conta de utilizador e altere a palavra-passe quando solicitado.


## Utilizar uma subscrição organizacional do Azure
Se já tiver uma subscrição do Azure com a sua conta organizacional, já possui um inquilino!  No [Portal de Gestão do Azure](https://manage.windowsazure.com), encontrará um inquilino listado em “Todos os Itens” e “Active Directory”.  Pode utilizar este inquilino como julgar conveniente.  Também pode pretender criar um novo inquilino com o botão “Novo”, no canto inferior esquerdo do portal.


## Começar do zero
Se tudo o que estiver acima não fizer qualquer sentido, não se preocupe.  Visite simplesmente [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para subscrever o Azure com uma nova organização.  Depois de concluir o processo, terá o seu próprio inquilino no Azure AD com o nome de domínio que escolheu durante a subscrição.  No [Portal de Gestão do Azure](https://manage.windowsazure.com), pode encontrar o seu inquilino ao navegar para “Active Directory” na barra de navegação esquerda.

Como parte do processo de subscrição do Azure, ser-lhe-á pedido para fornecer os detalhes do cartão de crédito.  Pode continuar com confiança, não lhe será cobrado nada por publicar aplicações no Azure AD ou por criar novos inquilinos.



<!--HONumber=Jun16_HO2-->


