---
title: "Configurar o SSO do Azure AD para aplicações | Microsoft Docs"
description: "Saiba como ligar aplicações para o Azure Active Directory utilizando SAML e SSO baseada em palavra-passe para Self-Service"
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.reviewer: luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9049f526243cb4659aaf86b3d31146abe8f5f3ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory
Este artigo é sobre uma funcionalidade que permite aos administradores configurar início de sessão único para aplicações não estão presentes na Galeria de aplicações do Azure Active Directory *sem escrever código*. Esta funcionalidade foi lançada a partir do technical preview no 18 de Novembro de 2015 e está incluída no [Azure Active Directory Premium](active-directory-editions.md). Se estiver em vez disso, a procurar orientações para programadores sobre como integrar aplicações personalizadas com o Azure AD através do código, consulte o artigo [cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).

Galeria de aplicações do Azure Active Directory fornece uma lista de aplicações que se sabe suportarem um formulário de início de sessão no Azure Active Directory, conforme descrito em [neste artigo](active-directory-appssoaccess-whatis.md). Uma vez (como um IT especialista em integrador na sua organização) encontrar a aplicação que pretende ligar, pode começar a utilizar, siga as instruções passo a passo apresentadas no portal de gestão do Azure para ativar o início de sessão único.

Clientes com [Azure Active Directory Premium](active-directory-editions.md) licenças também obterem estas capacidades adicionais:

