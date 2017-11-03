---
title: "Como concluir uma revisão do acesso | Microsoft Docs"
description: "Depois de iniciar uma revisão do acesso no Azure AD Privileged Identity Management, saiba como concluí-la e ver os resultados"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: ca2a1c7c287e4cf6b1b50cfb0068861b6f525596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Como concluir uma revisão do acesso no Azure AD Privileged Identity Management
Os administradores de com função privilegiada podem rever uma vez acesso privilegiado um [revisão de segurança tiver sido iniciado](active-directory-privileged-identity-management-how-to-start-security-review.md). O Azure AD Privileged Identity Management (PIM) enviará automaticamente um e-mail a pedir aos utilizadores para rever o respetivo acesso. Se um utilizador não foi possível obter um e-mail, pode enviar-lhes as instruções [como executar uma revisão de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Após o período de revisão de segurança, ou todos os utilizadores tem concluído os respetivos rever automática, siga os passos neste artigo para gerir a rever e ver os resultados.

## <a name="manage-security-reviews"></a>Gerir revisões de segurança
1. Vá para o [portal do Azure](https://portal.azure.com/) e selecione o **do Azure AD Privileged Identity Management** aplicação no seu dashboard.
2. Selecione o **aceder revisões** secção do dashboard.
3. Selecione a revisão do acesso que pretende gerir.

No painel de detalhes da revisão do acesso tem um número opções para gerir essa revisão.

![Botões de revisão de acesso do PIM - captura de ecrã][1]

### <a name="remind"></a>Lembrar
Se uma revisão do acesso está configurado para que os utilizadores rever, o **Remind** botão envia uma notificação. 

### <a name="stop"></a>Parar
Uma data de fim de ter todas as revisões de acesso, mas pode utilizar o **parar** botão para terminar numa fase inicial. Se os utilizadores ainda não foram revistos neste momento, não seria capazes de depois de interromper a revisão. Não é possível reiniciar uma revisão depois-é foi parado.

### <a name="apply"></a>Registe-se
Uma revisão do acesso esteja concluída, ou porque foi atingido a data de fim ou parado-lo manualmente, o **aplicar** botão implementa o resultado a revisão. Se acesso um utilizador foi negado de revisão, este é o passo que irá remover a atribuição de função.  

### <a name="export"></a>Exportar
Se pretender aplicar manualmente os resultados da revisão de segurança, pode exportar a revisão. O **exportar** botão iniciará transferir um ficheiro CSV. Pode gerir os resultados no Excel ou outros programas que abrir ficheiros CSV.

### <a name="delete"></a>Eliminar
Se não estiver interessado em mais de revisão, elimine-o. O **eliminar** botão remove a revisão da aplicação do PIM.

> [!IMPORTANT]
> Não irá receber um aviso antes de ocorre a eliminação, por isso, lembre-se de que pretende eliminar esse revisão. 

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
