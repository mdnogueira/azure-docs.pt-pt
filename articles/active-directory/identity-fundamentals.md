---
title: "Noções básicas sobre a gestão de identidades do Azure | Microsoft Docs"
description: "Identidades baseado na nuvem estão agora a melhor forma de manter o controlo sobre e visibilidade, como e quando os utilizadores aceder a aplicações e dados empresariais."
keywords: 
author: jeffgilb
manager: femila
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 52f05ee8a5c07fc008da40aef12d1ad8e8136429
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="fundamentals-of-azure-identity-management"></a>Noções básicas sobre a gestão de identidades do Azure
Como mais recursos digitais da empresa em direto fora da rede empresarial na nuvem e em dispositivos, uma excelente baseado na nuvem identidades e acessos solução de gestão está a tornar-se uma necessidade. Identidades baseado na nuvem estão agora a melhor forma de manter o controlo sobre e visibilidade, como e quando os utilizadores aceder a aplicações e dados empresariais.

Microsoft tem sido proteger identidades baseado na nuvem para através de um decade e agora, com [do Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), estes sistemas de proteção mesmo estão disponíveis para si. Com o Azure AD, administradores da empresa podem facilmente Certifique-se de utilizador e administrador accountability com maior segurança e governação que anteriormente.

O Azure AD Premium é um baseado na nuvem identidades e acessos solução de gestão com capacidades de proteção avançada que permite uma identidade segura para todas as aplicações, proteção de identidade (avançada pelo [gráfico de segurança do Microsoft intelligence](https://www.microsoft.com/en-us/security/intelligence)) e Privileged Identity Management. Apenas outra monitorização ou a ferramenta, de relatórios do Azure AD Premium pode proteger as identidades dos utilizadores em tempo real e permitem-lhe criar políticas de acesso baseado em risco, adaptável para proteger dados da sua organização.

Ver este breve vídeo para uma descrição geral da proteção e gestão de identidades do Azure AD:
<iframe width="560" height="315" src="https://www.youtube.com/embed/9LGIJ2-FKIM" frameborder="0" allowfullscreen></iframe>

A Microsoft fornece não apenas uma identidade que o leva everywhere, mas também um conjunto de ferramentas para automatizar, ajudar a proteger e gerir IT na sua organização. Mesmo após a introdução de informática em nuvem, existe ainda está a pedido para gerir e controlar tarefas como chamadas de suporte técnico para o utilizador de reposição de palavras-passe, gestão de grupo de utilizadores e pedidos de aplicações. Complicating coisas ainda mais, os funcionários agora colocarem os seus dispositivos pessoais para o trabalho e a utilização de aplicações de SaaS prontamente disponíveis. Isto torna a manutenção controlo sobre as aplicações em centros de dados empresariais e de plataformas na nuvem pública um significativo desafio.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Ligar o Active Directory no local com o Azure AD e do Office 365
As organizações que tem efetuado investimentos grandes no Active Directory no local podem expandir os seus investimentos para a nuvem ao integrar os seus diretórios no local com o Azure AD para [gestão de identidade híbrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Se o fizer, torna os utilizadores mais produtivos, ao fornecer uma identidade comum para acederem a recursos, independentemente da localização. Em seguida, utilizadores e organizações podem utilizar início de sessão único (SSO) para acesso ambos os recursos no local e na cloud services como o Office 365.

[O Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) é a única ferramenta tem de obter a integração concluída. O Azure AD Connect fornece capacidades para suportar as suas necessidades de sincronização de identidade e substitui as versões anteriores das ferramentas de integração de identidade, tais como o DirSync e Azure AD Sync. Com o Azure AD Connect, a gestão de identidades e a sincronização entre no local e o Azure AD é ativada através da:

- Sincronização – este componente é responsável pela criação de utilizadores, grupos e outros objetos. Também é responsável por verificar se as informações de identidade dos seus utilizadores e grupos no local têm correspondência na nuvem. Também pode ser ativada a repetição de escrita de palavras-passe para manter os diretórios no local em sincronização quando um utilizador atualiza a palavra-passe no Azure AD.
- AD FS – a Federação tem uma capacidade opcional fornecida pelo Azure AD Connect, que pode ser utilizado para configurar um ambiente híbrido utilizando um local infraestrutura do AD FS. A Federação pode ser utilizada por organizações para lidarem com implementações complexas, tais como início de sessão único, a imposição de AD início de sessão política e de smart card ou de terceiros MFA.
- Monitorização de estado de funcionamento - [do Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) pode proporcionar uma monitorização robusta e uma localização central no portal do Azure para visualizar esta atividade.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Aumentar a produtividade e reduzir os custos de suporte técnico com self-service e único início de sessão experiências

Os empregados são mais produtivos quando têm um único nome de utilizador e palavra-passe lembrar-se e uma experiência consistente de cada dispositivo. Também guardam tempo quando podem executar tarefas de self-service, como [repor uma palavra-passe esquecida](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) ou pedir acesso a uma aplicação sem aguardar assistência a partir do suporte técnico.

Azure AD [expande o Active Directory no local](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) para a nuvem, permitindo que os utilizadores utilizar a respetiva conta organizacional principal para os respetivos dispositivos associados a um domínio, recursos da empresa e todos os web e aplicações de SaaS têm de utilizar para obter as respetivas tarefas causadas. Para além de não terem de se lembrar vários conjuntos de nomes de utilizador e palavras-passe, à aplicação o acesso dos utilizadores pode também ser automaticamente aprovisionado (ou anular aprovisionado) com base nas respetivas associações a organização e o respetivo estado como um empregado. E podem controlar o que o acesso para aplicações de galeria ou para as suas aplicações no local que tenha desenvolvidas e publicadas através do [Proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Gerir e controlar o acesso aos recursos da empresa
Microsoft identidades e acessos soluções ajuda da gestão de IT proteger o acesso a aplicações e recursos em todo o datacenter empresarial e para a nuvem, ativar níveis adicionais de validação, tais como [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) e [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Monitorização de atividade suspeita através de segurança avançada, auditoria e relatórios de alerta ajuda a mitigar potenciais problemas de segurança.

Políticas de acesso condicional no Azure AD Premium dão-lhe, o administrador de empresa, a capacidade de criar regras de acesso baseado em política para qualquer aplicação do Azure AD-ligado (aplicações SaaS, as aplicações personalizadas em execução nas aplicações web na nuvem ou no local). Azure AD avalia estas políticas em tempo real e impõe-las sempre que um utilizador tenta aceder a uma aplicação. Políticas de proteção de identidade do Azure permitem-lhe automaticamente ação se for detetada atividade suspeita. Estas ações podem incluir a bloquear o acesso a utilizadores em risco elevado, impor a autenticação multifator e repor palavras-passe do utilizador, se parece tem sido comprometidas credenciais.


## <a name="azure-active-directory-privileged-identity-management"></a>Gestão de Identidades Privilegiadas do Azure Active Directory

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), incluído com a oferta do Azure Active Directory Premium P2, permite-lhe detetar, restringir e monitorizar as contas administrativas e respetivo acesso a recursos no seu Azure Active Directory e outros serviços online da Microsoft. Também o ajuda a administrar o acesso administrativo a pedido para o período de tempo que tem de exato.

Privileged Identity Management pode impor direitos de administrador a pedido para que os administradores podem pedir multifator autenticada, temporária elevação dos seus privilégios para a pré-configurados períodos de tempo antes das respetivas contas regressar a um Estado de utilizador normal.

## <a name="benefits-of-azure-identity"></a>Vantagens da identidade do Azure

Com a gestão de identidade do Azure, pode:

-   Criar e gerir uma identidade única para cada utilizador em toda a empresa completa, ao manter os utilizadores, grupos e dispositivos sincronizadas com [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Fornecer acesso de início de sessão único para as aplicações, incluindo milhares de aplicações de SaaS previamente integradas ou fornecer acesso remoto seguro a aplicações de SaaS no local utilizando o [Proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Ativar segurança de acesso da aplicação através da imposição de baseada em regras [multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) para no local e nuvem aplicações.

-   Melhorar a produtividade do utilizador com [reposição de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)e grupo e a aplicação aceder pedidos utilizando o [MyApps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Tirar partido do [elevada disponibilidade e fiabilidade](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) de um nível de empresarial em todo o mundo, baseado na nuvem identidades e acessos solução de gestão.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre as soluções de identidade do Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)