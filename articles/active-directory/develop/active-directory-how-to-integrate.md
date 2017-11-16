---
title: Como integrar com o Azure Active Directory | Microsoft Docs
description: "Um guia para benefícios do e recursos para integração com o Azure Active Directory."
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: e2a83e8dcd054576b4a99533cdfbb9f5605cd6f9
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="integrating-with-azure-active-directory"></a>Integração com o Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory fornece às organizações com gestão de identidades de nível empresarial para aplicações em nuvem.  A integração do Azure AD fornece aos utilizadores uma experiência de início de sessão simplificada e ajuda a sua aplicação está em conformidade com a política de TI.

## <a name="how-to-integrate"></a>Como integrar
Existem várias formas para a sua aplicação integrar com o Azure AD.  Tire partido das como muitas ou poucos um destes cenários, como é adequado para a sua aplicação.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Suporte do Azure AD como uma forma de início de sessão na sua aplicação
**Reduza o início de sessão friction e reduzir os custos de suporte.** Ao utilizar o Azure AD para iniciar sessão sua aplicação, os utilizadores não podem ter um nome mais e a palavra-passe de recordar.  Como um programador, terá uma menor palavra-passe para armazenar e proteger.  Não terem de lidar com a reposição de palavra-passe esquecida pode ser uma poupança significativa individualmente.  Azure AD está na base de início de sessão para algumas das mais populares aplicações em nuvem o mundo, incluindo o Office 365 e o Microsoft Azure.  Com centenas de milhões são de possibilidades de utilizadores de milhões de organizações, o utilizador já iniciou sessão com o Azure AD.  Saiba mais sobre [adicionando suporte para início de sessão do Azure AD](active-directory-authentication-scenarios.md).

