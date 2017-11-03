---
title: "Tutorial: Integração do Azure Active Directory a área de trabalho por Facebook | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e à área de trabalho por Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 27e62a00832484667117d8718db9f2fc05e2f4e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Integração do Azure Active Directory a área de trabalho por Facebook

Neste tutorial, irá aprender a integrar à área de trabalho por Facebook no Azure Active Directory (Azure AD).

Área de trabalho por Facebook a integração com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à área de trabalho por Facebook.
- Pode permitir aos utilizadores obter iniciada automaticamente à área de trabalho por Facebook (-início de sessão único) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central: portal do Azure.

Para obter mais detalhes sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com área de trabalho, Facebook, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma área de trabalho por Facebook-início de sessão único (SSO) ativada subscrição

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testar o SSO do Azure AD num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicione à área de trabalho por Facebook partir da galeria.
2. Configurar e testar o Azure AD-início de sessão único.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Adicionar à área de trabalho por Facebook na galeria do
Para configurar a integração da área de trabalho por Facebook com o Azure AD, adicione à área de trabalho por Facebook na Galeria à sua lista de aplicações SaaS geridas.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **do Azure Active Directory**. 

    ![O botão do Azure Active Directory][1]

2. Navegue até à **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, selecione **nova aplicação** no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **à área de trabalho por Facebook**e selecione **à área de trabalho por Facebook** de resultados. Em seguida, selecione **adicionar**, para adicionar a aplicação.

    ![Área de trabalho por Facebook na lista de resultados](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configurar e testar o SSO do Azure AD a área de trabalho, Facebook, com base num utilizador de teste chamado "Britta Simon."

Para SSO funcionar, do Azure AD tem de saber o que o utilizador homólogo na área de trabalho por Facebook é um utilizador no Azure AD. Por outras palavras, uma relação entre um utilizador do Azure AD e o utilizador relacionado na área de trabalho por Facebook ligada deve ser estabelecida.

Estabelecer esta relação ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** na área de trabalho por Facebook.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, ativar o SSO do Azure AD no portal do Azure e configurar o SSO no seu local de trabalho por aplicação do Facebook.

1. No portal do Azure, no **à área de trabalho por Facebook** página de integração de aplicações, selecione **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para permitir SSO.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. No **à área de trabalho ao domínio do Facebook e URLs** secção, efetue o seguinte:

    a. No **URL de início de sessão** caixa de texto, escreva um URL que utiliza o padrão do seguinte:`https://<company subdomain>.facebook.com`

    b. No **identificador** caixa de texto, escreva um URL que utiliza o padrão do seguinte:`https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > Estes valores são apenas um exemplo. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte o [à área de trabalho pela equipa de suporte de cliente do Facebook](https://workplace.fb.com/faq/) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, selecione **certificado (Base64)**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Selecione **Guardar**.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. No **à área de trabalho pela configuração do Facebook** secção, selecione **configurar área de trabalho por Facebook** para abrir o **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **referência rápida** secção.

    ![Área de trabalho pela configuração do Facebook](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. Numa janela do browser web diferente, inicie sessão à área de trabalho por site de empresa do Facebook como administrador.
  
   > [!NOTE] 
   > Como parte do processo de autenticação SAML, à área de trabalho pode utilizar cadeias de consulta de até 2,5 quilobytes tamanho para poder passar os parâmetros para o Azure AD.

8. No **Dashboard da empresa**, vá para o **autenticação** separador.

9. Em **autenticação SAML**, selecione **SSO apenas** na lista pendente.

10. Introduza os valores copiados a partir de **à área de trabalho pela configuração do Facebook** secção do portal do Azure para os campos correspondentes:

    *   No **SAML URL** texto caixa, cole o valor de **URL Single Sign-On serviço**, que copiou do portal do Azure.
    *   No **URL do emissor SAML** texto caixa, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.
    *   No **redirecionamento de fim de sessão de SAML (opcional)**, cole o valor de **Sign-Out URL**, que copiou do portal do Azure.
    *   Abra o **certificado com codificação base 64** no bloco de notas, transferido a partir do portal do Azure. Copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **SAML certificado** caixa de texto.

11. Poderá ter de introduzir o URL do público-alvo, destinatário URL e o URL de ACS (serviço de consumidor asserção), listados no **configuração SAML** secção.

12. Desloque-se na parte inferior da secção e selecione **teste SSO**. É apresentada uma janela de pop-up, com a página de início de sessão do Azure AD. Para autenticar, introduza as suas credenciais como normal. Certifique-se de que o endereço de e-mail devolvido atrás do Azure AD é o mesmo que a conta de área de trabalho que tem sessão iniciada com.

13. Se o teste foi concluída com êxito, desloque-se na parte inferior da página e selecione **guardar**.

14. Qualquer pessoa que utilize a área de trabalho é agora apresentada página sessão do Azure AD para autenticação.

Pode optar por configurar um sessão SAML URL, que pode ser utilizada para apontar para a página de início de sessão do Azure AD. Quando esta definição está ativada e configurada, o utilizador já não é direcionado para a página de início de sessão à área de trabalho. Em vez disso, o utilizador é redirecionado para o URL que foi adicionado na definição de redirecionamento de fim de sessão de SAML.


> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação. Depois de adicionar esta aplicação a partir do **do Active Directory** > **aplicações empresariais** secção, basta selecionar a **Single Sign-On** separador e a documentação incorporada através de acesso a **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporado no [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="configure-reauthentication-frequency"></a>Configurar a frequência de reautenticação rápida

Pode configurar a área de trabalho para pedir uma verificação SAML diariamente, três dias, uma semana, duas semanas, um mês, ou nunca.

> [!NOTE] 
>O valor mínimo para a verificação SAML em aplicações móveis está definido para uma semana.

Pode também forçar um SAML repor para todos os utilizadores. Para tal, utilize o **exigir autenticação SAML para todos os utilizadores agora** botão.


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

1. No **portal do Azure**, no painel esquerdo, selecione **do Azure Active Directory**.

    ![O botão do Azure Active Directory](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e selecione **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar**.
 
    ![O botão de adição](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, efetue o seguinte:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, escreva o **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe**e anotá-lo.

    d. Selecione **Criar**.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Criar uma área de trabalho por utilizador de teste do Facebook

Nesta secção, um utilizador chamado Britta Simon é criado na área de trabalho por Facebook. Área de trabalho por Facebook suporta o aprovisionamento de just-in-time, que está ativada por predefinição.

Não há nenhuma ação por si nesta secção. Se um utilizador não existe na área de trabalho, Facebook, uma nova é criada quando tentar aceder à área de trabalho por Facebook.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte o [à área de trabalho pela equipa de suporte de cliente do Facebook](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o SSO do Azure ao conceder acesso à área de trabalho por Facebook.

![Atribua o utilizador][200] 

1. No portal do Azure, abra a vista de aplicações. Ir para a vista de diretório, aceda a **aplicações empresariais**e, em seguida, selecione **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **à área de trabalho por Facebook**.

    ![Área de trabalho por Facebook ligação na lista de aplicações](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Selecione **Adicionar**. Em seguida, no **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. No **utilizadores e grupos** caixa de diálogo, selecione **selecione**.

7. No **adicionar atribuição** caixa de diálogo, selecione **atribuir**.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Se pretender testar as definições de SSO, abra o painel de acesso.
Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Passos seguintes

* Consulte o [lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md).
* Leitura [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).
* Saiba mais sobre como [configurar o aprovisionamento de utilizadores](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png

