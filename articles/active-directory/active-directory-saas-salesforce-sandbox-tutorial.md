---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce Sandbox | Microsoft Docs"
description: "Saiba como utilizar o Salesforce Sandbox no Azure Active Directory para ativar o início de sessão único, automatizada de aprovisionamento e muito mais!."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 32835e79188806bb2ff319eea23b1b52ab585ab1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Azure Active Directory com o Salesforce Sandbox

O objetivo deste tutorial é mostrar a integração do Azure e a Salesforce Sandbox.  

>[!TIP]
>Para comentários, consulte o [página de suporte do Azure](http://go.microsoft.com/fwlink/?LinkId=521878). 
> 

Sandboxes dão-lhe a capacidade de criar várias cópias da sua organização em ambientes diferentes para diversos fins, tal como o desenvolvimento, teste e de preparação, sem comprometer a dados e as aplicações na produção Salesforce organização.  

Para obter mais detalhes, consulte [Sandbox descrição-geral](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)

O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Uma sandbox no site Salesforce.com

Se ainda não tiver uma sandbox válida no site Salesforce.com, terá de contactar o Salesforce.

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

1. Ativar a integração de aplicações do Salesforce Sandbox
2. Configurar início de sessão único (SSO)
3. Ativar o seu domínio
4. Configurar o aprovisionamento de utilizadores
5. Atribuir utilizadores

![Cenário](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "cenário")

## <a name="enable-the-application-integration-for-salesforce-sandbox"></a>Ativar a integração de aplicações do Salesforce Sandbox
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações do Salesforce sandbox.

**Para ativar a integração de aplicações do Salesforce sandbox, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
   ![Do Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "do Active Directory")
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
   ![Aplicações](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "aplicações")
4. Para abrir o **Galeria de aplicações**, clique em **adicionar uma aplicação**e, em seguida, clique em **adicionar uma aplicação para a minha organização utilizar**.
   
   ![O que pretende fazer? ] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Que pretende fazer?")
5. No **caixa pesquisa**, tipo **Salesforce Sandbox**.
   
   ![Galeria de aplicações](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galeria de aplicações")
6. No painel de resultados, selecione **Salesforce Sandbox**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
   
## <a name="configur-single-sign-on-sso"></a>Configur-início de sessão único (SSO)

O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar Salesforce com a conta no Azure AD com Federação com base no protocolo SAML.

**Para configurar o início de sessão único, execute os seguintes passos:**

1. No portal clássico do Azure, no **Salesforce Sandbox** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "configurar o início de sessão único")
2. No **como gostaria aos utilizadores iniciar sessão Salesforce Sandbox** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")
3. No **configurar o URL da aplicação** na página de **URL de início de sessão** caixa de texto, escreva o URL através do padrão de seguinte `http://company.my.salesforce.com`e, em seguida, clique em **seguinte**.
   
   ![Configurar o URL da aplicação](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "configurar o URL da aplicação")
4. Se já tiver configurado início de sessão único para outra instância do Salesforce Sandbox no seu diretório, em seguida, também tem de configurar o **identificador** ter o mesmo valor que o **iniciar sessão no URL**. 
 * O **identificador** campo pode ser encontrado ao verificar o **mostrar as definições avançadas** caixa de verificação no **URL da aplicação configurar** página da caixa de diálogo.
5. No **configurar o início de sessão único em Salesforce Sandbox** página, clique em **Transferir certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "configurar o início de sessão único")
6. Numa janela do browser web diferente, inicie sessão no seu sandbox Salesforce como administrador.
7. No menu na parte superior, clique em **configuração**.
   
   ![A configuração](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "programa de configuração")
8. No painel de navegação à esquerda, clique em **controlos de segurança**e, em seguida, clique em **as definições de início de sessão único**.
   
   ![Single Sign-On definições](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "único as definições de início de sessão")
9. A secção de definições de início de sessão único, execute os seguintes passos:
   
   ![Single Sign-On definições](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "único as definições de início de sessão")  
 1.  Selecione **SAML ativada**. 
 2.  Clique em **Novo**.
