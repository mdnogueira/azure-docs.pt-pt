---
title: "Introdução ao Azure AD API do relatório | Microsoft Docs"
description: "Como começar com o Azure Active Directory API do relatório"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9944cbd2b1b7c4acb18d37da1394c0bbc170f77d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Introdução ao Azure Active Directory API do relatório

Azure Active Directory fornece-lhe uma variedade de relatórios. Os dados destes relatórios podem ser bastante úteis para as suas aplicações, como sistemas SIEM e ferramentas de auditoria e de business intelligence. As APIs dos relatórios do Azure AD proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação.

Este artigo fornece-lhe as informações necessárias começar a utilizar com o Azure AD APIs de relatórios.
Na secção seguinte, pode encontrar mais detalhes sobre como utilizar a auditoria e início de sessão APIs. 

Para perguntas mais frequentes, leia a nossa [FAQ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq). Para problemas, consulte [um pedido de suporte de ficheiros](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="learning-map"></a>Mapa de aprendizagem
1. **Preparar** -antes de testar as amostras da API, terá de concluir o [pré-requisitos para o Azure AD API do relatório de acesso](active-directory-reporting-api-prerequisites-azure-portal.md).
2. **Explorar** -obter uma primeira impressão das APIs Reporting Services:
   
   * [Utilizando os exemplos para a API de auditoria](active-directory-reporting-api-audit-samples.md) 
   * [Os exemplos a utilizar para o relatório de atividade de início de sessão API](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personalizar** -criar a sua própria solução de: 
   
   * [Utilizar a referência da API de auditoria](active-directory-reporting-api-audit-reference.md) 
   * [Utilizar a referência da API do relatório de atividade de início de sessão](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Passos Seguintes
Se estiver com curiosidade ver todos os pontos finais de AD Graph API do Azure disponíveis, utilize esta ligação: [https://graph.windows.net/tenant-name/activities/$ metadados? api-version = beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta).

