---
title: "Problemas em iniciar sessão a uma aplicação a partir do painel de acesso | Microsoft Docs"
description: "Como resolver problemas de acesso à aplicação a partir do painel de acesso do Microsoft Azure AD em myapps.microsoft.com"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 188a00db59b0aa8d26facc678fb52d96272183b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemas em iniciar sessão a uma aplicação a partir do painel de acesso

O painel de acesso é um portal baseado na web que permite que um utilizador com uma conta escolar ou profissional no Azure Active Directory (Azure AD) para ver e iniciar as aplicações baseadas na nuvem que o administrador do Azure AD concedeu-lhes acesso. 

Estas aplicações estão configuradas em nome do utilizador no portal do Azure AD. A aplicação tem de ser corretamente configurada e atribuída ao utilizador ou um grupo que do utilizador é um membro de para ver a aplicação no painel de acesso.

O tipo de aplicações um utilizador pode estar a ver enquadram-se nas seguintes categorias:

-   Aplicações do Office 365

-   Aplicações de terceiros da Microsoft e configuradas com o SSO baseada em Federação

-   Aplicações de SSO baseadas em palavra-passe

-   Aplicações com soluções de SSO existentes

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Certifique-se a utilização de um **browser** que cumpra os requisitos mínimos para o painel de acesso.

-   Certifique-se de que o browser do utilizador foi adicionado o URL da aplicação a respetiva **fidedigna sites**.

-   Certifique-se a aplicação é **configurado** corretamente.

-   Certifique-se de que a conta de utilizador **ativada** para inícios de sessão.

-   Certifique-se de que a conta de utilizador **não bloqueadas.**

-   Certifique-se de que o utilizador **palavra-passe não está expirado ou esquecimento.**

-   Certifique-se **multi-factor Authentication** não está a bloquear o acesso do utilizador.

-   Certifique-se de um **política de acesso condicional** ou **Identity Protection** política não está a bloquear o acesso do utilizador.

-   Certifique-se de que um utilizador **informações de contacto de autenticação** é atualizado para permitir que as políticas de autenticação Multifator ou o acesso condicional a serem impostas.

-   Certifique-se para também tente limpar os cookies do browser e tentar iniciar sessão novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Que cumprem os requisitos de browser para o painel de acesso

O painel de acesso necessita de um browser que suporte JavaScript e ativou CSS. Para utilizar com base em palavra-passe-início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no browser do utilizador. Esta extensão é transferida automaticamente quando um utilizador seleciona uma aplicação que está configurada para SSO baseada em palavra-passe.

Para SSO baseada em palavra-passe, os browsers do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior

-   Limite de aniversário da edição do Windows 10 ou posterior

-   Chrome – No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou posterior – no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga os passos abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) dos browsers suportados e início de sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação de palavra-passe SSO** no painel de acesso.

3.  Na linha de pedir instalar o software, selecione **instalar agora**.

4.  Com base no seu browser, direcionado para a hiperligação de transferência. **Adicionar** a extensão para o seu browser.

5.  Se o browser pede-lhe, selecione como **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reiniciar** a sessão do browser.

7.  Inicie sessão no painel de acesso e veja se pode **iniciar** as aplicações de SSO de palavra-passe

