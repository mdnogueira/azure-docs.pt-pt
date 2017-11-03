---
title: "Acesso a aplicações de self-service e gestão de delegado com o Azure Active Directory | Microsoft Docs"
description: "Este artigo descreve como ativar o acesso a aplicações de self-service e gestão de delegado com o Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 448a7fe8-a162-475e-9ba2-2e3ab59302bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 39c62461c9659b0cb4422de88686283ba462c53b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-application-access-and-delegated-management-with-azure-active-directory"></a>Acesso a aplicações de self-service e gestão de delegado com o Azure Active Directory
Ativar capacidades de self-service para utilizadores finais é um cenário comum de TI empresariais. Muitos utilizadores, muitas das aplicações e a pessoa que é best-informed para tomar decisões de conceder acesso poderão não ser o administrador de diretório. Muitas vezes, a pessoa que melhor para decidir quem pode aceder a uma aplicação é uma antecedência de equipa ou outro administrador delegado. No entanto, é o utilizador que utiliza a aplicação e o utilizador sabe o precisam de efetuar o trabalho.

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo. 

Acesso à aplicação self-service é uma funcionalidade do [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) P1 e P2 licenciamento que permitem aos administradores de diretório:

* Permitir que os utilizadores pedir acesso a aplicações utilizando um mosaico "Obter aplicações mais" no [painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* Conjunto de utilizadores que as aplicações podem pedir acesso a
* Definir ou não é necessária para os utilizadores Self-atribuir acesso a uma aplicação a uma aprovação
* Conjunto quem deve aprovar os pedidos e gerir o acesso para cada aplicação

Hoje em dia esta capacidade é suportada para todos os pré-integrado e aplicações personalizadas que suportam único federado baseado em palavra-passe ou início de sessão [Galeria de aplicações do Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/), incluindo aplicações como o Salesforce, Dropbox, Google Apps e muito mais.
Este artigo descreve como:

* Configurar o acesso de aplicação personalizada para os utilizadores finais, incluindo a configuração de um fluxo de trabalho de aprovação opcional 
* Delegar a gestão de acesso para aplicações específicas para as pessoas mais adequadas na sua organização e ativá-los utilizar o painel de acesso do Azure AD para aprovar pedidos de acesso, atribuir diretamente o acesso a utilizadores selecionados ou (opcionalmente) Definir credenciais para acesso de aplicação quando é configurado com base em palavra-passe de início de sessão

## <a name="configuring-self-service-application-access"></a>Configurar o acesso de aplicação personalizada
Para ativar o acesso de aplicação personalizada e configurar as aplicações que podem ser adicionadas ou solicitado por utilizadores finais, siga estas instruções.

1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com/).

2.   Sob o **do Active Directory** secção, selecione o diretório, em seguida, selecione o **aplicações** separador. 

3. Selecione o **adicionar** botão e utilizar a opção de galeria para seleccionar e adicionar uma aplicação.

4. Depois da aplicação foi adicionada, obterá a página de início rápido da aplicação. Clique em **configurar Single Sign-On**, selecione o pretendido único início de sessão no modo e guardar a configuração. 

5. Em seguida, selecione o **configurar** separador. Para permitir que os utilizadores pedir acesso a esta aplicação a partir do painel de acesso do Azure AD, defina **permitir o acesso de aplicação personalizada** para **Sim**.
  
  ![][1]

