---
title: Azure Active Directory Reporting FAQ | Microsoft Docs
description: "Azure Active Directory FAQ de relatórios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: faee3bc9b0b1a10a48a514d830af5045cb047e02
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory reporting FAQ

Este artigo inclui as respostas a perguntas mais frequentes sobre o Azure Active Directory (Azure AD) Reporting Services. Para obter mais informações, veja [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md). 

**P: estiver a utilizar o https://graph.windows.net/&lt;nome do inquilino&gt;endpoint /reports/ APIs para auditoria de solicitação do Azure AD e a utilização de aplicação integrada relatórios para o nosso sistemas relatórios através de programação. O que posso mudar a?**

**R:** procurar nosso [documentação de referência da API](https://developer.microsoft.com/graph/) para ver como pode utilizar as novas APIs para aceder à [relatórios de atividade](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Este ponto final tem dois relatórios (auditoria e inícios de sessão) que fornecer todos os dados que obteve no ponto de final de API antigo. Este novo ponto final tem também um relatório de inícios de sessão com a licença do Azure AD Premium que pode utilizar para obter a utilização da aplicação, utilização de dispositivos e informações de início de sessão do utilizador.


--- 

**P: estiver a utilizar o https://graph.windows.net/&lt;nome do inquilino&gt;/reports/ endpoint APIs extraia relatórios de segurança do Azure AD (tipos específicos de deteções, tais como as credenciais obtidas ilicitamente ou inícios de sessão de endereços IP anónimos) para a nossa sistemas de relatórios através de programação. O que posso mudar a?**

**R:** pode utilizar o [eventos de risco Identity Protection API](active-directory-identityprotection-graph-getting-started.md) para deteções de segurança de acesso através do Microsoft Graph. Este novo formato proporciona uma maior flexibilidade na forma como pode consultar dados, com a filtragem avançados, seleção de campo e muito mais e uniformiza eventos de risco para um tipo de integração mais fácil para SIEMs e outras ferramentas de recolha de dados. Porque os dados estão num formato diferente, não é possível substituir uma nova consulta para as suas consultas antigas. No entanto, [a nova API utiliza o Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que é o padrão da Microsoft para estas APIs como o Office 365 ou do Azure AD. Para que o trabalho necessário a expandir o atuais MS Graph os investimentos em ou ajuda começar a transição para esta nova plataforma padrão.

--- 

**P: qual é a retenção de dados para os registos de atividade (auditoria e inícios de sessão) no portal do Azure?** 

**R:** podemos fornecer dados de 7 dias para os nossos clientes livres ou pode aceder aos dados para 30 dias, compra de um Azure AD Premium 1 ou Premium 2 licença. Para obter mais informações sobre a retenção de relatórios, consulte [políticas de retenção de relatórios do Azure Active Directory](active-directory-reporting-retention.md).

--- 

**P: quanto tempo demora até posso ver os dados de atividade depois de ter concluir a minha tarefa?**

**R:** registos de atividade de auditoria têm uma latência entre 15 minutos e uma hora. Registos de atividade de início de sessão podem demorar entre 15 minutos para alguns registos de até 2 horas.

---

**P: é necessário ser um administrador global para ver os atividade inícios de sessão no portal do Azure ou para obter os dados através da API?**

**R:** Não. Tem de ser um **segurança leitor**, um **administrador de segurança**, ou um **Administrador Global** para obter dados no portal do Azure ou através da API de relatórios.

---

**P: posso obter informações de registo de atividade do Office 365 através do portal do Azure?**

**R:** , apesar de atividade do Office 365 e do Azure AD atividade registos partilha muitos dos recursos de diretório, se pretender uma visão completa dos registos de atividade do Office 365, deverá passar para o Centro de administração do Office 365 para obter informações de registo de atividade do Office 365.

---


**P: quais APIs utilizar para obter informações sobre os registos de atividade do Office 365?**

**R:** utilizar as APIs de gestão do Office 365 para aceder a [registos de atividade do Office 365 através de uma API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**P: o número de registos posso pode transferir a partir do portal do Azure?**

**R:** pode transferir os registos de K até 120 do portal do Azure. Os registos são ordenados por *mais recente* e por predefinição, o que obtém os registos de 120 K mais recentes. 

---

**P: o número de registos pode consultar através das API de atividades**

**R:** pode consultar os registos de até 1 milhões (se não utilizar o operador top, que ordena o registo por mais recentes). Se utilizar o operador "top", pode consultar cópias de segurança para os registos de 500K. Pode encontrar consultas de exemplo sobre como utilizar a API aqui [aqui](active-directory-reporting-api-getting-started.md).

---

**P: como posso obter uma licença de premium?**

**R:** consulte [introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md) para uma resposta a esta pergunta.

---

**P: como logo que deve ver dados de atividades após obter uma licença de premium?**

**R:** se já tiver dados de atividades como uma licença livre, em seguida, pode ver os mesmos dados. Se não tiver quaisquer dados, em seguida, irá demorar um ou dois dias.

---

**P: Posso ver dados de último mês após obter uma licença de premium do Azure AD?**

**R:** se recentemente terem mudado para uma versão de Premium (incluindo uma versão de avaliação), pode ver dados de cópia de segurança para 7 dias inicialmente. Quando os dados acumulam-se, verá até 30 dias.

---

**P: há um evento de risco na proteção de identidade, mas não estou a ver correspondente início de sessão em todos os inícios de sessão. É esta esperada?**

**R:** Sim, Identity Protection avalia risco para todos os fluxos de autenticação se se ser interativo ou não interativo. No entanto, todos os relatórios apenas de inícios de sessão mostra apenas os interativos inícios de sessão.

---

**P: como podem a transferir o relatório "Utilizadores sinalizados para risco" no portal do Azure?**

**R:** a opção para transferir *utilizadores sinalizados para risco* relatório será adicionado em breve.

---

**P: como posso saber por que motivo um início de sessão ou um utilizador foi sinalizado arriscados no portal do Azure?**

**R:** clientes de edição Premium podem saber mais sobre os eventos de risco subjacente clicando no utilizador no "Utilizadores sinalizados para risco" ou clicando nos "duvidosos inícios de sessão". Obter os clientes de edição gratuita e básico para ver os utilizadores at-risk e inícios de sessão sem as informações de eventos de risco subjacente.

---

**P: como são calculados os endereços IP no relatório de risco inícios de sessão e inícios de sessão?**

**R:** endereços IP são emitidos de forma a que não há nenhuma ligação definitiva entre um endereço IP e onde está localizado fisicamente o computador com esse endereço. Isto é complicou fatores, tais como fornecedores de móveis e VPNs emitir endereços IP de agrupamentos centrais muito frequentemente longe onde o dispositivo de cliente, na verdade, é utilizado. Tendo em conta o acima, a conversão de endereço IP para uma localização física é um melhor esforço, com base nos rastreios, dados de registo, procure inversa ups e outras informações. 

---
