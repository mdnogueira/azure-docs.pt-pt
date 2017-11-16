---
title: "Azure de identidade e acesso melhores práticas de segurança | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas para gestão de identidade e controlo de acesso utilizando incorporada capacidades do Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: d80fdd5a2e4339823c05368d76de333f3314d4ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Melhores práticas de segurança de controlo de acesso e gestão de identidades do Azure
Muitas considere identidade seja de nova camada de limites para segurança, tendo mais essa função da perspetiva centrada em rede tradicional. Este evolução do pivot principal de segurança de atenção e investimentos provenientes do facto de que perimeters de rede têm de se cada vez mais porous e esse defesa de perímetro não pode ser como efetiva à medida que uma vez foram antes da explosão de [BYOD ](http://aka.ms/byodcg) dispositivos e aplicações em nuvem.

Neste artigo, vamos abordar uma coleção de gestão de identidades do Azure e práticas recomendadas de segurança do controlo de acesso. Estas melhores práticas são derivadas da nossa experiência com [do Azure AD](../active-directory/active-directory-whatis.md) e as experiências dos clientes, como por si.

Para cada melhor prática, vamos explicar:

* O que é a melhor prática
* Por que motivo que pretende ativar essa recomendado
* Se falhar ativar a melhor prática, que poderá ser o resultado
* Possíveis alternativas à melhor prática
* Como pode saber mais ativar a melhor prática

Esta gestão de identidades do Azure e a segurança de controlo de acesso artigo de melhores práticas baseia-se num opinião consenso e capacidades da plataforma do Azure e conjuntos de funcionalidades, tal como existem no momento este artigo foi escrito. Opinions e tecnologias alteram ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Identidade do Azure de gestão e acesso controlo melhores práticas de segurança abordadas neste artigo incluem:

* Centralizar a gestão de identidades
* Ativar início de sessão único (SSO)
* Implementar a gestão de palavra-passe
* Impor autenticação multifator (MFA) para os utilizadores
* Controlo de acesso (RBAC) baseado em funções de utilização
* Controlar as localizações onde os recursos são criados com o resource manager
* Guia de programadores tirar partido das capacidades de identidade para aplicações SaaS
* Monitorizar ativamente para atividades suspeitas

## <a name="centralize-your-identity-management"></a>Centralizar a gestão de identidades
É um passo importante para proteger a sua identidade para assegurar que TI podem gerir contas de uma única localização sobre onde esta conta foi criada. Enquanto a maioria das empresas as organizações de TI terá a sua conta principal diretório no local, implementações de nuvem híbrida estão no aumento súbito e é importante que compreenda como integrar no local e diretórios de nuvem e forneça um totalmente integrada experiência para o utilizador final.

Para tal [identidade híbrida](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) cenário Recomendamos duas opções:

* Sincronizar o seu diretório no local com o diretório em nuvem com o Azure AD Connect
* Federar a sua identidade no local com o diretório de nuvem utilizando [serviços de Federação do Active Directory](https://msdn.microsoft.com/library/bb897402.aspx) (AD FS)

As organizações que não obedeçam a integrar as identidades no local com a identidade de nuvem verificará maiores despesas administrativas gerais na gestão de contas, que aumenta a probabilidade de prende e falhas de segurança.

Para obter mais informações sobre a sincronização do Azure AD, leia o artigo [integrar as identidades no local ao Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Ativar início de sessão único (SSO)
Quando tiver vários diretórios gerir, isto torna-se um problema administrativo não só para departamento de TI, mas também para os utilizadores finais que vai ter de memorizar várias palavras-passe. Ao utilizar [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) irá fornecer os seus utilizadores a capacidade de utilizar o mesmo conjunto de credenciais de início de sessão e aceder os recursos que necessitam, independentemente de já se encontra onde este recurso se encontra localizada no local ou na nuvem.

Utilizar o SSO para permitir que os utilizadores para aceder ao respetivo [aplicações SaaS](../active-directory/active-directory-appssoaccess-whatis.md) com base na respetiva conta organizacional no Azure AD. Isto é aplicável não só para aplicações SaaS da Microsoft, mas também as outras aplicações, tais como [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). A aplicação pode ser configurada para utilizar o Azure AD como um [identidade baseada em SAML](../active-directory/fundamentals-identity.md) fornecedor. Como um controlo de segurança do Azure AD não irá emitir um token, permitindo-lhes iniciar sessão na aplicação, a menos que tenham sido concedidos acesso através do Azure AD. Pode conceder acesso diretamente ou através de um grupo que são membros do.

> [!NOTE]
> a decisão de utilizar o SSO irá afetar a forma como integrar o diretório no local com o diretório em nuvem. Se pretender que o SSO, terá de utilizar a Federação, porque a sincronização de diretórios só irá fornecer [mesma experiência de início de sessão](../active-directory/active-directory-aadconnect.md).
>
>

As organizações que não a impor o SSO para os seus utilizadores e aplicações mais estão expostas a cenários onde os utilizadores terão de várias palavras-passe que diretamente aumenta a probabilidade dos utilizadores a reutilização de palavras-passe ou utilizar palavras-passe fracas.

Pode saber mais sobre o SSO do Azure AD ao ler o artigo [gestão do AD FS e personalização com o Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Implementar a gestão de palavra-passe
Em cenários em que tem vários inquilinos ou se pretender permitir que os utilizadores [repor os seus próprios palavra-passe](../active-directory/active-directory-passwords-update-your-own-password.md), é importante que utilize as políticas de segurança adequadas para evitar abuso. No Azure podem tirar partido da capacidade de reposição de palavra-passe self-service e personalizar as opções de segurança para satisfazer os seus requisitos empresariais.

É particularmente importante obter comentários destes utilizadores e obter a partir das suas experiências como tentarem efetuar estes passos. Com base nestes experiências, elaborate um plano de correção para atenuar potenciais problemas que possam ocorrer durante a implementação de um grupo superior. Também é recomendável que utilize o [relatório de atividade de registo de reposição de palavra-passe](../active-directory/active-directory-passwords-get-insights.md) para monitorizar os utilizadores que estão a registar.

As organizações que pretendem evitar chamadas de suporte de alteração de palavra-passe, mas a permitir que os utilizadores reponham as suas próprias palavras-passe sejam mais suscetíveis a um volume de chamadas superior para o suporte técnico devido a problemas de palavra-passe. Nas organizações que têm múltiplos inquilinos, é imperativo que implementar este tipo de capacidade e permitir que os utilizadores executar dentro de limites de segurança que foram estabelecidos na política de segurança de reposição de palavra-passe.

Pode saber mais sobre a reposição ao ler o artigo palavra-passe [implementar gestão de palavras-passe e preparação de utilizadores para utilizá-lo](../active-directory/active-directory-passwords-best-practices.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Impor autenticação multifator (MFA) para os utilizadores
Para organizações que precisam de estar em conformidade com as normas do sector, como [PCI DSS versão 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), autenticação multifator é um tem de ter capacidade para autenticar os utilizadores. Para além de ser compatível com as normas do sector, impor a MFA para autenticar os utilizadores também pode ajudar as organizações a mitigar o tipo de roubo de credenciais de ataque, tais como [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Ao ativar a MFA do Azure para os seus utilizadores, que está a adicionar uma segunda camada de segurança para inícios de sessão de utilizador e de transações. Neste caso, uma transação poderá aceder a um documento localizado num servidor de ficheiros ou o SharePoint Online. Também ajuda-o MFA do Azure que as TI reduzem a probabilidade de que uma credencial comprometida terão acesso aos dados da organização.

Por exemplo: impor o MFA do Azure para os seus utilizadores e configurá-lo para utilizar uma chamada telefónica ou mensagem de texto como verificação. Se as credenciais do utilizador ficam comprometidas, o atacante será capaz de aceder a qualquer recurso uma vez que ele não terá acesso para o telefone do utilizador. As organizações que não adicione camadas adicionais de proteção de identidade sejam mais suscetíveis de ataque de roubo de credenciais, que pode levar ao comprometimento de dados.

Uma alternativa para as organizações que pretende manter a autenticação completa controlo no local é utilizar [do servidor multi-factor Authentication Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), também denominado MFA no local. Através deste método, irá ainda ser capaz de impôr a autenticação multifator, mantendo o MFA server no local.

Para obter mais informações sobre o MFA do Azure, leia o artigo [introdução ao Azure multi-factor Authentication na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Controlo de acesso (RBAC) baseado em funções de utilização
Restringir o acesso com base no [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é imperativo para as organizações que pretendem aplicar políticas de segurança para acesso a dados. Azure baseada em funções controlo de acesso (RBAC) pode ser utilizado para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. O âmbito de uma atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso.

Pode tirar partido [incorporadas do RBAC](../active-directory/role-based-access-built-in-roles.md) funções no Azure para atribuir os privilégios aos utilizadores. Considere a utilização de *contribuinte de conta de armazenamento* para os operadores da nuvem que necessitam de gerir as contas do storage e *contribuinte de conta de armazenamento clássico* função para gerir contas de armazenamento clássicas. Para os operadores da nuvem que necessita para gerir VMs e conta de armazenamento, considere adicionar-lhes *contribuinte de Máquina Virtual* função.

As organizações que não a impor controlo de acesso de dados ao tirar partido das capacidades, tal como o RBAC podem ser dá ao mais privilégios que necessárias aos respetivos utilizadores. Isto pode levar a dados comprometimento por permitir que os utilizadores acesso a determinados tipos de tipos de dados (por exemplo, elevado impacto comercial), que não deve ter em primeiro lugar.

Pode saber mais sobre o RBAC do Azure ao ler o artigo [controlo de acesso em funções do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Controlar as localizações onde os recursos são criados com o resource manager
Ativar os operadores da nuvem efetuar tarefas ao impedir que interrompendo as convenções de que são necessárias para gerir os recursos da sua organização é muito importante. As organizações que pretendem controlar as localizações onde os recursos são criados rígido devem código estas localizações.

Para atingir esse objetivo, as organizações podem criar políticas de segurança que têm definições que descrevem os recursos que são especificamente negados ou ações. Atribuir o âmbito pretendido, como a subscrição, grupo de recursos ou um recurso individual essas definições de política.

> [!NOTE]
> Esta não é o mesmo que o RBAC, na verdade, utiliza o RBAC para autenticar os utilizadores que possuem privilégios para criar esses recursos.
>
>

Tire partido [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para criar políticas personalizadas também para cenários em que a organização pretende permitir operações apenas quando o Centro de custos adequado está associado; caso contrário, o irão negar o pedido.

As organizações que não são controlar a forma como os recursos são criados sejam mais suscetíveis a utilizadores que poderão injuriar o serviço através da criação de mais recursos de que precisam. Proteger o processo de criação de recursos é um passo importante para proteger um cenário de multi-inquilino.

Pode saber mais sobre como criar políticas com o Azure Resource Manager ao ler o artigo [o que é a política do Azure?](../azure-policy/azure-policy-introduction.md).

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Guia de programadores tirar partido das capacidades de identidade para aplicações SaaS
A identidade do utilizador irá ser aproveitada em vários cenários, quando os utilizadores acedem [aplicações SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) que pode ser integrado no local ou na nuvem diretório. Primeiro e mais importante, recomendamos que os programadores utilizar uma metodologia segura para desenvolver estas aplicações, tais como [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Azure AD simplifica a autenticação para os programadores, fornecendo identidade como um serviço, com suportem para protocolos de norma da indústria, tais como [OAuth 2.0](http://oauth.net/2/) e [OpenID Connect](http://openid.net/connect/), bem como código aberto bibliotecas para plataformas diferentes.

Certifique-se registar as aplicações que outsources autenticação para o Azure AD, este é um procedimento obrigatório. O motivo por trás isto é porque precisa do Azure AD coordenar a comunicação com a aplicação quando o processamento de início de sessão (SSO) ou trocar tokens. A sessão do utilizador expira quando expira a duração do token emitido pelo Azure AD. Avalie sempre se a aplicação deve utilizar este tempo ou se é possível reduzir neste momento. Reduzir a duração pode agir como uma medida de segurança que irá exigir que os utilizadores para terminar sessão com base no durante um período de inatividade.

As organizações que não a impor controlo de identidade para aceder a aplicações não guia e dos programadores sobre como integrar com segurança as aplicações com o sistema de gestão de identidade podem ser mais suscetíveis a credencial, tais como o tipo de roubo de ataque, [fracos gestão de sessão e autenticação descrito na parte superior de aplicação do abra Web projeto segurança (OWASP) 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Pode saber mais sobre os cenários de autenticação para aplicações SaaS lendo [cenários de autenticação para o Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorizar ativamente para atividades suspeitas
Em conformidade com [relatório de violação de dados de 2016 da Verizon](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), credenciais comprometidas ainda está no aumento súbito e a tornar-se um das empresas mais rentáveis para criminals informático. Por este motivo, é importante que tenha um sistema de monitor de identidade do Active Directory no local que pode detetar atividades de comportamento suspeito rapidamente e acionar um alerta para uma investigação mais aprofundada. Azure AD dispõe de dois capacidades principais que podem ajudar as organizações a monitorizarem os seus identidades: Azure AD Premium [anomalias relatórios](../active-directory/active-directory-view-access-usage-reports.md) e o Azure AD [proteção identidade](../active-directory/active-directory-identityprotection.md) capacidade.

Certifique-se de utilizar os relatórios de anomalias para identificar as tentativas para iniciar sessão [sem ser rastreadas](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [força bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) ataques contra uma conta específica, tenta iniciar sessão a partir de várias localizações, iniciar sessão a partir de [infetados dispositivos](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md) e endereços IP suspeitos. Tenha em atenção que estes são os relatórios. Por outras palavras, tem de ter processos e procedimentos no local para administradores de TI executar estes relatórios, numa base diária ou a pedido (normalmente, num cenário de resposta a incidentes).

Em contrapartida, a proteção de identidade do Azure AD é um sistema de monitorização Active Directory e irá sinalizador os riscos atuais no seu próprio dashboard. Para além disso, também irá receber notificações diárias de resumidas por e-mail. Recomendamos que ajustar o nível de risco, de acordo com os requisitos de negócio. O nível de risco para um evento de risco é uma indicação (alta, média ou baixa) a gravidade do evento de risco. O nível de risco ajuda os utilizadores de Identity Protection atribuir prioridades as ações que devem efetuar para reduzir o risco para a respetiva organização.

As organizações que não monitorize ativamente os respetivos sistemas de identidade estão em risco de ter credenciais de utilizador comprometidas. Sem dados de conhecimento que estão a utilizar atividades suspeitas colocar a utilizar estas credenciais, as organizações não conseguirão mitigar este tipo de ameaça.
Pode saber mais sobre proteção de identidade do Azure através da leitura [do Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