Também pode transferir a extensão para Chrome e limite de ligações diretas abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão de painel de acesso de limite](https://www.microsoft.com/store/apps/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar federado-início de sessão único para uma aplicação de galeria do Azure AD

Todas as aplicações na galeria do Azure AD ativada com capacidade de Enterprise Single Sign-On tem um tutorial passo a passo disponível. Pode aceder a [lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo de detalhe.

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação na galeria do Azure AD](#add-an-application)

-   [Configurar os valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD na aplicação (início de sessão URL, o emissor, o URL de fim de sessão e o certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Atribuir utilizadores para a aplicação](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação na galeria do Azure AD

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel

6.  No **introduza um nome** caixa de texto do **adicionar da galeria do** secção, escreva o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para o início de sessão único.

8.  Antes de adicionar a aplicação, pode alterar o nome do **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o início de sessão para uma aplicação na galeria do Azure AD

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione **baseados em SAML início de sessão** do **modo** pendente.

9.  Introduza os valores necessários no **URLs e domínio.** Deve obter estes valores do fabricante da aplicação.

   1. Para configurar a aplicação como SSO iniciado por SP, o URL de início de sessão é um valor necessário. Para algumas aplicações, o identificador também é um valor necessário.

   2. Para configurar a aplicação como SSO iniciado por IdP, o URL de resposta é um valor necessário. Para algumas aplicações, o identificador também é um valor necessário.

10. **Opcional:** clique **Mostrar avançadas definições de URL** se pretender ver os valores não necessário.

11. No **atributos de utilizador**, selecione o identificador único para os utilizadores no **identificador de utilizador** pendente.

12. **Opcional:** clique **ver e editar todos os outros atributos de utilizador** para editar os atributos a ser enviadas para a aplicação no SAML token quando o utilizador iniciar sessão.

   Para adicionar um atributo:

   1. Clique em **adicionar atributo**. Introduza o **nome** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** Pode ver o novo atributo na tabela.

13. Clique em **configurar &lt;nome da aplicação&gt;**  documentação de acesso sobre como configurar o início de sessão único na aplicação. Além disso, tem os URLs de metadados e o certificado necessário para configurar o SSO com a aplicação.

14. Clique em **guardar** para guardar a configuração.

15. Atribua utilizadores para a aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

Para selecionar o identificador de utilizador ou adicionar atributos de utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui a cópia de segurança, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação tiver configurado o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Sob o **atributos de utilizador** secção, selecione o identificador único para os utilizadores no **identificador de utilizador** pendente. A opção selecionada tem de corresponder ao valor esperado na aplicação para autenticar o utilizador.

    >[!NOTE]
    >Selecione de AD do Azure o formato para o atributo de NameID (identificador de utilizador) com base no valor selecionado ou o formato solicitado pela aplicação na AuthRequest SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.
    >
    >

9.  Para adicionar os atributos de utilizador, clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a ser enviadas para a aplicação no SAML token quando o utilizador iniciar sessão.

   Para adicionar um atributo:

   1. Clique em **adicionar atributo**. Introduza o **nome** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** Pode ver o novo atributo na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

Para transferir os metadados da aplicação ou o certificado do Azure AD, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação tiver configurado o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Aceda a **certificado de assinatura de SAML** secção, em seguida, clique em **transferir** valor da coluna. Consoante a aplicação que necessita de configurar o início de sessão único, consulte o a opção para transferir o XML de metadados ou o certificado.

    Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser obtidos como um ficheiro XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar federado-início de sessão único para uma aplicação não Galeria

Para configurar uma aplicação não galeria, tem de ter o Azure AD premium e a aplicação suporta SAML 2.0. Para obter mais informações sobre as versões do AD do Azure, visite [preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configurar os valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configuring-single-sign-on)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD na aplicação (início de sessão URL, o emissor, o URL de fim de sessão e o certificado)](#configuring-single-sign-on)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurar os valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)

Para configurar o início de sessão para uma aplicação que não se encontra na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel

6.  Clique em **aplicação Galeria não** no **adicionar a sua própria aplicação** secção

7.  Introduza o nome da aplicação a **nome** caixa de texto.

8.  Clique em **adicionar** botão, para adicionar a aplicação.

9.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

10. Selecione **baseados em SAML início de sessão** no **modo** pendente

11. Introduza os valores necessários no **URLs e domínio.** Deve obter estes valores do fabricante da aplicação.

  1. Para configurar a aplicação como SSO iniciado por IdP, introduza o URL de resposta e o identificador.

  2. **Opcional:** para configurar a aplicação como SSO iniciado por SP, o URL de início de sessão é um valor necessário.

12. No **atributos de utilizador**, selecione o identificador único para os utilizadores no **identificador de utilizador** pendente.

13. **Opcional:** clique **ver e editar todos os outros atributos de utilizador** para editar os atributos a ser enviadas para a aplicação no SAML token quando o utilizador iniciar sessão.

   Para adicionar um atributo:

   1. Clique em **adicionar atributo**. Introduza o **nome** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** Pode ver o novo atributo na tabela.

14. Clique em **configurar &lt;nome da aplicação&gt;**  documentação de acesso sobre como configurar o início de sessão único na aplicação. Além disso, tem URLs do Azure AD e o certificado necessário para a aplicação.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

Para selecionar o identificador de utilizador ou adicionar atributos de utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação tiver configurado o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Sob o **atributos de utilizador** secção, selecione o identificador único para os utilizadores no **identificador de utilizador** pendente. A opção selecionada tem de corresponder ao valor esperado na aplicação para autenticar o utilizador.

   >[!NOTE]
   >Selecione de AD do Azure o formato para o atributo de NameID (identificador de utilizador) com base no valor selecionado ou o formato solicitado pela aplicação na AuthRequest SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.
   >
   >

9.  Para adicionar os atributos de utilizador, clique em **ver e editar todos os outros atributos de utilizador** para editar os atributos a ser enviadas para a aplicação no SAML token quando o utilizador iniciar sessão.

   Para adicionar um atributo:

   1. clique em **adicionar atributo**. Introduza o **nome** e selecione o **valor** na lista pendente.

   Clique em 2 **guardar.** Pode ver o novo atributo na tabela.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

Para transferir os metadados da aplicação ou o certificado do Azure AD, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação tiver configurado o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Aceda a **certificado de assinatura de SAML** secção, em seguida, clique em **transferir** valor da coluna. Consoante a aplicação que necessita de configurar o início de sessão único, consulte o a opção para transferir o XML de metadados ou o certificado.

    Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser obtidos como um ficheiro XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar a palavra-passe-início de sessão único para uma aplicação de galeria do Azure AD

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação na galeria do Azure AD](#add-an-application)

-   [Configurar a aplicação para a palavra-passe-início de sessão único](#configure-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação na galeria do Azure AD

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel

6.  No **introduza um nome** caixa de texto do **adicionar da galeria do** secção, escreva o nome da aplicação

7.  Selecione a aplicação que pretende configurar para o início de sessão único

8.  Antes de adicionar a aplicação, pode alterar o nome do **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe-início de sessão único

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

 * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  Atribua utilizadores para a aplicação.

10. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar no **credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome de utilizadores. Caso contrário, os utilizadores ser pedido que introduza as credenciais próprios após iniciar.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe-início de sessão único para uma aplicação não Galeria

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação não Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe-início de sessão único](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação não Galeria

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel

6.  Clique em **aplicação não galeria.**

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe-início de sessão único

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

 * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**. Este é o URL onde os utilizadores introduzem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. Certifique-se de que os campos de início de sessão está visível no URL.

10. Atribua utilizadores para a aplicação.

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar no **credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome de utilizadores. Caso contrário, os utilizadores ser pedido que introduza as credenciais próprios após iniciar.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir diretamente um utilizador a uma aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de correio eletrónico** do utilizador que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **utilizador** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do utilizador ou logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais do que um utilizador**, tipo noutra **nome completo** ou **endereço de correio eletrónico** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação para os utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou conseguir iniciar estas aplicações no painel de acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se efetue estes passos de resolução de problemas não a resolva o problema

Abra um pedido de suporte com as seguintes informações se disponíveis:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios de fiddler

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)

