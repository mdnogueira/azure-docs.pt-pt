---
title: "Tutorial: Integração do Azure Active Directory com Coupa | Microsoft Docs"
description: "Saiba como utilizar Coupa com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: c952975919cfd948f1b9ea93ff2ac2641a53f923
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integração do Azure Active Directory com Coupa
O objetivo deste tutorial é mostrar a integração do Azure e Coupa.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Um Coupa-início de sessão único (SSO) ativada subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que tem atribuídos ao Coupa será capazes de início de sessão único para a aplicação a utilizar o [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

* Ativar a integração de aplicações para Coupa
* Configurar o início de sessão único
* Configurar o aprovisionamento de utilizadores
* Atribuir utilizadores

![Cenário](./media/active-directory-saas-coupa-tutorial/IC791897.png "cenário")

## <a name="enable-the-application-integration-for-coupa"></a>Ativar a integração de aplicações para Coupa
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações para Coupa.

**Para ativar a integração de aplicações para Coupa, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
   ![Do Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "do Active Directory")
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
   ![Aplicações](./media/active-directory-saas-coupa-tutorial/IC700994.png "aplicações")
4. Clique em **adicionar** na parte inferior da página.
   
   ![Adicionar aplicação](./media/active-directory-saas-coupa-tutorial/IC749321.png "Adicionar aplicação")
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
   ![Adicionar uma aplicação de gallerry](./media/active-directory-saas-coupa-tutorial/IC749322.png "adicionar uma aplicação de gallerry")
6. No **caixa pesquisa**, tipo **Coupa**.
   
   ![Galeria de aplicações](./media/active-directory-saas-coupa-tutorial/IC791898.png "Galeria de aplicações")
7. No painel de resultados, selecione **Coupa**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
## <a name="configure-single-sign-on"></a>Configurar o início de sessão único

O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar Coupa à respetiva conta no Azure AD com Federação com base no protocolo SAML.  

Configurar o início de sessão para Coupa requer a obter um valor de thumbprint de um certificado. Se não estiver familiarizado com este procedimento, consulte [como obter o valor de thumbprint do certificado](http://youtu.be/YKQF266SAxI).

**Para configurar o início de sessão único, execute os seguintes passos:**

1. Inicie sessão site da sua empresa Coupa como administrador.
2. Aceda a **configuração \> controlo de segurança**.
   
   ![Controlos de segurança](./media/active-directory-saas-coupa-tutorial/IC791900.png "controlos de segurança")
3. Para transferir o ficheiro de metadados Coupa para o seu computador, clique em **transferir e importar metadados de SP**.
   
   ![Metadados Coupa SP](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadados")
4. Numa janela do browser diferente, inicie sessão portal clássico do Azure.
5. No **Coupa** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-coupa-tutorial/IC791902.png "configurar o início de sessão único")
6. No **como gostaria aos utilizadores iniciar sessão Coupa** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-coupa-tutorial/IC791903.png "configurar o início de sessão único")
7. No **URL da aplicação configurar** página, execute os seguintes passos:
   
   ![Configurar o URL da aplicação](./media/active-directory-saas-coupa-tutorial/IC791904.png "configurar o URL da aplicação")   
   1. No **URL de início de sessão** caixa de texto, URL de tipo utilizado pelos seus utilizadores para iniciar sessão sua aplicação Coupa (por ex.: "*http://company.Coupa.com*").
   2. Abra o ficheiro de metadados Coupa transferido e, em seguida, copie o **AssertionConsumerService índice/URL**.
   3. No **URL de resposta de Coupa** caixa de texto, cole o **AssertionConsumerService índice/URL** valor.
   4. Clique em **Seguinte**.
8. No **configurar o início de sessão único em Coupa** página, para transferir o ficheiro de metadados, clique em **transferir metadados**e, em seguida, guarde o ficheiro localmente no seu computador.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-coupa-tutorial/IC791905.png "configurar o início de sessão único")
9. No site de empresa Coupa, aceda a **configuração \> controlo de segurança**.
   
   ![Controlos de segurança](./media/active-directory-saas-coupa-tutorial/IC791900.png "controlos de segurança")
10. No **inicie sessão com credenciais de Coupa** secção, execute os seguintes passos:  

   ![Inicie sessão com credenciais de Coupa](./media/active-directory-saas-coupa-tutorial/IC791906.png "inicie sessão com credenciais de Coupa") 
   1. Selecione **iniciar sessão utilizando SAML**.
   2. Clique em **procurar** para carregar o ficheiro de metadados do Azure Active transferido.
   3. Clique em **Guardar**.
11. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
    
   ![Configurar o início de sessão único](./media/active-directory-saas-coupa-tutorial/IC791907.png "configurar o início de sessão único")
    
## <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores

Para permitir que os utilizadores do Azure AD sessão Coupa, têm de ser aprovisionados para Coupa.  

* No caso de Coupa, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Coupa** site da empresa como administrador.
2. No menu na parte superior, clique em **configuração**e, em seguida, clique em **utilizadores**.
   
   ![Os utilizadores](./media/active-directory-saas-coupa-tutorial/IC791908.png "utilizadores")
3. Clique em **Criar**.
   
   ![Criar utilizadores](./media/active-directory-saas-coupa-tutorial/IC791909.png "criar utilizadores")
4. No **criar utilizador** secção, execute os seguintes passos:
   
   ![Detalhes de utilizador](./media/active-directory-saas-coupa-tutorial/IC791910.png "detalhes de utilizador")
   
   1. Tipo de **início de sessão**, **nome próprio**, **Apelido**, **ID de início de sessão único**, **E-Mail** atributos de um conta válida do Azure Active Directory que pretende aprovisionar num relacionados caixas de texto.
   2. Clique em **Criar**.   
   >[!NOTE]
   >O marcador de posição de conta do Azure Active Directory irá receber um e-mail com uma ligação para confirmar a conta para ficar ativa. 
   > 

>[!NOTE]
>Pode utilizar quaisquer outras Coupa utilizador conta criação ferramentas ou APIs fornecidas pelo Coupa para aprovisionar contas de utilizador do AAD. 
> 

## <a name="assign-users"></a>Atribuir utilizadores
Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

**Para atribuir utilizadores a Coupa, execute os seguintes passos:**

1. No portal clássico do Azure, crie uma conta de teste.
2. No * * Coupa * * página de integração de aplicações, clique em **atribuir utilizadores**.
   
   ![Atribuir utilizadores](./media/active-directory-saas-coupa-tutorial/IC791911.png "atribuir utilizadores")
3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
   ![Sim](./media/active-directory-saas-coupa-tutorial/IC767830.png "Sim")

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

