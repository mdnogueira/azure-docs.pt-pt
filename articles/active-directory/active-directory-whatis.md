---
title: "O que é o Azure Active Directory?"
description: "Utilize o Azure Active Directory para expandir as identidades no local existentes para a nuvem ou para desenvolver aplicações do Azure AD integrado."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.openlocfilehash: b6746afd508832afbd54153851b6f2ae404af147
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-active-directory"></a>O que é o Azure Active Directory?
Azure Active Directory (Azure AD) é Microsoft do multi-inquilino, o serviço de gestão de identidades e diretórios baseados na nuvem. Azure AD combina serviços de diretório de principais, governação de identidades avançada e gestão de acesso de aplicação. Do Azure AD também oferece uma plataforma avançada, baseada em normas que permite aos programadores fornecer controlo de acesso para as respetivas aplicações, com base na política centralizada e regras. 

Para administradores de TI, o Azure AD fornece uma solução económica, fácil de utilizar para dar aos funcionários e parceiros de negócios acesso início de sessão único (SSO) para [milhares de nuvem aplicações SaaS](active-directory-saas-tutorial-list.md) como o Office 365, em Salesforce.com, DropBox e Concur.

Para programadores de aplicações permite ao Azure AD lhe concentrar-se na criação da aplicação, tornando rápida e simples integrar com uma solução de identidade classe do mundo gestão utilizada pelo milhões de organizações em todo o mundo.

Azure AD também inclui um conjunto completo de capacidades de gestão de identidade, incluindo autenticação multifator, registo de dispositivos, gestão de palavra-passe self-service, gestão de grupos self-service, gestão de contas com privilégios, controlo de acesso baseado em funções , monitorização de utilização de aplicações, de auditoria avançada e monitorização de segurança e alertas. Estas capacidades podem ajudar a aplicações de nuvem segura, com base, simplificar processos de TI, cortar os custos e ajudar a garantir que são cumpridos os objetivos de conformidade empresarial.

Além disso, com apenas [quatro cliques](./connect/active-directory-aadconnect-get-started-express.md), do Azure AD pode ser integrado com um Windows Server Active Directory existente, as organizações a dar a capacidade para tirar partido existente no local investimentos de identidade para gerir o acesso ao aplicações SaaS baseados na nuvem.

Se for um cliente do Office 365, Azure ou do Dynamics CRM Online, se pode não ter apercebido que já estão a utilizar Azure AD. Cada inquilino do Office 365, Azure e Dynamics CRM realmente já é um inquilino do Azure AD. Sempre que pretender que começar a utilizar esse inquilino para gerir o acesso a milhares de outras aplicações em nuvem do Azure AD integra-se com!

