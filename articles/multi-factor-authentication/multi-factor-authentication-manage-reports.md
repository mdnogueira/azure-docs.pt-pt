---
title: "Relatórios de acesso e utilização de MFA do Azure | Microsoft Docs"
description: "Descreve como utilizar a funcionalidade de multi-factor Authentication do Azure - relatórios."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: a0ac1711b6bfb8f461cd775ed1f3409925643615
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios do multi-factor Authentication do Azure

Multi-factor Authentication do Azure fornece diversos relatórios que podem ser utilizados pelo utilizador e a sua organização. Estes relatórios podem ser acedidos através do Portal de gestão do multi-factor Authentication. A tabela seguinte lista os relatórios disponíveis:

| Relatório | Descrição |
|:--- |:--- |
| Utilização |A utilização de relatórios de informações de apresentação em utilização global, resumo do utilizador e detalhes de utilizador. |
| Estado do Servidor |Este relatório apresenta o estado dos servidores de autenticação Multifator associado à sua conta. |
| Histórico de Utilizador Bloqueado |Estes relatórios mostram o histórico de pedidos de bloqueio ou desbloqueio de utilizadores. |
| Histórico de Utilizador Ignorado |Mostra o histórico de pedidos para ignorar o multi-factor Authentication para o número de telefone de um utilizador. |
| Alerta de Fraude |Mostra um histórico de alertas de fraude apresentados durante o intervalo de datas especificado. |
| Em Fila |Apresenta uma lista de relatórios em fila para processamento e o respetivo estado. Quando o relatório estiver concluído, é fornecida uma ligação para transferir ou visualizar o relatório. |

## <a name="view-reports"></a>Ver relatórios

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione Active Directory.
3. Siga uma destas duas opções, consoante utilize ou fornecedores de autenticação:
   * **Opção 1**: clique no separador de fornecedores de autenticação Multifator. Selecione o seu fornecedor MFA e clique em de **gerir** na parte inferior.
   * **Opção 2**: selecione o diretório e vá para o **configurar** separador. Na secção da autenticação multifator, selecione **Gerir definições do serviço**. Na parte inferior da página de definições do serviço de MFA, clique a aceda ao link do portal.
4. No Portal de gestão do Azure multi-factor Authentication, selecione o tipo de relatório pretendido no **visualizar um relatório** secção no painel de navegação esquerdo.

<center>![Nuvem](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>Relatórios de PowerShell

Identifica os utilizadores se tem registado para MFA através do Powershell que se segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifique os utilizadores que não tenham registado para MFA através do Powershell que se segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**Recursos Adicionais**

* [Para os utilizadores](end-user/multi-factor-authentication-end-user.md)
* [Azure multi-factor Authentication no MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
