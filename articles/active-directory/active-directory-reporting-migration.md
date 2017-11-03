---
title: "Localizar os relatórios de atividade no portal do Azure | Microsoft Docs"
description: "Saiba como localizar relatórios de atividade do Azure Active Directory no portal do Azure."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6f1485ad57eb28841ef5fe585e8cf1b8eb29e4a7
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar os relatórios de atividade no portal do Azure

Se estiver a mover do portal clássico do Azure no portal do Azure, obtenha uma nova vista de olhos registos de atividade do Azure Active Directory (Azure AD). Num recente [blogue](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/), vamos explicar como pode ver os registos de atividade no contexto do recurso está a trabalhar no portal do Azure. Neste artigo, vamos descrevem como localizar relatórios que utilizou no portal clássico do Azure no portal do Azure.

## <a name="whats-new"></a>Novidades

Relatórios no portal clássico do Azure estão separados em categorias:

1.  Relatórios de segurança
2.  Relatórios de atividade
3.  Relatórios de aplicação integrada

### <a name="activity-and-integrated-app-reports"></a>Atividade e os relatórios de aplicação integrada

Para baseado no contexto de relatórios no portal do Azure, os relatórios existentes são intercalados numa única vista. Um único subjacente API fornece os dados para a vista.

Para ver esta vista na **do Azure Active Directory** painel, em **ATIVIDADE**, selecione **registos de auditoria**.

![Registos de auditoria](./media/active-directory-reporting-migration/482.png "Registos de auditoria")

Os seguintes relatórios são consolidados nesta vista:

-   Relatório de auditoria
-   Atividade de reposição de palavra-passe
-   Atividade de registo de reposição de palavra-passe
-   Atividade de grupos self-service
-   Alterações de nome de grupo do Office 365
-   Atividade de aprovisionamento de contas
-   Estado de rollover de palavra-passe
-   Erros de aprovisionamento de contas


O relatório de utilização da aplicação foi melhorado e está incluído no **inícios de sessão** vista. Para ver esta vista na **do Azure Active Directory** painel, em **ATIVIDADE**, selecione **inícios de sessão**.

![Vista de inícios de sessão](./media/active-directory-reporting-migration/483.png "ver inícios de sessão")

O **inícios de sessão** vista inclui todos os utilizadores inícios de sessão. Pode utilizar estas informações para obter informações de utilização da aplicação. Também pode ver informações de utilização da aplicação no **aplicações empresariais** descrição geral, no **GERIR** secção.

![As aplicações empresariais](./media/active-directory-reporting-migration/484.png "aplicações da empresa")

## <a name="access-a-specific-report"></a>Aceder a um relatório específico

Embora o portal do Azure oferece uma vista única, também pode ver relatórios específicos.

### <a name="audit-logs"></a>Registos de auditoria

Em resposta a comentários de clientes, no portal do Azure, pode utilizar a filtragem avançadas para aceder aos dados que pretende. É um filtro pode utilizar um *categoria de atividade*, que lista os diferentes tipos de atividade de registo no Azure AD. Para limitar os resultados para aquilo procura, selecione uma categoria.

Por exemplo, se estiver interessado apenas em atividades relacionadas com a reposição de palavra-passe self-service, pode escolher o **gestão de palavras-passe Self-Service** categoria. As categorias que vir baseiam-se no recurso que está a trabalhar.  

![Opções de categoria na página registos de auditoria de filtro](./media/active-directory-reporting-migration/06.png "opções de categoria na página registos de auditoria de filtro")

Categorias de atividade incluem:

- Diretório do Núcleo
- Gestão de Palavra-passe Personalizada
- Gestão de Grupos Personalizada
- Aprovisionamento de Contas

### <a name="application-usage"></a>Utilização da aplicação

Para ver os detalhes sobre a utilização de aplicação para todas as aplicações ou para uma única aplicação em **ATIVIDADE**, selecione **inícios de sessão**. Para restringir os resultados, pode filtrar por nome de utilizador ou nome da aplicação.

![Página de início de sessão de eventos de filtro](./media/active-directory-reporting-migration/07.png "página Eventos de início de sessão de filtragem")

### <a name="security-reports"></a>Relatórios de segurança

#### <a name="azure-ad-anomalous-activity-reports"></a>Relatórios de atividade anómala do Azure AD

