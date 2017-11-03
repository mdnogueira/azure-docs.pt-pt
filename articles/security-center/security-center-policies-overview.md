---
title: "Introdução às políticas de segurança do Centro de segurança do Azure | Microsoft Docs"
description: "Saiba mais sobre as políticas de segurança do Centro de segurança do Azure e as respetivas capacidades de chaves."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 95ef2099cb16bcfd550ce2799428f1a16031f535
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="security-policies-overview"></a>Descrição geral das políticas de segurança
Este documento fornece uma descrição geral das políticas de segurança no Centro de segurança.

## <a name="what-are-security-policies"></a>O que são políticas de segurança?
As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares. No Centro de Segurança, pode definir políticas para as suas subscrições do Azure, que podem ser adaptadas ao tipo de carga de trabalho ou à sensibilidade dos dados. Por exemplo, as aplicações que utilizam dados regulados como pessoal informações de identificação podem exigir um nível mais elevado de segurança que outras cargas de trabalho. 

As políticas do Centro de Segurança contêm os componentes seguintes:

- Recolha de dados: agente de aprovisionamento e [recolha de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) definições.
- Política de segurança: determina quais os controlos são monitorizados e recomendados pelo centro de segurança (editar o [política de segurança](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) no Centro de segurança ou utilize [política Azure](security-center-azure-policy.md), na pré-visualização limitada para criar um novo as definições, definir políticas adicionais e atribuir políticas em grupos de gestão).
- Notificações por correio eletrónico: contactos de segurança, e [notificação de correio electrónico](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) definições.
- Escalão de preço: gratuita ou standard [preços seleção](https://docs.microsoft.com/azure/security-center/security-center-pricing), que determina as funcionalidades do Centro de segurança estão disponíveis para os recursos no âmbito (pode ser especificado para as subscrições, grupos de recursos e áreas de trabalho). 


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
Centro de segurança utiliza baseada em funções controlo de acesso (RBAC), que fornece funções incorporadas que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando um utilizador abre o Centro de Segurança, vê apenas informações relacionadas com os recursos a que tem acesso. Isso significa que é atribuído ao utilizador a função de Proprietário, Contribuinte ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso. Além destas funções, há duas funções específicas do Centro de Segurança:

- Leitor de segurança: o utilizador que pertence a esta função é capaz de ver direitos ao centro de segurança, que inclui as recomendações, alertas, políticas e estado de funcionamento, mas não conseguirá efetuar alterações.
- Administrador de segurança: mesmo que o leitor de segurança, mas também pode atualizar a política de segurança, dispensar recomendações e alertas.


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu sobre as políticas de segurança no Centro de segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar políticas de segurança para as subscrições do Azure e os grupos de recursos.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Segurança de dados do Centro de segurança do Azure](security-center-data-security.md) -Saiba como os dados são geridos e salvaguardados no Centro de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenha as mais recentes notícias de segurança do Azure e as informações.


