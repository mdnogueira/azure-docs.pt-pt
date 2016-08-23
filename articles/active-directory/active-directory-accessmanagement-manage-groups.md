<properties
    pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
    description="How to create and manage groups to manage Azure users using Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/14/2016"
    ms.author="curtand"/>


# Gerir grupos no Azure Active Directory

Uma das funcionalidades da gestão de utilizadores do Azure Active Directory (Azure AD) é a capacidade de criar grupos de utilizadores. Pode utilizar um grupo para efetuar tarefas de gestão, tais como atribuir licenças ou permissões a vários utilizadores em simultâneo. Pode também utilizar grupos para atribuir permissão de acesso a

- Recursos como objetos no diretório
- Recursos externos ao diretório, como aplicações SaaS, serviços do Azure, sites SharePoint ou recursos no local

Além disso, um proprietário de recursos também pode atribuir acesso de um recurso a um grupo do Azure AD de outra pessoa. Esta atribuição concede aos membros desse grupo acesso ao recurso. O proprietário do grupo gere, então, a filiação no grupo. Efetivamente, o proprietário do recurso delega no proprietário do grupo a permissão de atribuir utilizadores ao seu recurso.

## Como crio um grupo?

Dependendo dos serviços para os quais a sua organização tiver subscrito, pode criar um grupo utilizando um dos seguintes procedimentos:
- Portal clássico do Azure
- portal de contas do Office 365
- portal de contas do Windows Intune

Vamos descrever as tarefas, tal como foram executadas no portal clássico do Azure. Para obter mais informações sobre como utilizar portais que não sejam do Azure para gerir o seu diretório do Azure AD,veja o artigo [Administrar o seu diretório do Azure AD](active-directory-administer.md).

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos**.

3. Selecione **Adicionar Grupo**.

4. Na janela **Adicionar Grupo**, especifique o nome e a descrição de um grupo.


## Como é que adiciono ou removo utilizadores individuais num grupo de segurança?

**Para adicionar um utilizador individual a um grupo**

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos**.

3. Abra o grupo a que pretende adicionar membros. Abra o separador **Membros** do grupo selecionado se ainda estiver apresentado.

4. Selecione **Adicionar membros**.

5. Na página **Adicionar Membros**, selecione o nome do utilizador ou de um grupo que pretende adicionar como membro deste grupo. Certifique-se de que este nome é adicionado ao painel **Selecionado**.


**Para remover um utilizador individual de um grupo**

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos**.

3. Abra o grupo de onde pretende remover membros.

4. Selecione o separador**Membros**, selecione o nome do membro que pretende remover deste grupo e clique em **Remover**.

6. Confirme na linha de comandos que pretende remover este membro do grupo.


## Como posso gerir dinamicamente a filiação de um grupo?

No Azure AD, pode configurar muito facilmente uma regra simples para determinar que utilizadores são membros do grupo. Uma regra simples é aquela que faz apenas uma única comparação. Por exemplo, se um grupo for atribuído a uma aplicação SaaS, pode configurar uma regra para adicionar utilizadores que tenham um cargo de "Representante de Vendas" Esta regra, em seguida, concede acesso a esta aplicação SaaS a todos os utilizadores com esse cargo no seu diretório.

> [AZURE.NOTE] Pode configurar uma regra de filiação dinâmica em grupos de segurança ou grupos do Office 365. As filiações aninhadas em grupos não são suportadas atualmente para uma atribuição baseada em grupos para aplicações .
>
> A filiação dinâmica para grupos exige uma licença do Azure AD Premium a ser atribuída
>
> - Ao administrador que gere a regra num grupo
> - Todos os membros do grupo

**Para ativar a filiação dinâmica num grupo**

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos** e abra o grupo que pretende editar.

3. Selecione o separador **Configurar** e defina **Ativar Filiação Dinâmica** para **Sim**.

4. Configure uma única regra simples para o grupo, que controla o modo como a filiação dinâmica funciona para este grupo. Certifique-se de que a opção **Adicionar utilizadores onde** está selecionada e, em seguida, selecione na lista uma propriedade de utilizador (por exemplo, departamento, jobTitle, etc.),

5. Em seguida, selecione uma condição (Não é Igual a, É Igual a, Não Começa Por, Começa Por, Não Contém, Contém, Não Corresponde a, Corresponde a).

6. Especifique um valor de comparação para a propriedade de utilizador selecionada.

Para saber mais sobre como criar regras *avançadas* (regras que podem conter várias comparações) para filiação dinâmica em grupos, consulte o artigo [Utilizar atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Informações adicionais

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Cmdlets do Azure Active Directory para configurar definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

* [O que é o Azure Active Directory?](active-directory-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Aug16_HO1-->


