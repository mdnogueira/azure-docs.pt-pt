---
title: "Novidades Notas de versão para o Azure Active Directory | Microsoft Docs"
description: "Saiba o que há de novo no Azure Active Directory (Azure AD), incluindo notas de versão mais recentes, problemas, correções de erros, funcionalidade preterida e as alterações futuras conhecidos."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a44faec6c21c338dfc6b1507af6f716e089c7038
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="whats-new-in-azure-active-directory"></a>O que é novo no Azure Active Directory?




> Se manter atualizado em relação às quais são as novidades no Azure Active Directory ao subscrever este [feed](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) no seu favorito leitor de feed RSS.



Iremos estão melhorar o Azure Active Directory numa base contínua. Para ativar a se manter atualizado com os desenvolvimentos mais recentes, este tópico fornece informações sobre:

-   As versões mais recentes 
-   Problemas conhecidos 
-   Correções de erros 
-   Funcionalidade preterida 
-   Planos de alterações 

Revê esta página regularmente, tal como estamos a são atualizar mensalmente.

## <a name="november-2017"></a>Novembro de 2017

**Tipo:** preterido funcionalidade  
**Categoria de serviço:** dos ACS  
**Capacidade de produto:** serviço de controlo de acesso 

<a name="acs-retirement"></a>

Microsoft Azure Active Directory controlo de acesso (também conhecido como o serviço de controlo de acesso ou ACS) vai ser reformado no Novembro de 2018.  Pode encontrar informações adicionais, incluindo uma agenda de detalhado & orientações de migração de nível elevado, [nesta página](./develop/active-directory-acs-migration.md).

---


## <a name="october-2017"></a>Outubro de 2017

**Tipo:** plano de alteração  
**Categoria de serviço:** Reporting Services  
**Capacidade de produto:** gestão de ciclo de vida de identidade  


**Relatórios do Azure AD (versão beta) a descontinuar APIs sob o `https://graph.windows.net/<tenant-name>/reports/` nó**

O portal do Azure fornece-lhe:

- Uma nova consola de administração do Azure Active Directory 
- Novas APIs para relatórios de atividade e segurança
 
Devido a estas novas funcionalidades, o relatório APIs sob o **/relatórios** endpoint será descontinuado no 10 de Dezembro de 2017. 

---

**Tipo:** fixo   
**Categoria de serviço:** as minhas aplicações  
**Capacidade de produto:** SSO  