![Pilha do Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Como fiável é o Azure AD?
A estrutura de multi-inquilino, geo-distribuição, de elevada disponibilidade do Azure AD significa que pode confiar na mesma para as suas necessidades de negócio mais importantes. A ficar sem centros de dados de 28 em todo o mundo com ativação pós-falha automática, terá de comfort de saber que do Azure AD é altamente fiável e que, mesmo se um centro de dados fica inativo, cópias dos seus dados do diretório estão em direto em, pelo menos, duas mais regionally dispersos dados os centros e disponíveis para acesso instantânea.

Para obter mais detalhes, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Escolher uma edição
Todos os serviços empresariais do Microsoft Online baseiam-se no Azure Active Directory (Azure AD) para início de sessão e outra identidade necessidades. Se subscrever qualquer um dos serviços de empresas do Microsoft Online (por exemplo, o Office 365 ou o Microsoft Azure), obtenha do Azure AD com acesso a todas as funcionalidades livres. Com a edição gratuito do Azure Active Directory, pode gerir utilizadores e grupos, sincronize com diretórios no local, obter o início de sessão único através do Azure, Office 365 e milhares de aplicações SaaS populares, como o Salesforce Workday, Concur, DocuSign, Google As aplicações, caixa, ServiceNow, Dropbox e muito mais. 

Para melhorar o Azure Active Directory, pode adicionar capacidades pagas utilizando as edições básico do Azure Active Directory, Premium P1 e Premium P2. Azure Active Directory paga edições assentes livre diretório existente, fornecer enterprise capacidades de classe expansão avançada self-service, monitorização, segurança de relatórios, multi-factor Authentication (MFA) e acesso seguro para o força de trabalho móvel.

> [!NOTE]
> Para as opções de preços destas edições, consulte [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 e básico do Azure Active Directory não são atualmente suportadas na China. Contacte-no fórum do Azure de diretório Active Directory para obter mais informações.
>

* **Azure Active Directory básico** -concebido para trabalhadores de tarefas com as necessidades de nuvem primeiro, nesta edição fornece nuvem aplicação centrado em Self-Service e de acesso identidade soluções de gestão. Com a edição Básica do Azure Active Directory, beneficia de funcionalidades de otimização de produtividade e de redução de custos, como gestão de acesso baseado em grupos, reposição de palavras-passe self-service para aplicações na cloud e o Proxy de Aplicações do Azure Active Directory (para publicar aplicações Web no local com o Azure Active Directory), com o apoio de um SLA de nível empresarial de 99,9% de tempo de atividade.
* **Azure Active Directory Premium P1** - concebida para organizações com pedir o seu trabalho mais de capacitar tem de gestão de identidades e acessos, edição do Azure Active Directory Premium adiciona funcionalidades de gestão de identidade de nível empresarial avançada em termos de funcionalidade e permite aos utilizadores híbrida totalmente integrada aceder no local e as capacidades de nuvem. Esta edição inclui tudo aquilo de que precisa para os técnicos de informações e os administradores de identidade em ambientes híbridos, desde acesso de aplicações, gestão de identidades e acesso (IAM) self-service, proteção de identidades a segurança na cloud. Suporta recursos de administração e a delegação avançados, como grupos dinâmicos e de gestão de grupos self-service. Inclui o Microsoft Identity Manager (um local identidades e acessos management suite) e fornece capacidades de repetição de escrita ativar soluções como self-service reposição palavra-passe para os seus utilizadores no local de nuvem.
* **Azure Active Directory Premium P2** -concebidas com proteção avançada para todos os seus utilizadores e administradores, esta nova oferta inclui todas as capacidades no Azure AD Premium P1, bem como a nossa nova Identity Protection e Privileged Identity Gestão. Azure Active Directory Identity Protection tira partido billions dos sinais para fornecer acesso condicional baseado em risco aos seus dados críticos da empresa e aplicações. Podemos também ajuda a gerir e proteger contas privilegiadas com o Azure Active Directory Privileged Identity Management, pelo que pode detetar, restringir e monitorizar os administradores e o respetivo acesso a recursos e fornecem acesso just-in-time quando necessário.  

> [!NOTE]
> Um número de capacidades do Azure Active Directory está disponível nas várias edições de "pay as you go":
>
> * Active Directory B2C é a solução de gestão de identidades e acessos para as suas aplicações direcionadas para o consumidor. Para obter mais detalhes, consulte [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Multi-factor Authentication do Azure pode ser utilizada através por utilizador ou por fornecedores de autenticação. Para obter mais detalhes, consulte [que é o Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Como pode começar a utilizar?

**Se for um administrador de TI:**

* [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) -Pode iniciar sessão para uma gratuita de 30 dias avaliação hoje e implementar a sua primeira solução em nuvem em 5 minutos, utilizando a seguinte hiperligação

* Leitura [introdução ao Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) para truques sobre a obtenção de um Azure AD e sugestões de inquilino e em execução rápida

**Se for um programador:**
 
* Consulte a nossa [guia para programadores](active-directory-developers-guide.md) ao Azure Active Directory

* [Iniciar uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/) – iniciar sessão para uma gratuita de 30 dias avaliação hoje e começar a integrar as suas aplicações com o Azure AD

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre as noções básicas sobre a gestão de identidades e acessos do Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
