---
title: "Relatório de utilização não autorizada | Microsoft Docs"
description: "O ajuda do relatório de utilização não autorizada que identificar os utilizadores sem licença que estão a utilizar paga funcionalidades do Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: 91b48098cc8ba2bb230b0536a9bcd121db79c533
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="unlicensed-usage-report"></a>Relatório de utilização não autorizada
O ajuda do relatório de utilização não autorizada que identificar os utilizadores sem licença que estão a utilizar paga funcionalidades do Azure AD. Isto permite-lhe melhorar a utilização de licenças que adquiriu e para identificar saber quando poderá ter licenças adicionais. 

O relatório mostra o Active Directory utilização das funcionalidades pagas nos últimos 30 dias. 

## <a name="report-structure"></a>Estrutura de relatório
| Nome da coluna | Descrição |
|:--- |:--- |
| Sem licença de utilizador |Nome do utilizador |
| Funcionalidade |O nome da funcionalidade. Por exemplo: acesso condicional |
| Aplicação acedida |O nome da aplicação que estiver a ser acedido com a funcionalidade. Por exemplo: Office 365 SharePoint Online |

> [!NOTE]
> Se uma conta de utilizador foi eliminada a coluna 'User sem licença' será preenchida com o ID, como 1003000090D8B285
> 
> 

## <a name="conditional-access-feature"></a>Funcionalidade de acesso condicional
Utilizadores sem licença vai ser sinalizados quando acedem a um serviço com a política de acesso condicional aplicada se não tiver uma licença do Azure AD Premium. 

Isto aplica-se a MFA / políticas de localização, bem como dispositivos políticas que utilizar o Intune.

## <a name="see-also"></a>Consultar também
* [Utilizar o acesso condicional com o Office 365 e outros Azure Active Directory ligado aplicações](active-directory-conditional-access-azure-portal.md)
* [Introdução ao acesso condicional para o Azure AD](active-directory-conditional-access-azure-portal-get-started.md) 