Azure Active Directory suporta deteção de campo de início de sessão automático para as aplicações que compor um campo de nome de utilizador e palavra-passe HTML.  Estes passos documentados em [como capturar automaticamente os campos de início de sessão para uma aplicação](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Pode encontrar esta capacidade adicionando um *não galeria* aplicação no **aplicações empresariais** página no [portal do Azure](http://aad.portal.azure.com). Além disso, pode configurar o **Single Sign-on** modo nesta aplicação nova para **baseada em palavra-passe Single Sign-on**, introduzir um URL de web e, em seguida, ao guardar a página.
 
Devido a um problema do serviço, esta funcionalidade foi temporariamente desativada durante um período de tempo. O problema foi resolvido e a deteção de campo de início de sessão automático está novamente disponível.

---

**Tipo:** nova funcionalidade  
**Categoria de serviço:** MFA  
**Capacidade de produto:** segurança de identidade e de proteção  


No mundo que residem no, a autenticação Multifator (MFA) é uma parte essencial dos proteger a sua organização. A equipa de identidade, Microsoft é evolução de multi-factor authentication fará com que as credenciais mais adaptável e a experiência mais integrada. Hoje em dia sou satisfeito anunciar dois passos importantes neste journey: 

- Integração de resultados de desafio multifator diretamente para o Azure AD início de sessão no relatório, incluindo acesso programático para resultados MFA

- Experiência de integração mais profunda da configuração da MFA para a configuração do principal do Azure AD no portal do Azure

Com esta pré-visualização pública, gestão de MFA e relatórios são uma parte integrada das principais experiência de configuração do Azure AD, permitindo-lhe gerir a funcionalidade do portal de gestão MFA dentro da experiência do Azure AD.

Para obter mais informações, consulte [referência para a autenticação multifator relatórios no portal do Azure](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**Tipo:** nova funcionalidade  
**Categoria de serviço:** termos de utilização  
**Capacidade de produto:** governação  


**Azure AD os termos de utilização** fornece um método simple para apresentar informações aos utilizadores finais. Isto garante que os utilizadores verão exclusões de responsabilidade relevantes para os requisitos legais ou conformidade.

Pode utilizar os termos do Azure AD de utilização nos seguintes cenários:

- Termos de utilização gerais para todos os utilizadores da sua organização. 

- Termos específicos de utilização com base nos atributos de um utilizador (ex. doctors vs nurses ou empregados internacional vs locais, efetuados pelo grupos dinâmicos). 

- Termos específicos de utilização para aceder a aplicações de impacto comercial elevada, como o Salesforce.

Para obter mais informações, consulte [do Azure Active Directory os termos de utilização](active-directory-tou.md).


---
**Tipo:** nova funcionalidade  
**Categoria de serviço:** PIM  
**Capacidade de produto:** Privileged Identity Management  


Do Azure Active Directory Privileged Identity gestão (PIM), já pode gerir, controlar e monitorizar o acesso a recursos do Azure (pré-visualização) dentro da sua organização. Isto inclui subscrições, grupos de recursos e até mesmo máquinas virtuais. Todos os recursos no portal do Azure que tiram partido da funcionalidade de controlo de acesso com base do Azure funções (RBAC) podem tirar partido de todas as segurança grande e capacidades de gestão do ciclo de vida do Azure AD PIM tem para oferecer e algumas novas funcionalidades excelentes planeamos colocar Funções do Azure AD em breve.

Para obter mais informações, consulte [PIM para recursos do Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Tipo:** nova funcionalidade  
**Categoria de serviço:** revisões de acesso  
**Capacidade de produto:** governação  


Revisões de acesso (pré-visualização) permitem às organizações gerir membros do grupo de forma eficiente e acesso a aplicações da empresa: 

- Pode recertify utilizando revisões de acesso do respetivo acesso a aplicações e as associações de grupos de acesso de utilizadores convidados. As informações que forneceu as revisões de acesso ativar revisores de forma eficiente decidir se convidados devem continuaram acesso.

- Pode voltar a certificar o acesso dos colaboradores às aplicações e as associações a grupos com revisões de acesso.

Pode recolher os controlos de revisão de acesso relativos a programas relevantes para a sua organização de modo a controlar as revisões de conformidade ou de aplicações sensíveis a riscos.

Para obter mais informações, consulte [acesso do Azure AD revê](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Tipo:** nova funcionalidade  
**Categoria de serviço:** as minhas aplicações  
**Capacidade de produto:** SSO  


**Capacidade para ocultar as aplicações de terceiros da minha aplicações e o Iniciador do Office 365**

Agora pode gerir melhor aplicações apresentados nos portais do utilizador através de um novo **ocultar aplicação** propriedade. Isto ajuda-o com casos em que os mosaicos da aplicação são ser apresentado para serviços de back-end ou mosaicos duplicados e acaba por ficar cluttering launchers de aplicação do utilizador. O botão de alternar está localizado na secção de propriedades da aplicação de terceiros e assinalada como **Visible ao utilizador?**. Também pode ocultar uma aplicação através de programação através do PowerShell. 

Para obter mais informações, consulte [ocultar uma aplicação de terceiros da experiência do utilizador no Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**O que está disponível?**

 Como parte da transição para a nova consola de administração, efetuamos 2 novas APIs para obter registos de atividade do Azure AD disponíveis. O novo conjunto de APIs fornecem mais rico de filtragem e ordenação funcionalidade além de proporcionarem auditoria mais completas e as atividades de início de sessão. Os dados previamente disponíveis através de relatórios de segurança agora podem ser acedidos através de eventos de risco Identity Protection API no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** do Microsoft Identity Manager  
**Capacidade de produto:** gestão de ciclo de vida de identidade  


Um pacote de rollup de correção (compilação 4.4.1642.0) está disponível a partir 25 de Setembro de 2017, do Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1). Este pacote de rollup:

- Resolve problemas e adiciona melhorias
- É uma atualização cumulativa que substitui todas as atualizações de MIM 2016 SP1 até compilação 4.4.1459.0 do Microsoft Identity Manager 2016. 
- Requer que tenha **4.4.1302.0 de compilação do Microsoft Identity Manager 2016.** 

Para obter mais informações, consulte [(compilação 4.4.1642.0) do pacote de rollup de correção está disponível para o Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
