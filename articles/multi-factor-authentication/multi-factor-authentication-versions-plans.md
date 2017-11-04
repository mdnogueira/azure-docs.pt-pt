---
title: "Versões do MFA do Azure e planos de consumo | Microsoft Docs"
description: "Informações sobre o cliente de multi-factor Authentication e os diferentes métodos e as versões disponíveis. Detalhes sobre cada plano de consumo"
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: kgremban
ms.openlocfilehash: 159e56c7ba1e0c27cd854f7d835611d5707c7a23
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>A obtenção de multi-factor Authentication do Azure

Quando for necessário para proteger as suas contas, a verificação deve ser standard na sua organização. Esta funcionalidade é especialmente importante para as contas administrativas que tem acesso a recursos privilegiado. Por este motivo, a Microsoft oferece funcionalidades de verificação de dois passos básicos para Office 365 e os administradores do Azure para existem custos adicionais associados. Se pretender atualizar as funcionalidades para os seus administradores ou expandir a verificação de dois passos para o resto dos seus utilizadores, pode comprar o Azure multi-factor Authentication. 

Este artigo explica a diferença entre as versões oferecido algum serviço para os administradores e a versão completa do MFA do Azure. Se estiver pronto para implementar o MFA do Azure completa oferta, a secção posterior aborda opções de implementação e a forma como o Microsoft calcula consumo.


