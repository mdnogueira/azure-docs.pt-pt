---
title: "Resolução de problemas de atividade do Active Directory do Azure regista erros de pacote de conteúdos | Microsoft Docs"
description: "Fornece uma lista de mensagens de erro do pacote de conteúdos de atividade do Active Directory do Azure e passos para corrigi-los."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 4c4c3eb1d7b363f93ad1e69bb93b755d56493f5e
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Resolução de problemas de atividade do Active Directory do Azure regista erros de pacote de conteúdos 


Ao trabalhar com o pacote de conteúdos do Power BI para o Azure Active Directory pré-visualização, é possível se depare com os seguintes erros: 

- [Falha na atualização](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [Falha ao atualizar as credenciais da origem de dados](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importação de dados está a demorar demasiado tempo](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Este tópico fornece informações sobre as possíveis causas e saber como corrigir estes erros.
 
## <a name="refresh-failed"></a>Falha na atualização 
 
**Como este erro é anexado**: E-Mail do Power BI ou Estado com falhas no histórico de atualização. 


| Causa | Saber como corrigir |
| ---   | ---        |
| Atualize a falha de erros podem ser provocados quando as credenciais dos utilizadores a ligar para o pacote de conteúdos tem sido repor, mas não atualizadas nas definições de ligação do do pacote de conteúdos. | No Power BI, localize o conjunto de dados correspondente para o dashboard de registos de atividade do Active Directory do Azure (registos de atividade do Active Directory do Azure), escolha a agendar a atualização e, em seguida, introduza as credenciais do Azure AD. |
| Uma atualização pode falhar devido a problemas de dados no pacote de conteúdos subjacente. | Um pedido de suporte de ficheiros. Para obter mais detalhes, consulte [como obter suporte do Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais da origem de dados 
 
**Como este erro é anexado**: no Power BI, quando estiverem a ligar para o pacote de conteúdos de registos (pré-visualização) de atividade do Active Directory do Azure. 

| Causa | Saber como corrigir |
| ---   | ---        |
| O utilizador de ligação é nem um administrador global nem um leitor de segurança ou um administrador de segurança. | Utilize uma conta que seja um administrador global ou um leitor de segurança ou um administrador de segurança para aceder aos pacotes de conteúdos. |
| O inquilino não é um inquilino Premium ou não tem, pelo menos, um utilizador com o ficheiro de licença de Premium. | Um pedido de suporte de ficheiros. Para obter mais detalhes, consulte [como obter suporte do Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Importação de dados está a demorar demasiado tempo 
 
**Como este erro é anexado**: no Power BI, assim que tiver estabelecido ligação o pacote de conteúdos, o processo de importação de dados é iniciado preparar o seu dashboard para o registo de atividade do Active Directory do Azure. Consulte a mensagem: "*importar dados...* "  

| Causa | Saber como corrigir |
| ---   | ---        |
| Dependendo do tamanho do seu inquilino, este passo pode demorar em qualquer local de alguns minutos e 30 minutos. | Acabou de ser patient. Se a mensagem não é alterado para mostrar o dashboard dentro de uma hora, ficheiros, um pedido de suporte. Para obter mais detalhes, consulte [como obter suporte do Azure Active Directory](active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Passos seguintes

Para instalar o pacote de conteúdos do Power BI para o Azure Active Directory pré-visualização, clique em [aqui](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/).


