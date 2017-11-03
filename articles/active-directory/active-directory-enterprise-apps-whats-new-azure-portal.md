---
title: "Novidades na gestão de aplicações da empresa no Azure Active Directory | Microsoft Docs"
description: "Saiba quais são as novidades na gestão de aplicações da empresa no Azure Active Directory."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 0c32a6719292aa903aa32dfdc4a31114e7a28346
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Novidades na gestão de aplicações da empresa no Azure Active Directory 

Azure Active Directory (Azure AD) tem melhorados ferramentas de gestão de aplicações de empresa, com novas funcionalidades e capacidades para tornar a gestão de aplicações mais simples e eficiente.

Seguem-se alguns dos melhoramentos para o Azure AD no [portal do Azure](https://portal.azure.com).

- Uma galeria de aplicações melhorada experiência, com um modelo de criação de aplicação simplificada e suporte para todos os tipos de aplicação que está a ser utilizado para. 
- Uma experiência de início rápido brand-new que pode ajudar a começar com um piloto da sua aplicação. 
- Configure políticas de self-service com apenas alguns cliques. 
- Melhoramentos ao proxy de aplicações, única configuração de início de sessão e colocar as seus próprios experiências de aplicação, permitindo-lhe obter mais efectuada que antes.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Melhorias na Galeria de aplicações do Azure Active Directory

Adicionar as suas aplicações favoritas, quer sejam do [Galeria de aplicações](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), está a expandir para a nuvem de aplicações personalizadas ou novas aplicações estiver a desenvolver.  Pode começar a utilizar com esta nova experiência, clicando em **adicionar** no **aplicações empresariais** descrição geral ou **todas as aplicações** painéis.
 
  ![Adicionar uma aplicação](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Uma vez na galeria, irá ver todas as nossas aplicações em destaque que suportam o aprovisionamento de utilizadores apresentados frente e center.  Pode procurar todos os tipos de diferentes categorias para explorar as aplicações que mais lhe interessam ou pode utilizar a experiência de pesquisa para localizar rapidamente as aplicações que pretende integrar.

  ![A Galeria de aplicações](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Adicionar aplicações personalizadas a partir de um local

Para além de adicionar aplicações previamente integradas da galeria do, todas as experiências de configuração de aplicação personalizada que eram utilizadas no portal de gestão clássico agora são possíveis no portal de novo. Se estiver a expandir uma aplicação no local com o proxy de aplicações, integrar a sua própria palavra-passe ou a aplicação de SSO federada, ou criar uma aplicação brand-new com o registo de aplicação, pode obter a ele tudo a partir deste único local.

  ![Adicionar a sua própria aplicação](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Para começar a adicionar a sua própria aplicação**:

1. Clique em de **adicionar a sua própria ligação** no topo da Galeria de aplicações. 
2. Verá duas opções à frente do: **implementar uma aplicação existente** ou **desenvolver uma nova aplicação**. Continue a ler para saber a diferença entre as duas opções e como utilizá-los.

### <a name="deploying-existing-applications"></a>Implementar as aplicações existentes

1. Se já tem uma aplicação em execução já e apenas pretende integrá-lo com o Azure AD para início de sessão único em ou aprovisionamento, escolha o **implementar uma aplicação existente** opção. Escolha um nome para a sua aplicação, clique em **adicionar**.
2. Já está! Em vez de ser preciso conhecer todos os detalhes sobre a sua aplicação adiantado, pode agora configurar como funciona a sua nova aplicação ao navegar através do menu à esquerda e configurar a aplicação para sua liking em qualquer altura.

  ![Adicionar uma aplicação existente com um clique](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Desenvolver novas aplicações

1. Se estiver a desenvolver uma nova aplicação, é uma forma fácil de obter ao registo de aplicação correta da galeria do:
2. Clique em de **adicionar os seus próprios** opção da Galeria de aplicações, selecione o **desenvolver uma aplicação existente** escolha, verá uma ligação rápida à direita para a experiência de adicionar aplicações.

  ![Adicionar uma aplicação desenvolvida recentemente em apenas alguns cliques](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Depois de adicionar uma aplicação com o registo de aplicação, verá que apareçam na lista de aplicações da empresa onde poderá configurar o início de sessão único e gerir políticas de acesso para a sua aplicação nova.

  ![Gerir o acesso à sua nova aplicação em aplicações da empresa](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Início rápido: começar com a sua nova aplicação imediato 

Depois de adicionar uma aplicação, se ser previamente integradas ou a própria aplicação, criámos uma experiência de início rápido personalizáveis para ajudá-lo grounded na nova experiência de aplicações, o mais rapidamente. Se seguir cada opção sistematicamente, vamos guiá-lo através da IU e mostrar-lhe como começar com um piloto da sua aplicação nova mais rapidamente possível. 
 
  ![Experiência de início de novas aplicações rápidas](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Pode visitar esta nova experiência de início rápido em qualquer altura e para qualquer aplicação, ao clicar no **início rápido** no menu de navegação esquerdo de aplicação.


## <a name="updated-application-proxy-configuration"></a>Configuração de proxy da aplicação atualizado
Agora, vamos indiquem uma das novas aplicações que adicionou está em execução no seu ambiente no local e pretende integrá-lo com o Azure AD.  Uma das ações que novo frio sobre a nova experiência de configuração de aplicação no Azure AD novo portal é que dividindo início de sessão no modo a aplicação da sua configuração de proxy de aplicação, pode agora facilmente expor palavra-passe SSO ou aplicações federadas que está a executar no seu direito da rede empresarial na nuvem, sem ter de criar várias instâncias da aplicação.

Além disto, agora pode também configurar qualquer uma das novas aplicações que adicionou para utilização com o direito de Proxy de aplicações do Azure AD no portal novo, incluindo as aplicações que suportam nativas experiências de autenticação do Windows.

  ![Configuração de uma aplicação para utilizar a opção de início de sessão na autenticação integrada do Windows](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Para começar a configurar uma aplicação de autenticação nativa do Windows com o Proxy de aplicações:
1. Clique no item de navegação de início de sessão único e escolha **autenticação integrada do Windows** do painel de definições de início de sessão e configurar as definições para o seu liking.
2. Sobre o suporte para estes modos de autenticação de novo, também agora pode carregar certificados a partir de domínios personalizados para suportar aplicações em execução em pontos finais segurados na sua organização.  
 
   ![Carregar um certificado para ser utilizado com o Proxy de aplicações](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Para carregar um novo certificado para a sua aplicação de Favoritos no local, clique em de **proxy de aplicações** opção do menu de navegação esquerdo, clique em de **certificado** Seletor e carregue um certificado ficheiro que pode utilizar para encriptar a pedidos do nosso ponto final da nuvem para a aplicação.

## <a name="advanced-federated-single-sign-on-configuration"></a>Configuração avançada de federado único início de sessão

Para das estiver a utilizar atualmente a aplicações federadas, existem muitas funcionalidades novas no painel de configuração baseados em SAML início de sessão. Para começar, agora pode totalmente personalizar, adicionar, remover e os atributos de utilizador existente emitidos como afirmações no SAML do mapa.
 
  ![Personalizar os atributos de utilizador token SAML passado para uma aplicação federada](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Para verificar que limite a nova configuração de SSO federado:
1. Abrir uma aplicação federada **de sessão único-** painel a partir do menu de navegação esquerdo e certifique-se a '*baseados em SAML início de sessão** modo está seleccionado. 
2. Uma vez, ativar a caixa de verificação sob o **atributos de utilizador** cabeçalho para modificar todos os atributos incluídos no SAML token transmitido para essa aplicação.

Pode também criar, rollover e gerir certificados utilizados para federado-início de sessão único, bem como editar que obtém notificado quando o certificado está prestes a expirar. Irá ver estas novas opções sob o **certificados** cabeçalho no mesmo único início de sessão no painel.
 
  ![Criar um novo certificado, personalizar o e-mail de notificação de expiração e as opções de assinatura de certificado](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Paramenter de estado de reencaminhamento
Por último, iremos também tiver expandido o conjunto de parâmetros de URL de SAML suportamos para incluir o **parâmetro State de reencaminhamento**, que é a página os seus utilizadores serão apresentado no dentro de uma aplicação federada depois do início de sessão está concluído. Isto é muito útil definição para configurar se pretende enviar os seus utilizadores para um local específico na aplicação para começá-las rapidamente.

  ![Definir o parâmetro de estado de reencaminhamento de SAML](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Para definir o parâmetro de estado de reencaminhamento**:

1. Ativar o **Mostrar avançadas definições de URL** caixa de verificação no **domínios e URLs** cabeçalho no início de sessão único no painel de configuração. 
2. Depois de fazê-lo, irá ver que um conjunto novo URL de entrada caixas aparecem que irá permitir-lhe definir esta e outras definições de URL de SAML.

## <a name="bring-your-own-password-sso-applications"></a>Traga a sua própria palavra-passe de aplicações de SSO

Sabemos que nem todas as aplicações suporta a Federação direito a box. Por exemplo, talvez uma das novas aplicações que adicionou tem um ecrã de início de sessão personalizado que os utilizadores utilizam um nome de utilizador e palavra-passe para iniciar sessão no. Ainda pode integrar estes tipos de aplicações com o Azure AD com a nossa **Traga as suas próprias aplicações** funcionalidade, que está agora disponível para configurar no novo portal.
 
  ![Integração de palavra-passe personalizada vaulting aplicações com o Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Verificar a funcionalidade 'Colocar as suas próprias aplicações'**:

1. Depois de definir o modo único início de sessão para uma nova aplicação personalizada que adicionou ao **baseada em palavra-passe de início de sessão**, introduza o URL onde a aplicação compõe o ecrã de início de sessão e clique em **guardar**.  
2. Depois, fazê-lo, iremos irá scrape automaticamente esse URL para um nome de utilizador e palavra-passe caixa de entrada e permite a utilização do Azure AD de forma segura transmitir palavras-passe para essa aplicação utilizando a extensão de browser do painel de acesso.

## <a name="configure-self-service-application-access"></a>Configurar o acesso de aplicação personalizada

Depois de acrescentar muitas aplicações novas, talvez que pretende permitir que os utilizadores procurar e adicionar as aplicações para os seus próprios painéis de acesso, sem necessidade de bother, como administrador. Agora, com esta versão mais recente, pode configurar e gerir o acesso de aplicação personalizada diretamente a partir do novo portal.

  ![Configurar o acesso de aplicação personalizada para uma aplicação de SSO de palavra-passe](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Para configurar e gerir o acesso de aplicação personalizada**:

1. Para começar a utilizar, pode selecionar o **self-service** opção da aplicação da esquerda do menu de navegação e defina o **permitir que os utilizadores pedir acesso a esta aplicação?** opção para '**Sim**'. 
2. Isto permitirá configurar quem tem permissão para aprovar o acesso a esta aplicação e os utilizadores self-service do grupo serão adicionados. Além disso, se a aplicação está configurada para a palavra-passe início de sessão único, também verá outra opção que lhe permite, opcionalmente, permitir que esses aprovadores gerir as palavras-passe atribuídas à aplicação.

##<a name="feedback"></a>Comentários

Esperamos, como a utilização a melhor experiência do Azure AD. Manter os comentários provenientes! Publique os seus comentários e ideias para melhoria no **Portal de administração** secção do nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Iremos estiver entusiasmados sobre como criar conteúdo novo frio diariamente e utilize a orientações sobre a forma e definir o que devemos criar a seguir.

## <a name="next-steps"></a>Passos seguintes

Para obter mais detalhes, consulte [gerir aplicações com o Azure Active Directory](active-directory-enable-sso-scenario.md).