10. A secção de SAML único início de sessão definições, execute os seguintes passos:
    
    ![SAML único início de sessão definições](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML único início de sessão definições")  
 1. Na caixa de texto nome, escreva o nome da configuração (por ex.: *SPSSOWAAD\_teste*). 
 2. No portal clássico do Azure, no **configurar o início de sessão único em Salesforce Sandbox** página de diálogo, copie o **URL do emissor** valor e, em seguida, cole-o para o **emissor** caixa de texto.
 3. No **Id de entidade** caixa de texto, tipo **https://test.salesforce.com** se esta for a primeira instância do Salesforce Sandbox que estiver a adicionar ao seu diretório. Se já adicionou uma instância do Salesforce Sandbox, em seguida, para o **ID de entidade** escreva a **URL de início de sessão**, que deve ter este formato:`http://company.my.salesforce.com`   
 4. Clique em **procurar** para carregar o certificado transferido.  
 5. Como **tipo de identidade de SAML**, selecione **asserção contém o ID de Federação do objeto User**. 
 6. Como **SAML identidade localização**, selecione **é de identidade no elemento NameIdentifier da declaração de assunto**.
 7. No portal clássico do Azure, no **configurar o início de sessão único em Salesforce Sandbox** página de diálogo, copie o **URL de início de sessão remoto** valor e, em seguida, cole-o para o **URL de início de sessão do fornecedor de identidade**  caixa de texto. 
 8. SFDC não suporta a fim de sessão SAML.  Como solução, cole 'https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0' para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.
 9. Como **fornecedor iniciada pedido vínculo de serviço**, selecione **HTTP POST**. 
 10. Clique em **Guardar**.
11. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "configurar o início de sessão único")

## <a name="enable-your-domain"></a>Ativar o seu domínio
Esta secção assume que já criou um domínio.  Para obter mais detalhes, consulte [definir o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Para ativar o seu domínio, execute os seguintes passos:**

1. No painel de navegação esquerdo, clique em **gestão de domínios**e, em seguida, clique em **meu domínio.**
   
   ![O meu domínio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "meu domínio")
   
   >[!NOTE]
   >Certifique-se que o seu domínio foi configurado corretamente. 
   > 
2. No **definições de páginas de início de sessão** secção, clique em **editar**, em seguida, como **serviço de autenticação**, selecione o nome de SAML único início de sessão na definição da secção anterior e, finalmente, clique em **guardar**.
   
   ![O meu domínio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "meu domínio")

Assim que tiver um domínio configurado, os utilizadores devem utilizar o URL do domínio para início de sessão para a sandbox do Salesforce.  

Para obter o valor do URL, clique no perfil SSO que criou na secção anterior.

## <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores
O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de utilizadores do Active Directory de contas de utilizador para a Sandbox do Salesforce.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. No portal do Salesforce, na barra de navegação superior, selecione o nome para expandir o menu de utilizador:
   
   ![As minhas definições](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "as minhas definições")
2. No menu do utilizador, selecione **as minhas definições** para abrir o **as minhas definições** página.
3. No painel esquerdo, clique em **pessoais** para expandir a secção pessoal e, em seguida, clique em **repor o meu Token de segurança**:
   
   ![As minhas definições](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "as minhas definições")
4. No **repor o meu Token de segurança** página, clique em **repor o Token de segurança** para pedir uma mensagem de e-mail que contém o token de segurança em Salesforce.com.
   
   ![Novo Token](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "novo Token")
5. Verifique a pasta a receber de correio eletrónico para uma mensagem de e-mail em Salesforce.com com "**confirmação de segurança salesforce.com.com**" como requerente.
6. Rever este e-mail e copie o valor de token de segurança.
7. No portal clássico do Azure, no **salesforce Sandbox** página de integração de aplicações, clique em **configurar o aprovisionamento de utilizadores** para abrir o **configurar o aprovisionamento de utilizadores** caixa de diálogo.
   
   ![Configurar o aprovisionamento de utilizadores](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "configurar o aprovisionamento de utilizadores")
8. No **introduzir as suas credenciais Salesforce Sandbox para ativar o aprovisionamento de utilizador automáticas** página, forneça as seguintes definições de configuração:
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")   
 1. No **nome de utilizador de Admin do Salesforce Sandbox** caixa de texto, tipo de uma sandbox Salesforce conta nome que tenha o **administrador de sistema** perfil no site Salesforce.com atribuído.
 2. No **palavra-passe de administrador do Salesforce Sandbox** caixa de texto, escreva a palavra-passe para esta conta.
 3. No **Token de segurança do utilizador** caixa de texto, colar o valor de token de segurança.
 4. Clique em **validar** para verificar a configuração.
 5. Clique em de **seguinte** botão para abrir o **confirmação** página.
9. No **confirmação** página, clique em **concluída** para guardar a configuração.
   
## <a name="assigning-users"></a>Atribuir utilizadores

Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

**Para atribuir utilizadores a Salesforce Sandbox, execute os seguintes passos:**

1. No portal clássico do Azure, crie uma conta de teste.
2. No * * Salesforce Sandbox * * página de integração de aplicações, clique em **atribuir utilizadores**.
   
   ![Atribuir utilizadores](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "atribuir utilizadores")
3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
   ![Sim](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sim")

Deve agora Aguarde 10 minutos e certifique-se de que a conta do Salesforce Sandbox foi sincronizada.

Se pretender testar as definições de SSO, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).

