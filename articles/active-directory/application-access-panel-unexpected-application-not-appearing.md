---
title: "Uma aplicação atribuída é não volte a aparecer no painel de acesso | Microsoft Docs"
description: "Resolver problemas com a razão pela qual uma aplicação não está a ser apresentados no painel de acesso"
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
ms.reviwer: japere
ms.openlocfilehash: 9ea5744d77b90929598ea5feb80c7bbdff3772fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Uma aplicação atribuída é não volte a aparecer no painel de acesso

O painel de acesso é um portal baseado na web que permite que um utilizador com uma conta escolar ou profissional no Azure Active Directory (Azure AD) para ver e iniciar as aplicações baseadas na nuvem que o administrador do Azure AD concedeu-lhes acesso. Estas aplicações estão configuradas em nome do utilizador no portal do Azure AD. A aplicação tem de ser corretamente configurada e atribuída ao utilizador ou um grupo que do utilizador é um membro de para ver a aplicação no painel de acesso.

O tipo de aplicações um utilizador pode estar a ver enquadram-se nas seguintes categorias:

-   Aplicações do Office 365

-   Aplicações de terceiros da Microsoft e configuradas com o SSO baseada em Federação

-   Aplicações de SSO baseadas em palavra-passe

-   Aplicações com soluções de SSO existentes

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Se uma aplicação que acabou de ser adicionada a um utilizador, tente voltar a iniciar e terminar no painel de acesso do utilizador após alguns minutos para ver se a aplicação é adicionada.

-   Se uma licença apenas tiver sido removida de um utilizador ou grupo, o utilizador é que membro deste processo poderá demorar muito tempo, dependendo do tamanho e complexidade do grupo para que as alterações para ser efetuada. Permitir tempo adicional antes de iniciar sessão do painel de acesso.

## <a name="problems-related-to-application-configuration"></a>Problemas relacionados com a configuração de aplicação

Uma aplicação não pode ser apresentados no painel de acesso de um utilizador porque não está configurado corretamente. Seguem-se algumas formas pode resolver problemas relacionados com a configuração de aplicação:

