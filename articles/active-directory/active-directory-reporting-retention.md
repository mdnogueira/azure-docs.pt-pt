---
title: "Políticas de retenção de relatórios do Azure Active Directory | Microsoft Docs"
description: "Políticas de retenção de dados do relatório no Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 933d251903f4ca74902e984e7a1581a75345ee7f
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatórios do Azure Active Directory


Este tópico fornece respostas às perguntas mais comuns em conjunto com a retenção de dados para os relatórios de atividade diferente no Azure Active Directory. 

**P: como é pode obter a recolha de dados de atividade iniciadas?**

**R:**

| Edição do Azure AD | Início da coleção |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando a inscrição para uma subscrição |
| Azure AD Gratuito | Na primeira vez que abrir o [painel do Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilize o [APIs de relatórios](https://aka.ms/aadreports)  |

---
**P: quando os dados de atividade está disponível no portal do Azure?**

**R:**

- **Imediatamente** - se de que tem estado a trabalhar com relatórios no portal clássico do Azure
- **Dentro de 2 horas** - se de que ainda não ativou o Reporting Services no portal clássico do Azure

---
**P: como é pode obter a coleção de sinais de segurança iniciada?**  

**R:** para sinais de segurança, o processo de recolha é iniciado quando que optar ativamente por participar sessão para utilizar o Centro de proteção de identidade. 


---
**P: por quanto estão os dados recolhidos armazenados?**

**R:**

**Relatórios de atividade**    

| Relatório                 | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditoria de Diretórios        | 7 dias        | 30 dias             | 30 dias             |
| Atividade de Início de Sessão       | N/D           | 30 dias             | 30 dias             |
| Utilização MFA do Azure        | 90 dias       | 90 dias             | 90 dias             |

**Sinais de segurança**

| Relatório         | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Utilizadores em risco  | 7 dias        | 30 dias             | 90 dias             |
| Risco inícios de sessão | 7 dias        | 30 dias             | 90 dias             |

---
