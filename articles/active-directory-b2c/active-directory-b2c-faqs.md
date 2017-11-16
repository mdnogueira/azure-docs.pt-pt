---
title: Perguntas mais frequentes sobre (FAQ) - Azure AD B2C | Microsoft Docs
description: Perguntas mais frequentes sobre o Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 45ae4ab4c832e7537e6ee78c32603734fa64ad86
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>O Azure AD B2C: Perguntas mais frequentes (FAQ) 
Esta página respostas a perguntas mais frequentes sobre o Azure Active Directory (Azure AD) B2C. Manter a verificação de volta para atualizações.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Pode utilizar funcionalidades do Azure AD B2C no meu inquilino do Azure AD existente, com base em empregado?
Azure AD e do Azure AD B2C são ofertas de produto separado e não podem coexistir no mesmo inquilino.  Um inquilino do Azure AD representa uma organização.  Um inquilino do Azure AD B2C representa uma coleção de identidades a serem utilizadas com aplicações das entidades confiadoras.  Com as políticas personalizadas (em pré-visualização pública), o Azure AD B2C podem federar com o Azure AD, permitindo que a autenticação de funcionários numa organização.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Posso utilizar o Azure AD B2C para fornecer início de sessão social (Facebook e Google +) no Office 365?
O Azure AD B2C não pode ser utilizado para autenticar os utilizadores para o Microsoft Office 365.  Azure AD é a solução de gestão de acesso dos empregados às aplicações SaaS da Microsoft e tem funcionalidades foram concebidas para esta finalidade, como o acesso condicional e licenciamento.  O Azure AD B2C fornece uma plataforma de gestão de identidades e acesso para a criação de aplicações móveis e web.  Quando o Azure AD B2C está configurado para federar a um inquilino do Azure AD, o inquilino do Azure AD gere o acesso dos empregados a aplicações que dependem do Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Quais são as contas locais no Azure AD B2C? Como são diferentes das contas profissionais ou escolares no Azure AD?
Num inquilino do Azure AD, os utilizadores que pertencem ao inquilino início de sessão com um endereço de e-mail do formulário `<xyz>@<tenant domain>`.  O `<tenant domain>` é um dos domínios verificados no inquilino ou iniciais `<...>.onmicrosoft.com` domínio. Este tipo de conta é uma conta escolar ou profissional.

No inquilino do Azure AD B2C, a maioria das aplicações pretendem que o utilizador iniciar sessão com qualquer endereço de e-mail arbitrários (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com, ou jim@live.com). Este tipo de conta é uma conta local.  Também é suportada a nomes de utilizador arbitrária como contas locais (por exemplo, João, Bernardo, sarah ou jim). Pode escolher um dos seguintes dois tipos de conta local através da configuração do Azure AD B2C no portal do Azure.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Os fornecedores de identidade sociais suportam agora? Aqueles planeia suportar no futuro?
Suportamos atualmente Facebook, Google +, LinkedIn, Amazon, Twitter (pré-visualização), WeChat (pré-visualização), Weibo (pré-visualização) e QQ (pré-visualização). Iremos adicionar suporte para outros fornecedores de identidade de redes sociais populares com base no pedido do cliente.

