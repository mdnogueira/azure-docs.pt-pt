<properties
    pageTitle="Configurar o Azure Active Directory para gestão personalizada de acesso à aplicação|Microsoft Azure"
    description="A gestão de grupo personalizada permite aos utilizadores criarem e gerirem grupos do Office 365 no Azure Active Directory ou grupos de segurança e oferece aos utilizadores a possibilidade de solicitar filiação num grupo de segurança ou em grupos do Office 365"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# Configurar o Azure Active Directory para gestão de grupo personalizada

A gestão de grupo personalizada permite aos utilizadores criar e gerir grupos de segurança ou grupos do Office 365 no Azure Active Directory (Azure AD). Os utilizadores também podem pedir um grupo de segurança ou as associações de grupos do Office 365 e, em seguida, o proprietário do grupo pode aceitar ou recusar a associação. Deste modo, o controlo diário da filiação no grupo pode ser delegado a quem entenda o contexto empresarial dessa filiação. As funcionalidades de gestão de grupo personalizada estão disponíveis apenas para grupos de segurança e grupos do Office 365, mas não para grupos de segurança com capacidade de e-mail correio ou listas de distribuição.

A gestão de grupo personalizada inclui atualmente dois cenários essenciais: gestão de grupo delegada e gestão de grupo personalizada.

- **Gestão de grupo delegada**
   Um exemplo é um administrador que está a gerir o acesso a uma aplicação SaaS que a sua empresa está a utilizar. A gestão destes direitos de acesso está a tornar-se complexa, por isso este administrador pede ao proprietário da empresa que crie um novo grupo. O administrador atribui acesso à aplicação para o novo grupo e adiciona ao grupo todas as pessoas que estão a aceder à aplicação. O proprietário da empresa pode então adicionar mais utilizadores e estes são aprovisionados automaticamente para a aplicação. O proprietário da empresa não tem de aguardar que o administrador faça a gestão do acesso dos utilizadores. Se o administrador conceder a mesma permissão a um gestor num grupo empresarial diferente, essa pessoa também pode gerir o acesso para os respetivos utilizadores. O proprietário empresarial e o gestor não podem ver ou gerir os utilizadores dos outros. O administrador ainda pode ver todos os utilizadores que têm acesso à aplicação e bloquear os direitos de acesso, se for necessário.

- **Gestão de grupo personalizada**
   Um exemplo deste cenário são dois utilizadores com sites do SharePoint Online que configuraram independentemente. Pretendem conceder acesso às equipas uns dos outros com os respetivos sites. Para o conseguirem, podem criar um grupo no Azure AD e no SharePoint Online cada um deles seleciona esse grupo para fornecer acesso aos respetivos sites. Quando alguém quiser ter acesso, pode solicitá-lo no Painel de Acesso e, após a aprovação, obtém automaticamente acesso a ambos os sites do SharePoint Online. Posteriormente, um deles decide que todas as pessoas que acedem ao site devem também ter acesso a uma determinada aplicação SaaS. O administrador da aplicação SaaS pode adicionar direitos de acesso da aplicação ao site do SharePoint Online. A partir daí, quaisquer pedidos que ele aprove concedem acesso aos dois sites SharePoint Online e também a esta aplicação SaaS.

## Disponibilizar um grupo para personalização por utilizadores finais

1. No [portal clássico do Azure](https://manage.windowsazure.com), abra o diretório do Azure AD.

2. No separador **Configurar**, defina **Gestão de grupo delegada** como Ativado.

3. Defina **Os utilizadores podem criar grupos de segurança** ou **Os utilizadores podem criar grupos do Office** como Ativado.

Se **Utilizadores podem criar grupos de segurança** estiver ativada, todos os utilizadores no seu diretório estão autorizados a criar novos grupos de segurança e a adicionar membros a estes grupos. Estes novos grupos apareceriam também no Painel de Acesso para todos os outros utilizadores. Se a definição de política do grupo o permitir, outros utilizadores podem criar pedidos de adesão para estes grupos. Se **Os utilizadores podem criar grupos de segurança** estiver desativado, os utilizadores não podem criar grupos e não é possível alterar os grupos existentes em relação aos quais são proprietários. No entanto, podem continuar gerir os membros desses grupos e aprovar pedidos de outros utilizadores para pertencer aos respetivos grupos.

Pode também utilizar **Os utilizadores podem utilizar Personalização em grupos de segurança** para conseguir um controlo de acesso mais refinado sobre a gestão de grupo personalizada para os seus utilizadores. Se **Utilizadores podem criar grupos** estiver ativada, todos os utilizadores no seu diretório estão autorizados a criar novos grupos e a adicionar membros a estes grupos. Se definir também **Utilizadores que podem utilizar Personalização em grupos de segurança** como Alguns, estará a restringir a gestão de grupo a um grupo limitado de utilizadores. Quando este parâmetro estiver definido como Algumas, tem de adicionar utilizadores ao grupo de SSGMSecurityGroupsUsers antes de poderem criar novos grupos e adicionar membros aos mesmos. Se definir **Utilizadores que podem utilizar Personalização em grupos de segurança** como Todos, estará a habilitar todos os utilizadores no seu diretório para criarem novos grupos.

Também pode utilizar a caixa **Grupo que pode utilizar personalização para grupos de segurança** para especificar um nome personalizado para um grupo cujos membros podem utilizar a gestão personalizada.

## Informações adicionais

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Cmdlets do Azure Active Directory para configurar definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

* [O que é o Azure Active Directory?](active-directory-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Sep16_HO3-->


