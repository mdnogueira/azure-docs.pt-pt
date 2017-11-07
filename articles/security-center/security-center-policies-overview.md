---
title: "Introdução às políticas de segurança do Centro de segurança do Azure | Microsoft Docs"
description: "Saiba mais sobre as principais capacidades e políticas de segurança do Centro de segurança do Azure."
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
ms.openlocfilehash: 60cc65bb94e05da1c0b7ee20930c0530f46e71ec
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="security-policies-overview"></a>Descrição geral das políticas de segurança
Este artigo fornece uma descrição geral das políticas de segurança no Centro de segurança.

## <a name="what-are-security-policies"></a>O que são políticas de segurança?
As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares. No Centro de segurança do Azure, pode definir políticas para as suas subscrições do Azure e personalizá-los para o tipo de carga de trabalho ou da sensibilidade dos seus dados. Por exemplo, as aplicações que utilizam dados regulados, tais como informações de identificação pessoal, podem exigir um nível mais elevado de segurança que outras cargas de trabalho. 

As políticas do Centro de Segurança contêm os componentes seguintes:

- **Recolha de dados**: determina o agente de aprovisionamento e [recolha de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) definições.
- **Política de segurança**: determina que controla os monitores de centro de segurança e recomenda. Pode editar o [política de segurança](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) no Centro de segurança. Também pode utilizar [política Azure](security-center-azure-policy.md) (em pré-visualização limitada) para criar novas definições, definir políticas adicionais e atribuir políticas em grupos de gestão.
- **Notificações por correio eletrónico**: determina contactos de segurança, e [notificação de correio electrónico](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) definições.
- **Escalão de preço**: define gratuita ou standard [preços seleção](https://docs.microsoft.com/azure/security-center/security-center-pricing). A camada que escolha determina as funcionalidades do Centro de segurança estão disponíveis para os recursos no âmbito. Pode especificar uma camada de subscrições, grupos de recursos e áreas de trabalho. 


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
Centro de segurança utiliza baseada em funções controlo de acesso (RBAC), que fornece funções incorporadas que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando os utilizadores abrem o Centro de segurança, verá apenas informações relacionadas com a recursos que têm acesso. O que significa que os utilizadores estão atribuídos a função de *proprietário*, *contribuinte*, ou *leitor* para o subscrição ou grupo de recursos que um recurso pertence. Além destas funções, há duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: ter ver direitos ao centro de segurança, que inclui as recomendações, alertas, políticas e estado de funcionamento, mas não é possível efetuar alterações.
- **Administrador de segurança**: ter os mesmos direitos de vista como *leitor segurança*, e também pode atualizar a política de segurança e ignorar as recomendações e alertas.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu sobre as políticas de segurança no Centro de segurança do Azure. Para saber mais acerca do Centro de segurança do Azure, consulte os artigos seguintes:

* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md): Saiba como configurar políticas de segurança para as subscrições do Azure e os grupos de recursos.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md): Saiba como as recomendações do Centro de segurança ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Segurança de dados do Centro de segurança do Azure](security-center-data-security.md): Saiba como o Centro de segurança gere e salvaguarda dados.
* [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](security-center-faq.md): obtenha respostas às perguntas mais frequentes sobre como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obter as informações e mais recentes notícias de segurança do Azure.


