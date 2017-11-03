---
title: "Gestão de aplicações com o Azure Active Directory | Microsoft Docs"
description: "Este artigo as vantagens de integrar o Azure Active Directory no local, a nuvem e de aplicações SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 95b96f10-2d5c-4b78-8af8-d3657a24140f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: ff3e3284f34f8373a2cfd80438d94a7052bb7f5e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="managing-applications-with-azure-active-directory"></a>Gestão de aplicações com o Azure Active Directory
Para além do fluxo de trabalho real ou conteúdo, as empresas têm dois requisitos básicos para todas as aplicações:

1. Para aumentar a produtividade, as aplicações devem ser fáceis de detetar e aceder
2. Para ativar a governação e de segurança, a organização necessita e supervisão em quem pode e, na verdade, está a aceder a cada aplicação

No mundo de aplicações em nuvem isto melhor ser possível utilizar a identidade para o controlo de "*quem tem permissão para efetuar o*".

Na terminologia de computação:

* *Quem* é conhecido como *identidade* -a gestão de utilizadores e grupos
* *O que* é conhecido como *aceder à gestão* – a gestão de acesso a recursos protegidos

Ambos os componentes em conjunto são conhecidos como *identidade e gestão de acesso (IAM)*, que é definido pelo [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) grupo como "*da disciplina de segurança que permite que indivíduos corretos aceder aos recursos à direita na direita horas para os motivos pelos quais direita*".

Há problema, por isso, o que é o problema? Se for IAM *não gerido* num local com uma solução integrada:

* Os administradores de identidade têm individualmente criar e atualizar as contas de utilizador em todas as aplicações em separado, uma atividade redundante e demorada.
* Os utilizadores têm memorize várias credenciais para aceder às aplicações que precisam para trabalhar com. Como resultado, os utilizadores tendem a anote as palavras-passe ou utilizar outras soluções de gestão de palavra-passe que introduz outros riscos de segurança de dados.
* Redundante atividades demoradas reduzir a quantidade de tempo que os utilizadores e os administradores estão a funcionar em atividades de negócio que aumentam a linha de parte inferior da sua empresa.

Por isso, o que, geralmente, impede que as organizações de adotar integradas soluções IAM?

* As soluções mais técnicas baseiam-se em plataformas de software que têm de ser implementados e adaptada descritos por cada organização para as suas próprias aplicações.
* Aplicações em nuvem adotado uma larga maioria são, muitas vezes, uma taxa superior a organização de TI pode integrar com soluções IAM existentes.
* Ferramentas de monitorização e de segurança requerem personalização adicional e a integração para alcançar os cenários de E2E abrangentes.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory integrado em aplicações
Azure Active Directory é identidade da Microsoft como uma solução de serviço (IDaaS) que:

