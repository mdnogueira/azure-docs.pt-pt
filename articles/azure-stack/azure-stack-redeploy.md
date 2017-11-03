---
title: Reimplementar a pilha do Azure | Microsoft Docs
description: Reimplemente a pilha do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Reimplementar a pilha do Azure
Para voltar a pilha do Azure, tem de iniciar através do zero conforme descrito abaixo.

## <a name="steps-to-redeploy-azure-stack"></a>Passos para voltar a pilha do Azure
1. No anfitrião do kit de desenvolvimento, abra uma consola elevada do PowerShell > navegue para o script de asdk installer.ps1 > executá-la > clique **reiniciar**.
2. Selecione o sistema operativo base (não **Azure pilha**) e clique em **seguinte**.
3. Depois de reiniciado o anfitrião do kit de desenvolvimento, elimine o ficheiro de CloudBuilder.vhdx que foi utilizado como parte da implementação anterior.
4. [Implementar o kit de desenvolvimento](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Passos seguintes
[Ligar ao Azure Stack](azure-stack-connect-azure-stack.md)

