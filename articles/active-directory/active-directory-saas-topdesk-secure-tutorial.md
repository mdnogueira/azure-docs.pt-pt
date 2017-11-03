---
title: "Tutorial: Integração do Azure Active Directory com TOPdesk - segura | Microsoft Docs"
description: "Saiba como utilizar TOPdesk - seguro com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 28f0542dbe87bb34c83a7852db7c3a9fef055ce9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Azure Active Directory com TOPdesk - segura
O objetivo deste tutorial é mostrar a integração do Azure e TOPdesk - segura.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Um TOPdesk - segura-início de sessão único ativada subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que tem atribuídos ao TOPdesk - segura será capazes de início de sessão único para a aplicação no seu TOPdesk - site da empresa seguros (serviço fornecedor iniciada pelo início de sessão) ou utilizando o [introdução para o acesso Painel](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

1. Ativar a integração de aplicações para TOPdesk - segura
2. Configurar o início de sessão único
3. Configurar o aprovisionamento de utilizadores
4. Atribuir utilizadores

![Cenário](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "cenário")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>Ativar a integração de aplicações para TOPdesk - segura
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações para TOPdesk - segura.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Para ativar a integração de aplicações para TOPdesk - proteger, execute os seguintes passos:
1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Do Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "do Active Directory")

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "aplicações")

4. Clique em **adicionar** na parte inferior da página.
   
    ![Adicionar aplicação](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Adicionar aplicação")

5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Adicionar uma aplicação de gallerry](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "adicionar uma aplicação de gallerry")

6. No **caixa pesquisa**, tipo **TOPdesk - segura**.
   
    ![Galeria de aplicações](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galeria de aplicações")

7. No painel de resultados, selecione **TOPdesk - segura**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![TOPdesk - segura](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - segura")

## <a name="configuring-single-sign-on"></a>Configurar o início de sessão único
O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar TOPdesk - seguro com a conta no Azure AD com Federação com base no protocolo SAML.  
Configuração-início de sessão único para TOPdesk - segura requer a carregar um ficheiro de ícone de logótipo. Para obter o ficheiro de ícone, contacte a equipa de suporte TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:
1. Inicie sessão no seu **TOPdesk - segura** site da empresa como administrador.
2. No **TOPdesk** menu, clique em **definições**.
   
    ![Definições](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "definições")

3. Clique em **definições de início de sessão**.
   
    ![Definições de início de sessão](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "definições de início de sessão")

4. Expanda o **definições de início de sessão** e, em seguida, clique em **geral**.
   
    ![Geral](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "geral")

5. No **seguro** secção o **início de sessão SAML** configuração secção, execute os seguintes passos:
   
    ![Definições técnicas](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "definições técnicas")
   
    a. Clique em **transferir** para transferir o ficheiro de metadados pública e, em seguida, guarde-o localmente no seu computador.
   
    b. Abra o ficheiro de metadados e, em seguida, localize o **AssertionConsumerService** nós.
    
    ![Serviço de consumidor asserção](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "asserção consumidor serviço")
   
    c. Copiar o **AssertionConsumerService** valor.  
      
    > [!NOTE]
    > Terá do valor a **configurar o URL da aplicação** secção mais tarde neste tutorial.
    > 
    > 

6. Numa janela do browser web diferente, inicie sessão no seu **portal clássico do Azure** como administrador.

7. No **TOPdesk - segura** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o * * configurar início de sessão único * * caixa de diálogo.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "configurar o início de sessão único")

8. No **como gostaria aos utilizadores iniciar sessão TOPdesk - segura** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "configurar o início de sessão único")

9. No **URL da aplicação configurar** página, execute os seguintes passos:
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "configurar o URL da aplicação")
   
    a. No **TOPdesk - início de sessão seguro no URL** caixa de texto, escreva o URL é utilizado pelos seus utilizadores para iniciar sessão na sua TOPdesk - aplicação segura (por ex.: "*https://qssolutions.topdesk.net*").
   
    b. No **TOPdesk – público de resposta de URL** caixa de texto, cole o **TOPdesk - URL de AssertionConsumerService Secure** (por ex.: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
   
    c. Clique em **Seguinte**.

10. No **configurar o início de sessão único em TOPdesk - segura** página, para transferir o ficheiro de metadados, clique em **transferir metadados**e, em seguida, guarde o ficheiro localmente no seu computador.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "configurar o início de sessão único")

11. Para criar um ficheiro de certificado, execute os seguintes passos:
    
    ![Certificado](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "certificado")
    
    a. Abra o ficheiro de metadados transferido.
    b. Expanda o **RoleDescriptor** nó que tenha um **xsi: type** de **sejam fornecidas: ApplicationServiceType**.
    c. Copie o valor do **certificado X509** nós.
    d. Guardar o copiados **certificado X509** valor localmente no seu computador num ficheiro.

12. No seu TOPdesk - Proteja site da empresa, o **TOPdesk** menu, clique em **definições**.
    
    ![Definições](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "definições")

13. Clique em **definições de início de sessão**.
    
    ![Definições de início de sessão](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "definições de início de sessão")

14. Expanda o **definições de início de sessão** e, em seguida, clique em **geral**.
    
    ![Geral](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "geral")

15. No **pública** secção, clique em **adicionar**.
    
    ![Adicionar](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "adicionar")

16. No **Assistente de configuração de SAML** diálogo página, execute os seguintes passos:
    
    ![Assistente de configuração de SAML](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Assistente de configuração de SAML")
    
    a. Para carregar o ficheiro de metadados transferido em **metadados da Federação**, clique em **procurar**.

    b. Para carregar o ficheiro de certificado em **certificados RSA ()**, clique em **procurar**.

    c. Carregar o ficheiro de logótipo obteve da equipa de suporte TOPdesk, em **ícone de logótipo**, clique em **procurar**.

    d. No **atributo de nome de utilizador** caixa de texto, tipo **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. No **nome a apresentar** caixa de texto, escreva um nome para a sua configuração.

    f. Clique em **Guardar**.

17. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "configurar o início de sessão único")

## <a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizadores
Para permitir que os utilizadores do Azure AD sessão TOPdesk - segura, estes têm de ser aprovisionados para TOPdesk - segura.  
No caso de TOPdesk - seguro, o aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:
1. Inicie sessão no seu **TOPdesk - segura** site da empresa como administrador.
2. No menu na parte superior, clique em **TOPdesk \> novo \> ficheiros de suporte \> operador**.
   
    ![Operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "operador")

3. No **operador New** caixa de diálogo, execute os seguintes passos:
   
    ![Operador New](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "operador New")
   
    a. Clique no separador Geral.
   
    b. No **Apelido** caixa de texto do **geral** secção, escreva o apelido de uma conta válida do Azure Active Directory que pretende aprovisionar.
   
    c. Selecione um **Site** para a conta no **localização** secção.
   
    d. No **nome de início de sessão** caixa de texto do **TOPdesk início de sessão** secção, escreva um nome de início de sessão para os seus utilizadores.
   
    e. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar qualquer outro TOPdesk - ferramentas de criação de conta de utilizador segura ou APIs fornecidas pelos TOPdesk - segura para aprovisionar contas de utilizador do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuir utilizadores
Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Para atribuir utilizadores a TOPdesk - proteger, execute os seguintes passos:
1. No portal clássico do Azure, crie uma conta de teste.
2. No * * TOPdesk - segura * * página de integração de aplicações, clique em **atribuir utilizadores**.
   
    ![Atribuir utilizadores](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "atribuir utilizadores")

3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
    ![Sim](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sim")

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