**Simplifica a sessão cópias de segurança para a sua aplicação.**  Durante a inscrição para a sua aplicação do Azure AD pode enviar informações essenciais sobre um utilizador para que possa previamente preencher a formulário de inscrição ou eliminá-la completamente.  Os utilizadores podem inscrever-se para a sua aplicação utilizando a respetiva conta do Azure AD através de uma experiência familiar consentimento semelhante às encontrado de redes sociais e de aplicações móveis.  Qualquer utilizador pode inscrever-se e iniciar sessão para uma aplicação que está integrada com o Azure AD sem necessidade de envolvimento de IT.  Saiba mais sobre [assinatura-se a aplicação para início de sessão de conta do Azure AD](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Procurar utilizadores, gerir o aprovisionamento de utilizadores e controlar o acesso à sua aplicação
**Procure utilizadores no diretório.**  Utilize a Graph API para ajudar os utilizadores de pesquisa e procure outras pessoas na sua organização quando inviting outras pessoas ou conceder acesso, em vez de exigi-los para o tipo de correio eletrónico endereços.  Os utilizadores podem procurar utilizando uma interface de estilo de livro endereço familiar, incluindo a visualização dos detalhes da hierarquia organizacional.  Saiba mais sobre o [Graph API](active-directory-graph-api.md).

**Utilize novamente a grupos do Active Directory e listas de distribuição, que o cliente já está a gerir.**  Azure AD contém os grupos que o cliente já está a utilizar para distribuição de correio eletrónico e gerir o acesso.  Voltar a utilizar a Graph API, utilize estes grupos em vez de exigir o cliente criar e gerir um conjunto separado de grupos na sua aplicação.  Informações de grupo também podem ser enviadas para a aplicação no início de sessão de tokens.  Saiba mais sobre o [Graph API](active-directory-graph-api.md).

**Utilize o Azure AD para controlar quem tem acesso à sua aplicação.**  Os administradores e os proprietários da aplicação no Azure AD podem atribuir acesso a aplicações para utilizadores e grupos específicos.  Utilizar a Graph API, pode ler esta lista e utilizá-la para controlar o aprovisionamento e anular o aprovisionamento de recursos e de acesso na sua aplicação.

**Controlo de acesso baseado em utilizar o Azure AD para funções.**  Os administradores e os proprietários da aplicação podem atribuir utilizadores e grupos para funções que definem quando registar a sua aplicação no Azure AD.  Informações de função são enviadas para o início de sessão de tokens de aplicação e também podem ser lidos utilizando a API de gráfico.  Saiba mais sobre [utilizar o Azure AD para autorização](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Aceder ao perfil de utilizador, calendário, E-Mail, contactos, ficheiros e muito mais
**Azure AD é o servidor de autorização para o Office 365 e outros serviços empresariais da Microsoft.**  Se tiver suporte do Azure AD para início de sessão na sua aplicação ou o suporte de ligar as contas de utilizador atual para contas de utilizador do Azure AD com OAuth 2.0, pode pedir a leitura e de acesso de escrita a um perfil de utilizador, calendário, e-mail, contactos, ficheiros e outras informações.  Totalmente integrada podem escrever eventos no calendário do utilizador e ler ou escrever ficheiros no respetivo OneDrive.  Saiba mais sobre [aceder as APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promover a sua aplicação no Azure e do Office 365 Marketplaces
**Promova a sua aplicação para milhões de organizações que já estão a utilizar o Azure AD.**  Utilizadores de pesquisa e procure estas marketplaces já estiver a utilizar um ou mais serviços em nuvem, torná-los qualificado clientes do serviço em nuvem.  Saiba mais sobre promover a sua aplicação no [no Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando os utilizadores inscrever-se para a sua aplicação, será apresentado no respetivo painel de acesso do Azure AD e iniciador da aplicação do Office 365.**  Os utilizadores serão capazes de forma rápida e fácil regressar à sua aplicação mais tarde, melhorar o envolvimento do utilizador.  Saiba mais sobre o [painel de acesso do Azure AD](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Proteger a comunicação de serviço de dispositivos e de serviços
**Utilizar o Azure AD para gestão de identidades de serviços e dispositivos reduz o código que precisa de escrever e permite que as TI para gerir o acesso.**  Os serviços e dispositivos, podem obter os tokens do Azure AD com OAuth e utilizar esses tokens para aceder a web APIs.  Utilizar o Azure AD pode evitar escrever código complexo de autenticação.  Uma vez que as identidades dos serviços e dispositivos são armazenadas no Azure AD, IT pode gerir as chaves e a revogação num local em vez de ter de fazê-lo em separado na sua aplicação.

## <a name="benefits-of-integration"></a>Vantagens da integração
Integração com o Azure AD é fornecido com vantagens que não necessitem de escrever código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com a gestão de identidades de empresa
**Ajude a sua aplicação está em conformidade com as políticas de TI.**  As organizações integram os respetivos sistemas de gestão de identidade empresarial com o Azure AD, pelo que, quando uma pessoa deixa uma organização, estes serão automaticamente perder o acesso à aplicação sem necessidade de efetuar passos adicionais de IT.  IT pode gerir quem pode aceder à aplicação e determinar quais as políticas de acesso são necessárias - exemplo multi-factor Authentication - reduzindo a necessidade de escrever código para estar em conformidade com políticas empresariais complexas.  Azure AD fornece aos administradores de com um registo de auditoria de detalhado do que com sessão iniciada para a sua aplicação, por isso, IT pode controlar a utilização.

**Azure AD expande do Active Directory para a nuvem, para que a aplicação pode integrar com o AD.**  Muitas organizações em todo o mundo utilizam o Active Directory como o respetivo principal início de sessão e o sistema de gestão de identidade e requerem as aplicações para trabalhar com o AD.  Integração com o Azure AD integra-se a aplicação do Active Directory.

### <a name="advanced-security-features"></a>Funcionalidades de segurança avançada
**Autenticação multifator.**  O Azure AD fornece nativa autenticação multifator.  Os administradores de TI podem exigir a autenticação multifator para aceder à sua aplicação, para que não tenha a este suporte de código por si.  Saiba mais sobre [multi-factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Início de sessão anómalos de deteção.**  Azure AD processa mais de mil milhões inícios de sessão por dia, durante a utilização de algoritmos do machine learning para detetar atividade suspeita e notificar os administradores de TI de informações sobre problemas possíveis.  Ao apoiar a sessão do Azure AD, a aplicação obtém a vantagem desta proteção. Saiba mais sobre [visualizar o relatório de acesso do Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Acesso condicional.**  Para além de autenticação multifator, os administradores podem exigir ser cumpridas condições específicas, antes dos utilizadores podem iniciar sessão para a aplicação.  As condições que podem ser definidas incluem o intervalo de endereços IP dos dispositivos cliente, a associação aos grupos especificados e o estado do dispositivo que está a ser utilizado para acesso.  Saiba mais sobre [acesso condicional do Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Desenvolvimento fácil
**Protocolos padrão da indústria.**  A Microsoft está empenhada para suportar as normas do sector.  AD do Azure suporta os protocolos de autenticação SAML 2.0, OpenID Connect 1.0, OAuth 2.0 e WS-Federation 1.2.  A Graph API é OData 4.0 em conformidade.  Se a aplicação já suporta os protocolos SAML 2.0 ou o OpenID Connect 1.0 para início de sessão federado, adicionar suporte para o Azure AD pode ser simples.  Saiba mais sobre [do Azure AD suportado protocolos de autenticação](active-directory-authentication-protocols.md).

**Bibliotecas de open source para.**  A Microsoft fornece bibliotecas de open source para totalmente suportada para idiomas populares e de plataformas de desenvolvimento de velocidade.  O código de origem está licenciado sob Apache 2.0 e está livre para bifurcar e contribuir para os projetos.  Saiba mais sobre [bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença em todo o mundo e elevada disponibilidade
**Do Azure AD é implementado nos centros de dados em todo o mundo é gerido e monitorizado cerca de clock.**  Azure AD é o sistema de gestão de identidade para o Microsoft Azure e o Office 365 e é implementado nos centros de 28 dados em todo o mundo.  Dados de diretório são garantidos de serem replicadas para, pelo menos, três centros de dados.  Balanceadores de carga global Certifique-se os utilizadores acedam a cópia mais próxima do Azure AD que contém os seus dados e automaticamente encaminhar novamente pedidos para outros centros de dados se for detetado um problema.

## <a name="next-steps"></a>Passos Seguintes
[Começar a escrever código](active-directory-developers-guide.md#get-started).

[Iniciar sessão dos utilizadores em utilizar o Azure AD](active-directory-authentication-scenarios.md)