Segurança de atividade anómala do Azure AD tem sido consolidados relatórios a partir do portal clássico do Azure para lhe fornecer uma, vista central. Esta vista mostra todos os eventos de risco relacionado com a segurança de que o do Azure AD pode detetar e comunicar.

A seguinte tabela apresenta o Azure AD atividade anómala segurança os relatórios e tipos de eventos de risco correspondentes no portal do Azure.

| Relatório de atividade anómala do Azure AD |  Tipo de evento de risco de proteção de identidade|
| :--- | :--- |
| Utilizadores com credenciais obtidas ilicitamente | Credenciais obtidas ilicitamente |
| Atividades irregulares de início de sessão | Deslocação impossível para localizações atípicas |
| Inícios de sessão de dispositivos possivelmente infetados | Inícios de sessão a partir de dispositivos infetados|
| Inícios de sessão de fontes desconhecidas | Inícios de sessão de endereços IP anónimos |
| Inícios de sessão de endereços IP com atividade suspeita | Inícios de sessão de endereços IP com atividade suspeita |
| - | Inícios de sessão a partir de localizações desconhecidas |

Relatórios de segurança seguinte para a atividade anómala do Azure AD não são incluídos como eventos de risco no portal do Azure:

* Inícios de sessão após várias falhas
* Inícios de sessão de várias localizações geográficas

Estes relatórios ainda estão disponíveis no portal clássico do Azure, mas que vão ser preteridas em altura no futuro.

Para obter mais informações, consulte [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md).  


#### <a name="detected-risk-events"></a>Eventos de risco detetados

No portal do Azure, pode aceder aos relatórios sobre eventos de risco detetados no **do Azure Active Directory** painel, em **segurança**. Eventos de risco detetados são registados nos relatórios do seguintes:   

- Utilizadores em risco
- Inícios de Sessão de Risco

![Relatórios de segurança](./media/active-directory-reporting-migration/04.png "relatórios de segurança")

Para obter mais informações sobre os relatórios de segurança, consulte:

- [Utilizadores no relatório de segurança de risco no portal do Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Risco relatório de inícios de sessão no portal do Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Relatórios de atividade no portal clássico do Azure vs. o portal do Azure

A tabela esta secção lista os relatórios existentes no portal clássico do Azure. Também descreve como obter as mesmas informações no portal do Azure.

Para ver todos os dados de auditorias, o **do Azure Active Directory** painel, em **ATIVIDADE**, aceda a **registos de auditoria**.

![Registos de auditoria](./media/active-directory-reporting-migration/61.png "Registos de auditoria")

| Portal Clássico do Azure                 | Para localizar no portal do Azure                                                         |
| ---                                  | ---                                                                        |
| Registos de auditoria                           | Para **categoria de atividade**, selecione **Core diretório**.                       |
| Atividade de reposição de palavra-passe              | Para **categoria de atividade**, selecione **gestão de palavras-passe Self-Service**. |
| Atividade de registo de reposição de palavra-passe | Para **categoria de atividade**, selecione **gestão de palavras-passe Self-Service**.     |
| Atividade de grupos self-service         | Para **categoria de atividade**, selecione **Self-Service de grupo de gestão**.        |
| Atividade de aprovisionamento de contas        | Para **categoria de atividade**, selecione **aprovisionamento de utilizador da conta**.         |
| Estado de rollover de palavra-passe             | Para **categoria de atividade**, selecione **Rollover de palavra-passe de aplicação automática**.      |
| Erros de aprovisionamento de contas          | Para **categoria de atividade**, selecione **aprovisionamento de utilizador da conta**.        |
| Alterações de nome de grupo do Office 365         | Para **categoria de atividade**, selecione **gestão de palavras-passe Self-Service**. Para **tipo de recurso de atividade**, selecione **grupo**. Para **atividade origem**, selecione **grupos do Office 365**.|

Para ver o **utilização da aplicação** no relatório o **do Azure Active Directory** painel, em **GERIR**, selecione **aplicações empresariais**, e, em seguida, selecione **inícios de sessão**.


![Relatório de inícios de sessão do aplicações empresariais](./media/active-directory-reporting-migration/199.png "relatório Enterprise aplicações inícios de sessão")

## <a name="next-steps"></a>Passos seguintes

Para obter uma descrição geral dos relatórios, veja [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).
