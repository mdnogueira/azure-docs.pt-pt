---
title: "Adicionar armazenamento do Azure utilizando os serviços ligados no Visual Studio | Microsoft Docs"
description: "Adicionar armazenamento do Azure para a sua aplicação utilizando a caixa de diálogo do Visual Studio adicionar ligado serviços"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 521ec044-ad4b-4828-8864-01decde2e758
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2017
ms.author: kraigb
ms.openlocfilehash: 35638083cd75e1b751d00a9c8163a3bc7480f0cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>A adição de armazenamento do Azure utilizando o Visual Studio ligado Services
Com o Visual Studio, pode ligar qualquer um dos seguintes ao Storage do Azure utilizando o **adicionar serviços ligados** diálogo:

- Serviço de nuvem do c#
- Serviço móvel de back-end de .NET
- Web site ASP.NET ou serviço
- Serviço de núcleo de ASP.NET
- Serviço de trabalho Web do Azure 

A funcionalidade do serviço ligado adiciona todas as referências necessárias e código de ligação ao seu projeto e modifica os ficheiros de configuração conforme apropriado. 

Após a conclusão, o **adicionar serviços ligados** diálogo automaticamente apresenta documentação com detalhes sobre os passos necessários para começar a trabalhar com armazenamento de BLOBs, filas e tabelas.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Ligar ao armazenamento do Azure com a caixa de diálogo Serviços ligados
1. Abra o projeto no Visual Studio

1. No **Explorador de soluções**, clique com botão direito do **serviços ligados** nó e, no menu de contexto e selecione **adicionar o serviço ligado**.
   
    ![Adicionar Azure ligado serviço](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. No **serviços ligados** página, selecione **armazenamento na nuvem com o Storage do Azure**.
   
    ![Adicionar armazenamento do Azure](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. No **Storage do Azure** caixa de diálogo, selecione uma conta de armazenamento existente e selecione **adicionar**.
   
    Se precisar de criar uma conta do storage, vá para o passo seguinte. Caso contrário, avance para o passo 6.
    
    ![Adicione a conta de armazenamento existente ao projeto](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Para criar uma conta de armazenamento: 
   
   1. Selecione **criar uma nova conta de armazenamento** na parte inferior da caixa de diálogo.

   1. Preencha o **criar conta de armazenamento** caixa de diálogo e selecione **criar**.
      
       ![Nova conta do storage do Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. Quando o **Storage do Azure** é apresentada a caixa de diálogo, a nova conta do storage será apresentada na lista. Selecione a nova conta de armazenamento na lista e selecione **adicionar**.

1. O armazenamento de serviço ligado é apresentado sob o **referências do serviço** nó do projeto.
   
## <a name="how-your-project-is-modified"></a>Como o projeto é modificado
Quando acabar de caixa de diálogo, o Visual Studio adiciona as referências e modifica determinados ficheiros de configuração. As alterações específicas dependem do tipo de projeto: 

- Projeto ASP.NET - [o que aconteceu – projetos de ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- Projeto em ASP.NET Core - [o que aconteceu – projetos de 5 do ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Projeto de serviço em nuvem (funções da web e funções de trabalho) - [o que aconteceu – projetos do serviço de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- Projeto de WebJob - [o que aconteceu - projetos de trabalho Web](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>Passos seguintes
- [Fórum MSDN: De armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Blogue da equipa de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Documentação do Storage do Azure](https://docs.microsoft.com/azure/storage/)