* Permite IAM como um serviço em nuvem 
* Fornece gestão de acesso central, início de sessão único (SSO) e relatórios 
* Suporta a gestão de acesso integrada para [milhares de aplicações](https://azure.microsoft.com/marketplace/active-directory/) na Galeria de aplicações, incluindo o Salesforce, Google Apps, caixa, Concur e muito mais. 

Com o Azure Active Directory, todas as aplicações publicar para os parceiros e clientes (ou empresariais consumidor) possui a mesma identidade capacidades de gestão de acesso.<br> Isto permite-lhe reduzir significativamente os custos operacionais.

E se necessita de implementar uma aplicação que ainda não está listada na Galeria de aplicações? Apesar de ser um pouco mais morosa ao configurar o SSO para aplicações a partir da Galeria de aplicações, o Azure AD fornece um assistente que o ajuda a com a configuração.

O valor do Azure AD vai mais além "apenas" as aplicações em nuvem. Também pode utilizá-lo com as aplicações no local ao fornecer o acesso remoto seguro. Com o acesso remoto seguro, pode eliminar a necessidade de VPNs ou outras implementações de gestão de acesso remoto tradicional.

Ao fornecer gestão de acesso central e de início de sessão único (SSO) para todas as suas aplicações, o Azure AD fornece a solução para os problemas de produtividade e segurança de dados principal.

* Os utilizadores podem aceder a várias aplicações com um início de sessão dar mais tempo para receitas de atividades de operações de negócio ou gerar causadas.
* Os administradores de identidade podem gerir o acesso às aplicações num único local.

A vantagem do utilizador e para a sua empresa é óbvias. Vamos um olhar as vantagens de um administrador de identidade e a organização.

## <a name="integrated-application-benefits"></a>Vantagens de aplicação integrada
O processo SSO tem dois passos:

* Autenticação, o processo de validação a identidade do utilizador.
* Autorização, a decisão de permitir ou bloquear o acesso a um recurso com uma política de acesso.

Quando utilizar o Azure AD para gerir aplicações e ativar a SSO:

* A autenticação é efetuada no local (por exemplo, AD) o utilizador ou a conta do Azure AD.
* Autorização executa do Azure AD proteção e de atribuição de política de garantir que a experiência de utilizador final consistente e que permite adicionar condições MFA, atribuição e localizações em qualquer aplicação, independentemente das respetivas capacidades de internas.

Importante compreender que a forma como a autorização é enacted na aplicação de destino que varia consoante a forma como a aplicação foi integrada com o Azure AD.

* **Aplicações previamente integradas pelo fornecedor de serviço** como o Office 365 e o Azure, estes são criados diretamente no Azure AD e depender para as respetivas capacidades de gestão de identidades e acessos abrangentes de aplicações. Acesso a estas aplicações é ativado através da emissão de tokens e informações de diretório.
* **Aplicações previamente integradas pela Microsoft e aplicações personalizadas** são independentes da nuvem aplicações que dependem de um diretório de aplicação interna e podem funcionar independentemente do Azure AD. Acesso a estas aplicações é ativado através da emissão de uma credencial de específico de aplicação mapeada para uma conta de aplicação. Consoante as capacidades de aplicação, a credencial pode ser um token de Federação ou nome de utilizador e palavra-passe para uma conta que foi aprovisionada na aplicação.
* **Aplicações no local** as aplicações publicadas através do proxy de aplicações do Azure AD principalmente permitir o acesso a aplicações no local. Estas aplicações baseiam-se no diretório central no local, como o Windows Server Active Directory. Acesso a estas aplicações está ativado por ter acionado o proxy para entregar o conteúdo da aplicação para o utilizador final para respeitar o requisito de início de sessão no local.

Por exemplo, se um utilizador associa a sua organização, terá de criar uma conta para o utilizador no Azure AD para as operações de início de sessão primários. Se este utilizador necessita de acesso a uma aplicação gerida, tais como o Salesforce, também terá de criar uma conta para este utilizador no Salesforce e ligá-lo para a conta do Azure para tornar o SSO funcione. Quando o utilizador deixa a sua organização, é recomendado para eliminar a conta do Azure AD e todas as contas de homólogo o IAM armazena as aplicações que o utilizador tinha acesso.

## <a name="access-detection"></a>Deteção de acesso
Moderna empresas, departamentos de TI, normalmente, não têm conhecimento de todas as aplicações em nuvem que estão a ser utilizadas. Em conjunto com a Cloud App Discovery, do Azure AD fornece uma solução para detetar estas aplicações.

## <a name="account-management"></a>Gestão de contas
Tradicionalmente, gerir contas de várias aplicações é um processo manual realizado IT ou suporta pessoais na organização. Do Azure AD totalmente automatizado gestão de contas em todas as aplicações de fornecedor integrado de serviço e as aplicações previamente integradas pela Microsoft que suportam o aprovisionamento automatizado do utilizador ou SAML JIT.

## <a name="automated-user-provisioning"></a>O aprovisionamento automatizado do utilizador
Algumas aplicações fornecem interfaces de automatização para criação e remoção (ou desativação) de contas. Se um fornecedor de oferecer dessa interface, é utilizado pelo Azure AD. Esta reduz os custos operacionais porque as tarefas administrativas ocorrem automaticamente e melhora a segurança do seu ambiente, porque o diminui a probabilidade de acesso não autorizado.

## <a name="access-management"></a>Gestão de acesso
Utilizar o Azure AD pode gerir o acesso a aplicações utilizando individuais ou regra orientadas por atribuições. Também pode delegar o acesso de gestão para as pessoas corretas na organização de garantir o melhor supervisão e reduzir a carga sobre o suporte técnico.

## <a name="on-premises-applications"></a>Aplicações no local
O incorporado na aplicação o proxy permite-lhe publicar as suas aplicações no local para os seus utilizadores, resultando em ambos consistente aceder à experiência com a aplicação em nuvem modernas e as vantagens de capacidades de monitorização, relatórios e segurança do Azure AD.

## <a name="reporting-and-monitoring"></a>Monitorização e relatórios
Azure AD fornece-lhe reporting previamente integradas e monitorização capacidades que permitem-lhe saber quem tem acesso a aplicações e quando, na verdade, a ser utilizadas-los.

## <a name="related-capabilities"></a>Funcionalidades relacionadas
Com o Azure AD pode proteger as suas aplicações com políticas de acesso granular e MFA previamente integrada. Para saber mais sobre MFA do Azure, consulte [MFA do Azure](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Introdução
Para começar a integrar aplicações com o Azure AD, observe o [guia de introdução de integrar o Azure Active Directory com aplicações obter](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Consultar também
[Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