-   [Como configurar federado-início de sessão único para uma aplicação de galeria do Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Como configurar federado-início de sessão único para uma aplicação não Galeria](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Como configurar uma palavra-passe única início de sessão na aplicação para uma aplicação de galeria do Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Como configurar uma palavra-passe única início de sessão na aplicação para uma aplicação não Galeria](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar federado-início de sessão único para uma aplicação de galeria do Azure AD

Todas as aplicações na galeria do Azure AD ativada com capacidade de Enterprise Single Sign-On tem um tutorial passo a passo disponível. Pode aceder a [lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo de detalhe.

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação na galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar os valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD na aplicação (início de sessão URL, o emissor, o URL de fim de sessão e o certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação na galeria do Azure AD

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel.

6.  No **introduza um nome** caixa de texto do **adicionar da galeria do** secção, escreva o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para o início de sessão único.

8.  Antes de adicionar a aplicação, pode alterar o nome do **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o início de sessão para uma aplicação na galeria do Azure AD

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

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

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

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

   2. Clique em **guardar.** Verá o novo atributo na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar federado-início de sessão único para uma aplicação não Galeria

Para configurar uma aplicação não galeria, tem de ter o Azure AD premium e a aplicação suporta SAML 2.0. Para obter mais informações sobre as versões do AD do Azure, visite [preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configurar os valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)](#configuring-single-sign-on)

-   [Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Obter metadados do Azure AD e o certificado](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD na aplicação (início de sessão URL, o emissor, o URL de fim de sessão e o certificado)](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurar os valores de metadados da aplicação no Azure AD (URL de resposta de URL, identificador, início de sessão)

Para configurar o início de sessão para uma aplicação que não se encontra na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel.

6.  Clique em **aplicação Galeria não** no **adicionar a sua própria aplicação** secção.

7.  Introduza o nome da aplicação a **nome** caixa de texto.

8.  Clique em **adicionar** botão, para adicionar a aplicação.

9.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

10. Selecione **baseados em SAML início de sessão** no **modo** pendente.

11. Introduza os valores necessários no **URLs e domínio.** Deve obter estes valores do fabricante da aplicação.

   1. Para configurar a aplicação como SSO iniciado por IdP, introduza o URL de resposta e o identificador.

   2.  **Opcional:** para configurar a aplicação como SSO iniciado por SP, o URL de início de sessão é um valor necessário.

12. No **atributos de utilizador**, selecione o identificador único para os utilizadores no **identificador de utilizador** pendente.

13. **Opcional:** clique **ver e editar todos os outros atributos de utilizador** para editar os atributos a ser enviadas para a aplicação no SAML token quando o utilizador iniciar sessão.

   Para adicionar um atributo:

   1. Clique em **adicionar atributo**. Introduza o **nome** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** Pode ver o novo atributo na tabela.

14. Clique em **configurar &lt;nome da aplicação&gt;**  documentação de acesso sobre como configurar o início de sessão único na aplicação. Além disso, tem URLs do Azure AD e o certificado necessário para a aplicação.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecione o identificador de utilizador e adicionar atributos de utilizador sejam enviados para a aplicação

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

   1. Clique em **adicionar atributo**. Introduza o **nome** e selecione o **valor** na lista pendente.

   2. Clique em **guardar.** Pode ver o novo atributo na tabela.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Transferir os metadados do Azure AD ou o certificado

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar a palavra-passe-início de sessão único para uma aplicação de galeria do Azure AD

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação na galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar a aplicação para a palavra-passe-início de sessão único](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação na galeria do Azure AD

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel.

6.  No **introduza um nome** caixa de texto do **adicionar da galeria do** secção, escreva o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para o início de sessão único.

8.  Antes de adicionar a aplicação, pode alterar o nome do **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe-início de sessão único

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

9.  [Atribuir utilizadores para a aplicação](#how-to-assign-a-user-to-an-application-directly).

10. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar no **credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome de utilizadores. Caso contrário, os utilizadores ser pedido que introduza as credenciais próprios após iniciar.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe-início de sessão único para uma aplicação não Galeria

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação não Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe-início de sessão único](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação não Galeria

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**.

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel.

6.  Clique em **aplicação não galeria.**

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe-início de sessão único

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

    1.  Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**. Este é o URL onde os utilizadores introduzem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. Certifique-se de que os campos de início de sessão está visível no URL.

10. [Atribuir utilizadores para a aplicação](#how-to-assign-a-user-to-an-application-directly).

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar no **credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome de utilizadores. Caso contrário, os utilizadores ser pedido que introduza as credenciais próprios após iniciar.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações para utilizadores

Um utilizador pode não estar a ver uma aplicação no respetivo painel de acesso porque não estão atribuídos à aplicação. Seguem-se algumas formas de verificação:

-   [Certifique-se um utilizador atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Como atribuir diretamente um utilizador a uma aplicação](#how-to-assign-a-user-to-an-application-directly)

-   [Certifique-se um utilizador atribuído a uma licença relacionadas com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Como atribuir uma licença a um utilizador](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Certifique-se um utilizador atribuído à aplicação

Para verificar se um utilizador está atribuído à aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

6.  **Pesquisa** para o nome da aplicação em questão.

7.  Clique em **utilizadores e grupos**.

8.  Verifique se o utilizador está atribuído à aplicação.

   * Se não siga os passos em "Como atribuir diretamente um utilizador a uma aplicação" para o fazer.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir diretamente um utilizador a uma aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verifique se um utilizador com uma licença relacionadas com a aplicação

Para verificar as licenças atribuídas de um utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o utilizador tem atribuída.

  * Se o utilizador está atribuído a um Office licenças este ativar primeiro terceiros as aplicações do Office a aparecer no painel de acesso do utilizador.

### <a name="how-to-assign-a-user-a-license"></a>Como atribuir uma licença de um utilizador 

Para atribuir uma licença a um utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o utilizador tem atribuída.

8.  Clique em de **atribuir** botão.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique o **opções de atribuição** item granularly atribuir produtos. Clique em **Ok** quando este está concluída.

11. Clique em de **atribuir** botão para atribuir estes licenças para este utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de aplicações para grupos

Um utilizador pode estar a ver uma aplicação no respetivo painel de acesso porque fazem parte de um grupo que tenha sido atribuído a aplicação. Seguem-se algumas formas de verificação:

-   [Verifique as associações de grupo do utilizador](#check-a-users-group-memberships)

-   [Como atribuir diretamente uma aplicação a um grupo](#how-to-assign-an-application-to-a-group-directly)

-   [Verificar se um utilizador faz parte do grupo atribuído a uma licença](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Como atribuir uma licença a um grupo](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Verifique as associações de grupo do utilizador

Para verificar a associação a um grupo, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **grupos**.

8.  Verifique se o utilizador faz parte de um grupo atribuído à aplicação.

  * Se pretender remover o utilizador do grupo, **clique na linha** de eliminar o grupo e selecione.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Como atribuir diretamente uma aplicação a um grupo

Para atribuir um ou mais grupos diretamente a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global**.

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome do grupo completa** do grupo que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **grupo** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais de um grupo**, tipo noutra **nome do grupo completa** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar grupos, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação em grupos selecionados.

Após um curto período de tempo, os utilizadores que selecionou conseguir iniciar estas aplicações no painel de acesso.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Verificar se um utilizador faz parte do grupo atribuído a uma licença

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **grupos**.

8.  Clique na linha de um grupo específico.

9.  Clique em **licenças** ver as licenças que o grupo foi atribuída.

   * Se o grupo for atribuído a uma licença do Office que isto poderá ativar determinadas aplicações do Office de terceiros primeiro a aparecer no painel de acesso do utilizador.

### <a name="how-to-assign-a-license-to-a-group"></a>Como atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o grupo foi atribuído.

8.  Clique em de **atribuir** botão.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique o **opções de atribuição** item granularly atribuir produtos. Clique em **Ok** quando este está concluída.

11. Clique em de **atribuir** botão para atribuir estes licenças para este grupo. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

>[!NOTE]
>Para fazê-lo mais rapidamente, considere temporariamente diretamente, atribuir uma licença ao utilizador. 
>
>

## <a name="next-steps"></a>Passos seguintes
[Adicionar novos utilizadores ao Azure Active Directory](active-directory-users-create-azure-portal.md)

