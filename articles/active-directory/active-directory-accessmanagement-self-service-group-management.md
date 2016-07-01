<properties
    pageTitle="Configurar o Azure Active Directory para gestão personalizada de acesso à aplicação|Microsoft Azure"
    description="A gestão de grupo personalizada permite aos utilizadores criarem e gerirem grupos do Office 365 no Azure Active Directory ou grupos de segurança e oferece aos utilizadores a possibilidade de solicitar filiação num grupo de segurança ou em grupos do Office 365"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="stevenpo"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="curtand"/>

# Configurar o Azure Active Directory para gestão de grupo personalizada

A gestão de grupo personalizada permite aos utilizadores criarem e gerirem grupos de segurança ou grupos do Office 365 no Azure Active Directory (Azure AD) e oferece aos utilizadores a possibilidade de solicitar filiação num grupo de segurança ou em grupos do Office 365, pedidos esses que podem subsequentemente ser aprovados ou recusados pelo dono do grupo. Ao utilizar funcionalidades de gestão de grupo personalizada, o controlo diário da filiação no grupo pode ser delegado a quem entenda o contexto empresarial dessa filiação. Funcionalidades de gestão de grupo personalizada estão disponíveis apenas para grupos de segurança e d grupos do Office 365, não para grupos de segurança com capacidade de correio ou listas de distribuição.

A gestão de grupo personalizada inclui atualmente dois cenários essenciais: gestão de grupo delegada e gestão de grupo personalizada.

- **Gestão de grupo delegada** – um exemplo é um administrador que está a gerir o acesso a uma aplicação SaaS que a sua empresa está a utilizar. A gestão destes direitos de acesso está a tornar-se complexa, por isso este administrador pede ao proprietário da empresa que crie um novo grupo. O administrador atribui então o acesso à aplicação a um novo grupo que o proprietário da empresa acabou de criar e coloca nesse grupo todas as pessoas que têm atualmente acesso à aplicação. O proprietário da empresa pode então adicionar mais utilizadores e estes são automaticamente aprovisionados para aplicação momentos depois. O proprietário da empresa não tem de aguardar que o administrador faça o trabalho, pode gerir ele próprio o acesso para os seus utilizadores. O administrador pode fazer a mesma coisa por um assistente administrativo para um grupo empresarial diferente e o proprietário da empresa e este assistente administrativo já podem gerir o acesso dos seus utilizadores – sem poderem tocar ou até ver os utilizadores um do outro. O administrador ainda pode ver todos os utilizadores que têm acesso à aplicação e bloquear os direitos de acesso, se for necessário.

- **Gestão de grupo personalizada** – um exemplo deste cenário é dois utilizadores ambos com sites do SharePoint Online que configuraram independentemente um do outro, mas que vão querer dar acesso ao site um do outro às suas equipas. Para o conseguirem, podem criar um grupo no Azure AD e no SharePoint Online cada um deles escolhe esse mesmo grupo para fornecer acesso aos respetivos sites. Quando alguém quiser ter acesso, pode solicitá-lo no Painel de Acesso e, após a aprovação, obtém automaticamente acesso a ambos os sites do SharePoint Online. Posteriormente, um deles decide que todas as pessoas que acedem ao seu site devem também ter acesso a uma determinada aplicação SaaS. Pede ao administrador desta aplicação SaaS que adicione ao seu site direitos de acesso a esta aplicação. A partir daí, quaisquer pedidos que ele aprove darão acesso aos dois sites SharePoint Online e também a esta aplicação SaaS.

## Disponibilizar um grupo para personalização por utilizadores finais

No [Portal Clássico do Azure](https://manage.windowsazure.com), no separador **Configurar**, defina **Gestão de grupo delegada** para Ativado e, em seguida, defina **Utilizadores podem criar grupos de segurança** ou **Utilizadores podem criar grupos do Office** para Ativado.

Se **Utilizadores podem criar grupos de segurança** estiver ativada, todos os utilizadores no seu diretório estão autorizados a criar novos grupos de segurança e a adicionar membros a estes grupos. Estes novos grupos aparecerão também no Painel de Acesso para todos os outros utilizadores e outros utilizadores podem criar pedidos de adesão a estes grupos, se a definição da política do grupo o permitir. Se **Utilizadores podem criar grupos de segurança** estiver desativado, utilizadores não podem criar grupos nem alterar os grupos existentes de que são proprietários, mas podem ainda gerir a filiação nesses grupos e aprovar pedidos de outros utilizadores se associarem aos respetivos grupos.

Pode também utilizar **Utilizadores que podem utilizar Personalização em grupos de segurança** para conseguir um controlo de acesso mais refinado sobre as a gestão de grupo personalizada para os seus utilizadores. Se **Utilizadores podem criar grupos** estiver ativada, todos os utilizadores no seu diretório estão autorizados a criar novos grupos e a adicionar membros a estes grupos. Se definir também **Utilizadores que podem utilizar Personalização em grupos de segurança** como Alguns, estará a restringir a gestão de grupo a um grupo limitado de utilizadores. Quando este comutador está definido como Alguns, é criado um grupo denominado SSGMSecurityGroupsUsers no seu diretório e apenas os utilizadores que tornou os membros deste grupo podem então criar novos grupos de segurança e adicionar membros a estes grupos no seu diretório. Se definir **Utilizadores que podem utilizar Personalização em grupos de segurança** como Todos, estará a habilitar todos os utilizadores no seu diretório para criarem novos grupos.

Também pode utilizar caixa **Grupo que pode utilizar personalização para grupos de segurança** (definida por predefinição, como 'SSGMSecurityGroupsUsers' para especificar o seu próprio nome personalizado para um grupo que incluirá todos os utilizadores com a capacidade para utilizar a Personalização para criar novos grupos no seu diretório.

## Informações adicionais

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

* [O que é o Azure Active Directory?](active-directory-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Jun16_HO2-->


