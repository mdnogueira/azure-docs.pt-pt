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
    ms.date="05/26/2016"
    ms.author="curtand"/>


# Gerir grupos no Azure Active Directory

Uma das funcionalidades da gestão de utilizadores do Azure Active Directory (Azure AD) é a capacidade de criar grupos de utilizadores. Pode depois utilizar um grupo para atribuir licenças a uma classe de utilizadores. Pode também utilizar grupos para atribuir permissão de acesso a

- Recursos como objetos no diretório
- Recursos externos ao diretório, como aplicações SaaS, serviços do Azure, sites SharePoint ou recursos no local.

Além disso, um proprietário de recursos pode atribuir acesso a um recurso a um grupo do Azure AD. Esta ação concede aos membros desse grupo acesso ao recurso. O proprietário do grupo gere, então, a filiação no grupo. Efetivamente, o proprietário do recurso delega no proprietário do grupo a permissão de atribuir utilizadores ao seu recurso.

## Como crio um grupo?

Esta tarefa pode ser concluída com o portal de contas do Office 365, o portal de contas do Windows Intune ou o Portal Clássico do Azure, dependendo dos serviços que a sua organização tiver subscrito. Para obter mais informações sobre como utilizar portais não do Azure para gerir o seu Azure Active Directory, consulte o artigo [Administrar o seu diretório do Azure AD](active-directory-administer.md).

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos**.

3. Selecione **Adicionar Grupo**.

4. Na janela **Adicionar Grupo**, especifique o nome e a descrição de um grupo.


## Como é que adiciono ou removo utilizadores individuais num grupo de segurança?

**Para adicionar um utilizador individual a um grupo**

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos**.

3. Abra o grupo a que pretende adicionar membros. Por predefinição, esta ação apresenta o separador **Membros** do grupo selecionado.

4. Selecione **Adicionar membros**.

5. Na página **Adicionar Membros**, selecione o nome do utilizador ou um grupo que pretende adicionar como membro deste grupo e certificar-se de que este nome é adicionado ao painel **Selecionados**.


**Para remover um utilizador individual de um grupo**

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos**.

3. Abra o grupo de onde pretende remover membros.

4. Selecione o separador**Membros**, selecione o nome do membro que pretende remover deste grupo e clique em **Remover**.

6. Confirme na linha de comandos que pretende remover este membro do grupo.


## Como posso gerir dinamicamente a filiação de um grupo?

No Azure AD, pode configurar muito facilmente uma regra simples (uma regra que permite apenas uma única comparação ) para determinar que utilizadores serão membros do grupo. Por exemplo, se um grupo é atribuído a uma aplicação SaaS e configurar uma regra para adicionar utilizadores que tenham um cargo de “Vendedor”, todos os utilizadores no seu diretório do Azure AD com esse cargo terão acesso a esta aplicação SaaS.

> [AZURE.NOTE] Pode configurar uma regra de filiação dinâmica em grupos de segurança ou grupos do Office 365. Filiações aninhadas em grupos não são suportadas atualmente para uma atribuição a aplicações baseada em grupos.
>
> A filiação dinâmica para grupos exige uma licença do Azure AD Premium a ser atribuída
>
> - Ao administrador que gere a regra num grupo
> - A todos os utilizadores que estão selecionados pela regra para serem membros do grupo

**Para ativar a filiação dinâmica num grupo**

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.

2. Selecione o separador **Grupos** e abra o grupo que pretende editar.

3. Selecione o separador **Configurar** e defina **Ativar Filiação Dinâmica** para **Sim**.

4. Configure uma única regra simples para o grupo, que controlará o modo como a filiação dinâmica funciona para este grupo. Certifique-se de que a opção **Adicionar utilizadores onde** está selecionada e, em seguida, selecione na lista uma propriedade de utilizador (por exemplo, departamento, jobTitle, etc.),

5. Em seguida, selecione uma condição (Não é Igual a, É Igual a, Não Começa Por, Começa Por, Não Contém, Contém, Não Corresponde a, Corresponde a) e, finalmente, especifique um valor para a propriedade de utilizador selecionada.

Para saber mais sobre como criar regras *avançadas* (regras que podem conter várias comparações) para filiação dinâmica em grupos, consulte o artigo [Utilizar atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Informações adicionais

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

* [O que é o Azure Active Directory?](active-directory-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Jun16_HO2-->


