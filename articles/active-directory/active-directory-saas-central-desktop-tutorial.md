---
title: "Tutorial: Integração do Azure Active Directory com o ambiente de trabalho Central | Microsoft Docs"
description: "Saiba como utilizar o ambiente de trabalho Central com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: fe32c1d68040ceb9d9de2ad6c4a6dc9ea93f5aef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Azure Active Directory com o ambiente de trabalho Central
O objetivo deste tutorial é mostrar a integração do Azure e o ambiente de trabalho Central. O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Um Central ambiente de trabalho início de sessão único ativado subscrição / inquilino do ambiente de trabalho Central

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

* Ativar a integração de aplicações de ambiente de trabalho Central
* Configurar início de sessão único (SSO)
* Configurar o aprovisionamento de utilizadores
* Atribuir utilizadores

![Cenário](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "cenário")

## <a name="enable-the-application-integration-for-central-desktop"></a>Ativar a integração de aplicações de ambiente de trabalho Central
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações de ambiente de trabalho Central.

**Para ativar a integração de aplicações de ambiente de trabalho Central, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
   ![Do Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "do Active Directory")
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
   ![Aplicações](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "aplicações")
4. Clique em **adicionar** na parte inferior da página.
   
   ![Adicionar aplicação](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Adicionar aplicação")
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
   ![Adicionar uma aplicação de gallerry](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "adicionar uma aplicação de gallerry")
6. No **caixa pesquisa**, tipo **ambiente de trabalho Central**.
   
   ![Galeria de aplicações](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galeria de aplicações")
7. No painel de resultados, selecione **ambiente de trabalho Central**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
   ![Ambiente de trabalho central](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "ambiente de trabalho Central")
   
## <a name="configure-single-sign-on"></a>Configurar o início de sessão único

O objetivo desta secção consiste em descrevem como permitir que os utilizadores autenticar para o ambiente de trabalho Central com a conta no Azure AD com Federação com base no protocolo SAML.

Como parte deste procedimento, é necessário para carregar um certificado codificado base-64 no seu inquilino de ambiente de trabalho Central.  
Se não estiver familiarizado com este procedimento, consulte [como converter um certificado de binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o início de sessão único, execute os seguintes passos:**

1. No portal clássico do Azure, no **ambiente de trabalho Central** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o * * configurar início de sessão único * * caixa de diálogo.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "configurar o início de sessão único")
2. No **como gostaria aos utilizadores iniciar sessão ambiente de trabalho Central** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "configurar o início de sessão único")
3. No **URL da aplicação configurar** página, execute os passos seguintes e, em seguida, clique em **seguinte**: 
   
   1. No **Central ambiente de trabalho no URL sessão** caixa de texto, escreva o URL do seu inquilino do ambiente de trabalho Central (por ex.: *http://contoso.centraldesktop.com*).
   2. Na caixa de texto de URL de resposta de ambiente de trabalho Central, escreva o URL de AssertionConsumerService Central do ambiente de trabalho (por ex.: https://contoso.centraldesktop.com/saml2-assertion.php).
   
   >[!NOTE]
   >Pode obter o valor dos metadados de ambiente de trabalho central (por ex.: *http://contoso.centraldesktop.com*).
   >  
   
   ![Configurar o URL da aplicação](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "configurar o URL da aplicação")
4. No **configurar o início de sessão único num ambiente de trabalho Central** página, para transferir o certificado, clique em **Transferir certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.
   
  ![Configurar o início de sessão único](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "configurar o início de sessão único")
5. Inicie sessão no seu **ambiente de trabalho Central** inquilino.
6. Aceda a **definições**, clique em **avançadas**e, em seguida, clique em **início de sessão único**.
   
  ![Configuração - avançada](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "configuração - avançada")
7. No **início de sessão único em definições** página, execute os seguintes passos:
   
  ![As definições de início de sessão único](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "nas definições de início de sessão único")
   
  1. Selecione **ativar SAML v2 início de sessão único**.
  2. No portal clássico do Azure, no **configurar o início de sessão único num ambiente de trabalho Central** página, copie o **URL do emissor** valor e, em seguida, cole-o para o **SSO URL** caixa de texto.
  3. No portal clássico do Azure, no **configurar o início de sessão único num ambiente de trabalho Central** página, copie o **URL de início de sessão remoto** valor e, em seguida, cole-o para o **URL de início de sessão de SSO** caixa de texto .
  4. No portal clássico do Azure, no **configurar o início de sessão único num ambiente de trabalho Central** página, copie o **único URL de serviço Sign-Out** valor e, em seguida, cole-o para o **URL de fim de sessão do SSO**caixa de texto.
8. No **método de verificação de assinatura de mensagem** secção, execute os seguintes passos:
   
   ![Método de verificação de assinatura da mensagem](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "método de verificação de assinatura da mensagem")
   
  1. Selecione **certificado**.
  2. Do **SSO certificado** lista, selecione **RSH SHA256**.
  3. Criar um ficheiro de texto do certificado transferido, copie o conteúdo do ficheiro de texto e, em seguida, cole-o para o **SSO certificado** campo.  
     >[!TIP]
     >Para obter mais detalhes, consulte [como converter um certificado de binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. Selecione **apresentar uma ligação à sua página de início de sessão SAMLv2**.
9. Clique em **atualização**.
10. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "configurar o início de sessão único")
    
## <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores

Para os utilizadores AAD conseguir iniciar sessão, têm de ser aprovisionados para a aplicação de ambiente de trabalho Central. Esta secção descreve como criar contas de utilizador do AAD no ambiente de trabalho Central.

**Aprovisionar contas de utilizador ao ambiente de trabalho Central:**
1. Inicie sessão no seu inquilino de ambiente de trabalho Central.
2. Aceda a **pessoas \> membros internos**.
3. Clique em **adicionar membros internos**.
   
  ![As pessoas](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "pessoas")
4. No **endereço de correio eletrónico de novos membros** caixa de texto, escreva uma conta do AAD, pretende aprovisionar e, em seguida, clique em **seguinte**.
   
  ![Endereços de novos membros de correio eletrónico](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "endereços dos novos membros de E-Mail")
5. Clique em **membro ou membros adicionar interno**.
   
  ![Adicionar membro interno](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Adicionar membro interno")
   
   >[!NOTE]
   >Os utilizadores que adicionou irão receber uma mensagem de e-mail que inclui uma ligação de confirmação têm de clicar para ativar a conta.
   > 

>[!NOTE]
>Pode utilizar qualquer outras ambiente de trabalho Central utilizador conta criação ferramentas ou APIs fornecidas pelo ambiente de trabalho Central para aprovisionar contas de utilizador do AAD
>  

## <a name="assign-users"></a>Atribuir utilizadores
Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

**Para atribuir utilizadores a ambiente de trabalho Central, execute os seguintes passos:**

1. No portal clássico do Azure, crie uma conta de teste.
2. No **ambiente de trabalho Central** página de integração de aplicações, clique em **atribuir utilizadores**.
   
   ![Atribuir utilizadores](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "atribuir utilizadores")
3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
   ![Sim](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sim")

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