* Integração de self-service de qualquer aplicação que suporta SAML 2.0 fornecedores de identidade (iniciado por SP ou iniciadas por IdP)
* Integração de self-service de qualquer aplicação web que tem um baseado em HTML página de início de sessão utilizando [SSO baseada em palavra-passe](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Ligação self-service das aplicações que utilizam o protocolo SCIM para aprovisionamento de utilizadores ([descrito aqui](active-directory-scim-provisioning.md))
* Capacidade de adicionar ligações a qualquer aplicação no [iniciador da aplicação do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou [painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Isto pode incluir não apenas aplicações de SaaS que utilizar, mas tem não ainda foi integrada para a Galeria de aplicações do Azure AD, mas as aplicações web de terceiros que tenha implementado organização servidores que controlar, ou na nuvem ou no local.

Estas capacidades, também conhecidas como *modelos de integração de aplicação*, forneça pontos de ligação baseada em normas para aplicações que suportam SAML, SCIM ou autenticação baseada em formulários e incluem opções flexíveis e as definições de compatibilidade com um número abrangente de aplicações. 

## <a name="adding-an-unlisted-application"></a>Adicionar uma aplicação não listada
Para ligar uma aplicação utilizando um modelo de integração de aplicações, inicie sessão no portal de gestão do Azure com a conta de administrador do Azure Active Directory e navegue para o **do Active Directory > [Directory] > aplicações** secção, selecione **adicionar**e, em seguida, **adicionar uma aplicação na galeria do**. 

![][1]

Na Galeria de aplicações, pode adicionar uma aplicação não listada, utilizando o **personalizada** categoria no lado esquerdo ou selecionando o **adicionar uma aplicação não listada** ligação que é apresentada nos resultados da pesquisa se a aplicação pretendida não foi encontrada. Depois de introduzir um nome para a sua aplicação, pode configurar o comportamento e as opções de início de sessão único. 

**Sugestão rápida**: como uma melhor prática, utilize a função de pesquisa para verificar se a aplicação já existe na Galeria de aplicações. Se a aplicação é localizada e a respetiva descrição menciona suportadas "início de sessão único", em seguida, a aplicação já é suportada para federado-início de sessão único. 

![][2]

Adicionar uma aplicação desta forma fornece uma experiência muito semelhante às disponíveis para aplicações previamente integradas. Para começar, selecione **configurar Single Sign-On**. O ecrã seguinte apresenta as três opções seguintes para configurar o início de sessão único, que são descritas nas secções seguintes.

![][3]

## <a name="azure-ad-single-sign-on"></a>Azure AD início de sessão único
Selecione esta opção para configurar a autenticação baseada em SAML para a aplicação. Isto requer que o suporte da aplicação SAML 2.0 e deve recolher informações sobre como utilizar as capacidades SAML da aplicação antes de continuar. Depois de selecionar **seguinte**, será solicitado que introduza três diferentes URLs correspondente para os pontos finais SAML para a aplicação. 

![][4]

Nomeadamente:

* **URL de início de sessão (iniciado por SP apenas)** – onde o utilizador passa para início de sessão para esta aplicação. Se a aplicação estiver configurada para efetuar serviço iniciada pelo fornecedor de sessão único, em seguida, quando um utilizador navega para este URL, o fornecedor de serviço irá efetuar o redirecionamento necessário para o Azure AD para autenticar e iniciar sessão do utilizador no. Se este campo é preenchido, Azure AD irá utilizar este URL para iniciar a aplicação do Office 365 e o painel de acesso do Azure AD. Se este campo é ommited, em seguida, do Azure AD em vez disso, executará o fornecedor de identidade-início de sessão iniciada quando a aplicação é iniciada a partir do Office 365, o painel de acesso do Azure AD, ou do Azure AD único URL sign-on (copiable a partir do separador Dashboard).
* **URL do emissor** -o URL do emissor deve identificar exclusivamente a aplicação para o qual único início de sessão está a ser configurado. Este é o valor do Azure AD envia a aplicação como o **público-alvo** parâmetro do SAML token e a aplicação deverá validá-lo. Este valor é também apresentado como o **ID de entidade** nos metadados qualquer SAML fornecido pela aplicação. Consulte a documentação de SAML da aplicação para obter detalhes sobre o que é o ID de entidade ou valor de audiência está. Abaixo está um exemplo de como o URL público-alvo é apresentado no token SAML devolvido para a aplicação:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL de resposta** -o URL de resposta é onde a aplicação de espera receber o token SAML. Isto também é referido como o **URL do serviço de consumidor da asserção (ACS)**. Consulte a documentação de SAML da aplicação para obter detalhes sobre a resposta de token SAML URL ou URL do ACS.
  Depois de estes serem introduzidas, clique em **seguinte** prossiga para o ecrã seguinte. Este ecrã fornece informações sobre o que tem de ser configurado no lado de aplicação para ativá-la aceitar um token SAML do Azure AD. 

![][5]

Os valores que são necessários variam consoante a aplicação, por isso, consulte a documentação de SAML da aplicação para obter mais detalhes. O **Sign-On** e **fim de sessão** ambos de resolver para o mesmo ponto de final, o que é o ponto final de processamento de pedidos SAML para a instância do Azure AD de URL de serviço. O URL do emissor é o valor que é apresentado como "Issuer" dentro do token SAML emitido para a aplicação. 

Depois da aplicação tiver sido configurada, clique em **seguinte** botão e, em seguida, o **concluída** para fechar a caixa de diálogo. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Atribuir utilizadores e grupos a sua aplicação de SAML
Assim que a sua aplicação tiver sido configurada para utilizar o Azure AD como um fornecedor de identidade baseada em SAML, está quase pronto para testar. Como um controlo de segurança do Azure AD não irá emitir um token, permitindo-lhes iniciar sessão na aplicação, a menos que tenham sido concedidos acesso através do Azure AD. Podem ser concedido acesso a utilizadores diretamente ou através de um grupo que são membros do. 

Para atribuir um utilizador ou grupo à sua aplicação, clique o **atribuir utilizadores** botão. Selecione o utilizador ou grupo que pretende atribuir e, em seguida, selecione o **atribuir** botão. 

![][6]

Atribuição de um utilizador irá permitir que o Azure AD para emitir um token para o utilizador, bem como fazendo com que um mosaico para esta aplicação a aparecer no painel de acesso do utilizador. Um mosaico de aplicações também será apresentado o iniciador da aplicação do Office 365 se o utilizador estiver a utilizar o Office 365. 

Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão no **configurar** separador para a aplicação. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>Personalizar afirmações emitidas no SAML token
Quando um utilizador efetua a autenticação para a aplicação, do Azure AD irá emitir um token SAML para a aplicação que contém informações (ou afirmações) sobre o utilizador que identifica exclusivamente. Por predefinição inclui o nome de utilizador, endereço de correio eletrónico, nome próprio e apelido do utilizador. 

Pode ver ou editar as afirmações enviadas no token SAML para a aplicação a **atributos** separador. 

![][7]

Existem duas razões possíveis por que razão poderá ser necessário editar as afirmações emitidas no SAML token: •The aplicação foi escrita para exigir um conjunto diferente de URIs de afirmação ou afirmação de valores de afirmação •Your aplicação foi implementada de forma que requer o NameIdentifier ser algo que o nome de utilizador (também conhecidas como nome principal do utilizador) armazenado no Azure Active Directory. 

Para obter informações sobre como adicionar e editar afirmações nestes cenários, veja isto [artigo personalização afirmações](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>Testar a aplicação de SAML
Depois dos URLs de SAML e o certificado tiverem sido configurados no Azure AD e na aplicação, utilizadores ou grupos foram atribuídos para a aplicação no Azure e as afirmações tenham sido revistas e editá-lo se for necessário, em seguida, o utilizador está pronto para iniciar sessão na aplicação. 

Para testar, basta iniciar sessão no painel de acesso do Azure AD em https://myapps.microsoft.com utilizando uma conta de utilizador atribuída à aplicação, e, em seguida, clique no mosaico para a aplicação para iniciar o processo de início de sessão único. Em alternativa, pode procurar diretamente para o URL de início de sessão da aplicação e início de sessão a partir daí. 

Para sugestões de depuração, consulte este [artigo sobre como depurar baseados em SAML-início de sessão único para aplicações](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Palavra-passe-início de sessão único
Selecione esta opção para configurar [baseada em palavra-passe de início de sessão](active-directory-appssoaccess-whatis.md) para uma aplicação web que tem uma página de início de sessão HTML. Baseado em palavra-passe SSO, também referido como palavra-passe vaulting, permite-lhe gerir o acesso de utilizador e palavras-passe de aplicações web que não suporta a Federação de identidade. Também é útil para cenários em que várias os utilizadores precisam de partilhar uma única conta, tal como para contas de aplicação da sua organização de redes sociais. 

Depois de selecionar **seguinte**, será solicitado para introduzir o URL da baseada na web início de sessão na página da aplicação. Tenha em atenção que tem de ser a página que inclua os campos de entrada de nome de utilizador e palavra-passe. Uma vez introduzidas, do Azure AD inicia um processo para analisar a página de início de sessão para introduzir um nome de utilizador e uma palavra-passe de entrada. Se o processo não for bem sucedido, em seguida,-orienta-o num processo de instalação de uma extensão de browser (requer o Internet Explorer, Chrome ou Firefox) que irá permitir-lhe capturar manualmente os campos alternativo.

Depois da página de início de sessão é capturada, podem ser atribuídos a utilizadores e grupos e políticas de credencial podem ser definidas como regular [palavra-passe SSO aplicações](active-directory-appssoaccess-whatis.md).

Nota: Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão no **configurar** separador para a aplicação. 

## <a name="existing-single-sign-on"></a>Existente Single Sign-On
Selecione esta opção para adicionar uma ligação para uma aplicação no portal de painel de acesso do Azure AD ou o Office 365 da sua organização. Pode utilizar este para adicionar hiperligações para aplicações web personalizado que a utilizam atualmente os serviços de Federação do Active Directory do Azure (ou outro serviço de Federação) em vez do Azure AD para autenticação. Em alternativa, pode adicionar ligações avançadas para páginas do SharePoint específicas ou de outras páginas web que pretende aparecem no painel de acesso do utilizador. 

Depois de selecionar **seguinte**, será solicitado para introduzir o URL da aplicação para ligar a. Depois de concluída, utilizadores e grupos que podem ser atribuídos a aplicação, o que faz com que a aplicação a aparecer no [iniciador da aplicação do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou [painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) para os utilizadores.

Nota: Pode carregar um logótipo de mosaico para a aplicação utilizando o **carregar o logótipo** botão no **configurar** separador para a aplicação.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Como personalizar afirmações emitidas no Token SAML para aplicações previamente integradas](active-directory-saml-claims-customization.md)
* [Resolução de problemas baseados em SAML Single Sign-On](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