O Azure AD B2C tenha também adicionado suporte para [políticas personalizadas](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Estes [políticas personalizadas](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom) permitir que um programador criar as seus próprios política que com qualquer fornecedor de identidade que suporte [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) ou SAML. 

Introdução às políticas personalizadas ao verificar nossos [pacote de arranque de política personalizada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Pode configurar âmbitos para recolher mais informações sobre os consumidores de vários fornecedores de identidade de redes sociais?
Não, mas esta funcionalidade não está no nosso plano. Os âmbitos de predefinido utilizados para o nosso conjunto de fornecedores de identidade sociais suportado são:

* Facebook: e-mail
* Google +: e-mail
* Conta Microsoft: perfil de e-mail openid
* Amazon: perfil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>A minha aplicação tem de ser executado no Azure, para que funcione com o Azure AD B2C?
Não, pode alojar a aplicação em qualquer local (na nuvem ou no local). Tudo o que tem de interagir com o Azure AD B2C é a capacidade de enviar e receber pedidos HTTP em pontos finais acessíveis publicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Posso ter vários inquilinos do Azure AD B2C. Como posso geri-las no portal do Azure?
Antes de o abrir 'Do Azure AD B2C' no menu do lado esquerdo do portal do Azure, tem de mudar para o diretório que pretende gerir.  Comutador diretórios clicando a sua identidade no canto superior direito do portal do Azure, em seguida, escolha um diretório na lista pendente que é apresentada.  Para um passo a passo com imagens, consulte [para navegar até ao Azure AD B2C definições](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar mensagens de correio eletrónico de verificação (o conteúdo e a "de:" campo) enviado pelo Azure AD B2C?
Pode utilizar o [corporativa funcionalidade](../active-directory/customize-branding.md) para personalizar o conteúdo das mensagens de correio eletrónico de verificação. Mais concretamente, podem ser personalizados estes dois elementos do e-mail:

* **Logótipo de faixa**: indicado no inferior direito.
* **Cor de fundo**: indicado no topo.

    ![Captura de ecrã de uma mensagem de verificação personalizado](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

A assinatura de e-mail contém o nome do inquilino do B2C que forneceu quando criou o inquilino do B2C. Pode alterar o nome a utilizar estas instruções:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) como administrador da subscrição.
1. Abra o **do Azure Active Directory** painel.
1. Clique em de **propriedades** separador.
1. Alterar o **nome** campo.
1. Clique em **guardar** na parte superior da página.

Atualmente não é possível alterar o "de:" campo na mensagem de correio eletrónico. Votar em [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) estiver interessado em Personalizar o corpo da mensagem de e-mail de verificação.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar os meus os nomes de utilizador existente, as palavras-passe e perfis da minha base de dados para o Azure AD B2C?
Pode utilizar a Azure AD Graph API ao escrever a sua ferramenta de migração. Consulte o [exemplo Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) para obter mais detalhes.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Que política de palavras-passe é utilizada para contas locais no Azure AD B2C?
A política de palavra-passe do Azure AD B2C para contas locais baseia-se na política para o Azure AD. O Azure AD B2C da inscrição, a inscrição ou início de sessão e palavra-passe repor políticas utiliza a força da palavra-passe "segura" e não expirarem as palavras-passe. Leia o [política de palavras-passe do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obter mais detalhes.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Pode utilizar o Azure AD Connect para migrar as identidades de consumidor que estão armazenadas no meu diretório Active Directory no local ao Azure AD B2C?
Não, do Azure AD Connect não foi concebida para funcionar com o Azure AD B2C. Considere utilizar o [Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) para a migração de utilizador.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Pode abrir a minha aplicação da cópia de segurança do Azure AD B2C páginas numa iFrame?
Não, por motivos de segurança, não não possível abrir as páginas do Azure AD B2C numa iFrame.  O nosso serviço comunica com o browser de modo a proibir iFrames.  A Comunidade de segurança em geral e a especificação do OAUTH2, recomenda-se contra a utilização de iFrames para experiências de identidade devido ao risco de jacking de clique.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C funciona com sistemas CRM como Microsoft Dynamics?
Integração com o Portal do Microsoft Dynamics 365 está disponível.  Consulte [configurar Portal de 365 Dynamics a utilizar o Azure AD B2C para autenticação](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C funcionam com o SharePoint no local 2016 ou anterior?
O Azure AD B2C não destinam-se o SharePoint externo partilhar de parceiro cenário; consulte [do Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) em vez disso.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Posso utilizar Azure AD B2C ou B2B para gerir identidades externas?
Leia este artigo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para saber mais sobre a aplicar as funcionalidades adequadas para os cenários de identidade externas.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Os relatórios e funcionalidades de auditoria do Azure AD B2C fornece? São os mesmos que no Azure AD Premium?
Não, o Azure AD B2C não suporta o mesmo conjunto de relatórios como o Azure AD Premium. No entanto, existem muitas commonalities:

* **Início de sessão relatórios** só estão disponíveis no portal do Azure (Azure Active Directory > atividade > inícios de sessão) e não estão disponíveis através da API de gráfico. Fornecem um registo de cada início de sessão com detalhes reduzidos.
* **Relatórios de auditoria** só estão disponíveis no portal do Azure (Azure Active Directory > atividade > registos de auditoria) e não estão disponíveis através da API de gráfico. Incluem de atividade de administrador, bem como a atividade das aplicações. 
* **Relatórios de utilização** só estão disponíveis através do [API de relatórios de utilização](active-directory-b2c-reference-usage-reporting-api.md) e não estão disponíveis através do portal do Azure. Estes incluem-se ao número de utilizadores, número de inícios de sessão e o volume do MFA. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Posso localizar a IU de páginas servidos pelo Azure AD B2C? Que idiomas são suportados?
Sim!  Leia sobre [personalização de idioma](active-directory-b2c-reference-language-customization.md), que se encontra na pré-visualização pública.  Fornecemos traduções para 36 idiomas e pode substituir qualquer cadeia de acordo com as suas necessidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Posso utilizar o meus próprio URLs nas minhas páginas de inscrição e o início de sessão que são servidas pelo Azure AD B2C? Por exemplo, altere o URL de login.microsoftonline.com para login.contoso.com?
Atualmente não. Esta funcionalidade está no nosso plano. Verificar o seu domínio no **domínios** separador no portal clássico do Azure não concretizar este objetivo.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como eliminar o meu inquilino do Azure AD B2C
Siga estes passos para eliminar o inquilino do Azure AD B2C:

1. Siga estes passos para [navegue para o Azure AD B2C definições](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
1. Navegue para o **aplicações**, **fornecedores de identidade**, e **todas as políticas** e elimine todas as entradas cada um deles.
1. Agora iniciar sessão para o [portal clássico do Azure](https://manage.windowsazure.com/) como administrador da subscrição. (Utilizar o mesmo ou conta profissional ou a mesma conta Microsoft que utilizou para iniciar sessão no Azure.)
1. Navegue para a extensão do Active Directory à esquerda e clique no inquilino do B2C.
1. Clique em de **utilizadores** separador.
1. Selecione cada utilizador, por sua vez (excluir o utilizador de administrador de subscrição tem atualmente sessão iniciada como). Clique em **eliminar** na parte inferior da página e clique em **Sim** quando lhe for pedido.
1. Clique em de **aplicações** separador.
1. Selecione **aplicações minha empresa detém** no **mostrar** campo de lista pendente e clique em Marcar a verificação.
1. Uma aplicação chamar **aplicação de extensões de b2c**. Clique em **eliminar** na parte inferior da página e clique em **Sim** quando lhe for pedido.
1. Navegue novamente para a extensão do Active Directory e selecione o seu inquilino do B2C.
1. Clique em **eliminar** na parte inferior da página. Para concluir o processo, siga as instruções no ecrã.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Pode obter do Azure AD B2C como parte do Enterprise Mobility Suite?
Não, o Azure AD B2C é uma serviço do Azure pay as you go e não faz parte do Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como posso comunicar problemas com o Azure AD B2C?
Consulte [pedidos de suporte de ficheiro para o Azure Active Directory B2C](active-directory-b2c-support.md).
