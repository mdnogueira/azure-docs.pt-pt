---
title: "Tutorial: Integração do Azure Active Directory a área de trabalho por Facebook | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e à área de trabalho por Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 1590a66f215f0c093d24ff602c0ad951ba1e1eea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Integração do Azure Active Directory a área de trabalho por Facebook

Neste tutorial, irá aprender a integrar à área de trabalho por Facebook no Azure Active Directory (Azure AD).

Área de trabalho por Facebook a integração com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à área de trabalho por Facebook
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a área de trabalho por Facebook (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com área de trabalho, Facebook, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma área de trabalho por Facebook-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar à área de trabalho por Facebook na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar à área de trabalho por Facebook na galeria do
Para configurar a integração da área de trabalho por Facebook com o Azure AD, tem de adicionar à área de trabalho por Facebook na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar à área de trabalho por Facebook na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **à área de trabalho por Facebook**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. No painel de resultados, selecione **à área de trabalho por Facebook**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configurar e testar o Azure AD-início de sessão único a área de trabalho por Facebook com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na área de trabalho por Facebook é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na área de trabalho por Facebook tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** na área de trabalho por Facebook.

Para configurar e testar o Azure AD-início de sessão único a área de trabalho, Facebook, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar a frequência de reautenticação](#configuring-reauthentication-frequency)**  - para configurar a área de trabalho para pedir uma verificação SAML.
3. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
4. **[Criar uma área de trabalho por utilizador de teste do Facebook](#creating-a-workplace-by-facebook-test-user)**  - para ter um homólogo de Britta Simon na área de trabalho por Facebook que está ligada a representação do Azure AD do utilizador.
5. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
6. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, ativar o Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua área de trabalho por aplicação do Facebook.

**Para configurar o Azure AD-início de sessão único a área de trabalho, Facebook, execute os seguintes passos:**

1. No portal do Azure, no **à área de trabalho por Facebook** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. No **à área de trabalho ao domínio do Facebook e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<instancename>.facebook.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://www.facebook.com/company/<instancename>`

    > [!NOTE] 
    > Estes valores não estiverem a real. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [à área de trabalho pela equipa de suporte de cliente do Facebook](https://workplace.fb.com/faq/) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_400.png)

6. No **à área de trabalho pela configuração do Facebook** secção, clique em **configurar área de trabalho por Facebook** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-workplacebyfacebook-tutorial/config.png) 

7. Numa janela do browser web diferente, início de sessão à área de trabalho por site de empresa do Facebook como administrador.
  
   > [!NOTE] 
   > Como parte do processo de autenticação SAML, à área de trabalho pode utilizar cadeias de consulta de até 2,5 quilobytes de tamanho para poder passar os parâmetros para o Azure AD.

8. No **Dashboard da empresa**, vá para o **autenticação** separador.

9. Em **autenticação SAML**, selecione **SSO apenas** na lista pendente.

10. Os valores copiados a partir de entrada **à área de trabalho pela configuração do Facebook** secção do portal do Azure para os campos correspondentes:

    *   No **SAML URL** caixa de texto, cole o valor de **URL Single Sign-On serviço**, que copiou do portal do Azure.
    *   No **caixa de texto do URL do emissor SAML**, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.
    *   No **redirecionamento de fim de sessão SAML** (opcional), cole o valor de **Sign-Out URL**, que copiou do portal do Azure.
    *   Abra o **certificado com codificação base 64** no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para sua área de transferência e, em seguida, cole-os para o **SAML certificado** caixa de texto.

11. Poderá ter de introduzir o URL público-alvo, o URL de destinatário, e o URL de ACS (serviço de consumidor asserção) listados no **configuração SAML** secção.

12. Desloque-se na parte inferior da secção e clique em de **teste SSO** botão. Isto resulta numa janela de pop-up apresentação com a página de início de sessão do Azure AD apresentados. Introduza as suas credenciais como normal para se autenticar. 

    **Resolução de problemas:** Certifique-se de que está a ser devolvido back do Azure AD é o mesmo que a conta de área de trabalho que é registados com o endereço de e-mail.

13. Depois do teste foi concluído com êxito, desloque-se na parte inferior da página e clique em de **guardar** botão.

14. Todos os utilizadores usem à área de trabalho serão agora apresentados com a página de início de sessão do Azure AD para autenticação.

15. **Redirecionamento de fim de sessão de SAML (opcional)** - 

    Pode optar por configurar opcionalmente um Url de fim de sessão SAML, que podem ser utilizadas para apontar para a página de fim de sessão do Azure AD. Quando esta definição está ativada e configurada, o utilizador já não será direcionado para a página de fim de sessão à área de trabalho. Em vez disso, o utilizador será redirecionado para o url que foi adicionado na definição de redirecionamento de fim de sessão SAML.


> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="configuring-reauthentication-frequency"></a>Configurar a frequência de reautenticação rápida

Pode configurar a área de trabalho para pedir uma verificação SAML cada dia, três dias, semanas, duas semanas, meses ou nunca.

> [!NOTE] 
>O valor mínimo para a verificação SAML em aplicações móveis está definido para uma semana.

Pode também forçar um SAML repor para todos os utilizadores com o botão: exigir autenticação SAML para todos os utilizadores agora.


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Criar uma área de trabalho por utilizador de teste do Facebook

Nesta secção, um utilizador chamado Britta Simon é criado na área de trabalho por Facebook. Área de trabalho por Facebook suporta o aprovisionamento de just-in-time, que está ativada por predefinição.

Não há nenhuma ação por si nesta secção. Se um utilizador não existe na área de trabalho, Facebook, uma nova é criada quando tentar aceder à área de trabalho por Facebook.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [à área de trabalho pela equipa de suporte de cliente do Facebook](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à área de trabalho por Facebook.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon à área de trabalho, Facebook, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **à área de trabalho por Facebook**.

    ![Configurar o início de sessão único](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Se pretender testar as definições de início de sessão único, abra o painel de acesso.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o aprovisionamento de utilizadores](active-directory-saas-workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_203.png

