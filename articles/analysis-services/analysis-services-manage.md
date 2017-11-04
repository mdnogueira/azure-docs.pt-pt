---
title: "Gerir serviços de análise do Azure | Microsoft Docs"
description: Saiba como gerir um servidor de Analysis Services no Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: cd452ff46f8a87fd04eaca037b6bc0120ca86e4d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="manage-analysis-services"></a>Gerir do Analysis Services
Depois de criar um servidor de Analysis Services no Azure, poderão existir algumas tarefas de administração e gestão que terá de efetuar imediatamente ou algum tempo para baixo viagem. Por exemplo, executar processamento para os atualização de dados, controlar quem pode aceder aos modelos no seu servidor, ou monitorizar estado de funcionamento do seu servidor. Algumas tarefas de gestão só podem ser efetuadas no portal do Azure, outros no SQL Server Management Studio (SSMS), e algumas tarefas podem ser efetuadas no.

## <a name="azure-portal"></a>Portal do Azure
[Portal do Azure](http://portal.azure.com/) é onde pode criar e eliminar servidores, monitorizar os recursos do servidor, altere o tamanho e gerir quem tem acesso aos seus servidores.  Se estiver a ter alguns problemas, também pode submeter um pedido de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Ligar ao seu servidor no Azure é como ligar a uma instância de servidor na sua própria organização. SSMS, pode efetuar muitas das mesmas tarefas como processamento de dados ou crie um script de processamento, gerir funções e utilizar o PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Transfira e instale o SSMS
Para obter todas as funcionalidades mais recentes e a experiência de smoothest ao ligar ao seu servidor de Analysis Services do Azure, lembre-se de que está a utilizar a versão mais recente do SSMS. 

[Transferir o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Para estabelecer ligação com o SSMS
 Quando com o SSMS, antes de ligar ao seu servidor pela primeira vez, certifique-se de que o seu nome de utilizador está incluído no grupo de administradores de serviços de análise. Para obter mais informações, consulte [os administradores de servidores](#server-administrators) posteriormente neste artigo.

1. Antes de ligar, terá de obter o nome do servidor. No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSMS > **Object Explorer**, clique em **Connect** > **Analysis Services**.
3. No **ligar ao servidor** caixa de diálogo, colar no nome do servidor, em seguida, em **autenticação**, escolha um dos seguintes tipos de autenticação:
   
    **Autenticação do Windows** para utilizar as credenciais de domínio ome de utilizador e palavra-passe do Windows.

    **Autenticação de palavra-passe do Active Directory** para utilizar uma conta institucional. Por exemplo, quando ligar a partir de um domínio associado a um computador.

    **Autenticação de Universal do Active Directory** utilizar [autenticação não interativa ou multifator](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Se ligar no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Os administradores de servidores e os utilizadores de base de dados
No Azure Analysis Services, existem dois tipos de utilizadores, os administradores de servidores e os utilizadores de base de dados. Ambos os tipos de utilizadores tem de estar no seu Azure Active Directory e tem de ser especificados por endereço de e-mail empresarial ou UPN. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).


## <a name="troubleshooting-connection-problems"></a>Resolução de problemas de ligação
Ao estabelecer ligação com o SSMS, caso se depare com problemas, poderá ter de limpar a cache de início de sessão. Nada é colocado em cache para disco. Para limpar a cache, feche e reinicie o processo de ligação. 

## <a name="next-steps"></a>Passos seguintes
Se já tiver ainda não implementado um modelo de tabela para o novo servidor, agora é uma boa altura. Para obter mais informações, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementar no Azure Analysis Services).

Se tiver implementado um modelo para o servidor, está pronto para ligar ao mesmo utilizando um cliente ou browser. Para obter mais informações, consulte [obter dados a partir do servidor de Analysis Services do Azure](analysis-services-connect.md).