6. Para configurar, opcionalmente, um fluxo de trabalho de aprovação de pedidos de acesso, defina **exigir a aprovação antes de conceder acesso** para **Sim**. Em seguida, um ou mais aprovadores podem ser selecionadas utilizando o **aprovadores** botão.

  Um aprovador pode ser qualquer utilizador na organização com uma conta do Azure AD e pode ser responsável pela conta de aprovisionamento, licenciamento, ou qualquer outro processo de negócio requer a sua organização antes de conceder acesso a uma aplicação. O aprovador mesmo pode ser o proprietário do grupo de um ou mais partilhado grupos de conta e podem atribuir utilizadores a um destes grupos para atribuir-lhes acesso através de uma conta partilhada. 

  Não se for necessária nenhuma aprovação, em seguida, os utilizadores de forma instantânea obtêm a aplicação ao respetivo painel de acesso do Azure AD. Se a aplicação tiver sido definida se [aprovisionamento de utilizadores automática](active-directory-saas-app-provisioning.md), ou foi configurado [modo SSO de palavra-passe "gerida pelo utilizador"](active-directory-appssoaccess-whatis.md#password-based-single-sign-on), o utilizador já deverá ter um utilizador da conta e conhecer a palavra-passe.

7. Se a aplicação tiver sido configurada para utilizar com base em palavra-passe de início de sessão, em seguida, uma opção para permitir que o aprovador definir as credenciais SSO em nome de cada utilizador também está disponível. Para obter mais informações, consulte a secção sobre [gestão de acesso de delegado](#delegated-application-access-management).

8. Por fim, o **grupo de utilizadores Self-Assigned** mostra o nome do grupo que é utilizado para armazenar os utilizadores que foram concedidos ou atribuídos o acesso à aplicação. O aprovador acesso torna-se o proprietário deste grupo. Se o nome do grupo indicado não existe, é automaticamente criada. Opcionalmente, o nome do grupo pode ser definido como o nome de um grupo existente.

9. Para guardar a configuração, clique em **guardar** na parte inferior do ecrã. Agora, os utilizadores podem para pedir acesso a esta aplicação do painel de acesso.

10. Para experimentar a experiência de utilizador final, inicie sessão no painel de acesso do Azure AD da sua organização em https://myapps.microsoft.com, preferencialmente, utilizando uma conta diferente que não é um aprovador de aplicação. 

11. Sob o **aplicações** separador, clique em de **obter aplicações mais** mosaico. Este mosaico mostra uma galeria de todas as aplicações que foram ativadas para acesso de aplicação personalizada no diretório, com a capacidade de pesquisar e filtrar por categoria de aplicação no lado esquerdo. 

12. Clicar numa aplicação arranca, o processo de pedido. Não se é necessário nenhum processo de aprovação, em seguida, a aplicação serão imediatamente adicionada sob o **aplicações** separador após uma curta confirmação. Se for necessária aprovação, em seguida, verá uma caixa de diálogo que indica esta e é enviado um e-mail para os aprovadores. Esta deve ser iniciada o painel de acesso como um aprovador não para ver este processo de pedido.

13. O e-mail direciona o aprovador para iniciar sessão no painel de acesso do Azure AD e aprovar o pedido. Depois do pedido é aprovado (e quaisquer processos especiais, definir tem sido executados, o aprovador), o utilizador verá a aplicação são apresentados nos respetivos **aplicações** separador onde pode iniciar sessão na mesma.

## <a name="delegated-application-access-management"></a>Gestão de acesso de delegado de aplicação
Um aprovador de acesso de aplicação pode ser qualquer utilizador na sua organização que é a pessoa mais adequada para aprovar ou negar o acesso à aplicação em questão. Este utilizador pode ser responsável por aprovisionamento da conta, licenciamento, ou qualquer outro processo de negócio requer a sua organização antes de conceder acesso a uma aplicação.

Quando configurar o acesso de aplicação personalizada descrito acima, qualquer atribuído aplicação aprovadores Consulte adicional **gerir aplicações** mosaico no painel de acesso do Azure AD, que mostra os quais as aplicações que estão a administrador de acesso para. Clicar uma aplicação apresenta um ecrã com várias opções.

![][2]

### <a name="approve-requests"></a>Aprovar pedidos
O **aprovar pedidos** mosaico permite aprovadores ver qualquer aprovações específicas para essa aplicação pendentes e redireciona para o separador de aprovações onde os pedidos podem ser confirmados ou negados. O aprovador recebe também o correio eletrónico automatizado sempre que um pedido é criado que os instrui o que fazer.

### <a name="add-users"></a>Adicionar utilizadores
O **adicionar utilizadores** mosaico permite aprovadores diretamente conceder acesso à aplicação de utilizadores selecionados. Após clicar neste mosaico, o aprovador verá que uma caixa de diálogo lhes permite ver e procurar utilizadores no seu diretório. Adicionar um resultados de utilizador na aplicação que está a ser apresentada na painéis de acesso desses utilizadores do Azure AD ou o Office 365. Se qualquer processo de aprovisionamento manual do utilizador é necessária a aplicação antes do utilizador é capaz de iniciar sessão, em seguida, o aprovador deve efetuar este processo antes de atribuir acesso.  

### <a name="manage-users"></a>Gerir utilizadores
O **gerir utilizadores** mosaico permite aprovadores diretamente atualizar ou remover os utilizadores que têm acesso à aplicação. 

### <a name="configure-password-sso-credentials-if-applicable"></a>Configurar as credenciais de SSO de palavra-passe (se aplicável)
O **configurar** mosaico só é apresentado se a aplicação foi configurada pelo administrador de TI a utilizar com base em palavra-passe de início de sessão e o administrador conceder o aprovador a capacidade de definir as credenciais SSO de palavra-passe, conforme descrito anteriormente. Quando selecionada, o aprovador é apresentado com várias opções para a forma como as credenciais sejam propagadas aos atribuídos aos utilizadores:

![][3]

* **Os utilizadores iniciam sessão com as suas próprias palavras-passe** – neste modo, os utilizadores atribuídos saber que os respetivos nomes de utilizador e palavras-passe são para a aplicação e, são-lhe pedido que introduza-las após a respetiva primeiro início de sessão para a aplicação. O cenário corresponde do cenário SSO de palavra-passe onde o [utilizadores gerem credenciais](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Os utilizadores são automaticamente sessão iniciados utilizando contas separadas que posso gerir** – neste modo, os utilizadores atribuídos não são necessárias para introduzir ou souber as suas credenciais de específico da aplicação quando iniciar sessão na aplicação. Em vez disso, o aprovador define as credenciais para cada utilizador depois de atribuir acesso através de **adicionar utilizador** mosaico. Quando o utilizador clica na aplicação no painel de acesso ou o Office 365, iniciar automaticamente a sessão utilizando as credenciais definidas pelo aprovador. O cenário corresponde do cenário SSO de palavra-passe onde o [os administradores gerem credenciais](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Os utilizadores são automaticamente iniciado sessão utilizando uma conta única que posso gerir** -num caso especial, neste caso é adequado para utilizar quando todos os utilizadores atribuídos têm de ser concedido acesso utilizando uma conta única partilhada. O caso de utilização mais comum para esta funcionalidade é com aplicações de redes sociais, em que uma organização tiver uma conta única "empresa" e vários utilizadores necessitam para fazer atualizações essa conta. O cenário também corresponde do cenário SSO de palavra-passe onde o [os administradores gerem credenciais](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). No entanto, depois de selecionar esta opção, o aprovador será solicitado para introduzir o nome de utilizador e palavra-passe de conta única partilhada. Depois de concluída, todos os utilizadores atribuídos iniciou a sessão utilizando esta conta quando clicar na aplicação nos respetivos painéis de acesso do Azure AD ou o Office 365.

## <a name="additional-resources"></a>Recursos Adicionais
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG
