---
title: "Instalar o Visual Studio e ligue à pilha do Azure | Microsoft Docs"
description: "Obter os passos necessários para instalar o Visual Studio e ligue à pilha do Azure"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 5487125095f05b2fbfa9489c5b4733f61c0212d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalar o Visual Studio e ligue à pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Utilizar o Visual Studio para criar e implementar o Azure Resource Manager [modelos](user/azure-stack-arm-templates.md) na pilha do Azure. Pode utilizar os passos descritos neste artigo para instalar o Visual Studio do [Kit de desenvolvimento de pilha do Azure](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um baseados em Windows externo cliente se estiver ligado através de [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Estes passos efetuar uma nova instalação de edição de Comunidade de 2015 do Visual Studio. Leia mais sobre [coexistência](https://msdn.microsoft.com/library/ms246609.aspx) entre outras versões de Visual Studio.

## <a name="install-visual-studio"></a>Instalar o Visual Studio
1. Transfira e execute o [instalador de plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx).             
2. Procurar **Visual Studio Community 2015 com o SDK do Microsoft Azure - 2.9.6**, clique em **adicionar**, e **instalar**.

    ![Passos de instalação de captura de ecrã de WebPI](./media/azure-stack-install-visual-studio/image1.png) 

3. Desinstalar o **do Microsoft Azure PowerShell** que é instalado como parte do Azure SDK.

    ![Captura de ecrã da interface de programas de adição/remoção para o Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png) 

4. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)

5. Reinicie o sistema operativo após a conclusão da instalação.

## <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

1. Inicie o Visual Studio.

2. Do **vista** menu, selecione **Cloud Explorer**.

3. No painel de novo, selecione **adicionar conta** e inicie sessão com as suas credenciais do Azure Active Directory.  
    ![Captura de ecrã da Cloud Explorer após a sessão iniciada e ligado à pilha do Azure](./media/azure-stack-install-visual-studio/image6.png)

Depois de a sessão, pode [implementar modelos](user/azure-stack-deploy-template-visual-studio.md) ou procurar tipos de recursos disponíveis e grupos de recursos para criar os seus próprios modelos.  

## <a name="next-steps"></a>Passos Seguintes

 - [Desenvolver modelos para a pilha do Azure](user/azure-stack-develop-templates.md)