>[!IMPORTANT]
>Este artigo destina-se como um guia para ajudar a compreender as diferentes formas para comprar o Azure multi-factor Authentication. Para obter detalhes específicos sobre preços e faturação, sempre devem consultar ao [multi-factor Authentication página de preços](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis de multi-factor Authentication do Azure

A tabela seguinte descreve as diferenças entre três versões da multi-factor authentication:

| Versão | Descrição |
| --- | --- |
| Multi-Factor Authentication para Office 365 |Esta versão funciona exclusivamente com aplicações do Office 365 e é gerida a partir do portal do Office 365. Os administradores podem [proteger recursos do Office 365 com verificação de dois passos](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Esta versão faz parte de uma subscrição do Office 365. |
| Autenticação Multifator para os administradores do Azure | Os administradores globais de inquilinos do Azure podem ativar a verificação de dois passos para as contas de administrador global sem custos adicionais.|
| Multi-Factor Authentication do Azure | Muitas vezes, referida como a versão "completa", o multi-factor Authentication do Azure oferece o conjunto de capacidades richest. Fornece opções de configuração adicionais através do [portal clássico do Azure](https://manage.windowsazure.com), avançadas de relatórios e suporte para um intervalo de no local e as aplicações em nuvem. Multi-factor Authentication do Azure está incluído no [planos do Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) e [Enterprise Mobility + planos de segurança](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing)e pode ser implementado ou na nuvem ou no local. |

## <a name="feature-comparison-of-versions"></a>Comparação de funcionalidades das versões
A tabela seguinte fornece uma lista das funcionalidades que estão disponíveis nas várias versões do Azure multi-factor Authentication.

> [!NOTE]
> Esta tabela de comparação descreve as funcionalidades que fazem parte de cada versão do multi-factor Authentication. Se tiver o serviço de multi-factor Authentication do Azure completo, algumas funcionalidades poderão não estar disponíveis consoante utilize ou [MFA na nuvem ou MFA no local](multi-factor-authentication-get-started.md).


| Funcionalidade | Multi-Factor Authentication para Office 365 | Autenticação Multifator para os administradores do Azure | Multi-Factor Authentication do Azure |
| --- |:---:|:---:|:---:|
| Proteger contas de administrador com a MFA |● |● (apenas para contas de Administrador Global) |● |
| Aplicação móvel como um segundo fator |● |● |● |
| Chamada telefónica como segundo fator de |● |● |● |
| SMS como um segundo fator |● |● |● |
| Palavras-passe de aplicação para clientes que não suportam MFA |● |● |● |
| Controlo de administração sobre métodos de verificação |● |● |● |
| Proteger contas de não administrador com a MFA | | |● |
| Modo PIN | | |● |
| Alerta de fraudes | | |● |
| Relatórios do MFA | | |● |
| Omissão de Uso Individual | | |● |
| Saudações personalizadas para chamadas telefónicas | | |● |
| ID do autor da chamada personalizadas para chamadas telefónicas | | |● |
| IPs Fidedignos | | |● |
| Memorizar MFA para dispositivos fidedignos |● |● |● |
| MFA SDK | | |● (fornecedor de multi-Factor Auth requer e subscrição do Azure completo) |
| MFA para aplicações no local | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>A obtenção de multi-factor Authentication do Azure
Se gostaria que todas as funcionalidades oferecidas pelo Azure multi-factor Authentication, existem várias opções:

### <a name="option-1---mfa-licenses"></a>Opção 1 - licenças MFA

Comprar licenças de Azure multi-factor Authentication e atribuí-las para os utilizadores no Azure Active Directory. 

Se utilizar esta opção, crie apenas um fornecedor de autenticação do multi-factor do Azure se tem de fornecer a verificação de dois passos para alguns utilizadores que não têm licenças. Caso contrário, poderá ser-lhe faturado duas vezes.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>Licenças de opção 2 - incluída que incluem o MFA

Licenças de compra que incluem o Azure multi-factor Authentication, como o Azure Active Directory Premium ou Enterprise Mobility + Security e atribuí-las para os utilizadores no Azure Active Directory. 

Se utilizar esta opção, deve criar um fornecedor de autenticação do multi-factor do Azure apenas se terá também de fornecer a verificação de dois passos para alguns utilizadores que não têm licenças. Caso contrário, poderá ser-lhe faturado duas vezes. 

### <a name="option-3---mfa-consumption-based-model"></a>Opção 3 - modelo baseada no consumo de MFA

Crie um fornecedor de autenticação do multi-factor do Azure dentro de uma subscrição do Azure. Fornecedores de MFA do Azure são recursos do Azure que são faturados contra o Enterprise Agreement, um compromisso monetário do Azure ou um cartão de crédito como todos os outros recursos do Azure. Estes fornecedores só podem ser criados em subscrições Azure completo, não se limitando subscrições do Azure que tenham um 0 $ limite de gastos. Limitado subscrições são criadas quando ativar licenças, como nas opções de 1 e 2. 

Quando utilizar um fornecedor de autenticação do multi-factor do Azure, existem dois modelos de utilização disponíveis que são faturadas através da sua subscrição do Azure:  

1. **Por utilizador ativado** - para que as empresas que pretendem ativar a verificação de dois passos para um número fixo de empregados que necessitem regularmente de autenticação. Faturação por utilizador baseia-se no número de utilizadores ativados para a MFA no seu inquilino do Azure AD e o servidor MFA do Azure. Se os utilizadores estão ativados para o MFA em ambas do Azure AD e o servidor do MFA do Azure e a sincronização de domínio (Azure AD Connect) está ativada, em seguida, iremos contagem maior conjunto de utilizadores. Se a sincronização de domínio não está ativada, então contagem é a soma de todos os utilizadores ativados para a MFA no Azure AD e o servidor MFA do Azure. Faturação é proporcional e comunicada ao sistema de comércio diariamente. 

  > [!NOTE]
  > Exemplo de faturação 1: tiver 5000 utilizadores ativados para a MFA hoje. O sistema MFA divide esse número 31 e 161.29 utilizadores relatórios para esse dia. Amanhã ativa 15 mais utilizadores, para que o sistema MFA 161.77 utilizadores de relatórios para esse dia. No final de ciclo de faturação, o número total de utilizadores com faturação contra a sua subscrição do Azure adiciona até à volta de 5000. 
  >
  > Exemplo de faturação 2: tem uma mistura de utilizadores com licenças e os utilizadores sem, pelo que tem um fornecedor de MFA do Azure por utilizador para constituir a diferença. Existem 4,500 Enterprise Mobility + licenças de segurança no seu inquilino, mas 5000 utilizadores ativados para a MFA. A subscrição do Azure é faturada por 500 utilizadores, proporcional e reportada 16.13 utilizadores diariamente. 

2. **Por autenticação** - para que as empresas que pretendem ativar a verificação de dois passos para um grupo grande de utilizadores que raramente necessitam de autenticação. Faturação baseada no número de pedidos de verificação de dois passos, independentemente se esses verificações êxito ou são negadas. Este é apresentado na sua declaração de utilização do Azure em pacotes de 10 autenticações e faturação é comunicada diariamente. 

  > [!NOTE]
  > Exemplo de faturação 3: hoje, o serviço de MFA do Azure recebeu 3,105 pedidos de verificação de dois passos. A subscrição do Azure é faturada 310.5 pacotes de autenticação. 

É importante ter em atenção que pode ter licenças de MFA do Azure, mas ainda obter cobrados com base no consumo de configuração. Se configurar um fornecedor de MFA do Azure por autenticação, é-lhe faturado para cada pedido de verificação de dois passos, incluindo os feito por utilizadores que possuem licenças. Se configurar um fornecedor de MFA do Azure por utilizador no domínio que não está ligado ao seu inquilino do Azure AD, é-lhe faturado por utilizador ativado, mesmo se os utilizadores têm de licenças no Azure AD. 

## <a name="next-steps"></a>Passos seguintes

- Para obter mais detalhes de preços, consulte [preços do Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Escolha se pretende implementar o MFA do Azure [na nuvem ou no local](multi-factor-authentication-get-started.md)
